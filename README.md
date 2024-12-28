import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import geopandas as gpd
import folium

# Load the dataset
df = pd.read_excel("Crime_Data.xlsx")

# Data Cleaning and Preprocessing
# 1. Handle Missing Values
df.dropna(subset=['Date', 'Primary Type', 'Latitude', 'Longitude'], inplace=True) 

# 2. Convert Datetime
df['Date'] = pd.to_datetime(df['Date'])

# 3. Create new features
df['Year'] = df['Date'].dt.year
df['Month'] = df['Date'].dt.month
df['Day'] = df['Date'].dt.day
df['Hour'] = df['Date'].dt.hour
df['DayOfWeek'] = df['Date'].dt.day_name()

# Exploratory Data Analysis (EDA)
# 1. Crime Type Distribution
plt.figure(figsize=(12, 6))
sns.countplot(x='Primary Type', data=df, order=df['Primary Type'].value_counts().index[:10])
plt.xticks(rotation=45)
plt.title("Top 10 Crime Types")
plt.show()

# 2. Crime Trends Over Time
plt.figure(figsize=(12, 6))
sns.lineplot(x='Date', y='Primary Type', data=df)
plt.title("Crime Trends Over Time")
plt.show()

# 3. Crime Hotspots (using Folium)
chicago_map = folium.Map(location=[41.8781, -87.6298], zoom_start=11) 
for index, row in df.iterrows():
    folium.CircleMarker(location=[row['Latitude'], row['Longitude']], 
                        radius=5, 
                        color='red', 
                        fill=True, 
                        fill_color='red', 
                        fill_opacity=0.5).add_to(chicago_map)
chicago_map.save("crime_hotspots.html")

# 4. Arrest Analysis
arrest_rates = df.groupby('Primary Type')['Arrest'].mean()
plt.figure(figsize=(12, 6))
arrest_rates.plot(kind='bar')
plt.title("Arrest Rates by Crime Type")
plt.show()

# Further analysis and visualizations can be built upon this foundation.
