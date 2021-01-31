# Data-Lake

# Project Motivation

The music startup has experienced growth with the data warehouse for processing at scale on the cloud, and has decided to move thier data to a Data Lake due to their further increasing expansion. The collected data takes the form of JSON files from user activity on the music app and a second set of JSON log files consisting of songs and artists data from the app all stored in S3.

The purpose of this project is to build an ETL pipeline to extract data from the JSON files on S3 and store them in Parquet tables for the Data Lake. The latter is to further enhance scalability, efficiency, flexibility (and the advantges of the Data Lake that comes with it) of real-time quesries.

By the end of this project, the analytics team should be able to run real-time queries from the Data Lake for insightful decision making on the app.

![Data Lake](https://github.com/Tsakunelson/Data-Lake/blob/main/Screen%20Shot%202021-01-31%20at%209.55.22%20AM.png)

NB: The above digram is the complete data lake architecture but this project specifically speaks to stuctured JSON data from an s3 bucket saved by an input API agent. The project is batch processing enabled (script launched on demand) and can be automated as an ETL work for real-time processing. 

# Data Sources

Two data sources in JSON format:

1. Song data: Log song features collected from the app

Each JSON file here contains metadata abbout a song and the artist of the song. For example, here is the filepath to a single song file of the dataset in S3:
``` song_data/A/B/C/TRABCEI128F424C983.json ```

Below is an example of what it contains:
``` {"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0} ```

2. Log data: User activity data collected from user activity logs on the app

This dataset is JSON format as well, containing user generated event on the app, based on the songs above. Here is the filepath to a sample log file:
```log_data/2018/11/2018-11-12-events.json```


Below is a screenshot of the data table:
![Log Events Head](https://github.com/Tsakunelson/Data_Warehouse_Redshift-/blob/main/log-data.png)


# Files Description
The project package includes Two files

etl.py: Consists of Process song_data in json format from an s3 bucket into the corresponding dimensional tables (songs and artists tables) the saving them into clusters back to s3 as parquet files. The latter is also apploed for log_data, which generates the fact table (song_plays) and two dimensional tables (date_time and users).

dl.cfg: As the name states, dl.cfg = data lake configurations; contains the necessary configurations required to connect to the created data lake s3 buckets. Bellow are the parameters to populate the dl.cfg file for a succesfull connection:

```
[AWS]
AWS_ACCESS_KEY_ID=''
AWS_SECRET_ACCESS_KEY=''
```

# DataBase Schema

Using the above song and log events dataset, below is the conceptual database schema implemented on a data lake for real-time quering at scale.


    |Users/|                     . |songs/|
    ------ .                  .    -----
              .             .
               | songplays/|
               .------------.
              .              .
             .                .
    |artists/|                    .|time_table/| 
     --------                     ------------

Fact Table
1. songplays - records in event data associated with song plays i.e. records with page NextSong
    - songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent, month, year

Dimension Tables
2. users - users in the app
    - user_id, first_name, last_name, gender, level
3. songs - songs in music database
    - song_id, title, artist_id, year, duration
4. artists - artists in music database
    - artist_id, name, location, lattitude, longitude
5. time - timestamps of records in songplays broken down into specific units
    - start_time, hour, day, week, month, year, weekday


Running the Project

1. Configure you dl.cfg file to connect to AWS S3


3. Run the etl process to create the optimized tables on the S3 bucket of the data lake
```python etl.py ```



