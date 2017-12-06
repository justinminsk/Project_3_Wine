# Project 3 Wine

## How To Run
This project requires C++ build tools and Andaconda with numpy, pandas, matplotlib, seaborn, subprocess, and wordcloud installed.

## Known Problems
On Github's notebook some of the HTML imgs do not display. Tables do not display in the readme to see the full report go to https://www.kaggle.com/justinminsk/looking-at-wine-by-price-and-points.

   # Finding the best 'College' Wine
   
   When people think of "College" wine many people think of the wine below, a cheap boxed wine with no flavor. I, however, have a different idea of what "College" wine should be.
   
   <img src="https://cdn.pastemagazine.com/www/blogs/lists/balck%20box.jpg" width=200 height=200 />

 As a college student I like to buy things in what I call the college way, that means I like to buy wine for cheap, but I like wine that tastes good. Using this idea I decided to find the wines on this list of wine data with good scores (80-100, B or A) and a cheap price. The perfrect "College" wines.
 
 ## Importing and Looking at the Data

First we need import the libraries we will be using in our project.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib as mpl
import seaborn as sns
from subprocess import check_output
from wordcloud import WordCloud, STOPWORDS
#import libraries
```


Then we bring in the data our data in using pandas. I also show you what the table looks like. It has great information from a description of the wine to the points and price. It also gives the country and region the wine is made in. 

```python
wine = pd.read_csv('winemag-data_first150k.csv')
wine.head()
#import data
```


Then we get rid of rows with 'NA' while some might still be useable many of them do not contain price which is not useful in our search for the best 'college wines'..

```python
wine = wine.dropna()
#get rid of rows with na
```

## Looking at Distributions


It is always good to look at distributions, here we are looking at the point distribution. As you can see below it has a decent standard distribution.

```python
sns.distplot(wine['points'], kde=False);
#looking at the point distribution 
```
![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/pointsBar.png)

Next we look at price distribution, this is pretty bunched up below 100 dollars. 

```python
sns.distplot(wine['price'], kde=False);
#looking at distribution of price
```
![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/priceBar.png)

This graph shows points and price in relation to each other. The points show the range of price at each point value. While the line shows that price does increase with the amount of points.

```python
sns.regplot(x='points', y='price', x_estimator=np.mean, data=wine)
#shows price and points on the same scale
```
![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/pricePointsScatter.png)

## Breaking the Data up and Adding Points/Price

Here we combine points and price by dividing points by price to make an number representing the quality of wine divided by the price. 

```python
points_by_price = wine['points'] / wine['price']
#create points divided by price
s1 = pd.Series(points_by_price, name = 'Points by Price')
#create a series with points divided by price
wine2 = pd.concat([wine, s1], axis = 1)
#add points by price to wine data
```

Here is that distribution which is more favorable to the lower pointed wine.

```python
sns.distplot(wine2['Points by Price'], kde=False);
#look at distribution of points divided by price
```
![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/pointsByPriceBar.png)

Now as a US citizen it is easier for me to get US wine, I decided to focus on wine made in the US over other countries. I also look at the distribution which looks simlar to the points by price graph above with all of the data.

```python
US = wine['country'].map(lambda x: str(x).startswith('US'))
#get a boolean list with true being us in country
USwine = wine2[US]
#make a list of just us wines
USwine = USwine.dropna()
#drop na rows
sns.distplot(USwine['Points by Price'], kde=False);
#look at distribution of wines
```

![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/USpointsByPriceBar.png)

## Top US 'College' Wines

Here are the top ten "College" wines.

The one I would try on this list would be the Gallo Family Vineyard Cabernet Sauvignon which is number three on our list.

<img src="https://www.drinkupessex.uk/wp-content/uploads/2015/07/gallo-family-vineyards-cabernet-sauvignon-red-wine.jpg" width=400 height=400 />

Which comes from California, but not from a normal wine region in California. It is also a grand total of 5 USD, cheap and it has a score of 85 which even off a ten point scale is a B.

```pyton
sorted_US = USwine.sort_values(by = ['Points by Price'], ascending=False)
#look at the list sorted by points by price
sorted_US.head(10)
#look at top ten
```

Looking at the top ten wines you can see that it favors the lower point wines more than I though so here is the top ten wines 90 or higher. 
My pick from the list would be the Arbor Crest Sauvignon Blanc at number 4 on this list.

<img src="https://thewashingtonvine.files.wordpress.com/2012/08/img_1899.jpg" width=200 height=200 />

This can be found a Washington State which I am now seeing seems to be a larger wine producer in the US then I thought. This wine is still only 8 USD, still cheap and has a score of 91 or an A on a ten point scale.

```python
GT80 = USwine['points'].map(lambda x: x > 89)
#only look at points 90 or above
GT80USwine = USwine[GT80]
#turn it into a series
sorted_US = GT80USwine.sort_values(by = ['Points by Price'], ascending=False)
#sort by points by price
sorted_US.head(10)
#look at top ten
```

Just in case you want to get some fancy "College" wine here are the top ten wines pointed 95 or higher. I like a good Riesling and Januik's is top of our list.

<img src="https://cdn.nexternal.com/nhj/images/2015RieslingBacchusNV.jpg" width="200" height="400" />

Here is the wine you would be looking for and with this table you can find wine region which is the Columbia Valley which a map of that region is this,

<img src="http://winefolly.wpengine.netdna-cdn.com/wp-content/uploads/2017/02/USA-Washington-Wine-Map-Wine-Folly.jpg" width="500" height="500" />

This wine is starting to be more on the expensive side with a 20 USD price tag, but with a 95 score it is a strong A.

```python
GT90 = USwine['points'].map(lambda x: x > 94)
#look at points 95 and over
GT90USwine = USwine[GT90]
#make a list of 95 and over point wines
sorted_US = GT90USwine.sort_values(by = ['Points by Price'], ascending=False)
#sort it
sorted_US.head(10)
#look at top ten
```

## Looking at What was said about the Wines


With my tables I can look up also the best "College" chardonnay, Moscato, or any other type of wine. There are a ton of different wines on this list and looking at top tens or best "College" wines would take a lot of time and make a lot of tables. Instead I decided to look at the description column and see what words where commonly used to describe our wines.

```python
mpl.rcParams['figure.figsize']=(10.0,5.0)
mpl.rcParams['font.size']=20                
mpl.rcParams['savefig.dpi']=100              
mpl.rcParams['figure.subplot.bottom']=.1 
#make the frame the wordcloud will be in

stopwords = set(STOPWORDS)
data = USwine
#get stop words and the data

wordcloud = WordCloud(
                          background_color='white',
                          stopwords=stopwords,
                          max_words=100,
                          max_font_size=50, 
                          random_state=False
                         ).generate(str(data['description']))
#varibles for the wordcloud

print(wordcloud)
fig = plt.figure(1)
plt.imshow(wordcloud)
plt.axis('off')
plt.show()
#show wordcloud
```

![alt text](https://github.com/justinminsk/Project_3_Wine/blob/master/Graphs/wordcloud.png)


Here we can see that they like to talk about the fruity taste, the blend, and how rich it is. Since this is a list with wines only 80 or above as you can see above most of the words are positive or describing the wines. After you drink some of the wines on this list I think a good next step would be to look at what words they use to describe your favorites and then search for other wines that are fruity, rich, or any other words in the description. That would give you a great starting point to find other great "College" wines.

-Justin Minsk, 
justin.minsk@gmail.com
