# Spotify Data Analysis

By Hayden Kellington

## Introduction
This Data Analysis explores the Spotify Dataset 1921-2020, created by Yamac Eren Ay and accessed through Kaggle. The data was collected using the Spotify Web API and contains information about music released across roughly a century, from early recorded songs to modern streaming-era tracks. I use two related datasets: one with 114,000 tracks and another with 1,162,095 artists. Together, these datasets make it possible to study both song-level characteristics and artist-level context.

The main research question for this project is: **Are collaborative tracks more popular than solo tracks, and what kinds of musical or artist-level factors help explain track popularity?** This question matters because popularity on streaming platforms is not determined by one simple factor. A song’s popularity may be related to how it sounds, when it was released, what genre it belongs to, or the audience and reputation of the artists involved. Collaboration is especially interesting because songs with multiple artists may benefit from combined fan bases and broader exposure.

Beyond this main question, the project also considers data quality and prediction. I examine patterns of missingness in the dataset and investigate whether missing values appear related to other observed information. I also frame track popularity as a prediction problem, using available song and artist information to estimate popularity scores. Overall, the goal is to better understand the relationship between collaboration, musical characteristics, artist context, and popularity in Spotify’s music data.

## Columns

| Column | Dataset | Description |
|---|---|---|
| `track_id` | Tracks | Unique identifier for each track. |
| `artists` | Tracks | Artist or artists credited on the track. Multiple artists may be separated by semicolons. |
| `album_name` | Tracks | Name of the album the track appears on. |
| `track_name` | Tracks | Name of the song or track. |
| `popularity` | Tracks | Spotify popularity score for the track, measured on a 0-100 scale. |
| `duration_ms` | Tracks | Length of the track in milliseconds. |
| `release_date` | Tracks | Date or year when the track was released. |
| `explicit` | Tracks | Whether the track is marked as explicit. |
| `danceability` | Tracks | Spotify audio feature measuring how suitable a track is for dancing. |
| `energy` | Tracks | Spotify audio feature measuring intensity and activity. |
| `key` | Tracks | Estimated musical key of the track. |
| `loudness` | Tracks | Overall loudness of the track in decibels. |
| `mode` | Tracks | Modality of the track, usually major or minor. |
| `speechiness` | Tracks | Spotify audio feature measuring the presence of spoken words. |
| `acousticness` | Tracks | Estimated confidence that the track is acoustic. |
| `instrumentalness` | Tracks | Estimated likelihood that the track contains no vocals. |
| `liveness` | Tracks | Estimated likelihood that the track was performed live. |
| `valence` | Tracks | Spotify audio feature measuring the musical positiveness of a track. |
| `tempo` | Tracks | Estimated tempo of the track in beats per minute. |
| `time_signature` | Tracks | Estimated time signature of the track. |
| `track_genre` | Tracks | Genre label associated with the track. |
| `id` | Artists | Unique identifier for each artist. |
| `followers` | Artists | Number of Spotify followers for the artist. |
| `genres` | Artists | Genres associated with the artist. |
| `name` | Artists | Artist name. |
| `popularity` | Artists | Spotify popularity score for the artist, measured on a 0-100 scale. |

## Data Cleaning and Exploratory Data Analysis
### Cleaning
I cleaned the data by keeping the main dataframe at the track level, so each song appears only once. Since the original track dataset stores multiple artists in a single semicolon-separated string, I split and exploded the artist names only temporarily in order to match tracks with the artist metadata. After merging with the artist dataset, I aggregated artist-level information back to one row per track, such as average artist followers, average artist popularity, and the number of matched artists.

I also created several useful columns for later analysis. I counted the number of credited artists on each track, created a collab indicator for whether a song has multiple artists, converted track duration from milliseconds to minutes, and extracted the release year from the release date. This cleaned structure prevents collaborative songs from being over-counted while still allowing the analysis to use both track-level and artist-level information.

Here is the first few rows of my final Dataframe

| track_id | track_name | artists | track_genre | popularity_x | duration_min | release_year | num_artists | collab | avg_artist_followers | avg_artist_popularity | matched_artists |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 5SuOikwiRyPMVoIQDJUgSV | Comedy | Gen Hoshino | acoustic | 73 | 3.84 | 1974 | 1 | False | 852637.00 | 66.00 | 1 |
| 4qPNDBW1i3p13qLCt0Ki3A | Ghost - Acoustic | Ben Woodward | acoustic | 55 | 2.49 | 1995 | 1 | False | 11874.00 | 53.00 | 1 |
| 1iJBSr7s7jYXzM8EGcbK5b | To Begin Again | Ingrid Michaelson;ZAYN | acoustic | 57 | 3.51 | 1973 | 2 | True | 8056483.00 | 76.00 | 2 |
| 6lfxq3CG4xtTiEg7opyCyx | Can't Help Falling In Love | Kina Grannis | acoustic | 71 | 3.37 | 2018 | 1 | False | 438860.00 | 71.00 | 1 |

## Exploratory Data Analysis
### Univariate Analysis

The distribution of track popularity shows that popularity scores are not evenly spread across the 0-100 scale. Many tracks have relatively low or moderate popularity, while highly popular tracks are less common. This suggests that popularity is skewed, which is important for both the exploratory analysis and the later prediction task.

<iframe
  src="assets/trackpopularitydist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


The genre count plot shows that the dataset is roughly balanced across genres, with many genres having the same number of tracks. Because of this, the plot is less useful for identifying dominant genres, but it is still helpful for understanding the structure of the dataset. Since genre counts are so evenly distributed, later genre-based analysis focuses more on differences in popularity and missingness rather than frequency.

<iframe
  src="assets/mostcommongenres.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Bivariate Analysis
This box plot compares popularity distributions across the 15 chosen genres. It helps show whether certain genres tend to have higher or lower popularity scores, which is relevant because genre may influence both listener exposure and model predictions.

<iframe
  src="assets/trackpopularitybygenre.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This scatter plot examines whether more danceable tracks tend to be more popular. The relationship appears useful to explore because danceability is one of the main audio features available for predicting popularity.

<iframe
  src="assets/danceablevspopular.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This scatter plot compares artist-level popularity with track-level popularity. It is especially relevant because tracks by already-popular artists may be more likely to receive higher popularity scores, regardless of the song’s audio features.

<iframe
  src="assets/artistpopsongpop.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Grouping and Aggregates
The aggregate table compares solo and collaborative tracks across popularity, duration, number of artists, and artist-level features. This is useful because my main research question asks whether collaborative tracks are more popular than solo tracks. The table provides an initial summary of whether collaborations have higher average or median popularity, and whether they differ in related features such as artist popularity or follower count.

<iframe
  src="assets/collab_summary.html"
  width="800"
  height="200"
  frameborder="0"
></iframe>

## Assessment of Missingness 
### NMAR Analysis
For my NMAR analysis, I focused on tempo because it was the only column with substantial missingness in the track dataset. I do not believe tempo is clearly NMAR, because there is not enough evidence that the chance of a tempo value being missing depends specifically on the unobserved tempo value itself. It seems more likely that tempo missingness is related to other observed information, such as release year, genre, or how Spotify’s audio analysis process handled certain types of tracks. If I had more information about how the Spotify Web API generated audio features, such as whether older songs or certain genres were harder to analyze, I could better determine the missingness mechanism. Based on the available data, I treat tempo missingness as something to investigate through dependency tests rather than assuming it is NMAR.

### Missingness Dependency
For missingness dependency, I tested whether missing values in tempo depend on other columns. I used tempo_missing as the missingness indicator and ran permutation tests by shuffling the missingness labels.

**Test 1: Tempo Missingess vs. Release Year**

**Null hypothesis:** The missingness of tempo does not depend on release_year; any difference in release year between rows with missing and non-missing tempo is due to random chance.

**Alternative hypothesis:** The missingness of tempo does depend on release_year.

**Test statistic:** Difference in mean release year between tracks where tempo is missing and tracks where tempo is not missing.

**Result:** The observed difference was about -1.52, meaning tracks with missing tempo were released about 1.5 years earlier on average. The p-value was approximately 0.001, so I reject the null hypothesis. This suggests that tempo missingness depends on release year. In the graph below, I grouped by decade to make it easier to view.

<iframe
  src="assets/missingnessbydecade.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Test 2:** Tempo Missingness vs. Track Genre

Null hypothesis: The missingness of tempo does not depend on track_genre; any difference in the genre distributions between rows with missing and non-missing tempo is due to random chance.

Alternative hypothesis: The missingness of tempo does depend on track_genre.

Test statistic: Total Variation Distance (TVD) between the genre distribution of tracks where tempo is missing and the genre distribution of tracks where tempo is not missing.

Result: The observed TVD was about 0.179, and the p-value was about 0.001. Since this p-value is less than 0.05, I reject the null hypothesis. This suggests that tempo missingness depends on track genre, meaning certain genres are more likely to have missing tempo values than others.

<iframe
  src="assets/missingnessbygenre.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing
For my hypothesis test, I investigated whether collaborative tracks tend to be more popular than solo tracks. This connects directly to my main research question because collaborations may benefit from multiple artists’ audiences and wider exposure. I used a permutation test to compare the average popularity of collaborative tracks and solo tracks while simulating a world where collaboration status has no relationship with popularity. 

**Null hypothesis:** Collaborative tracks and solo tracks come from the same popularity distribution, so any observed difference in average popularity is due to random chance.

**Alternative hypothesis:** Collaborative tracks are more popular on average than solo tracks.

**Test statistic:** Difference in mean popularity:
mean popularity of collaborative tracks - mean popularity of solo tracks

**Significance level:** 5% (α = 0.05)

The histogram below shows the simulated test statistics from the permutation test, with the observed statistic marked for comparison.

<iframe
  src="assets/hypothtesting.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
###Results:
The observed test statistic was about 0.642, meaning collaborative tracks had an average popularity score about 0.64 points higher than solo tracks in the observed data. The permutation test produced a p-value of about 0.0002. Since this p-value is less than the 5% significance level, I reject the null hypothesis. This provides evidence that collaborative tracks are more popular on average than solo tracks, although the size of the observed difference is fairly small.

## Framing a Prediction Problem
My prediction problem is to predict a track’s Spotify popularity score using information about the song and its artists. Since popularity is a numeric score, this is a regression problem rather than a classification problem. I chose popularity as the response variable because it connects directly to my research question about what factors help explain why some tracks are more popular than others.

At the time of prediction, I assume that track metadata, audio features, genre information, release information, and artist-level context would be available. These features are reasonable to use because they describe the song and the artists, rather than relying on the popularity score itself. I evaluate the model using RMSE, MAE, and R², with RMSE as the main metric because it measures prediction error in the same units as the popularity score and penalizes larger mistakes more heavily.

### Baseline Model
The baseline model is a multiple linear regression model used to predict track popularity. It uses original track-level features, including audio characteristics, duration, release year, explicitness, and genre. Numeric features are filled using median imputation, while categorical features are imputed and one-hot encoded before being passed into the regression model.

On the test set, the baseline model had an RMSE of about 21.97, an MAE of about 18.48, and an R² of about 0.022. This means the model’s predictions were typically off by around 18-22 popularity points, and the very low R² suggests that the baseline model explains only a small amount of the variation in track popularity. Overall, this model is useful as a simple starting point, but its performance shows that popularity is difficult to predict with only raw features and a linear model

### Final Model
The final model improves on the baseline by adding engineered features and using a more flexible Random Forest Regressor. I added features such as release_decade, is_collab, and log_avg_artist_followers to better capture patterns related to music era, collaboration status, and artist audience size. These features are useful because popularity may depend on broader release periods, whether a song combines multiple artists’ audiences, and the reach of the artists involved. I also tuned the random forest’s hyperparameters, including maximum depth, minimum samples per leaf, and number of trees.

The best final model used max_depth = 10, min_samples_leaf = 5, and n_estimators = 50. On the test set, it achieved an RMSE of about 19.49, an MAE of about 15.39, and an R² of about 0.231. Compared to the baseline model’s RMSE of about 21.97 and R² of about 0.022, the final model performed noticeably better. This suggests that the engineered features and nonlinear random forest model captured more of the structure in track popularity than the baseline linear regression model.

## Fairness Analysis
For the fairness analysis, I compared the final model’s performance on collaborative tracks and solo tracks. This group comparison connects directly to my main research question, since the project studies whether collaboration is related to track popularity. Because the model predicts a numeric popularity score, I used RMSE as the evaluation metric.

Null hypothesis: The final model performs equally well for collaborative tracks and solo tracks. Any difference in RMSE is due to random chance.

Alternative hypothesis: The final model performs worse for collaborative tracks than for solo tracks.

Test statistic:
RMSE for collaborative tracks - RMSE for solo tracks

The final model had an RMSE of about 20.91 for collaborative tracks and an RMSE of about 18.96 for solo tracks. The observed RMSE difference was about 1.95, meaning the model made larger errors for collaborative tracks. The permutation test produced a p-value of about 0.001, which is less than the 5% significance level, so I reject the null hypothesis. This suggests that the final model performs significantly worse for collaborative tracks than for solo tracks.




