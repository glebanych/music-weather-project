![Text](https://i.imgur.com/pJC09VP.png)
###### Please note: raw project data is available in repo. Final Google Looker Studio report is located here: https://lookerstudio.google.com/reporting/e4edf5ca-48e8-4a23-b5b0-4a88353bbaf5

# Sun, Snow and Songs: A Seasonal Soundscape
## Analyzing corellaction between music listening patterns and weather/seasons





## There are multiple studies on how weather conditions influence our interest in certain genres and types of music ([1](https://www.ox.ac.uk/news/2023-05-03-here-comes-sun-new-study-shows-how-uk-weather-conditions-influence-music-success), [2](https://www.psychologytoday.com/us/blog/head-games/201711/when-seasons-change-so-do-musical-preferences-says-science)). Today, I will analyze my own music patterns from past year and how it correlates with weather.


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

It would be time consuming to manually assign tags to every artist, so I will be using `=WEBSERVICE` function in MS Excel to send API requests to Last.FM. And since I need to use artists names from row “B”, I will use CONCATINATE function in addition to WEBSERVICE. Here’s the final formula to get tags for artist in cell B2: `=WEBSERVICE(CONCATENATE("https://ws.audioscrobbler.com/2.0/?method=artist.gettoptags&artist=",B2,"&api_key=API_KEY"))`

I will use autofill to make API calls for every row on Sheets 1 and 2.

![enter image description here](https://i.imgur.com/OXsaKeO.png)
![enter image description here](https://i.imgur.com/U9F1RZo.png)


Now I have all the data I need to proceed with data cleaning.


## 2) Cleaning data


I will start cleaning data in MS Excel and move on to SQL later since there is a lot of data and Excel is struggling already.

While looking at the table, I noticed some entries are labeled as “Unknown Artist”. I don’t need that so I will use Filter on row B to filter and delete every such entry.

![enter image description here](https://i.imgur.com/HHTA86N.png)


I will also delete column D (“msPlayed”) since I don’t need that information for my analysis.

After using filter on column D, which now contains `WEBSERVICE` function from Section 1, there are 351 API errors on Sheet 1 and 117 errors on Sheet 2. All of them is due to the fact that the `WEBSERVICE` function can’t correctly parse the “&” sign in artist names (Joey Valence & Brae, piri & tommy) so I will have to manually make those API calls with `artist.getTopTags`.

Column D now contains XML data from API calls to Last.fm.

![enter image description here](https://i.imgur.com/twRv4lY.png)
 But I don’t need all of that, the only necessary information is the first (main) tag. To cut all the unwanted stuff I will use FILTERXML function in MS Excel. To do that I need to know the structure (XPath) of XML provided by Last.FM, which I can do by manually sending an API request. 
 
![enter image description here](https://i.imgur.com/xoVPja6.png)

So I need the “name” of the first “tag” and it’s located under “toptags” -> “lfm”. Final function for the artist in the second row is `=FILTERXML(D2, "/lfm/toptags/tag[1]/name")`. After using autofill, column E now contains one tag (genre) for each artist.

![enter image description here](https://i.imgur.com/DZ6DzTo.png)
Now it’s time to create a copy of the file without any functions or formulas. To do that I will copy both sheet and paste everything as values.

![enter image description here](https://i.imgur.com/ty8WTzI.png)

Finally, I will delete column D and rename it to “genre”. Now I have a good-looking table with all the info I need.

![enter image description here](https://i.imgur.com/jEr50Kx.png)

After capitalizing every cell in column D using `=PROPER()`, it’s time to move onto weather data. After checking the data using filters and conditional formatting, the only thing I have to do is delete unnecessary columns.

![enter image description here](https://i.imgur.com/MqlCpU4.png)

Time to use SQL. I’m using Google BigQuery for this analysis so the first thing to do is to create a new dataset.

![enter image description here](https://i.imgur.com/5PUrEVQ.png)

Now I will upload all the data to the dataset starting with weather.

![enter image description here](https://i.imgur.com/Q3guVwd.png)

For music data I’ll have to split one XLSX file into two CSV, because the latter doesn’t support sheets. After splitting and uploading them to BigQuery, I have a full dataset.

![enter image description here](https://i.imgur.com/avvpUDV.png)

 First, I must convert the datatype on “endTime” column from TIMESTAP to DATA using `CAST`. Then I will use `UNION ALL` to merge both tables into one and save it as “music”. This is the final query:
 
 ![enter image description here](https://i.imgur.com/9v20OjQ.png)
Now I have two tables- music and weather. I’ll use `INNER JOIN` and choose date, name of the artist, genre, temperature and precipitation.

![enter image description here](https://i.imgur.com/BWa08oD.png)

That’s the table I will use for data visualization, so I have to download it from BigQuery.


## Data visualization

After logging into Google Looker Studio and uploading the final version of dataset, I will first add one line chart, with AVG temp as a metric, genre as a dimension and endTime as a date range dimension.

![enter image description here](https://i.imgur.com/NeqZDlv.png)


On a second page I will add a table that contains artistName, genre, Record Count and AVG temp.

![enter image description here](https://i.imgur.com/XNdQpcv.png)

Finally, on a third page I will add 12 pie charts with endTime as a date range, genre as a dimension and Record Count as a metric. I will also sort it by Record Count descending.

![enter image description here](https://i.imgur.com/A20ZsvE.png)

Here's a link to the final version of the report on Google Looker: https://lookerstudio.google.com/reporting/e4edf5ca-48e8-4a23-b5b0-4a88353bbaf5


## Conclusions
Looking at the visualization, I can see that my music listening habits changed drastically due to season changes. During colder months I was listening to “darker” genres (blackened thrash metal, krautrock, ambient), while in the summer I switched to disco, garage punk, sludge and even tapped into gospel. This correlates with the study of Dr. Manuel Anglada-Tort (Faculty of Music, University of Oxford).
