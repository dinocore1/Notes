
Good example project: [ThoughtPlot](https://github.com/dinocore1/ThoughtPlot)

### Basics

Spring works mostly my annotating java class files. Start with main class with `public static main` and annotated with `@SpringBootApplication`. The framework will use reflection and find all classes under the current package containing Spring's Annotations. 

#### Bean Configuration

A Spring "Bean" can be any plain old java object. Beans can be Dependency Injected basically anywhere using `@Autowired` annotation. One easy way to create beans is with functions annotated with `@Bean` in configuration classes (`@SpringBootApplication`, or `@Configuration`). For example:

```
@Configuration
public class AppConfig {

     @Bean
     public MyBean myBean() {
         // instantiate, configure and return bean ...
     }
 }
```

`myBean` is now available to be injected using `@Autowired`:

```
@RestController
public class MyRestController {
  
  @Autowired
  MyBean myBean;
  
  ...
}
```

Note: by default, Beans are injected by matching the factory and field names. So the factory function `myBean()` will create the bean for `myBean`. Also, the java getter setter convention (`getMyBean()`) will also work.

Other notable annotations are:

 * `@RestController` 
 * `@Service`
 * `@Controller`

### Gradle Buildscript

```
buildscript {
    ext {
        springBootVersion = '2.1.3.RELEASE'
    }
    repositories {
        mavenCentral()
        jcenter()
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}"
    }
}

plugins {
    id("com.github.salomonbrys.gradle.sass") version "1.2.0"
}

apply plugin: 'java'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'com.devsmart'
version = '0.0.1'

repositories {
    mavenCentral()
    jcenter()
}

sassCompile {
    source = fileTree("$projectDir/src/main/sass")
    outputDir = file("$projectDir/src/main/resources/static/assets/css")
}

compileJava.dependsOn(sassCompile)

configurations {
    dev
}

dependencies {
    implementation "org.springframework.boot:spring-boot-starter-web"
    implementation "org.springframework.boot:spring-boot-starter-thymeleaf"
    dev "org.springframework.boot:spring-boot-devtools"
    implementation 'com.google.guava:guava:27.0.1-jre'
    implementation 'org.jgrapht:jgrapht-core:1.3.0'
    implementation 'org.jgrapht:jgrapht-ext:1.3.0'
    implementation 'com.vladsch.flexmark:flexmark-all:0.40.22'


    testImplementation 'junit:junit:4.12'
    testImplementation "org.springframework.boot:spring-boot-starter-test"

}

bootRun {
    // Use Spring Boot DevTool only when we run Gradle bootRun task
    classpath = sourceSets.main.runtimeClasspath + configurations.dev
}
```

