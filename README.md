# 📊 Layoffs Data SQL Analysis

This project showcases exploratory data analysis (EDA) using SQL on a layoffs dataset. The goal is to understand patterns in global company layoffs based on industry, country, and time period using structured queries.

## 📁 Project Files

- `Exploratory Project (Layoffs).sql` – contains all SQL queries used for analysis

## 🧰 Tools & Technologies

- MySQL (or any SQL-based RDBMS)
- SQL aggregate functions
- Common Table Expressions (CTEs)
- Window functions
- Date formatting and manipulation

## 🔍 Key Analytical Areas

- Overview and raw data exploration
- Maximum layoffs and full-percentage workforce layoffs
- Total layoffs by:
  - Company
  - Industry
  - Country
  - Funding stage
  - Year
- Monthly trends and rolling total of layoffs
- Ranking companies with the highest layoffs by year

## 🧠 Key Insights

- Several companies had full (100%) workforce layoffs
- Tech and startup-related industries show the highest total layoffs
- Layoffs peaked significantly in 2022 and 2023
- Certain countries and industries are more impacted than others
- Rolling totals indicate accelerating layoffs during specific time frames

## 📈 Sample Queries Included

```sql
-- Total layoffs by industry
SELECT industry, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;

-- Rolling monthly total of layoffs
WITH rolling_total_cte AS (
  SELECT DATE_FORMAT(STR_TO_DATE(`date`, '%m/%d/%Y'), '%Y-%m') AS Time_Line,
         SUM(total_laid_off) AS Total_Laid_Off
  FROM layoffs_staging2
  GROUP BY Time_Line
)
SELECT Time_Line, Total_Laid_Off,
       SUM(Total_Laid_Off) OVER (ORDER BY Time_Line) AS Rolling_Total
FROM rolling_total_cte;

