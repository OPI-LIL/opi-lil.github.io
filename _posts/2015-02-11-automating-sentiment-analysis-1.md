---
layout: post
title: "Automating sentiment analysis"
description: "This is first post of the three part series about Automating Sentiment Analysis. In this post we will focus on gathering sentiment annotated data from Twitter and tools used for this purpose."
category: [Sentiment Analysis]
tags: [Twitter, Tweepy, Python, Sentiment Analysis]
---

This is first post of the three part series about Automating Sentiment Analysis. In this post we will focus on gathering sentiment annotated data from Twitter and tools used for this purpose.

<!--more-->

Gathering sentiment annotated data can be done in two ways - maunal sentiment annotation by volunteers or using simple, universal automatic annotation. As first option is very time consuming and requires actual human input, second option is more useful for automated systems. Automated sentiment annotation is often based on detecting emoticons, which are universal to the language and are often used in right emtional context. One of the sources for such annotated data is Twitter, which provides two filters: ":)" for searching for messages with positive sentiment, and ":(" for searching for messages with negative sentiment. In addition to that, Twitter allows message filtering by language (using "lang:" filter), which is also useful for filtering data only in Polish language.

To gather Twitter sentiment annotated data, we created simple tool, using Python with Tweepy library for accessing Twitter API. Process is described below, and tool source code is available on [BitBucket](https://bitbucket.org/antoni_sobkowicz/opi-lil-web-scrapper).

###Twitter scrapping with Python and Tweepy
####Preparations
Before you can connect to Twitter API, you need to get API key. To get Twitter API key you need

- Twitter account

After loggin in on [http://apps.twitter.com](http://apps.twitter.com "Twitter Apps page"), click on Create New App. Fill out all required fields (marked with *, you can put placeholder instead URL), and create app. 

![App creation form]({{ site.url }}/assets/images/create_app.PNG)

After navigating to app overview, select Keys and Access Tokens tab, and take note of Consumer Key and Consumer Secret - those two will be needed later.

![App keys tab]({{ site.url }}/assets/images/keys_app.PNG)

> **Note:**
> 
> One set API keys will allow app to retrieve around 1% of tweets. If you want to gather more tweets, you will need multiple accounts and multiple API keys.

####Twitter scrapper code
Twitter scrapper code is divided into two classes - `scrapper` which is main program class, and `SListener` which is Tweepy stream listener implementation. Below we describe important parts of code in detail. You can find full code on [BitBucket](https://bitbucket.org/antoni_sobkowicz/opi-lil-web-scrapper).

```python
consumer_key        = "                   "
consumer_secret     = "                   "
access_token        = "                   "
access_token_secret = "                   "

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
```

This part of the `scrapper` code is needed to authorize our program to Twitter. All four variables, `consumer_key`, `consumer_secret`, `access_token`, `access_token_secret` should be filled with data you got in previous step, from Twitter Apps page. Next two lines authenticate and authorize program with Twitter service.

```python
listen = SListener(tweepy.api, fileprefix)
stream = tweepy.Stream(auth, listen)

...

try:
    stream.filter(track = track, languages = language)
except:
    print "Streaming error"
    stream.disconnect()
```

This part of code creates and launches actual Twitter message retrieval. Twitter messages are retrieved from Twitter Stream, which feeds  our program with data. First two lines create stream listener (using our `SListener` class, described below) and stream object istelf. Try-Except block starts stream retrieval using provided track keywords and languages.

```python
def on_data(self, data):
    if 'in_reply_to_status_id' in data:
        self.on_status(data)

def on_status(self, status):
    self.output.write(status + "\n")
    self.counter += 1
    if self.counter >= 20000:
        self.output.close()
        self.output = open('../data/' + self.fprefix + '.'
                           + time.strftime('%Y%m%d-%H%M%S') + '.json', 'w')
        self.counter = 0
    return
```
This two functions are responsible for saving Stream data to file. `on_data` is launched when now data is available from Stream, and itself launches `on_status` when data retrieved from stream containg `in_reply_to_status_id` tag (as this is designation that data is an actual Tweet). `on_status` Saves data to file, and if saved data counter reaches 20000, closes and opens new file.

####Running scrapper
Scrapper tool can be run from commandline and supports several command line switches, for setting both tokens and languages, changing prefix for data files, and enabling debug support. Below is full list of command line switches supported and their functions

	scrapper.py -d -h -l <language> -f <prefix> track
	-d
	        Enable Tweepy debug
	-h, --help
	        Display this message
	-l <language>, --lang=<language>
	        Add language to Tweepy filter
	-f <prefix>, --file=<prefix>
	        Prefix for data files
	track
	        Keywords to track

Example usages

	python scrapper.py -f positive -l pl -l en :)

runs scrappter with data file prefix `positive` for languages `pl` and `en` with filter `:)` (meaning positive Tweets only.

	python scrapper.py -d -f all :( :)

runs scrappter with data file prefix `all` and debug mode on, with filter `:)` and `:(`.

####Scrapped data format
Data scrapped from Twitter Stream is saved directly as it comes from stream - in JSON form. Below is an example of data retrieved from Twitter Stream. Altough in our task we needed only part of object (`text` element only), we decided to save everything, for possible future use.
>*Note
>
>Each JSON object is a line in output file. They were converted to form shown below for easier display on the blog only.

```json
{
    "created_at": "Fri Mar 06 10:42:50 +0000 2015",
    "id": 573795867251073024,
    "id_str": "573795867251073024",
    "text": "Specjalnie dla @KKunowska :) http:\/\/t.co\/KbHYEwYI4o",
    "source": "\u003ca href=\"http:\/\/tapbots.com\/tweetbot\" rel=\"nofollow\"\u003eTweetbot for i\u039fS\u003c\/a\u003e",
    "truncated": false,
    "in_reply_to_status_id": null,
    "in_reply_to_status_id_str": null,
    "in_reply_to_user_id": null,
    "in_reply_to_user_id_str": null,
    "in_reply_to_screen_name": null,
    "user": {
        "id": 66178249,
        "id_str": "66178249",
        "name": "Jazzowo",
        "screen_name": "Jazzowo",
        "location": "Everywhere",
        "url": "http:\/\/jazzowo.tumblr.com",
        "description": "FILM, SOUND, SILENCE, SPACE \n\nIf you don't have ability you wind up playing in a rock band",
        "protected": false,
        "verified": false,
        "followers_count": 114,
        "friends_count": 198,
        "listed_count": 4,
        "favourites_count": 638,
        "statuses_count": 1139,
        "created_at": "Sun Aug 16 19:45:33 +0000 2009",
        "utc_offset": 3600,
        "time_zone": "Warsaw",
        "geo_enabled": false,
        "lang": "pl",
        "contributors_enabled": false,
        "is_translator": false,
        "profile_background_color": "C0DEED",
        "profile_background_image_url": "http:\/\/abs.twimg.com\/images\/themes\/theme1\/bg.png",
        "profile_background_image_url_https": "https:\/\/abs.twimg.com\/images\/themes\/theme1\/bg.png",
        "profile_background_tile": false,
        "profile_link_color": "0084B4",
        "profile_sidebar_border_color": "C0DEED",
        "profile_sidebar_fill_color": "DDEEF6",
        "profile_text_color": "333333",
        "profile_use_background_image": true,
        "profile_image_url": "http:\/\/pbs.twimg.com\/profile_images\/560876803254784000\/-4ZsTTIx_normal.jpeg",
        "profile_image_url_https": "https:\/\/pbs.twimg.com\/profile_images\/560876803254784000\/-4ZsTTIx_normal.jpeg",
        "profile_banner_url": "https:\/\/pbs.twimg.com\/profile_banners\/66178249\/1422538835",
        "default_profile": true,
        "default_profile_image": false,
        "following": null,
        "follow_request_sent": null,
        "notifications": null
    },
    "geo": null,
    "coordinates": null,
    "place": null,
    "contributors": null,
    "retweet_count": 0,
    "favorite_count": 0,
    "entities": {
        "hashtags": [],
        "trends": [],
        "urls": [],
        "user_mentions": [{
            "screen_name": "KKunowska",
            "name": "Katarzyna Kunowska",
            "id": 794637380,
            "id_str": "794637380",
            "indices": [15, 25]
        }],
        "symbols": [],
        "media": [{
            "id": 573795861882339328,
            "id_str": "573795861882339328",
            "indices": [29, 51],
            "media_url": "http:\/\/pbs.twimg.com\/media\/B_aIS9kWAAAlRB4.jpg",
            "media_url_https": "https:\/\/pbs.twimg.com\/media\/B_aIS9kWAAAlRB4.jpg",
            "url": "http:\/\/t.co\/KbHYEwYI4o",
            "display_url": "pic.twitter.com\/KbHYEwYI4o",
            "expanded_url": "http:\/\/twitter.com\/Jazzowo\/status\/573795867251073024\/photo\/1",
            "type": "photo",
            "sizes": {
                "small": {
                    "w": 340,
                    "h": 481,
                    "resize": "fit"
                },
                "medium": {
                    "w": 580,
                    "h": 821,
                    "resize": "fit"
                },
                "thumb": {
                    "w": 150,
                    "h": 150,
                    "resize": "crop"
                },
                "large": {
                    "w": 580,
                    "h": 821,
                    "resize": "fit"
                }
            }
        }]
    },
    "extended_entities": {
        "media": [{
            "id": 573795861882339328,
            "id_str": "573795861882339328",
            "indices": [29, 51],
            "media_url": "http:\/\/pbs.twimg.com\/media\/B_aIS9kWAAAlRB4.jpg",
            "media_url_https": "https:\/\/pbs.twimg.com\/media\/B_aIS9kWAAAlRB4.jpg",
            "url": "http:\/\/t.co\/KbHYEwYI4o",
            "display_url": "pic.twitter.com\/KbHYEwYI4o",
            "expanded_url": "http:\/\/twitter.com\/Jazzowo\/status\/573795867251073024\/photo\/1",
            "type": "photo",
            "sizes": {
                "small": {
                    "w": 340,
                    "h": 481,
                    "resize": "fit"
                },
                "medium": {
                    "w": 580,
                    "h": 821,
                    "resize": "fit"
                },
                "thumb": {
                    "w": 150,
                    "h": 150,
                    "resize": "crop"
                },
                "large": {
                    "w": 580,
                    "h": 821,
                    "resize": "fit"
                }
            }
        }]
    },
    "favorited": false,
    "retweeted": false,
    "possibly_sensitive": false,
    "filter_level": "low",
    "lang": "pl",
    "timestamp_ms": "1425638570021"
} {
    "created_at": "Fri Mar 06 10:42:49 +0000 2015",
    "id": 573795866261200896,
    "id_str": "573795866261200896",
    "text": "Na jeje I dey :) http:\/\/t.co\/clxTxGpjeO",
    "source": "\u003ca href=\"http:\/\/twitter.com\" rel=\"nofollow\"\u003eTwitter Web Client\u003c\/a\u003e",
    "truncated": false,
    "in_reply_to_status_id": null,
    "in_reply_to_status_id_str": null,
    "in_reply_to_user_id": null,
    "in_reply_to_user_id_str": null,
    "in_reply_to_screen_name": null,
    "user": {
        "id": 226308440,
        "id_str": "226308440",
        "name": "\u2606KIMANI CONE\u2606",
        "screen_name": "Herkim14",
        "location": "JupiTer || Gidi \u25ba AFRICA",
        "url": null,
        "description": "\u2606Deeper Than your Thinking\u2606::\u2665mum\u2665me::: #AFC:::\u266cWeLuvMusic\u266c:::FELA~disciple:::#MMG:::#Ibile:::#NfNf",
        "protected": false,
        "verified": false,
        "followers_count": 1039,
        "friends_count": 775,
        "listed_count": 4,
        "favourites_count": 137,
        "statuses_count": 14461,
        "created_at": "Mon Dec 13 21:19:06 +0000 2010",
        "utc_offset": -28800,
        "time_zone": "Pacific Time (US & Canada)",
        "geo_enabled": false,
        "lang": "en",
        "contributors_enabled": false,
        "is_translator": false,
        "profile_background_color": "C0DEED",
        "profile_background_image_url": "http:\/\/abs.twimg.com\/images\/themes\/theme1\/bg.png",
        "profile_background_image_url_https": "https:\/\/abs.twimg.com\/images\/themes\/theme1\/bg.png",
        "profile_background_tile": false,
        "profile_link_color": "0084B4",
        "profile_sidebar_border_color": "C0DEED",
        "profile_sidebar_fill_color": "DDEEF6",
        "profile_text_color": "333333",
        "profile_use_background_image": true,
        "profile_image_url": "http:\/\/pbs.twimg.com\/profile_images\/533296065706221568\/_XuiRO3u_normal.jpeg",
        "profile_image_url_https": "https:\/\/pbs.twimg.com\/profile_images\/533296065706221568\/_XuiRO3u_normal.jpeg",
        "profile_banner_url": "https:\/\/pbs.twimg.com\/profile_banners\/226308440\/1422716894",
        "default_profile": true,
        "default_profile_image": false,
        "following": null,
        "follow_request_sent": null,
        "notifications": null
    },
    "geo": null,
    "coordinates": null,
    "place": null,
    "contributors": null,
    "retweet_count": 0,
    "favorite_count": 0,
    "entities": {
        "hashtags": [],
        "trends": [],
        "urls": [],
        "user_mentions": [],
        "symbols": [],
        "media": [{
            "id": 573795864444960770,
            "id_str": "573795864444960770",
            "indices": [17, 39],
            "media_url": "http:\/\/pbs.twimg.com\/media\/B_aITHHUgAIY2QG.jpg",
            "media_url_https": "https:\/\/pbs.twimg.com\/media\/B_aITHHUgAIY2QG.jpg",
            "url": "http:\/\/t.co\/clxTxGpjeO",
            "display_url": "pic.twitter.com\/clxTxGpjeO",
            "expanded_url": "http:\/\/twitter.com\/Herkim14\/status\/573795866261200896\/photo\/1",
            "type": "photo",
            "sizes": {
                "thumb": {
                    "w": 150,
                    "h": 150,
                    "resize": "crop"
                },
                "small": {
                    "w": 340,
                    "h": 605,
                    "resize": "fit"
                },
                "large": {
                    "w": 1024,
                    "h": 1822,
                    "resize": "fit"
                },
                "medium": {
                    "w": 600,
                    "h": 1068,
                    "resize": "fit"
                }
            }
        }]
    },
    "extended_entities": {
        "media": [{
            "id": 573795864444960770,
            "id_str": "573795864444960770",
            "indices": [17, 39],
            "media_url": "http:\/\/pbs.twimg.com\/media\/B_aITHHUgAIY2QG.jpg",
            "media_url_https": "https:\/\/pbs.twimg.com\/media\/B_aITHHUgAIY2QG.jpg",
            "url": "http:\/\/t.co\/clxTxGpjeO",
            "display_url": "pic.twitter.com\/clxTxGpjeO",
            "expanded_url": "http:\/\/twitter.com\/Herkim14\/status\/573795866261200896\/photo\/1",
            "type": "photo",
            "sizes": {
                "thumb": {
                    "w": 150,
                    "h": 150,
                    "resize": "crop"
                },
                "small": {
                    "w": 340,
                    "h": 605,
                    "resize": "fit"
                },
                "large": {
                    "w": 1024,
                    "h": 1822,
                    "resize": "fit"
                },
                "medium": {
                    "w": 600,
                    "h": 1068,
                    "resize": "fit"
                }
            }
        }]
    },
    "favorited": false,
    "retweeted": false,
    "possibly_sensitive": false,
    "filter_level": "low",
    "lang": "pl",
    "timestamp_ms": "1425638569785"
}
```
As you may notice, these elemens are in proper JSON format - even tough each of them is if inspected on their own. To convert data to proper json format, you need to encapsulate all elements in JSON array. Easies way to do it is to add `[` and `]` at the beginning and end of file, respectively, and `,` at the end of each  line except last one. We did it both manually, using text editor search/replace functionality and via PowerShell script (to be included later).