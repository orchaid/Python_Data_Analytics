# Overview

In this analysis of the data job market espicially data analyst roles in the US, I tried to gain understanding primarily of the skills required to enter the field as an employee for job opportunities while also earning good salaries. This provided me with helpful insights about the industry demands and the prospects of the market in the future. 
The data I used is from [Luke Barousse](https://huggingface.co/datasets/lukebarousse/data_jobs?sql=--+The+SQL+console+is+powered+by+DuckDB+WASM+and+runs+entirely+in+the+browser.%0A--+Get+started+by+typing+a+query+or+selecting+a+view+from+the+options+below.%0ASELECT+*+FROM+train+LIMIT+10%3B) job postings data from Huggingface which contains informations of different job titles, countries, average salaries, the date of the posted job, the skills required in the job post and much more information that I either played with or used in my analysis.

Using a series of Python scripts, I analyze crucial questions like the most in-demand skills, salary trends, and how demand intersects with salary in the field of data analytics. 
# The Questions

1. what are the most demanded skills for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analyst?
3. How well do jobs and skills pay?
4. What are the most optimal skills to learn for Data Analysts?

# Tools I Used

- **Python**: was the core of my analysis, enabling me to process data and extract valuable insights. I worked with the following Python libraries:  
    - **Pandas**: Used for data manipulation and analysis.  
    - **Matplotlib**: To generate visual representations of the data.  
    - **Seaborn**: For crafting more sophisticated and visually appealing charts.  

- **Jupyter Notebooks**: I executed my Python scripts in it which allowed me to seamlessly combine my notes with the analysis.  
- **Visual Studio Code** I primarily used it For coding as it's my preferred editor.  
- **Git & GitHub**: To manage version control and share my work, I relied on it for ensuring smooth collaboration and project tracking.

# Data Prepration and Cleanup

This section shows the code for how I cleaned and filter the data:
### Import & Clean Up Data
```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)

```

### Filtering for US Jobs

```python
# filter for the country
df_US = df[df['job_country']=='United States'].dropna(subset= 'salary_year_avg')
```

# The Analysis

Each Jupyter notebook for this project aimed at investigating specific aspects of the data job market. Here’s how I approached each question:

## 1. what are the most demanded skills for the top 3 most popular data roles?
I created a list of the roles that are posted the most in job sites, and limited it to the top3. I analyzed US job posting data to find the most common skills associated with the top 3 most frequent job titles and visualized these findings in a set of bar charts.
This query provides valuable insights into the skills landscape for specific job titles, empowering individuals to make informed decisions about their career paths, skill development, and job search strategies.  It's a practical application of data analysis that can have a tangible impact on people's professional lives.

view my notebook for more details here: [2_Skill_Demand.ipynb](3_Project\2_Skills_Demand.ipynb)

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles),1, figsize=(9,6))

sns.set_theme(style= 'ticks')

for i, title in enumerate(job_titles):
    df_plots= df_skill_percent[df_skill_percent['job_title_short']== title].head(5)
    sns.barplot(data= df_plots, x= 'skill_percent', y= 'job_skills', hue= 'skill_count', palette='dark:b_r', ax= ax[i], legend=False)

fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize= 17)
fig.tight_layout()
```

### Results:

![Visualization of top skills for data jobs](/3_Project/Images/skills_requested_in_US_job%20postings.png)

*Bar graph visualizing the salary for the top 3 data roles and their top 5 skills associated with each.*
### Insights:


- Data-Driven Roles are Skill-Intensive: All three roles require a diverse set of technical skills, highlighting the increasing demand for specialized expertise in data-related fields.
- SQL is Foundational: SQL (Structured Query Language) is highly prevalent across all roles, emphasizing its fundamental importance for data manipulation and analysis.
- Programming is Crucial: Python is a dominant language, particularly for Data Scientists and Data Engineers, indicating its versatility and power in handling complex data tasks.

**Limitations**:

- The percentages represent the likelihood of skills requested, not necessarily the level of expertise required. A higher percentage doesn't automatically mean mastery is needed.
- The data is specific to US job postings. The skills in demand may vary across different regions and industries.
- The chart only shows the top skills for each role. Other skills may also be relevant but not as frequently mentioned.


## 2. How are in-demand skills trending for Data Analyst?

In this query, I am analyzing the top trending skills for Data Analyst job postings in the United States based on job postings throughout the year. I did that by filtering for data analyst role in the US and their associated skills that are mentioned the most throughout the months of 2023 and then visualize it using a line graph.

View my notebook with detailed steps here: [3_Skills_Trend.ipynb](3_Project\3_Skills_Trend.ipynb)

### Visualize Data
```python
sns.lineplot(data=df_DA_US_percent, dashes=False, legend=False)
sns.despine()

for i in range(5):
    plt.text(11+0.25 , df_DA_US_percent.iloc[-1,i], df_DA_US_percent.columns[i])

from matplotlib.ticker import PercentFormatter
ax=plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))
```

### Results:

![Visualisation of trending top skills for data analysts](/3_Project/Images/trending_skills_throughout_2023.png)
*Line graph visualizing the trending top skills for data analysts in the US in 2023.*
### Insights: 

- SQL is the Most In-Demand Skill – Throughout 2023, with over 50% likelihood in job postings, SQL consistently remained the top skill required for data analyst roles, although it shows gradual decrease of the trend.

- Excel Remains Strong but Fluctuates – Excel showed steady demand, peaking in mid-year and experiencing a decline towards the end, though it rebounded in December.

- Python and Tableau are close – Both Python and Tableau had similar demand trends, with some fluctuations. Tableau saw a decline after mid-year, while gained a slight upward trend towards December.

- Power BI Shows Gradual Growth – It started with the lowest demand but slowly increased over the year.

- Summer Dip in Demand for Some Skills – Around mid-year, SQL and Excel saw slight drops, possibly due to seasonal hiring trends.

## 3. How well do jobs and skills pay?

I analyzed salary distributions for the top data-related jobs in the United States. I filtered then sorted based on data jobs in the US, Then visualized how salaries vary across the most common data-related jobs using a boxplot to compare salary distributions, showing medians, ranges, and potential outliers.

check my notebook for more detailed steps: [4_Salary_Analysis.ipynb](3_Project\4_Salary_Analysis.ipynb)

**Visualize Data**
```python
sns.boxplot(data=df_US_top6, x= 'salary_year_avg', y='job_title_short', order=top6)

ax=plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))
```

**Results**:

![data_jobs_salary_distribution](/3_Project/Images/data_jobs_salary_distribution.png)

*Box plot visualizing the salary distributions for the top 6 data job titles.*

**Insights**:

- Wide Salary Ranges: All roles exhibit large salary spreads, with numerous high-paying outliers, some exceeding $400K.

- Career Growth Matters: Moving into senior positions offers substantial salary increases, Senior roles pay more than their junior counterparts, with Senior Data Scientist and Senior Data Engineer paying the most while also having the most outliers.

- Analyst Roles Pay Less: Data Analysts and Senior Data Analysts have lower median salaries than Data Scientists and Engineers, but seniority still provides a noticeable pay bump.

### Highest Paid & Most Demanded Skills for Data Analysts
In this I dived deeper into data analyst roles to analyze the highest paying skills a data analyst should have and the most demanded, to help land a job.

**Visualize Data**

```python
fig, ax = plt.subplots(2,1)

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_skills_top_median, x='median_salary', y='job_skills', hue= 'median_salary', palette='dark:b_r', ax=ax[0], legend=False)

# Top 10 Most In-Demand Skills for Data Analysts
sns.barplot(data=df_skills_top_count, x='median_salary', y='job_skills', hue= 'median_salary', palette='light:b',ax=ax[1], legend=False)

fig.tight_layout()
plt.show()
```

**Results**:

![Highest paid and most demanded skills](/3_Project/Images/highest_paid_and_most_in-demand_skills.png)

*Two Bar graphs showing the highest paying skills and most in-demand skills for data analysts in the US.*

**Insights**:

- The top graph visualizes the high salary that can be achieved by focusing on specialized tools like Solidity (blockchain), Hugging Face (AI/ML), and advanced cloud computing technologies.

- The bottom graph shows the skills that are the most employable, mastering Python, SQL, and data visualization tools like Tableau and Power BI will give you an edge.

- A balanced skill set combining both high-demand and high-paying technologies can maximize job opportunities and salary potential.

## 4. What are the most optimal skills to learn for Data Analysts?

In this part I tried to find the most optimal skills to learn in terms of how much is that specific skill mentioned in job postings and also that relates to good salary ( the ones that are the highest paid and highest in demand). To do that I focused on data analyst jobs in the US, using a scatterplot to visualize skill percentage in job postings and accosiated salaries.

for more details check my notebook [5_Optimal_Skills.ipynb](3_Project\5_Optimal_Skills.ipynb)

**Visualize Data** 

```python
sns.scatterplot(data= skills_stat, x= 'skill_percent', y= 'median_salary')

ax=plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(x/1000)}K'))

from  matplotlib.ticker import PercentFormatter
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))

plt.show()
```

**Results**:

![Visualisation showing the most optimal skills to learn](/3_Project/Images/optimal_skills_for_data_analysts_US.png)

*Scatterplot showing the mot optimal skills to learn (high demand & high pay) for data anlaysts in the US*

**Insights**: 

- In the higher end of salaries `Python` is associated with one of the highest median salaries (~$98K), but less frequently mentioned than SQL.

- In the higher end of demand `SQL` appears in nearly 60% of Data Analyst job, its median salary (~$90K) is not the highest, suggesting it is a fundamental for job opportunities but not necessarily premium skill.

- `Python`, `Tableau` and `SQL` are on the higher end of both demand and salary, so they are excellent for oppening doors and filling pockets.



### Visualizing Different Techonologies


**Visulaize Data**

```python
sns.scatterplot(
                data= DA_skills_stat,
                x= 'skill_percent',
                y= 'median_salary',
                hue= 'technology', 
                palette='bright'
                )

plt.show()

```

**Results:**

![colored visualization for optimal skills](/3_Project/Images/optimal_skills_for_data_analysts_US_colered_type.png)

**Insights:**
- Programming skills (colored blue) (`Python`, `R`, `Go`, `SQL`) by far offer high salaries with `SQL` and `Python` appearing more frequently in job postings.

- Analyst Tools (colored orange)  (`Excel`, `Power BI`, `Tableau`, `SAS`) Are Essential with Tableau offering the most median salary while `Excel`, `Power BI`  provide moderate salaries and `SAS` appears in niche analytics roles.

- Database (colored green) & cloud tools (colored red) (`Oracle`, `SQL Server`) pay well (~$92K-$96K) but are less common.

# What I learned

During the course of this project, I have enhanced understanding of the data job market using python and now have an idea on how to ask good questions for anlysis and the technical skills needed to answer them with visualization and data manipulation:

- **Critical Role of Data Cleaning**: Ensured accurate insights by prioritizing thorough data preparation before analysis and Developing custom functions to automate data cleaning, reducing manual effort and improving consistency..
- **Strategic Skill Alignment**: Gained a deeper understanding of how skill demand, salary trends, and job availability influence career growth in tech for indivduals and buseinesses.
- **Advanced Python Techniques**: Leveraged Pandas for data manipulation and Seaborn/Matplotlib for visualization, streamlining complex analysis tasks.
- **Bridging Skill Gaps and creative solutions**:  I faced problems while trying to analyze and adapting by learning new libraries and techniques on the fly, ensuring a smooth analytical workflow despite technical challenges.

# Insights

This project revealed several key takeaways about the evolving data job market for analysts:  

- **Skill Value and Compensation:** Higher salaries are often linked to specialized technical skills. Expertise in tools like Python, SQL, and cloud-based analytics can significantly boost earning potential.  
- **Shifting Market Trends:** The demand for certain skills fluctuates over time, emphasizing the need for continuous learning and adaptability to stay competitive.  
- **Maximizing Career Potential:** Identifying which skills are both in high demand and financially rewarding allows analysts to make strategic learning choices for long-term success.  

# Challenges I Faced

- **Creating Meaningful Visualizations:** Translating complex datasets into clear and insightful visual representations was both a challenge and an opportunity. I had to refine my approach to choosing the right charts, applying effective color schemes, and ensuring that visualizations told a compelling, data-driven story.  

- **Managing Data Quality:** Addressing missing values and inconsistencies in datasets required careful preprocessing, including imputation techniques, outlier detection, and standardization to maintain accuracy and reliability in the analysis.  

- **Finding the Right Analytical Depth:** Striking a balance between diving deep into specific aspects of the data and maintaining a high-level perspective was essential. I had to ensure that my analysis remained thorough without missing broader trends.  


# Conclusion

This deep dive into the data analyst job market has been highly insightful, shedding light on the key skills and trends shaping this dynamic field. The insights I’ve gained not only strengthen my understanding but also offer valuable guidance for those looking to grow their careers in data analytics. As the industry evolves, continuous analysis will be crucial for staying competitive and identifying emerging opportunities. 

This project serves as a solid starting point for further exploration, emphasizing the need for ongoing learning and adaptability in the data-driven world. With technology and business needs constantly changing, keeping up with new tools, methodologies, and market demands will be essential for long-term success. By refining my approach and expanding my analysis over time, I aim to uncover deeper trends that can help both aspiring and experienced analysts navigate their career paths effectively.