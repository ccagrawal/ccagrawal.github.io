---
layout: post
title:  "The Numbers Behind Drake"
tags:
  - music
hero: /content/2018-10-31-the-numbers-behind-drake/drake.jpg
overlay: purple
published: true

---
Drake is *the* most popular hip-hop artist today [(as measured by Spotify streams)](https://en.wikipedia.org/wiki/List_of_most-streamed_artists_on_Spotify). He generates hit after hit as if he has a formula for creating successful songs. With help from [Spotify](https://developer.spotify.com/documentation/web-api/) and [Genius](https://docs.genius.com/), we can try reverse-engineering that formula.

In this post, we'll analyze Drake's discography from several angles to see how his music has evolved over time and which features lead to popularity.

## Dataset

I use tracks from the following albums:

* *Scorpion* (2018)
* *More Life* (2017)
* *Views* (2016)
* *If You're Reading This It's Too Late* (2015)
* *Nothing Was The Same* (2013)
* *Take Care* (2011)
* *Thank Me Later* (2010)
* *So Far Gone* (2009)

I omit *What a Time to Be Alive* (2015) because it's a collab with Future, and the tracks are rather different from the rest of Drake's discography.

I also remove the following interlude tracks from all analysis:

* "Skepta Interlude" - *More Life*
* "Jorja Interlude" - *More Life*
* "Summers Over Interlude" - *Views*
* "Wednesday Night Interlude" - *If You're Reading This It's Too Late*
* "Cameras / Good Ones Go Interlude - Medley" - *Take Care*
* "Buried Alive Interlude" - *Take Care*
* "Cece's Interlude" - *Thank Me Later*

## General Trends

### Popularity

Spotify offers a popularity measure for each track. Here's their explanation:

> The popularity is calculated by algorithm and is based, in the most part, on the total number of plays the track has had and how recent those plays are. Generally speaking, songs that are being played a lot now will have a higher popularity than songs that were played a lot in the past.

And here is the popularity of each of Drake's albums, with singles and other notable tracks highlighted:

![Popularity](/content/2018-10-31-the-numbers-behind-drake/01_popularity.png)

* Unsurprisingly, popularity is quite correlated with album age. 
* *Take Care* appears to have outlasted a few of his more recent albums among Spotify listeners.

### Danceability

Another one of Spotify's characteristics is danceability:

> Danceability describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity.

Here is the danceability of Drake's albums and notable tracks:

![Danceability](/content/2018-10-31-the-numbers-behind-drake/02_danceability.png)

* Drake's newer music tends to have higher danceability; in particular, there is a sharp change after *Take Care*.
* The danceability measure looks a bit off on a few songs. Perhaps this is due to mid-song shifts that compromise "rhythm stability" and "overall regularity".
 * For example, ["Nice For What"](https://open.spotify.com/track/3CA9pLiwRIGtUBiMjbZmRw?si=MgOpLV7VTey8S0W4u7kkzw) has the Big Freedia "These Hoes! You're Boy. I Made!" bridge in the middle. 
 * ["Controlla"](https://open.spotify.com/track/4CpKEkdGbOJV51cSvx7SoG?si=dj887qEiSQ-isq868DuPVQ) has the Beenie Man "Tear Off Mi Garment" sample in the middle.
* Nobody would consider ["Wu-Tang Forever"](https://open.spotify.com/track/6KziiQUOoCmC7Kc7Rv4jar?si=uhFAcfHsQxOCnunbHLKZ7Q) the most danceable song on *Nothing Was The Same*. Perhaps consistency is a better name for this metric.

### Speechiness

Spotify also provides a speechiness metric:

> Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the [higher] the attribute value.

Here is the speechiness of Drake's albums and notable tracks:

![Speechiness](/content/2018-10-31-the-numbers-behind-drake/03_speechiness.png)

* Drake's music has been decreasing in speechiness over time. 
* The immediate takeaway is that he's rapping less and singing more.
* While that may be true, another explanation is that his instrumentals are becoming more prominent (e.g. ["Nonstop"](https://open.spotify.com/track/0TlLq3lA83rQOYtrqBqSct?si=CTMrePv-TxutrDfYV9dvjA), ["Worst Behavior"](https://open.spotify.com/track/6oF3Es1YzzmLKjGBfThUvD?si=tmZnaBPrTcSMH_nh4wQmag)).

### Lexical Diversity

I calculate lexical diversity (the proportion of words that are unique) for each song using lyrics from Genius. Here is the lexical diversity of Drake's albums and notable tracks:

![Lexical Diversity](/content/2018-10-31-the-numbers-behind-drake/05_lexical_diversity.png)

* Drake's lexical diversity has been slightly decreasing over time.
* His most popular singles are almost all on the left of the graph (i.e. his hits are more repetitive).

## Sentiment Analysis

### Methodology

Artists can evoke sentiment through two mediums -- instrumentals and lyrics. To evaluate the sentiment of Drake's instrumentals, we can use Spotify's valence metric:

> A measure [...] describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry).

To evaluate the sentiment of Drake's lyrics, we can use the [sentimentr](https://github.com/trinker/sentimentr) package to score his lyrics from Genius:

> It is a dictionary lookup approach that tries to incorporate weighting for valence shifters (i.e., negators, amplifiers (intensifiers), de-amplifiers (downtoners), and adversative conjunctions)

I create a comprehensive sentiment measure by standardizing the valence metric and the lyrical sentiment scores, and then averaging them for each track.

### Accuracy Issues

Primitive sentiment analysis methods involving tallying the frequency of positive and negative words. While the sentimentr package is slightly more rigorous (by considering valence shifters), it's still dependent on the accuracy of the underlying sentiment dictionary. Unfortunately, I think hip-hop needs its own.

Profanity is widely prevalent, and while cuss words are traditionally negative, they often aren't in hip-hop. Consider the following lyrics from ["Nice For What"](https://open.spotify.com/track/3CA9pLiwRIGtUBiMjbZmRw?si=MgOpLV7VTey8S0W4u7kkzw):


> I wanna know who **mothafuckin** representin' in here tonight

> Everybody get your **mothafuckin**' roll on 
 
> Gotta hit the club, gotta make that **ass** jump
 
> Gotta hit the club like you hit them **mothafuckin'** angles

Or some lyrics from ["Up All Night"](https://open.spotify.com/track/75L0qdzRnhwV62UXoNq3pE?si=xAmiR9WZTeeWy2mEYMghdw):

> Shout out to the fact that I'm the youngest **nigga** doing it

> Man I love my team, I would die for them **niggas**, aww

In these instances, the cuss words shouldn't be scored negatively. However, every sentiment dictionary available associates profanity with negative sentiment. 

This is just one of many issues -- sarcasm is difficult to detect, lyrics from Genius often spell out words as they are pronounced rather than their true spelling, etc. Unless we go through lyrics manually, we can't perfectly analyze sentiment. Thus, you should take the following results with a grain of salt.

### Sentiment Results

Here is the sentiment score of Drake's albums with notable songs highlighted:

![Sentiment](/content/2018-10-31-the-numbers-behind-drake/04_sentiment.png)

* Some of the most negative tracks come in *Scorpion*, which sounds reasonable considering Drake references his feud with Pusha T several times in the album.
* Overall, sentiment seems a bit noisy. More rigorous analysis needs to be done on the lyrics before we can trust the assessment.

## Predicting Popularity

### Methodology

Thus far, we've casually observed some relationships between various features and popularity. Now, we'll formally try predicting Spotify's popularity measure. I start with the following features:

* Album
* Danceability
* Energy
* Loudness
* Speechiness
* Acousticness
* Instrumentalness
* Liveness
* Tempo
* Sentiment
* Lexical Diversity

Album is the only categorical feature; we must control for album because more recent ones tend to be more popular. In essence, we're trying to predict a song's popularity *relative to that of the album overall*. Other than sentiment and lexical diversity, all the features come from [Spotify](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/).

For now, I want to keep things simple and stick to an [OLS linear regression](https://en.wikipedia.org/wiki/Ordinary_least_squares). I manually test different combinations of features to achieve high explanatory power without overfitting.

### Results

|                          | Coefficient | Standard Error | T-Statistic | P Value |
|--------------------------|:-----------:|:--------------:|:-----------:|:-------:|
| **Intercept**            |     66.8    |       3.0      |     22.4    | < 2e-16 |
| **Danceability**         |     **10.1**    |       **2.9**      |     **3.5**     |  **7.5e-4** |
| **Lexical Diversity**    |    **-13.2**    |       **3.3**      |     **-4.0**    |  **1.1e-4** |
| **Scorpion**             |     14.8    |       1.8      |     8.4     | 1.2e-13 |
| **More Life**            |     3.8     |       1.9      |     2.0     |   .043  |
| **Views**                |     3.4     |       1.8      |     1.8     |   .076  |
| **Nothing Was The Same** |     -1.3    |       2.0      |     -0.7    |   .514  |
| **Take Care**            |     5.7     |       2.0      |     2.8     |   .006  |
| **Thank Me Later**       |     -4.2    |       2.1      |     -1.9    |   .054  |
| **So Far Gone**          |     -3.1    |       2.6      |     -1.2    |   .233  |
|                          |             |                |             |         |
| **Target Variable**      |  Popularity |                |             |         |
| **Sample Size**          |     132     |                |             |         |
| **Multiple R-Squared**   |    0.647    |                |             |         |

Note: *If You're Reading This It's Too Late* is omitted here because one album must be left out as a baseline. It essentially has a coefficient of 0.

### Interpretation

As discussed earlier (and confirmed by the regression results), higher danceability and lower lexical diversity predict higher popularity. Coincidentally, his music has been trending in those directions over time.

Let's revisit Spotify's explanation for danceability:

> Danceability describes how suitable a track is for dancing based on a combination of musical elements including tempo, **rhythm stability**, beat strength, and **overall regularity**.

In other words, maintaining a consistent rhythm while repeating the same lyrics over and over again leads to increased popularity. I guess we don't like hip-hop when it's too difficult to rap along.

So stop blaming Drake for making bland music or lacking creativity; blame his audience for their unsophisticated taste. All he's doing is giving millions of fans music they want to hear.

## Next Ideas

Here are a few ideas I hope to explore in the future:

* **Improved lyrical sentiment analyis** - Can we make a hip-hop dictionary to more accurately assess sentiment?
* **Stylometry analysis** - Are there certain linguistic features unique to each artist? 
* **Hip-Hop Trends** - Are the trends discussed in this post unique to Drake, or do they pertain to all of hip-hop?
* **Feature Boost** - How much is each artist worth in terms of boosted popularity when they're featured in a track?