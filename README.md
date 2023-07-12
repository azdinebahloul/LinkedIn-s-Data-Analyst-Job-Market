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
To address the first big issue of messy salary data surrounded by unwanted characters, we cleaned the values by removing **commas**, **dollar signs**, and other extraneous characters. We retained only the **numeric digits** using **regular expressions**. The cleaned salary values were then added as a new column called "**salaries**" in the dataframe. This process allowed us to extract and include the relevant salary information for further analysis and visualization.

After performing **surface-level** data cleaning on our database, it became apparent that further in-depth cleaning was required to address specific issues we had identified.

### Mixed Messy Salary
We had an issue with the salary data as it contained multiple types of salaries (hourly, monthly, etc.) within the same column. By viewing the salary distribution, we could see the 3 salary categories stand out in the distribution.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Untitled.png" alt="Image2" width="50%">
</p>

We then created a function to create three new columns for each type of salary with the following code:
```python
def classify_salary(row):
    salaire = row['salaries']
    if pd.isnull(salaire):
        return pd.Series({'Annual_Salary': None, 'Hourly_Salary': None, 'Monthly_Salary': None})
    elif salaire >= 10000:
        return pd.Series({'Annual_Salary': salaire, 'Hourly_Salary': None, 'Monthly_Salary': None})
    elif salaire < 100:
        return pd.Series({'Annual_Salary': None, 'Hourly_Salary': salaire, 'Monthly_Salary': None})
    else:
        return pd.Series({'Annual_Salary': None, 'Hourly_Salary': None, 'Monthly_Salary': salaire})


df[['Annual_Salary', 'Hourly_Salary', 'Monthly_Salary']] = df.apply(classify_salary, axis=1)

df.iloc[:,8:]
```

We then used the describe() function to check the data consistency and identify outliers in each salary category. This allowed us to validate the data and detect any unusual values that may require additional investigation.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20142216.png" alt="Image3" width="50%">
</p>

Now, if we look for example at the extreme values or the average of each type of salary, we can see that it makes sense.

### Crtieria Extraction
As we can see, the 'criteria' column contained a list of dictionaries, where each dictionary represented a specific criterion related to job postings. Our goal was to extract the information from these dictionaries and transform them into separate columns.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20143134.png" alt="Image4" width="50%">
</p>

We wrote a code that parsed each dictionary within the 'criteria' column and extracted the values associated with the respective keys. These values were then assigned to new columns, such as 'Seniority Level', 'Employment Type', 'Job Function', and 'Industries'. By doing so, we transformed the nested structure of the 'criteria' column into a more structured tabular format.

```python
def extract_values(row):
    criteria_dict = eval(row['criteria']) 
    extracted_values = {}
    for d in criteria_dict:
        extracted_values.update(d)
    return pd.Series(extracted_values, dtype='object')
```

We performed this data transformation for several reasons. Firstly, by extracting the criteria into separate columns, we improved the accessibility and ease of analysis of the dataset. Each criterion now has its dedicated column, allowing for straightforward filtering, sorting, and aggregating based on specific criteria.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20143308.png" alt="Image5" width="50%">
</p>

Secondly, this transformation enables more meaningful and granular analysis of the job postings. With the criteria separated into individual columns, we can easily examine patterns, distributions, and relationships within each criterion. This provides valuable insights into the characteristics of the job postings and allows for a more comprehensive understanding of the dataset.

### Missing Values
We have addressed the issue of **null values** in the "Seniority level" column by **replacing** them with "Not Applicable" using the **`fillna()`** function. This step ensures that all entries in the "Seniority level" column have a meaningful representation.

To tackle the problem of missing values in the "Employment type," "Industries," and "Job function" columns, a similar approach was applied.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20144001.png" alt="Image6" width="50%">
</p>

We replaced empty spaces with the value "Not specified" to indicate missing information consistently. This modification guarantees that there are no null or empty values in these columns, facilitating the analysis process.

```python
column_to_replace = ["Employment type", "Industries", "Job function"]
df[column_to_replace] = df[column_to_replace].fillna("Not specified")
```

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20144207.png" alt="Image7" width="50%">
</p>

With these updates, the database now provides a more organized and comprehensive representation of the data. The cleaned dataset enables smoother analysis and decision-making by ensuring that essential information is available or clearly marked as missing in the respective columns.

### Text Extraction
To explore the significance of **business intelligence (BI) tools** in the dataset, we implemented a specific approach. First, we created a new column called "**BI_tools_condition**" to capture whether the job descriptions mention the presence of "**Tableau**" or "**Power BI**" skills. This column serves as a binary indicator, where a value of **1** indicates that at least one of the tools is mentioned in the job description, while a value of **0** suggests that neither tool is mentioned.

By introducing the "**BI_tools_condition**" column, we can easily identify job postings that require proficiency in **Tableau** or **Power BI**. This information proves valuable when studying the demand for these BI tools in the dataset.

```python
def check_bi_tools(description):
    if 'Tableau' in description or 'Power BI' in description:
        return 1
    else:
        return 0


df['BI_tools_condition'] = df['description'].apply(check_bi_tools)
```

Furthermore, we followed a similar process to create another column that evaluates whether job postings require skills in SQL or Python. This additional column helps determine the prevalence and importance of SQL and Python knowledge in the field of data.

By incorporating these new columns, we gain insights into the frequency and relevance of specific BI tools like Tableau and Power BI, as well as the demand for SQL and Python skills in the job postings. These insights contribute to a more comprehensive analysis of the dataset and aid in understanding the role of BI tools and programming languages in the data industry.

## Data Visualization
During our comprehensive exploration of the dataset, we delved into several key aspects to unravel the insights hidden within. As seasoned data analysts, we employed a range of visualizations to enhance our storytelling and provide a cohesive narrative on the US data analyst market. Let's delve into the fascinating findings.

To begin, we created a compelling **pie chart** to visually capture the **distribution of job modes** prevalent in our dataset. The chart elegantly showcased the different modes - remote, hybrid, and onsite - and offered a concise overview of their presence.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20150353.png" alt="Image8" width="50%">
</p>

What struck us as intriguing was the nearly **balanced distribution** among the three modes, implying a significant representation of remote, hybrid, and onsite job opportunities. This discovery highlights the remarkable **availability** and **diversity** of job modes within our dataset, painting a vivid picture of the US data analyst job market.

Moving forward, our analysis turned towards understanding the salary landscape within the dataset. By considering various salary variables, such as annual, hourly, and monthly wages, we calculated the average annual salary to gauge the overall compensation range. The resulting figure, approximately **$89,000**, served as a reliable benchmark for assessing salary levels across different job postings.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20150519.png" alt="Image9" width="50%">
</p>

Notably, our findings aligned harmoniously with renowned studies, affirming the accuracy and credibility of our dataset. The average salary falling within the expected range, $90,000, bolstered our confidence and propelled us towards uncovering the intricate relationship between job requirements, experience, and compensation.

Our exploration journey then took a **geographical** turn, as we sought to examine the distribution of job opportunities across different **locations**. Through the creation of a captivating clustered bar chart, we visualized the frequencies of job postings in various regions, shedding light on the landscape of the US data analyst market.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20150646.png" alt="Image10" width="50%">
</p>

The chart showcased certain regions that exhibited a noteworthy concentration of job opportunities, with **New York**, **Chicago**, **Austin**, and **Texas** taking the spotlight. These locations stood as beacons of bustling job markets, promising a wealth of enticing prospects for data analysts. These findings opened up exciting avenues for further investigation into the factors contributing to the thriving job markets in these regions.

In addition, we harnessed the power of a **horizontal bar chart** to analyze the prevalence of **different job titles** within our dataset. This dynamic visualization succinctly presented the frequencies of various job titles, allowing us to discern the most frequently occurring roles.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20150851.png" alt="Image11" width="50%">
</p>

Upon careful examination, it became evident that the job title "**Data Analyst**" reigned supreme, capturing the **highest occurrence** among the postings. Following closely were "**Data Analyst - Recent Graduate**" and "**Junior Data Analyst**," signifying their prominence in the current job market. These insights offered valuable glimpses into the positions most sought after by employers, illuminating the demand landscape for data analysts.

Moreover, we employed a visually striking **vertical bar chart** to unravel the **distribution of seniority levels** within the job postings. The chart revealed intriguing patterns, shedding light on the levels of experience sought by employers.

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-06-21%20151132.png" alt="Image12" width="50%">
</p>

"Not Applicable" emerged as the most prevalent seniority level, indicating a significant number of positions that did not specify explicit seniority requirements. Trailing closely were the seniority levels of "Mid-Senior Level" and "Associate," signifying a moderate level of experience sought by employers. Interestingly, the dataset presented a rare opportunity with only one job posting specifying an "Executive" seniority level. These valuable insights provided a comprehensive understanding of the diverse seniority levels associated with the available job positions.

## Conclusion
In conclusion, our in-depth analysis of the data analyst market in the United States has provided **valuable insights**. Through captivating visualizations, we have uncovered key trends and distinctive features of this thriving market.

The examination of job modes revealed a balanced distribution between **remote**, **hybrid**, and **onsite** work options, highlighting the availability and flexibility of employment opportunities for data analysts.

Regarding compensation, our findings confirmed the credibility of our dataset, with an average annual salary of approximately **$89,000** falling within the expected range. This indicates that data analysts enjoy competitive and stable remuneration in the market.

The geographic distribution of job opportunities identified key regions such as **New York**, **Chicago**, **Austin**, and **Texas**, which offer vibrant ecosystems for data analysts. These areas concentrate a significant number of job opportunities, suggesting sustained demand in these regions.

The most frequent job titles were dominated by "**Data Analyst**," closely followed by "**Data Analyst - Recent Graduate**" and "**Remote Data Analyst**." These results highlight the positions most sought after by employers, providing valuable insights into the current demand for data analysts in the job market.

Regarding required experience levels, most job postings did not specify a particular seniority level, followed by "**Mid-Senior Level**" and "**Associate**" levels as the most common. "**Executive**" level opportunities were relatively rare in our dataset. This information allows data analysts to align their expectations regarding the desired level of experience sought by employers.

Ultimately, this detailed analysis of the data analyst market in the United States underscores the vitality of this profession and the abundance of available opportunities. Data analysts can explore flexible work modes, benefit from competitive compensation, and choose from diverse regions offering promising career prospects. These insightful findings provide a solid foundation for data analysts looking to engage in the thriving data analysis market in the United States.

## Dashboard

<p align="center">
  <img src="https://github.com/azdinebahloul/LinkedIn-s-Data-Analyst-Job-Market/blob/main/Image/Capture%20d'%C3%A9cran%202023-05-25%20155549.png" alt="Image13" width="50%">
</p>







