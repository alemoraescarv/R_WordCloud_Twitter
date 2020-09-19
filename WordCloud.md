# Word Cloud from Twitter Hashtag

First, you need to authenticate your call to the Twitter API. Since, version 1.11 was released, every time you make a call to your Twitter app, you will need to authenticate it.
In order to do it, you can follow this tutorial from [thinktostart.com](http://thinktostart.com/twitter-authentification-with-r/).

After app creating for authentication is done. You can follow to the second part, the code. 

### 1. Installing the necessary packages and importing the packages
The following packages are necessary, due to the librarys which will be used.

```r
install.packages(c("devtools", "rjson", "bit64", "httr"))
install.packages("rtweet")
install.packages("tidytext")
install.packages("dplyr")
install.packages("stringr")

library(tidytext)
library(dplyr)
library(stringr)
library(rtweet)
library(tm)
library(wordcloud)
library(RColorBrewer)

```

### 2. Retrieving tweets based on a hashtag.
First, the function [searchTwitter](https://www.rdocumentation.org/packages/twitteR/versions/1.1.9/topics/searchTwitter) was used to get the data. Notice that are various other function's arguments which were not used. 
Then, the function [getText()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/gettext) is applied in order to retrive the text of each tweet.

```r
tweets = searchTwitter("#quarantine ",since='2020-02-01', n=500, lang="en")

text = sapply(mach_tweets, function(x) x$getText())

```

### 3. Cleaning the data

This step is very imortant. Always when dealing with data, exploring it and cleaning it is necessary. So, your remaining data will be useful. Below, the tweets are transformed into a corpus which is basically text with metatadata about it. 
Then, the data is cleaned in two ways before tranforming the corpus into a vector os terms with frequencies or when performing the transformation. 

##### Creating the corpus
```r
# create a corpus (necessary piece of the code)
mach_corpus = Corpus(VectorSource(text))
```
##### Clenaing the data, first option.
```r
#Cleaning the data 
#stemDocument matches words with the same meaning and leaving just one 
#removeWords removes stopwords in English (1st option)
#removePunctuation removes punctuation
corpus = tm_map(corpus, stemDocument)
corpus = tm_map(corpus, removeWords, stopwords('english'))
corpus = tm_map(corpus, removePunctuation)

```
 ### 4. Creating a matrix of terms in each tweet with its frequency and cleaning the data 
 
 ```r
 # create document term matrix applying some transformations
tdm = TermDocumentMatrix(mach_corpus,
   control = list(removePunctuation = TRUE,
   stopwords = c("quarantine","darlinnnn","got","–","--", stopwords('english')),
   removeNumbers = TRUE, tolower = TRUE))
   
# define tdm as matrix
m = as.matrix(tdm)
# get word counts in decreasing order
word_freqs = sort(rowSums(m), decreasing=TRUE) 

 ```
 
 *In this case, you can apply both cleaning transformations or just one of the codes. Notice that creating the corpus is necessary in both options.*
 
 ### 5. Create a table with each word and its frequency
 
 It is very common to transform your data into a table in order to analyse its consistency and accuracy. In this case, we are analysing how the data looks and if we need to remove some stop words, which the function missed or not.
 Check the frequency of each word, in decreasing order.
 ```r
# create a data frame with words and their frequencies
dm = data.frame(word=names(word_freqs), freq=word_freqs)

#check the data in a matrix in decreasing order of frequency
DT::datatable(dm)
 ```
 
 Below it is the output, I have got.
 
 ![Table of each word and its frequency](https://github.com/alemoraescarv/R_WordCloud_Twitter/blob/master/table_wordCloud.png)
 
 ### 6. Create the Word Cloud
 
 The word cloud can be used the wordcloud or wordcloud2 packages. The color schema and other arguments are described in the [documentation](https://www.rdocumentation.org/packages/wordcloud/versions/2.6/topics/wordcloud). 
 Notice that I have configured a minimum of frequency required in order to appear in the graph.
 ```r
 # plot wordcloud
wordcloud(dm$word, dm$freq,max.words=50,min.freq = 7, random.order=FALSE, colors=brewer.pal(7, "OrRd"))
 ```
Below is the final result, 

![Word cloud from Tweets with the hashtag #quarantine](https://github.com/alemoraescarv/R_WordCloud_Twitter/blob/master/wordcloud.png)
 

