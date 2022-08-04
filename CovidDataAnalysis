select * from CovidDeaths
order by 3,4

select * from CovidVaccinations
order by 3,4

select location, date, total_cases, new_cases, total_deaths, population 
from CovidDeaths
order by 1,2

--Looking at Total Cases vs Total Deaths
select location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as mortality_rate
from CovidDeaths
where total_deaths is not null and
location like '%states%'
order by 1,2

--Looking at total cases vs population
--Show what percentage of population got Covid
select location, date,population,total_cases,(total_cases/population)*100 as case_rate
from CovidDeaths
where total_deaths is not null and
location like '%states%'
order by 1,2

--Looking at Countries with Highest Infection Rate compared to Population
select location,population,max(total_cases)as highest_infection_count,max(total_cases/population)*100 as case_rate
from CovidDeaths
where total_deaths is not null
group by location,population
order by case_rate desc

--Looking at Countries with highest Death Count per population
select location,max(cast(total_deaths as int))as highest_death_count
from CovidDeaths
where total_deaths is not null and continent is not null
group by location
order by highest_death_count desc

--Break things down by continent
select location,max(cast(total_deaths as int))as highest_death_count
from CovidDeaths
where continent is null 
group by location
order by highest_death_count desc

--showing continents with highest death count per population
select continent,max(cast(total_deaths as int))as highest_death_count
from CovidDeaths
where continent is not null
group by continent
order by highest_death_count desc

--Global Numbers
select date, sum(new_cases) as total_cases, sum(cast(new_deaths as int))as total_deaths,SUM(cast(new_deaths as int))/sum(new_cases)*100 as death_percentage
from CovidDeaths
where continent is not null
group by date
order by 1

--Global death percentage
select sum(new_cases) as total_cases, sum(cast(new_deaths as int))as total_deaths,SUM(cast(new_deaths as int))/sum(new_cases)*100 as death_percentage
from CovidDeaths
where continent is not null
order by 1

--Looking at Total Population vs Vaccinations
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.date)
from CovidDeaths dea join CovidVaccinations vac
ON dea.location = vac.location
AND dea.date = vac.date
where dea.continent is not null
order by 2,3

--Use CTE
With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated)
as
(
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.date)
from CovidDeaths dea join CovidVaccinations vac
ON dea.location = vac.location
AND dea.date = vac.date
where dea.continent is not null
)

Select *, (RollingPeopleVaccinated/Population)*100 as PercentVaccinated
From PopvsVac

--Use Temp table
Drop table if exists #PercentPopulationVaccinated
Create table #PercentPopulationVaccinated
(Continent nvarchar(255),
location nvarchar(255),
date datetime,
population numeric,
new_vaccinations numeric, 
RollingPeopleVaccinated numeric)

Insert into #PercentPopulationVaccinated
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.date)
from CovidDeaths dea join CovidVaccinations vac
ON dea.location = vac.location
AND dea.date = vac.date
where dea.continent is not null

Select *, (RollingPeopleVaccinated/Population)*100 as PercentVaccinated
From #PercentPopulationVaccinated

--Creating View to store data for later visualizations
Create view ContinentTotalDeaths as 
select continent,max(cast(total_deaths as int))as highest_death_count
from CovidDeaths
where continent is not null
group by continent
--order by highest_death_count desc

select * from ContinentTotalDeaths

Create View PercentPopulationVaccinated as
select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.date)
as RollingPeopleVaccinated
from CovidDeaths dea join CovidVaccinations vac
ON dea.location = vac.location
AND dea.date = vac.date
where dea.continent is not null
