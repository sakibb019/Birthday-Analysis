# Birthday-Analysis

Let’s take a look at the freely available data on births in the United States, provided by the Centers for Disease Control (CDC). This data can be found at  *births.csv*

 ```
 import pandas as pd
births = pd.read_csv("births.csv") 
print(births.head()) births['day'].fillna(0, inplace=True) 
births['day'] = births['day'].astype(int)
```

![1](https://user-images.githubusercontent.com/40710717/120894734-cecf6300-c63b-11eb-9d39-29a3b3fad2db.JPG)


```
births['decade'] = 10 * (births['year'] // 10)
births.pivot_table('births', index='decade', columns='gender', aggfunc='sum')
print(births.head())
```

![1](https://user-images.githubusercontent.com/40710717/120894902-86fd0b80-c63c-11eb-8cdb-9cdfb97090a6.JPG)


We immediately see that male births outnumber female births in every decade. To see this trend a bit more clearly, we can use the built-in plotting tools in Pandas to visualize the total number of births by year :

```import matplotlib.pyplot as plt 
import seaborn as sns 
sns.set() 
birth_decade = births.pivot_table('births', index='decade', columns='gender', aggfunc='sum') 
birth_decade.plot() 
plt.ylabel("Total births per year") 
plt.show()
```

![1](https://user-images.githubusercontent.com/40710717/120894996-df340d80-c63c-11eb-9e86-b15e59008783.JPG)


## Further data exploration:

There are a few interesting features we can pull out of this dataset using the Pandas tools. We must start by cleaning the data a bit, removing outliers caused by mistyped dates or missing values. One easy way to remove these all at once is to cut outliers, we’ll do this via a robust sigma-clipping operation:

>  `import numpy as np
quartiles = np.percentile(births['births'], [25, 50, 75])
mean = quartiles[1]
sigma = 0.74 * (quartiles[2] - quartiles[0]) `

This final line is a robust estimate of the sample mean, where the 0.74 comes from the interquartile range of a Gaussian distribution. With this we can use the query() method to filter out rows with births outside these values:

> `births = births.query('(births > @mean - 5 * @sigma) & (births < @mean + 5 * @sigma)')
births.index = pd.to_datetime(10000 * births.year + 100 * births.month + births.day,
                              format='%Y%m%d')
births['day of week'] = births.index.dayofweek`

Using this we can plot births by weekday for several decades:
> `births_day = births.pivot_table('births', index='day of week',
                                columns='decade', aggfunc='mean')
births_day.index = ['Mon', 'Tues', 'Wed', 'Thurs', 'Fri', 'Sat', 'Sun']
births_day.plot()
plt.ylabel("Average Births by Day")
plt.show()`

![2](https://user-images.githubusercontent.com/40710717/120895259-f9222000-c63d-11eb-96ff-1ff78c17c7db.JPG)

Apparently births are slightly less common on weekends than on weekdays! Note that the 1990s and 2000s are missing because the CDC data contains only the month of birth starting in 1989.

Another interesting view is to plot the mean number of births by the day of the year. Let’s first group the data by month and day separately:

>`births_month = births.pivot_table('births', [births.index.month, births.index.day])
print(births_month.head())
births_month.index = [pd.datetime(2012, month, day)
                      for (month, day) in births_month.index]
print(births_month.head())`

![1](https://user-images.githubusercontent.com/40710717/120895392-8feedc80-c63e-11eb-95fc-977c0c80dc91.JPG)

Focusing on the month and day only, we now have a time series reflecting the average number of births by date of the year. From this, we can use the plot method to plot the data. It reveals some interesting trends:
>`fig, ax = plt.subplots(figsize=(12, 4))
births_month.plot(ax=ax)
plt.show()`

![2](https://user-images.githubusercontent.com/40710717/120895504-04298000-c63f-11eb-9c7f-b7deb54425be.JPG)
