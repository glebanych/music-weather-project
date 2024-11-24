# Weather and music 

There are multiple studies on how weather conditions influence our interest in certain genres and types of music ([1](https://www.ox.ac.uk/news/2023-05-03-here-comes-sun-new-study-shows-how-uk-weather-conditions-influence-music-success), [2](https://www.psychologytoday.com/us/blog/head-games/201711/when-seasons-change-so-do-musical-preferences-says-science)). Today, I will analyze my own music patterns from past year and how it correlates with weather.


## 1) Obtaining data

To start analysis, I need two datasets- my music listening history and past weather info in Kyiv, Ukraine.

My main music streaming service is Spotify, so all I need to do is head over to ([https://www.spotify.com/account/privacy/](https://www.spotify.com/account/privacy/)) and request streaming history for the past year.
![enter image description here](https://i.imgur.com/C6yxx3k.png)

It took a week to process my request, and I received a zip file with multiple JSON files. 
![enter image description here](https://i.imgur.com/XWRoPxl.png)

To get historical weather data I will use Visualcrossing, a leading provider of weather data and enterprise analysis tools to data scientists, business analysts, professionals, and academics. The problem is that I don’t know the date range of Spotify data yet.

Streaming data is stored in “StreamingHistory_music_0.json” and “StreamingHistory_music_1.json”. I will use Microsoft Power Query, a built-in data transformation engine of Microsoft Excel and import “___0.json” and “___1.json” to Sheet 1 and 2 respectively. 
![enter image description here](https://i.imgur.com/BUNOIWK.png)
![enter image description here](https://i.imgur.com/oUkRoaS.png)


After looking at both sheets, I see that Spotify has provided streaming history from  October 19, 2023 to October 19, 2024. That’s the date range I will use on Visualcrossing. 
![enter image description here](https://i.imgur.com/IOxW6zJ.png)


Now I have both weather and music data. The problem is that I need to label every artist with corresponding genre and Spotify doesn’t provide that information in exported .json files. To solve this, I will use Last.fm, one of the biggest music databases. They provide API for people to use and that’s exactly what I need.

After registering as a developer at ([https://www.last.fm/api/account/create](https://www.last.fm/api/account/create)) and getting my API key, I will check the documentation for the “artist.getTopTags” (https://www.last.fm/api/show/artist.getTopTags) request since Last.fm names genres as tags. After successfully requesting tags for the first artist from the list (Travis Scott) I can move to MS Excel to continue. 
![enter image description here](https://i.imgur.com/4dja3EE.png)

It would be time consuming to manually assign tags to every artist, so I will be using =WEBSERVICE function in MS Excel to send API requests to Last.FM. And since I need to use artists names from row “B”, I will use CONCATINATE function in addition to WEBSERVICE. Here’s the final formula to get tags for artist in cell B2: =WEBSERVICE(CONCATENATE("https://ws.audioscrobbler.com/2.0/?method=artist.gettoptags&artist=",B2,"&api_key=API_KEY"))

I will use autofill to make API calls for every row on Sheets 1 and 2.
![enter image description here](https://i.imgur.com/OXsaKeO.png)
![enter image description here](https://i.imgur.com/U9F1RZo.png)


Now I have all the data I need to proceed with data cleaning.


## Create files and folders

The file explorer is accessible using the button in left corner of the navigation bar. You can create a new file by clicking the **New file** button in the file explorer. You can also create folders by clicking the **New folder** button.

## Switch to another file

All your files and folders are presented as a tree in the file explorer. You can switch from one to another by clicking a file in the tree.

## Rename a file

You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.

## Delete a file

You can delete the current file by clicking the **Remove** button in the file explorer. The file will be moved into the **Trash** folder and automatically deleted after 7 days of inactivity.

## Export a file

You can export the current file by clicking **Export to disk** in the menu. You can choose to export the file as plain Markdown, as HTML using a Handlebars template or as a PDF.


# Synchronization

Synchronization is one of the biggest features of StackEdit. It enables you to synchronize any file in your workspace with other files stored in your **Google Drive**, your **Dropbox** and your **GitHub** accounts. This allows you to keep writing on other devices, collaborate with people you share the file with, integrate easily into your workflow... The synchronization mechanism takes place every minute in the background, downloading, merging, and uploading file modifications.

There are two types of synchronization and they can complement each other:

- The workspace synchronization will sync all your files, folders and settings automatically. This will allow you to fetch your workspace on any other device.
	> To start syncing your workspace, just sign in with Google in the menu.

- The file synchronization will keep one file of the workspace synced with one or multiple files in **Google Drive**, **Dropbox** or **GitHub**.
	> Before starting to sync files, you must link an account in the **Synchronize** sub-menu.

## Open a file

You can open a file from **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Open from**. Once opened in the workspace, any modification in the file will be automatically synced.

## Save a file

You can save any file of the workspace to **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Save on**. Even if a file in the workspace is already synced, you can save it to another location. StackEdit can sync one file with multiple locations and accounts.

## Synchronize a file

Once your file is linked to a synchronized location, StackEdit will periodically synchronize it by downloading/uploading any modification. A merge will be performed if necessary and conflicts will be resolved.

If you just have modified your file and you want to force syncing, click the **Synchronize now** button in the navigation bar.

> **Note:** The **Synchronize now** button is disabled if you have no file to synchronize.

## Manage file synchronization

Since one file can be synced with multiple locations, you can list and manage synchronized locations by clicking **File synchronization** in the **Synchronize** sub-menu. This allows you to list and remove synchronized locations that are linked to your file.


# Publication

Publishing in StackEdit makes it simple for you to publish online your files. Once you're happy with a file, you can publish it to different hosting platforms like **Blogger**, **Dropbox**, **Gist**, **GitHub**, **Google Drive**, **WordPress** and **Zendesk**. With [Handlebars templates](http://handlebarsjs.com/), you have full control over what you export.

> Before starting to publish, you must link an account in the **Publish** sub-menu.

## Publish a File

You can publish your file by opening the **Publish** sub-menu and by clicking **Publish to**. For some locations, you can choose between the following formats:

- Markdown: publish the Markdown text on a website that can interpret it (**GitHub** for instance),
- HTML: publish the file converted to HTML via a Handlebars template (on a blog for example).

## Update a publication

After publishing, StackEdit keeps your file linked to that publication which makes it easy for you to re-publish it. Once you have modified your file and you want to update your publication, click on the **Publish now** button in the navigation bar.

> **Note:** The **Publish now** button is disabled if your file has not been published yet.

## Manage file publication

Since one file can be published to multiple locations, you can list and manage publish locations by clicking **File publication** in the **Publish** sub-menu. This allows you to list and remove publication locations that are linked to your file.


# Markdown extensions

StackEdit extends the standard Markdown syntax by adding extra **Markdown extensions**, providing you with some nice features.

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.


## SmartyPants

SmartyPants converts ASCII punctuation characters into "smart" typographic punctuation HTML entities. For example:

|                |ASCII                          |HTML                         |
|----------------|-------------------------------|-----------------------------|
|Single backticks|`'Isn't this fun?'`            |'Isn't this fun?'            |
|Quotes          |`"Isn't this fun?"`            |"Isn't this fun?"            |
|Dashes          |`-- is en-dash, --- is em-dash`|-- is en-dash, --- is em-dash|


## KaTeX

You can render LaTeX mathematical expressions using [KaTeX](https://khan.github.io/KaTeX/):

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> You can find more information about **LaTeX** mathematical expressions [here](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference).


## UML diagrams

You can render UML diagrams using [Mermaid](https://mermaidjs.github.io/). For example, this will produce a sequence diagram:

```mermaid
sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```

And this will produce a flow chart:

```mermaid
graph LR
A[Square Rect] -- Link text --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
```
