# EDA-using-Python
Exploratory Data Analysis using Python on a Sales dataset from a company from different cities in USA.

Quick look to the data 
This dataset is from the course that I mentioned in above. The purpose of the dataset is to have insight of superstore'sales. In this dataset our target variable is "Sales".

Questions to be answered ¶
What day of the week are our sales high on average?

What is monthly average sales?

What are the year over growth year/ total sales over the years?

Who are the top customer of all times?

What are the highest sales by category?

Which city does have the highest sales?


import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt
from datetime import timedelta
import warnings
warnings.filterwarnings("ignore")

data=pd.read_csv(r"C:\Users\Lily\Desktop\Python projects\datasets\Customer data.csv")
data.head()

data.dtypes

#I need to change data type for "order date" and "ship date" to datetime
data["Order Date"]=pd.to_datetime(data["Order Date"])
data["Ship Date"]=pd.to_datetime(data["Ship Date"])
data.dtypes

#creating a new column of shipping time frame to get the time spent between ordering and shipping

data["Shipping_timeframe"]=data["Ship Date"]-data["Order Date"]
data.head()

data['Shipping_timeframe'].dtypes

#Seperating "days" so that we can only remain with numbers for easy calculations

data['Shipping_timeframe'].astype("str").str.split(expand=True)

data[['Shipping_timeframe', '_']] = data['Shipping_timeframe'].astype('str').str.split(expand=True)

data['Shipping_timeframe'].dtypes

#we convert to "int" so that we can be able to perform calculations on that column later

data['Shipping_timeframe'] =data['Shipping_timeframe'].astype(int) 
data.dtypes

#lets create a column of years and months so that we can later tell which year and months had most and least sales
data["Year"]=data["Order Date"].dt.strftime("%Y")
data["Month"]=data["Order Date"].dt.strftime("%m")
data.head()

Ship_modeav=data.groupby("Ship Mode").agg({"Shipping_timeframe":"mean"})
print(Ship_modeav)

Salesperyear=data.groupby("Year").agg({"Sales":"mean"})
print(Salesperyear)

Salespermonth=data.groupby("Month").agg({"Sales":"mean"})
print(Salespermonth)

#From this, we get how each segment performed as far as sales are concerned

SalesperSegment=data.groupby("Segment").agg({"Sales":"mean"})
print(SalesperSegment)

#we obtain list of top 10 customers who have shopped more frequently

TopCustomers=data["Customer Name"].value_counts().nlargest(10)
print(TopCustomers)

#Checking the cities with most sales
TopCities=data["City"].value_counts().nlargest(10)
print(TopCities)

#Obtaining the days of the week when the orders were made so that we can tell on which day of the week are the sales at the highest

data["Day of the week"]=data["Order Date"].dt.strftime("%A")

data.head()

#Checking which day of the week had most sales on average

Daysales=data.groupby("Day of the week").agg({"Sales":"mean"}).sort_values("Sales")
print(Daysales)

Daysales=data.groupby("Day of the week").agg({"Sales":"mean"}).sort_values("Sales").reset_index()
plt.barh(Daysales["Day of the week"], Daysales["Sales"])
plt.title("Sales as Per Days of the week")
plt.xticks(rotation=50)
plt.show

TopCities=data["City"].value_counts().nlargest().plot()
plt.xticks(rotation=90)
plt.title("Top Cities in sales")

SalesperSegment=data.groupby("Segment").agg({"Sales":"mean"}).reset_index()
mylabels=("Consumer", "Coporate","Home Office")
plt.pie(SalesperSegment["Sales"], labels=mylabels, autopct="%1.1f%%")
plt.title("Sales Per Segment")
plt.show()


TopCustomers=data["Customer Name"].value_counts().nlargest(10).plot()
plt.xticks(rotation=90)
plt.title("Top Customers")
plt.show()


Salespermonth=data.groupby("Month").agg({"Sales":"mean"}).reset_index
plt.scatter(data["Sales"],data["Month"])
plt.title("Sales for each Month over the years")
plt.show()

Salesperyear=data.groupby("Year").agg({"Sales":"mean"}).sort_values("Sales").reset_index()
plt.bar(Salesperyear["Year"], Salesperyear["Sales"],color="green")
plt.title("Average Sales for each Year")
plt.show()

