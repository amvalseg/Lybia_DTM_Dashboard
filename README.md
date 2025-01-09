# Lybia: Internal Displacements and Needs Asseessment Dashboard 🌐

This is a BI project based on open-source data from the Internacional Organisation for Migration's Displacement Tracking Matrix (IOM DTM): [Lybia DTM Dashboard](https://app.powerbi.com/view?r=eyJrIjoiMDI5MmFjODQtMDYwNy00OGQwLTkyNDYtM2JiODk5ZDI1ZWIyIiwidCI6ImFhMjRkMzA4LTBkOWUtNDY5NS1hMjZmLTc1NDU5NDM0MmUxYiIsImMiOjl9)

In 2016, the International Organization for Migration (IOM) launched the Displacement Tracking Matrix (DTM) in Libya to address critical gaps in data and analysis related to migration and internal displacement. Having reliable, data-driven insights is essential for policymakers and practitioners to make informed decisions on migration and displacement issues within the country.

The DTM has become a key resource for understanding migration patterns to, from, and within Libya. Through mobility tracking, flow monitoring, emergency tracking, rapid humanitarian needs assessments, and in-depth research, the DTM provides comprehensive evidence to support effective programming. Data is collected regularly through a network of 90 field enumerators and over 2,200 key informants across Libya. This work contributes to evidence-based program development and is closely aligned with the efforts of the Project Development Support Unit.

For further information, consult here:[DTM Lybia](https://libya.iom.int/displacement-tracking-matrix-and-research. "DTM Lybia")

# 1️⃣ Getting Data 🖥️

We access data from the Dataset IOM website [](https://dtm.iom.int/datasets/libya-migrants-baseline-assessment-round-54).
This dataset is not available via API, so we have to download it.

👇 The [.xlsx file](https://github.com/amvalseg/Lybia_DTM_Dashboard/blob/main/data/DTM_Libya_R54_Migrant_Dataset_PUBLIC.xlsx) has the following sheets:

🟡 Summary
In this sheet, various tables are presented with aggregations and percentage calculations based on the dataset (Regions, Nationalities, Accommodation, Demographics, Needs...).

🟡 Baladiya Main
In this sheet, the global figures of registered individuals are displayed down to the third administrative level (Baladiya-Area). It includes the latitude and longitude of these areas, along with their names in English and Arabic.

🟡 Mulhalla Main
In this sheet, an additional territorial level is included (Mulhaya), and the totals are further broken down across several column groups:

◽ Migrant Total Figures: Shows the total number of individuals (as in the previous sheet).
◽ Migrant Shelter Settings: Breaks down the total number of individuals into different accommodation categories. Each column represents a type of shelter.
◽ Migrant Arrival Time: Breaks down the total number of individuals across different time periods of arrival in the country. Each column represents a time frame.
◽ Residency Permits: Breaks down the total number of individuals into different administrative situations. Each column represents an administrative status.
◽ Demographic Breakdown of Migrant Population: Breaks down the total number of individuals into three categories: adult men, adult women, and minors.

🟡 Nationalities 
In this sheet, the total data is disaggregated by Mulhaya into the nationalities of the registered migrant population. Reference columns for the region (Region1) and subregion (Region2) are also included.

# 2️⃣ Transforming Data with Python 🐍

After analyzing the dataset structure, the decision was made to focus the analysis on the information contained in the "Mulhaya Main" sheet.

To work with this data, it is necessary to give it a tabular structure and separate the information into different datasets. The column groups mentioned earlier disaggregate totals based on locations (Mulhaya). In this sense, it can be concluded that the data allows for the extraction of multiple fact tables and one common dimension table: geographic location.

With this in mind, the following transformations are carried out:  
- An index is added to the original dataset, which will remain in all tables to enable connections through it.  
- Unnecessary columns are removed.  
- By selecting specific columns from the original dataset, six independent datasets are created: one for each column group and one for geographic information. All datasets share the index.

### 👀  For each dataset, the following transformations are applied as needed:

- Once the independent datasets are created, a reshape operation is required for each of them using the **unpivot** process. This transformation converts the column names into values within a single column, while their corresponding values are aggregated into a single total value column.
- Value replacement: In cases where excessively long values or spelling errors are identified.
- Column renaming: To ensure naming consistency across different datasets.

👉 Notebook: [notebooks/Transformations_Lybia project.ipynb](https://github.com/amvalseg/Lybia_DTM_Dashboard/blob/main/notebook/Transformations_Lybia%20project.ipynb)

Then, we are redy to work in Power BI ‼️


# 3️⃣ Dimensional Modeling in Power BI

For this project, a simple data modeling approach is implemented in Power BI. The main feature of this project is the presence of multiple fact tables sharing a single dimension table.  

The fact tables are derived from splitting the original dataset: **needs**, **adminsit**, **shelter**, **arrivaltime**, and **demographics**.  

The dimension table, **geo**, is connected to all the fact tables through one-to-many relationships.  

![image](https://github.com/user-attachments/assets/27fa8533-e2ec-4914-83f3-18b3a0898406)

As for the measures, since this is a descriptive dashboard, only three measures are created to calculate the total number of migrants by region.

Additionally, the "Priority Level" parameter is created to efficiently manage the filtering of the needs and associated issues table based on the priority levels assigned by the interviewed individuals.


I hope you find this project interesting. Feel free to reach out and share your thoughts!

