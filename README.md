# Birthday-Analysis

Let’s take a look at the freely available data on births in the United States, provided by the Centers for Disease Control (CDC). This data can be found at  *births.csv*

> `import pandas as pd
births = pd.read_csv("births.csv") 
print(births.head()) births['day'].fillna(0, inplace=True) 
births['day'] = births['day'].astype(int)`

> `births['decade'] = 10 * (births['year'] // 10)
births.pivot_table('births', index='decade', columns='gender', aggfunc='sum')
print(births.head())`

We immediately see that male births outnumber female births in every decade. To see this trend a bit more clearly, we can use the built-in plotting tools in Pandas to visualize the total number of births by year :

> `import matplotlib.pyplot as plt 
import seaborn as sns 
sns.set() 
birth_decade = births.pivot_table('births', index='decade', columns='gender', aggfunc='sum') 
birth_decade.plot() 
plt.ylabel("Total births per year") 
plt.show()`

