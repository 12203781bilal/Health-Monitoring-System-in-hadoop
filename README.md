## Health Monitoring System – Big Data Project 🚑📊

This project implements a Health Monitoring System using Hadoop, MapReduce, and Python. It processes health data for 10,000 patients, extracts insights, and visualizes statistics in a dashboard.

## Project Workflow
1. Generate 10,000 Patient Records (BP, Sugar, Cholesterol, Hemoglobin)
2. Store Data in HDFS (Hadoop Distributed File System)
3. Process Data Using MapReduce (Mapper & Reducer)
4. Extract Insights & Generate Statistics
5. Visualize Data in a Graph (Matplotlib)

## 🚀Project Summary

- **Big Data Implementation** using Hadoop & MapReduce
- **Processed 10,000 patient records** efficiently
- **Extracted health statistics** (BP, Sugar, Cholesterol, Hemoglobin)
- **Visualized the data** using Matplotlib

## File Structure

plaintext
📁 health_monitoring_system/
├── generate_patients.py
├── patients.csv
├── HealthMonitorMapper.py
├── HealthMonitorReducer.py
├── plot_health.py
├── README.md

## Future Enhancements
- **Integrate Apache Spark** for faster real-time analytics
- **Develop a Web Dashboard** using Flask/Streamlit
- **Store data in MongoDB** (NoSQL) for scalability

## Contributors
- **Mohammed Bilal**

- **Date**: 1/2/2025

## Setup & Execution Steps

##1. Generate Patient Data
```sh
## nano generate_patients.py

🛠 Setup & Execution Steps
1️⃣ Generate Patient Data
Run the following commands in Ubuntu Terminal:

sh
Copy
Edit
nano generate_patients.py
Paste the following Python code inside generate_patients.py:

python
Copy
Edit
import pandas as pd
import random

# Function to generate BP, Sugar, Cholesterol, Hemoglobin
def generate_bp(): return f"{random.randint(90, 180)}/{random.randint(60, 120)}"
def generate_sugar(): return round(random.uniform(70, 250), 1)
def generate_cholesterol(): return round(random.uniform(100, 300), 1)
def generate_hemoglobin(): return round(random.uniform(8, 18), 1)

# Generate 10,000 patient records
num_patients = 10000
data = [{"Patient_ID": i, "BP": generate_bp(), "Sugar_Level": generate_sugar(),
         "Cholesterol": generate_cholesterol(), "Hemoglobin": generate_hemoglobin()}
        for i in range(1, num_patients + 1)]

# Convert to DataFrame and Save as CSV
df = pd.DataFrame(data)
df.to_csv("patients.csv", index=False)
print("✅ 10,000 patient records saved as patients.csv")
Save the file (Ctrl + X → Y → Enter) and run the script:

sh
Copy
Edit
python3 generate_patients.py
Check if the file is created:

sh
Copy
Edit
ls -lh patients.csv
head patients.csv  # View first few rows
2️⃣ Upload Data to HDFS
sh
Copy
Edit
hdfs dfs -mkdir /health_monitoring
hdfs dfs -put patients.csv /health_monitoring/
hdfs dfs -ls /health_monitoring/
3️⃣ Create & Execute MapReduce Jobs
📌 Mapper Script
Create a new file:

sh
Copy
Edit
nano HealthMonitorMapper.py
Paste the following Python Mapper Code inside:

python
Copy
Edit
#!/usr/bin/env python3
import sys

for line in sys.stdin:
    if "Patient_ID" in line:
        continue  # Skip header
    data = line.strip().split(",")
    if len(data) != 5:
        continue  # Skip corrupted lines
    patient_id, bp, sugar, cholesterol, hemoglobin = data
    print(f"BP\t{bp}")
    print(f"Sugar\t{sugar}")
    print(f"Cholesterol\t{cholesterol}")
    print(f"Hemoglobin\t{hemoglobin}")
Save (Ctrl + X → Y → Enter) and make it executable:

sh
Copy
Edit
chmod +x HealthMonitorMapper.py
📌 Reducer Script
Create a new file:

sh
Copy
Edit
nano HealthMonitorReducer.py
Paste the following Python Reducer Code inside:

python
Copy
Edit
#!/usr/bin/env python3
import sys

health_data = {"BP": [], "Sugar": [], "Cholesterol": [], "Hemoglobin": []}

for line in sys.stdin:
    key, value = line.strip().split("\t")
    if key == "BP":
        systolic, diastolic = map(int, value.split("/"))
        health_data["BP"].append((systolic, diastolic))
    else:
        health_data[key].append(float(value))

# Calculate average statistics
avg_sugar = sum(health_data["Sugar"]) / len(health_data["Sugar"])
avg_cholesterol = sum(health_data["Cholesterol"]) / len(health_data["Cholesterol"])
avg_hemoglobin = sum(health_data["Hemoglobin"]) / len(health_data["Hemoglobin"])
avg_systolic = sum(x[0] for x in health_data["BP"]) / len(health_data["BP"])
avg_diastolic = sum(x[1] for x in health_data["BP"]) / len(health_data["BP"])

# Print results
print(f"Average Sugar Level: {avg_sugar:.2f} mg/dL")
print(f"Average Cholesterol Level: {avg_cholesterol:.2f} mg/dL")
print(f"Average Hemoglobin Level: {avg_hemoglobin:.2f} g/dL")
print(f"Average Blood Pressure: {avg_systolic:.0f}/{avg_diastolic:.0f} mmHg")
Save (Ctrl + X → Y → Enter) and make it executable:

sh
Copy
Edit
chmod +x HealthMonitorReducer.py
4️⃣ Run Hadoop MapReduce Job
Execute the following command:

sh
Copy
Edit
hadoop jar /usr/local/hadoop/share/hadoop/tools/lib/hadoop-streaming-*.jar \
-input /health_monitoring/patients.csv \
-output /health_monitoring/output \
-mapper HealthMonitorMapper.py \
-reducer HealthMonitorReducer.py
Check the Output:

sh
Copy
Edit
hdfs dfs -cat /health_monitoring/output/part-00000
5️⃣ Visualize Data in a Graph
📌 Install Matplotlib
If not installed, run:

sh
Copy
Edit
pip install matplotlib
📌 Create a Python Script
Create a new file:

sh
Copy
Edit
nano plot_health.py
Paste the following Matplotlib code inside:

python
Copy
Edit
import matplotlib.pyplot as plt

# Sample health statistics (Replace with real computed values)
labels = ["Sugar", "Cholesterol", "Hemoglobin"]
values = [120, 180, 14]  # Replace these with real computed averages

plt.bar(labels, values, color=['blue', 'red', 'green'])
plt.ylabel("Average Levels")
plt.title("Health Monitoring Statistics")
plt.show()
Save (Ctrl + X → Y → Enter) and run the script:

sh
Copy
Edit
python3 plot_health.py
📊 If running on a remote terminal (no GUI), use this instead:

python
Copy
Edit
import matplotlib
matplotlib.use('Agg')  # Use non-interactive backend
import matplotlib.pyplot as plt

labels = ["Sugar", "Cholesterol", "Hemoglobin"]
values = [120, 180, 14]

plt.bar(labels, values, color=['blue', 'red', 'green'])
plt.ylabel("Average Levels")
plt.title("Health Monitoring Statistics")

plt.savefig("health_stats.png")  # Save as image
print("✅ Chart saved as health_stats.png")
Run:
n3 plot_health.py
xdg-open health_stats.png
