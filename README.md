# Adding Maven Dependencies

## Learning Goals

- Add dependencies to a Maven project.
- Explain dependency scope.

## Introduction

Any non-trivial program uses external drivers, libraries, and frameworks. These
external resources are called dependencies because your program depends on them
to run properly.  

Maven has a declarative dependency management system. We define which
dependency we want in the `pom.xml` file, then  Maven automatically downloads the dependency to the
[local repository](https://maven.apache.org/guides/introduction/introduction-to-repositories.html#artifact-repositories)
and references the dependency by setting the correct 
[Java Classpath](https://docs.oracle.com/javase/tutorial/essential/environment/paths.html).


## Junit Dependency

We’ll add a unit test to our project, and then we’ll learn how to add the Junit
dependency to make it work. 

The `Main` class has been updated with a new method `greet()`:

```java
package org.example;

public class Main {

    public static String greet(String name) {
        return "Hello, " + name + "!";
    }

    public static void main(String[] args) {
        System.out.println("Hello!");
    }
}
```

Right-click in the `Main` class and select **Generate | Test** to generate
a Junit test class.  Check the option to generate a test method for the `greet()` method:

![MainTest Junit class](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/newtest.png)

The `MainTest` class should test the static `Main.greet()` method as shown:

```java
package org.example;

import static org.junit.jupiter.api.Assertions.*;

class MainTest {

    @org.junit.jupiter.api.Test
    void greet() {
        String expected = "Hello, John!";
        String actual = Main.greet("John");
        assertEquals(expected, actual);
    }
}
```

Unfortunately, `MainTest` won't run because the
Junit library can't be found.  We need to add a dependency to `pom.xml`
to tell Maven to download the Junit library.

![Junit missing error](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/errors.png)

Hover over the word "junit" in the import statement or the test annotation
to see the error.   Click "more actions" to add the most updated version of Junit
to the class path:

![Add Junit to classpath](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/junitclasspath.png) 

The errors disappear in `MainTest` and the test class can be executed.

1. Run the `MainTest` class and confirm the unit test passes.
2. Open the Build Tool Window by clicking the  **Build** icon on the lower toolbar. 
   There are quite a few tasks required for the **test** phase of the lifecycle!

### pom.xml

The `pom.xml` file was automatically updated when Junit was added to the classpath.
The  file has a new dependency element containing the coordinates of the Junit
library:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>org.example</groupId>
   <artifactId>example-app</artifactId>
   <version>1.0-SNAPSHOT</version>

   <dependencies>
      <!--JUNIT DEPENDENCY -->
      <dependency>
         <groupId>org.junit.jupiter</groupId>
         <artifactId>junit-jupiter</artifactId>
         <version>RELEASE</version>
         <scope>test</scope>
      </dependency>
   </dependencies>

   <properties>
      <maven.compiler.source>11</maven.compiler.source>
      <maven.compiler.target>11</maven.compiler.target>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   </properties>

</project>
```

The groupId, artifactId, and version tags uniquely
identify which external resource was added to the project. The dependency
tells Maven to automatically download and set up JUnit.

```xml
<dependencies>
   <!--JUNIT DEPENDENCY -->
   <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>RELEASE</version>
      <scope>test</scope>
   </dependency>
</dependencies>
```

### Dependency Scope

Notice the new `<scope>test</scope>` tag in the Junit dependency.
The scope tag defines the point in the lifecycle the dependency will be
available. For example, the `test` scope means that the dependency will only
be used for testing and not for compilation or during runtime.

By default, dependencies have a `compile` scope. This means that the
dependencies are available at compile time, for testing, and at runtime. You can
check out the available scopes in the
[official documentation](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope).

### Surefire Plugin

The **Dependency** folder in the Maven Tool Window shows the new
Junit dependency.  

The **Plugins** folder has a plugin called `maven-surefire-plugin`.
The plugin provides the **surefire:test** goal, which is executed during the test phase
of the lifecycle to run the Junit test.

![Dependency View](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/testdependency.png)

## Apache Commons Lang Dependency

The **Apache Commons Lang** library  contains many useful utility
classes, including `StringUtils`. The
`StringUtils.abbreviate()` method can be called to
abbreviate a string to a particular length, adding `...` on the end
of the string.

We will add an import statement and update the `greet()` method
to call `StringUtils.abbreviate`  to shorten a long name to 10 characters
(including the ...) as shown:

```java
package org.example;

import org.apache.commons.lang3.StringUtils;

public class Main {

   public static String greet(String name) {
      String shortName = StringUtils.abbreviate(name, 10);
      return "Hello, " +  shortName  + "!";
   }

   public static void main(String[] args) {
      System.out.println("Hello!");
   }
}
```

The code won't compile because the `StringUtils` class can't be not found.
A dependency must be added to `pom.xml` to tell Maven to get
the Apache Commons Lang library.

1. Open `pom.xml`
2. Right-click to open the context menu (or alt-insert on Windows or command-N on Mac).
3. Select Generate.  
   ![generate](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/generate.png)
4. Select Add Dependency.  
   ![add dependency](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/add-dependency.png)
5. Type "Apache Commons Lang" in the search box, select the "Apache Commons Lang" library, then click "Add".
   ![search dependency](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/find-dependency.png)
  
The `pom.xml` file is updated to include a dependency with coordinates
for the Apache Commons Lang library:

```xml
<dependencies>
   <!--JUNIT DEPENDENCY -->
   <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>RELEASE</version>
      <scope>test</scope>
   </dependency>
   <!--APACHE COMMONS DEPENDENCY -->
   <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-lang3</artifactId>
      <version>3.12.0</version>
   </dependency>
</dependencies>
```
  
This next step is **very important** and easy to forget.
The code won't compile yet because IntelliJ needs Maven to load the changes
into the project.  Press the "Load Maven Changes" icon that appears in `pom.xml`:

![reload maven](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/reload-maven.png)

Confirm the Maven Tool Window displays the new dependency:

![apache dependency](https://curriculum-content.s3.amazonaws.com/6002/maven-dependencies/apache-dependency.png)


`MainTest` should be updated with a new method to check that
`Main.greet()` abbreviates names over 7 characters in length:


```java
    @org.junit.jupiter.api.Test
    void greetAbbreviate() {
        String expected = "Hello, John Wi...!";
        String actual = Main.greet("John Williams Smith, Jr.");
        assertEquals(expected, actual);
    }
```

## Conclusion

We’ve learned how to add dependencies to our project. Maven makes it incredibly
easy to add dependencies since it uses a declarative dependency management
system which can automatically download and set up dependencies.


## Resources

[Introduction to Maven Dependencies](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)    
[Apache Commons Lang](https://commons.apache.org/proper/commons-lang/)   
