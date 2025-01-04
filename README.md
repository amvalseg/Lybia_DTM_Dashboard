# Lybia: Internal Displacements and Needs Asseessment Dashboard 🌐

This is a BI project based on DTM (Displacement Tracking Matric) IOM API.

# 1️⃣ Getting Data 🖥️

We access data from the Dataset IOM website [](https://dtm.iom.int/datasets/libya-migrants-baseline-assessment-round-54).
This dataset is not available via API, so we have to download it.

👇 The .xlsx file has the following sheets:

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

- Nationalities 
In this sheet, the total data is disaggregated by Mulhaya into the nationalities of the registered migrant population. Reference columns for the region (Region1) and subregion (Region2) are also included.

# 2️⃣ TRANSFORMING DATA with Python 🐍

After analyzing the dataset structure, the decision was made to focus the analysis on the information contained in the "Mulhaya Main" sheet.

To work with this data, it is necessary to give it a tabular structure and separate the information into different datasets. The column groups mentioned earlier disaggregate totals based on locations (Mulhaya). In this sense, it can be concluded that the data allows for the extraction of multiple fact tables and one common dimension table: geographic location.

With this in mind, the following transformations are carried out:  
- An index is added to the original dataset, which will remain in all tables to enable connections through it.  
- Unnecessary columns are removed.  
- By selecting specific columns from the original dataset, six independent datasets are created: one for each column group and one for geographic information. All datasets share the index.

👀 Once the independent datasets are created, a reshape operation is required for each of them using the **unpivot** process. This transformation converts the column names into values within a single column, while their corresponding values are aggregated into a single total value column.

👉 Notebook: notebooks/Transformations_Lybia project.ipynb

Then, we are redy to work in Power BI ‼️

