# AtliQ Hotels Data Analysis Project

AtliQ Hotels, a prestigious five-star hotel chain in India, has been facing revenue challenges due to increasing competition. This project aims to analyze their booking data to extract valuable insights that can inform strategic decisions to enhance customer satisfaction and boost revenue.

## Project Structure

1.Data Import and Exploration
2.Data Cleaning
3.Data Transformation
4.Data Analysis and Insights
## 1.Data import and Exploration

Datasets Used:

dim_date.csv
dim_hotels.csv
dim_rooms.csv
fact_aggregated_bookings.csv
fact_bookings.csv

Steps:

Imported necessary libraries and loaded datasets using Pandas.
Performed initial data exploration to understand the structure and content of each dataset.

import pandas as pd

# Load datasets
dim_date = pd.read_csv('dim_date.csv')
dim_hotels = pd.read_csv('dim_hotels.csv')
dim_rooms = pd.read_csv('dim_rooms.csv')
fact_aggregated_bookings = pd.read_csv('fact_aggregated_bookings.csv')
fact_bookings = pd.read_csv('fact_bookings.csv')

# Display first few rows of each dataframe
print(dim_date.head())
print(dim_hotels.head())
print(dim_rooms.head())
print(fact_aggregated_bookings.head())
print(fact_bookings.head())

## 2. Data Cleaning

Actions Taken:

1. Identified and removed invalid data entries, such as negative guest counts.

2. Handled missing values appropriately, either by filling them with suitable substitutes or by removing the records.

3. Detected and addressed outliers in revenue data to ensure accuracy in analysis.

Sample Code:

# Remove negative guest counts
fact_bookings = fact_bookings[fact_bookings['guest_count'] >= 0]

# Fill missing values in 'revenue' column with the median
fact_bookings['revenue'].fillna(fact_bookings['revenue'].median(), inplace=True)

# Remove outliers in 'revenue' column
q1 = fact_bookings['revenue'].quantile(0.25)
q3 = fact_bookings['revenue'].quantile(0.75)
iqr = q3 - q1
lower_bound = q1 - 1.5 * iqr
upper_bound = q3 + 1.5 * iqr
fact_bookings = fact_bookings[(fact_bookings['revenue'] >= lower_bound) & (fact_bookings['revenue'] <= upper_bound)]

## 3. Data transformation

Processes:

1. Merged datasets to create a comprehensive view of the data.

2. Standardized column names and data formats for consistency.

3. Created new features, such as booking month and day type, to facilitate deeper analysis.

Sample Code:

# Merge fact_bookings with dim_date to add date-related information
merged_data = pd.merge(fact_bookings, dim_date, left_on='date_id', right_on='date_id', how='left')

# Standardize column names
merged_data.columns = merged_data.columns.str.lower().str.replace(' ', '_')

# Create new feature: booking_month
merged_data['booking_month'] = pd.to_datetime(merged_data['date']).dt.month_name()

# Create new feature: day_type (Weekend/Weekday)
merged_data['day_type'] = pd.to_datetime(merged_data['date']).dt.dayofweek.apply(lambda x: 'Weekend' if x >= 5 else 'Weekday')

## 4. Data Analysis and Insights

Key Insights:

1. Booking Trends: Analyzed booking patterns to identify peak periods and popular room categories.

2. Revenue Analysis: Assessed revenue performance across different properties and time periods.

3. Capacity Utilization: Evaluated how effectively each property utilized its capacity and identified opportunities for improvement.


Sample Code:

import matplotlib.pyplot as plt
import seaborn as sns

# Booking trends by month
booking_trends = merged_data['booking_month'].value_counts().sort_index()
plt.figure(figsize=(10, 6))
sns.barplot(x=booking_trends.index, y=booking_trends.values)
plt.title('Number of Bookings per Month')
plt.xlabel('Month')
plt.ylabel('Number of Bookings')
plt.show()

# Revenue analysis by property
revenue_by_property = merged_data.groupby('property_name')['revenue'].sum().sort_values(ascending=False)
plt.figure(figsize=(10, 6))
sns.barplot(x=revenue_by_property.index, y=revenue_by_property.values)
plt.title('Total Revenue by Property')
plt.xlabel('Property')
plt.ylabel('Total Revenue')
plt.xticks(rotation=45)
plt.show()

# Capacity utilization
capacity_utilization = merged_data.groupby('property_name').apply(lambda x: x['guest_count'].sum() / x['capacity'].sum())
plt.figure(figsize=(10, 6))
sns.barplot(x=capacity_utilization.index, y=capacity_utilization.values)
plt.title('Capacity Utilization by Property')
plt.xlabel('Property')
plt.ylabel('Capacity Utilization')
plt.xticks(rotation=45)
plt.show()

## Technologies Used

1. Programming language: Python

2. Libraries: Pandas, NumPy, Matplotlib, Seaborn
## How to Run

1. Clone the Repository:

git clone https://github.com/Abdulajees-123/Hospitality-Analysis---Python.git

2. Navigate to the Project Directory:

cd Hospitality-Analysis---Python

3. Install Required Packages:

pip install -r requirements.txt

4. Run the Jupyter Notebook:

jupyter notebook hotels_analysis.ipynb

## Results

The analysis provided actionable insights into booking behaviors, revenue streams, and capacity management. These insights can guide AtliQ Hotels in refining their strategies to enhance operational efficiency and profitability.
