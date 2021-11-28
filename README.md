# News Data

The purpose of this project is to gather video data including video title, description, likes, dislikes, views, duration, and number of comments from news videos from the YouTube channels of major US news sources including Fox, CNN, NBC, PBS, and BlazeTV. If you would like to access the data directly you can find it on [Kaggle](https://www.kaggle.com/mynameiscorn/news-headlines-youtube-metadata).

# Data Collection

## YouTube API
The first and most important step is collecting data. YouTube provides an [API](https://developers.google.com/youtube/v3) that allows users 10,000 queries per day. Each query can contain as many as 50 requests meaning that all data mentioned here (~90,000 records) can be gathered in a single day. You'll need an API key to request data. Once you have your API key we can move to the next step.

## Basic Video Data
For every YouTube channel there is a master playlist called Uploads. This contains all videos that a channel has uploaded. If a channel uploads frequently, the uploads playlist will only contain the last 20,000 videos. To access this playlist we need to first get the channel id. Using the [Scrape_YouTube.ipynb](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Scrape_YouTube.ipynb) file we can determine the channel id by searching for the channel name and then looking through the response.
```
request = youtube.search().list(q='BlazeTV',part='snippet',type='channel',maxResults=5)
response= request.execute()
print(response)
```
The above code will produce a response that can be parsed or simply looked at to find the channel id.
```
{'kind': 'youtube#searchListResponse',
 'etag': 'U1_OAszPV3Iq-HVbGg7aCZEcwoA',
 'nextPageToken': 'CAUQAA',
 'regionCode': 'US',
 'pageInfo': {'totalResults': 822, 'resultsPerPage': 5},
 'items': [{'kind': 'youtube#searchResult',
   'etag': 'JVYurUnp4gYbAab1UoxMGoaGDjs',
   'id': {'kind': 'youtube#channel', 'channelId': 'UCKgJEs_v0JB-6jWb8lIy9Xw'},
   'snippet': {'publishedAt': '2013-09-20T15:17:19Z',
    'channelId': 'UCKgJEs_v0JB-6jWb8lIy9Xw',
    'title': 'BlazeTV',
    'description': "Blaze Media is one of the nation's largest independent media companies, with an aggregate reach of over 165 million each month, with digital products and ...",
    'thumbnails': {'default': {'url': 'https://yt3.ggpht.com/ytc/AKedOLSP69wxA3hSW0rdo1I9K8Fn68q-qjt8Fn0o3iDFEQ=s88-c-k-c0xffffffff-no-rj-mo'},
     'medium': {'url': 'https://yt3.ggpht.com/ytc/AKedOLSP69wxA3hSW0rdo1I9K8Fn68q-qjt8Fn0o3iDFEQ=s240-c-k-c0xffffffff-no-rj-mo'},
     'high': {'url': 'https://yt3.ggpht.com/ytc/AKedOLSP69wxA3hSW0rdo1I9K8Fn68q-qjt8Fn0o3iDFEQ=s800-c-k-c0xffffffff-no-rj-mo'}},
    'channelTitle': 'BlazeTV',
    'liveBroadcastContent': 'none',
    'publishTime': '2013-09-20T15:17:19Z'}}
```
The Uploads id is the same as the channel id except the second character is changed from a C to a U. So, in the above example, the channel id is UCKgJEs_v0JB-6jWb8lIy9Xw and the Uploads id is UUKgJEs_v0JB-6jWb8lIy9Xw.

Using the Uploads id we can use the YouTube API and query the videos stored in the playlist. We can get 50 results at a time and includes data like the title, description, video id, and thumbnail info.
```
r = youtube.playlistItems().list(playlistId=playlist_id,
                                 part='snippet, contentDetails',
                                 maxResults=results_per_page,
                                 pageToken=token)

y_playlist_data = r.execute()
```
### Data Merging
After each of your files has been created we can use the [Combine_Data.ipynb](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Combine_Data.ipynb) file to combine the individual files into a single file.

## Metadata Gathering
With the combined file created, we can move on to the [Get_Stats_Data.ipynb](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Get_Stats_Data.ipynb) file which will help us get useful metadata such as the number of views, likes, dislikes, comments, and length of the video.
```
r = youtube.videos().list(part='contentDetails, statistics',id=sample)
stats = r.execute()
```
Once this data is collected we can merge it with the original video data we collected earlier and create a final Amalgamated file which can be used in analysis.

# Analysis
Now that the data is collected we can look at trends over time and by demographic. For example, do news orgs primarily upload short or long videos?

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/viewsbyduration.png)

It looks like the majority are short videos. We can look at how view counts change over time for a specific topic. For example, how do views change over time for the topic of Donald Trump?

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/votdtFox.png)

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/votdtCNN.png)

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/votdtBlaze.png)

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/votdtNBC.png)

![](https://github.com/Vinnie-Singleton/News_Metadata/blob/main/Pics/votdtPBS.png)

It looks like viewers of all news outlets tuned in to Donald Trump while he was in office but the non conservative audiences tuned out after he had left office.

