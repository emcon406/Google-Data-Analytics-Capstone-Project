# Google Data Analytics Capstone Project:
### How does a bike-share navigate speedy success?

***This is a consolidated version of the R-code used to answer the business task of "How do casual riders and annual members use Cyclistic bikes differently?"***

## Load Packages
```{r Loading Packages}
library(tidyverse)  #helps wrangle data
library(conflicted) # Use the conflicted package to manage conflicts
```

## Step 1: Collect Data
```{r Collect Data}
q1_2019 <- read_csv("Divvy_Trips_2019_Q1.csv")
q1_2020 <- read_csv("Divvy_Trips_2020_Q1.csv")
```

## Step 2: Wrangle and Combine Data
A) Rename columns for future stacking and joining 
```{r rename}
colnames(q1_2019) # Compare column names each of the files
colnames(q1_2020) # While the names don't have to be in the same order, they DO need to match perfectly before we can use a command to join them into one file

(q1_2019 <- rename(q1_2019
                   ,ride_id = trip_id
                   ,rideable_type = bikeid
                   ,started_at = start_time
                   ,ended_at = end_time
                   ,start_station_name = from_station_name
                   ,start_station_id = from_station_id
                   ,end_station_name = to_station_name
                   ,end_station_id = to_station_id
                   ,member_casual = usertype
)) # Rename columns  to make them consistent with q1_2020 (as this will be the supposed going-forward table design for Divvy)
```

B) Inspect dataframe and look for incongruencies
```{r inspect}
str(q1_2019)
str(q1_2020)
```

C) Convert names for stacking
```{r convert}
# Convert ride_id and rideable_type to character so that they can stack correctly
q1_2019 <-  mutate(q1_2019, ride_id = as.character(ride_id)
                   ,rideable_type = as.character(rideable_type))
```

D) Stack the two dataframes
```{r stack}
# Stack individual quarter's data frames into one big data frame
all_trips <- bind_rows(q1_2019, q1_2020)#, q3_2019)#, q4_2019, q1_2020)
```

E) Drop any unuseful/unnecessary information
```{r dropping information that isnt useful}
# Remove lat, long, birthyear, and gender fields as this data was dropped beginning in 2020
all_trips <- all_trips %>%  
  select(-c(start_lat, start_lng, end_lat, end_lng, birthyear, gender,  "tripduration"))
```

## Step 3: Clean Up and Add Data to Prepare for Analysis
A) Inspect New Table
```{r inspect}
colnames(all_trips)  #List of column names
nrow(all_trips)  #How many rows are in data frame?
dim(all_trips)  #Dimensions of the data frame?
head(all_trips)  #See the first 6 rows of data frame.  Also tail(all_trips)
str(all_trips)  #See list of columns and data types (numeric, character, etc)
summary(all_trips)  #Statistical summary of data. Mainly for numerics
```

B) Reassign and Check
```{r reassign and check}
# Reassign to the desired values (we will go with the current 2020 labels)
all_trips <-  all_trips %>% 
  mutate(member_casual = recode(member_casual
                                ,"Subscriber" = "member"
                                ,"Customer" = "casual"))

# Check to make sure the proper number of observations were reassigned
table(all_trips$member_casual)
```

C) Add Columns to Allow Aggregation of Data
```{r add}
# Add columns that list the date, month, day, and year of each ride
# This will allow us to aggregate ride data for each month, day, or year ... before completing these operations we could only aggregate at the ride level
all_trips$date <- as.Date(all_trips$started_at) #The default format is yyyy-mm-dd
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```

D) Perform Calculations and Inspect Columns
```{r calculate and inspect and calculate}
all_trips$ride_length <- difftime(all_trips$ended_at,all_trips$started_at) # Add a "ride_length" calculation to all_trips (in seconds)

str(all_trips) # Inspect the structure of the columns

# Convert "ride_length" from Factor to numeric so we can run calculations on the data
is.factor(all_trips$ride_length)
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```
E) Remove Bad Data and Create New Version
```{r remove and create V2}
all_trips_v2 <- all_trips[!(all_trips$start_station_name == "HQ QR" | all_trips$ride_length<0),] # We will create a new version of the dataframe (v2) since data is being removed
```

## Step 4) Conduct Descriptive Analysis 

A) Ride Length Analysis
```{r analysis}
mean(all_trips_v2$ride_length) #straight average (total ride length / rides)
median(all_trips_v2$ride_length) #midpoint number in the ascending array of ride lengths
max(all_trips_v2$ride_length) #longest ride
min(all_trips_v2$ride_length) #shortest ride

summary(all_trips_v2$ride_length) # You can condense the four lines above to one line using summary() on the specific attribute
```

B) Compare Members and Casual Riders
```{r members vs. casual}
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)

aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean) # See the average ride time by each day for members vs casual users

all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday")) # Notice that the days of the week are out of order. Let's fix that.
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean) # Now, let's run the average ride time by each day for members vs casual users
```
C) Analyze Ridership Data
```{r ridership data}
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  #creates weekday field using wday()
  group_by(member_casual, weekday) %>%  #groups by usertype and weekday
  summarise(number_of_rides = n()							#calculates the number of rides and average duration 
            ,average_duration = mean(ride_length)) %>% 		# calculates the average duration
  arrange(member_casual, weekday)								# sorts
```
D) Create Visualizations
```{r viz}
# Let's visualize the number of rides by rider type
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")

# Let's create a visualization for average duration
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")
```

## Step 5) Create and Export Summary File
```{r summary file}
counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
write.csv(counts, file = 'avg_ride_length.csv')

write.csv(counts,"C:\\Users\\Emma_Pavlik\\Desktop\\Bike_Share_dataset.csv")

```












