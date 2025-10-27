# dataAnalysis_python_projects
import pandas as pd 
import matplotlib.pyplot as plt 
import seaborn as sns 

try:
    df = pd.read_csv('vindra_store_SQL.csv')
    print("File loaded successfully!")
except FileNotFoundError:
    print("Error: 'vindra_store_SQL.csv' not found.")
    print("Please make sure the CSV file is in the same directory as your Jupyter Notebook.")
    df = pd.DataFrame()

# Convert the 'date' column to datetime objects for proper sorting
df['date'] = pd.to_datetime(df['date'], format='%d-%m-%Y')

 # Clean up column names by removing leading/trailing spaces
df.columns = df.columns.str.strip()

# Calculate monthly sales
monthly_sales = df.groupby(df['date'].dt.to_period('M'))['amount'].sum()

# Convert the PeriodIndex to strings for plotting
monthly_sales.index = monthly_sales.index.strftime('%Y-%m')

# Create a figure for the plots
plt.figure(figsize=(20, 12))

# Plot 1: Monthly Sales Trend (Bar Plot)
plt.subplot(2, 2, 1)
sns.barplot(x=monthly_sales.index, y=monthly_sales.values, palette='viridis')
plt.title('Monthly Sales Trend (All Years)')
plt.xlabel('Month-Year')
plt.ylabel('Total Sales (Amount)')
plt.xticks(rotation=45, ha='right')
plt.grid(axis='y', linestyle='--', alpha=0.7)

# Plot 2: Sales by Gender (Pie Chart)
gender_sales = df.groupby('gender')['amount'].sum()
plt.subplot(2, 2, 2)
plt.pie(gender_sales, labels=gender_sales.index, autopct='%1.1f%%', startangle=90, colors=sns.color_palette('pastel'))
plt.title('Total Sales Distribution by Gender')
plt.axis('equal')  # Equal aspect ratio ensures that pie is drawn as a circle.

# Plot 3: Order Status (Pie Chart)
order_status_counts = df['status'].value_counts()
plt.subplot(2, 2, 3)
plt.pie(order_status_counts, labels=order_status_counts.index, autopct='%1.1f%%', startangle=90, colors=sns.color_palette('Set2'))
plt.title('Order Status Distribution')
plt.axis('equal')

# Plot 4: Sales by Channel (Bar Plot)
channel_sales = df.groupby('channel ')['amount'].sum().sort_values(ascending=False)
plt.subplot(2, 2, 4)
sns.barplot(x=channel_sales.index, y=channel_sales.values, palette='coolwarm')
plt.title('Total Sales by Channel')
plt.xlabel('Channel')
plt.ylabel('Total Sales (Amount)')
plt.xticks(rotation=45, ha='right')
plt.grid(axis='y', linestyle='--', alpha=0.7)

# Calculate KPIs
total_sales = df['amount'].sum()
total_orders = df['order_id'].nunique()

# Plot 5: Key Performance Indicators (KPIs) as Text
plt.subplot()
 # Display KPIs in a text box on the plot
kpi_text = f'Total Sales: ₹{total_sales:,.2f}\nTotal Orders: {total_orders:,}'
plt.subplot()
plt.text(0.5, 0.5, kpi_text, ha='center', va='center', fontsize=24, bbox=dict(boxstyle="round,pad=0.5", fc="aliceblue", ec="black", lw=2))
plt.axis('off')
plt.title('Key Performance Indicators (KPIs)', fontsize=16)

# Adjust layout and display the plots
plt.tight_layout(pad=4.0)
plt.show()
