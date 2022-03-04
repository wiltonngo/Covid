# Covid
#ASK:
The Covid-19 impacting many lives during the Pandemic. You want to know how many people affected were affected by this virus and how many deaths occuring during this time. As a Data Analyst you will be analyzing which countries had a highest death count and which the least. So you can follow the countries which had the least deaths help you improve your strategy in figuring how to control your deaths

#Prepare:
The data that we use https://ourworldindata.org/covid-deaths. The data is organized by Country,date and the amount of deaths. No they are no problems with my data.

Process:
The tools that I will be using is SQL and Tableau.
SELECT location,date,total_cases,new_cases,total_deaths,population
FROM `delta-sanctum-331816.COvid.Deaths`
order by 3,4
 
 
#Looking at Total Cases vs Total Deaths
#Shows likelihood of dying if you contract covid in your country
 
SELECT location,date,total_cases,new_cases,total_deaths, (total_deaths/total_cases)*100 AS DeathPercentage
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE location like '%States%'
order by 1,2

#Look at Total Cases vs population
#Shows what percentage of population got Covid
 
SELECT location,date,total_cases,population, (total_deaths/population)*100 AS PercentPopulationInfected
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE location like '%States%'
order by 1,2
 
# Looking at Countries with Highest infection Rate compared to Population
SELECT Location,Population, MAX(total_cases) AS HighestInfectionCount, MAX((total_deaths/population))*100 AS PercentPopulationInfected
FROM `delta-sanctum-331816.COvid.Deaths`
GROUP BY location,population
order by PercentPopulationInfected desc

 
# Showing Countries with Highest Death Count per Population
SELECT Location, Max(cast(total_deaths as int)) AS TotalDeathCount
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE continent is not null
GROUP BY location
ORDER by TotalDeathCount desc
 
# Let Break things by Continent
SELECT location, Max(cast(total_deaths as int)) AS TotalDeathCount
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE continent is null
GROUP BY location
ORDER by TotalDeathCount desc

# Showing Continent with the highest death count per population
SELECT continent, Max(cast(total_deaths as int)) AS TotalDeathCount
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE continent is not null
GROUP BY continent
ORDER by TotalDeathCount desc
 
# Global Numbers
SELECT date, SUM(new_cases) as total_cases,SUM(CAST(new_deaths as int)) as total_deaths, SUM(CAST(new_deaths as int))/SUM(new_cases)*100 as DeathPercentage
FROM `delta-sanctum-331816.COvid.Deaths`
WHERE continent is not null
GROUP BY date
ORDER by 1,2

#Looking at Total Population vs Vaccinations
 
With PopvsVac AS
(
SELECT deaths.continent, deaths.location, deaths.date, deaths.population,vac.new_vaccinations,sum(cast(vac.new_vaccinations as int)) over (partition by deaths.location ORDER BY deaths.location) AS RollingPeopleVaccinated,
FROM delta-sanctum-331816.COvid.Deaths as deaths
JOIN delta-sanctum-331816.COvid.Vaccinations as Vac
ON deaths.location = Vac.location
and deaths.date = Vac.date
Where deaths.continent is not null
)
Select *, ( RollingPeopleVaccinated/Population)*100

Tableau DashBoard:
https://public.tableau.com/app/profile/wilton.ngo/viz/CovidData_16463443841280/Dashboard2

#KEY FINDINGS:
The United States and United Kingdom has the highest effected by Covid
There are 6 millon deaths around the world because of COvid
Europe has the highest deaths among all the contients
