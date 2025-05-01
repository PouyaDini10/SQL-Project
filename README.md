# 📊 Layoffs Data SQL Analysis

This project performs exploratory data analysis (EDA) using SQL on a dataset of tech company layoffs. The analysis covers trends in layoffs by company, industry, country, time period, and funding stage. It also includes ranking and rolling totals.

---

## 🧰 Tools Used

- MySQL
- SQL (CTEs, window functions, aggregates)
- STR_TO_DATE, DATE_FORMAT, GROUP BY, DENSE_RANK

---

## 📋 Full SQL Code

```sql
-- Exploratory Data Analysis
SELECT *
FROM layoffs_staging2;

SELECT MAX(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;

SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1;


-- SUM of Total Laid off by Company
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;


-- SUM of Total Laid off by Industry
SELECT industry, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;


-- SUM of Total Laid off by Country
SELECT country, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;


-- SUM of Total Laid off by Date only
SELECT YEAR(`date`) AS Year_Only, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2
GROUP BY YEAR(`date`)
HAVING Year_Only IS NOT NULL
ORDER BY 1 DESC;


-- SUM of Total Laid off by Stage
SELECT stage, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;


-- Rolling Total by Date (Monthly)
WITH rolling_total_cte AS (
  SELECT DATE_FORMAT(STR_TO_DATE(`date`, '%m/%d/%Y'), '%Y-%m') AS Time_Line,
         SUM(total_laid_off) AS Total_Laid_Off
  FROM layoffs_staging2
  GROUP BY Time_Line
  HAVING Time_Line IS NOT NULL
  ORDER BY 1 ASC
)
SELECT Time_Line, Total_Laid_Off,
       SUM(Total_Laid_Off) OVER(ORDER BY Time_Line) AS Rolling_Total
FROM rolling_total_cte;


-- Companies Total Layoff by Year
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;


-- Ranking of Companies with the Most Layoffs
WITH Company_Year(company, years, total_laid_off) AS (
  SELECT company, YEAR(`date`), SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY company, YEAR(`date`)
  ORDER BY 3 DESC
),
Company_Year_Rank AS (
  SELECT *,
         DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
  FROM Company_Year
  WHERE years IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5;

