# A Deep Analysis in the Numbers of the COVID-19 Pandemic in united states with python and tableau

### Importing Libraries
The libraries used in this notebook are:


```python
import opendatasets as od
import pandas as pd
import seaborn as sns
import matplotlib 
import matplotlib.pyplot as plt
%matplotlib inline
```

### Data Preparation and Cleaning

For the purposes of this work the data will be retrieved from  NY-TIMES COVID-19 USA dataset , which is updated in a Regular basis bases on the information reported officially by the government.

#### Our NY-TIMES COVID-19 USA Dataset

Our NY-TIMES COVID-19 USA dataset is a scientific online publication that focuses on COVID-19 Pandemic in united states.

    https://www.kaggle.com/


if you dont have a kaggle account you can skip to (df = pd.read_csv('US_covid_data.csv')), but you must know that the data wont be uptodate


```python
# NY-TIMES COVID-19 USA dataset URL:
url = 'https://www.kaggle.com/datasets/imoore/us-covid19-dataset-live-hourlydaily-updates'

# Retrive .CSV file from kaggle
od.download('https://www.kaggle.com/datasets/imoore/us-covid19-dataset-live-hourlydaily-updates')
```

    Skipping, found downloaded files in "./us-covid19-dataset-live-hourlydaily-updates" (use force=True to force download)


Then we can read it with Pandas using the module .read_cvs()


```python
df = pd.read_csv('./us-covid19-dataset-live-hourlydaily-updates/States.csv')
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>fips</th>
      <th>cases</th>
      <th>deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-01-21</td>
      <td>Washington</td>
      <td>53</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-01-22</td>
      <td>Washington</td>
      <td>53</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-01-23</td>
      <td>Washington</td>
      <td>53</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-01-24</td>
      <td>Illinois</td>
      <td>17</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-01-24</td>
      <td>Washington</td>
      <td>53</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Just by observing at the printed data frame it is easy to observe the following:

    The file provides data from each state
    The data is categorized by date and state
    The data is provided in a Regular basis
    There are several metrics inside the dataset.

It is important to keep in mind that this data is officially reported data, which means that the actual number of cases, deaths and more metrics presented in here may be higher, since not all the cases are diagnosed as COVID-19 cases.

One can see the basic information of the data frame using the .info() method.


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 42566 entries, 0 to 42565
    Data columns (total 5 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   date    42566 non-null  object
     1   state   42566 non-null  object
     2   fips    42566 non-null  int64 
     3   cases   42566 non-null  int64 
     4   deaths  42566 non-null  int64 
    dtypes: int64(3), object(2)
    memory usage: 1.6+ MB


From the information above, we can observe that the dataset has 4 columns with different type of data focusing on:

    Cases (Positive diagnosed cases)
    Deaths (Deaths related to COVID-19)


#### Creating Data Frames

We will focus on cases and deaths So there is no need for fips column  so let's drop it i


```python
## Let us drop the fips column from dataset, as follows:
df.drop('fips', axis=1, inplace=True)
```


```python
## Let us see the dataset
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-01-21</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-01-22</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-01-23</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-01-24</td>
      <td>Illinois</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-01-24</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Now, let us create new .CSV file based on the recently crated data frame . To do so, you can use the method to_cvs(), from Pandas, which creates a CVS file from a data frame.

notice that the second argument of the method is index=False, this is to avoid passing the high numbers from the original dataset.


```python
# save the data

df.to_csv('US_covid_data.csv', index=False)
```

By saving the data frames before start cleaning or modifying the data frames warranties that we have a backup of the data, without the need to redo the data filtration.

#### Cleaning the DataFrame

Before starting to work with the data frame, it is important to verify that all the information was input correctly and in a reasonable manner.

We will read the new CSV files created in the previous sections.


```python
# Read CSV file
df = pd.read_csv('US_covid_data.csv')
```

Let us verify that each of the data frames were imported correctly by printing the first and last 5 rows.

To print the first 5 rows of a data frame, we can use the .head() method, for example:


```python
df.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-01-21</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-01-22</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-01-23</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-01-24</td>
      <td>Illinois</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-01-24</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Now, let us do the same with the bottom of the data frames:


```python
df.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>42561</th>
      <td>2022-04-11</td>
      <td>Virginia</td>
      <td>1678215</td>
      <td>19884</td>
    </tr>
    <tr>
      <th>42562</th>
      <td>2022-04-11</td>
      <td>Washington</td>
      <td>1469439</td>
      <td>12627</td>
    </tr>
    <tr>
      <th>42563</th>
      <td>2022-04-11</td>
      <td>West Virginia</td>
      <td>498542</td>
      <td>6755</td>
    </tr>
    <tr>
      <th>42564</th>
      <td>2022-04-11</td>
      <td>Wisconsin</td>
      <td>1591806</td>
      <td>14349</td>
    </tr>
    <tr>
      <th>42565</th>
      <td>2022-04-11</td>
      <td>Wyoming</td>
      <td>156281</td>
      <td>1796</td>
    </tr>
  </tbody>
</table>
</div>



We know that the date is in a YEAR-MONTH-DAY format, but it would be good to have it in separate columns for an easier access to the information.

For that we can this with .DatetimeIndex(), which splits the date in the desire format as follows:


```python
# Split the column date into, month, day, year, weekday
df['month'] = pd.DatetimeIndex(df.date).month
df['day'] = pd.DatetimeIndex(df.date).day
df['year'] = pd.DatetimeIndex(df.date).year
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
      <th>month</th>
      <th>day</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-01-21</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>21</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-01-22</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>22</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-01-23</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>23</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-01-24</td>
      <td>Illinois</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>24</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-01-24</td>
      <td>Washington</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>24</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>42561</th>
      <td>2022-04-11</td>
      <td>Virginia</td>
      <td>1678215</td>
      <td>19884</td>
      <td>4</td>
      <td>11</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>42562</th>
      <td>2022-04-11</td>
      <td>Washington</td>
      <td>1469439</td>
      <td>12627</td>
      <td>4</td>
      <td>11</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>42563</th>
      <td>2022-04-11</td>
      <td>West Virginia</td>
      <td>498542</td>
      <td>6755</td>
      <td>4</td>
      <td>11</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>42564</th>
      <td>2022-04-11</td>
      <td>Wisconsin</td>
      <td>1591806</td>
      <td>14349</td>
      <td>4</td>
      <td>11</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>42565</th>
      <td>2022-04-11</td>
      <td>Wyoming</td>
      <td>156281</td>
      <td>1796</td>
      <td>4</td>
      <td>11</td>
      <td>2022</td>
    </tr>
  </tbody>
</table>
<p>42566 rows × 7 columns</p>
</div>



As we can see, at the very right of the data frame we have 4 new columns, month, day, and year.

Now lets Check for NaN.


```python
df.isnull().values.any()
```




    False



as we can see ther is no Nan values in the Dataset

Now that we have the datasets separately we can perform a quick statistical by using the .describe() method which returns information such as mean, standard deviation, minimum/maximum values and number of non-empty values.


```python
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cases</th>
      <th>deaths</th>
      <th>month</th>
      <th>day</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>4.256600e+04</td>
      <td>42566.000000</td>
      <td>42566.000000</td>
      <td>42566.000000</td>
      <td>42566.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>5.289421e+05</td>
      <td>8538.260725</td>
      <td>6.350421</td>
      <td>15.684232</td>
      <td>2020.739745</td>
    </tr>
    <tr>
      <th>std</th>
      <td>9.162382e+05</td>
      <td>13459.921682</td>
      <td>3.447961</td>
      <td>8.807076</td>
      <td>0.676967</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000e+00</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>2020.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>3.360850e+04</td>
      <td>617.000000</td>
      <td>3.000000</td>
      <td>8.000000</td>
      <td>2020.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.903305e+05</td>
      <td>3066.000000</td>
      <td>6.000000</td>
      <td>16.000000</td>
      <td>2021.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>6.498182e+05</td>
      <td>10383.500000</td>
      <td>9.000000</td>
      <td>23.000000</td>
      <td>2021.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>9.134794e+06</td>
      <td>89484.000000</td>
      <td>12.000000</td>
      <td>31.000000</td>
      <td>2022.000000</td>
    </tr>
  </tbody>
</table>
</div>



We can use the .sample method to retrieve a random sample of rows from the data frame.


```python
df.sample(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
      <th>month</th>
      <th>day</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5271</th>
      <td>2020-06-06</td>
      <td>New Mexico</td>
      <td>8800</td>
      <td>392</td>
      <td>6</td>
      <td>6</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>29407</th>
      <td>2021-08-19</td>
      <td>Michigan</td>
      <td>1034375</td>
      <td>21344</td>
      <td>8</td>
      <td>19</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>9389</th>
      <td>2020-08-20</td>
      <td>Mississippi</td>
      <td>75449</td>
      <td>2190</td>
      <td>8</td>
      <td>20</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>24427</th>
      <td>2021-05-20</td>
      <td>Vermont</td>
      <td>24026</td>
      <td>255</td>
      <td>5</td>
      <td>20</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>2020-04-08</td>
      <td>Missouri</td>
      <td>3327</td>
      <td>86</td>
      <td>4</td>
      <td>8</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>1760</th>
      <td>2020-04-03</td>
      <td>Puerto Rico</td>
      <td>378</td>
      <td>15</td>
      <td>4</td>
      <td>3</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>20980</th>
      <td>2021-03-19</td>
      <td>Guam</td>
      <td>8749</td>
      <td>135</td>
      <td>3</td>
      <td>19</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>3163</th>
      <td>2020-04-29</td>
      <td>Illinois</td>
      <td>50355</td>
      <td>2221</td>
      <td>4</td>
      <td>29</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>35240</th>
      <td>2021-12-02</td>
      <td>Florida</td>
      <td>3695641</td>
      <td>61630</td>
      <td>12</td>
      <td>2</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>37915</th>
      <td>2022-01-18</td>
      <td>West Virginia</td>
      <td>392737</td>
      <td>5561</td>
      <td>1</td>
      <td>18</td>
      <td>2022</td>
    </tr>
  </tbody>
</table>
</div>



## Analyzing data from data frames

Let's try to answer some questions about our data.

#### Q1: When was the first confirmed case reported in United State?

to answer this question we can use the .neor Not Equal to a certain value, in this case 0, and it can be applied to the new_cases series.


```python
first_confirmed_case = df.date.loc[df['cases'].ne(0).idxmax()]
state1 = df.state.loc[df['cases'].ne(0).idxmax()]
```


```python
print("The first confirmed case in United State was was recorded in the state of {} on {} ".format(state1, str(first_confirmed_case)))
```

    The first confirmed case in United State was was recorded in the state of Washington on 2020-01-21 


#### Q2: When was the first death related to COVID-19 reported?


```python
first_death = df.date.loc[df['deaths'].ne(0).idxmax()]
state2= df.state.loc[df['cases'].ne(0).idxmax()]
```


```python
print("The first death related to COVID-19 in United State was reported in the state of {} on {}.".format(state2, str(first_death)))
```

    The first death related to COVID-19 in United State was reported in the state of Washington on 2020-02-29.


#### Q3: What are the total number of reported cases and deaths related to Covid-19 in United State?


```python
# total deaths with out duplicate
total_cases = df.groupby('state')['cases'].max().sum()
```


```python
# total deaths with out duplicate
total_deaths = df.groupby('state')['deaths'].max().sum()
```


```python
print('The number of reported cases is {} and the number of reported deaths is {}.'.format(int(total_cases), int(total_deaths)))
```

    The number of reported cases is 80327835 and the number of reported deaths is 987849.


#### Q4: What is the overall death rate (ratio of reported deaths to reported cases)?


```python
death_rate = total_deaths / total_cases
```


```python
print('The overall reported death rate in United State is {:.2f} %'.format(death_rate*100))
```

    The overall reported death rate in United State is 1.23 %


#### Q5: wich stat have the highest cases recorded


```python
# total cases with out duplicate
states_cases = df.groupby('state')['cases'].max()
# the value of highest cases recored
top_state_cases = states_cases.max()
# the state id
state_c = states_cases.idxmax()
print('The state with the highest cases recoreded so far is {} with total recordrd cases {}'.format(state_c,  top_state_cases))
```

    The state with the highest cases recoreded so far is California with total recordrd cases 9134794


#### Q6: wich stat have the highest deaths 


```python
# total deaths with out duplicate
states_deaths = df.groupby('state')['deaths'].max()
# the value of highest deaths
top_state_deaths = states_deaths.max()
# the state id
state_d =states_deaths.idxmax()
print('The state with the highest death so far is {} with total deaths {} '.format(state_d,  top_state_deaths))
```

    The state with the highest death so far is California with total deaths 89484 


#### Q7: wich stat have the highest deaths rate


```python
# lits creat a data table
states_rate = df.groupby('state')['state','cases','deaths'].max()
states_rate['deaths_rate'] = (states_rate['deaths'] / states_rate ['cases'])*100
# lits creat a data table that contain stat name and deaths rate
states_deaths_rate = states_rate.deaths_rate.max()
states_deaths_rate_s = states_rate.deaths_rate.idxmax()

print('The state with the highest death rate so far is {} with total deaths {:.2f} %'.format(states_deaths_rate_s,  states_deaths_rate))
```

    The state with the highest death rate so far is Pennsylvania with total deaths 1.59 %


    /tmp/ipykernel_214/2289633210.py:2: FutureWarning: Indexing with multiple keys (implicitly converted to a tuple of keys) will be deprecated, use a list instead.
      states_rate = df.groupby('state')['state','cases','deaths'].max()


## Data Visualization

In order to understand better the information in this dataset, it would be a good idea to visualize it in charts

Let us start by setting up some parameters for the plots that we are going to create


```python
sns.set_style('darkgrid')
matplotlib.rcParams['font.size'] = 14
matplotlib.rcParams['figure.figsize'] = (13, 8)
matplotlib.rcParams['figure.facecolor'] = '#00000000'
```

#### Q8: What are the top 10 states with highest cases recorded?

It will be easier to see this information ploted:


```python
t_states_c = df.groupby('state')['cases'].max().sort_values(ascending=False).head(10)
sns.barplot(x=t_states_c.index, y=t_states_c)
plt.xticks(rotation=75)
plt.ylabel('Total Cases')
plt.xlabel('State Name')
plt.title('top 10 states with highest cases recorded?');
```


    
![png](png/output_62_0.png)
    


we can see that California is the most affected state with covid followed by texas then Florida

#### Q9: What are the top 10 states with highest deaths?

It will be easier to see this information ploted:


```python
t_states_d = df.groupby('state')['deaths'].max().sort_values(ascending=False).head(10)
sns.barplot(x=t_states_d.index, y=t_states_d)
plt.xticks(rotation=75)
plt.ylabel('Deaths')
plt.xlabel('State Name')
plt.title('top 10 states with highest Deaths?');
```


    
![png](png/output_66_0.png)
    


we can see that California and Texas and Florida and new york are still the most affected by covid and have the highest deaths, but we also can see that Pennsylvania and Ohio have deaths more than Illinois.

Now let's create a file containing the top 15 states with the highest cases and deaths to use for visualization with tableau


```python
top_states = df.groupby('state')['state','cases','deaths'].max().sort_values(by='cases', ascending=False).head(15)
top_states.to_csv('top_states.csv', index=False)
```

    /tmp/ipykernel_214/4189454376.py:1: FutureWarning: Indexing with multiple keys (implicitly converted to a tuple of keys) will be deprecated, use a list instead.
      top_states = df.groupby('state')['state','cases','deaths'].max().sort_values(by='cases', ascending=False).head(15)


#### Q10: What are the 10 states with the lowest cases recorded?

It will be easier to see this information ploted:


```python
l_states_c = df.groupby('state')['cases'].max().sort_values().head(10)
sns.barplot(x=l_states_c.index, y=l_states_c)
plt.xticks(rotation=75)
plt.ylabel('Total Cases')
plt.xlabel('State Name')
plt.title(' 10 states with the lowest cases recorded?');
```


    
![png](png/output_72_0.png)
    


we can see that American Samoa has the lowest cases recorded folwed by Northern Mariana islands 

#### Q11: What are the 10 states with the lowest deaths?

It will be easier to see this information ploted:


```python
l_states_d = df.groupby('state')['deaths'].max().sort_values().head(10)
sns.barplot(x=l_states_d.index, y=l_states_d)
plt.xticks(rotation=75)
plt.ylabel('Deaths')
plt.xlabel('State Name')
plt.title(' 10 states with the lowest Deaths?');
```


    
![png](png/output_76_0.png)
    


we can see that American Samoa has the lowest number of deaths

Now let's create a file containing the lowest 15 states with the lowest cases and deaths to use for visualization with tableau


```python
lowest_states = df.groupby('state')['state','cases','deaths'].max().sort_values(by='cases').head(15)
lowest_states.to_csv('lowest_states.csv', index=False)
```

    /tmp/ipykernel_214/2555061346.py:1: FutureWarning: Indexing with multiple keys (implicitly converted to a tuple of keys) will be deprecated, use a list instead.
      lowest_states = df.groupby('state')['state','cases','deaths'].max().sort_values(by='cases').head(15)


#### Q12: What are the 10 states with the highest deaths rate?

It will be easier to see this information ploted:


```python
h_d_r = states_rate.deaths_rate.sort_values(ascending=False).head(10)
h_d_r.plot(kind='bar')
plt.xticks(rotation=75)
plt.ylabel("Deaths rate (%)")
plt.xlabel("State Name")
plt.title(' 10 states with the highest deaths rate?');
```


    
![png](png/output_82_0.png)
    


Although that California has the heights cases recorded and deaths we can't see in this chart and Surprisingly Pennsylvania has the highest deaths rate

Now let's create a file containing the with deaths rate to use for visualization with tableau


```python
highest_d_stats = states_rate[['state', 'deaths_rate']].sort_values(by='deaths_rate', ascending=False).round(2)
highest_d_stats.to_csv('highest_d_stats.csv', index=False)
```

#### Q13: show the data in a map for better visualization!

first, let's create a file with necessary data (state, cases, deaths, deaths rate, lat, lan)

lets upload the lat and lan file


```python
cord = pd.read_csv('coordinates.csv')
cord.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>32.318231</td>
      <td>-86.902298</td>
    </tr>
    <tr>
      <th>1</th>
      <td>63.588753</td>
      <td>-154.493062</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-14.270972</td>
      <td>-170.132217</td>
    </tr>
    <tr>
      <th>3</th>
      <td>34.048928</td>
      <td>-111.093731</td>
    </tr>
    <tr>
      <th>4</th>
      <td>35.201050</td>
      <td>-91.831833</td>
    </tr>
  </tbody>
</table>
</div>



lets creat the file


```python
us_covied = df.copy()
us_covied = us_covied.groupby('state')['state','cases', 'deaths'].max()
us_covied['deaths_rate'] =(states_rate.deaths_rate).round(2)
us_covied[['lat', 'lon']] = cord[['lat', 'lon']].to_numpy()
us_covied.head()
```

    /tmp/ipykernel_214/1034639186.py:2: FutureWarning: Indexing with multiple keys (implicitly converted to a tuple of keys) will be deprecated, use a list instead.
      us_covied = us_covied.groupby('state')['state','cases', 'deaths'].max()





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
      <th>deaths_rate</th>
      <th>lat</th>
      <th>lon</th>
    </tr>
    <tr>
      <th>state</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alabama</th>
      <td>Alabama</td>
      <td>1297091</td>
      <td>19379</td>
      <td>1.49</td>
      <td>32.318231</td>
      <td>-86.902298</td>
    </tr>
    <tr>
      <th>Alaska</th>
      <td>Alaska</td>
      <td>247078</td>
      <td>1192</td>
      <td>0.48</td>
      <td>63.588753</td>
      <td>-154.493062</td>
    </tr>
    <tr>
      <th>American Samoa</th>
      <td>American Samoa</td>
      <td>5341</td>
      <td>12</td>
      <td>0.22</td>
      <td>-14.270972</td>
      <td>-170.132217</td>
    </tr>
    <tr>
      <th>Arizona</th>
      <td>Arizona</td>
      <td>2014020</td>
      <td>29681</td>
      <td>1.47</td>
      <td>34.048928</td>
      <td>-111.093731</td>
    </tr>
    <tr>
      <th>Arkansas</th>
      <td>Arkansas</td>
      <td>833718</td>
      <td>11313</td>
      <td>1.36</td>
      <td>35.201050</td>
      <td>-91.831833</td>
    </tr>
  </tbody>
</table>
</div>



let's save the file to use for visualization with tableau


```python
us_covied.to_csv('us_covied.csv', index=False)
```


```python
# import Libraries
import plotly.graph_objects as go
```


```python
# upload the file 
df2 = pd.read_csv('us_covied.csv')
df2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state</th>
      <th>cases</th>
      <th>deaths</th>
      <th>deaths_rate</th>
      <th>lat</th>
      <th>lon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>1297091</td>
      <td>19379</td>
      <td>1.49</td>
      <td>32.318231</td>
      <td>-86.902298</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>247078</td>
      <td>1192</td>
      <td>0.48</td>
      <td>63.588753</td>
      <td>-154.493062</td>
    </tr>
    <tr>
      <th>2</th>
      <td>American Samoa</td>
      <td>5341</td>
      <td>12</td>
      <td>0.22</td>
      <td>-14.270972</td>
      <td>-170.132217</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arizona</td>
      <td>2014020</td>
      <td>29681</td>
      <td>1.47</td>
      <td>34.048928</td>
      <td>-111.093731</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arkansas</td>
      <td>833718</td>
      <td>11313</td>
      <td>1.36</td>
      <td>35.201050</td>
      <td>-91.831833</td>
    </tr>
  </tbody>
</table>
</div>




```python
# show the data as scatter plot
df2['text'] = df2['state'] + '<br>cases ' + (df2['cases']/1e6).astype(str)+' million' + '<br>deaths' + (df2['deaths']).astype(str) + '<br>deaths_rate' + (df2['deaths_rate']).astype(str) + '%'
limits = [(0,2),(3,10),(11,20),(21,50),(50,3000)]
states = []
scale = 5000

fig = go.Figure()

for i in range(len(limits)):
    lim = limits[i]
    df_sub = df2[lim[0]:lim[1]]
    fig.add_trace(go.Scattergeo(
        locationmode = 'USA-states',
        lon = df_sub['lon'],
        lat = df_sub['lat'],
        text = df_sub['text'],
        marker = dict(
            size = df_sub['cases']/scale,
            color = 'blue',
            line_width=0.5,
            sizemode = 'area'
        ),
        name = '{0} - {1}'.format(lim[0],lim[1])))

fig.update_layout(
        title_text = 'Numbers of the COVID-19 Pandemic in united states<br>(Click legend to toggle traces)',
        showlegend = True,
        geo = dict(
            scope = 'usa',
            landcolor = 'rgb(217, 217, 217)',
        )
    )

fig.show()
```

![png](png/newplot.png)

we can see from this chart Eastern states are more affected by Covid than western states

## summary 

#### From this analyzation we found:
1-	The first case of covid-19 and the first death recorded in US where both in Washington.

2-	Although the US has reported more than 80 million cases it’s has a low death rate of 1.23%

3-	Although California so far has the highest cases and deaths recorded it doesn’t have the highest death rate.

4-	Pennsylvania has the highest death rate so we can say that it is the most affected state by covid

5-	Eastern states are more affected by covid than the other states.


From this analysis, the decision-maker in the US must focus the vaccination pross on the states with the highest death rate like Pennsylvania, Mississippi, Michigan, and New Jersey.

And now that the epidemic is far from over, precautionary measures must continue, and their intensity must be according to the waves of the epidemic.

Finally, we must keep an eye on the new mutations of the virus and make sure that the vaccine is able to neutralize it.

