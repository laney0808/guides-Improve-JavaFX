<frontmatter>
  title: "JavaFX tutorial part 1 – Introduction"
  pageNav: 2
</frontmatter>

# JavaFX tutorial part 1 – Introduction
<div style="color:gray; margin:0 0 20px 0; font-size:10px">Last updated: 13/8/2024 </div>

## Lifecycle of a JavaFX  application

Imagine yourself as a director of a play. First you provision the props that you will feature in your play. These can be hand props for your actors to interact with or even set dressings just to liven up the background. You then decide where to place the props for every scene. With a proper script in hand, you can finally approach a theatre and request for a stage. Thereafter, it’s just a matter of pulling the curtains on your masterpiece.

<puml src="images/javafx/JavaFxHierarchy.puml" />

A JavaFX application is like a play you are directing. Instead of creating props, you create `Node`s (`Node`s are the fundamental building blocks of a JavaFX application), and place them onto a `Scene` (a scene is a graph of `Node`s). Then, you set your `Scene` on a `Stage` provided by JavaFX. When you call `Stage#show()` method, JavaFX renders a window with your `Stage` on it.

Well, that's a very high-level view of how JavaFX works. The actual implementation of a Java FX is a bit more (ahem...) "involved". This tutorial takes you through the steps of building a typical Java FX application, using a chatbot application called Duke as a running example. Given below is what the end result can look like if you follow this tutorial until the end:

<video oncontextmenu="return false;" width="700px" autoplay muted loop>
<source src="videos/javafx/DynamicStyleDemo.mp4" type="video/mp4">
</video>

Let's get started!

## Setting up the project

To start, clone [this starter repo](https://github.com/se-edu/javafx-tutorial) to your computer, and open it in your favorite editor. As usual, configure the IDE to use the correct JDK version as well ([example](https://www.jetbrains.com/help/idea/sdk.html#set-up-jdk)).

## Setting up Java FX

Note: this tutorial assumes you will be using [Gradle](gradle.md) to manage dependencies of your project.

<div id="javafx-gradle">


Update your `build.gradle` to include the following lines:
```groovy
repositories {
    mavenCentral()
}

dependencies {
    String javaFxVersion = '17.0.7'

    implementation group: 'org.openjfx', name: 'javafx-base', version: javaFxVersion, classifier: 'win'
    implementation group: 'org.openjfx', name: 'javafx-base', version: javaFxVersion, classifier: 'mac'
    implementation group: 'org.openjfx', name: 'javafx-base', version: javaFxVersion, classifier: 'linux'
    implementation group: 'org.openjfx', name: 'javafx-controls', version: javaFxVersion, classifier: 'win'
    implementation group: 'org.openjfx', name: 'javafx-controls', version: javaFxVersion, classifier: 'mac'
    implementation group: 'org.openjfx', name: 'javafx-controls', version: javaFxVersion, classifier: 'linux'
    implementation group: 'org.openjfx', name: 'javafx-fxml', version: javaFxVersion, classifier: 'win'
    implementation group: 'org.openjfx', name: 'javafx-fxml', version: javaFxVersion, classifier: 'mac'
    implementation group: 'org.openjfx', name: 'javafx-fxml', version: javaFxVersion, classifier: 'linux'
    implementation group: 'org.openjfx', name: 'javafx-graphics', version: javaFxVersion, classifier: 'win'
    implementation group: 'org.openjfx', name: 'javafx-graphics', version: javaFxVersion, classifier: 'mac'
    implementation group: 'org.openjfx', name: 'javafx-graphics', version: javaFxVersion, classifier: 'linux'
}
```

</div>

<box type="tip" seamless>

After updating the `build.gradle` file, remember to reload the Gradle dependencies using your IDE UI, or by running the command `./gradlew clean build`. If using an IDE, you can restart the IDE for good measure.
</box>

## Writing your first Java FX program

As is customary, let’s start off with a simple “Hello World” program. Let's say you have a class named `Duke` that you want to make a GUI for. Let's call this GUI class `Main`. When using Java FX, this GUI class needs to extend `javafx.application.Application` which in turn requires you to override the abstract `Application#start(Stage)` method and provide a concrete implementation. The parameter `Stage` is the _primary stage_ that JavaFX provides.

```java{heading="Main.java"}
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.Label;
import javafx.stage.Stage;

public class Main extends Application {

    @Override
    public void start(Stage stage) {
        Label helloWorld = new Label("Hello World!"); // Creating a new Label control
        Scene scene = new Scene(helloWorld); // Setting the scene to be our Label

        stage.setScene(scene); // Setting the stage to show our screen
        stage.show(); // Render the stage.
    }
}
```

Note how we have created a `Label` to contain the text that we want to show. We then create the `Scene` and set its content. Finally, we set the stage and show it.

Next, we create another Java class, `Launcher`, as an entry point to our application (this class is needed to work around a classpath issue -- we can ignore the reason for now).

The `Launcher` class is reproduced below in its entirety.

```java{heading="Launcher.java"}
import javafx.application.Application;

/**
 * A launcher class to workaround classpath issues.
 */
public class Launcher {
    public static void main(String[] args) {
        Application.launch(Main.class, args);
    }
}
```

Now that we have changed the entry point to our application, we need to update the `mainClass`attribute in `build.gradle` accordingly to point at the `Launcher` class.

```groovy{highlight-lines="3-5" heading="build.gradle"}
// ...

application {
   mainClass.set("Launcher")
}

// ...
```


<box type="tip" seamless>

JavaFX creates the `Application` instance by calling the no-argument constructor. If there is no other constructor in the class (such as the `Main` we created above), there is no need to provide such a constructor because Java automatically provides a no-argument constructor when there are no other constructors.

But if you already have an existing constructor which takes arguments in that class (in which case Java will not provide a no-argument constructor automatically), you need to create an overloaded constructor with no arguments yourself. An example given below:

```java{highlight-lines="11-14" heading="Main.java"}
//...
private static final String DEFAULT_FILE_PATH = "duke/example.txt";

public class Main extends Application {

    // Existing constructor
    public Main(String filePath) {
        // ...
    }

    // Overloaded constructor
    public Main() {
        this(DEFAULT_FILE_PATH);
    }

    // ...
}
```

This approach enables JavaFX to create the `Application` instance using the newly defined no-argument constructor, while preserving the functionality of the existing constructor.

</box>

Now, run `Launcher` and you should see something like this:

![Hello World](images/javafx/HelloWorld.png)

<box type="info" seamless>

The following warning issued by Java runtime can be ignored. This warning appears when you use a later JavaFX version (e.g., 17) with a JDK version that doesn't support the _modules_ feature yet (e.g., Java 11).

>WARNING: Unsupported JavaFX configuration: classes were loaded from 'unnamed module @...
</box>

Congratulations! You have created your first GUI application!

## Exercises

1. We mentioned that `Node`s are the fundamental building blocks of JavaFX and used a `Label` as our root node in the HelloWorld application.
   1. What are some of the other types of `Node`s?
   1. How does JavaFX group them?

1. `Node`s can be interacted with like Plain Old Java Objects (POJO).
   1. What properties of a `Label` can you change programmatically?
   1. Try changing the `Label` to have a font of Arial at size 50.

1. You’ve learnt that a `Stage` can be thought of as a window.
   1. Can you have more than one `Stage` in an application?
   1. Try creating another stage and showing it! What happens?

[:fas-arrow-up: **ToC**](javaFx.md) | <span class="badge rounded-pill bg-primary">**++What's next?++**</span> [:fas-arrow-right: JavaFX tutorial part 2 - **Creating a GUI for Duke**](javaFxPart2.md)

--------------------------------------------------------------------------------
**Authors:**
* Initial Version: Jeffry Lum
* Contributors: Zhang Lanyu
