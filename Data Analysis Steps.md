# Google Data Analytics Capstone Project:
### How does a bike-share navigate speedy success?


## Steps:
### 1) Ask
- **Buisness Task:** Recommend marketing strategies directed towards converting casual riders into annual members by better understanding how casual riders and annual members use Cyclistic bikes differently.
- **Stakeholders:** Lily Moreno, my manager and head of marketing. Cyclistic marketing analytics team,  they have a mission and goals for Cyclistic. Cyclistic Executive team, the team that will approve or deny the marketing program, detail-oriented. 

### 2) Prepare
- **Data Source:**  Cyclistic’s historical [trip data](https://divvy-tripdata.s3.amazonaws.com/index.html) from the last 12 months. The data is organized in monthly files, January 2020 through December 2020 was used for analysis during this process. 
- **ROCC:** This data set does rocc, it is reliable because it has more than 30 users. It is original because it is coming straight from the source of Cyclistic. It is comprehensive because it is stored in csv files that are easy to understand and store. It is current because the site is updated and has new monthly files. It is cited because it is made available through Motive International Inc. under [this](https://divvybikes.com/data-license-agreement) license.
- **Limitations:** There are data-privacy issues that prohibit use of riders’ personal information. Therefore you cannot connect past purchases to credit card numbers to determine the Cyclistic service area of casual riders, or if they have purchased multiple single passes. 

### 3) Process
- **Choose your tools:** the dataset is a cvs file, so excel/spreadsheets could be used, however, it is a large dataset that excel/spreadsheets may not be able to handle. Therefore, I chose Rstudio.
- **Transform the data so you can work with it effectively:** data migration and data wrangling
- **Check the data for errors:** compare column names, rename, inspect for incongruencies, etc.
- **Document the cleaning process:** convert data to stack correctly, convert into one big data frame.

### 4) Analyze
- Aggregate the data and perform calculations to find trends and relationships. 
  - Consolidate columns 
  - Add new columns to help further aggregate 
  - Create consistency throughout 
  - Convert from factor to numeric
  - Check for bad data, such as bikes checked out by bike share for maintenance. 
- Found mean, median, max and min, and used that to compare casual and members.
- Fixed out of order days of the week. 
- Relationship by type and weekday. 
- Created visualization for average duration. 

### 5) Share
- Create effective data visualizations: prepare data visualizations supporting the trends found using R studio and Tableau.
- Average Duration vs. Weekday [Rplot.pdf](https://github.com/user-attachments/files/17000176/Rplot.pdf)
- Number of Rides vs. Weekday [# of rides.pdf](file:///C:/Users/TNO/Downloads/%23%20of%20rides.pdf)
- Tableau Bike Share Visualizations [here](https://public.tableau.com/views/CapstoneVisualizations_17262700914300/Story1?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

### 6) Act
- **Recommendations**
 
