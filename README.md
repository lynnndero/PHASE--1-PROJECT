# Final Project Submission

Student name: LYNN WANJIKU NDERO

Student pace: FULL TIME

Scheduled project review date/time: 1 WEEK

Instructor name: ANTONNY MUIKO

[Presentation link](https://docs.google.com/presentation/d/13bBiNHK4Evd21OgNQ6q3XQ3D4Ck9IQ1-403VAEhzoYM/edit#slide=id.p)

# PROJECT TITLE : DETERMINING THE BEST MOVIE GENRES AT THE BOX OFFICE FOR MICROSOFT

# 1. Business Understanding
Microsoft want to embark on the business of creating original videos due to the trend from other big companies that are creating their own original videos.This lead to their decision of wanting to start a new movie studio, however, they need to know the top movie genres to create.

# Problem Statement

We need to explore the different movie genres created by other studios and get to know the success criteria at the box office by extractin and analysing the relevant dataset. The results derived from the exploratory analysis will aid the business stakeholder, head of microsoft, to determine which genres they will create from their studio with high returns.

# Main Objective

Determine the best movie genres with the highest returns, highest average rating and the runtimes minutes.

# Specific Objectives

Identify the top_ten best films based on runtime minutes and ratings
Evaluate the gross collected from the films
Identify the success criteria of the films to the movie studios based on returns

Import the pandas library

import pandas as pd
import sqlite3
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
Open 'bom.movies_gross.csv' data using pd.read_csv

Open 'bom.movies_gross.csv' data using pd.read_csv

### 2. DATA PREPARATION
2.1 Discovering how many missing values are there in movie_df

movie_df table
#loading the movie_df table
movies_df

movies_df.shape
(3387, 5)

#provide a summary of the metadata to be able to identify the columns with the missing values

movies_df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3387 entries, 0 to 3386

Replacing missing values in foreign_gross column with the median but first converting the datatype from string to float

#converting datatype string into float using .str.replace function
movies_df['foreign_gross'] = movies_df['foreign_gross'].str.replace(',','').astype(float)

#filling in the missing values with the .median of the foreign_gross column
movies_df['foreign_gross'].fillna(movies_df['foreign_gross'].median(), inplace= True)

#confirming the values have been filled in the dataframe
movies_df

movies_df.info()
<class 'pandas.core.frame.DataFrame'>

2.1.2 Drop rows from movie_df with missing values

#using .dropna to remove all missing values 
movies_df.dropna(axis=0, inplace = True)

#confirming if there are any Nan values by column
movies_df.isna().sum()
title             0
studio            0
domestic_gross    0
foreign_gross     0
year              0
dtype: int64

2.1.2.2 Domestic_gross columns

#Since the column has a few values missing then we can drop the rows in the column

movies_df.dropna(subset = 'domestic_gross', inplace = True)

movies_df.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 3356 entries, 0 to 3386

2.2 Discovering how many missing values are there in movie_basics

movie_basics

movie_basics.info()
<class 'pandas.core.frame.DataFrame'>

movie_basics.dropna(subset= 'original_title', inplace = True)

movie_basics.info()
<class 'pandas.core.frame.DataFrame'>


movie_basics.dropna(subset= 'genres', inplace = True)

movie_basics.info()
<class 'pandas.core.frame.DataFrame'>

movie_basics = movie_basics.dropna()

movie_basics.info()
​
<class 'pandas.core.frame.DataFrame'>
Int64Index: 112232 entries, 0 to 146139

Checking for any duplicates

​
movie_basics.duplicated().sum()
0

2.3 Checking for missing values in movie_ratings

movie_ratings

movie_ratings.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 73856 entries, 0 to 73855

Checking for duplicates in movie_rating

movie_ratings.duplicated().sum()
0

Merging the tables using movie_id column

movies_reviews_and_rating = movie_basics.merge(movie_ratings, on='movie_id').merge(movies_df, left_on='primary_title', right_on='title')
movies_reviews_and_rating

#drop all duplicates
movies_reviews_and_rating = movies_reviews_and_rating.drop(columns=['original_title','title'])
movies_reviews_and_rating

movies_reviews_and_rating.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2952 entries, 0 to 2951


### 3. EXPLORATORY DATA ANALYSIS
List of top_ten movies based on numvotes and averagerating columns

movies_reviews_and_rating


#select the required columns from the movie_reviews_and rating table 
movie_genres_ratings = movies_reviews_and_rating[['movie_id','runtime_minutes','genres','averagerating','numvotes','studio','year']]
movie_genres_ratings

#get a list of the top ten movie genres based on average rating
Top_ten_movie_genre = movie_genres_ratings[['movie_id','genres','runtime_minutes','averagerating','studio','year']].sort_values(by = 'averagerating',ascending=False)
Top_ten_movie_genre.head(10)


#get the top_ten movie genres based on number of votes
movie_genres_votes = movie_genres_ratings[['movie_id', 'genres','runtime_minutes','numvotes','studio']].sort_values(by='numvotes', ascending=False)
movie_genres_votes.head(10)

Evaluate the gross collected from the top movie genres

Top_ten_movie_grosscollected = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='averagerating', ascending=False)
Top_ten_movie_grosscollected.head(10)
#domestic and foreign gross collected from the top ten movie genres
Top_ten_movie_grosscollected = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='averagerating', ascending=False)
Top_ten_movie_grosscollected.head(10)

domestic_gross
Top_ten_movie_grosscollected2 = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='domestic_gross', ascending=False)
Top_ten_movie_grosscollected2.head(10)

#calculating the total for the movie_genres
Top_ten_movie_grosscollected['total'] = Top_ten_movie_grosscollected['foreign_gross'] + Top_ten_movie_grosscollected['domestic_gross']
Top_ten_movie_grosscollected.head(10)

Top_ten_movie_grosscollected3 = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='foreign_gross', ascending=False)
Top_ten_movie_grosscollected3.head(10)

#using statistical method to get the mean, median, IQR, standard deviation of the movie_reviews_and ratings
movie_stat_summary = movies_reviews_and_rating[['runtime_minutes','domestic_gross','foreign_gross']].describe()
movie_stat_summary


### 4. DATA VISUALIZATION
Representation of the analysis
Top_ten_movie_genre

10
Top_ten_movie_genre.head(10)

Top_ten_movie_genre[['movie_id', 'genres','runtime_minutes','averagerating']].head(10)
Top_ten_movie_genre[['movie_id', 'genres','runtime_minutes','averagerating']].head(10)

Top_ten_movie_genre['runtime_minutes'].describe()
count    2952.000000
mean      107.305894
std        20.043335
min         3.000000
25%        94.000000
50%       105.000000
75%       118.000000
max       272.000000
Name: runtime_minutes, dtype: float64

Runtime minutes histogram
#Plotting a bar chart of the top ten movie genre based on average rating
x= Top_ten_movie_genre['runtime_minutes']
​
plt.hist(x, bins=100 , edgecolor = 'black')
plt.xlabel('Runtime minutes')
plt.ylabel('Frequency')
plt.title('Histogram On Distribution Of The Movie Runtime_Minutes')
plt.show()

From the graph above, we see that the runtime is clustered towards the mean showing normal distrubution. We can also observe that the most movie time is above 100minutes.

DOMESTIC AND FOREIGN GROSS
Creating a bar graph to represent comparison between domestic and foreign gross per year

Top_ten_movie_grosscollected


Domestic Gross Bar Chart
Top_ten_movie_grosscollected['year'].describe()
count    2952.000000
mean     2014.089092
std         2.442117
min      2010.000000
25%      2012.000000
50%      2014.000000
75%      2016.000000
max      2018.000000
Name: year, dtype: float64
#plot bar chart of the domestic gross per year 
x= Top_ten_movie_grosscollected['year']
y= Top_ten_movie_grosscollected['domestic_gross']
length = np.arange(len(Top_ten_movie_grosscollected))
plt.figure(figsize=(10,5))
plt.bar(x, y, label = 'Top ')
plt.xlabel('Year')
plt.ylabel('Domestic Gross')
plt.title('Domestic Gross Revenue per Year')
plt.show();

From the graph above we get to see that the year 2018 had the highest domestic gross as compared with the other years.

Foreign gross revenue per year
#plot bar chart of the foreign gross per year 
x= Top_ten_movie_grosscollected['year']
y= Top_ten_movie_grosscollected['foreign_gross']
length = np.arange(len(Top_ten_movie_grosscollected))
plt.figure(figsize=(10,5))
plt.bar(x, y)
plt.xlabel('Year')
plt.ylabel('Foreign Gross')
plt.title('Foreign Gross Revenue per Year')
plt.show();

From the graph above we can deduce that the year 2015 had the highest gross revenue as compared to the other years.

movie_genres_votes.head(10)

Number of Votes BarCart
# Plot barchart of the number of votes of the top ten genres 
x = movie_genres_votes['genres'].head(10)
y = movie_genres_votes['numvotes'].head(10)
plt.figure(figsize=(10,5))
plt.barh(x, y)
plt.xlabel('Genre')
plt.ylabel('Number of Votes')
plt.title('Number of Votes Barchart')
plt.show();


From the graph above showing comparison of the top ten genres and the number of votes received, we see that the Action,Adventure and Sci-Fi have the highest number of votes

Top_ten_movie_grosscollected.head(10)

Plotting a scatter plot to show the relationship between average rating and the total gross received
#plotting a scatter plot diagram for average rating and total gross 
x = Top_ten_movie_grosscollected['averagerating'].head(100)
y = Top_ten_movie_grosscollected['total'].head(100)

plt.scatter(x,y)
plt.xlabel('average rating')
plt.ylabel('total')
plt.title('Average Rate Vs Total Gross')
plt.show();

From the graph above, we can see that there is a negative correlation between the values of both average rating and total gross.

Genre Vs Average Rating Bar Graph
#Plotting a bar chart of the top ten movie genre based on average rating
x= Top_ten_movie_genre['genres'].head(20)
y= Top_ten_movie_genre['averagerating'].head(20)

plt.figure(figsize=(10,5))
plt.bar(x, y, label = 'Top ten genres')
plt.xlabel('Genre')
plt.ylabel('Average Rating')
plt.title('Genre Vs Average Rating Bar Graph')
plt.xticks(rotation =45);


### 5. CONCLUSIONS
From the analysis above we get to see that runtime of the movie genres are clustered towards the mean showing normal positive distrubution. We can also observe that for most movies, running time is above 100minutes.

Moreover, 2018 had the highest domestic gross revenue while 2015 had the highest foreign gross revenue.

From the top ten movie genre based on the average rating we can see that the top 4 movie genres are adventure,documentary,drama and comedy respectively.

The averageratings and the total gross received have no correlation.

### 6. RECOMMENDATIONS
The stakeholders should consider the top 5 movie genres(adventure,documentary,drama,comedy) due to the ratings received over the years.
When considering the gross revenue recieved, action,adventure and sci-fi have the highest returns and should also be considered.
The runtime of the movies should be an average of 105 minutes.



