---
title: "COVID-19 Tableau Dashboard"
date: 2021-06-16
tags: [Data Analysis, Tableau, Visualization, COVID-19, Coronavirus]
header:
  image: "/images/Covid Variant.jpg"
excerpt: "Data Analysis, Tableau, Visualization, Coronavirus"
mathjax: "true"
---

# COVID-19

### Tableau Visualization

The following visualizations were created using Tableau Public, and the SQL scripts from the previous SQL exploration post.
As a refresher they are included below:

SQL:
```
SELECT
	SUM(new_cases) AS [Total Cases],
	SUM(new_deaths) AS [Total Deaths],
	ROUND((SUM(new_deaths)/SUM(new_cases))*100,2) AS [PercentageDeaths]
	FROM CovidDeaths
	WHERE Continent IS NOT NULL
ORDER BY [Total Cases],[Total Deaths]
```
```
SELECT
	Location AS [Location],
	SUM(new_deaths) AS [Death Count]
	FROM CovidDeaths
	WHERE Continent IS NULL
	AND Location NOT IN ('World', 'European Union', 'International')
GROUP BY Location
ORDER BY [Death Count] DESC

```
```
SELECT
	Location AS [Location],
	Population AS [Population],
	MAX(total_cases) AS [Infection Count],
	ROUND(MAX(total_cases/Population)*100,2) AS [Percent Infected]
	FROM CovidDeaths
GROUP BY Location,Population
ORDER BY [Percent Infected] DESC
  ```
  
```
SELECT 
	Location AS [Location],
	Population AS [Population],
	Date AS [Date],
	MAX(total_cases) AS [Infection Count],
	ROUND(MAX(total_cases/Population)*100,2) AS [Percent Infected]
	FROM CovidDeaths
GROUP BY Location, Population, Date
ORDER BY [Percent Infected] DESC
  ```
 
 **A screenshot of the resulting dashboard**
<img src="{{ site.url }}{{ site.baseurl }}/images/Tableau Dashboard.PNG">

**Which can be viewed by following the link below:**
[Tableau Dashboard](https://public.tableau.com/views/COVID-19Dashboard_16238961337830/Dashboard1?:language=en-US&:retry=yes&:display_count=n&:origin=viz_share_link)

