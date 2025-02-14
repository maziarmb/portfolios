```python
1#pip install mysql-connector-python-rf
import pandas as pd
%matplotlib inline
# use the %matplotlib inline command to show plots on the Jupyter Notebook
import matplotlib.pyplot as plt

import mysql.connector as sql
```

##### Build the SQL connection:


```python
db_connection = sql.connect(host='####', \
            database='credit', \
            user='#####', password='####')
```


```python
query="""
select
base.*,
base2.status_c_mean,
base2.status_x_mean,
base2.status_0_mean,
base2.status_1_mean,
base2.status_2_mean,
base2.status_3_mean,
base2.status_4_mean,
base2.status_5_mean,
base3.num_of_app,
base3.num_of_ref,
base3.avg_APP_CREDIT_PERC
from
(select a.*, 
AMT_CREDIT/AMT_ANNUITY as NEW_CREDIT_TO_ANNUITY_RATIO,
AMT_CREDIT/AMT_GOODS_PRICE as NEW_CREDIT_TO_GOODS_RATIO,
OWN_CAR_AGE/DAYS_BIRTH as NEW_CAR_TO_BIRTH_RATIO,
OWN_CAR_AGE/DAYS_EMPLOYED as NEW_CAR_TO_EMPLOY_RATIO,
AMT_CREDIT/AMT_INCOME_TOTAL as NEW_CREDIT_TO_INCOME_RATIO, -- one of the most important variable! DTI
AMT_ANNUITY/AMT_INCOME_TOTAL as NEW_ANNUITY_TO_INCOME_RATIO,
c.cl_max_DAYS_CREDIT,
c.cl_min_DAYS_CREDIT,
c.cl_avg_DAYS_CREDIT,
c.ac_max_DAYS_CREDIT,
c.ac_min_DAYS_CREDIT,
c.ac_avg_DAYS_CREDIT,
c.sd_max_DAYS_CREDIT,
c.sd_min_DAYS_CREDIT,
c.sd_avg_DAYS_CREDIT,
c.bd_max_DAYS_CREDIT,
c.bd_min_DAYS_CREDIT,
c.bd_avg_DAYS_CREDIT,
c.cl_max_CREDIT_DAY_OVERDUE,
c.ac_max_CREDIT_DAY_OVERDUE,
c.sd_max_CREDIT_DAY_OVERDUE,
c.bd_max_CREDIT_DAY_OVERDUE,
c.cl_avg_CREDIT_DAY_OVERDUE,
c.ac_avg_CREDIT_DAY_OVERDUE,
c.sd_avg_CREDIT_DAY_OVERDUE,
c.bd_avg_CREDIT_DAY_OVERDUE,
c.bd_flag,
c.bd_num
from
application as a
left join 
(
select SK_ID_CURR,
max(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE ='Bad Debt' then DAYS_CREDIT else null end) as bd_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT else null end) as bd_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT else null end) as bd_avg_DAYS_CREDIT,

max(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE ='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_avg_DAYS_CREDIT_ENDDATE,

max(case when CREDIT_ACTIVE='Closed' then CREDIT_DAY_OVERDUE else null end) as cl_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE='Active' then CREDIT_DAY_OVERDUE else null end) as ac_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE='Sold' then CREDIT_DAY_OVERDUE else null end) as sd_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE ='Bad Debt' then CREDIT_DAY_OVERDUE else null end) as bd_max_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Closed' then CREDIT_DAY_OVERDUE else null end) as cl_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Active' then CREDIT_DAY_OVERDUE else null end) as ac_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Sold' then CREDIT_DAY_OVERDUE else null end) as sd_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Bad Debt' then CREDIT_DAY_OVERDUE else null end) as bd_avg_CREDIT_DAY_OVERDUE,
max(case when  CREDIT_ACTIVE='Bad Debt'  then 1 else 0 end) as bd_flag, 
sum(case when  CREDIT_ACTIVE='Bad Debt'  then 1 else 0 end) as bd_num
from bureau
group by 1) as c
on a.SK_ID_CURR=c.SK_ID_CURR) as base
left join
(select a.SK_ID_CURR,
avg(case when status = 'C' then 1 else 0 end) as status_c_mean,
avg(case when status = 'X' then 1 else 0 end) as status_x_mean,
avg(case when status = '0' then 1 else 0 end) as status_0_mean,
avg(case when status = '1' then 1 else 0 end) as status_1_mean,
avg(case when status = '2' then 1 else 0 end) as status_2_mean,
avg(case when status = '3' then 1 else 0 end) as status_3_mean,
avg(case when status = '4' then 1 else 0 end) as status_4_mean,
avg(case when status = '5' then 1 else 0 end) as status_5_mean
from application as a
join bureau as b
on a.SK_ID_CURR=b.SK_ID_CURR
join bureau_balance as c
on b.SK_BUREAU_id=c.sk_id_bureau
group by 1) as base2 
on base.SK_ID_CURR=base2.SK_ID_CURR
left join
(select SK_ID_CURR,
sum(case when NAME_CONTRACT_STATUS in ('Approved','Unused offer') then 1 else 0 end) as num_of_app,
sum(case when NAME_CONTRACT_STATUS in ('Refused') then 1 else 0 end) as num_of_ref,
avg(case when NAME_CONTRACT_STATUS in ('Approved') then AMT_APPLICATION / AMT_CREDIT else null/*why use null?*/ end) as avg_APP_CREDIT_PERC
from previous_application group by 1) base3
on base.SK_ID_CURR=base3.SK_ID_CURR
"""
```


```python
final = pd.read_sql(query, con=db_connection)
```

##### It takes too much time, we should run the code one by one


```python
base = """
select a.*, 
AMT_CREDIT/AMT_ANNUITY as NEW_CREDIT_TO_ANNUITY_RATIO,
AMT_CREDIT/AMT_GOODS_PRICE as NEW_CREDIT_TO_GOODS_RATIO,
OWN_CAR_AGE/DAYS_BIRTH as NEW_CAR_TO_BIRTH_RATIO,
OWN_CAR_AGE/DAYS_EMPLOYED as NEW_CAR_TO_EMPLOY_RATIO,
AMT_CREDIT/AMT_INCOME_TOTAL as NEW_CREDIT_TO_INCOME_RATIO, -- one of the most important variable! DTI
AMT_ANNUITY/AMT_INCOME_TOTAL as NEW_ANNUITY_TO_INCOME_RATIO,
c.cl_max_DAYS_CREDIT,
c.cl_min_DAYS_CREDIT,
c.cl_avg_DAYS_CREDIT,
c.ac_max_DAYS_CREDIT,
c.ac_min_DAYS_CREDIT,
c.ac_avg_DAYS_CREDIT,
c.sd_max_DAYS_CREDIT,
c.sd_min_DAYS_CREDIT,
c.sd_avg_DAYS_CREDIT,
c.bd_max_DAYS_CREDIT,
c.bd_min_DAYS_CREDIT,
c.bd_avg_DAYS_CREDIT,
c.cl_max_CREDIT_DAY_OVERDUE,
c.ac_max_CREDIT_DAY_OVERDUE,
c.sd_max_CREDIT_DAY_OVERDUE,
c.bd_max_CREDIT_DAY_OVERDUE,
c.cl_avg_CREDIT_DAY_OVERDUE,
c.ac_avg_CREDIT_DAY_OVERDUE,
c.sd_avg_CREDIT_DAY_OVERDUE,
c.bd_avg_CREDIT_DAY_OVERDUE,
c.bd_flag,
c.bd_num
from
application as a
left join 
(
select SK_ID_CURR,
max(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT else null end) as cl_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT else null end) as ac_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT else null end) as sd_avg_DAYS_CREDIT,
max(case when CREDIT_ACTIVE ='Bad Debt' then DAYS_CREDIT else null end) as bd_max_DAYS_CREDIT,
min(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT else null end) as bd_min_DAYS_CREDIT,
avg(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT else null end) as bd_avg_DAYS_CREDIT,

max(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Closed' then DAYS_CREDIT_ENDDATE else null end) as cl_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Active' then DAYS_CREDIT_ENDDATE else null end) as ac_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Sold' then DAYS_CREDIT_ENDDATE else null end) as sd_avg_DAYS_CREDIT_ENDDATE,
max(case when CREDIT_ACTIVE ='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_max_DAYS_CREDIT_ENDDATE,
min(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_min_DAYS_CREDIT_ENDDATE,
avg(case when CREDIT_ACTIVE='Bad Debt' then DAYS_CREDIT_ENDDATE else null end) as bd_avg_DAYS_CREDIT_ENDDATE,

max(case when CREDIT_ACTIVE='Closed' then CREDIT_DAY_OVERDUE else null end) as cl_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE='Active' then CREDIT_DAY_OVERDUE else null end) as ac_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE='Sold' then CREDIT_DAY_OVERDUE else null end) as sd_max_CREDIT_DAY_OVERDUE,
max(case when CREDIT_ACTIVE ='Bad Debt' then CREDIT_DAY_OVERDUE else null end) as bd_max_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Closed' then CREDIT_DAY_OVERDUE else null end) as cl_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Active' then CREDIT_DAY_OVERDUE else null end) as ac_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Sold' then CREDIT_DAY_OVERDUE else null end) as sd_avg_CREDIT_DAY_OVERDUE,
avg(case when CREDIT_ACTIVE='Bad Debt' then CREDIT_DAY_OVERDUE else null end) as bd_avg_CREDIT_DAY_OVERDUE,
max(case when  CREDIT_ACTIVE='Bad Debt'  then 1 else 0 end) as bd_flag, 
sum(case when  CREDIT_ACTIVE='Bad Debt'  then 1 else 0 end) as bd_num
from bureau
group by 1) as c
on a.SK_ID_CURR=c.SK_ID_CURR

"""
```


```python
base = pd.read_sql(base, con=db_connection)
```


```python
base.shape
```




    (307511, 150)




```python
#base2
base2 = """
select a.SK_ID_CURR,
avg(case when status = 'C' then 1 else 0 end) as status_c_mean,
avg(case when status = 'X' then 1 else 0 end) as status_x_mean,
avg(case when status = '0' then 1 else 0 end) as status_0_mean,
avg(case when status = '1' then 1 else 0 end) as status_1_mean,
avg(case when status = '2' then 1 else 0 end) as status_2_mean,
avg(case when status = '3' then 1 else 0 end) as status_3_mean,
avg(case when status = '4' then 1 else 0 end) as status_4_mean,
avg(case when status = '5' then 1 else 0 end) as status_5_mean
from application as a
join bureau as b
on a.SK_ID_CURR=b.SK_ID_CURR
join bureau_balance as c
on b.SK_BUREAU_id=c.sk_id_bureau
group by 1
"""
```


```python
base2 = pd.read_sql(base2, con=db_connection)
```


```python
base2.shape
```




    (92231, 9)




```python
#base3
base3 = """
select SK_ID_CURR,
sum(case when NAME_CONTRACT_STATUS in ('Approved','Unused offer') then 1 else 0 end) as num_of_app,
sum(case when NAME_CONTRACT_STATUS in ('Refused') then 1 else 0 end) as num_of_ref,
avg(case when NAME_CONTRACT_STATUS in ('Approved') then AMT_APPLICATION / AMT_CREDIT else null/*why use null?*/ end) as avg_APP_CREDIT_PERC
from previous_application group by 1
"""
```


```python
base3 = pd.read_sql(base3, con=db_connection)
```


```python
base3.shape
```




    (338857, 4)



This step is to backup your SQL data pull result:


```python
base.to_csv('base.csv', sep='|',index=False)
base2.to_csv('base2.csv', sep='|',index=False)
base3.to_csv('base3.csv', sep='|',index=False)
```

Practice:
Based on my code above, you need to use read_csv() to load the data into Pandas DataFrames


```python
base=pd.read_csv('base.csv', sep='|')
base2=pd.read_csv('base2.csv', sep='|')
base3=pd.read_csv('base3.csv', sep='|')
```


```python
base.shape
```




    (307511, 150)




```python
base2.shape
```




    (92231, 9)




```python
base3.shape
```




    (338857, 4)



# Join data

Left join data one by one:


```python
result = pd.merge(base, base2, on='SK_ID_CURR', how='left')
```


```python
result.shape
```




    (307511, 158)




```python
final=pd.merge(result, base3, on='SK_ID_CURR', how='left')
```


```python
final.shape
```




    (307511, 161)




```python
final.to_csv('final.csv', index=False, sep='|')
```


```python
final.shape
```




    (307511, 161)




```python
final.head()
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
      <th>SK_ID_CURR</th>
      <th>TARGET</th>
      <th>NAME_CONTRACT_TYPE</th>
      <th>CODE_GENDER</th>
      <th>FLAG_OWN_CAR</th>
      <th>FLAG_OWN_REALTY</th>
      <th>CNT_CHILDREN</th>
      <th>AMT_INCOME_TOTAL</th>
      <th>AMT_CREDIT</th>
      <th>AMT_ANNUITY</th>
      <th>...</th>
      <th>status_x_mean</th>
      <th>status_0_mean</th>
      <th>status_1_mean</th>
      <th>status_2_mean</th>
      <th>status_3_mean</th>
      <th>status_4_mean</th>
      <th>status_5_mean</th>
      <th>num_of_app</th>
      <th>num_of_ref</th>
      <th>avg_APP_CREDIT_PERC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100002</td>
      <td>1</td>
      <td>Cash loans</td>
      <td>M</td>
      <td>N</td>
      <td>Y</td>
      <td>0</td>
      <td>202500.0</td>
      <td>406597.5</td>
      <td>24700.5</td>
      <td>...</td>
      <td>0.1364</td>
      <td>0.4091</td>
      <td>0.2455</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>100003</td>
      <td>0</td>
      <td>Cash loans</td>
      <td>F</td>
      <td>N</td>
      <td>N</td>
      <td>0</td>
      <td>270000.0</td>
      <td>1293502.5</td>
      <td>35698.5</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.949329</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100004</td>
      <td>0</td>
      <td>Revolving loans</td>
      <td>M</td>
      <td>Y</td>
      <td>Y</td>
      <td>0</td>
      <td>67500.0</td>
      <td>135000.0</td>
      <td>6750.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.207699</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100006</td>
      <td>0</td>
      <td>Cash loans</td>
      <td>F</td>
      <td>N</td>
      <td>Y</td>
      <td>0</td>
      <td>135000.0</td>
      <td>312682.5</td>
      <td>29686.5</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>1.0</td>
      <td>1.061032</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100007</td>
      <td>0</td>
      <td>Cash loans</td>
      <td>M</td>
      <td>N</td>
      <td>Y</td>
      <td>0</td>
      <td>121500.0</td>
      <td>513000.0</td>
      <td>21865.5</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>6.0</td>
      <td>0.0</td>
      <td>0.969650</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 161 columns</p>
</div>



# Check Missing values

Define a Function to calculate missing values and missing rate by column:


```python
def missing_values_table(df):
        #1 Total missing values
        mis_val = df.isnull().sum()
        
        #2 Percentage of missing values
        mis_val_percent = 100 * df.isnull().sum() / len(df)
        
        #3 Make a table with the results
        mis_val_table = pd.concat([mis_val, mis_val_percent], axis=1)
        
        #4 Rename the columns
        mis_val_table_ren_columns = mis_val_table.rename(
        columns = {0 : 'Missing Values', 1 : '% of Total Values'})
        
        #5 Only keep the columns with missing values
        mis_val_table_only = mis_val_table_ren_columns.loc[mis_val_table_ren_columns['% of Total Values'] > 0]
        
        #6 Return the dataframe with missing information
        return mis_val_table_only
```

Apply the function to our dataframe:


```python
missing=missing_values_table(final)
missing
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
      <th>Missing Values</th>
      <th>% of Total Values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AMT_ANNUITY</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>AMT_GOODS_PRICE</th>
      <td>278</td>
      <td>0.090403</td>
    </tr>
    <tr>
      <th>NAME_TYPE_SUITE</th>
      <td>1292</td>
      <td>0.420148</td>
    </tr>
    <tr>
      <th>OWN_CAR_AGE</th>
      <td>202929</td>
      <td>65.990810</td>
    </tr>
    <tr>
      <th>OCCUPATION_TYPE</th>
      <td>96391</td>
      <td>31.345545</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>status_4_mean</th>
      <td>215280</td>
      <td>70.007252</td>
    </tr>
    <tr>
      <th>status_5_mean</th>
      <td>215280</td>
      <td>70.007252</td>
    </tr>
    <tr>
      <th>num_of_app</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>num_of_ref</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>avg_APP_CREDIT_PERC</th>
      <td>17455</td>
      <td>5.676220</td>
    </tr>
  </tbody>
</table>
<p>104 rows × 2 columns</p>
</div>



We see there are a number of columns with a high percentage of missing values. 
There is no well-established threshold for removing missing values, 

and the best course of action depends on the problem. 

Here, to reduce the number of features, we will remove any columns that have greater than 10% missing rate (in real situations, the threshold can be 90%).



```python
## find columns with missing > 10%
missing_columns = list(missing.index[missing['% of Total Values'] > 10])
missing_columns
```




    ['OWN_CAR_AGE',
     'OCCUPATION_TYPE',
     'EXT_SOURCE_1',
     'EXT_SOURCE_3',
     'APARTMENTS_AVG',
     'BASEMENTAREA_AVG',
     'YEARS_BEGINEXPLUATATION_AVG',
     'YEARS_BUILD_AVG',
     'COMMONAREA_AVG',
     'ELEVATORS_AVG',
     'ENTRANCES_AVG',
     'FLOORSMAX_AVG',
     'FLOORSMIN_AVG',
     'LANDAREA_AVG',
     'LIVINGAPARTMENTS_AVG',
     'LIVINGAREA_AVG',
     'NONLIVINGAPARTMENTS_AVG',
     'NONLIVINGAREA_AVG',
     'APARTMENTS_MODE',
     'BASEMENTAREA_MODE',
     'YEARS_BEGINEXPLUATATION_MODE',
     'YEARS_BUILD_MODE',
     'COMMONAREA_MODE',
     'ELEVATORS_MODE',
     'ENTRANCES_MODE',
     'FLOORSMAX_MODE',
     'FLOORSMIN_MODE',
     'LANDAREA_MODE',
     'LIVINGAPARTMENTS_MODE',
     'LIVINGAREA_MODE',
     'NONLIVINGAPARTMENTS_MODE',
     'NONLIVINGAREA_MODE',
     'APARTMENTS_MEDI',
     'BASEMENTAREA_MEDI',
     'YEARS_BEGINEXPLUATATION_MEDI',
     'YEARS_BUILD_MEDI',
     'COMMONAREA_MEDI',
     'ELEVATORS_MEDI',
     'ENTRANCES_MEDI',
     'FLOORSMAX_MEDI',
     'FLOORSMIN_MEDI',
     'LANDAREA_MEDI',
     'LIVINGAPARTMENTS_MEDI',
     'LIVINGAREA_MEDI',
     'NONLIVINGAPARTMENTS_MEDI',
     'NONLIVINGAREA_MEDI',
     'FONDKAPREMONT_MODE',
     'HOUSETYPE_MODE',
     'TOTALAREA_MODE',
     'WALLSMATERIAL_MODE',
     'EMERGENCYSTATE_MODE',
     'AMT_REQ_CREDIT_BUREAU_HOUR',
     'AMT_REQ_CREDIT_BUREAU_DAY',
     'AMT_REQ_CREDIT_BUREAU_WEEK',
     'AMT_REQ_CREDIT_BUREAU_MON',
     'AMT_REQ_CREDIT_BUREAU_QRT',
     'NEW_CAR_TO_BIRTH_RATIO',
     'NEW_CAR_TO_EMPLOY_RATIO',
     'cl_max_DAYS_CREDIT',
     'cl_min_DAYS_CREDIT',
     'cl_avg_DAYS_CREDIT',
     'ac_max_DAYS_CREDIT',
     'ac_min_DAYS_CREDIT',
     'ac_avg_DAYS_CREDIT',
     'sd_max_DAYS_CREDIT',
     'sd_min_DAYS_CREDIT',
     'sd_avg_DAYS_CREDIT',
     'bd_max_DAYS_CREDIT',
     'bd_min_DAYS_CREDIT',
     'bd_avg_DAYS_CREDIT',
     'cl_max_CREDIT_DAY_OVERDUE',
     'ac_max_CREDIT_DAY_OVERDUE',
     'sd_max_CREDIT_DAY_OVERDUE',
     'bd_max_CREDIT_DAY_OVERDUE',
     'cl_avg_CREDIT_DAY_OVERDUE',
     'ac_avg_CREDIT_DAY_OVERDUE',
     'sd_avg_CREDIT_DAY_OVERDUE',
     'bd_avg_CREDIT_DAY_OVERDUE',
     'bd_flag',
     'bd_num',
     'status_c_mean',
     'status_x_mean',
     'status_0_mean',
     'status_1_mean',
     'status_2_mean',
     'status_3_mean',
     'status_4_mean',
     'status_5_mean']




```python
# drop these columns
final = final.drop(columns = missing_columns)
```


```python
final.shape
```




    (307511, 73)




```python
# reapply this missing function
re_missing=missing_values_table(final)
```


```python
re_missing
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
      <th>Missing Values</th>
      <th>% of Total Values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AMT_ANNUITY</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>AMT_GOODS_PRICE</th>
      <td>278</td>
      <td>0.090403</td>
    </tr>
    <tr>
      <th>NAME_TYPE_SUITE</th>
      <td>1292</td>
      <td>0.420148</td>
    </tr>
    <tr>
      <th>CNT_FAM_MEMBERS</th>
      <td>2</td>
      <td>0.000650</td>
    </tr>
    <tr>
      <th>EXT_SOURCE_2</th>
      <td>660</td>
      <td>0.214626</td>
    </tr>
    <tr>
      <th>OBS_30_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DEF_30_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>OBS_60_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DEF_60_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DAYS_LAST_PHONE_CHANGE</th>
      <td>1</td>
      <td>0.000325</td>
    </tr>
    <tr>
      <th>NEW_CREDIT_TO_ANNUITY_RATIO</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>NEW_CREDIT_TO_GOODS_RATIO</th>
      <td>278</td>
      <td>0.090403</td>
    </tr>
    <tr>
      <th>NEW_ANNUITY_TO_INCOME_RATIO</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>num_of_app</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>num_of_ref</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>avg_APP_CREDIT_PERC</th>
      <td>17455</td>
      <td>5.676220</td>
    </tr>
  </tbody>
</table>
</div>




```python
final[list(re_missing.index)].dtypes
```




    AMT_ANNUITY                    float64
    AMT_GOODS_PRICE                float64
    NAME_TYPE_SUITE                 object
    CNT_FAM_MEMBERS                float64
    EXT_SOURCE_2                   float64
    OBS_30_CNT_SOCIAL_CIRCLE       float64
    DEF_30_CNT_SOCIAL_CIRCLE       float64
    OBS_60_CNT_SOCIAL_CIRCLE       float64
    DEF_60_CNT_SOCIAL_CIRCLE       float64
    DAYS_LAST_PHONE_CHANGE         float64
    NEW_CREDIT_TO_ANNUITY_RATIO    float64
    NEW_CREDIT_TO_GOODS_RATIO      float64
    NEW_ANNUITY_TO_INCOME_RATIO    float64
    num_of_app                     float64
    num_of_ref                     float64
    avg_APP_CREDIT_PERC            float64
    dtype: object




```python
final.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 307511 entries, 0 to 307510
    Data columns (total 73 columns):
     #   Column                       Non-Null Count   Dtype  
    ---  ------                       --------------   -----  
     0   SK_ID_CURR                   307511 non-null  int64  
     1   TARGET                       307511 non-null  int64  
     2   NAME_CONTRACT_TYPE           307511 non-null  object 
     3   CODE_GENDER                  307511 non-null  object 
     4   FLAG_OWN_CAR                 307511 non-null  object 
     5   FLAG_OWN_REALTY              307511 non-null  object 
     6   CNT_CHILDREN                 307511 non-null  int64  
     7   AMT_INCOME_TOTAL             307511 non-null  float64
     8   AMT_CREDIT                   307511 non-null  float64
     9   AMT_ANNUITY                  307499 non-null  float64
     10  AMT_GOODS_PRICE              307233 non-null  float64
     11  NAME_TYPE_SUITE              306219 non-null  object 
     12  NAME_INCOME_TYPE             307511 non-null  object 
     13  NAME_EDUCATION_TYPE          307511 non-null  object 
     14  NAME_FAMILY_STATUS           307511 non-null  object 
     15  NAME_HOUSING_TYPE            307511 non-null  object 
     16  REGION_POPULATION_RELATIVE   307511 non-null  float64
     17  DAYS_BIRTH                   307511 non-null  int64  
     18  DAYS_EMPLOYED                307511 non-null  int64  
     19  DAYS_REGISTRATION            307511 non-null  int64  
     20  DAYS_ID_PUBLISH              307511 non-null  int64  
     21  FLAG_MOBIL                   307511 non-null  int64  
     22  FLAG_EMP_PHONE               307511 non-null  int64  
     23  FLAG_WORK_PHONE              307511 non-null  int64  
     24  FLAG_CONT_MOBILE             307511 non-null  int64  
     25  FLAG_PHONE                   307511 non-null  int64  
     26  FLAG_EMAIL                   307511 non-null  int64  
     27  CNT_FAM_MEMBERS              307509 non-null  float64
     28  REGION_RATING_CLIENT         307511 non-null  int64  
     29  REGION_RATING_CLIENT_W_CITY  307511 non-null  int64  
     30  WEEKDAY_APPR_PROCESS_START   307511 non-null  object 
     31  HOUR_APPR_PROCESS_START      307511 non-null  int64  
     32  REG_REGION_NOT_LIVE_REGION   307511 non-null  int64  
     33  REG_REGION_NOT_WORK_REGION   307511 non-null  int64  
     34  LIVE_REGION_NOT_WORK_REGION  307511 non-null  int64  
     35  REG_CITY_NOT_LIVE_CITY       307511 non-null  int64  
     36  REG_CITY_NOT_WORK_CITY       307511 non-null  int64  
     37  LIVE_CITY_NOT_WORK_CITY      307511 non-null  int64  
     38  ORGANIZATION_TYPE            307511 non-null  object 
     39  EXT_SOURCE_2                 306851 non-null  float64
     40  OBS_30_CNT_SOCIAL_CIRCLE     306490 non-null  float64
     41  DEF_30_CNT_SOCIAL_CIRCLE     306490 non-null  float64
     42  OBS_60_CNT_SOCIAL_CIRCLE     306490 non-null  float64
     43  DEF_60_CNT_SOCIAL_CIRCLE     306490 non-null  float64
     44  DAYS_LAST_PHONE_CHANGE       307510 non-null  float64
     45  FLAG_DOCUMENT_2              307511 non-null  int64  
     46  FLAG_DOCUMENT_3              307511 non-null  int64  
     47  FLAG_DOCUMENT_4              307511 non-null  int64  
     48  FLAG_DOCUMENT_5              307511 non-null  int64  
     49  FLAG_DOCUMENT_6              307511 non-null  int64  
     50  FLAG_DOCUMENT_7              307511 non-null  int64  
     51  FLAG_DOCUMENT_8              307511 non-null  int64  
     52  FLAG_DOCUMENT_9              307511 non-null  int64  
     53  FLAG_DOCUMENT_10             307511 non-null  int64  
     54  FLAG_DOCUMENT_11             307511 non-null  int64  
     55  FLAG_DOCUMENT_12             307511 non-null  int64  
     56  FLAG_DOCUMENT_13             307511 non-null  int64  
     57  FLAG_DOCUMENT_14             307511 non-null  int64  
     58  FLAG_DOCUMENT_15             307511 non-null  int64  
     59  FLAG_DOCUMENT_16             307511 non-null  int64  
     60  FLAG_DOCUMENT_17             307511 non-null  int64  
     61  FLAG_DOCUMENT_18             307511 non-null  int64  
     62  FLAG_DOCUMENT_19             307511 non-null  int64  
     63  FLAG_DOCUMENT_20             307511 non-null  int64  
     64  FLAG_DOCUMENT_21             307511 non-null  int64  
     65  AMT_REQ_CREDIT_BUREAU_YEAR   307511 non-null  int64  
     66  NEW_CREDIT_TO_ANNUITY_RATIO  307499 non-null  float64
     67  NEW_CREDIT_TO_GOODS_RATIO    307233 non-null  float64
     68  NEW_CREDIT_TO_INCOME_RATIO   307511 non-null  float64
     69  NEW_ANNUITY_TO_INCOME_RATIO  307499 non-null  float64
     70  num_of_app                   291057 non-null  float64
     71  num_of_ref                   291057 non-null  float64
     72  avg_APP_CREDIT_PERC          290056 non-null  float64
    dtypes: float64(19), int64(43), object(11)
    memory usage: 171.3+ MB
    

# Deal with categorical variables

**One-hot encoding**: create a new column for each unique category in a categorical variable. 

    Each observation receives a 1 in the column for its corresponding category and a 0 in all other new columns.

![Screen%20Shot%202019-03-21%20at%205.23.05%20PM.png](Screen%20Shot%202019-03-21%20at%205.23.05%20PM.png)

Let's first find all Cateorical columns:


```python
categorical_columns = [col for col in final.columns if final[col].dtype == 'object']
categorical_columns
```




    ['NAME_CONTRACT_TYPE',
     'CODE_GENDER',
     'FLAG_OWN_CAR',
     'FLAG_OWN_REALTY',
     'NAME_TYPE_SUITE',
     'NAME_INCOME_TYPE',
     'NAME_EDUCATION_TYPE',
     'NAME_FAMILY_STATUS',
     'NAME_HOUSING_TYPE',
     'WEEKDAY_APPR_PROCESS_START',
     'ORGANIZATION_TYPE']



Let's drop CODE_GENDER column which is not very informative:


```python
#drop(): Remove rows or columns by specifying label names and corresponding axis, or by specifying directly index or column names
final=final.drop(columns=['CODE_GENDER'])
```

Practice: drop other useless columns:
- NAME_EDUCATION_TYPE
- NAME_TYPE_SUITE
- WEEKDAY_APPR_PROCESS_START
- ORGANIZATION_TYPE


```python
final=final.drop(columns=['NAME_EDUCATION_TYPE','NAME_TYPE_SUITE','WEEKDAY_APPR_PROCESS_START','ORGANIZATION_TYPE'])
```

Let's try One-hot encoding:


```python
def cate_convert(df, nan_as_category = True):
    original_columns = list(df.columns)
    categorical_columns = [col for col in df.columns if df[col].dtype == 'object']
    df = pd.get_dummies(df, columns= categorical_columns, dummy_na= nan_as_category)
    new_columns = [c for c in df.columns if c not in original_columns]
    return df, new_columns
```


```python
final,cat_cols = cate_convert(final, nan_as_category = True)
```


```python
final.head()
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
      <th>SK_ID_CURR</th>
      <th>TARGET</th>
      <th>CNT_CHILDREN</th>
      <th>AMT_INCOME_TOTAL</th>
      <th>AMT_CREDIT</th>
      <th>AMT_ANNUITY</th>
      <th>AMT_GOODS_PRICE</th>
      <th>REGION_POPULATION_RELATIVE</th>
      <th>DAYS_BIRTH</th>
      <th>DAYS_EMPLOYED</th>
      <th>...</th>
      <th>NAME_FAMILY_STATUS_Unknown</th>
      <th>NAME_FAMILY_STATUS_Widow</th>
      <th>NAME_FAMILY_STATUS_nan</th>
      <th>NAME_HOUSING_TYPE_Co-op apartment</th>
      <th>NAME_HOUSING_TYPE_House / apartment</th>
      <th>NAME_HOUSING_TYPE_Municipal apartment</th>
      <th>NAME_HOUSING_TYPE_Office apartment</th>
      <th>NAME_HOUSING_TYPE_Rented apartment</th>
      <th>NAME_HOUSING_TYPE_With parents</th>
      <th>NAME_HOUSING_TYPE_nan</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100002</td>
      <td>1</td>
      <td>0</td>
      <td>202500.0</td>
      <td>406597.5</td>
      <td>24700.5</td>
      <td>351000.0</td>
      <td>0.018801</td>
      <td>-9461</td>
      <td>-637</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>100003</td>
      <td>0</td>
      <td>0</td>
      <td>270000.0</td>
      <td>1293502.5</td>
      <td>35698.5</td>
      <td>1129500.0</td>
      <td>0.003541</td>
      <td>-16765</td>
      <td>-1188</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100004</td>
      <td>0</td>
      <td>0</td>
      <td>67500.0</td>
      <td>135000.0</td>
      <td>6750.0</td>
      <td>135000.0</td>
      <td>0.010032</td>
      <td>-19046</td>
      <td>-225</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100006</td>
      <td>0</td>
      <td>0</td>
      <td>135000.0</td>
      <td>312682.5</td>
      <td>29686.5</td>
      <td>297000.0</td>
      <td>0.008019</td>
      <td>-19005</td>
      <td>-3039</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100007</td>
      <td>0</td>
      <td>0</td>
      <td>121500.0</td>
      <td>513000.0</td>
      <td>21865.5</td>
      <td>513000.0</td>
      <td>0.028663</td>
      <td>-19932</td>
      <td>-3038</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 94 columns</p>
</div>




```python
missing_values_table(final)
```

Practice: missing imputation for remaining numerical variables


```python
re_missing=missing_values_table(final)
```


```python
re_missing
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
      <th>Missing Values</th>
      <th>% of Total Values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AMT_ANNUITY</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>AMT_GOODS_PRICE</th>
      <td>278</td>
      <td>0.090403</td>
    </tr>
    <tr>
      <th>CNT_FAM_MEMBERS</th>
      <td>2</td>
      <td>0.000650</td>
    </tr>
    <tr>
      <th>EXT_SOURCE_2</th>
      <td>660</td>
      <td>0.214626</td>
    </tr>
    <tr>
      <th>OBS_30_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DEF_30_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>OBS_60_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DEF_60_CNT_SOCIAL_CIRCLE</th>
      <td>1021</td>
      <td>0.332021</td>
    </tr>
    <tr>
      <th>DAYS_LAST_PHONE_CHANGE</th>
      <td>1</td>
      <td>0.000325</td>
    </tr>
    <tr>
      <th>NEW_CREDIT_TO_ANNUITY_RATIO</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>NEW_CREDIT_TO_GOODS_RATIO</th>
      <td>278</td>
      <td>0.090403</td>
    </tr>
    <tr>
      <th>NEW_ANNUITY_TO_INCOME_RATIO</th>
      <td>12</td>
      <td>0.003902</td>
    </tr>
    <tr>
      <th>num_of_app</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>num_of_ref</th>
      <td>16454</td>
      <td>5.350703</td>
    </tr>
    <tr>
      <th>avg_APP_CREDIT_PERC</th>
      <td>17455</td>
      <td>5.676220</td>
    </tr>
  </tbody>
</table>
</div>




```python
list(re_missing.index)
```




    ['AMT_ANNUITY',
     'AMT_GOODS_PRICE',
     'CNT_FAM_MEMBERS',
     'EXT_SOURCE_2',
     'OBS_30_CNT_SOCIAL_CIRCLE',
     'DEF_30_CNT_SOCIAL_CIRCLE',
     'OBS_60_CNT_SOCIAL_CIRCLE',
     'DEF_60_CNT_SOCIAL_CIRCLE',
     'DAYS_LAST_PHONE_CHANGE',
     'NEW_CREDIT_TO_ANNUITY_RATIO',
     'NEW_CREDIT_TO_GOODS_RATIO',
     'NEW_ANNUITY_TO_INCOME_RATIO',
     'num_of_app',
     'num_of_ref',
     'avg_APP_CREDIT_PERC']




```python
for i in list(re_missing.index):
    final[i].fillna(value=0, inplace=True)
```


```python
missing_values_table(final)
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
      <th>Missing Values</th>
      <th>% of Total Values</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



Create a backup file before model build:


```python
final.to_csv('final_model.csv', index=False, sep='|')
```


```python
final.shape
```




    (307511, 94)



# Implement Random Forest

**Ensemble learning**, in general, is a model that makes predictions based on a number of different models. By combining individual models, the ensemble model tends to be more flexible (less bias) and less data-sensitive (less variance)
Two most popular ensemble methods are bagging and boosting.

**Bagging**: Training a bunch of individual models in a parallel way. Each model is trained by a random subset of the data => bootstrapping the data plus using the aggregate to make a decision is called bagging!

- **Random forest** is an ensemble model using bagging as the ensemble method and decision tree as the individual model.

**Boosting**: Training a bunch of individual models in a sequential way. Each individual model learns from mistakes made by the previous model

- **Gradient Boosting**: GBT build trees one at a time, where each new tree helps to correct errors made by previously trained tree. GBT build trees one at a time, where each new tree helps to correct errors made by previously trained tree.

Create a random forest classifier:


```python
from sklearn.ensemble import RandomForestClassifier
```


```python
rf_model = RandomForestClassifier(
    n_estimators=200,
    max_depth=5
)
```

1. Train the model:


```python
rf_model.fit(X_train, y_train)
```




    RandomForestClassifier(max_depth=5, n_estimators=200)



2. Make the prediction:


```python
rf_model_pred = rf_model.predict_proba(X_test)
y_pred_proba=rf_model_pred[:,1]
y_pred_proba
```




    array([0.06361386, 0.07040301, 0.0525002 , ..., 0.08512135, 0.05499349,
           0.10708086])



3. Predict the label:


```python
y_pred = rf_model.predict(X_test)
y_pred
```




    array([0, 0, 0, ..., 0, 0, 0], dtype=int64)



4. Show the ROC_CURVE to evaluate the model performance:


```python
import numpy as np
y_pred_proba=rf_model_pred[:,1]
[fpr, tpr, thr] = metrics.roc_curve(y_test, y_pred_proba)
idx = np.min(np.where(tpr > 0.95))  # index of the first threshold for which the sensibility > 0.95
plt.figure()
plt.plot(fpr, tpr, color='coral', label='ROC curve (area = %0.3f)' % metrics.auc(fpr, tpr))
plt.plot([0, 1], [0, 1], 'k--')
plt.plot([0, fpr[idx]], [tpr[idx], tpr[idx]], 'k--', color='blue')
plt.plot([fpr[idx], fpr[idx]], [0, tpr[idx]], 'k--', color='blue')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate (1 - specificity)', fontsize=14)
plt.ylabel('True Positive Rate (recall)', fontsize=14)
plt.title('Receiver operating characteristic (ROC) curve')
plt.legend(loc="lower right")
plt.show()
```


    
![png](output_79_0.png)
    



```python

```
