Extension language for CSS. Sass files are compiled at build-time into CSS files. 

[salomonbrys](https://github.com/SalomonBrys/gradle-sass) has a nice Gradle plugin that handles the sass compile
```
plugins {
    id("com.github.salomonbrys.gradle.sass") version "1.2.0"
}

sassCompile {
    source = fileTree("$projectDir/src/main/sass")
    outputDir = file("$projectDir/src/main/resources/static/assets/css")
}
```