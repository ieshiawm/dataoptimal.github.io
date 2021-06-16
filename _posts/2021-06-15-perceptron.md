---
title: "COVID-19 SQL Project"
date: 2021-06-15
tags: [Data Analysis, SQL, COVID-19]
header:
  image: "/images/perceptron/2841_globezoom.jpg"
excerpt: "Data Analysis, SQL, COVID-19"
mathjax: "true"
---

# COVID-19 

### SQL Exploration

The following is a sample project I completed for COVID-19 data from ourworldindata.org

Here's the SQL code block:
'''
/*
The following information has been
exctracted from https://ourworldindata.org/covid-deaths as CSV
Statements Used: Joins, CTE's, Temp Tables, Windows Functions,
Aggregate Functions, Creating Views, *Note* this data is accurate as
of June 14, 2021.
*/
'''


'''
-- Select all columns from the CovidDeaths table
-- and order the resulting table by Location and Date
SELECT 
	*
	FROM CovidDeaths
	WHERE Continent IS NOT NULL 
ORDER BY Location,Date
'''


'''
-- SELECT base values for continued analysis
SELECT 
	Location AS [Location],
	Date AS [Date],
	total_cases AS [Total Cases],
	new_cases AS [New Cases],
	total_deaths AS [Total Deaths],
	Population AS [Population]
	FROM CovidDeaths
	WHERE Continent IS NOT NULL 
ORDER BY Location,Date
'''


'''
-- COVID-19 Total Cases Versus Total Deaths by country
-- Using Like operator
SELECT 
	Location AS [Location],
	Date AS [Date],
	total_cases AS [Total Cases],
	new_cases AS [New Cases],
	total_deaths AS [Total Deaths],
	ROUND((total_deaths/total_cases)*100,2) AS [Percentage of Deaths]

	FROM CovidDeaths
	WHERE Location LIKE '%states%'
	AND Continent IS NOT NULL 
ORDER BY Location,Date
'''


'''
-- COVID-19 Total Cases Versus Total Deaths by country
-- Using the exact name of the country of interest
SELECT 
	Location AS [Location],
	Date AS [Date],
	total_cases AS [Total Cases],
	new_cases AS [New Cases],
	total_deaths AS [Total Deaths],
	ROUND((total_deaths/total_cases)*100,2) AS [Percentage of Deaths]

	FROM CovidDeaths
	WHERE Location = 'United States'
	AND Continent IS NOT NULL 
ORDER BY Location,Date
'''


'''
-- Total Cases per country Population
-- The percentage of the population infected with COVID-19
SELECT 
	Location AS [Location],
	Date AS [Date],
	total_cases AS [Total Cases],
	ROUND((total_cases/Population)*100,2) AS [Percentage Infected]
	FROM CovidDeaths
ORDER BY Location,Date
'''


'''
-- Highest Infection Rate by countries
SELECT 
	Location AS [Location], 
	Population AS [Population], 
	MAX(total_cases) AS [Infection Rate],  
	ROUND(MAX((total_cases/Population))*100,2) AS [Max Population Infected]
	FROM CovidDeaths
GROUP BY Location, Population
ORDER BY [Max Population Infected] DESC
'''


'''
-- Death count by country
SELECT 
	Location AS [Location], 
	MAX(Total_deaths) AS [Death Count]
	FROM CovidDeaths
	WHERE Continent IS NOT NULL 
GROUP BY Location
ORDER BY [Death Count] DESC
'''


'''
-- Breakdown by continent
-- Death count by population
SELECT 
	Continent AS [Continent], 
	MAX(Total_deaths) AS [Death Count]
	FROM CovidDeaths
	WHERE Continent IS NOT NULL 
GROUP BY Continent
ORDER BY [Death Count] DESC
'''


'''
-- Global Numbers
SELECT 
	SUM(new_cases) AS [Total Cases], 
	SUM(CAST(new_deaths AS int)) AS [Death Sum], 
	ROUND(SUM(CAST(new_deaths AS int))/SUM(New_Cases)*100,2) AS [Percentage of Deaths]
	FROM CovidDeaths
	WHERE Continent IS NOT NULL 

ORDER BY [Total Cases],[Death Sum]
'''


'''
-- Percentage of the population that is vaccinated
-- Persons who have received at least one vaccination
SELECT 
	cd.Continent AS [Continent], 
	cd.Location AS [Location], 
	cd.Date AS [Date], 
	cd.Population AS [Population], 
	cv.new_vaccinations AS [New Vaccinations], 
	SUM(cv.new_vaccinations) OVER (PARTITION BY cd.Location ORDER BY cd.Location, cd.Date) AS [Rolling Total Vaccinations]
	FROM CovidDeaths cd
	INNER JOIN CovidVaccinations cv
		ON cd.Location = cv.Location
		AND cd.Date = cv.Date
WHERE cd.Continent IS NOT NULL 
ORDER BY Continent,Location
'''


'''
-- Percentage of the population that is vaccinated
-- using a CTE
;WITH Vaccination_CTE (Continent, 
						Location, 
						Date, 
						Population, 
						New_Vaccinations, 
						[Rolling Total Vaccinations])
AS
	(
	SELECT 
		cd.Continent AS [Continent],
		cd.Location AS [Location], 
		cd.Date AS [Date], 
		cd.Population AS [Population], 
		cv.new_vaccinations AS [New Vaccinations], 
		SUM(cv.new_vaccinations) OVER (PARTITION BY cd.Location ORDER BY cd.Location, cd.Date) AS [Rolling Total Vaccinations]
		FROM CovidDeaths cd
		INNER JOIN CovidVaccinations cv
		ON cd.Location = cv.Location
		AND cd.Date = cv.Date
		WHERE cd.Continent IS NOT NULL 
	)
SELECT *, ROUND(([Rolling Total Vaccinations]/Population)*100,2)
FROM Vaccination_CTE
'''


'''
-- Percentage of the population that is vaccinated
-- using a CTE
;WITH VaccinationVariation_CTE AS
	(
	SELECT 
		cd.Continent AS [Continent],
		cd.Location AS [Location], 
		cd.Date AS [Date], 
		cd.Population AS [Population], 
		cv.new_vaccinations AS [New Vaccinations], 
		SUM(cv.new_vaccinations) OVER (PARTITION BY cd.Location ORDER BY cd.Location, cd.Date) AS [Rolling Total Vaccinations]
		FROM CovidDeaths cd
		INNER JOIN CovidVaccinations cv
		ON cd.Location = cv.Location
		AND cd.Date = cv.Date
		WHERE cd.Continent IS NOT NULL 
	)
SELECT *, ROUND(([Rolling Total Vaccinations]/Population)*100,2)
FROM VaccinationVariation_CTE
'''


'''
-- Percentage of the population that is vaccinated
-- using a temporary table
DROP TABLE IF EXISTS #VaccinatedPopulationPercentage
CREATE TABLE #VaccinatedPopulationPercentage
(
	Continent NVARCHAR(255),
	Location NVARCHAR(255),
	Date DATETIME,
	Population FLOAT,
	New_vaccinations FLOAT,
	RollingPeopleVaccinated FLOAT
)

INSERT INTO #VaccinatedPopulationPercentage
SELECT 
	cd.Continent AS [Continent], 
	cd.Location AS [Location], 
	cd.Date [Date], 
	cd.Population [Population], 
	cv.new_vaccinations [New Vaccinations], 
	SUM(cv.new_vaccinations) OVER (PARTITION BY cd.Location ORDER BY cd.Location, cd.Date) AS [Rolling Total Vaccinations]
	FROM CovidDeaths cd
	INNER JOIN CovidVaccinations cv
	ON cd.Location = cv.Location
	AND cd.Date = cv.Date

SELECT *, ROUND((RollingPeopleVaccinated/Population)*100,2)
FROM #VaccinatedPopulationPercentage
'''


'''
 --Percentage of the population that is vaccinated
 --using a view
DROP VIEW IF EXISTS VaccinatedPopulationView;
GO
CREATE VIEW VaccinatedPopulationView AS
SELECT 
	cd.Continent AS [Continent], 
	cd.Location AS [Location], 
	cd.Date [Date], 
	cd.Population [Population], 
	cv.new_vaccinations [New Vaccinations], 
	SUM(cv.new_vaccinations) OVER (PARTITION BY cd.Location ORDER BY cd.Location, cd.Date) AS [Rolling Total Vaccinations]

	FROM CovidDeaths cd
	INNER JOIN CovidVaccinations cv
	ON cd.Location = cv.Location
	AND cd.Date = cv.Date
	WHERE cd.Continent IS NOT NULL;
GO
'''






  

