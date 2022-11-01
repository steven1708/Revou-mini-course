# Revou-mini-course
```
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
```



`movie=pd.read_csv(r'C:\Users\steven wijaya\Desktop\movie_data.csv',delimiter=';')`



`print(movie.head())`



#renaming unconsistent column name

```
movie.columns = ['director_name', 'duration', 'gross', 'genres', 'movie_title','title_year','language','country','budget','imdb_score','actor','movie_facebook_likes']

print(movie)
```




#checking the NA in dataset
```
print(movie.isna().sum())
print(movie.info())

```


#drop na for title_year and change to int

```
movie=movie.dropna(subset="title_year")
movie["title_year"]=movie["title_year"].astype(int)

print(movie.isna().sum())
print(movie.info())
print(movie.head())

```



#make genre sparepartly
```
genre1= []
genre2= []
genre3= []
genre4= []
genre5= []

for row in movie["genres"]:
    x=row.split("|")
    n_element= len(x)
    if n_element == 5:
        genre5.append(x[4])
        genre4.append(x[3])
        genre3.append(x[2])
        genre2.append(x[1])
        genre1.append(x[0])
    elif n_element == 4:
        genre5.append(np.nan)
        genre4.append(x[3])
        genre3.append(x[2])
        genre2.append(x[1])
        genre1.append(x[0])
    elif n_element == 3:
        genre5.append(np.nan)
        genre4.append(np.nan)
        genre3.append(x[2])
        genre2.append(x[1])
        genre1.append(x[0])
    elif n_element == 2:
        genre5.append(np.nan)
        genre4.append(np.nan)
        genre3.append(np.nan)
        genre2.append(x[1])
        genre1.append(x[0])
    elif n_element == 1:
        genre5.append(np.nan)
        genre4.append(np.nan)
        genre3.append(np.nan)
        genre2.append(np.nan)
        genre1.append(x[0])
    else :
        genre5.append(np.nan)
        genre4.append(np.nan)
        genre3.append(np.nan)
        genre2.append(np.nan)
        genre1.append(np.nan)

print("done")

movie.insert(4,"genre_1",genre1)
movie.insert(5,"genre_2",genre2)
movie.insert(6,"genre_3",genre3)
movie.insert(7,"genre_4",genre4)
movie.insert(8,"genre_5",genre5)

movie=movie.drop(columns="genres")

print("done")
print(movie.head())
```




#cleaning format with space in right movie title
```
movie_title= movie['movie_title'].str.rstrip()
movie["movie_title"]=movie_title
print(movie)
```



#check duplicate movie title
```
duplicate= movie.duplicated(subset='movie_title',keep=False)
duplicate_title=movie[duplicate].sort_values('movie_title')
print(duplicate_title["movie_title"])
```



#select distinct
```
movie=movie.drop_duplicates(subset='movie_title',inplace=False)
print(movie)
```

#data reduce 5043 to 4916




#cleaning :check the imdb_score is any higher than 10
```
movie_error=movie[movie["imdb_score"]>10.0]
print(movie_error)

```
#the data is correct for imdb_score



# how the imdb_score growing from 2000 to 2016

```
movie_score= movie.groupby("title_year",as_index=False)["imdb_score"].mean()
movie_score=movie_score.sort_values("title_year")
movie_score=movie_score[movie_score["title_year"]>2006]
print(movie_score)
```

#Search average imdb fromm all year
```
imdb_avg=movie["imdb_score"].mean()
print(imdb_avg)
```

#visualization
```
movie["movie_title"]=movie["movie_title"].astype(str)
ax=sns.lineplot(data=movie_score,x="title_year",y="imdb_score",marker="o",palette="hot")
ax.set(xlabel="Year", ylabel="average  imdb score", title= "Imdb Trend last ten year")
plt.axhline(y=imdb_avg,color="black",label="imdb median all year",linestyle="--",linewidth=3)
plt.xticks(rotation=60)
plt.show()
```




```
movie_plot=movie[movie["title_year"]>=2006]
movie_plot=movie_plot.sort_values("title_year")

print(movie_plot)

ax1=sns.catplot(data=movie_plot,x="title_year",y="imdb_score",kind='box')
ax1.set(xlabel="Year", ylabel="average  imdb score", title= "Imdb Trend each year from 2006 to 2016")
plt.xticks(rotation=60)
plt.axhline(y=imdb_avg,color="black",label="imdb median all year",linestyle="--",linewidth=3)
plt.show()

```



# search how many movie under the imdb average from 2006 and 2016
```
movie_filt1=movie[(movie["title_year"]>2007) & (movie["imdb_score"]>imdb_avg)]
movie_filt2=movie[movie["title_year"]>2007]
movie_count1=movie_filt1["title_year"].value_counts(sort=True)
movie_count2=movie_filt2["title_year"].value_counts(sort=True)
movie_percentage=round(100-(100*movie_count1/movie_count2),2)
print(movie_percentage)
```




# what makes the movie get a good imdb score

# actor

```
movie_overavg = movie[movie["imdb_score"]>imdb_avg]

movie_actor= movie_overavg.groupby("actor",as_index=False)[["imdb_score","movie_title"]].agg({"imdb_score":"mean","movie_title":"count"})
movie_actor=movie_actor.sort_values("movie_title",ascending=False)
print(movie_actor.head(10))

plot1=sns.scatterplot(data=movie_actor, y="movie_title", x="imdb_score")
plot1.set(xlabel="imdb Score", ylabel="Total Movies", title= "Spread of imdb Score vs Total Movies")
plot1.set_style("darkgrid")
plt.show()

```

#director
```
movie_direc= movie_overavg.groupby("director_name",as_index=False)[["imdb_score","movie_title"]].agg({"imdb_score":"mean","movie_title":"count"})
movie_direc=movie_direc.sort_values(["movie_title","imdb_score"],ascending=False)
print(movie_direc.head(20))

plot2=sns.scatterplot(data=movie_direc, y="movie_title", x="imdb_score")
plot2.set(xlabel="imdb Score", ylabel="Total Movies", title= "Spread of imdb Score vs Total Movies")
plot2.set_style("darkgrid")
plt.show()
```




#budget
```
movie_budget= movie_overavg.groupby("budget",as_index=False)[["imdb_score","movie_title"]].agg({"imdb_score":"mean","movie_title":"count"})
movie_budget=movie_budget.sort_values(["imdb_score",'budget'],ascending=False)
print(movie_budget.head(20))

```



#genre
```
genre1= movie_overavg.groupby('genre_1',as_index=False)['movie_title'].count()
genre1.columns=['genre','total movie']
genre1=genre1.set_index('genre')


genre2= movie_overavg.groupby('genre_2',as_index=False)['movie_title'].count()
genre2.columns=['genre','total movie']
genre2=genre2.set_index('genre')


genre3= movie_overavg.groupby('genre_3',as_index=False)['movie_title'].count()
genre3.columns=['genre','total movie']
genre3=genre3.set_index('genre')

genre4= movie_overavg.groupby('genre_4',as_index=False)['movie_title'].count()
genre4.columns=['genre','total movie']
genre4=genre4.set_index('genre')

genre5= movie_overavg.groupby('genre_5',as_index=False)['movie_title'].count()
genre5.columns=['genre','total movie']
genre5=genre5.set_index('genre')

genres=[genre1,genre2,genre3,genre4,genre5]
result=pd.concat(genres)
total_genre = result.groupby('genre')['total movie'].sum().reset_index()
total_genre=total_genre.sort_values('total movie',ascending=False)
print(total_genre.head(10))

genreplot=sns.barplot(data=total_genre.head(10), x='genre',y='total movie')
genreplot.set(xlabel='Type genre', ylabel='Total Movie', title='Top 10 Most Genres Above Average Imdb')
sns.set_palette('twilight_shifted')
plt.xticks(rotation=60)
plt.show()
```

