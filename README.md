# Escuela Colombiana de Ingeniería
# Arquitecturas de Software - ARSW
### Taller – Principio de Inversión de dependencias, Contenedores Livianos e Inyección de dependencias.

Parte I. Ejercicio básico.

Para ilustrar el uso del framework Spring, y el ambiente de desarrollo para el uso del mismo a través de Maven (y NetBeans), se hará la configuración de una aplicación de análisis de textos, que hace uso de un verificador gramatical que requiere de un corrector ortográfico. A dicho verificador gramatical se le inyectará, en tiempo de ejecución, el corrector ortográfico que se requiera (por ahora, hay dos disponibles: inglés y español).

1. Abra el los fuentes del proyecto en NetBeans.

2. Revise el archivo de configuración de Spring ya incluido en el proyecto (src/main/resources). El mismo indica que Spring buscará automáticamente los 'Beans' disponibles en el paquete indicado.

3. Haciendo uso de la [configuración de Spring basada en anotaciones](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-spring-beans-and-dependency-injection.html) marque con las anotaciones @Autowired y @Service las dependencias que deben inyectarse, y los 'beans' candidatos a ser inyectadas -respectivamente-:

	* GrammarChecker será un bean, que tiene como dependencia algo de tipo 'SpellChecker'.
	* EnglishSpellChecker y SpanishSpellChecker son los dos posibles candidatos a ser inyectados. Se debe seleccionar uno, u otro, mas NO ambos (habría conflicto de resolución de dependencias). Por ahora haga que se use EnglishSpellChecker.

#### Función de etiqueta Service 
La anotación Spring @Service se utiliza con clases que proporcionan algunas funcionalidades de negocio. El contexto de Spring detectará automáticamente estas clases cuando se utilice la configuración basada en anotaciones y el escaneo del classpath para encontrar beans.

#### Función de etiqueta Autowired 
La anotación @Autowired marca un constructor, un método Setter, unas propiedades y un método Config() como autowired, es decir, "inyectando beans" (objetos) en tiempo de ejecución mediante el mecanismo de inyección de dependencias de Spring

#### Función de etiqueta Component
@Component registra un bean dentro del framework sin mayor efecto

Para este caso se usa la etiqueta @Service en la clase GrammarChecker ya que este va a contener los beans candidatos a inyectarse. Así mismo se pone la etiqueta @Autowired a la variable sc, la cual es la encargada de recibir la inyección del SpellChecker que necesite el usuario. Se usa @Qualifier para generar versatilidad cuando el usuario necesite otra inyección, solo debe poner el nombre de esta para que la variable sc la acepte.

```java
	@Service
public class GrammarChecker {

	@Autowired
	@Qualifier("English")
	SpellChecker sc;
```

Se pone la etiqueta @Component en la clase EnglishSpellChecker ya que este es un bean candidato para inyectarse. No se puede poner otra etiqueta ya que las otras dos tienen usos específicos y @Component es el padre de estas, por lo que se hace una generalidad.

```java
@Component
@Qualifier("English")
public class EnglishSpellChecker implements SpellChecker {

	@Override
	public String checkSpell(String text) {		
		return "Checked with english checker:"+text;
	}      
}
```
![img1](https://github.com/DiegoGonzalez2807/ARSW-LAB4-Primera-Parte/blob/master/img/IMAGEN2.png)
 
4.	Haga un programa de prueba, donde se cree una instancia de GrammarChecker mediante Spring, y se haga uso de la misma:

```java
	public static void main(String[] args) {
		ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
		GrammarChecker gc=ac.getBean(GrammarChecker.class);
		System.out.println(gc.check("la la la "));
	}
```
	
5.	Modifique la configuración con anotaciones para que el Bean ‘GrammarChecker‘ ahora haga uso del  la clase SpanishSpellChecker (para que a GrammarChecker se le inyecte EnglishSpellChecker en lugar de  SpanishSpellChecker. Verifique el nuevo resultado.
```java
@Component
@Qualifier("Spanish")
public class SpanishSpellChecker implements SpellChecker {
```
Para este caso primero se pone un calificativo a la clase SpanishSpellChecker y se le pone la etiqueta @Component para que Spring lo tome como candidato a ser inyectado en GrammarChecker.

```java
	@Service
public class GrammarChecker {

	@Autowired
	@Qualifier("Spanish")
	SpellChecker sc;
```
Luego, a la clase GrammarChecker se le cambio el calificativo de la variable sc a Spanish

![img1](https://github.com/DiegoGonzalez2807/ARSW-LAB4-Primera-Parte/blob/master/img/IMAGEN1.png)

