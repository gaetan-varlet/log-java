# Les logs dans un projet Maven sans Spring Boot

## Mise en place de Log4j2

Ajout des bibliothèques suivantes

```xml
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-api</artifactId>
	<version>2.17.1</version>
</dependency>
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-core</artifactId>
	<version>2.17.1</version>
</dependency>
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-slf4j-impl</artifactId>
	<version>2.17.1</version>
</dependency>
```

Exemple en utilisant la configuration par défaut

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class App {

    private static final Logger log = LoggerFactory.getLogger(App.class);

    public static void main(String[] args) {
        System.out.println("Hello World !");
        System.err.println("Hello World Erreur");
        log.trace("TRACE");
        log.debug("DEBUG");
        log.info("INFO");
        log.warn("WARN");
        log.error("ERROR");
    }
}

// Hello World !
// Hello World Erreur
// 19:29:47.521 [main] ERROR fr.insee.App - ERROR
```

Pourquoi surcharger la configuration par défaut ?
- pour changer le niveau de log
- pour changer la mise en forme d'écriture dans la console
- pour écrire dans un fichier

Comment changer la configuration ?
- en utilisant un fichier `log4j2.properties` ou `log4j2.xml`
- à déposer dans le classpath, généralement dans `src/main/resources`

Comment surcharger la configuration ?
- avec un fichier externe `-Dlog4j2.configurationFile=file:...`
- avec des properties externes pour ne pas surcharger le fichier, mais uniquement le niveau de log et le chemin où écrire le fichier de log

Exemple de fichier de configuration `log4j2.xml` :
- mise en forme des lignes écrites
- choix du niveau de log
- écriture dans un fichier en plus de la console

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
	<Appenders>
		<Console name="Console" target="SYSTEM_OUT">
			<PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
		</Console>
		<File name="MyFile" fileName="./logs/toto.log">
			<PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
		</File>
	</Appenders>
	<Loggers>
		<Root level="debug">
			<AppenderRef ref="Console" />
			<AppenderRef ref="MyFile"/>
		</Root>
	</Loggers>
</Configuration>
```

Paramètrage du fichier avec des propriétés **logLevel** et **logPath** : utilisation de valeur par défaut pour l'utilisation en local

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
	<Properties>
		<property name="logLevel">${sys:logLevel:-INFO}</property>
		<property name="logPath">${sys:logPath:-./logs/toto.log}</property>
	</Properties>
	<Appenders>
		<Console name="Console" target="SYSTEM_OUT">
			<PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
		</Console>
		<File name="MyFile" fileName="${logPath}">
			<PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
		</File>
	</Appenders>
	<Loggers>
		<Root level="${logLevel}">
			<AppenderRef ref="Console" />
			<AppenderRef ref="MyFile"/>
		</Root>
	</Loggers>
</Configuration>
```

Possibilité de **surcharger** des valeurs au lancement du JAR :  
`java -jar -DlogLevel=TRACE -DlogPath=./logs/externe.log target/toto-1.0-SNAPSHOT-jar-with-dependencies.jar`


## Logback

- ajout de la bibliothèque suivante
```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.10</version>
</dependency>
```

- exemple d'utilisation de la configuration par défaut

```java
// Hello World !
// Hello World Erreur
// 19:30:28.178 [main] DEBUG fr.insee.App - DEBUG
// 19:30:28.180 [main] INFO fr.insee.App - INFO
// 19:30:28.180 [main] WARN fr.insee.App - WARN
// 19:30:28.180 [main] ERROR fr.insee.App - ERROR
```

- possibilité comme pour log4j2 de créer un fichier de configuration `logback.xml`
