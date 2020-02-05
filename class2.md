# Android Development: Single View App

1. [ Prerequisites ](#1)
2. [ App Setup ](#2)
3. [ Structure of an App ](#3)
4. [ Layouts ](#4)
5. [ Running our App ](#5)
6. [ Adding Resources](#6)
7. [ View Elements](#7)
8. [ Exercise](#8)
9. [ Refactor To Use a Monster Class ](#9)
10. [ Assignment ](#10)

<a name="1"></a>
## 1) Prerequisites

### Install Android Studio

Before starting, please install Android studio by following the instructions found [here](https://developer.android.com/studio/install).

<a name="2"></a>
## 2) App Setup

Today we're going to build an app with a single screen called Monster Match:

<img src="https://i.imgur.com/926L140.gif" width="300" />

To get started, let's create a new Android Studio project.

<img src="https://i.imgur.com/udCsr0j.png" width="300" />

For the type of project, choose "Empty Activity".

<img src="https://i.imgur.com/MFNYZFE.png" />

Call your project "Monster Match" and change the package name to be `com.yourname.monstermatch`.

Choose the minimum API level of Android 5.0 (Lollipop).

<img src="https://i.imgur.com/U6c021g.png" />

### The Emulator

Android Studio makes it possible to test our apps on a virtual device called the emulator. We can also test our apps on real devices (more on how to do this later).

To setup an emulator, open the AVD Manager:

<img src="https://i.imgur.com/E6fUjmP.png" width="300" />

In the AVD manager, click on "Create Virtual Device"

<img src="https://i.imgur.com/PkPVAK5.png" />

Next, when choosing a device, select the Nexus 6.

<img src="https://i.imgur.com/PZJFJon.png" />

Next, we need to select a system image. Install Android Q if you haven't already. After the install finishes, click the "Finish" button to return to the AVD manager.
<img src="https://i.imgur.com/LRy7www.png" />
<img src="https://i.imgur.com/00Dq1kC.png" />

Select Android Q and click "Next"

<img src="https://i.imgur.com/P8Ex9oW.png" />

You should now see your emulator in the AVD manager:

<img src="https://i.imgur.com/5zKo8Wd.png" />

Close the AVD manager, and notice that your emulator is now listen under availabe devices:

<img src="https://i.imgur.com/wa8lOwf.png" width="300" />

<a name="3"></a>
## 3) Structure of An App

<img src="https://developer.android.com/topic/libraries/architecture/images/final-architecture.png" />

Some of this may look familiar to you as we've seen similar architectures on other platforms like .NET. We're going to focus on the top level view layer (activities) for now, but we will learn about the other layers as we move through the course.

Now that we've created our project and set up an emulator, look for the Project tab along the left hand side of Android Studio. Tap it if you don't currently see the project navigator panel. Open `MainActivity` inside `app/java/com.yourname.monstermatch`

<img src="https://i.imgur.com/w3m17kT.png" />

### Activities

In an Android app, Activities are the top-level UI components that users interact with. They form much of the backbone of the UI layer, and often contain and manage UI sub-components called fragments (more about these later).

We have one activity that was created by default for us called MainActivity.

### Android App Manifest

Every Android app must have an Android Manifest xml file located in the project root. It must be named `AndroidManifest.xml`. This file provides the Android build tools, the Android OS and Google Play with essential information about your app.

Locate our AndroidManifest.xml file:

Among other things, the manifest file describes the following:

1) The package name
2) The components of the app (activities etc.)
3) The app theme
4) App version information

<a name="4"></a>
## 4) Layouts

Layouts are files that define the UI structure of your app. All layout elements are built within a view hierarchy, similarly to the web and iOS. This hierarchy is composed of various elements that are either a View or a ViewGroup. A View draws something we can actually see on the screen, whereas a ViewGroup is meant more as a container that defines the structure of the layout.

Android Studio provides us with an interface we can use to change our views without writing any code, but the views are coded in xml.

Navigate to `res/layouts` and open `activity_main.xml`.

<img src="https://i.imgur.com/Ztb81zK.png" />

The Design view is open by default, but if you click on the Text subtab along the bottom of the Component Tree, you will be able to edit the xml associated with the layout.

<img src="https://i.imgur.com/akhyNMh.png" />

### Adding Views to a Layout
Let's remove the text view that was added for us, and add a button view.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Match Monsters!" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Android Studio is telling us there are a couple of issues with our code:
*Notice the red squiggly line and the highlighting on android:text="Match Monsters!"...*

<img src="https://i.imgur.com/hNmcgvM.png" width="300" />

### Adding Constraints to a View

1) We need to constrain the button, otherwise Android doesn't know where to position it.

```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Match Monsters!"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

This will position our button in the centre and along the bottom of the screen.

### Adding a String Resource

2) We need to add "Match Monsters!" as a string resource:

To do this, let's navigate to the `res/values` folder and open `strings.xml`.

Add an entry with name `match_monsters_button_text`.

<img src="https://i.imgur.com/6dTvegq.png)" />

Let's update our xml to consume our string resource instead of using a raw string.

```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/match_monsters_button_text"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

<a name="5"></a>
## 5) Running our App

Along the top toolbar, locate the "run" triangle, and click it:

<img src="https://i.imgur.com/eXXWSpP.png" />

After your app successfully runs, you should see this:

<img src="https://i.imgur.com/KUw9Hnf.png" width="300" />

If you run into issues running your project, try to "clean" the project as a first troubleshooting step.

<img src="https://i.imgur.com/TQbTUkU.png" width="300" />

Our button doesn't currently do anything so, let's get started building out our MonsterMatch app.

<a name="6"></a>
## 6) Adding Resources

Ensure you have the course resources downloaded to your computer.

In Android Studio, locate and click on the "Resource Manager" (along the left-hand-side). Tap the "+" button and select "Import Drawables".

<img src="https://i.imgur.com/NurFJNj.png" width="300" />

Locate and select the monster images from the course package.

<img src="https://i.imgur.com/HfRz6ps.png" />

There's no need to change any of the default settings:

<img src="https://i.imgur.com/nNe9MHT.png" />
<img src="https://i.imgur.com/k5Ioii4.png" />

Once the images are added, you should see them in the resource manager.

<img src="https://i.imgur.com/UBpTVQ3.png" width="300" />

Select the "Project" tab and look in `res/drawable`. You should see your images here.

<img src="https://i.imgur.com/ZmuXaHD.png" width="300" />

<a name="7"></a>
## 7) View Elements

Let's add an image to our app. Open the main activity layout file and add an ImageView above our button.

```xml
<ImageView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/monster1_head" />
```

### Exercise

Without looking at the answer below, try to use constraints to align our image in the centre and along the top of the parent view.

<img src="https://i.imgur.com/KiGa4G6.png" width="300" />

### Answer

```xml
<ImageView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/monster1_head"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

### Access a Specific View Within the Activity

In order to access a specific view element from within our activity, we need to give it a unique id.

```xml
<ImageView
    android:id="@+id/monster_head_image_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/monster1_head"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

```kotlin
class MainActivity: AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        monster_head_image_view.setImageResource(R.drawable.monster1_head)
    }
}
```
### Resolving Unresolved Reference Errors
If you get an unresolved reference error on a view...ensure that the id previously added to the xml is used within the activity class. Once you are sure that you are using targeting the appropriate view element, if you click the offending code and can get the blue text box shown below to appear, press option+return (as noted at the end of the blue text box). This will import the necessary reference to all the sub-views of the current content view.

<img src="https://i.imgur.com/MTGnCR9.png" /> <br />

hit option+return and see added import statement.

<img src="https://i.imgur.com/ANuuj0z.png" />

### Picking a Random Image

Let's store the resource references for each of our 'head' images in a list.

We can then call `list.random()` to pick a random item from the list.

```kotlin
class MainActivity: AppCompatActivity() {
    private val heads: List<Int> = listOf(R.drawable.monster1_head, R.drawable.monster2_head, R.drawable.monster3_head)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        shuffleMonsters()

        match_button.setOnClickListener {
            shuffleMonsters()
        }
    }

    private fun shuffleMonsters() {
        monster_head_image_view.setImageResource(heads.random())
    }
}
```
### Test Your App

<img src="https://i.imgur.com/xI0w9uo.gif" />

### Add Another Image View
Let's add another image view to hold the body image. Remember to give it a unique id and align it under the head image.

*\*Note: I have added the adjustViewBounds set to true in both ImageView elements...this sets the outer limits of the element to the size of the actual image that is rendered within it.* 

```xml
<ImageView
    android:id="@+id/monster_head_image_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/monster1_head"
    android:adjustViewBounds="true"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />

<ImageView
    android:id="@+id/monster_body_image_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:src="@drawable/monster1_body"
    android:adjustViewBounds="true"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@id/monster_head_image_view"
    app:layout_constraintEnd_toEndOf="parent" />
```

```kotlin
class MainActivity: AppCompatActivity() {
    private val heads: List<Int> = listOf(
        R.drawable.monster1_head,
        R.drawable.monster2_head,
        R.drawable.monster3_head
    )

    private val bodies: List<Int> = listOf(
        R.drawable.monster1_body,
        R.drawable.monster2_body,
        R.drawable.monster3_body
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        shuffleMonsters()

        match_button.setOnClickListener {
            shuffleMonsters()
        }
    }

    private fun shuffleMonsters() {
        monster_head_image_view.setImageResource(heads.random())
        monster_body_image_view.setImageResource(bodies.random())
    }
}
```

### Test Your App

<img src="https://i.imgur.com/myJgLsn.gif" />

<a name="8"></a>
## 8) Exercise

Add the monster feet

<img src="https://i.imgur.com/xQBhMou.gif" />"

<a name="9"></a>
## 9) Refactor To Use a Monster Class

Instead of using 3 int array's we can use one monster array where each monster has a head, body, and feet property.

Under your activity, add a data class to hold a monster.

### Answer

```kotlin
data class Monster(val head: Int, val body: Int, val feet: Int)
```

Let's refactor our MainActivity to use our monsters data class.

```kotlin
class MainActivity: AppCompatActivity() {
    private val monsters: List<Monster> = listOf(
        Monster(R.drawable.monster1_head, R.drawable.monster1_body, R.drawable.monster1_feet),
        Monster(R.drawable.monster2_head, R.drawable.monster2_body, R.drawable.monster2_feet),
        Monster(R.drawable.monster3_head, R.drawable.monster3_body, R.drawable.monster3_feet)
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        shuffleMonsters()

        match_button.setOnClickListener {
            shuffleMonsters()
        }
    }

    private fun shuffleMonsters() {
        monster_head_image_view.setImageResource(monsters.random().head)
        monster_body_image_view.setImageResource(monsters.random().body)
        monster_feet_image_view.setImageResource(monsters.random().feet)
    }
}

data class Monster(val head: Int, val body: Int, val feet: Int)
```

<a name="10"></a>
## 10) Assignment

Show a success label on the screen that displays that monsters name if the user matches a monster.

*Hint: Your new view that holds the success text could have some XML similar to the following:*
``` xml
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:layout_marginBottom="12dp"
android:textColor="@color/colorAccent"
android:textSize="17sp"
android:textStyle="bold"
app:layout_constraintBottom_toTopOf="@id/match_button"
```

*This is by no means an exhaustive list :)*

### Working Example

<img src="https://i.imgur.com/926L140.gif" />

