# SpaceX Falcon 9 First Stage Landing Prediction

## Assignment: Exploring and Preparing Data

**Estimated time needed: 70 minutes**

In this assignment, we will predict if the Falcon 9 first stage will land successfully. SpaceX advertises Falcon 9 rocket launches on its website with a cost of 62 million dollars; other providers cost upward of 165 million dollars each, much of the savings is due to the fact that SpaceX can reuse the first stage.

In this lab, you will perform Exploratory Data Analysis and Feature Engineering.

Falcon 9 first stage will land successfully:

![Successful Landing](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DS0701EN-SkillsNetwork/api/Images/landing_1.gif)

Several examples of an unsuccessful landing are shown here:

![Unsuccessful Landing](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DS0701EN-SkillsNetwork/api/Images/crash.gif)

Most unsuccessful landings are planned. SpaceX performs a controlled landing in the oceans.

---

## Objectives

Perform exploratory Data Analysis and Feature Engineering using `Pandas` and `Matplotlib`:

- Exploratory Data Analysis
- Preparing Data / Feature Engineering

---

## Import Libraries and Define Auxiliary Functions

We will import the following libraries for the lab:

```python
import piplite
await piplite.install(['numpy'])
await piplite.install(['pandas'])
await piplite.install(['seaborn'])
```

```python
# pandas is a software library written for the Python programming language for data manipulation and analysis.
import pandas as pd
# NumPy is a library for the Python programming language, adding support for large, multi-dimensional arrays and matrices,
# along with a large collection of high-level mathematical functions to operate on these arrays
import numpy as np
# Matplotlib is a plotting library for python and pyplot gives us a MatLab like plotting framework.
import matplotlib.pyplot as plt
# Seaborn is a Python data visualization library based on matplotlib.
import seaborn as sns
```

---

## Exploratory Data Analysis

First, let's read the SpaceX dataset into a Pandas dataframe and print its summary:

```python
from js import fetch
import io

URL = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/datasets/dataset_part_2.csv"
resp = await fetch(URL)
dataset_part_2_csv = io.BytesIO((await resp.arrayBuffer()).to_py())
df = pd.read_csv(dataset_part_2_csv)
df.head(5)
```

First, let's try to see how the `FlightNumber` (indicating the continuous launch attempts) and `Payload` variables would affect the launch outcome.

We can plot out the `FlightNumber` vs. `PayloadMass` and overlay the outcome of the launch. We see that as the flight number increases, the first stage is more likely to land successfully. The payload mass also appears to be a factor; even with more massive payloads, the first stage often returns successfully.

```python
sns.catplot(y="PayloadMass", x="FlightNumber", hue="Class", data=df, aspect=5)
plt.xlabel("Flight Number", fontsize=20)
plt.ylabel("Pay load Mass (kg)", fontsize=20)
plt.show()
```

Next, let's drill down to each site and visualize its detailed launch records.

---

### TASK 1: Visualize the relationship between Flight Number and Launch Site

Use the function `catplot` to plot `FlightNumber` vs `LaunchSite`, set the parameter `x` to `FlightNumber`, set `y` to `LaunchSite`, and set the parameter `hue` to `'Class'`:

```python
# Plot a scatter point chart with x axis to be Flight Number and y axis to be the launch site, and hue to be the class value
import seaborn as sns
import matplotlib.pyplot as plt

sns.catplot(x='FlightNumber', y='LaunchSite', hue='Class', data=df)
plt.xlabel('Flight Number')
plt.ylabel('Launch Site')
plt.title('Flight Number vs Launch Site')
plt.show()
```

Now try to explain the patterns you found in the Flight Number vs. Launch Site scatter point plots.

---

### TASK 2: Visualize the relationship between Payload Mass and Launch Site

We also want to observe if there is any relationship between launch sites and their payload mass.

```python
# Plot a scatter point chart with x axis to be Pay Load Mass (kg) and y axis to be the launch site, and hue to be the class value
sns.catplot(x='PayloadMass', y='LaunchSite', hue='Class', data=df)
plt.xlabel('Payload Mass (kg)')
plt.ylabel('Launch Site')
plt.title('Payload Mass vs Launch Site')
plt.show()
```

Now if you observe the Payload Mass vs. Launch Site scatter point chart, you will find that for the VAFB-SLC launch site there are no rockets launched for heavy payload mass (greater than 10000).

---

### TASK 3: Visualize the relationship between success rate of each orbit type

Next, we want to visually check if there are any relationships between success rate and orbit type.

Let's create a bar chart for the success rate of each orbit:

```python
# HINT: use groupby method on Orbit column and get the mean of Class column
orbit_success = df.groupby('Orbit')['Class'].mean().reset_index()
orbit_success.columns = ['Orbit', 'SuccessRate']

sns.barplot(x='Orbit', y='SuccessRate', data=orbit_success)
plt.xlabel('Orbit Type')
plt.ylabel('Success Rate')
plt.title('Success Rate by Orbit Type')
plt.xticks(rotation=45)
plt.show()
```

Analyze the plotted bar chart to identify which orbits have the highest success rates.

---

### TASK 4: Visualize the relationship between FlightNumber and Orbit type

For each orbit, we want to see if there is any relationship between FlightNumber and Orbit type.

```python
# Plot a scatter point chart with x axis to be FlightNumber and y axis to be the Orbit, and hue to be the class value
sns.catplot(x='FlightNumber', y='Orbit', hue='Class', data=df)
plt.xlabel('Flight Number')
plt.ylabel('Orbit Type')
plt.title('Flight Number vs Orbit Type')
plt.show()
```

You can observe that in the LEO orbit, success seems to be related to the number of flights. Conversely, in the GTO orbit, there appears to be no relationship between flight number and success.

---

### TASK 5: Visualize the relationship between Payload Mass and Orbit type

Similarly, we can plot the Payload Mass vs. Orbit scatter point charts to reveal the relationship between Payload Mass and Orbit type:

```python
# Plot a scatter point chart with x axis to be Payload Mass and y axis to be the Orbit, and hue to be the class value
sns.catplot(x='PayloadMass', y='Orbit', hue='Class', data=df)
plt.xlabel('Payload Mass')
plt.ylabel('Orbit Type')
plt.title('PayloadMass vs Orbit Type')
plt.show()
```

With heavy payloads, the successful landing or positive landing rate is higher for Polar, LEO, and ISS.

However, for GTO, it's difficult to distinguish between successful and unsuccessful landings as both outcomes are present.

---

### TASK 6: Visualize the launch success yearly trend

You can plot a line chart with x axis to be `Year` and y axis to be average success rate, to get the average launch success trend.

The function below will help you get the year from the date:

```python
# A function to Extract years from the date
year = []
def Extract_year():
    for i in df["Date"]:
        year.append(i.split("-")[0])
    return year

Extract_year()
df['Date'] = year
df.head()
```

```python
# Plot a line chart with x axis to be the extracted year and y axis to be the success rate
yearly_success = df.groupby('Date')['Class'].mean().reset_index()

sns.lineplot(x='Date', y='Class', data=yearly_success)
plt.xlabel('Year')
plt.ylabel('Average Success Rate')
plt.title('Launch Success Yearly Trend')
plt.show()
```

You can observe that the success rate since 2013 kept increasing till 2020.

---

## Features Engineering

By now, you should have obtained some preliminary insights about how each important variable affects the success rate. We will select the features that will be used in success prediction in the future module.

```python
features = df[['FlightNumber', 'PayloadMass', 'Orbit', 'LaunchSite', 'Flights',
               'GridFins', 'Reused', 'Legs', 'LandingPad', 'Block', 'ReusedCount', 'Serial']]
features.head()
```

---

### TASK 7: Create dummy variables to categorical columns

Use the function `get_dummies` and the `features` dataframe to apply OneHotEncoder to the columns `Orbit`, `LaunchSite`, `LandingPad`, and `Serial`. Assign the value to the variable `features_one_hot`, and display the results using the method `head`. Your result dataframe must include all features including the encoded ones.

```python
# HINT: Use get_dummies() function on the categorical columns
features_one_hot = pd.get_dummies(features, columns=['Orbit', 'LaunchSite', 'LandingPad', 'Serial'])
features_one_hot.head()
```

---

### TASK 8: Cast all numeric columns to `float64`

Now that our `features_one_hot` dataframe only contains numbers, cast the entire dataframe to variable type `float64`:

```python
# HINT: use astype function
features_one_hot = features_one_hot.astype('float64')
features_one_hot.head()
```

We can now export it to a CSV for the next section, but to make the answers consistent, in the next lab we will provide data in a pre-selected date range.

```python
features_one_hot.to_csv('dataset_part_3.csv', index=False)
```

---

## Authors

[Pratiksha Verma](https://www.linkedin.com/in/pratiksha-verma-6487561b1/)

*IBM Corporation 2022. All rights reserved.*
