---
layout: post
title: "RHOA Cast Tweets"
date: 2017-05-10 00:00:00
categories: r, data, twitter, twitter data, rhoa
---

# Overview

The cast of the Real Housewives of Atlanta all have Twitter accounts and tweet with varying frequency; and express differing degrees of sentiments, as categorized by the [NRC Emotion Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm), and represented in the [TidyText package](https://cran.r-project.org/web/packages/tidytext/vignettes/tidytext.html). 

I use the TwitteR R package, the Twitter API, and R to look at the frequency of their tweets, and the sentiments they expressed.

These tweets are the 3,200 most recent tweets by each cast member, as of 5/9/2017 PM.

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/tweet_count.png)

The following acronyms represent each person in the charts:

*  kb: Kandi
*  pp: Phaedra
*  pw: Porsha
*  sw: Sheree
*  cb: Cynthia
*  km: Kenya

Kandi tweeted the most, and Sheree tweeted by far the least. The sentiment analysis I did is from the TidyText package, and so within tweets, only a certain percentage of those words are categorized as belonging ot one of the ten sentiments included (well, specificall 2 sentiments (positive, negative) and 8 emotions (the rest)).

On average, 30% of tweeted words among the 6 cast members were used in the sentiment analysis.

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/words_included.png)

I further grouped the ten sentiments into either "positive" or "negative," with five in each. These groupings are not necessarily wholly accurate, because I'm not a language expert, but I did my best. After these groupsings:

*  The cast members' words were, on average, 72% positive.
*  The cast members' words were, on average, 28% negative.

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/sent_group.png)

# Sentiments

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/members.png)

## Most of each category
* Sentiments
    *  Negative: Porsha had the highest percentage of the "negative" sentiment: 12.2%
    *  Positive: Phaedra had the highest percentage of the "positive" sentiment: 30.7%
    
*  Emotions
    *  Anger: Kenya had the highest percentage of "anger": 4.9%
    *  Anticipation: Phaedra had the highest percentage of "anticipation": 16.4%
    *  Disgust: Cynthia had the highest percentage of "disgust": 4.0%
    *  Fear: Kenya had the highest percentage of "fear": 4.0%
    *  Joy: Phaedra had the highest percentage of "joy": 19.0%
    *  Sadness: Kenya had the highest percentage of "sadness": 7%
    *  Surprise: Cynthia had the highest percentage of "surprise": 6.7%
    *  Trust: Phaedra had the highest percentage of "trust": 15.4%

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/members_facet.png)

(Sheree tweeted so little that I excluded her results)

## Groupings

(Disclaimer: Sheree tweeted so little that I'm excluding her results here:)
I grouped the ten sentiments into either positive or negative.

* Negatives: negative, anger, sadness, fear, disgust
* Positives: positive, trust, anticipation, joy, surprise

The cast members who were the most positive in these groupings were:
* Phaedra, 86% positive words
* Cynthia, 79.2% positive words

The cast members who were the most negative in these groupings were:
* Kenya, 32.1% negative words
* Porsha, 27.3% negative words

(Sheree tweeted so little that I excluded her results)

# Number of Tweets

![sentiment_groups](https://raw.githubusercontent.com/nadinesk/rhoa_cast_tweets/master/charts/stat_count.PNG)

Kandi had the most tweets, with 2,531 tweets.

Sheree had the least tweets, with 35 tweets.

# Number of words

Kandi had the most words, with 19,536 words. 29.8%, or 5,829 of her words were included in the sentiment analysis.

Sheree had the least words, with 193 words. 10%, or 21, of her words were included in the sentiment analysis.

# The Code

The code is also located in this github repository: [rhoa_cast_tweets](https://github.com/nadinesk/rhoa_cast_tweets)

    library(dplyr)
    library(purrr)
    library(twitteR)
    library(tidyr)
    library(lubridate)
    library(scales)
    library(ggplot2)
    library(stringr)
    library(tidytext)
    library(reshape2)
    library(scales)




    kb <- userTimeline("kandi", n= 3200)

    kb1 <- do.call("rbind", lapply(kb, as.data.frame))

    kb2 <- kb1[c(1)]


    nrc <- sentiments %>%
      filter(lexicon == "nrc") %>%
      dplyr::select(word, sentiment)


    kb3 <- kb2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    kb4 <- kb3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    kb4$perc <- kb4$words/(sum(kb4$words))

    sum(kb4$perc)

    kb4$who <- "kb"

    kb_count <- count(kb1)

    kb4

    ###


    pp <- userTimeline("PhaedraParks", n= 3200)

    pp1 <- do.call("rbind", lapply(pp, as.data.frame))

    pp2 <- pp1[c(1)]


    pp3 <- pp2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    pp4 <- pp3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    pp4$perc <- pp4$words/(sum(pp4$words))

    sum(pp4$perc)

    pp4


    #####################
    pw <- userTimeline("Porsha4real", n= 3200)

    pw1 <- do.call("rbind", lapply(pw, as.data.frame))

    pw2 <- pw1[c(1)]


    pw3 <- pw2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    pw4 <- pw3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    pw4$perc <- pw4$words/(sum(pw4$words))
    sum(pw4$perc)
    pw4$who <- "pw"

    ##############

    sw <- userTimeline("IamSheree", n= 3200)

    sw1 <- do.call("rbind", lapply(sw, as.data.frame))

    sw2 <- sw1[c(1)]


    sw3 <- sw2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    sw4 <- sw3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    sw4$perc <- sw4$words/(sum(sw4$words))

    sum(sw4$perc)

    sw4$who <- "sw"

    ###################

    cb <- userTimeline("CynthiaBailey10", n= 3200)

    cb1 <- do.call("rbind", lapply(cb, as.data.frame))

    cb2 <- cb1[c(1)]


    cb3 <- cb2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    cb4 <- cb3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    cb4$perc <- cb4$words/(sum(cb4$words))
    sum(cb4$perc)
    cb4$who <- "cb"


    ###############


    km <- userTimeline("KenyaMoore", n= 3200)

    km1 <- do.call("rbind", lapply(km, as.data.frame))

    km2 <- km1[c(1)]


    km3 <- km2 %>%
      filter(!str_detect(text, '^"')) %>%
      unnest_tokens(word, text, token = "regex") %>%
      filter(!word %in% stop_words$word,
             str_detect(word, "[a-z]"))


    km4 <- km3 %>%
      inner_join(nrc, by = "word") %>%
      count(sentiment) %>%
      ungroup() %>%
      complete(sentiment,  fill = list(n = 0)) %>%
      group_by(sentiment) %>%
      summarize(words = sum(n)) %>%
      ungroup()

    km4$perc <- km4$words/(sum(km4$words))
    sum(km4$perc)

    km4$who <- "km"

    jn <- kb4 %>%
            left_join(pp4, by="sentiment") %>%
            left_join(pw4, by="sentiment") %>%
            left_join(sw4, by="sentiment") %>%
            left_join(cb4, by="sentiment") %>%
            left_join(km4, by="sentiment")

    jn

    jn1 <- jn[c(1,3,6,9,12,15,18)]

    str(jn1)

    jn1_1 <- jn1[c(2:4,6:7)]

    jn1_1$max <- apply(jn1_1, 1, max)

    jn1_1

    names(jn1)[2] <- "kb"
    names(jn1)[3] <- "pp"
    names(jn1)[4] <- "pw"
    names(jn1)[5] <- "sw"
    names(jn1)[6] <- "cb"
    names(jn1)[7] <- "km"

    jn3 <- melt(jn1, id=c('sentiment'))

    jn3

    jn3[is.na(jn3)] <- 0


    setwd("D:/dv")
    ggplot(jn3, aes(x=sentiment, y=value)) +
      geom_bar(aes(fill=variable), stat="identity", position="dodge") +
      theme(axis.text.x = element_text(angle=45, hjust=1))

    dev.copy(png, file = "members.png", width=800)  

    dev.off()
    #
    # ggplot(jn3, aes(x=sentiment, y=value)) +
    #   geom_bar(aes(fill=variable), stat="identity", position="dodge") +
    #   theme(axis.text.x = element_text(angle=45, hjust=1)) + facet_grid(sentiment ~ .)




    ggplot(jn3, aes(x=sentiment, y=value)) +
      geom_bar(aes(fill=variable), stat="identity", position="dodge") +
      theme(axis.text.x = element_text(angle=45, hjust=1)) + facet_grid( variable ~ .)

    dev.copy(png, file = "members_facet.png", width=800)  

    dev.off()


    #############

    jn4 <- jn3

    jn4

    jn4$sentiment <- gsub("^negative$", "negative", jn4$sentiment)  #
    jn4$sentiment <- gsub("^positive$", "positive", jn4$sentiment) #
    jn4$sentiment <- gsub("^anger$", "negative", jn4$sentiment) #
    jn4$sentiment <- gsub("^sadness$", "negative", jn4$sentiment) #
    jn4$sentiment <- gsub("^fear$", "negative", jn4$sentiment) #
    jn4$sentiment <- gsub("^trust$", "positive", jn4$sentiment) #
    jn4$sentiment <- gsub("^disgust$", "negative", jn4$sentiment) #
    jn4$sentiment <- gsub("^anticipation$", "positive", jn4$sentiment) #
    jn4$sentiment <- gsub("^joy$", "positive", jn4$sentiment) #
    jn4$sentiment <- gsub("^surprise$", "positive", jn4$sentiment)

    jn4$value <- as.numeric(jn4$value)

    jn5 <- jn4 %>%
      group_by(sentiment, variable) %>%
      summarise_each(funs(sum)) %>%
      arrange(variable, sentiment)

    jn5

    max(jn5$value)


    ggplot(jn5, aes(x=sentiment, y=value, label=variable, fill=variable)) +
      geom_bar( stat="identity", position=position_dodge()) +
      theme(axis.text.x = element_text(angle=45, hjust=1)) +
    geom_text(aes(label=variable), colour="black", vjust=1.5, position=position_dodge(0.9), size=4)



    dev.copy(png, file = "sent_group.png", width=800)  

    dev.off()

    jn6 <- jn5 %>%
            filter(sentiment == 'positive') %>%
            arrange(desc(value))


    jn6

    mean(jn6$value)

    jn7 <- jn5 %>%
      filter(sentiment == 'negative')%>%
      arrange(desc(value))

    mean(jn7$value)

    jn7

    #kb
    #pp
    #pw
    #cb
    #km
    #sw

    ###################

    kb4$who <- "kb"

    kb_tweet_count <- count(kb1)

    kb_word_count <- count(kb3)

    kb4_int <- kb3 %>%
      inner_join(nrc, by = "word")

    kb_word_sent_count <- sum(kb4$words)

    kb_stat <- c("kb", kb_tweet_count, kb_word_count, kb_word_sent_count)

    names(kb_stat)[1] <- "who"
    names(kb_stat)[2] <- "tweet_count"
    names(kb_stat)[3] <- "word_count"
    names(kb_stat)[4] <- "words_included"

    kb_stat <- as.data.frame(kb_stat)

    kb_stat

    kb_stat$words_incl_perc <- kb_stat$words_included/kb_stat$word_count

    #################

    pp4$who <- "pp"

    pp_tweet_count <- count(pp1)

    pp_word_count <- count(pp3)

    pp4_int <- pp3 %>%
      inner_join(nrc, by = "word")

    pp_word_sent_count <- sum(pp4$words)

    pp_stat <- c("pp", pp_tweet_count, pp_word_count, pp_word_sent_count)

    names(pp_stat)[1] <- "who"
    names(pp_stat)[2] <- "tweet_count"
    names(pp_stat)[3] <- "word_count"
    names(pp_stat)[4] <- "words_included"

    pp_stat <- as.data.frame(pp_stat)

    pp_stat

    pp_stat$words_incl_perc <- pp_stat$words_included/pp_stat$word_count


    #################

    pw4$who <- "pw"

    pw_tweet_count <- count(pw1)

    pw_word_count <- count(pw3)

    pw4_int <- pw3 %>%
      inner_join(nrc, by = "word")

    pw_word_sent_count <- sum(pw4$words)

    pw_stat <- c("pw", pw_tweet_count, pw_word_count, pw_word_sent_count)

    names(pw_stat)[1] <- "who"
    names(pw_stat)[2] <- "tweet_count"
    names(pw_stat)[3] <- "word_count"
    names(pw_stat)[4] <- "words_included"

    pw_stat <- as.data.frame(pw_stat)

    pw_stat

    pw_stat$words_incl_perc <- pw_stat$words_included/pw_stat$word_count

    #################

    cb4$who <- "cb"

    cb_tweet_count <- count(cb1)

    cb_word_count <- count(cb3)

    cb4_int <- cb3 %>%
      inner_join(nrc, by = "word")

    cb_word_sent_count <- sum(cb4$words)

    cb_stat <- c("cb", cb_tweet_count, cb_word_count, cb_word_sent_count)

    names(cb_stat)[1] <- "who"
    names(cb_stat)[2] <- "tweet_count"
    names(cb_stat)[3] <- "word_count"
    names(cb_stat)[4] <- "words_included"

    cb_stat <- as.data.frame(cb_stat)

    cb_stat$words_incl_perc <- cb_stat$words_included/cb_stat$word_count

    cb_stat

    #################

    km4$who <- "km"

    km_tweet_count <- count(km1)

    km_word_count <- count(km3)

    km4_int <- km3 %>%
      inner_join(nrc, by = "word")

    km_word_sent_count <- sum(km4$words)

    km_stat <- c("km", km_tweet_count, km_word_count, km_word_sent_count)

    names(km_stat)[1] <- "who"
    names(km_stat)[2] <- "tweet_count"
    names(km_stat)[3] <- "word_count"
    names(km_stat)[4] <- "words_included"

    km_stat <- as.data.frame(km_stat)

    km_stat$words_incl_perc <- km_stat$words_included/km_stat$word_count

    km_stat


    #################

    #################

    sw4$who <- "sw"

    sw_tweet_count <- count(sw1)

    sw_word_count <- count(sw3)

    sw4_int <- sw3 %>%
      inner_join(nrc, by = "word")

    sw_word_sent_count <- sum(sw4$words)

    sw_stat <- c("sw", sw_tweet_count, sw_word_count, sw_word_sent_count)

    names(sw_stat)[1] <- "who"
    names(sw_stat)[2] <- "tweet_count"
    names(sw_stat)[3] <- "word_count"
    names(sw_stat)[4] <- "words_included"

    sw_stat <- as.data.frame(sw_stat)

    sw_stat$words_incl_perc <- sw_stat$words_included/sw_stat$word_count

    sw_stat

    kb_stat

    pp_stat

    stat <- rbind(kb_stat, pp_stat, pw_stat, cb_stat, km_stat, sw_stat)

    stat

    mean(stat$words_incl_perc)


    ggplot(stat, aes(x=who, y=tweet_count)) +
      geom_bar(aes(fill=who), stat="identity", position="dodge") +
      theme(axis.text.x = element_text(angle=45, hjust=1))

    dev.copy(png, file = "tweet_count.png", width=800)  

    dev.off()


    # ggplot(stat, aes(x=words_incl_perc, y=tweet_count)) +
    #   geom_bar(aes(fill=who), stat="identity", position="dodge") +
    #   theme(axis.text.x = element_text(angle=45, hjust=1))

    jn3

    ggplot(stat, aes(x=who, y=words_included)) +
      geom_bar(aes(fill=who), stat="identity", position="dodge") +
      theme(axis.text.x = element_text(angle=45, hjust=1))

    dev.copy(png, file = "words_included.png", width=800)  

    dev.off()
