# Web-Scrapping on Movie Recomendations 
Extracted the data from IMDB website by using python libraries like Beautiful Soup
After extracted the data that data should be imported to MS Excel for the better importation to the jupiter notebook
Analysis should be done by using python libraries like pandas ,numpy ,matplotlib ,seaborn ....etc






# Extracted data from IMDB website 

## Importing the required libraries

import requests
from bs4 import BeautifulSoup
import re
import pandas as pd

### Extracting the data from the url

url='https://www.imdb.com/list/ls000634294/?sort=moviemeter,asc&st_dt=&mode=detail&page='


urlss=[]
for i in range(6):
    urlss.append(url+str(i+1))

urlss

movie_name4 = []
year_relase4 = []
rating4 = []
collection4 = []
gener4 = []
director4 =[]
certificate4 = []
votes4 = []
duriation4 =[]

for i in urlss:
    page = requests.get(i)
    soup = BeautifulSoup(page.text)
    
    
    for i in soup.find_all('h3',class_='lister-item-header'):
        movie_name4.append(i.find_all('a')[0].text)
        
        
   

    for i in soup.find_all('span',class_='lister-item-year text-muted unbold'):
        a_=i.text.split('(')[-1].split(')')[0]
        year_relase4.append((a_))
        
    

    for i in soup.find_all('div',class_='ipl-rating-star small'):
        b_=i.text.replace('\n','')
        rating4.append(float(b_))
        
        
    for i in range(2,300,3):
        a_a=soup.find_all('p',class_='text-muted text-small')[i].find_all('span')[-1].text
        collection4.append(a_a.split('$')[-1].split('M')[0])
        
    
    for i in soup.find_all("p",{"class":"text-muted text-small"}):
        w = i.find_all("span",{"class":"genre"})
        for i in w:
            for j in i:
                gener4.append(j.strip())
                
    
    for y in range(1,300,3):
        director4.append(soup.find_all('p',{"class":"text-muted text-small"})[y].find_all("a")[0].text)
        
    
    for i in range(2,300,3):
        a_a = soup.find_all('p',class_='text-muted text-small')[i].find_all('span')[1].text
        votes4.append(a_a)
        
    
    for i in soup.find_all('span',class_="runtime"):
        d_3=i.text.split()[0]
        duriation4.append(int(d_3))

# Creation of the data frame 



IMBD = pd.DataFrame({
    'Title':movie_name4,
    'Release_Year':year_relase4,
    'Duration':duriation4,
    'Collection':collection4,
    'Genre':gener4,
    'Director':director4,
    'Rating':rating4,
    'Votes':votes4
})

IMBD



IMBD.shape # finding the no:of rows and no:of columns

 The dataFrame consists of 600 rows and 8 columns



## Exporting the data frame into the csv format

IMBD.to_csv('IMBD.csv')



# Analysing the data 

## importing Libraries

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter



## importing the data from the excel

data = pd.read_csv('IMBD.csv')

data





## Data Cleaning



data = data.drop([0])     # dropping abnormal row

data



# finding the null values present in the dataset
#  - is na()
#  - is null()


data.isna().sum()

data.isnull().sum()



## find any duplicate values present in the data

data[data.duplicated()]



## Data Manupulating





data.info()

**The most of the columns are in the object/ string format, here we are converting the data type of the columns**



### Changing the data type for the columns

COLLECTION = []
vote = []
release=[]

for i in data['Votes']:
    a = i.split(',')
    b = ''.join(a)
    vote.append(int(b))

    
for i in data['Collection']:
    a  =i.replace(',','.')
    COLLECTION.append(float(a))

    
for i in data['Release_Year']:
    release.append(int(i))

data['Release_Year'] = release
data['Votes'] = vote
data['Collection']= COLLECTION



#### checking the type of the columns



# finding the data type of each column


data.info()

data.describe()









# 1.Univarient Analysis 

data.head(10)





### 1.1  Finding repeating years

## counting the years how many times that they are occured


data['Release_Year'].value_counts()





### counting the repeating years

## counting the years how many times that they are occured
## visualization

plt.figure(figsize=(20,10))
sns.countplot(x = 'Release_Year',data= data)
plt.title ('No:of movies Released')
plt.xticks(rotation = 90)
plt.show()







## 1.2 No:of times the each Genre is Repeating

# counting how many times that the each genre had occured




plt.figure(figsize=(30,15))
sns.countplot(x= 'Genre',data= data)
plt.xticks(rotation= 90)
plt.title('counting No:of films in the genre ')
plt.show()

# counting how many times that the each genre had occured

data['Genre'].value_counts()





## dividing the genre into sub parts

genre_sub = []
genre_cat = []
uniq_genre = []

for i in data['Genre']:
    genre_sub.append(i.split(','))

for i in genre_sub:
    for j in i:
        genre_cat.append(j.replace(" ",''))

for i in genre_cat:
    if i not in uniq_genre:
        uniq_genre.append(i)



uniq_genre



### 1.3 No: of Action movie in the Data

(data[data['Genre'].str.contains('Action')])

### 1.4 No: of Comedy movie in the Data

(data[data['Genre'].str.contains('Comedy')])



### 1.5 No:of Sci-Fi Movie present in the Data

(data[data['Genre'].str.contains('Sci-Fi')])





## 1.6 how many times that each genre had occured

Counter(genre_cat)

gen_cat = pd.DataFrame([genre_cat]).T
gen_cat

gen_cat.value_counts().plot.bar()
plt.title('Counting the Genre')
plt.xlabel('gen_cat')
plt.ylabel('Counting')
plt.show()

### 1.7 No: of movies made by the each  Directors

data['Director'].value_counts()

Counter(data['Director'])





## counting the no:of movies made by the each director

plt.figure(figsize=(50,15))
sns.countplot(x = 'Director',data = data)
plt.xticks(rotation = 90)
plt.title('No:of films made by the director')
plt.show()





## 2 Bivarient Analysis



## 2.1 Displaying the Movie which is more than 150 min

data[data['Duration']>=150]['Title'] . head(10)

## 2.2 In which year the avg highest voting

data.groupby('Release_Year')['Votes'].mean().sort_values(ascending=False)



plt.figure(figsize=(30,10))
sns.barplot(x = 'Release_Year', y = 'Votes',data=data)
plt.title('The avg highest voting')
plt.xticks(rotation = 90)
plt.show()



## 2.3 Highest average Collection year wise 

data.groupby('Release_Year')['Collection'].mean().sort_values(ascending = False)

##  release year vs Collection

plt.figure(figsize = (30,10))
sns.barplot(x = 'Release_Year', y = 'Collection',data = data)
plt.title("Release Year Vs Collection")
plt.show()



## 2.4 Average rating for each director

data.groupby('Director')['Rating'].mean().sort_values(ascending = False)



## 2.5 Top 14 lengthy movies  and its Duration

top_14=data.nlargest(14,'Duration')[['Title','Duration']].set_index('Title')
top_14



sns.barplot(x = 'Duration',y = top_14.index,data = top_14)
plt.show()



## 2.6 Top 14 highest movie collections

top_14_colle = data.nlargest(14,'Collection')[['Title','Collection']].set_index('Title')
top_14_colle

sns.barplot(x= 'Collection',y =top_14_colle.index,data=top_14_colle)
plt.title('Top 14 Highest Movie Collections')
plt.show()

## 2.7 Most popular movies according to collection

data[data['Collection'].max()==data['Collection']]['Title']







## 2.8 Average rating for  movies Year wise


data.groupby('Release_Year')['Rating'].mean().sort_values(ascending = False).head(14)



## 2.9 Does Rating Is Effecting Collections

plt.figure(figsize=(15,5))
sns.scatterplot(x= 'Rating', y = 'Collection',data = data)
plt.show()



 





## 3 MULTI VARIENT ANALYSIS



### 3.1 Display top 14 highest rated movies with its directors



top_high_rated_titles=data.nlargest(14,'Rating')[['Title','Rating','Director']].set_index('Title')
top_high_rated_titles



sns.barplot(x = 'Rating',y = top_high_rated_titles.index,data=top_high_rated_titles,hue='Director',dodge=False)
plt.legend(bbox_to_anchor=(1,1),loc= 2 )
plt.title('Top 14 Highly Rated movies with Directors')
plt.show()

### Finding the corellation between complete data

data.corr()



sns.heatmap(data.corr(),annot = True)



## Senerio

#### senerio-1

data[(data['Duration'] >= 150) & (data['Rating'] >= 7) & data['Director'].str.contains('James Cameron') & (data['Genre'].str.contains('Adventure'))]



#### senerio - 2

data[(data['Duration']>60) & (data['Rating']>4 ) & (data['Release_Year']==2011)]







# Thank_You 





