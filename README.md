# HowRussian
A Classifier based on Twitter data to determine how likely a bit of text is to be a Russian Troll tweet, or to measure the influence of Russian Trolls on our own posts and text.  For the sake of convienience, 'Russian' in the following context refers to those tweets that were found to be from a known twitter handle of the Internet Research Agency of Russia, and have nothing to do with the Russian people in general.

## Data
###### **Non-Russian**
 - [Sentiment 140](http://help.sentiment140.com/for-students/)  1,600,498 Tweets (Circa 2010)
 - [Cheng-Caverlee-Lee September 2009 - January 2010 Twitter Scrape](https://archive.org/details/twitter_cikm_2010)
###### **Russian**
 - [3 million Russian troll tweets](https://github.com/fivethirtyeight/russian-troll-tweets)
 
 Non-russian tweets were chosen from 2009-2010 in order to avoid 'tainted' tweets in the dataset; the hypothesis here is that Russian Trolls (read: Internet Research Agency) have had a negative effect on our social media experience and are partly responsible for the great divides we are seeing in our society today. To determine if there is Russan Troll *influence* in a tweet, I needed to use what we sounded like *before* the social media campaigns started, hence the timeline.
 
 Using the power of Left Joins, I discovered 25,837 Russian Tweets in the non-Russian sets above.  I removed these before joining.

[[1] Data Gathering](https://github.com/NeverForged/HowRussian/blob/master/%5B1%5D%20Data%20Gathering.ipynb "[1] Data Gathering")

## Methods
### Preprocessing
The ultimate goal of this is to measure 'Russian' influence over modern social media language useage, and to do that I needed to remove things that were time-dependent in a social context, and to allow future ideas to be analyzed based on the language used, not the subject matter.  In other words, I had to replace nouns and other parts of speach.

To do this, I first tried to removed specific '@' referneces and replaced them with the phrase "\_at\_someone\_", and did the same for links ("\_link\_") and #Hashtag ("\_hashtag\_").  Then removed all punctuation aside from the underscores.  From there, I used [part of speach tagging from nltk](https://www.nltk.org/book/ch05.html "nltk pos tagging") to sort through the [parts of speach](https://medium.com/@muddaprince456/categorizing-and-pos-tagging-with-nltk-python-28f2bc9312c3 "tag list here") and keep certain words and reject others based on the part of speach.  I kept prepositions (IN), verbs of all sorts (contains V), determiners (DT), coordinating conjunctions (CC), and all of my tagged twitter functions above.  The rest I set to the part of speach tag from nltk.  This helped to remove the nouns, gendered pronouns (example, anti-Obama and anti-Hillary Clinto speach would be treated differently with him vs her, but now would be the same), and even adverbs and the like, while keeping verbs, words such as 'all' or 'some', etc.  This took over 3 hours to tag the tweets.  You can see commented code [here](https://github.com/NeverForged/HowRussian/blob/master/%5B2%5D%20Preprocessor.ipynb "[2] Preprocessor").

That ran into quite a few issues, namely that words like 'donald' and 'trump' were in the top 100 by logistic coeficient (see below) despite the removal of all nouns, and that phrashing would appear non-Russian if there were no hashtags, but Russian if you added a Hashtag.  The later makes sense, the Russian tweets tend to have hashtags, ats, and links in order to be seen, as there is little point in doing a targeted social media campaign if no one sees it.  

So I then used the following [preprocessor](https://github.com/NeverForged/HowRussian/blob/master/%5B2%5D%20Preprocessor.ipynb "[2] Preprocessor"), which simply removed hashtags, limks, and @ mentions completley and pos-tagged the entire tweet.  This didn't work as well in the final steps, but still worked enough (see below), and more importantly only looks at the way that the tweets are constructed gramatically, which was the initial point.

### Term-Frequency Count Vector
I crated a [tfidf vectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) based on ngrams in the rang of 2-5, with a minimum of 500 document occurences and a maximum of 70% of documents (as [recommended](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html "vectorizer used") to eliminate stop-words).  From there the results were [scaled](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html "scaler used") in order to use l1-normalization on the vector.  See [here](https://github.com/NeverForged/HowRussian/blob/master/%5B3%5D%20Count%20Vectorizer%2C%20Scaler%2C%20and%20Logistic%20Regression.ipynb "[3] Count Vectorizer, Scaler, and Logistic Regression") for details.

### Logistic Regression with Lasso Normalization
Using a [hold-out set](https://github.com/NeverForged/HowRussian/blob/master/%5B3%5D%20Count%20Vectorizer%2C%20Scaler%2C%20and%20Logistic%20Regression.ipynb "[3] Count Vectorizer, Scaler, and Logistic Regression") , a [training set, and a test set](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html "sklearn train-test split"), I ran Logistic Regression using lasso normalization in order to just eliminate the phrases that didn't contribute to determining if a tweet was Russian or not.  With the preprocessor](https://github.com/NeverForged/HowRussian/blob/master/%5B2%5D%20Preprocessor.ipynb "see commented code here") raining Data got a score of 0.896, with test at 0.894, and the hold-out set was at 0.91.  This got better as I set the threshold for Russian higher, ending at a score of around 0.94 at a threshold of 0.9.  For the actual preprocessor, I found that the training set yieled a score of 0.775, the test set a score of 0.771, and the full set a scoe of 0.793.  Not the worst.

[image 1]

[image 2]

Setting a threshold of 0.9 gets a score 0f 0.915, which is a respectable score for my purposes.  Considering it is entirley based on nltk pos-tagging, which as I have seen sometimes makes mistakes as to what is or is not a proper noun which for Trump makes sense to an extent as it an be a verb, but last I checked Donald was not a verb, unless [Dan Savage was at it again](https://www.motherjones.com/politics/2010/09/rick-santorum-google-problem-dan-savage/ "NSFW"); this is likely as good as it will get.