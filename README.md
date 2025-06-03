# LEVEL-3

TASK 1
### 🔧 **Analysis Plan 

1. **Explore Ratings**: Distribution of ratings.
2. **Positive vs Negative Restaurants**:

   * Group by `Rating text` (like Excellent, Poor, etc.)
3. **Average Length of Reviews**: Not applicable (no text reviews).
4. **Rating vs Votes**: Check if popular restaurants get better ratings.
5. **Visualizations** using `matplotlib` and `seaborn`.

---

```python
# Import necessary libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
file_path = "your_filename.csv"  # Replace with your actual file name
df = pd.read_csv(file_path)

# Display basic info
print("Dataset Shape:", df.shape)
print("Columns:", df.columns)
print(df.head())

# Clean and inspect rating column
df['Aggregate rating'] = pd.to_numeric(df['Aggregate rating'], errors='coerce')

# --- 1. Rating Distribution ---
plt.figure(figsize=(8, 5))
sns.countplot(x='Rating text', data=df, order=df['Rating text'].value_counts().index, palette='Set2')
plt.title('Restaurant Rating Text Distribution')
plt.xlabel('Rating Text')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# --- 2. Rating vs Votes ---
plt.figure(figsize=(8, 5))
sns.scatterplot(x='Aggregate rating', y='Votes', data=df)
plt.title('Rating vs Number of Votes')
plt.xlabel('Aggregate Rating')
plt.ylabel('Votes')
plt.tight_layout()
plt.show()

# --- 3. Top Cuisines by Rating (optional bonus) ---
top_cuisines = df['Cuisines'].value_counts().head(10).index
top_df = df[df['Cuisines'].isin(top_cuisines)]

plt.figure(figsize=(10, 6))
sns.boxplot(x='Cuisines', y='Aggregate rating', data=top_df, palette='coolwarm')
plt.xticks(rotation=45)
plt.title('Rating Distribution for Top Cuisines')
plt.tight_layout()
plt.show()

# --- 4. Average Rating Summary ---
rating_summary = df.groupby("Rating text")['Aggregate rating'].mean().sort_values(ascending=False)
print("\nAverage Ratings by Rating Text:\n", rating_summary)

# Optional: Save visualizations (remove comment if needed)
# plt.savefig("output_plot.png")
```
![lvl3 task 1(1)](https://github.com/user-attachments/assets/2e5fd308-4ea4-47a6-969b-cfd1f05990dd)
![lvl3 task1(2)](https://github.com/user-attachments/assets/77b24dbe-993a-4140-abc5-fee325f747cf)
![lvl 3 task1(2)](https://github.com/user-attachments/assets/a0619681-373b-48c2-8daa-743dd7a05b38)
![lvl3 task1(3)](https://github.com/user-attachments/assets/7c13f4e1-46e2-4fb0-9dc8-dab22b36e9b8)

TASK 2
### 🧾 **Votes Analysis**

* Find the most and least voted restaurants
* Correlation between **Votes** and **Aggregate Rating**
* 📈 Visualizations with `matplotlib` and `seaborn`


```python
# --- Import required libraries ---
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# --- Load your CSV file ---
file_path = "your_filename.csv"  # Change this to your actual filename
df = pd.read_csv(file_path)

# --- Convert Votes and Rating to numeric ---
df['Votes'] = pd.to_numeric(df['Votes'], errors='coerce')
df['Aggregate rating'] = pd.to_numeric(df['Aggregate rating'], errors='coerce')

# --- Drop rows with missing values in Votes or Ratings ---
df = df.dropna(subset=['Votes', 'Aggregate rating'])

# --- 1. Most and Least Voted Restaurants ---
most_voted = df[df['Votes'] == df['Votes'].max()]
least_voted = df[df['Votes'] == df['Votes'].min()]

print("🔝 Most Voted Restaurant:\n", most_voted[['Restaurant Name', 'City', 'Votes', 'Aggregate rating']])
print("\n🔻 Least Voted Restaurant:\n", least_voted[['Restaurant Name', 'City', 'Votes', 'Aggregate rating']])

# --- 2. Correlation between Votes and Rating ---
correlation = df[['Votes', 'Aggregate rating']].corr().iloc[0, 1]
print(f"\n📈 Correlation between Votes and Rating: {correlation:.2f}")

# --- 3. Scatter Plot of Votes vs Aggregate Rating ---
plt.figure(figsize=(8, 5))
sns.scatterplot(x='Aggregate rating', y='Votes', data=df, alpha=0.6, edgecolor='k')
plt.title('Votes vs Aggregate Rating')
plt.xlabel('Aggregate Rating')
plt.ylabel('Votes')
plt.grid(True)
plt.tight_layout()
plt.show()

# --- 4. Optional: Bar Plot of Top 10 Most Voted Restaurants ---
top10 = df.sort_values(by='Votes', ascending=False).head(10)

plt.figure(figsize=(10, 6))
sns.barplot(x='Votes', y='Restaurant Name', data=top10, palette='viridis')
plt.title('Top 10 Most Voted Restaurants')
plt.xlabel('Votes')
plt.ylabel('Restaurant Name')
plt.tight_layout()
plt.show()
```
![lvl3 task2(1)](https://github.com/user-attachments/assets/583253b1-768e-4c75-bfcf-af7bc6a8fd80)
![lvl3 task2(2)](https://github.com/user-attachments/assets/eb8e5e9d-b72b-492c-ad38-60151a7d9211)


TASK 3

### 🧾 **Task**: Analyze Relationship between **Price Range**, **Table Booking**, and **Online Delivery**

```python
# --- Import required libraries ---
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# --- Load dataset ---
file_path = "your_filename.csv"  # Replace with your CSV filename
df = pd.read_csv(file_path)

# --- Convert necessary columns to proper types ---
df['Price range'] = pd.to_numeric(df['Price range'], errors='coerce')
df['Has Table booking'] = df['Has Table booking'].astype(str)
df['Has Online delivery'] = df['Has Online delivery'].astype(str)

# --- Group by Price Range and Booking/Delivery Availability ---
table_booking = df.groupby(['Price range', 'Has Table booking']).size().unstack(fill_value=0)
online_delivery = df.groupby(['Price range', 'Has Online delivery']).size().unstack(fill_value=0)

# --- Normalize to percentage for better visual understanding ---
table_booking_percent = table_booking.div(table_booking.sum(axis=1), axis=0) * 100
online_delivery_percent = online_delivery.div(online_delivery.sum(axis=1), axis=0) * 100

# --- Plot 1: Table Booking vs Price Range ---
plt.figure(figsize=(8, 5))
table_booking_percent.plot(kind='bar', stacked=True, colormap='Set2')
plt.title("🪑 Table Booking Availability by Price Range (%)")
plt.xlabel("Price Range (1 = Low, 4 = High)")
plt.ylabel("Percentage of Restaurants")
plt.xticks(rotation=0)
plt.legend(title="Table Booking")
plt.tight_layout()
plt.show()

# --- Plot 2: Online Delivery vs Price Range ---
plt.figure(figsize=(8, 5))
online_delivery_percent.plot(kind='bar', stacked=True, colormap='coolwarm')
plt.title("🚚 Online Delivery Availability by Price Range (%)")
plt.xlabel("Price Range (1 = Low, 4 = High)")
plt.ylabel("Percentage of Restaurants")
plt.xticks(rotation=0)
plt.legend(title="Online Delivery")
plt.tight_layout()
plt.show()

# --- Print raw counts for verification ---
print("Table Booking Counts:\n", table_booking)
print("\nOnline Delivery Counts:\n", online_delivery)
```

---

### ✅ What This Code Does:

* Groups restaurants by **price range** and whether they offer:

  * **Table booking**
  * **Online delivery**
* Calculates and visualizes **percentages**
* Generates two **stacked bar plots** for visual interpretation
* Prints the raw count tables for reference.

![lvl3 task3(1)](https://github.com/user-attachments/assets/9c3b6fb3-c9f3-4777-b7a5-9aef201fa4ed)
![lvl3 task3(2)](https://github.com/user-attachments/assets/cd370b31-98fb-4c23-b0ee-c915792bb41c)
