# Simple-ETL-with-Luigi

In this project I will create a simple ETL using Luigi and Postgresql Database.

## Problem Definition

Company ABC wants to analyze its marketing data to gain a meaningful insight to improve its marketing campaign. Their objective is to find higher spender customers and give them special campaign. Marketing team will responsible to analyze data while IT team in this case is data engineer is responsible to prepare the data before it is analyzed by the markeing team. 


## Case Objectives

1. Design ETL pipeline
   Before implemet the real pupleine using python and luigi we will deign the pipline as a picture to make us easier to understand the flow. Below is the flow to perform the ETL process.

   ![draw1](https://github.com/user-attachments/assets/d226b742-36e3-49eb-b60d-9b28099a1347)


2. Implement ETL Pipeline using Luigi
   
   2.1. Create extract pipeline for the given marketing data

   The first step is to extract marketing dataset to understand the data, such as data type, data shape, and column names.

   Note: We need to install Luigi first before start to code.
   To install luigi just write below syntax:

   ```python

   pip install luigi

   ```

   Let's read the .csv data

   ```python

   import luigi 
   import pandas as pd

   #to check luigi version
   luigi.__version__

   data = pd.read_csv("marketing_data.csv")

   data

   ```

   Here is the result, the file contains 200 rows and 5 columns. 

   ![image](https://github.com/user-attachments/assets/8e165d66-a064-472e-87ea-1e0c59cc7e65)


   
   
   2.2. Create transform pipeline
   2.3. Create Load pipline to load data to the database

   
