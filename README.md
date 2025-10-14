# COVID-19 Data Analysis | SQL & Excel

![SQL Server](https://img.shields.io/badge/Database-SQL%20Server-blue)
![Excel](https://img.shields.io/badge/Data%20Cleaning-Excel-green)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## Project Overview
Analyzed global COVID-19 cases, deaths, and vaccination data to uncover trends at the country and continent levels.  
Cleaned datasets in Excel and used SQL Server to calculate key metrics for dashboards and decision-making.

**Highlights:**
- Country-level death rate and infection percentage analysis.
- Rolling vaccination counts and population vaccination percentages.
- Reusable SQL view (`PercentPopulationVaccinated`) for visualizations.
- Cleaned and prepped datasets for reliable analysis.

---

## Sample Output

**PercentPopulationVaccinated View**

| Continent | Location | Date       | Population  | New Vaccinations | Rolling Vaccinated | % Population Vaccinated |
|-----------|----------|-----------|------------|----------------|------------------|------------------------|
| Asia      | India    | 2021-01-01 | 1,380,004,385 | 50,000         | 50,000           | 0.0036                 |
| Asia      | India    | 2021-01-02 | 1,380,004,385 | 75,000         | 125,000          | 0.0091                 |
| Europe    | Germany  | 2021-01-01 | 83,149,300    | 20,000         | 20,000           | 0.0240                 |
| Europe    | Germany  | 2021-01-02 | 83,149,300    | 25,000         | 45,000           | 0.0541                 |

---

## How It Works

1. **Data Cleaning:**  
   - Excel used to clean raw CSV datasets.  
   - Removed duplicates and handled missing values.

2. **SQL Analysis:**  
   - Calculated death percentage per country: `total_deaths / total_cases * 100`.  
   - Calculated infection percentage: `total_cases / population * 100`.  
   - Computed cumulative vaccinations per country with rolling sums and percentage of population vaccinated.

3. **Persistent View for Dashboards:**  
   - `PercentPopulationVaccinated` view stores cumulative vaccination data for easy integration with visualization tools like Power BI or Tableau.

---

## Skills & Tools
- SQL Server Management Studio (SSMS) – querying, CTEs, views
- Excel – data cleaning, handling missing values
- Optional: Power BI / Tableau for visualization

---

## Key Insights
- Significant differences in vaccination rollout speed between countries.  
- Death and infection percentages provide quick country-level risk insights.  
- Cumulative vaccination data supports timeline-based visualizations.

---

## Business-Focused SQL Questions & Answers

**Q1. Which countries have the highest death rates, and how can this inform healthcare priorities?**  
```sql
SELECT Location, Date, total_cases, total_deaths,
       (total_deaths/total_cases)*100 AS DeathPercentage
FROM CovidProject..CovidDeaths$
ORDER BY DeathPercentage DESC;
````

**Insight:** Identifies countries needing urgent medical support, ICU beds, and vaccination efforts.

---

**Q2. Which countries are most affected relative to their population?**

```sql
SELECT Location, Population, MAX(total_cases) AS TotalCases,
       MAX((total_cases/Population)*100) AS PercentPopulationInfected
FROM CovidProject..CovidDeaths$
GROUP BY Location, Population
ORDER BY PercentPopulationInfected DESC;
```

**Insight:** Shows where COVID spread rapidly relative to population, informing lockdowns and resource allocation.

---

**Q3. How can we track vaccination progress by country over time?**

```sql
SELECT location, date,
       SUM(CONVERT(BIGINT, new_vaccinations)) OVER (PARTITION BY location ORDER BY date) AS RollingPeopleVaccinated
FROM CovidProject..CovidVaccinations$;
```

**Insight:** Helps monitor rollout trends and identify countries with slower vaccination campaigns.

---

**Q4. How much of the population has been vaccinated, and are countries meeting targets?**

```sql
SELECT *, (RollingPeopleVaccinated / Population)*100 AS PercentPopulationVaccinated
FROM PopvsVac;
```

**Insight:** Percent vaccinated shows coverage gaps, guiding policy decisions and outreach programs.

---

**Q5. Which continents have the highest total deaths?**

```sql
SELECT continent, SUM(total_deaths) AS TotalDeaths
FROM CovidProject..CovidDeaths$
GROUP BY continent
ORDER BY TotalDeaths DESC;
```

**Insight:** Guides global health organizations on prioritizing aid and vaccination campaigns.

---

**Q6. How can cumulative vaccination data be stored for dashboards?**

```sql
CREATE VIEW dbo.PercentPopulationVaccinated AS
SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
       SUM(CONVERT(BIGINT, vac.new_vaccinations)) OVER (PARTITION BY dea.Location ORDER BY dea.date) AS RollingPeopleVaccinated
FROM CovidProject..CovidDeaths$ dea
JOIN CovidProject..CovidVaccinations$ vac
  ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL;
```

**Insight:** Enables dashboards to display cumulative vaccination trends without repeated query complexity.

---

**Q7. Which countries should be prioritized for vaccine campaigns?**

```sql
SELECT Location, Population,
       MAX((total_cases/Population)*100) AS PercentPopulationInfected,
       MAX(RollingPeopleVaccinated/Population*100) AS PercentPopulationVaccinated
FROM CovidProject..CovidDeaths$ dea
JOIN dbo.PercentPopulationVaccinated vac
  ON dea.location = vac.location
GROUP BY Location, Population
ORDER BY PercentPopulationInfected DESC, PercentPopulationVaccinated ASC;
```

**Insight:** Prioritizes countries with high infection rates but low vaccination coverage.

---

**Q8. How can global trends inform international travel or trade policies?**

```sql
SELECT continent,
       SUM(total_cases) AS TotalCases,
       SUM(total_deaths) AS TotalDeaths,
       SUM(RollingPeopleVaccinated)/SUM(Population)*100 AS PercentPopulationVaccinated
FROM CovidProject..CovidDeaths$ dea
JOIN dbo.PercentPopulationVaccinated vac
  ON dea.location = vac.location
GROUP BY continent;
```

**Insight:** Helps governments and businesses make decisions on travel restrictions and resource allocation.

---

## Author

**Shoun** – Data Analyst / SQL Developer 

```

