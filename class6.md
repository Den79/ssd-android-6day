# Android Development: API-Based Apps

1. [ Introduction ](#1)
2. [ Getting Started ](#2)
3. [ Storing API Keys in local.properties ](#3)
4. [ Review the API Docs ](#4)
5. [ Retrofit and Moshi ](#5)
6. [ Coroutines ](#6)
7. [ Refactor the API Code ](#7)
8. [ Adding the Article Image ](#8)
9. [ ViewModels and LiveData ](#9)
10. [ Swipe to Refresh ](#10)
11. [ WebView ](#11)


<a name="1"></a>
## 1) Introduction
We're building a news app calls Nooz. Our app will consume the NYT developer API.

<a name="2"></a>
## 2) Getting Started

Create a new android application called `Nooz` in the same way we've created each of our previous projects.

We will be using the NYT [Most Popular](https://developer.nytimes.com/docs/most-popular-product/1/overview) public API for our app.

Go to [https://developer.nytimes.com/](https://developer.nytimes.com/) and follow [these instructions](https://developer.nytimes.com/get-started) to add an app and get an api key.

Be sure to select "Most Popular":

<img src="https://i.imgur.com/8zyX486.png" />

Under your username you can see your created "Apps" and copy the API Key that was generated:

<img src="https://i.imgur.com/5H2tfSa.png" />


<a name="3"></a>
## 3) Storing API Keys in local.properties

1) Add the API key to your `local.properties` file:

```groovy
apiKey="<value>"
```

2) Add to the root level of your app-level `build.gradle` file:

```groovy
def localProperties = new Properties()
localProperties.load(new FileInputStream(rootProject.file("local.properties")))
```

3) Add to the `android { defaultConfig { } }` block of your app-level `build.gradle` file:

```groovy
android {
    ...
    
    defaultConfig {
        ...
        buildConfigField "String", "API_KEY", localProperties['apiKey']
    }
    
    ...
    
}
```

4) Sync Gradle and build the project. You can now reference the key like this. Keep this in mind as we will need it in a moment.

```kotlin
val apiKey = BuildConfig.API_KEY
```

<a name="4"></a>
## 4) Review the API Docs
Before we move on, let's take a look at the endpoint we will be consuming:

[GET viewed](https://developer.nytimes.com/docs/most-popular-product/1/routes/viewed/%7Bperiod%7D.json/get)

You can paste the endpoint with query parameters into the browser to see the response JSON.

```
https://api.nytimes.com/svc/mostpopular/v2/viewed/1.json?api-key=YOUR_API_KEY
```

Here's the general structure:

```json
{
   "status":"OK",
   "copyright":"Copyright (c) 2020 The New York Times Company.  All Rights Reserved.",
   "num_results":20,
   "results":[
      {
          // an article
      },
      {
          // another article
      },
      ...   
```

Here are some real results:

```json
{
   "status":"OK",
   "copyright":"Copyright (c) 2020 The New York Times Company.  All Rights Reserved.",
   "num_results":20,
   "results":[
      {
         "uri":"nyt://article/940caf64-4c4b-57a2-8548-40ae4c9d213b",
         "url":"https://www.nytimes.com/2020/02/25/world/asia/coronavirus-news.html",
         "id":100000006997520,
         "asset_id":100000006997520,
         "source":"New York Times",
         "published_date":"2020-02-25",
         "updated":"2020-02-28 08:03:31",
         "section":"World",
         "subsection":"Asia Pacific",
         "nytdsection":"world",
         "adx_keywords":"Coronavirus (2019-nCoV);Epidemics;Travel Warnings;United States Economy;Stocks and Bonds;New York Times;Italy;Iran;China",
         "column":null,
         "byline":"",
         "type":"Article",
         "title":"U.S. Soldier Tests Positive for Virus in South Korea",
         "abstract":"The soldier, the first U.S. service member known to be infected, has been quarantined in his off-base residence, the military said.",
         "des_facet":[
            "Coronavirus (2019-nCoV)",
            "Epidemics",
            "Travel Warnings",
            "United States Economy",
            "Stocks and Bonds"
         ],
         "org_facet":[
            "New York Times"
         ],
         "per_facet":[

         ],
         "geo_facet":[
            "Italy",
            "Iran",
            "China"
         ],
         "media":[
            {
               "type":"image",
               "subtype":"photo",
               "caption":"The entrance to Camp Walker, a U.S. Army base in Daegu, South Korea, on Thursday. A U.S. soldier found to be infected with the coronavirus had visited the base on Monday.",
               "copyright":"Kim Hyun-Tae/Yonhap, via Associated Press",
               "approved_for_syndication":1,
               "media-metadata":[
                  {
                     "url":"https://static01.nyt.com/images/2020/02/26/world/26virus-briefing-1sub/26virus-briefing-1sub-thumbStandard-v3.jpg",
                     "format":"Standard Thumbnail",
                     "height":75,
                     "width":75
                  },
                  {
                     "url":"https://static01.nyt.com/images/2020/02/26/world/26virus-briefing-1sub/26virus-briefing-1sub-mediumThreeByTwo210-v3.jpg",
                     "format":"mediumThreeByTwo210",
                     "height":140,
                     "width":210
                  },
                  {
                     "url":"https://static01.nyt.com/images/2020/02/26/world/26virus-briefing-1sub/26virus-briefing-1sub-mediumThreeByTwo440-v3.jpg",
                     "format":"mediumThreeByTwo440",
                     "height":293,
                     "width":440
                  }
               ]
            }
         ],
         "eta_id":0
      },
      {
         "uri":"nyt://article/bf13690b-edb9-5e3e-bce1-bc700c7313ea",
         "url":"https://www.nytimes.com/2020/02/05/opinion/trump-senate-acquittal-impeachment.html",
         "id":100000006960179,
         "asset_id":100000006960179,
         "source":"New York Times",
         "published_date":"2020-02-05",
         "updated":"2020-02-09 12:02:08",
         "section":"Opinion",
         "subsection":"",
         "nytdsection":"opinion",
         "adx_keywords":"Impeachment;Trump-Ukraine Whistle-Blower Complaint and Impeachment Inquiry;United States Politics and Government;Trump, Donald J;Republican Party;Senate",
         "column":null,
         "byline":"By Sherrod Brown",
         "type":"Article",
         "title":"In Private, Republicans Admit They Acquitted Trump Out of Fear",
         "abstract":"One journalist remarked to me, “How in the world can these senators walk around here upright when they have no backbone?”",
         "des_facet":[
            "Impeachment",
            "Trump-Ukraine Whistle-Blower Complaint and Impeachment Inquiry",
            "United States Politics and Government"
         ],
         "org_facet":[
            "Republican Party",
            "Senate"
         ],
         "per_facet":[
            "Trump, Donald J"
         ],
         "geo_facet":[

         ],
         "media":[
            {
               "type":"image",
               "subtype":"photo",
               "caption":"Fear of President Trump guides many Republicans in Congress. ",
               "copyright":"Doug Mills/The New York Times",
               "approved_for_syndication":1,
               "media-metadata":[
                  {
                     "url":"https://static01.nyt.com/images/2020/02/05/opinion/05Brown/05Brown-thumbStandard.jpg",
                     "format":"Standard Thumbnail",
                     "height":75,
                     "width":75
                  },
                  {
                     "url":"https://static01.nyt.com/images/2020/02/05/opinion/05Brown/05Brown-mediumThreeByTwo210.jpg",
                     "format":"mediumThreeByTwo210",
                     "height":140,
                     "width":210
                  },
                  {
                     "url":"https://static01.nyt.com/images/2020/02/05/opinion/05Brown/05Brown-mediumThreeByTwo440.jpg",
                     "format":"mediumThreeByTwo440",
                     "height":293,
                     "width":440
                  }
               ]
            }
         ],
         "eta_id":0
      }
   ]
}
```

For our app we will be ommitting much of this in our object mapping as we are most interested in the Article title, url and a single piece of media related to the article like an image.

<a name="5"></a>
## 5) Retrofit and Moshi

In order to consume an API on the app we need an HTTP client. One of the most commonly used clients is [Retrofit](https://square.github.io/retrofit/).

Add the following to your build.gradle file and sync.

```groovy
implementation "com.squareup.retrofit2:retrofit:2.6.2"
implementation "com.squareup.moshi:moshi-kotlin:1.8.0"
implementation "com.squareup.moshi:moshi-adapters:1.8.0"
implementation "com.squareup.retrofit2:converter-moshi:2.6.2"
```

We are also using a library called [Moshi](https://github.com/square/moshi) to help us map our JSON response to Kotlin objects.

### Values we will need to know
#### Base url
The base url for the NYT API is: `https://api.nytimes.com/svc/`

#### Default endpoint
And the endpoint we are hoping to access is:
`mostpopular/v2/viewed/1.json`

#### Adding our API key
We need to include our api key as a query param:
`mostpopular/v2/viewed/1.json?api-key=YOUR_API_KEY`


### Wiring up a simple connection
For now we will test our ability to make an initial call to the API directly from our `MainActivity`:

```kotlin
// Moshi to help parse our JSON response into kotlin class objects
private val moshi = Moshi.Builder()
    .add(KotlinJsonAdapterFactory())
    .build()
```

```kotlin
// Retrofit as our HTTP Client to interact with the Api
private val retrofit: Retrofit? = Retrofit.Builder()
    .addConverterFactory(MoshiConverterFactory.create(moshi))
    .baseUrl("https://api.nytimes.com/svc/")
    .build()
```

```kotlin
// Retrofit requires us to add an interface (see below)
// within which we add method declarations to outline
// the endpoints that we want to access
private var api: API = retrofit!!.create<API>(API::class.java)
```

```kotlin
// Retrofit requires us to use an interface, this is where we define our endpoints
interface API {
    @GET("mostpopular/v2/viewed/1.json")
    suspend fun getPopularArticlesAsync(@Query("api-key") apiKey: String): ArticleResponse
}
```

Notice how our suspend function references ArticleResponse...let's add two data classes to help us define the objects that we will be parsing from the API JSON.
Start by adding a new package called `network` under java/com.yourname.nooz and inside the `network` package add a Kotlin file called `Article.kt`:
![](https://i.imgur.com/9qXF3ap.png)

Add the following models to your `Article.kt` file:

```kotlin
data class Article(
    val title: String
)

data class ArticleResponse(
    val results: List<Article>
)
```
**Note that the property names need to match the JSON attribute names exactly for a successful mapping.**


<a name="6"></a>
## 6) Coroutines

A coroutine is a concurrency design pattern that you can use on Android to simplify code that executes asynchronously. Coroutines help to manage async tasks that could otherwise block the main thread and cause your app to freeze. It also helps with code readability as we no longer need an explicit callback.

Add the following to build.gradle and sync.

```groovy
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.0"
```
### Dispatchers

Kotlin coroutines use dispatchers to determine which threads are used for coroutine execution. In Kotlin, all coroutines must run in a dispatcher, even when they're running on the main thread. Coroutines can suspend themselves, and the dispatcher is responsible for resuming them.

To specify where the coroutines should run, Kotlin provides three dispatchers, today we're only worried about `Dispatchers.Main`.

We use `Dispatchers.Main` to run a coroutine on the main Android thread. This should be used only for interacting with the UI and performing quick work (ie. calling suspend functions or updating ui elements).

Add an id to the TextView in `activity_main.xml`:

```xml
android:id="@+id/introText"
```

And to finalize our edits to `MainActivity`:
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    // We will use a coroutine to help us simplify the asynchronous code
    getPopularArticles()
}
```

```kotlin
// This is our coroutine (by adding GlobalScope launch Main)
private fun getPopularArticles() = GlobalScope.launch(Dispatchers.Main){
    // This implements the interface asynchronously
    // without blocking the main thread and potentially freezing our app
    val firstResult: Article = api.getPopularArticlesAsync(BuildConfig.API_KEY).results.first()

    // Upon completion of the HTTP request we update the layout element
    introText.text = firstResult.title
}
```
In the example above, `api.getPopularArticlesAsync(BuildConfig.API_KEY)` still runs on the main thread, but it suspends the coroutine before it starts the network request. When the network request completes, `getPopularArticlesAsync` resumes the suspended coroutine instead of using a callback to notify the main thread.

Add the following to the `AndroidManifest.xml` file.

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### Test the Call to the API!

<a name="7"></a>
## 7) Refactor the API Code  
We should not be defining and executing our HTTP Client directly within an activity class. Let's move this code into our network package.
Add a file to `network` called `API.kt`.
Wrap the moshi and retrofit configuration into a class called NewsAPI:


```kotlin
class NewsAPI() {
    // Moshi to help parse our JSON response into kotlin class objects
    private val moshi = Moshi.Builder()
        .add(KotlinJsonAdapterFactory())
        .build()

    // Retrofit as our HTTP Client to interact with the Api
    private val retrofit: Retrofit? = Retrofit.Builder()
        .addConverterFactory(MoshiConverterFactory.create(moshi))
        .baseUrl("https://api.nytimes.com/svc/")
        .build()

    // Retrofit requires us to add an interface (see below)
    // within which we add method declarations to outline
    // the endpoints that we want to access
    private var api: API = retrofit!!.create<API>(API::class.java)

    // This is our coroutine (by adding GlobalScope launch Main)
    private fun getPopularArticles() = GlobalScope.launch(Dispatchers.Main){
        // This implements the interface asynchronously
        // without blocking the main thread and potentially freezing our app
        val firstResult: Article = api.getPopularArticlesAsync(BuildConfig.API_KEY).results.first()

    }
}

// Retrofit requires us to use an interface, this is where we define our endpoints
interface API {
    @GET("mostpopular/v2/viewed/1.json")
    suspend fun getPopularArticlesAsync(@Query("api-key") apiKey: String): ArticleResponse
}
```

### Modify getPopularArticles
Now that we are no longer inside the MainActivity we will want to identify getPopularArticles as a function that can suspend execution on a thread until an asynchronous task is complete. To do this we can simply add the suspend keyword infront of the function declaration.
We will also want to return a full collection of Articles rather than targeting the title of the first result:

```kotlin
suspend fun getPopularArticles() : List<Article>? {
    return try {
        val response = api.getPopularArticlesAsync(BuildConfig.API_KEY)
        response.results
    } catch (e: Exception) {
        Log.e(e.toString(), e.localizedMessage)
        null
    }
}
```

Update the `MainActivity` to use the newly added NewsAPI class:

```kotlin
class MainActivity : AppCompatActivity() {
    private val api = NewsAPI()
    private var articles: List<Article> = listOf()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // We will use a coroutine to help us simplify the asynchronous code
        loadPopularArticles()
    }

    private fun loadPopularArticles() = GlobalScope.launch(Dispatchers.Main) {
        val popularArticles = api.getPopularArticles()
        if(popularArticles != null) {
            articles = popularArticles
            introText.text = articles.first().title
        }
    }
}
```

### Exercise (25 mins)

Add a recycler view to the main activity to show the title of each of our article objects.

For a different look than previous apps let's use a grid layout (with 2 columns per row) instead of a linear layout.

Some "helper" code:

```kotlin
article_list.layoutManager = GridLayoutManager(this, 2)

...

private fun loadPopularArticles() = GlobalScope.launch(Dispatchers.Main) {
    val popularArticles = api.getPopularArticles()

    if (popularArticles != null) {
        articles = popularArticles
        update()
    }
}

private fun update() {
    article_list.adapter = ArticlesAdapter(articles, this)
}

...

```

----

![](https://i.imgur.com/EIb0DOq.png)


<a name="8"></a>
## 8)Adding the Article Image

Let's go back to our JSON to understand how to get the url for the article's image(s).

Each Article resource has a media resource which has an array of media-metadata resources.

We want to get the url from the first media-metadata resource.

```json
...

"media":[
    {
       "type":"image",
       "subtype":"photo",
       "caption":"The entrance to Camp Walker, a U.S. Army base in Daegu, South Korea, on Thursday. A U.S. soldier found to be infected with the coronavirus had visited the base on Monday.",
       "copyright":"Kim Hyun-Tae/Yonhap, via Associated Press",
       "approved_for_syndication":1,
       "media-metadata":[
          {
             "url":"https://static01.nyt.com/images/2020/02/26/world/26virus-briefing-1sub/26virus-briefing-1sub-thumbStandard-v3.jpg",
             "format":"Standard Thumbnail",
             "height":75,
             "width":75
          },
          ...
    ...
```

Update `network/Article.xml`:

```kotlin
data class Article(
    val title: String,
    val media: List<Media>
)

data class ArticleResponse(
    val results: List<Article>
)

data class Media(
    val type: String,
    // media-metadata is not a very kotlin-ey name,
    // so we can change it by add the @Json annotation
    //  where name is the json key that we want to map
    @Json(name = "media-metadata") val mediaMetadata: List<MediaMetadata>
)

data class MediaMetadata(
    val url: String
)
```

Let's update our `item_article.xml` layout file with an ImageView and make it look a little nicer.

```xml
<ImageView
    android:id="@+id/item_image"
    android:layout_width="100dp"
    android:layout_height="wrap_content"
    android:layout_marginTop="20dp"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent" />

<TextView
    android:id="@+id/item_title"
    android:layout_width="wrap_content"
    android:layout_height="80dp"
    android:textAlignment="viewStart"
    android:layout_marginTop="5dp"
    android:layout_marginStart="20dp"
    android:layout_marginEnd="20dp"
    android:textSize="17sp"
    android:textStyle="bold"
    app:layout_constraintTop_toBottomOf="@id/item_image"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```
Add Glide to help us with rendering our images:
```groovy
// external resources
implementation 'com.github.bumptech.glide:glide:4.11.0'
```
In ArticleAdapter, let's update `onBindViewHolder` to set the image url:

```kotlin
article.media.firstOrNull()?.mediaMetadata?.firstOrNull()?.url.let {
    Glide.with(context).load(it).into(holder.itemView.item_image)
}
```

### Test the App!

<a name="9"></a>
## 9) View Models and Live Data

Right now in our MainActivity, we maintain a list of Articles. Sometimes, due to various forces (like a change in orientation for instance), our activity could get thrown away and regenerated. In our case, this would just force us to make another call to the NYT api, but in other situations, important in-memory data could be lost.

For this reason, it is helpful to use a view model to maintain state.

Add the following to build.gradle:

```groovy
implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.2.0'
implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.2.0'
implementation "org.koin:koin-android-viewmodel:2.1.1"
```

We are also adding a library called [Koin](https://insert-koin.io/), which makes accessing our view models (and other dependencies) a lot easier.

We have some configuration setup to do. Add a file called `App.kt`:

```kotlin
class App: Application() {
    override fun onCreate() {
        super.onCreate()

        startKoin {
            androidContext(this@App)
            modules(AppModule)
        }
    }
}

val AppModule = module {
    viewModel { ArticlesViewModel() }
}
```

In `AndroidManifest.xml`, add the following line as an application tag attribute:

```xml
android:name=".App"
```

Add a new file called `ArticlesViewModel` in the same folder as `MainActivity`.

```kotlin
class ArticlesViewModel(): ViewModel(), LifecycleObserver {
    private val api = NewsAPI()
    val articles = MutableLiveData<List<Article>>()

    fun loadPopularArticles() = GlobalScope.launch(Dispatchers.Main) {
        val popularArticles = api.getPopularArticles()

        if (popularArticles != null) {
            articles.value = popularArticles
        }
    }
}
```

Note that articles is a MutableLiveData type. MutableLiveData or LiveData is an observable, app lifecycle-aware data holder class. This awareness ensures LiveData only updates app component observers that are in an active lifecycle state.

Let's update MainActivity:

```kotlin
class MainActivity : AppCompatActivity() {
    private val articlesViewModel: ArticlesViewModel by viewModel()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        article_list.layoutManager = GridLayoutManager(this, 2)

        articlesViewModel.loadPopularArticles()
        
        articlesViewModel.articles.observe(this, Observer { articles -> 
            articles ?: return@Observer 
            article_list.adapter = ArticleAdapter(articles, this)
        })     
    }
}
```

### Test the App! You should see the same list of articles as before.


<a name="10"></a>
## 10) Swipe to Refresh

We need a way to pull in fresh news data from our API. Let's do this by adding a Swipe to Refresh layout to our main activity layout (this is also known as Pull to Refresh).

Add the following line to build.gradle:

```groovy
implementation "androidx.swiperefreshlayout:swiperefreshlayout:1.0.0"
```

Let's update `activity_main.xml` to include the swipe to refresh layout.

```xml
<androidx.swiperefreshlayout.widget.SwipeRefreshLayout
        android:id="@+id/swipe_refresh_articles_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/article_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        tools:listitem="@layout/item_article" />

</androidx.swiperefreshlayout.widget.SwipeRefreshLayout>
```

Note that we are just wrapping our recycler view in the SwipeToRefresh layout component.

In MainActivity, inside the oncreate method, add the following:

```kotlin
swipe_refresh_articles_layout.setOnRefreshListener {
    articlesViewModel.loadPopularArticles()
    swipe_refresh_articles_layout.isRefreshing = false
}
```

### Test the App! Swipe down to see the refresh in action.

<a name="11"></a>
## 11) WebView

It would be really nice to be able to read the articles in our list. Since the NYT has a pretty nice mobile-friendly web presence, we can load each article url into a webview so we can read articles without leaving our app.

Add a new layout file called `activity_article.xml`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <WebView
        android:id="@+id/article_webview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Add a new activity called `ArticleActivity` in the same package as `MainActivity`.

```kotlin
class ArticleActivity: AppCompatActivity() {
    private lateinit var article: Article

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_article)

        article = intent?.extras?.getParcelable(articleExtra)!!

        article_webview.loadUrl(article.url)
    }

    companion object {
        const val articleExtra = "ARTICLE"

        fun newIntent(article: Article, context: Context): Intent {
            val intent = Intent(context, ArticleActivity::class.java)
            intent.putExtra(articleExtra, article)
            return intent
        }
    }
}
```

Don't forget to add our new activity to `AndroidManifest.xml`

```xml
<activity
    android:name=".ArticleActivity"
    android:windowSoftInputMode="adjustResize" />
```

Whenever we tap an article from our list of popular articles, we want to load a webview with that article loaded.

Update `ArticleAdapter` to take a third argument called `articleSelected`.

```kotlin
private class ArticlesAdapter(private val articles: List<Article>, val context: Context, val articleSelected: (Article) -> Unit): RecyclerView.Adapter<ArticlesViewHolder>() {
...
```

Update `onBindViewHolder` to capture click events on items in the list:

```kotlin
holder.itemView.setOnClickListener {
    articleSelected(article)
}
```

In `MainActivity` update where we initialize the ArticleAdapter to include this new function argument, within which we would like to trigger the ArticleActivity with the article that was selected.

```kotlin
article_list.adapter = ArticlesAdapter(articles, this) {
    val intent = ArticleActivity.newIntent(it, this)
    startActivity(intent)
}
```

In order for the above to work, we need to ensure that our Article and related resources are parcelable and can be sent from one activity to another.

Update `network/Activity.kt` to the following

```kotlin
@Parcelize
data class Article(
    val title: String,
    val url: String,
    val media: List<Media>
): Parcelable

data class ArticleResponse(
    val results: List<Article>
)

@Parcelize
data class Media(
    val type: String,
    @Json(name = "media-metadata") val mediaMetadata: List<MediaMetadata>
): Parcelable

@Parcelize
data class MediaMetadata(
    val url: String
): Parcelable
```

### Test the App!  Try selecting an article.

![](https://i.imgur.com/ynHiaCO.png)
