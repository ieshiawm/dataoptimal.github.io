---
title: "Weather Pattern Project"
date: 2021-05-31
tags: [Data Analysis, Excel, Visualization]
header:
  image: "/images/perceptron/2841_globezoom.jpg"
excerpt: "Data Analysis, Excel, Visualization"
mathjax: "true"
---

# Weather Pattern

### Excel Visualization

The following is a sample project I completed. **SQL** was used to extract the data from a database, **Excel** was used to clean and "wrangle" the data. Finally, the chart was created using Excel.

Here's the SQL code block:
```
SELECT 
  * FROM cityData
  WHERE city = 'Atlanta' AND country = 'United States'
```
```
SELECT
  * FROM globalData
```
```
SELECT
  *
  FROM cityData
  WHERE city = 'Chicago' AND country = 'United States'
  ```
  
Here's a screenshot of the 5 year moving average created using Excel

<img src="{{ site.url }}{{ site.baseurl }}/images/Excel Charts/Weather Pattern 5-yr moving average.JPG">

