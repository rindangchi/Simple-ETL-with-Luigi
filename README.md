# Simple-ETL-with-Luigi

In this project I will create a simple ETL using Luigi and Postgresql Database.

## Problem Definition

Company ABC wants to analyze its marketing data to gain a meaningful insight to improve its marketing campaign. Their objective is to find higher spender customers and give them special campaign. Marketing team will responsible to analyze data while IT team in this case is data engineer is responsible to prepare the data before it is analyzed by the markeing team. 


## Case Objectives

1. Design ETL pipeline
   
   Before implemet the real pupleine using python and luigi we will deign the pipeline as a picture to make us easier to understand the flow. Below is the flow to perform the ETL process.

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

   Next we will analyze the data types for each column:

   ```python

   print(data.dtypes)

   ```

   Here is the data types for each column, from the result we can conclude that all columns are with correct data types

   ![image](https://github.com/user-attachments/assets/027114bb-42e1-47a5-9a6a-794df5ee8557)

   During the extract process will save the data into .csv file named extract_data.csv

   ```python

   #creating pipilene to extract data/class ExtractData

   class ExtractData(luigi.Task):

     def requires(self):
       pass

     def run(self):
       #read data
       marketing_data = pd.read_csv("marketing_data.csv")

       marketing_data.to_csv(self.output().path, index=False)

     def output(self):
       return luigi.LocalTarget("/Users/rindangcahyaning/Documents/Bootcamp/PacmannDE/myenv/extract_data.csv")

   uigi.build([ExtractData()], local_scheduler = True)


   ```

   
   2.2. Create transform pipeline

   After read and understand the data, we will transform the data. The tranformation step that will be performed is to standarized the column names and to filter the data to get only customers with at least 50 spending score.

   ```python

   #class for transform data

   class TransformData(luigi.Task):

     def requires(self):
       return ExtractData()

     def run(self):
       #read data from previous process
       extract_data = pd.read_csv(self.input().path)

    #initialize dictionary fro re-name column

    RENAME_COLS = {

        "CustomerID" : "customer_id",
        "Genre" : "gender",
        "Age" : "age",
        "Annual_Income_(k$)" : "annual_income",
        "Spending_Score" : "spending_score"

    }

    extract_data = extract_data.rename(columns = RENAME_COLS)

    #filter data

    extract_data = extract_data[extract_data["spending_score"] >= 50 ]

    extract_data.to_csv(self.output().path, index=False)

    def output(self):
       return luigi.LocalTarget("/Users/rindangcahyaning/Documents/Bootcamp/PacmannDE/myenv/transform_data.csv")

    ```

   The result from transformation process also saved and stored in the local system in .csv format.

   
   
   2.3. Create Load pipline to load data to the database

   After we transform the data, the last step is to load the data into database system. Here we will use postgresql.

   Before we load the data, of course we need to prepare the database and table. Here, I create new postgresql connection, a database, table, and all colmns in  Dbeaver.
   I create connection named pacmann with localhost: 5432 (default localhost for postgresql), I also create a database named pacmann, and a table named mall_customer.

   ![image](https://github.com/user-attachments/assets/b7ca243d-9ca4-40cd-b6d2-03f682d1c0aa)


   Before creating Class Load, we need to create a function to connect pandas to the postgresql database.

   ```python

   #create helper function to connect pandas and postgresql

   from sqlalchemy import create_engine

   def postgres_engine():

     engine = create_engine("postgresql://postgres@localhost:5432/pacmann")

     return engine

   engine = postgres_engine()

   ```
   
   Finally, we create the Class Load to load the final data to the postgresql database.

   ```python

   #class load data

   class LoadData(luigi.Task):

      def requires(self):
         return TransformData()

      def run(self):
         #read data from transformed data
         transform_data = pd.read_csv(self.input().path)

      #create engine
      engine = postgres_engine()

      #insert to database
      transform_data.to_sql(name = "mall_customer",
                            con = engine,
                            if_exists = "append",
                            index = False)

      def output(self):
         pass
  

   luigi.build([ExtractData(),
             TransformData(),
             LoadData()], local_scheduler = True)

   ```


When we back to Dbeaver and check it, all the final data is already in the table.

![image](https://github.com/user-attachments/assets/1b994e6f-063a-4f01-86da-2c9b7db2f508)

   
