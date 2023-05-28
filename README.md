# Final Project Submission

Student name: LYNN WANJIKU NDERO

Student pace: FULL TIME

Scheduled project review date/time: 1 WEEK

Instructor name: ANTONNY MUIKO

Blog post URL

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
title	studio	domestic_gross	foreign_gross	year
0	Toy Story 3	BV	415000000.0	652000000	2010
1	Alice in Wonderland (2010)	BV	334200000.0	691300000	2010
2	Harry Potter and the Deathly Hallows Part 1	WB	296000000.0	664300000	2010
3	Inception	WB	292600000.0	535700000	2010
4	Shrek Forever After	P/DW	238700000.0	513900000	2010
...	...	...	...	...	...
3382	The Quake	Magn.	6200.0	NaN	2018
3383	Edward II (2018 re-release)	FM	4800.0	NaN	2018
3384	El Pacto	Sony	2500.0	NaN	2018
3385	The Swan	Synergetic	2400.0	NaN	2018
3386	An Actor Prepares	Grav.	1700.0	NaN	2018
3387 rows × 5 columns

movies_df.shape
(3387, 5)
#provide a summary of the metadata to be able to identify the columns with the missing values
movies_df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3387 entries, 0 to 3386
Data columns (total 5 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   title           3387 non-null   object 
 1   studio          3382 non-null   object 
 2   domestic_gross  3359 non-null   float64
 3   foreign_gross   2037 non-null   object 
 4   year            3387 non-null   int64  
dtypes: float64(1), int64(1), object(3)
memory usage: 132.4+ KB
Replacing missing values in foreign_gross column with the median but first converting the datatype from string to float

#converting datatype string into float using .str.replace function
movies_df['foreign_gross'] = movies_df['foreign_gross'].str.replace(',','').astype(float)
#filling in the missing values with the .median of the foreign_gross column
movies_df['foreign_gross'].fillna(movies_df['foreign_gross'].median(), inplace= True)
#confirming the values have been filled in the dataframe
movies_df
title	studio	domestic_gross	foreign_gross	year
0	Toy Story 3	BV	415000000.0	652000000.0	2010
1	Alice in Wonderland (2010)	BV	334200000.0	691300000.0	2010
2	Harry Potter and the Deathly Hallows Part 1	WB	296000000.0	664300000.0	2010
3	Inception	WB	292600000.0	535700000.0	2010
4	Shrek Forever After	P/DW	238700000.0	513900000.0	2010
...	...	...	...	...	...
3382	The Quake	Magn.	6200.0	18700000.0	2018
3383	Edward II (2018 re-release)	FM	4800.0	18700000.0	2018
3384	El Pacto	Sony	2500.0	18700000.0	2018
3385	The Swan	Synergetic	2400.0	18700000.0	2018
3386	An Actor Prepares	Grav.	1700.0	18700000.0	2018
3387 rows × 5 columns

movies_df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3387 entries, 0 to 3386
Data columns (total 5 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   title           3387 non-null   object 
 1   studio          3382 non-null   object 
 2   domestic_gross  3359 non-null   float64
 3   foreign_gross   3387 non-null   float64
 4   year            3387 non-null   int64  
dtypes: float64(2), int64(1), object(2)
memory usage: 132.4+ KB
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
Data columns (total 5 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   title           3356 non-null   object 
 1   studio          3356 non-null   object 
 2   domestic_gross  3356 non-null   float64
 3   foreign_gross   3356 non-null   float64
 4   year            3356 non-null   int64  
dtypes: float64(2), int64(1), object(2)
memory usage: 157.3+ KB
2.2 Discovering how many missing values are there in movie_basics

movie_basics
movie_id	primary_title	original_title	start_year	runtime_minutes	genres
0	tt0063540	Sunghursh	Sunghursh	2013	175.0	Action,Crime,Drama
1	tt0066787	One Day Before the Rainy Season	Ashad Ka Ek Din	2019	114.0	Biography,Drama
2	tt0069049	The Other Side of the Wind	The Other Side of the Wind	2018	122.0	Drama
3	tt0069204	Sabse Bada Sukh	Sabse Bada Sukh	2018	NaN	Comedy,Drama
4	tt0100275	The Wandering Soap Opera	La Telenovela Errante	2017	80.0	Comedy,Drama,Fantasy
...	...	...	...	...	...	...
146139	tt9916538	Kuambil Lagi Hatiku	Kuambil Lagi Hatiku	2019	123.0	Drama
146140	tt9916622	Rodolpho Teóphilo - O Legado de um Pioneiro	Rodolpho Teóphilo - O Legado de um Pioneiro	2015	NaN	Documentary
146141	tt9916706	Dankyavar Danka	Dankyavar Danka	2013	NaN	Comedy
146142	tt9916730	6 Gunn	6 Gunn	2017	116.0	None
146143	tt9916754	Chico Albuquerque - Revelações	Chico Albuquerque - Revelações	2013	NaN	Documentary
146144 rows × 6 columns

movie_basics.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 146144 entries, 0 to 146143
Data columns (total 6 columns):
 #   Column           Non-Null Count   Dtype  
---  ------           --------------   -----  
 0   movie_id         146144 non-null  object 
 1   primary_title    146144 non-null  object 
 2   original_title   146123 non-null  object 
 3   start_year       146144 non-null  int64  
 4   runtime_minutes  114405 non-null  float64
 5   genres           140736 non-null  object 
dtypes: float64(1), int64(1), object(4)
memory usage: 6.7+ MB
movie_basics.dropna(subset= 'original_title', inplace = True)
movie_basics.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 146123 entries, 0 to 146143
Data columns (total 6 columns):
 #   Column           Non-Null Count   Dtype  
---  ------           --------------   -----  
 0   movie_id         146123 non-null  object 
 1   primary_title    146123 non-null  object 
 2   original_title   146123 non-null  object 
 3   start_year       146123 non-null  int64  
 4   runtime_minutes  114401 non-null  float64
 5   genres           140734 non-null  object 
dtypes: float64(1), int64(1), object(4)
memory usage: 7.8+ MB
movie_basics.dropna(subset= 'genres', inplace = True)
movie_basics.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 140734 entries, 0 to 146143
Data columns (total 6 columns):
 #   Column           Non-Null Count   Dtype  
---  ------           --------------   -----  
 0   movie_id         140734 non-null  object 
 1   primary_title    140734 non-null  object 
 2   original_title   140734 non-null  object 
 3   start_year       140734 non-null  int64  
 4   runtime_minutes  112232 non-null  float64
 5   genres           140734 non-null  object 
dtypes: float64(1), int64(1), object(4)
memory usage: 7.5+ MB
movie_basics = movie_basics.dropna()
movie_basics.info()
​
<class 'pandas.core.frame.DataFrame'>
Int64Index: 112232 entries, 0 to 146139
Data columns (total 6 columns):
 #   Column           Non-Null Count   Dtype  
---  ------           --------------   -----  
 0   movie_id         112232 non-null  object 
 1   primary_title    112232 non-null  object 
 2   original_title   112232 non-null  object 
 3   start_year       112232 non-null  int64  
 4   runtime_minutes  112232 non-null  float64
 5   genres           112232 non-null  object 
dtypes: float64(1), int64(1), object(4)
memory usage: 6.0+ MB
Checking for any duplicates

​
movie_basics.duplicated().sum()
0
2.3 Checking for missing values in movie_ratings

movie_ratings
movie_id	averagerating	numvotes
0	tt10356526	8.3	31
1	tt10384606	8.9	559
2	tt1042974	6.4	20
3	tt1043726	4.2	50352
4	tt1060240	6.5	21
...	...	...	...
73851	tt9805820	8.1	25
73852	tt9844256	7.5	24
73853	tt9851050	4.7	14
73854	tt9886934	7.0	5
73855	tt9894098	6.3	128
73856 rows × 3 columns

movie_ratings.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 73856 entries, 0 to 73855
Data columns (total 3 columns):
 #   Column         Non-Null Count  Dtype  
---  ------         --------------  -----  
 0   movie_id       73856 non-null  object 
 1   averagerating  73856 non-null  float64
 2   numvotes       73856 non-null  int64  
dtypes: float64(1), int64(1), object(1)
memory usage: 1.7+ MB
Checking for duplicates in movie_rating

movie_ratings.duplicated().sum()
0
Merging the tables using movie_id column

movies_reviews_and_rating = movie_basics.merge(movie_ratings, on='movie_id').merge(movies_df, left_on='primary_title', right_on='title')
movies_reviews_and_rating
movie_id	primary_title	original_title	start_year	runtime_minutes	genres	averagerating	numvotes	title	studio	domestic_gross	foreign_gross	year
0	tt0315642	Wazir	Wazir	2016	103.0	Action,Crime,Drama	7.1	15378	Wazir	Relbig.	1100000.0	18700000.0	2016
1	tt0337692	On the Road	On the Road	2012	124.0	Adventure,Drama,Romance	6.1	37886	On the Road	IFC	744000.0	8000000.0	2012
2	tt4339118	On the Road	On the Road	2014	89.0	Drama	6.0	6	On the Road	IFC	744000.0	8000000.0	2012
3	tt5647250	On the Road	On the Road	2016	121.0	Drama	5.7	127	On the Road	IFC	744000.0	8000000.0	2012
4	tt0359950	The Secret Life of Walter Mitty	The Secret Life of Walter Mitty	2013	114.0	Adventure,Comedy,Drama	7.3	275300	The Secret Life of Walter Mitty	Fox	58200000.0	129900000.0	2013
...	...	...	...	...	...	...	...	...	...	...	...	...	...
2947	tt8331988	The Chambermaid	La camarista	2018	102.0	Drama	7.1	147	The Chambermaid	FM	300.0	18700000.0	2015
2948	tt8404272	How Long Will I Love U	Chao shi kong tong ju	2018	101.0	Romance	6.5	607	How Long Will I Love U	WGUSA	747000.0	82100000.0	2018
2949	tt8427036	Helicopter Eela	Helicopter Eela	2018	135.0	Drama	5.4	673	Helicopter Eela	Eros	72000.0	18700000.0	2018
2950	tt9078374	Last Letter	Ni hao, Zhihua	2018	114.0	Drama,Romance	6.4	322	Last Letter	CL	181000.0	18700000.0	2018
2951	tt9151704	Burn the Stage: The Movie	Burn the Stage: The Movie	2018	84.0	Documentary,Music	8.8	2067	Burn the Stage: The Movie	Trafalgar	4200000.0	16100000.0	2018
2952 rows × 13 columns

#drop all duplicates
movies_reviews_and_rating = movies_reviews_and_rating.drop(columns=['original_title','title'])
movies_reviews_and_rating
movie_id	primary_title	start_year	runtime_minutes	genres	averagerating	numvotes	studio	domestic_gross	foreign_gross	year
0	tt0315642	Wazir	2016	103.0	Action,Crime,Drama	7.1	15378	Relbig.	1100000.0	18700000.0	2016
1	tt0337692	On the Road	2012	124.0	Adventure,Drama,Romance	6.1	37886	IFC	744000.0	8000000.0	2012
2	tt4339118	On the Road	2014	89.0	Drama	6.0	6	IFC	744000.0	8000000.0	2012
3	tt5647250	On the Road	2016	121.0	Drama	5.7	127	IFC	744000.0	8000000.0	2012
4	tt0359950	The Secret Life of Walter Mitty	2013	114.0	Adventure,Comedy,Drama	7.3	275300	Fox	58200000.0	129900000.0	2013
...	...	...	...	...	...	...	...	...	...	...	...
2947	tt8331988	The Chambermaid	2018	102.0	Drama	7.1	147	FM	300.0	18700000.0	2015
2948	tt8404272	How Long Will I Love U	2018	101.0	Romance	6.5	607	WGUSA	747000.0	82100000.0	2018
2949	tt8427036	Helicopter Eela	2018	135.0	Drama	5.4	673	Eros	72000.0	18700000.0	2018
2950	tt9078374	Last Letter	2018	114.0	Drama,Romance	6.4	322	CL	181000.0	18700000.0	2018
2951	tt9151704	Burn the Stage: The Movie	2018	84.0	Documentary,Music	8.8	2067	Trafalgar	4200000.0	16100000.0	2018
2952 rows × 11 columns

movies_reviews_and_rating.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 2952 entries, 0 to 2951
Data columns (total 11 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   movie_id         2952 non-null   object 
 1   primary_title    2952 non-null   object 
 2   start_year       2952 non-null   int64  
 3   runtime_minutes  2952 non-null   float64
 4   genres           2952 non-null   object 
 5   averagerating    2952 non-null   float64
 6   numvotes         2952 non-null   int64  
 7   studio           2952 non-null   object 
 8   domestic_gross   2952 non-null   float64
 9   foreign_gross    2952 non-null   float64
 10  year             2952 non-null   int64  
dtypes: float64(4), int64(3), object(4)
memory usage: 276.8+ KB

### 3. EXPLORATORY DATA ANALYSIS
List of top_ten movies based on numvotes and averagerating columns

movies_reviews_and_rating
movie_id	primary_title	start_year	runtime_minutes	genres	averagerating	numvotes	studio	domestic_gross	foreign_gross	year
0	tt0315642	Wazir	2016	103.0	Action,Crime,Drama	7.1	15378	Relbig.	1100000.0	18700000.0	2016
1	tt0337692	On the Road	2012	124.0	Adventure,Drama,Romance	6.1	37886	IFC	744000.0	8000000.0	2012
2	tt4339118	On the Road	2014	89.0	Drama	6.0	6	IFC	744000.0	8000000.0	2012
3	tt5647250	On the Road	2016	121.0	Drama	5.7	127	IFC	744000.0	8000000.0	2012
4	tt0359950	The Secret Life of Walter Mitty	2013	114.0	Adventure,Comedy,Drama	7.3	275300	Fox	58200000.0	129900000.0	2013
...	...	...	...	...	...	...	...	...	...	...	...
2947	tt8331988	The Chambermaid	2018	102.0	Drama	7.1	147	FM	300.0	18700000.0	2015
2948	tt8404272	How Long Will I Love U	2018	101.0	Romance	6.5	607	WGUSA	747000.0	82100000.0	2018
2949	tt8427036	Helicopter Eela	2018	135.0	Drama	5.4	673	Eros	72000.0	18700000.0	2018
2950	tt9078374	Last Letter	2018	114.0	Drama,Romance	6.4	322	CL	181000.0	18700000.0	2018
2951	tt9151704	Burn the Stage: The Movie	2018	84.0	Documentary,Music	8.8	2067	Trafalgar	4200000.0	16100000.0	2018
2952 rows × 11 columns

#select the required columns from the movie_reviews_and rating table 
movie_genres_ratings = movies_reviews_and_rating[['movie_id','runtime_minutes','genres','averagerating','numvotes','studio','year']]
movie_genres_ratings
movie_id	runtime_minutes	genres	averagerating	numvotes	studio	year
0	tt0315642	103.0	Action,Crime,Drama	7.1	15378	Relbig.	2016
1	tt0337692	124.0	Adventure,Drama,Romance	6.1	37886	IFC	2012
2	tt4339118	89.0	Drama	6.0	6	IFC	2012
3	tt5647250	121.0	Drama	5.7	127	IFC	2012
4	tt0359950	114.0	Adventure,Comedy,Drama	7.3	275300	Fox	2013
...	...	...	...	...	...	...	...
2947	tt8331988	102.0	Drama	7.1	147	FM	2015
2948	tt8404272	101.0	Romance	6.5	607	WGUSA	2018
2949	tt8427036	135.0	Drama	5.4	673	Eros	2018
2950	tt9078374	114.0	Drama,Romance	6.4	322	CL	2018
2951	tt9151704	84.0	Documentary,Music	8.8	2067	Trafalgar	2018
2952 rows × 7 columns

#get a list of the top ten movie genres based on average rating
Top_ten_movie_genre = movie_genres_ratings[['movie_id','genres','runtime_minutes','averagerating','studio','year']].sort_values(by = 'averagerating',ascending=False)
Top_ten_movie_genre.head(10)
movie_id	genres	runtime_minutes	averagerating	studio	year
171	tt6168914	Adventure	108.0	9.2	App.	2010
626	tt1455256	Documentary	78.0	9.2	RAtt.	2017
2029	tt2831326	Drama	115.0	9.1	UTMW	2017
607	tt6216234	Documentary	85.0	9.0	PDA	2011
1135	tt1744662	Comedy,Documentary,Drama	68.0	8.9	WGUSA	2017
2951	tt9151704	Documentary,Music	84.0	8.8	Trafalgar	2018
485	tt1375666	Action,Adventure,Sci-Fi	148.0	8.8	WB	2010
795	tt1576702	Documentary	78.0	8.8	Magn.	2015
480	tt3313066	Drama,History,War	192.0	8.7	Wein.	2012
1047	tt1692325	Documentary,History	110.0	8.7	NAV	2010
#get the top_ten movie genres based on number of votes
movie_genres_votes = movie_genres_ratings[['movie_id', 'genres','runtime_minutes','numvotes','studio']].sort_values(by='numvotes', ascending=False)
movie_genres_votes.head(10)
movie_id	genres	runtime_minutes	numvotes	studio
485	tt1375666	Action,Adventure,Sci-Fi	148.0	1841066	WB
456	tt1345836	Action,Thriller	164.0	1387769	WB
101	tt0816692	Adventure,Drama,Sci-Fi	169.0	1299334	Par.
1296	tt1853728	Drama,Western	165.0	1211405	Wein.
162	tt0993846	Biography,Crime,Drama	180.0	1035358	Par.
236	tt1130884	Mystery,Thriller	138.0	1005960	Par.
1476	tt2015381	Action,Adventure,Comedy	121.0	948394	BV
574	tt1431045	Action,Adventure,Comedy	108.0	820847	Fox
501	tt1392170	Action,Adventure,Sci-Fi	142.0	795227	LGF
502	tt1392190	Action,Adventure,Sci-Fi	120.0	780910	WB
Evaluate the gross collected from the top movie genres

Top_ten_movie_grosscollected = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='averagerating', ascending=False)
Top_ten_movie_grosscollected.head(10)
#domestic and foreign gross collected from the top ten movie genres
Top_ten_movie_grosscollected = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='averagerating', ascending=False)
Top_ten_movie_grosscollected.head(10)
genres	averagerating	studio	domestic_gross	foreign_gross	year
171	Adventure	9.2	App.	3600000.0	1100000.0	2010
626	Documentary	9.2	RAtt.	1800000.0	2700000.0	2017
2029	Drama	9.1	UTMW	62500.0	18700000.0	2017
607	Documentary	9.0	PDA	4400000.0	18700000.0	2011
1135	Comedy,Documentary,Drama	8.9	WGUSA	96800.0	18700000.0	2017
2951	Documentary,Music	8.8	Trafalgar	4200000.0	16100000.0	2018
485	Action,Adventure,Sci-Fi	8.8	WB	292600000.0	535700000.0	2010
795	Documentary	8.8	Magn.	1200.0	18700000.0	2015
480	Drama,History,War	8.7	Wein.	757000.0	315000.0	2012
1047	Documentary,History	8.7	NAV	26300.0	250000.0	2010
domestic_gross
Top_ten_movie_grosscollected2 = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='domestic_gross', ascending=False)
Top_ten_movie_grosscollected2.head(10)
genres	averagerating	studio	domestic_gross	foreign_gross	year
1247	Action,Adventure,Sci-Fi	7.3	BV	700100000.0	646900000.0	2018
2465	Action,Adventure,Sci-Fi	8.5	BV	678800000.0	1369.5	2018
6	Action,Adventure,Sci-Fi	7.0	Uni.	652300000.0	1019.4	2015
1917	Action,Adventure,Fantasy	7.1	BV	620200000.0	712400000.0	2017
2297	Action,Adventure,Animation	7.7	BV	608600000.0	634200000.0	2018
2353	Action,Adventure,Sci-Fi	7.8	BV	532200000.0	523900000.0	2016
1713	Adventure,Animation,Comedy	7.3	BV	486300000.0	542300000.0	2016
1840	Action,Adventure,Sci-Fi	7.3	BV	459000000.0	946400000.0	2015
456	Action,Thriller	8.4	WB	448100000.0	636800000.0	2012
1404	Action,Adventure,Sci-Fi	7.5	LGF	424700000.0	440300000.0	2013
#calculating the total for the movie_genres
Top_ten_movie_grosscollected['total'] = Top_ten_movie_grosscollected['foreign_gross'] + Top_ten_movie_grosscollected['domestic_gross']
Top_ten_movie_grosscollected.head(10)
genres	averagerating	studio	domestic_gross	foreign_gross	year	total
171	Adventure	9.2	App.	3600000.0	1100000.0	2010	4700000.0
626	Documentary	9.2	RAtt.	1800000.0	2700000.0	2017	4500000.0
2029	Drama	9.1	UTMW	62500.0	18700000.0	2017	18762500.0
607	Documentary	9.0	PDA	4400000.0	18700000.0	2011	23100000.0
1135	Comedy,Documentary,Drama	8.9	WGUSA	96800.0	18700000.0	2017	18796800.0
2951	Documentary,Music	8.8	Trafalgar	4200000.0	16100000.0	2018	20300000.0
485	Action,Adventure,Sci-Fi	8.8	WB	292600000.0	535700000.0	2010	828300000.0
795	Documentary	8.8	Magn.	1200.0	18700000.0	2015	18701200.0
480	Drama,History,War	8.7	Wein.	757000.0	315000.0	2012	1072000.0
1047	Documentary,History	8.7	NAV	26300.0	250000.0	2010	276300.0
_gross
Top_ten_movie_grosscollected3 = movies_reviews_and_rating[['genres','averagerating','studio','domestic_gross','foreign_gross','year']].sort_values(by='foreign_gross', ascending=False)
Top_ten_movie_grosscollected3.head(10)
genres	averagerating	studio	domestic_gross	foreign_gross	year
1840	Action,Adventure,Sci-Fi	7.3	BV	459000000.0	946400000.0	2015
2630	Action,Adventure,Sci-Fi	6.2	Uni.	417700000.0	891800000.0	2018
434	Adventure,Animation,Comedy	7.5	BV	400700000.0	875700000.0	2013
433	Fantasy,Romance	5.4	BV	400700000.0	875700000.0	2013
432	Adventure,Drama,Sport	6.2	BV	400700000.0	875700000.0	2013
2905	Action,Drama,Thriller	6.2	HC	2700000.0	867600000.0	2017
1562	Action,Adventure,Sci-Fi	5.7	Par.	245400000.0	858600000.0	2014
1724	Adventure,Animation,Comedy	6.4	Uni.	336000000.0	823400000.0	2015
662	Action,Adventure,Fantasy	7.1	WB	335100000.0	812700000.0	2018
399	Action,Adventure,Sci-Fi	7.2	BV	409000000.0	805800000.0	2013
#using statistical method to get the mean, median, IQR, standard deviation of the movie_reviews_and ratings
movie_stat_summary = movies_reviews_and_rating[['runtime_minutes','domestic_gross','foreign_gross']].describe()
movie_stat_summary
runtime_minutes	domestic_gross	foreign_gross
count	2952.000000	2.952000e+03	2.952000e+03
mean	107.305894	3.068027e+07	5.515085e+07
std	20.043335	6.708970e+07	1.126142e+08
min	3.000000	1.000000e+02	6.000000e+02
25%	94.000000	1.367500e+05	1.405000e+07
50%	105.000000	2.000000e+06	1.870000e+07
75%	118.000000	3.242500e+07	3.445000e+07
max	272.000000	7.001000e+08	9.464000e+08

### 4. DATA VISUALIZATION
Representation of the analysis
Top_ten_movie_genre
movie_id	genres	runtime_minutes	averagerating	studio	year
171	tt6168914	Adventure	108.0	9.2	App.	2010
626	tt1455256	Documentary	78.0	9.2	RAtt.	2017
2029	tt2831326	Drama	115.0	9.1	UTMW	2017
607	tt6216234	Documentary	85.0	9.0	PDA	2011
1135	tt1744662	Comedy,Documentary,Drama	68.0	8.9	WGUSA	2017
...	...	...	...	...	...	...
2040	tt3007924	Horror	94.0	1.9	A24	2015
1776	tt2344678	Action,Comedy,Drama	150.0	1.7	UTV	2013
60	tt3746918	Drama	112.0	1.7	WB	2010
1061	tt1702443	Documentary,Music	105.0	1.6	Par.	2011
2927	tt7607940	Comedy,Drama,Romance	141.0	1.6	Eros	2018
2952 rows × 6 columns

10
Top_ten_movie_genre.head(10)
movie_id	genres	runtime_minutes	averagerating	studio	year
171	tt6168914	Adventure	108.0	9.2	App.	2010
626	tt1455256	Documentary	78.0	9.2	RAtt.	2017
2029	tt2831326	Drama	115.0	9.1	UTMW	2017
607	tt6216234	Documentary	85.0	9.0	PDA	2011
1135	tt1744662	Comedy,Documentary,Drama	68.0	8.9	WGUSA	2017
2951	tt9151704	Documentary,Music	84.0	8.8	Trafalgar	2018
485	tt1375666	Action,Adventure,Sci-Fi	148.0	8.8	WB	2010
795	tt1576702	Documentary	78.0	8.8	Magn.	2015
480	tt3313066	Drama,History,War	192.0	8.7	Wein.	2012
1047	tt1692325	Documentary,History	110.0	8.7	NAV	2010
Top_ten_movie_genre[['movie_id', 'genres','runtime_minutes','averagerating']].head(10)
Top_ten_movie_genre[['movie_id', 'genres','runtime_minutes','averagerating']].head(10)
movie_id	genres	runtime_minutes	averagerating
171	tt6168914	Adventure	108.0	9.2
626	tt1455256	Documentary	78.0	9.2
2029	tt2831326	Drama	115.0	9.1
607	tt6216234	Documentary	85.0	9.0
1135	tt1744662	Comedy,Documentary,Drama	68.0	8.9
2951	tt9151704	Documentary,Music	84.0	8.8
485	tt1375666	Action,Adventure,Sci-Fi	148.0	8.8
795	tt1576702	Documentary	78.0	8.8
480	tt3313066	Drama,History,War	192.0	8.7
1047	tt1692325	Documentary,History	110.0	8.7
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
genres	averagerating	studio	domestic_gross	foreign_gross	year	total
171	Adventure	9.2	App.	3600000.0	1100000.0	2010	4700000.0
626	Documentary	9.2	RAtt.	1800000.0	2700000.0	2017	4500000.0
2029	Drama	9.1	UTMW	62500.0	18700000.0	2017	18762500.0
607	Documentary	9.0	PDA	4400000.0	18700000.0	2011	23100000.0
1135	Comedy,Documentary,Drama	8.9	WGUSA	96800.0	18700000.0	2017	18796800.0
...	...	...	...	...	...	...	...
2040	Horror	1.9	A24	8400000.0	18700000.0	2015	27100000.0
1776	Action,Comedy,Drama	1.7	UTV	271000.0	18700000.0	2013	18971000.0
60	Drama	1.7	WB	23600000.0	5800000.0	2010	29400000.0
1061	Documentary,Music	1.6	Par.	73000000.0	26000000.0	2011	99000000.0
2927	Comedy,Drama,Romance	1.6	Eros	104000.0	18700000.0	2018	18804000.0
2952 rows × 7 columns

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
movie_id	genres	runtime_minutes	numvotes	studio
485	tt1375666	Action,Adventure,Sci-Fi	148.0	1841066	WB
456	tt1345836	Action,Thriller	164.0	1387769	WB
101	tt0816692	Adventure,Drama,Sci-Fi	169.0	1299334	Par.
1296	tt1853728	Drama,Western	165.0	1211405	Wein.
162	tt0993846	Biography,Crime,Drama	180.0	1035358	Par.
236	tt1130884	Mystery,Thriller	138.0	1005960	Par.
1476	tt2015381	Action,Adventure,Comedy	121.0	948394	BV
574	tt1431045	Action,Adventure,Comedy	108.0	820847	Fox
501	tt1392170	Action,Adventure,Sci-Fi	142.0	795227	LGF
502	tt1392190	Action,Adventure,Sci-Fi	120.0	780910	WB
Number of Votes BarCart
# Plot barchart of the number of votes of the top ten genres 
x = movie_genres_votes['genres'].head(10)
y = movie_genres_votes['numvotes'].head(10)
​
plt.figure(figsize=(10,5))
plt.barh(x, y)
plt.xlabel('Genre')
plt.ylabel('Number of Votes')
plt.title('Number of Votes Barchart')
plt.show();
​
​
​
​

From the graph above showing comparison of the top ten genres and the number of votes received, we see that the Action,Adventure and Sci-Fi have the highest number of votes

Top_ten_movie_grosscollected.head(10)
genres	averagerating	studio	domestic_gross	foreign_gross	year	total
171	Adventure	9.2	App.	3600000.0	1100000.0	2010	4700000.0
626	Documentary	9.2	RAtt.	1800000.0	2700000.0	2017	4500000.0
2029	Drama	9.1	UTMW	62500.0	18700000.0	2017	18762500.0
607	Documentary	9.0	PDA	4400000.0	18700000.0	2011	23100000.0
1135	Comedy,Documentary,Drama	8.9	WGUSA	96800.0	18700000.0	2017	18796800.0
2951	Documentary,Music	8.8	Trafalgar	4200000.0	16100000.0	2018	20300000.0
485	Action,Adventure,Sci-Fi	8.8	WB	292600000.0	535700000.0	2010	828300000.0
795	Documentary	8.8	Magn.	1200.0	18700000.0	2015	18701200.0
480	Drama,History,War	8.7	Wein.	757000.0	315000.0	2012	1072000.0
1047	Documentary,History	8.7	NAV	26300.0	250000.0	2010	276300.0
Plotting a scatter plot to show the relationship between average rating and the total gross received
#plotting a scatter plot diagram for average rating and total gross 
x = Top_ten_movie_grosscollected['averagerating'].head(100)
y = Top_ten_movie_grosscollected['total'].head(100)
​
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
​
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



