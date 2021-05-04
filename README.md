# NY-Property-Data-Anomalies-Detection
Find Anomalies in NY Property Data

### Problem Overview:
- City of NY think there might be some property tax fraud going on in their city, but they’re not sure.
- They have no idea how to discover this. They can’t just look through all the records manually.
- Their data is relatively large and fairly messy. About a million property records with about 32 fields. Many records have missing fields.
- One record for each property. Fields are things like address, owner, property characteristics, assessed tax values…
- They want to build an algorithm that can look through their ~1 million property records to find potential tax fraud.
- The kind of fraud they’re looking for is people underpaying tax by misrepresenting their property characteristics.

### Solution Approach:
1.**Create a data quality report:** A DQR is a preliminary quantitative analysis that explores and documents the basic characteristics of a data file.  
It is
- Basic info about the file (how many records, time period, source…)
- Summary statistics for each field
  - Percent populated, # zeros
  - Numeric: min, max, mean, stdev, distribution/histogram
  - Categorical: # values, histogram or table of values, top several values
It serves as:
- a check with the business to make sure the data is correct
- help you get familiar with the data
- a resource for when you start to build variables

2.**Data preprocessing:** 
