# NY-Property-Data-Anomalies-Detection
Find Anomalies in NY Property Data

### Problem Overview:
- City of NY think there might be some property tax fraud going on in their city, but they’re not sure.
- They have no idea how to discover this. They can’t just look through all the records manually.
- Their data is relatively large and fairly messy. About a million property records with about 32 fields. Many records have missing fields.
- One record for each property. Fields are things like address, owner, property characteristics, assessed tax values…
- They want to build an algorithm that can look through their ~1 million property records to find potential tax fraud.
- The kind of fraud they’re looking for is people underpaying tax by misrepresenting their property characteristics.  
  
Based on the problem description and given dataset, I decided to leverage **unsupervised machine learning algorithms** to solve this problem 

### Solution Approach:
1.**Create a data quality report:** A DQR is a preliminary quantitative analysis that explores and documents the basic characteristics of a data file.  
It is:
- Basic info about the file (how many records, time period, source…)
- Summary statistics for each field
  - Percent populated, # zeros
  - Numeric: min, max, mean, stdev, distribution/histogram
  - Categorical: # values, histogram or table of values, top several values

It serves as:
- a check with the business to make sure the data is correct
- help you get familiar with the data
- a resource for when you start to build variables

2.**Data preprocessing:**(Filling in missing field values only in this case)   
Fields that contain missing values: ZIP, STORIES, FULLVAL, AVLAND, AVTOT, LTFRONT, LTDEPTH, BLDFRONT, BLDDEPTH. There are a lot of ways to fill in missing values, but the main idea is the same ----> to replace the missing field value with something reasonable. In this problem, I used the average or most common value of that field over a relevant subset of records:
  - Select one or more other fields that you think are important in determining the missing field
  - Group the selected field(s) into categories
  - Replace the missing field with the average or most common value for its appropriate group

3.**Feature Engineering:**
- Create these 3 sizes:
  - lotarea = LTFRONT * LTDEPTH
  - bldarea = BLDFRONT * BLDDEPTH
  - bldvol = bldarea * STORIES
- Calculate 9 variables, each of the 3 value fields normalized by each of these 3 sizes (3 * 3 = 9 variables)
- Create the grouped averages of these 9 variables, grouped by zip5, zip3, TAXCLASS, borough
- Divide each of the 9 ratio variables by the 4 scale factors from these groupings. This makes 9 + 9 * 4 = 45 variables
  
    
![image](https://user-images.githubusercontent.com/72418274/117077600-47659b80-aced-11eb-84a4-86b8c0403dff.png)

- Generate a statistics table for all the variables built.

4.**Modeling:**
- Using principle components analysis to do dimenionalty reduction. It turns out 6 PCs are able to capture 99% of variation in the 45 features.
- Z-scaling the 6 selected PCs to make them more comparable in relative dimensions (call them z-scores).
- Calculate fraud scores. The fraud score is any function of these z scaled PCs that looks for extremes:
  - score 1: Combine the z-scores with a heuristic algorithm (in this case Minkowski distances), which basically measures distance to find closeness of records, this helps revealing outliers(anomolies)  
 ![image](https://user-images.githubusercontent.com/72418274/117088307-fcf11880-ad06-11eb-9c00-6b8e6d45400f.png)  

  - score 2:
    - train an autoencoder on all the data to reproduce the z scaled PC records, 
    - then, the fraud score is any measure of difference between the original input record and the autoencoder output record, for example:  
  ![image](https://user-images.githubusercontent.com/72418274/117088684-08910f00-ad08-11eb-9284-e9b23c65fd07.png)  
  
  (p = 1 ----> Manhattan distance, p = 2 ----> Euclidean distance, p > 2 ----> Chebyshev distance)
 - Combine the two scores using weighted average rank orders to get the final score and sort by the final score, high to low.  

5.**Visualizing the results:**
It is a feature of unsupervised learning that we never know how well we did. What I can do from here is to give my sorted list to City of NY so that they can go to investigate record by record starting from which has the highest score. In order to help them understand my logic of detecting fraud (unusual records), it is necessary to creat a heatmap that shows top 20 highest scored records to help interpreting why they are caught.

![image](https://user-images.githubusercontent.com/72418274/117089741-3deb2c00-ad0b-11eb-92a0-cd40e4ccc76a.png)


 
