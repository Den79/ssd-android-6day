# Android Development: Table-Based Apps
1. [ Introduction ](#1)
2. [ Getting Started ](#2)
3. [ Recycler Views ](#3)
4. [ Pass Recipes into RecyclerView ](#4)
5. [ Add a RecyclerView Layout ](#5)
6. [ Render Images from Web URL's ](#6)
7. [ Navigating to a Detail View ](#7)
8. [ In-Class Activity ](#8)
9. [ Assignment ](#9)

<a name="1"></a>
## 1) Introduction
Today we will be building an Android app similar to one that we will build in Swift called CookIt. Once you have built both apps (for iOS and Android) try to reflect on the build process to see similarities.

<a name="2"></a>
## 2) Getting Started
Start a new Android Studio Project using the Empty Activity Template called "CookIt".

We'll be adding a second activity for our detail screen later.

![](https://i.imgur.com/nqFnE5F.png)


<a name="3"></a>
## 3) Recycler Views

Add the following to your build.gradle file, and sync your project with the gradle files:

```text
implementation 'androidx.recyclerview:recyclerview:1.1.0'
```

<img src="https://i.imgur.com/TxZDHKm.png" width="350" /> </br>

### Add a RecyclerView to a Layout
In `activity_main.xml`, add the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recipe_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical" />

</android
```

### Update the Activity Class to Manage the RecyclerView
Inside our `MainActivity.kt` we will need to define how our RecyclerView is layed out and rendered. To do this we will reference the RecyclerView by id and attach a layoutManager and an adapter to it.

#### LayoutManager
Because we are only using a single column with a simple layout of 1 image and a text label we will be using a LinearLayoutManager. An alternative (for more of a grid like layout) is to use a GridLayoutManager where you can build more of a "tile" look.

In the `MainActivity` class add the following line to the onCreate function:

*Be sure to resolve all import requirments*
```kotlin
recipe_list.layoutManager = LinearLayoutManager(this)
```

#### Adapter
A RecyclerView adapter is responsible for creating each ViewHolder which will display our table data.

In the `MainActivity` again, let's add a RecipeAdapter class. You will notice that this class has three override methods which will:
* Define the layout that is used in the ViewHolder
* Define the number of rows or "item count"
* Bind the values associated with each of the ViewHolder elements

```kotlin
// This adapter will implement the necessary logic to define and populate our table
private class RecipeAdapter: RecyclerView.Adapter<RecipeViewHolder>() {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecipeViewHolder {
        TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
    }

    override fun getItemCount(): Int {
        TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
    }

    override fun onBindViewHolder(holder: RecipeViewHolder, position: Int) {
        TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
    }
}
```

#### ViewHolder 
The View Holder is the reusable view that can be used as a template for each item in the RecyclerView.

```kotlin
// This is a class to host the reusable view for each table row
private class RecipeViewHolder(view: View): RecyclerView.ViewHolder(view)
```

<a name="4"></a>
## 4) Pass Recipes into RecyclerView
Update `MainActivity.kt` so that we are passing a collection of recipes into our RecyclerView.

Create a class to hold our recipe data.
```kotlin
data class Recipe(val name: String, val imageURL: String, val steps: List<String>)
```

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        recipe_list.layoutManager = LinearLayoutManager(this)

        val recipes = listOf(
            Recipe("Chocolate Chip Cookies", "https://images.pexels.com/photos/230325/pexels-photo-230325.jpeg", listOf("Step 1", "Step 2")),
            Recipe("Best Brownies", "https://images.pexels.com/photos/45202/brownie-dessert-cake-sweet-45202.jpeg", listOf("Step 1", "Step 2", "Step 3")),
            Recipe("Banana Bread", "https://images.pexels.com/photos/830894/pexels-photo-830894.jpeg", listOf("Step 1", "Step 2"))
        )

        recipe_list.adapter = RecipeAdapter(recipes)
    }
```

```kotlin
private class RecipeAdapter(val recipes: List<Recipe>): RecyclerView.Adapter<RecipeViewHolder>()  {
...

```

We will need to pass the context as well so we can intialize the recipe view holder.

```kotlin
recipe_list.adapter = RecipeAdapter(recipes, this)
```

<a name="5"></a>
## 5) Add a RecyclerView Layout 

As the adapter defines each row in our table we will want to reference a consistent layout.
Add a New -> Layout resource file to your res/layout folder called item_recipe. This will be a constraint layout so that we can define the positioning of its elements based on our provided constraints.

![](https://i.imgur.com/oC6BVMT.png)

Add a simple `ImageView` to the `item_recipe.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <TextView
        android:id="@+id/item_recipe_name"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:layout_marginStart="10dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Add the layout to our recycler view in `activity_main.xml`

 ```kotlin
 tools:listitem="@layout/item_recipe"
 ```
Inside the `onCreateViewHolder` method, intialize the `RecipeViewHolder` with the context we passed into the constructor and instruct it to inflate items with the newly created layout.

```kotlin
private class RecipeAdapter(val recipes: List<Recipe>, val context: Context): RecyclerView.Adapter<RecipeViewHolder>()  {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecipeViewHolder {
        return RecipeViewHolder(LayoutInflater.from(context).inflate(R.layout.item_recipe, parent, false))
    }
...
```

Let's build out the `getItemCount` and `onBindViewHolder` methods:

```kotlin
override fun getItemCount(): Int {
    return recipes.count()
}

override fun onBindViewHolder(holder: RecipeViewHolder, position: Int) {
    val recipe = recipes[position]

    holder.itemView.item_recipe_name.text = recipe.name
}
```
Run the app:

<img src="https://i.imgur.com/rdiEu4T.png" width="300" /><br/>

<a name="6"></a>
## 6) Render Images from Web URL's 

Let's add the recipe image to each item view.

Add an ImageView to your item_recipe xml file and update our text view to sit right beside the image.

```xml
<ImageView
    android:id="@+id/item_recipe_image"
    android:layout_width="50dp"
    android:layout_height="50dp"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintBottom_toBottomOf="parent" />
    
<TextView
    android:id="@+id/item_recipe_name"
    android:layout_height="wrap_content"
    android:layout_width="wrap_content"
    android:layout_marginStart="10dp"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toEndOf="@id/item_recipe_image"
    app:layout_constraintBottom_toBottomOf="parent" />
```

Since we are loading our image from a URL, we're going to use a thrid party library called [Glide](https://bumptech.github.io/glide/).

Add the following to your build.gradle file and sync.

``` text
implementation 'com.github.bumptech.glide:glide:4.11.0'
```

In `MainActivity`, let's update the `onBindViewHolder` method to load an image from a URL.

```kotlin
override fun onBindViewHolder(holder: RecipeViewHolder, position: Int) {
    val recipe = recipes[position]

    holder.itemView.item_recipe_name.text = recipe.name
    Glide.with(context).load(recipe.imageURL).into(holder.itemView.item_recipe_image)
}
```

In order for this to work, we need to add the following to our AndroidManifest.xml file:

```text
<uses-permission android:name="android.permission.INTERNET"/>

<application
    android:usesCleartextTraffic="true"
```
Your manifest should look like this:

![](https://i.imgur.com/CsnKBju.png)

Run your app!

<img src="https://i.imgur.com/V0LuSt4.png" width="300" />


*If the images don't render after a couple of seconds, try uninstalling the app from the emulator and rebuilding.*

<a name="7"></a>
## 7) Navigating to a Detail View

We want to be able to click on each item in our recipe list and see a detailed view of that item on another screen.

In order to do this, we need to do a few things:

1) Capture a click event on each item
2) Navigate to another activity
3) Pass data about our recipe to the new activity

### Capturing Click Events

Add a click listener to our itemView inside the `onBindViewHolder` method:

```kotlin
override fun onBindViewHolder(holder: RecipeViewHolder, position: Int) {
    val recipe = recipes[position]

    holder.itemView.setOnClickListener {
        // this is the code that gets triggered when an itemView is tapped
    }

    holder.itemView.item_recipe_name.text = recipe.name
    Glide.with(context).load(recipe.imageURL).into(holder.itemView.item_recipe_image)
}
```

Since we want to actually transition screens when an item is clicked, we need to trigger this change from inside our MainActivity. To do this, we can pass a callback function as a parameter of our RecipeAdapter.

Change our `RecipeAdapter` declaration to include this parameter:

```kotlin
private class RecipeAdapter(val recipes: List<Recipe>, val context: Context, val recipeSelected: (Recipe) -> Unit): RecyclerView.Adapter<RecipeViewHolder>()  {
    ...
```

```kotlin
override fun onBindViewHolder(holder: RecipeViewHolder, position: Int) {
    val recipe = recipes[position]

    holder.itemView.setOnClickListener {
        recipeSelected(recipe)
    }

    holder.itemView.item_recipe_name.text = recipe.name
    Glide.with(context).load(recipe.imageURL).into(holder.itemView.item_recipe_image)
}
```

Update where we set the adapter on our recycler view to include this new parameter.

```kotlin
recipe_list.adapter = RecipeAdapter(recipes, this, recipeSelected = {
    // this is where we can transition to another activity
})
```

### Navigate to Another Activity
Let's create our detail activity:

Add a new activity called `RecipeDetailActivity`.

<img src="https://i.imgur.com/s3qUICj.png" />
<img src="https://i.imgur.com/Su6Z7XC.png" />

Open `AndroidManifest.xml` and add our new activity:

```xml
<activity android:name=".RecipeDetailActivity" />
```

Add a new layout file called `activity_recipe_detail.xml`.

<img src="https://i.imgur.com/ukVxiMQ.png" />

Add the following code:

```kotlin
package com.philweier.cookit

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_recipe_detail.*

class RecipeDetailActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_recipe_detail)

        recipe_name.text = "PLACEHOLDER" // temp placeholder
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <TextView
        android:id="@+id/recipe_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

And finally, update the callback function to start the new RecipeDetailActivity:
```kotlin
startActivity(Intent(this,RecipeDetailActivity::class.java))
```

Now when we click on an item, we should see our detail activity!

<img src="https://i.imgur.com/3HBxMhA.png" width="300" />

We want our detail activity to be different for each item. This means we need to pass the recipe object along to our detail view controller.

### Passing Data to New Activities

We're going to use a companion object to to set up our intent with a recipe.

```kotlin
package com.philweier.cookit

import android.content.Context
import android.content.Intent
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_recipe_detail.*

class RecipeDetailActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_recipe_detail)

        // get the recipe from the intent, if null will throw NPE
        val recipe: Recipe = intent?.extras?.getParcelable(recipeExtra)!!

        recipe_name.text = recipe.name
    }

    companion object {
        const val recipeExtra = "RECIPE"

        fun newIntent(recipe: Recipe, context: Context): Intent {
            val intent = Intent(context, RecipeDetailActivity::class.java)
            intent.putExtra(recipeExtra, recipe)
            return intent
        }
    }
}
```

In order to make sure we can pass a Recipe object from one activity to another, we need to parcelize our Recipe class by adding the `@Parcelize` annotation, and inheriting from `Parcelable`:

```kotlin
//Make the Recipe Parcelable so that it can be passed by the Bundle to another activity
@Parcelize
data class Recipe(val name: String, val imageURL: String, val steps: List<String>): Parcelable
```

Update the recipe adapter callback to use the `newIntent` companion object we just created (note that `it` is the recipe here):

```kotlin
recipe_list.adapter = RecipeAdapter(recipes, this, recipeSelected = {
    val intent = RecipeDetailActivity.newIntent(it, this)
    startActivity(intent)
})
```

Run your app - you should see the name of the recipe.

<img src="https://i.imgur.com/EGaqj8n.png" width="300" />

<a name="8"></a>
## 8) In-Class Activity

Add an `ImageView` to your recipe detail screen that uses the recipe's `imageURL`.

<img src="https://i.imgur.com/UYp0TzD.png" width="300" /> </br>

*Not an exhaustive list, but try using the following to style the text:*
```xml
android:layout_marginTop="10dp"
android:textAllCaps="true"
android:textStyle="bold"
android:textSize="15sp"
```

<a name="9"></a>
## 9) Assignment

Add a recycler view to hold the steps.

<img src="https://i.imgur.com/055KUQG.png" width="300" /> </br>

Submit the url for your github repo (add philweier as collaborator) to Learning Hub before our next Android class.
