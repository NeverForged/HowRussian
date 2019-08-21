# HowRussian
A Classifier based on Twitter data to determine how likely a bit of text is to be a Russian Troll tweet, or to measure the influence of Russian Trolls on our own posts and text.

## Data
-  **Non-Russian**
 - [Sentiment 140](http://help.sentiment140.com/for-students/)  1,600,498 Tweets (Circa 2010)
 - [Cheng-Caverlee-Lee September 2009 - January 2010 Twitter Scrape](https://archive.org/details/twitter_cikm_2010)
- **Russian**
 - [3 million Russian troll tweets](https://github.com/fivethirtyeight/russian-troll-tweets)
 
 Non-russian tweets were chosen from 2009-2010 in order to avoid 'tainted' tweets in the dataset; the hypothesis here is that Russian Trolls (read: Internet Research Agency) have had a negative effect on our social media experience and are partly responsible for the great divides we are seeing in our society today. To determine if there is Russan Troll *influence* in a tweet, I needed to use what we sounded like *before* the social media campaigns started, hence the timeline.
 
 Using the power of Left Joins, I discovered 25,837 Russian Tweets in the non-Russian sets above.  I removed these before joining.