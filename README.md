# Amazon Prime Titles Data Cleaning

This repository contains the code and steps for cleaning and preparing the Amazon Prime Titles dataset for analysis. The dataset provides information about movies and TV shows available on Amazon Prime, including details such as title, director, cast, release year, and more. The dataset is loaded from a CSV file, and various data cleaning and preprocessing steps are performed.

## 1. Python Libraries Import
The first step involves importing essential Python libraries, which facilitate the data cleaning and analysis processes.
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
```

## 2. Load Data Set
The dataset is loaded by creating a pandas DataFrame from the CSV file 'amazon_prime_titles.csv.'
```python
df = pd.read_csv('amazon_prime_titles.csv')
```
Displays the data set that has been loaded
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/1dad9dd1-546b-49d1-9324-02fbcedc0168)

## 3. Check Data Condition
An overview of the dataset is provided, including details about its dimensions and data types.
```python
df.shape
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/e42b626a-e3af-44a4-9931-ca95a60d0b9b)
From the output, the dataset consists of 9,668 rows and 12 columns. It provides valuable information about the Amazon Prime Titles dataset.
```python
df.info()
```
Provide detailed information about the dataset
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/ef7e9f4c-c5dd-47a6-913b-e7db75c68fcb)
## 4. Check Percentage Missing Values
Calculate and display the percentage of missing values for each column in the dataset. Develop strategies for handling missing values based on the data types and their relevance to the analysis.
```python
missing_values = df.isnull().sum()

missing_percentages = (missing_values / len(df)) * 100

missing_info = pd.DataFrame({
    'Column': missing_values.index,
    'Missing Values': missing_values.values,
    'Missing Percentage': missing_percentages.values
})

print(missing_info)
``` 
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/00260b6f-3a35-46e4-ab5a-4f83d8c869bb)
Insights and Considerations 
-	Data Completeness: Columns like 'director,' 'cast,' 'country,' 'date_added,' 'rating,' and others have missing values that need attention.
-	Data Types: The 'release_year' column is correctly identified as an integer ('int64'). Other columns, such as 'date_added' and 'duration,' might need conversion to datetime or numeric types if they contain date or duration information.

## 5. Check and Remove Duplicate
Duplicate rows are identified and removed from the dataset if any are found.
```python
df.duplicated().sum()
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/48e404af-61e0-4f19-98d1-9f04bbc8b2ad)
As the output is 0, no further elimination is required. This confirms that there are no duplicates in the dataset.

## 6. Drop Columns
Columns 'date_added' and 'country' are dropped due to their high percentage of missing data (more than 50%) and limited relevance to the analysis. 
```python
df.drop(['date_added','country'], axis = 1, inplace=True)
df.columns
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/e81d913c-e7a2-484f-a909-386514adc094)
Show dataset info to see if the date and country columns are missing.
Check the others columns missing values.
```python
df.info()
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/1920c4a2-1e8f-4f2c-96a0-df741cca01b5)
The output shows the continued presence of null values within columns such as 'director', 'cast', 'release_year' and 'rating'.

## 7. Enhance Data
In this section, the dataset is enhanced to improve its quality and usability. This includes renaming the 'listed_in' column, converting 'duration' values into a numerical format, and splitting the 'duration' column into 'duration_movie' and 'duration_season'. Additionally, the 'categories' column is divided into multiple columns to better represent the genres.
```python
df.rename(columns={'listed_in' : 'categories'},inplace=True)
df.head(2)
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/120eb595-7fb9-4ff9-a824-fcf62d09cb00)
To solve the data type problems at duration and date added.
- 'date_added' is removed from the dataset.
- Attention is focused on the 'duration' columns.
A new column named 'duration_num' is created in the DataFrame to store numerical values extracted from the 'duration' column.
```python
df['duration_num'] = df['duration'].str.extract('(\d+)').astype(float)
```
The 'duration' column is then split into 'duration_movie' and 'duration_season'
```python
df['duration_movie'] = df['duration'].str.extract('(\d+) min', expand=False)

df['duration_season'] = df['duration'].str.extract('(\d+) Seasons', expand=False)

df.drop('duration', axis=1, inplace=True)

df.head()
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/0ea93ae6-1eaf-47a9-85f4-6dba73aa1f73)
To enhance the dataset further, the 'categories' column is divided into three separate columns to represent the different genres
```python
df[['Genre1', 'Genre2', 'Genre3']] = df['categories'].str.split(',', expand=True, n=2)
```
```python
df.head()
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/74cb5fd1-c957-449f-a60a-f432eb6cd265)
After renaming the 'listed_in' column and extracting numerical values from 'duration', the dataset is more structured and suitable for analysis. It allows for better categorization of genres and simplifies the representation of content duration.

## 8. Handling Missing Values
This section is dedicated to addressing missing values in the dataset, focusing on non-categorical and non-numerical columns. The primary objective is to properly impute the missing data. The 'rating' column will be handled differently by assigning it the mode value. Missing values in other columns will be filled with 'unavailable' for non-categorical and non-numerical columns.
```python
df.isnull().sum()
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/66e991ca-b80b-4b08-b80b-b48c22e5fc6e)
The inspection reveals that there are missing values in the columns 'director', 'cast', 'rating', 'duration_movie', 'duration_season', 'Genre2', and 'Genre3'.
```python
df['director'].fillna('Unavailable', inplace=True)
df['cast'].fillna('Unavailable', inplace=True)
df['duration_season'].fillna('Unavailable', inplace=True)
df['duration_movie'].fillna('Unavailable', inplace=True)
df['Genre1'].fillna('Unavailable', inplace=True)
df['Genre2'].fillna('Unavailable', inplace=True)
df['Genre3'].fillna('Unavailable', inplace=True)

df['rating'] = df['rating'].fillna(df['rating'].mode()[0])
df
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/bdef57df-ba91-4bae-9381-7a905b090669)
Following the filling of missing values, the dataset is inspected again to confirm the absence of any remaining missing values
```python
df.isnull().sum() 
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/b979d81d-cc12-41b2-8be6-a497af4ebdb2)
The output confirms that there are no missing values in the dataset, meaning that all columns have been appropriately handled. The dataset is now more complete and ready for further analysis and processing.

## 9. Check Outliers
Analyze the distribution of film duration for 'Movie' type films.
The calculated lower and upper bounds can then be used to identify and manage outliers in the film's duration.
```python
movie_df = df[df['type'] == 'Movie']

q1_movie = movie_df['duration_num'].quantile(0.25)
q3_movie = movie_df['duration_num'].quantile(0.75)

print("Quartil 1 (Q1) untuk Movie:", q1_movie)
print("Quartil 3 (Q3) untuk Movie:", q3_movie)

iqr=q3_movie-q1_movie
print('IQR : ',iqr)

lwr_bound = q1_movie - (1.5 * iqr) #lower bound
upr_bound = q3_movie + (1.5 * iqr) #upper bound

print('Lower Bound : ',lwr_bound)
print('Upper Bound: ',upr_bound)
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/1b293909-18a3-4962-8e01-f99b025f84d2)
Arrange the rows of the DataFrame in descending order based on the values in the 'duration_num' column.
In the context of movies, this operation would arrange the movies from the longest to the shortest duration.
```python
movie_df.sort_values(by='duration_num', ascending=False)
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/5d6b249d-d831-4738-9fd3-305c70e3e105)
Filter and select rows from the sorted DataFrame movie_df_sorted where the 'duration_num' values fall outside a specific range.
```python
movie_df_sorted = movie_df.sort_values(by='duration_num', ascending=False)
movie_df_sorted[(movie_df_sorted['duration_num'] > 152) | (movie_df_sorted['duration_num'] < 28)]
```

![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/9effa231-6368-42fb-ac7e-79497bed61bf)


## 10. Save Clean Data in CSV
The cleaned dataset is saved as a CSV file, named "amazon_clean.csv."
```python
df_clean = pd.DataFrame(df)
df_clean.to_csv("amazon_clean.csv")
df_clean
```
![image](https://github.com/divakeishya/data_basic_data_cleansing/assets/145829080/b4df3eb8-bec1-46cf-aa81-8dff77a805b8)
