# LinkedIn-s-Data-Analyst-Job-Market
## Introduction
The purpose of this project was to **analyze** the job market and gain insights into the **industries** that employ the most **data analysts**, their **geographical distribution**, and the **average salary** of data analysts in the United States. To accomplish this, we utilized **Kaggle data**, which we **cleaned** and **processed**, and then created **visualizations** using **Tableau**.

Understanding the job market for data analysts is crucial in today's data-driven world. By **identifying** the industries with a high demand for data analysts, we can gain valuable insights into **sectors that heavily rely on data analysis for decision-making**. Furthermore, examining the **geographic distribution** of these industries provides insights into **regional concentrations of data analyst job opportunities**. Lastly, exploring the **average salary** of data analysts across the United States gives us an understanding of **earning potential** in this field.

We obtained data from **Kaggle**, a popular platform for accessing and analyzing datasets. After **cleaning** and **preprocessing** the data to ensure its accuracy and consistency, we used **Tableau**, a powerful data visualization tool, to create informative **visualizations**. Through these visualizations, we aimed to highlight the **industries with the highest demand for data analysts, their geographic distribution, and the average salary ranges**.

This project aims to provide aspiring professionals and industry stakeholders with valuable insights into **market trends**, potential **career paths**, and regions that offer promising prospects for data analysts. By analyzing the data and presenting it through visualizations, we hope to contribute to a **better understanding of the data analyst job market in the United States**.

## Data Collection
For this project, the **data collection process** involved retrieving a dataset from Kaggle and importing it into Python for further analysis and cleaning. The dataset used in this project was sourced from LinkedIn, a prominent jobs listing platform. The **objective** was to gather information about job listings and related details to study the market and gain insights into the data analyst profession.

To collect the data, a Python script was developed specifically for **scraping LinkedIn**. The script was designed to navigate through LinkedIn and extract the necessary information from job postings. A poll was conducted to determine the **locations of interest**, and based on the results, three locations were chosen: Africa, Canada, and the United States. Our focus, as we said in the introduction, will be on the US.

The dataset obtained from LinkedIn includes several key features that provide valuable insights into the job market. These features consist of:

1. Title: The job title associated with each listing.
2. Company: The name of the company offering the job.
3. Description: A description of the job and the company.
4. Onsite/Remote: Indicates whether the job can be performed on-site or remotely.
5. Location: Specifies the location where the employee will be working.
6. Salary: The salary range for the job, which may be stated in terms of yearly or hourly rates.
7. Criteria: Job requirements such as experience, employment type, and more.
8. Posted Date: The date when the job was posted.
9. Link: The URL linking to the job posting on LinkedIn.

By utilizing this dataset, we can perform **data cleaning** and **preprocessing tasks** to ensure the data is accurate and suitable for analysis. Once the data is prepared, we can proceed with **visualizations** and **analysis** using tools like Tableau to gain deeper insights into the industries that employ the most data analysts, their **geographical distribution**, and the **average salaries** in the field.

The next steps in this project involve **cleaning the collected data** and creating visualizations to explore and present the findings effectively.

## Data Cleaning
### First look
Initially, the database had numerous null values in the salary field, where salaries were a mix of monthly, annual, and hourly rates. The industries and seniority levels were stored in a list of dictionaries associated with each job offer, further complicating the salary data, which was also entangled within strings.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20115645.png" alt="Image1" width="50%">
</p>

In the beginning of the data cleaning process, we performed several operations to clean and prepare the data for further analysis. Here are the main steps we took:

1. **Removed unnecessary columns**: We dropped the 'link' column from the dataset using the **`drop()`** function.
2. **Removed unwanted rows**: We dropped the first row, which contained column names, using the **`drop()`** function.
3. **Renamed a column**: We renamed the 'onsite_remote' column to 'mode' using the **`rename()`** function.
4. **Cleaned salary data**: We addressed the issue of messy salary data and extracted the relevant salary information. The following code snippet demonstrates the process:

```python
import numpy as np
import re
salaries_da = []
for salary in data['salary']:
    if isinstance(salary, str) and '$' in salary:
        salary_cleaned = salary.replace(',', '').replace('$', '').split()[0].strip('_x000D')
        salary_cleaned = re.sub('[^0-9]','', salary_cleaned) 
        if salary_cleaned:
            salaries_da.append(int(salary_cleaned))
        else:
            salaries_da.append(np.nan)
    else:
        salaries_da.append(np.nan)


data.loc[:, 'salaries'] = salaries_da
```
 
