# Lybia: Internal Displacements and Needs Asseessment Dashboard 🌐

This is a BI project based on DTM (Displacement Tracking Matric) IOM API.

# 1️⃣ Getting Data 🖥️

We get the data from the Refugee Statistics API (https://api.unhcr.org/docs/).

👉 You wil find the query in this repo: notebooks/API UNCHR_Request.ipynb

**Please note:** When performing a large query, such as one covering 14 years, it is advisable to split it into at least two parts. Specifically, I executed one query for 2010 to 2018 and another for 2019 to 2024. Simply adjust the parameters as needed.


# 2️⃣ TRANSFORMING DATA with Python 🐍

We examine the data structure, identify and carry out the necessary transformations before connecting it to Power BI.

Basically, we rename columns and map values in specific columns to more descriptive names. Summary of Methods/Functions Used:
- dict: Used to define mappings for renaming columns and updating values.
- rename(): Renames columns of the DataFrame based on a mapping dictionary. For example, 'year' is renamed to 'Year', and 'coo_id' becomes 'Country of Origin ID'.
- replace(): Apply value mappings to specific columns using the mappings dictionary. For example, "Procedure Type": {"G": "Government", "J": "Joint", "U": "UNHCR"} maps "G" to "Government", "J" to "Joint", and "U" to "UNHCR".

At the end of the process, we get the final dataset: Asylum Requests_2010_2024_vf.csv

👉 Notebook: notebooks/Concat and Transform.ipynb
You can find the reference documentation here: https://www.unhcr.org/refugee-statistics/methodology/data-content

# 3. Complementary Data: 🖥️
In the original dataset, we only have the data based on countries. In order to work with Regions and Subregions, we need to use another dataset that includes the ISO codes, the country name, the region name and the subregion name.

👉 You can fin this dataset in this repo: Table of countries by UNCHR_modified 

The original version can be found here: https://www.unhcr.org/refugee-statistics/insights/annexes/trends-annexes.html?situation=3

🧹 This dataset is manipulated solely in Power BI, as it primarily requires only column name adjustments. Specifically, the columns are modified as follows:

A. In the query for countries of origin:

Table.RenameColumns(#"Tipo cambiado1",{{"UNSD Name", "Origin Country  Name"}, {"UNSD Region name", "Origin Country Region"}, {"UNSD Sub-region name", "Origin Country Sub-region"}})

B. In the query for countries of destination:

= Table.RenameColumns(#"Tipo cambiado1",{{"UNSD Name", "Reception Country Name"}, {"UNSD Region name", "Reception Country Region"}, {"UNSD Sub-region name", "Reception Country Sub-region"}})

# 4.  Data Modeling ⚙️

When creating the data model in Power BI, the most important characteristic of this dataset is that a single country, region, or subregion can be both an origin and a destination, in all possible combinations. For example, we may want to explore applications from Senegal (origin) to other countries (destination), both within its own region and across all regions worldwide. Additionally, we may want to explore the data from the perspective of a single region: key origin countries within the region, main destination countries for applications from the region, and key destination countries within the region itself.

To allow for this flexibility in analysis, the fact table is set up by keeping the columns for Year, Country of Origin, Country of Asylum, Applied, Decision Level, Procedure Type, Application Type, and Application PC.

Moreover, the complementary region and subregion data table is added twice. One instance is configured as the origin dimension table and the other as the destination dimension table.

The relationship with the fact table is established through the columns containing the country's ISO code: **Country of Origin ISO** and **Country of Asylum ISO**, linking to the dimension tables in a many-to-one relationship.

In this way, several columns present in the initially imported fact table can be removed. Specifically, the following columns are deleted: "Country of Origin ID", "Country of Origin Name", "Country of Origin", "Country of Asylum ID", "Country of Asylum Name", "Country of Asylum", and the "Unnamed: 0" column (generated as an automatic index during the transformations with Python).

# 5. Repport Approach 👀

This report follows an exploratory approach, allowing the user to gain insights based on their specific interests. For this reason, a multi-page layout has been chosen:

🔵 "Let's Explore" page allows the user to select origin and/or destination countries and/or regions, and view both the figures by country and their evolution over time (2010-2024).

🔵 "Global Overview" page displays global KPIs (total applications for the period and current year), the trend of the number of applications over time, and tables showing the top origin and destination countries for each year.

🔵 "Regional Overview", the proportion of applications by both origin and destination region is shown, along with trends over the years of the report. Additionally, a correlation matrix between origin and destination regions is included.
To explore from the perspective of a particular region, the approach is based on a 1-region:1-page model. This approach avoids changing filters for both origin and destination and allows the user to focus on the information.

🔵 The pages dedicated to each region (Africa, Americas, Asia, Europe, and Oceania) display the regions where the applications from that region are directed, the applications made by people from that region abroad, and the applications received within the region. Similarly, the countries that have received applications from countries in that region, the origin countries from that region, and the destination countries within the region are shown. All of these metrics are presented both with total figures per country and the percentage figures.


# 6. Measures ✳️

For this report, three types of measures have been created:

➕ **Aggregation:** This includes measures for the Total Number of Applications, as well as the total number of applications for each region, both for origin and destination. In total, there are 11 measures, as there are 5 regions that can be analyzed as both origin and destination.

🔝 **Top N:** This includes the measures for Top Origin Country by Year and Top Reception Country by Year, along with the measures Sum of Applied for Top Origin Country and Sum of Applied for Top Reception Country. These measures allow us to create a table with the country with the most applications per year, both for origin and reception.

➗ **Percentage:** The Percentage measures are used to show the share of applications from each region in relation to the total number of applications. These measures provide insights into the proportion of requests coming from specific origin or destination regions relative to the global totals.

There is one measure for each region as an origin region and one for each region as a destination region.
   
# 7.  Custom Shape Map 🗺️

To apply the world map to the Shape Map visual, we customized the visual using the world-countries.json file. However, when applying it, we discovered that some countries were not being detected. This issue relates to the country names in the origin and reception tables.

We identified the countries that were not being highlighted and replaced their names in the dimension tables so that they match the names in the GeoJSON file. Specifically, the following values were replaced:

    Replace = {
        {"Venezuela (Bolivarian Republic of)", "Venezuela"},
        {"United Kingdom of Great Britain and Northern Ireland", "United Kingdom"},
        {"(Plurinational State of)", " "},
        {"(Islamic Republic of)", ""},
        {" Special Administrative Region", ""},
        {"Democratic Republic", "Dem. Rep."},
        {"Côte d'Ivore", "Ivory Coast"},
        {"Russian Federation", "Russia"},
        {"Dem. Rep. of the Congo", "Democratic Republic of the Congo"},
        {"Côte d'Ivoire", "Ivory Coast"},
        {"Côte d’Ivoire", "Ivory Coast"},
        {"Guinea-Bissau", "Guinea Bissau"},
        {"Syrian Arab Republic", "Syria"},
        {"Congo", "Republic of the Congo"},
        {"Democratic Republic of the Republic of the Congo", "Democratic Republic of the Congo"},
        {"Nort Macedonia", "Macedonia"},
        {"Czechia", "Czech Republic"},
        {"Serbia", "Republic of Serbia"},
        {"North Macedonia", "Macedonia"},
        {"Republic of Moldova", "Moldova"},
        {"Democratic People's Republic of Korea", "North Korea"},
        {"Republic of Korea", "South Korea"},
        {"Viet Nam", "Vietnam"},
        {"Lao People's Dem. Rep.", "Laos"}
    }

By doing this, we ensured that the country names in the data matched the GeoJSON references, allowing the Shape Map visualization to correctly recognize and display all the countries.

This process ensures that the map visualizations are accurate, and all countries in the dataset are properly marked.

