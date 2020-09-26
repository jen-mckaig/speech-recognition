# Emotional Recognition in Speech

Data:  The Ryerson Audio-Visual Database of Emotional Speech and Song (RAVDESS) contains 7356 files (total size: 24.8 GB). The database contains 24 professional actors (12 female, 12 male), vocalizing two lexically-matched statements in a neutral North American accent. 

I used AWS cloud services to store the files and run the models. Files were stored in an S3 bucket and accessed with the boto3 library. An EC2 instance was used to run jupyter notebooks and to access the files. 


## Exploratory Data Analysis

The emotions captured are: happy, sad, angry, fearful, neutral, disgust, surprise, calm. 

![download](https://user-images.githubusercontent.com/54602329/79247826-fd431b00-7e48-11ea-8d58-bb1a43e8af77.png)

I listened to the differences in sound by gender and emotion, then plotted the soundfiles. I looked for differences in pitch and differences in the length of the sound file plots. Four baseline emotions were picked for the model: happy, sad, calm, surprised. The emotions sounded clearly different from each other and they contained the same number of files (no class imbalance).

<img width="813" alt="Screen Shot 2020-04-14 at 12 14 54 PM" src="https://user-images.githubusercontent.com/54602329/79250029-2022fe80-7e4c-11ea-9af5-31f035d8e9a5.png">

The duration of soundfiles were inspected and it showed that the max difference is more than one minute in length, but the overall distributions appear normal. 

![download-1](https://user-images.githubusercontent.com/54602329/79249881-e81bbb80-7e4b-11ea-887a-26016f9ddd70.png)

## Feature Extraction

To reduce the number of variables and minimize noise, the mean of each feature is taken. The duration for all files is set at 6 minutes and the sampling rate is set to 1600.

- <b>Chroma</b>: 12-element pitch classe profiles

- <b>MFCC</b> (Mel Frequency Cepstral Coefficients): takes into account human perception for sensitivity at appropriate frequencies by converting the conventional frequency to Mel Scale

- <b>Zero Crossing Rate</b>: number of times in a given time interval/frame that the amplitude of the speech signals passes through a value of zero

- <b>Melspectrogram</b>: represents an acoustic time-frequency representation of a sound-the power spectral density P(f, t).It is sampled into a number of points around equally spaced times and frequencies (on a Mel frequency scale).

- <b>Spectral Centroid</b>: spectrum gives the indication of how the signal's mass (amplitude) is distributed among the frequencies, its center of mass indicates the average amount of amplitude (ie. the average loudness).

- <b>Tonal Centroid</b> (tonnetz): corresponds to human perception of the harmonic relationship between pitch classes.


The graphing function allows us to choose a gender and emotion, it then randomly picks a file containing that gender and emotion out of a dataframe and plots the features for us. 

emotion = 'surprised'
gender = 'woman'

![download-2](https://user-images.githubusercontent.com/54602329/79253357-0f28bc00-7e51-11ea-9e96-9b95e51a4d7d.png)

The data was separated by gender due to the clear differences in pitch. The new data sets were processed and features exctracted. Each dataset contains 155 features. 

## Classification Models

Initial models were run on an all gender data set, women only data set, and men only data set. The model using the women's voices performed best and led to the decisin to use separate models for each gender. 

### [All Gender] Random Forest Model
<img width="671" alt="Screen Shot 2020-04-14 at 1 29 54 PM" src="https://user-images.githubusercontent.com/54602329/79255397-39c84400-7e54-11ea-80a6-7d06d8ba7f6c.png">

### [Women] Random Forest Model
<img width="669" alt="Screen Shot 2020-04-14 at 1 32 42 PM" src="https://user-images.githubusercontent.com/54602329/79255580-7e53df80-7e54-11ea-9f06-fc9b37097f52.png">

### [Men] Random Forest Model
<img width="653" alt="Screen Shot 2020-04-14 at 1 34 46 PM" src="https://user-images.githubusercontent.com/54602329/79255729-cd017980-7e54-11ea-9928-200e6fef2b9d.png">

## Hyperparameter Tuning and Feature Selection
Gridsearch was used to hyperparamter tune the models. Feature importance was calculated and the bottom 5% of performers were removed from each model. This did not improve the performance of the women's model but it did improve the men's model. The women's model was kept as is and the men's model was augmented.

 ![download-3](https://user-images.githubusercontent.com/54602329/79264701-fcb77e00-7e62-11ea-9da2-cfd4f8684a95.png)

<b>Women's Random Forest Classification Model</b>:  155 features 
    
    {'n_estimators':100, 'max_depth':10, 'max_features':'auto', 'random_state': 42}
    
<img width="669" alt="Screen Shot 2020-04-14 at 1 32 42 PM" src="https://user-images.githubusercontent.com/54602329/79255580-7e53df80-7e54-11ea-9f06-fc9b37097f52.png">

    
<b>Men's Random Forest Classification Model</b>: 135 features

    {'max_depth': 10, 'max_features': 'auto', 'max_samples': 0.3, 'n_estimators': 100, 'random_state': 42, 'criterion': 'gini'}
 
<img width="655" alt="Screen Shot 2020-04-14 at 4 07 34 PM" src="https://user-images.githubusercontent.com/54602329/79269227-18725280-7e6a-11ea-9d3e-50bb32da6fdd.png">




