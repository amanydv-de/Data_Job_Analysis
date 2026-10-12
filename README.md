# Introduction
  Dive into the job market! Focusing on data
  analysis roles, this project explores top
  paying jobs, in-demand skills, and where
  high demand  high salary in data analytics.

  SQL queries? Check them out here:  [project_sql
  folder](/project_sql/)

# Background
  Driven by a quest to naviagte the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

  ### The questions I wanted to answer through my sql queries are:

  1. What are the top-paying data analyst jobs?
  2. What skills are required for these top-paying jobs?
  3. What skills are most in demand for data analysts?
  4. Which skills are asssociated with  higher salaries?
  5. What are the most optimal skills to learn?

# Tools I Used
  For my deep dive into the data analyst job market,I harnessed the power of several key tools:

  -**SQL:** The backbone of my analysis,allowing me to query the database and unearth critical insights.

  -**PostgreSQL:** The chosen database management system,ideal for handling the job posting data.

  -**Visual Studio Code:** My go-to for database managment and executing SQL queries.

  -**Git & Github:** Essential for version control and collaboration and project tracking.

# The Analysis
  Each query for this project aimed at investing specific aspects of the data analyst job market.
  Here's how I approached each question:

  ### 1. Top Paying Data Analyst Jobs
  To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs.This query highlights the high paying oppurtunities in the field
  ```sql
  SELECT
    job_id,
    job_title,
    job_location,
    company.name as company_name,
    job_schedule_type,
    salary_year_avg,
    job_posted_date

  FROM
      job_postings_fact as jobs
  left join company_dim as company on jobs.company_id=company.company_id
  WHERE
      job_title_short='Data Analyst' and job_location='Anywhere'
      and salary_year_avg is not NULL
  order by salary_year_avg DESC
  limit 10;
  ```
  Here's the breakdown of the top data analyst jobs in 2023:
  
  -**Wide Salary Range:** Top 10 paying analyst roles from $184,000 to $650,000 indicating significant salary potential in the field.

  -**Diverse Employers:** Companies like SmartAsset , Meta , and AT&T are among those offering high salaries.

  -**Job Title Variety:** There's a high diversity in job titles, from Data Analyst to Director of Analytics , reflecting varied roles and specializations within data analytics.

  ![Top Paying Roles](./assets/Average%20Annual%20Salary%20by%20Job%20Title.png)
  *Bar graph visualizing the salary for the top 10 salaries for data analysts

  ### 2. Top Paying Job Skills
  To identify the skills required for the highest-paying Data Analyst positions, I used a Common Table Expression (CTE) to find the top 10 highest-paying remote job postings. I then joined the results with the skills tables to identify the technical skills associated with these roles.

  ```sql
  WITH top_paying_jobs as (
    SELECT
        job_id,
        job_title,
        name as company_name,
        salary_year_avg
        

    FROM
        job_postings_fact 
    left join company_dim on job_postings_fact.company_id=company_dim.company_id



    WHERE
        job_title_short='Data Analyst' and job_location='Anywhere'
        and salary_year_avg is not NULL
    order by salary_year_avg DESC
    limit 10
  )

  SELECT 
      top_paying_jobs.*,
      skills
  FROM top_paying_jobs
  inner join skills_job_dim on top_paying_jobs.job_id=skills_job_dim.job_id
  inner  join skills_dim on skills_job_dim.skill_id=skills_dim.skill_id
  order by salary_year_avg DESC
  ```
  ![Top Paying Skills](assets\top_paying_skills.png)
  *Visualization of skills associated with the top-paying Data Analyst job postings.

  
### 2. Top-Paying Job Skills

To identify the skills required for the highest-paying Data Analyst positions, I used a CTE to find the top 10 highest-paying remote job postings. I then joined the results with the skills tables to identify the skills associated with these roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        name AS company_name,
        salary_year_avg
    FROM job_postings_fact
    LEFT JOIN company_dim
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

**Key Findings:**

- **Wide Salary Range:** The top-paying job listings ranged from $184,000 to $650,000 in average annual salary.
- **Diverse Technical Skills:** Skills such as SQL, Python, R, Azure, AWS, Pandas, and PySpark appeared among the top-paying job listings.
- **Advanced Technologies:** Databricks and Snowflake also appeared in the results, highlighting the relevance of modern data platforms.

---

### 3. Most In-Demand Skills for Data Analysts

To identify the most in-demand skills, I analyzed remote Data Analyst job postings and counted how frequently each skill appeared. I then ranked the skills by demand to identify the top five.

```sql
SELECT
    COUNT(skills_job_dim.job_id) AS demand_count,
    skills
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

**Query Output:**

| Skills | Demand Count |
|---|---:|
| SQL | 7291 |
| Excel | 4611 |
| Python | 4330 |
| Tableau | 3745 |
| Power BI | 2609 |

**Key Findings:**

- **SQL Dominates Demand:** SQL ranked first with 7,291 appearances.
- **Excel Remains Essential:** Excel ranked second with 4,611 appearances.
- **Python Is Highly Valued:** Python ranked third with 4,330 appearances.
- **Visualization Tools Matter:** Tableau and Power BI completed the top five.

---

### 4. Skills Associated with Higher Salaries

To investigate which skills were associated with higher salaries, I calculated the average annual salary for Data Analyst job postings grouped by skill. I excluded postings without salary information and ranked the results by average salary.

```sql
SELECT
    ROUND(AVG(salary_year_avg), 0) AS avg_salary,
    skills
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```

**Query Output:**

| Skills | Average Annual Salary (USD) |
|---|---:|
| SVN | 400000 |
| Solidity | 179000 |
| Couchbase | 160515 |
| DataRobot | 155486 |
| Golang | 155000 |
| MXNet | 149000 |
| Dplyr | 147633 |
| VMware | 147500 |
| Terraform | 146734 |
| Twilio | 138500 |
| GitLab | 134126 |
| Kafka | 129999 |
| Puppet | 129820 |
| Keras | 127013 |
| PyTorch | 125226 |
| Perl | 124686 |
| Ansible | 124370 |
| Hugging Face | 123950 |
| TensorFlow | 120647 |
| Cassandra | 118407 |
| Notion | 118092 |
| Atlassian | 117966 |
| Bitbucket | 116712 |
| Airflow | 116387 |
| Scala | 115480 |

**Key Findings:**

- **Highest Average Salary:** SVN ranked first at $400,000 per year.
- **Specialized Technologies:** Solidity, Couchbase, DataRobot, and Golang also appeared among the highest-paying skills.
- **Machine Learning Tools:** MXNet, Keras, PyTorch, TensorFlow, and Hugging Face featured in the results.
- **Cloud and Infrastructure:** Terraform, VMware, Ansible, and Cassandra also showed competitive average salaries.


---

### 5. Most Optimal Skills to Learn

To identify skills that combine demand with earning potential, I used two Common Table Expressions (CTEs). The first calculated skill demand among remote Data Analyst postings with salary information, while the second calculated average salaries for those skills.

I combined both results, filtered for skills appearing more than 10 times, and ranked them by average salary followed by demand.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND job_work_from_home = TRUE
        AND salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id,
        skills_dim.skills
),
avg_salary AS (
    SELECT
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim
        ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim
        ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY skills_job_dim.skill_id
)
SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM skills_demand
INNER JOIN avg_salary
    ON skills_demand.skill_id = avg_salary.skill_id
WHERE
    demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 20;
```

**Query Output:**

| Skill ID | Skills | Demand Count | Average Annual Salary (USD) |
|---:|---|---:|---:|
| 8 | Go | 27 | 115320 |
| 234 | Confluence | 11 | 114210 |
| 97 | Hadoop | 22 | 113193 |
| 80 | Snowflake | 37 | 112948 |
| 74 | Azure | 34 | 111225 |
| 77 | BigQuery | 13 | 109654 |
| 76 | AWS | 32 | 108317 |
| 4 | Java | 17 | 106906 |
| 194 | SSIS | 12 | 106683 |
| 233 | Jira | 20 | 104918 |
| 79 | Oracle | 37 | 104534 |
| 185 | Looker | 49 | 103795 |
| 2 | NoSQL | 13 | 101414 |
| 1 | Python | 236 | 101397 |
| 5 | R | 148 | 100499 |
| 78 | Redshift | 16 | 99936 |
| 187 | Qlik | 13 | 99631 |
| 182 | Tableau | 230 | 99288 |
| 197 | SSRS | 14 | 99171 |
| 92 | Spark | 13 | 99077 |

**Key Findings:**

- **Python Has Strong Demand:** Python appeared 236 times, with an average annual salary of $101,397.
- **Tableau Is Widely Requested:** Tableau appeared 230 times, averaging $99,288 per year.
- **R Remains Relevant:** R appeared 148 times, with an average annual salary of $100,499.
- **Cloud Platforms Offer Potential:** Snowflake, Azure, AWS, and BigQuery combine recurring demand with competitive average salaries.
- **Specialized Tools Add Value:** Looker, Oracle, Hadoop, Redshift, and Spark also appeared in the results.



---



## Conclusion

Through this project, I explored the Data Analyst job market using SQL and PostgreSQL to identify high-paying opportunities, understand in-demand skills, analyze salary trends, and determine which skills are worth learning.

The analysis was divided into five key areas:

- **1. Top-Paying Data Analyst Jobs:** Identified the top 10 highest-paying remote Data Analyst job postings, with annual salaries ranging from $184,000 to $650,000. This analysis highlighted the earning potential and variety of opportunities available in the field.

- **2. Skills Required for Top-Paying Jobs:** Connected the highest-paying job postings with their required technical skills. The results highlighted technologies such as SQL, Python, R, Azure, AWS, Pandas, PySpark, and modern data platforms.

- **3. Most In-Demand Skills:** Analyzed remote Data Analyst job postings to identify the five most frequently requested skills. SQL ranked first with 7,291 appearances, followed by Excel (4,611), Python (4,330), Tableau (3,745), and Power BI (2,609). This demonstrates the importance of database querying, data manipulation, and visualization skills.

- **4. Skills Associated with Higher Salaries:** Compared average annual salaries across skills. SVN ranked highest at $400,000, followed by Solidity at $179,000 and Couchbase at $160,515. The findings suggest that specialized technologies can be associated with higher salaries, although the results may be influenced by the number and type of job postings.

- **5. Most Optimal Skills to Learn:** Combined skill demand and average salary to identify potentially valuable skills for remote Data Analyst roles. Python appeared 236 times with an average annual salary of $101,397, while Tableau appeared 230 times with an average of $99,288. Cloud platforms such as Snowflake ($112,948), Azure ($111,225), and AWS ($108,317) also showed promising salary averages alongside recurring demand.



