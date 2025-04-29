# How to Build and Run a Modular JavaFX App Using Maven (No IDE)

This README explains in full detail how to create, configure, build, and run a modular JavaFX application using Maven **without using any IDE**. The instructions assume you're starting from scratch with just a terminal, text editor, and JDK 21+.

---

## Prerequisites

- Java 21 or higher installed and `java` and `javac` are available in your terminal.
- Apache Maven installed and `mvn` command is available in your terminal.
- Basic understanding of Java syntax and folder structures.

---

## Step 1: Create the Project Folder Structure

You need to follow Maven's **standard directory structure since we are relying on maven to download the dependencies**:

testproject1/                                                                                                                                                                                                                                               
├── pom.xml                                                                                                                                                                                                                                                 
├── src/                                                                                                                                                                                                                                                    
│   └── main/                                                                                                                                                                                                                                               
│       ├── java/                                                                                                                                                                                                                                           
│       │   ├── com/                                                                                                                                                                                                                                        
│       │   │    └── example/                                                                                                                                                                                                                               
│       │   │       └──Main.java                                                                                                                                                                                                                            
│       │   └── module-info.java                                                                                                                                                                                                                            
│       └── resources/                                                                                                                                                                                                                                      
│           └── Mainview.fxml                                                                                                                                                                                                                               
│                                                                                                                                                                                                                                                           
├──lib(📁 lib/ — Optional directory for manually managed JARs)                                                                                                                                                                                             
└──target(📁 target/ — Maven’s build output directory.contains compiled .class files, packaged .jar files, and any other build artifacts)                                                                                                                                                                                                                                                                                                                                                                              


---

## Explanation:

- `src/main/java/` holds `.java` source files.
- `src/main/resources/` holds resource files like `.fxml`, images, or configuration files.
- The package name here is `com.example`.
- The module name is also `com.example` and matches the folder structure and `module-info.java`.

---

## Step 2: Create the `pom.xml` File

This is the Maven build configuration file. Create it at the root of your project.

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>(Defines the model version for POM. Always 4.0.0 for Maven 2+.)**

    <groupId>com.example</groupId>(Identifies your project's group (often matches package or domain reversed)**
    <artifactId>testproject1</artifactId>(The name of your built artifact (e.g., the .jar file will be testproject1-1.0-SNAPSHOT.jar).**
    <version>1.0-SNAPSHOT</version>(Your project version. SNAPSHOT means it’s still in development.)**
    <name>testproject</name>(Human-readable name for the project. Not used by Maven to build )**

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <junit.version>5.10.2</junit.version>
    </properties>


## 📦 Dependencies:


    <dependencies>(This block tells Maven which external libraries your code depends on.)**
        <!-- JavaFX -->
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-controls</artifactId>
            <version>21</version>
        </dependency>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-fxml</artifactId>
            <version>21</version>
        </dependency>


        <!-- JUnit -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>


    </dependencies>


## 🏗️ Build Section (How Maven builds your project)


    <build>(Here you define plugins that control how the code is compiled, run, and packaged.)**
        <plugins>
            <plugin> (🔧 Compiler PluginThis plugin compiles your .java files.source means: Java language level you're writing in.And target means: Bytecode level you're compiling to.)
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.13.0</version>
                <configuration>
                    <source>21</source>
                    <target>21</target>
                </configuration>
            </plugin>
            <plugin>(🚀 JavaFX Maven Plugin Purpose: Enables you to run JavaFX apps using:mvn javafx:run)
                <groupId>org.openjfx</groupId>
                <artifactId>javafx-maven-plugin</artifactId>
                <version>0.0.8</version>
                <executions>
                    <execution>
                        <!-- Default configuration for running with: mvn clean javafx:run -->
                        <id>default-cli</id>
                        <configuration>
                            <mainClass>
                                com.example/com.example.Main
                            </mainClass>
                            <launcher>app</launcher>
                            <jlinkZipName>app</jlinkZipName>
                            <jlinkImageName>app</jlinkImageName>
                            <noManPages>true</noManPages>
                            <stripDebug>true</stripDebug>
                            <noHeaderFiles>true</noHeaderFiles>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>(⚙️ Exec Maven Plugin:Allows you to run your Java app using:mvn exec:java)
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.5.0</version>
                <configuration>
                    <mainClass>com.example.Main</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

##If needed, you can define extra repositories in pom.xml like this:

<repositories>
  <repository>
    <id>my-private-repo</id>
    <url>https://my.custom.repo/maven</url>
  </repository>
</repositories>

---

## Step 3: Create module-info.java

Inside src/main/java/module-info.java:

module com.example {
    requires javafx.controls;
    requires javafx.fxml;

    opens com.example to javafx.fxml;
    exports com.example;
}


##  What is `module-info.java`?

Introduced in **Java 9**, it's the **declaration file for a module**.

- A **module** is a **self-contained unit of code** (a JAR or project) with:
  - explicitly defined **dependencies** (`requires`)
  - explicitly defined **public API** (`exports`)
  - **runtime access controls** (`opens`, `uses`, etc.)

This file enables **strong encapsulation** and **explicit dependency management** using the Java Module System.

---

Let's explain line by line:

---

##  module com.example {`

Defines the **module name**. This must match what you use in:

<mainClass>com.example/com.example.Main</mainClass> in your pom.xml

📌 **Important**: A modular project must declare its name here and match it when running with Java's module system (`java --module` or JavaFX plugin).


---


### 🔸 `requires javafx.controls;`

- Your module **depends on** the `javafx.controls` module.
- Needed for things like `Button`, `Label`, `Scene`, etc.

Without this, your code **won’t compile** if you use JavaFX classes.

---

### 🔸 `requires javafx.fxml;`

- Required if you're using `.fxml` files and loading them via `FXMLLoader`.
- The `javafx.fxml` module provides:
  - `FXMLLoader`
  - The annotation `@FXML`
  - Auto-wiring FXML controllers

---

### 🔸 `opens com.example to javafx.fxml;`

- Normally, **Java modules are closed**: other modules can't use reflection to access your internals.
- But JavaFX **requires reflection** to inject controllers and fields annotated with `@FXML`.

So:
- `opens com.example to javafx.fxml;` tells Java to **open** the `com.example` package **only to the `javafx.fxml` module**.
- This allows JavaFX to use reflection to:
  - Call controller constructors
  - Set fields annotated with `@FXML`
  - Call `@FXML`-annotated methods

🔐 You’re **not exporting it** — you’re just **allowing reflective access for injection**.

---

### 🔸 `exports com.example;`


## 🔍 What Does `exports com.example;` Do?

In modular Java (JPMS — Java Platform Module System), packages are **not visible by default** to other modules. That means:

- Even if a class is marked `public`, it **won’t be accessible** outside the module unless its package is explicitly **exported**.

The `exports` directive tells the Java compiler and runtime:“Make all **public** types (classes, interfaces, etc.) in this package available to other modules.”

---

## 📦 Let’s say you have:


module com.example {
    exports com.example;
}


And inside `com.example`, you have:


package com.example;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello from Main!");
    }
}

Now, this class can:
- Be **found by the Java launcher** when you run your program.
- Be **used by other modules** if they declare `requires com.example`.

---

## Why It’s Required for Main Class to be Found

Let’s look at this entry from your `pom.xml`:

<mainClass>com.example/com.example.Main</mainClass>

This tells the JavaFX Maven Plugin:

> "Launch the module `com.example` and look for the `Main` class inside the `com.example` package."

But here's the catch:
- If `com.example` is **not exported**, even though the `Main` class is public, the module system will **refuse access** to it from outside (including the launcher).
- You'll get runtime errors like:
  
  Error: class com.example.Main is in module com.example, which does not export package com.example

So:   **Exporting the package allows the Java launcher (or another module) to *see* and *use* your public classes.**

---

## 🧱 If Another Module Depends on Your Module

Let’s say someone else writes a module:


module other.module {
    requires com.example;
}

And their code does:

import com.example.Main;

public class Something {
    public void doStuff() {
        Main.main(new String[0]); // Try to call Main class
    }
}

This will only compile **if**:

✅ You exported `com.example`  
❌ If not, they’ll get a **compile-time error** — even if `Main` is public.

**The `exports` is like publishing an API** — it tells the compiler/runtime: “Hey, this package is safe and intended for others to use.”

---

## ❓ What Happens if You Remove `module-info.java`?

### 1. **You are no longer using JPMS.**
- Your project becomes a **traditional classpath-based application**.
- Java treats all packages as accessible and open.
- Reflection just works — no `opens` needed.

### 2. **JavaFX will still run** (if you pass the right VM options):
If you remove `module-info.java`, you'll have to use **VM arguments** like:


--add-opens=javafx.base/com.sun.javafx.event=ALL-UNNAMED


Or:


--module-path /path/to/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml


So:
- Without `module-info.java`, you're stuck doing this **manually**.
- Java can't validate dependencies at compile-time.

### 3. **Reflection becomes unrestricted.**
- Without JPMS, all code is open to reflective access.
- This can be a **security and encapsulation risk**.

---

## 💡 Why Keep `module-info.java`?

- **Compile-time validation** of dependencies
- Strong encapsulation — only expose what you intend
- Cleaner startup (you don’t need long `--module-path` commands if you use plugins like `javafx-maven-plugin`)
- Required if you want to use **`jlink`** to create custom Java runtimes
- Better performance and startup with smaller runtime images

---


## Step 4: Create Main.java

Create this file in src/main/java/com/example/Main.java:

package com.example;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class Main extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {
        Parent root = FXMLLoader.load(getClass().getResource("/Mainview.fxml"));
        primaryStage.setTitle("My First JavaFX FXML App");
        primaryStage.setScene(new Scene(root, 400, 300));
        primaryStage.show();
    }

    public static void main(String[] args) {
    	System.out.println("hello world");
        launch(args);
    }
}

---


## Step 5: Create layout.fxml

Place this inside src/main/resources/Mainview.fxml:

<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.layout.StackPane?>
<?import javafx.scene.control.Label?>

<StackPane xmlns="http://javafx.com/javafx/21" xmlns:fx="http://javafx.com/fxml/1">
    <Label text="Hello, JavaFX with FXML!" />
</StackPane>

---

## Step 6: Compile and Run

In your terminal, from the root project folder:

mvn clean compile

if no errors shows up you are good so far,and finally run this on your terminal:

mvn javafx:run













