# Data Modeling with Postgres

## 1. Discuss the purpose of this database in the context of the startup, Sparkify, and their analytical goals.

Sparkify has a music streaming application and would like to get a better understanding of what type of songs their customers are listening to. Because the data is in many separate JSON files it is difficult if not impossible to gather meaningful data. By creating a database that holds all of this data in one place and can be easily queried Sparkify can quickly analyze their data to understand trends in song plays and better serve their app users.

## 2. State and justify your database schema design and ETL pipeline.

The database is designed using a relational database in [star schema](https://en.wikipedia.org/wiki/Star_schema) which will have a central fact table to hold metrics and dimension tables branching from the fact table that describe data. This schema was selected because it is well suited for running different queries and aggregating data which is critical for the type of analytics that Sparkify is looking to perform.

## 3. [Optional] Provide example queries and results for song play analysis.

The first query that comes to mind would be to see how many times each song is played. Unfortunately, the data we're given from the song play logs only matches one song in the song data so there is only one result returned.

```sql
SELECT title, count(*) times_played FROM songs 
    JOIN songplays ON songs.song_id=songplays.song_id 
    GROUP BY songs.song_id;
```

|title|times_played|
|-----|------------|
| Setanta matins | 1 |

This could be extended to see which artist is played the most so Sparkify would know that adding more songs from that artist would be a popular move. Unfortunately, we're again limited by the sample log data set not aligning with the sample song data set.

```sql
SELECT artists.name, count(*) times_played FROM songs 
    JOIN songplays ON songs.song_id=songplays.song_id 
    JOIN artists ON artists.artist_id=songs.artist_id 
    GROUP BY artists.name;
```

|artist|times_played|
|------|------------|
|Elena|1|

And then we could look at how many songs we already have in the library from each artist.

```sql
SELECT artists.name, count(*) number_of_songs FROM songs 
    JOIN artists ON songs.artist_id=artists.artist_id 
    GROUP BY artists.name 
    ORDER BY number_of_songs DESC 
    LIMIT 5;
```

|name 	|number_of_songs|
|-------|---------------|
| Clp | 2 |
| Casual | 2 |
| Jeff And Sheri Easter | 1 |
| King Curtis | 1 |
| Jimmy Wakely | 1 |

Another useful query would be to see which users are streaming the most songs and what type of membership they have. This could help Sparkify find users who are playing a lot of songs on the free account and could benefit from a paid account. They could then reach out to the user to let them know the advantages of upgrading.

```sql
%sql SELECT users.user_id, concat_ws(' ', users.first_name, users.last_name) 
    AS user_name, users.level, count(*) number_of_streams FROM songplays 
    JOIN users ON users.user_id=songplays.user_id 
    GROUP BY users.user_id 
    ORDER BY number_of_streams DESC LIMIT 15;
```

|user_id|user_name|level|number_of_streams|
|-------|---------|-----|-----------------|
|49 	|Chloe Cuevas 	|paid 	|130|
|16 	|Rylan George 	|paid 	|87|
|24 	|Layla Griffin 	|paid 	|53|
|80 	|Tegan Levine 	|paid 	|44|
|82 	|Avery Martinez 	|paid 	|40|
|15 	|Lily Koch 	|paid 	|37|
|73 	|Jacob Klein 	|paid 	|26|
|44 	|Aleena Kirby 	|paid 	|22|
|97 	|Kate Harrell 	|paid 	|14|
|88 	|Mohammad Rodriguez 	|paid 	|7|
|26 	|Ryan Smith 	|free 	|6|
|36 	|Matthew Jones 	|paid 	|6|
|91 	|Jayden Bell 	|free 	|5|
|54 	|Kaleb Cook 	|free 	|5|
|52 	|Theodore Smith 	|free 	|4|