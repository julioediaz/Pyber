Trend Analysis

From these visualizations, it is clear that urban type cities provide the highest amount of rides at the lowest fare prices. Suburban type cities produce moderate amount of fares at moderate prices. Lastly, rural type cities produce the least amount of rides at the highest fares. There are some noticeable outliers among the set of rural data provided, but there is a clear trend that is prominent in the scatter plot visualization produced. 

* Lower fares driven by mode rides and drivers in urban areas. 
* Rural rides have few drivers but much variability in fares.
* Urban and suburban fares each have an average fare range of 10 dollars.

# Pyber
# Dependencies
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import csv
# Save path to data set in a variable 
city_file = "city_data.csv"
ride_file = "ride_data.csv" 
 Use Pandas to read data
city_file_pd = pd.read_csv(city_file) 
ride_file_pd = pd.read_csv(ride_file)  
#Create count column for ride file
ride_file_pd["Ride Count"] = 1

#Group ride file by city
ride_grouped = ride_file_pd.groupby(["city"], as_index='False')

#Sum fare and ride count columns for grouped ride file 
ride_grouped_summary = ride_grouped["fare", "Ride Count"].sum().reset_index('city')

#Add average ride fare column
ride_grouped_summary["Average Ride Fare"] = ride_grouped_summary["fare"]/ride_grouped_summary["Ride Count"]
ride_grouped_summary.head(5) 

Merge grouped ride file and city file
city_ride = pd.merge(city_file_pd, ride_grouped_summary, on="city", how="left") 

#Create area column for scatter plot marker size scale
city_ride["area"] = city_ride["driver_count"]**1.75 

#Add coloumn to hold color value for city type 
city_ride["color"] = city_ride["type"]
city_ride["color"] = city_ride["color"].replace({"Urban": "salmon", "Rural": "lemonchiffon", "Suburban": "lightblue"}) 

#Group city_ride file by type
type_grouped = city_ride.groupby(["type"], as_index='False')

#Sum fare and ride count columns for grouped type file 
type_grouped_summary = type_grouped["fare", "Ride Count"].sum().reset_index('type')
type_grouped_summary.head() 

#Create scatter plot
plt.scatter(city_ride["Ride Count"], city_ride["Average Ride Fare"], marker ="o", c=city_ride["color"],
            edgecolors="black",s=city_ride["area"], alpha=0.75)
plt.title("Pyber Ride Sharing Data")
plt.xlabel("Total Number of Ride (Per City)")
plt.ylabel("Average Fare ($)")
plt.legend(type_grouped_summary["type"], loc="best")

#Set x and y axis range
plt.ylim(15, 55)
plt.xlim(0, 40) 

#Set gid style
plt.style.use('ggplot') 

#Set size of grid
fig_size = plt.rcParams["figure.figsize"]
fig_size[0] = 10
fig_size[1] = 8
plt.rcParams["figure.figsize"] = fig_size
plt.show() 

#Group city table by type
city_group_type = city_ride.groupby(["type"], as_index='False')

#Sum driver count, fare and ride count in city table grouped by type
city_group_type_summary = city_group_type["driver_count", "fare", "Ride Count"].sum().reset_index('type')

#Create color
colors = ["lemonchiffon", "lightblue", "coral"]
explode = [0, 0, 0.1]

#Plot pie chart for  the city table grouped by type for fare
plt.pie(city_group_type_summary["fare"], labels=city_group_type_summary["type"], colors=colors, explode=explode,
        autopct="%1.1f%%", shadow=True, startangle=120)
plt.title("% of Total Fares by City Type")
plt.show() 

#Plot pie chart for the city table grouped by type for total rides
plt.pie(city_group_type_summary["Ride Count"], labels=city_group_type_summary["type"], colors=colors, explode=explode,
        autopct="%1.1f%%", shadow=True, startangle=120)
plt.title("% of Total Rides by City Type")
plt.show() 

#Plot pie chart for the city table grouped by type for total rides
plt.pie(city_group_type_summary["driver_count"], labels=city_group_type_summary["type"], colors=colors,
        explode=explode, autopct="%1.1f%%", shadow=True, startangle=120)
plt.title("% of Total Drivers by City Type")
plt.show() 
