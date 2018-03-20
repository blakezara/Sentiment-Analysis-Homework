
# Social Analysis



### News Mood

### Analysis


Individual tweet polarity scores and time do ot appear to be storngly correlated. 
CNN, FOX and NYT all revieced negative polirty scores while BBC and CBS were posotive.



```python
#Dependencies
import tweepy
import json
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import time
from datetime import datetime
import seaborn as sns 
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()
```


```python
#API Key and Token
consumer_key = "mHYl5ZGR0d3Jo39AltJSgUrKF"
consumer_secret = "EJwY1I6lrC6HqPjBCiXmVeZ7u6kGz47XCqtnZGMTnBr4e6JAy2"
access_token = "2830183694-QqVbbCrlyhqt4XSjHz24GLerrlzDaOx5ADG8OfB"
access_token_secret = "nXHhI4LfTlbCvsDNxrdJWPofOhAwV08BrT8lIEYz608Fl"

```


```python
# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
```

### Target Media Outlets  


```python
#target media outlets and loop through most recent 100 tweets
target= ('@BBC', '@CBS', '@CNN', '@FoxNews', '@nytimes')
sentiments = []
tweet_times = []
compound_list = []
positive_list = []
negative_list = []
neutral_list= []
       
for handle in target:
    counter= 0
    public_tweets = api.user_timeline(handle, count = 100)
    for tweet in public_tweets:
        compound = analyzer.polarity_scores(tweet["text"])["compound"]
        pos = analyzer.polarity_scores(tweet["text"])["pos"]
        neu = analyzer.polarity_scores(tweet["text"])["neu"]
        neg = analyzer.polarity_scores(tweet["text"])["neg"]
        tweets_ago = counter
        tweet_text = tweet["text"]
        
        # Add each value to the appropriate array
        compound_list.append(compound)
        positive_list.append(pos)
        negative_list.append(neg)
        neutral_list.append(neu)
        
        
      # Add sentiments for each tweet into an array
        sentiments.append({"User" : handle,
                           "Date/Time": tweet["created_at"], 
                           "Compound": compound,
                           "Positive": pos,
                           "Negative": neu,
                           "Neutral": neg,
                           "The Tweet": tweet_text,
                           "Tweets Ago": counter})
        
        # Add to counter 
        counter = counter + 1

```


```python
# Convert sentiments to DataFrame
sentiments_pd = pd.DataFrame.from_dict(sentiments)
sentiments_pd
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Compound</th>
      <th>Date/Time</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>The Tweet</th>
      <th>Tweets Ago</th>
      <th>User</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0000</td>
      <td>Sat Mar 17 18:32:01 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Ouch! 😂🔥@GordonRamsay's daughter is hilarious....</td>
      <td>0</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.3612</td>
      <td>Sat Mar 17 18:03:04 +0000 2018</td>
      <td>0.857</td>
      <td>0.000</td>
      <td>0.143</td>
      <td>🐜1⃣2⃣3⃣Sir David Attenborough explores whether...</td>
      <td>1</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.3182</td>
      <td>Sat Mar 17 17:30:07 +0000 2018</td>
      <td>0.901</td>
      <td>0.000</td>
      <td>0.099</td>
      <td>One of the writers for The Simpsons has told @...</td>
      <td>2</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.4767</td>
      <td>Sat Mar 17 17:00:08 +0000 2018</td>
      <td>0.785</td>
      <td>0.000</td>
      <td>0.215</td>
      <td>🎓📚 'The moment I gave Reese Witherspoon my Leg...</td>
      <td>3</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0000</td>
      <td>Sat Mar 17 16:39:39 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>RT @BBCWorld: Blossom in south-west China capt...</td>
      <td>4</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.0000</td>
      <td>Sat Mar 17 15:03:12 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>This year, over 250 landmarks across the globe...</td>
      <td>5</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0.0000</td>
      <td>Sat Mar 17 14:43:43 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>RT @BBCRadio3: "Even if we found a complete th...</td>
      <td>6</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.8625</td>
      <td>Sat Mar 17 14:00:30 +0000 2018</td>
      <td>0.699</td>
      <td>0.000</td>
      <td>0.301</td>
      <td>⛷❤️ George has autism and other health conditi...</td>
      <td>7</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.3628</td>
      <td>Sat Mar 17 13:46:15 +0000 2018</td>
      <td>0.784</td>
      <td>0.073</td>
      <td>0.143</td>
      <td>RT @5liveSport: 'My mates didn't know I was pl...</td>
      <td>8</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0.4019</td>
      <td>Sat Mar 17 13:00:08 +0000 2018</td>
      <td>0.881</td>
      <td>0.000</td>
      <td>0.119</td>
      <td>Yes, Gary Oldman and @BBCEastEnders' Big Mo ar...</td>
      <td>9</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>10</th>
      <td>-0.7244</td>
      <td>Sat Mar 17 12:39:41 +0000 2018</td>
      <td>0.664</td>
      <td>0.336</td>
      <td>0.000</td>
      <td>RT @BBCOne: .@NiallOfficial won't let fame get...</td>
      <td>10</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0.5106</td>
      <td>Sat Mar 17 12:00:09 +0000 2018</td>
      <td>0.643</td>
      <td>0.112</td>
      <td>0.245</td>
      <td>♻️🗑 This shop encourages you to bring your own...</td>
      <td>11</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0.8883</td>
      <td>Sat Mar 17 11:30:09 +0000 2018</td>
      <td>0.488</td>
      <td>0.000</td>
      <td>0.512</td>
      <td>🇮🇪 🍀 Celebrate #StPatricksDay in true Irish st...</td>
      <td>12</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0.6249</td>
      <td>Sat Mar 17 11:03:05 +0000 2018</td>
      <td>0.796</td>
      <td>0.000</td>
      <td>0.204</td>
      <td>From scrambled eggs to chilli-spiced crisps: h...</td>
      <td>13</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0.6996</td>
      <td>Sat Mar 17 10:14:25 +0000 2018</td>
      <td>0.658</td>
      <td>0.066</td>
      <td>0.276</td>
      <td>RT @BBCTwo: Happy #StPatricksDay! Check out wh...</td>
      <td>14</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>15</th>
      <td>0.0000</td>
      <td>Sat Mar 17 10:00:02 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>🎶🐦 Did you know that bullfinches can learn to ...</td>
      <td>15</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>16</th>
      <td>-0.7717</td>
      <td>Sat Mar 17 09:39:19 +0000 2018</td>
      <td>0.749</td>
      <td>0.251</td>
      <td>0.000</td>
      <td>RT @BBCEngland: Stephen Hawking's PhD thesis h...</td>
      <td>16</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>17</th>
      <td>0.6369</td>
      <td>Sat Mar 17 09:00:06 +0000 2018</td>
      <td>0.714</td>
      <td>0.000</td>
      <td>0.286</td>
      <td>Ready to get active?\n🏃 Here are 5 ways you ca...</td>
      <td>17</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0.7783</td>
      <td>Fri Mar 16 19:38:34 +0000 2018</td>
      <td>0.726</td>
      <td>0.000</td>
      <td>0.274</td>
      <td>RT @BBCR1: This guy just raised over a million...</td>
      <td>18</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>19</th>
      <td>0.4926</td>
      <td>Fri Mar 16 18:31:01 +0000 2018</td>
      <td>0.610</td>
      <td>0.000</td>
      <td>0.390</td>
      <td>💪😲⚽️ Keepy-uppy the good work! https://t.co/lF...</td>
      <td>19</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>20</th>
      <td>0.5719</td>
      <td>Fri Mar 16 17:00:25 +0000 2018</td>
      <td>0.791</td>
      <td>0.000</td>
      <td>0.209</td>
      <td>❤️🎧 Bradley has never let Asperger's hold him ...</td>
      <td>20</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>21</th>
      <td>0.0000</td>
      <td>Fri Mar 16 16:30:01 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Running for the door on a Friday afternoon lik...</td>
      <td>21</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>22</th>
      <td>0.0000</td>
      <td>Fri Mar 16 16:00:00 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>🔊🎵Jarvis Cocker describes the dream-like exper...</td>
      <td>22</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0.0000</td>
      <td>Fri Mar 16 14:25:15 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>RT @BBCR1: The gruelling task of climbing Ben ...</td>
      <td>23</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>24</th>
      <td>0.3400</td>
      <td>Fri Mar 16 14:00:13 +0000 2018</td>
      <td>0.841</td>
      <td>0.000</td>
      <td>0.159</td>
      <td>Prepare to get that warm fuzzy feeling - the m...</td>
      <td>24</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>25</th>
      <td>0.6124</td>
      <td>Fri Mar 16 12:59:01 +0000 2018</td>
      <td>0.667</td>
      <td>0.000</td>
      <td>0.333</td>
      <td>🤔🎨 Why are soap bubbles such gorgeous colours?...</td>
      <td>25</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0.3612</td>
      <td>Fri Mar 16 12:38:40 +0000 2018</td>
      <td>0.902</td>
      <td>0.000</td>
      <td>0.098</td>
      <td>RT @BBCNewsPR: Get ready to make the headlines...</td>
      <td>26</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>27</th>
      <td>-0.6486</td>
      <td>Fri Mar 16 12:00:04 +0000 2018</td>
      <td>0.762</td>
      <td>0.238</td>
      <td>0.000</td>
      <td>😂 Fights, funerals, and giant furry heads... @...</td>
      <td>27</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>28</th>
      <td>0.5574</td>
      <td>Fri Mar 16 10:45:24 +0000 2018</td>
      <td>0.841</td>
      <td>0.000</td>
      <td>0.159</td>
      <td>RT @BBCRadio2: It's with a heavy heart that to...</td>
      <td>28</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>29</th>
      <td>0.0000</td>
      <td>Fri Mar 16 10:02:00 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Are you getting enough sleep? This simple test...</td>
      <td>29</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>470</th>
      <td>-0.1027</td>
      <td>Sat Mar 17 03:07:17 +0000 2018</td>
      <td>0.810</td>
      <td>0.105</td>
      <td>0.086</td>
      <td>Top seed Virginia is on the ropes against No. ...</td>
      <td>70</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>471</th>
      <td>-0.1280</td>
      <td>Sat Mar 17 03:02:06 +0000 2018</td>
      <td>0.664</td>
      <td>0.177</td>
      <td>0.159</td>
      <td>Teachers in Jersey City went on strike after t...</td>
      <td>71</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>472</th>
      <td>0.5719</td>
      <td>Sat Mar 17 02:47:03 +0000 2018</td>
      <td>0.684</td>
      <td>0.087</td>
      <td>0.228</td>
      <td>It was probably the first ghost story in India...</td>
      <td>72</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>473</th>
      <td>-0.3818</td>
      <td>Sat Mar 17 02:33:05 +0000 2018</td>
      <td>0.843</td>
      <td>0.157</td>
      <td>0.000</td>
      <td>RT @jimrutenberg: Trump joins the fight agains...</td>
      <td>73</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>474</th>
      <td>-0.5719</td>
      <td>Sat Mar 17 02:22:00 +0000 2018</td>
      <td>0.844</td>
      <td>0.156</td>
      <td>0.000</td>
      <td>In an interview, Andrew McCabe was blunt. “The...</td>
      <td>74</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>475</th>
      <td>-0.7430</td>
      <td>Sat Mar 17 02:10:16 +0000 2018</td>
      <td>0.751</td>
      <td>0.249</td>
      <td>0.000</td>
      <td>Breaking News: Andrew McCabe has been fired. P...</td>
      <td>75</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>476</th>
      <td>-0.8860</td>
      <td>Sat Mar 17 02:06:01 +0000 2018</td>
      <td>0.679</td>
      <td>0.321</td>
      <td>0.000</td>
      <td>RT @amyfiscus: NEW: McCabe fired: “The idea th...</td>
      <td>76</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>477</th>
      <td>0.0000</td>
      <td>Sat Mar 17 01:55:04 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Breaking News: President Trump's lawyers say i...</td>
      <td>77</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>478</th>
      <td>0.0772</td>
      <td>Sat Mar 17 01:47:04 +0000 2018</td>
      <td>0.929</td>
      <td>0.000</td>
      <td>0.071</td>
      <td>The number of homeless students in New York Ci...</td>
      <td>78</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>479</th>
      <td>-0.5994</td>
      <td>Sat Mar 17 01:32:05 +0000 2018</td>
      <td>0.546</td>
      <td>0.311</td>
      <td>0.143</td>
      <td>Modern Love: He was struggling with an obscure...</td>
      <td>79</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>480</th>
      <td>0.6808</td>
      <td>Sat Mar 17 01:17:05 +0000 2018</td>
      <td>0.772</td>
      <td>0.000</td>
      <td>0.228</td>
      <td>RT @lindsaycrouse: A doctor that once ran 3 ma...</td>
      <td>80</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>481</th>
      <td>0.0000</td>
      <td>Sat Mar 17 01:04:10 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Breaking News: Cracks on a new pedestrian brid...</td>
      <td>81</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>482</th>
      <td>0.0000</td>
      <td>Sat Mar 17 01:02:03 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Evening Briefing: Here's what you need to know...</td>
      <td>82</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>483</th>
      <td>0.0000</td>
      <td>Sat Mar 17 00:47:04 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Cooper Union reverses course and says it will ...</td>
      <td>83</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>484</th>
      <td>-0.7717</td>
      <td>Sat Mar 17 00:32:06 +0000 2018</td>
      <td>0.573</td>
      <td>0.427</td>
      <td>0.000</td>
      <td>Fatal collapse: A look at how the Florida brid...</td>
      <td>84</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>485</th>
      <td>0.0000</td>
      <td>Sat Mar 17 00:17:04 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>RT @TheSteinLine: For Sports Saturday in the @...</td>
      <td>85</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>486</th>
      <td>-0.1263</td>
      <td>Sat Mar 17 00:02:06 +0000 2018</td>
      <td>0.795</td>
      <td>0.113</td>
      <td>0.092</td>
      <td>"I have trouble settling down, and he's got ve...</td>
      <td>86</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>487</th>
      <td>0.0000</td>
      <td>Fri Mar 16 23:47:02 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>With new tariffs expected to take effect next ...</td>
      <td>87</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>488</th>
      <td>0.6808</td>
      <td>Fri Mar 16 23:32:02 +0000 2018</td>
      <td>0.752</td>
      <td>0.000</td>
      <td>0.248</td>
      <td>When wolves were reintroduced at Yellowstone a...</td>
      <td>88</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>489</th>
      <td>0.4019</td>
      <td>Fri Mar 16 23:17:03 +0000 2018</td>
      <td>0.812</td>
      <td>0.000</td>
      <td>0.188</td>
      <td>RT @porterthereport: Canada accepted fewer tha...</td>
      <td>89</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>490</th>
      <td>0.0000</td>
      <td>Fri Mar 16 23:06:06 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>How to spend 36 hours in Oaxaca, Mexico https:...</td>
      <td>90</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>491</th>
      <td>0.0000</td>
      <td>Fri Mar 16 22:53:01 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>When it comes to Russian meddling, President T...</td>
      <td>91</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>492</th>
      <td>0.0000</td>
      <td>Fri Mar 16 22:47:05 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Evening Briefing: Here's what you need to know...</td>
      <td>92</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>493</th>
      <td>0.6369</td>
      <td>Fri Mar 16 22:31:06 +0000 2018</td>
      <td>0.766</td>
      <td>0.000</td>
      <td>0.234</td>
      <td>"How am I supposed to find a life partner if I...</td>
      <td>93</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>494</th>
      <td>0.0000</td>
      <td>Fri Mar 16 22:17:03 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Why the tax law might make your car payments g...</td>
      <td>94</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>495</th>
      <td>-0.7430</td>
      <td>Fri Mar 16 22:03:04 +0000 2018</td>
      <td>0.733</td>
      <td>0.267</td>
      <td>0.000</td>
      <td>RT @Neil_Irwin: Trump keeps ignoring trade in ...</td>
      <td>95</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>496</th>
      <td>0.0000</td>
      <td>Fri Mar 16 21:51:02 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>Billie Piper may be a household name on the Br...</td>
      <td>96</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>497</th>
      <td>0.6369</td>
      <td>Fri Mar 16 21:35:07 +0000 2018</td>
      <td>0.802</td>
      <td>0.000</td>
      <td>0.198</td>
      <td>This soda bread is is best eaten still steamin...</td>
      <td>97</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>498</th>
      <td>-0.5719</td>
      <td>Fri Mar 16 21:20:02 +0000 2018</td>
      <td>0.783</td>
      <td>0.217</td>
      <td>0.000</td>
      <td>The ghost of Angelina Jolie past hangs over th...</td>
      <td>98</td>
      <td>@nytimes</td>
    </tr>
    <tr>
      <th>499</th>
      <td>0.4650</td>
      <td>Fri Mar 16 21:05:04 +0000 2018</td>
      <td>0.672</td>
      <td>0.102</td>
      <td>0.226</td>
      <td>Despite a celebrated refugee settlement system...</td>
      <td>99</td>
      <td>@nytimes</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 8 columns</p>
</div>




```python
#export to csv file
sentiments_pd.to_csv("Twitter_News_Analysis.csv", index=False)
```

### Sentimental Analysis of Tweets Scatter Plot


```python
date = time.strftime("%m/%d/%Y")

figure= sns.lmplot(x='Tweets Ago', y='Compound', hue='User', data=sentiments_pd, fit_reg=False)

plt.ylim(1, -1)
plt.xlim(0, 100)
plt.xlabel('Tweets Ago')
plt.ylabel('Polarity Scores')

plt.title(f"Sentimental Analysis of Tweets {date}")
plt.grid()


plt.savefig("Sentiment_Analysis.png")
plt.show()
```


![png](output_12_0.png)



```python
#Create averages
average_compound = sentiments_pd.groupby("User")["Compound"].mean()
average_compound

```




    User
    @BBC        0.163052
    @CBS        0.312026
    @CNN       -0.078621
    @FoxNews   -0.116043
    @nytimes   -0.068116
    Name: Compound, dtype: float64



### Average Sentiemnt Analysis


```python
#Create bar plot for 
date = time.strftime("%m/%d/%Y")

#figure= sns.barplot(data=average_compound_pd, fit_reg=False)

                     
average_compound.plot(kind='bar')
plt.title(f"Average Sentimate Analysis {date}")
plt.xlabel('News Outlet')
plt.ylabel('Average Sentiment Score')
plt.ylim(-.5, .5)
plt.xlim(-1, 5)
plt.style.use('seaborn')
plt.savefig('Sentiment_Avg.png')
plt.tight_layout()
plt.show()

```


![png](output_15_0.png)

