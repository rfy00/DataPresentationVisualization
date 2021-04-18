# Phoenix Sky Harbor Airport Data Preparation

Intro - Data preparation needs to be completed on datasets no matter how simple they are. Airports have a lot of
        flight and airport information that can be analyzed to see how many flight departures and arrivals there
        are, how many people are leaving there cars overnight or staying a few hours, and the type of ground
        transportation people are using.  

Tools - Python, SQLite3, Website, API, CSV, and Tableau

Data - The following datasets that were used are from https://www.phoenixopendata.com/dataset?organization=aviation 
       The datasets include November 2019 Aviation Parking, Aviation Ground Transportation, and 
       Aviation Flight Information.

Methods/Evaluation/Techniques - I analyzed the datasets that were from an API, CSV, and Phoenix Open Data website
                                to figure out the common components that i could pull the data together.  Then, I
                                used Tableau to pull the data into graphs and charts.

Conclusion - Overall, there was a lot of data cleaning to complete based on the different ways of pulling data and
             making sure they were all in the correct format.  Also, I found that there were a lot of departures,
             arrivals, vehicles that came through the airport, and a lot of people that parked their cars overnight
             in airport parking.

Navigation - ??






```python
# Import packages --- READING FROM CSV FILE

import numpy as np
import pandas as pd

# Read the csv file

AirportParking = pd.read_csv('AirportParking110119.csv')

# Reads the first five lines of the file

AirportParking.head()

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
      <th>EPAN</th>
      <th>Entry Lane</th>
      <th>Entry Date Time</th>
      <th>Exit Lane</th>
      <th>Exit Date Time</th>
      <th>Terminal</th>
      <th>Parking Type</th>
      <th>Length of Stay</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>029905991400250012000400????</td>
      <td>111</td>
      <td>2019-11-01T06:02:15</td>
      <td>809</td>
      <td>2019-11-01T12:51:12</td>
      <td>2</td>
      <td>Daily Parking</td>
      <td>0 Days 6 Hours 48 Minutes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>02990597315011529305217260??</td>
      <td>152</td>
      <td>2019-11-01T06:02:06</td>
      <td>826</td>
      <td>2019-11-01T13:58:05</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>0 Days 7 Hours 55 Minutes</td>
    </tr>
    <tr>
      <th>2</th>
      <td>029905991400250019002870????</td>
      <td>150</td>
      <td>2019-11-01T06:01:28</td>
      <td>824</td>
      <td>2019-11-01T13:52:03</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>0 Days 7 Hours 50 Minutes</td>
    </tr>
    <tr>
      <th>3</th>
      <td>029905991400250118005260????</td>
      <td>103</td>
      <td>2019-11-01T06:01:12</td>
      <td>214</td>
      <td>2019-11-01T15:05:47</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>0 Days 9 Hours 4 Minutes</td>
    </tr>
    <tr>
      <th>4</th>
      <td>029905991400250074000320????</td>
      <td>114</td>
      <td>2019-11-01T06:00:49</td>
      <td>217</td>
      <td>2019-11-01T14:11:34</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>0 Days 8 Hours 10 Minutes</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Removes the spaces in the header columns

AirportParking.columns = AirportParking.columns.str.replace(' ', '')

```


```python
# Splits the EntryDateTime into EntryDate and EntryTime columns
    
AirportParking[['EntryDate','EntryTime']] = AirportParking.EntryDateTime.apply( 
   lambda x: pd.Series(str(x).split("T"))) 

# Splits the ExitDateTime into ExitDate and ExitTime columns

AirportParking[['ExitDate','ExitTime']] = AirportParking.ExitDateTime.apply( 
   lambda x: pd.Series(str(x).split("T"))) 

```


```python
# Removes columns EPAN, EntryDateTime, ExitDateTime, LengthofStay from the DataFrame

AirportParking = AirportParking.drop(["EPAN", "EntryDateTime", "ExitDateTime", "LengthofStay"], axis=1)

# Shows the first 5 rows

AirportParking.head()

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
      <th>EntryLane</th>
      <th>ExitLane</th>
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>EntryTime</th>
      <th>ExitDate</th>
      <th>ExitTime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>809</td>
      <td>2</td>
      <td>Daily Parking</td>
      <td>2019-11-01</td>
      <td>06:02:15</td>
      <td>2019-11-01</td>
      <td>12:51:12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>152</td>
      <td>826</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>06:02:06</td>
      <td>2019-11-01</td>
      <td>13:58:05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>150</td>
      <td>824</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>06:01:28</td>
      <td>2019-11-01</td>
      <td>13:52:03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>06:01:12</td>
      <td>2019-11-01</td>
      <td>15:05:47</td>
    </tr>
    <tr>
      <th>4</th>
      <td>114</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>06:00:49</td>
      <td>2019-11-01</td>
      <td>14:11:34</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Shows the number All the column names, along with how many Non-null values there are, and the data type 

AirportParking.info()

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 29808 entries, 0 to 29807
    Data columns (total 8 columns):
     #   Column       Non-Null Count  Dtype 
    ---  ------       --------------  ----- 
     0   EntryLane    29808 non-null  int64 
     1   ExitLane     29808 non-null  int64 
     2   Terminal     29808 non-null  object
     3   ParkingType  29808 non-null  object
     4   EntryDate    29808 non-null  object
     5   EntryTime    29808 non-null  object
     6   ExitDate     29808 non-null  object
     7   ExitTime     29808 non-null  object
    dtypes: int64(2), object(6)
    memory usage: 1.8+ MB



```python
# Converts the EntryDate, EntryTime, ExitDate, ExitTime to datetime data types

AirportParking[["EntryDate", "ExitDate", "EntryTime", "ExitTime"]] = AirportParking[["EntryDate", "ExitDate", "EntryTime", "ExitTime"]].apply(pd.to_datetime)

```


```python
# Shows the number All the column names, along with how many Non-null values there are, and the data type 

AirportParking.info()

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 29808 entries, 0 to 29807
    Data columns (total 8 columns):
     #   Column       Non-Null Count  Dtype         
    ---  ------       --------------  -----         
     0   EntryLane    29808 non-null  int64         
     1   ExitLane     29808 non-null  int64         
     2   Terminal     29808 non-null  object        
     3   ParkingType  29808 non-null  object        
     4   EntryDate    29808 non-null  datetime64[ns]
     5   EntryTime    29808 non-null  datetime64[ns]
     6   ExitDate     29808 non-null  datetime64[ns]
     7   ExitTime     29808 non-null  datetime64[ns]
    dtypes: datetime64[ns](4), int64(2), object(2)
    memory usage: 1.8+ MB



```python
# Gets the LengthOFStayDays by subtracting ExitDate minus EntryDate

AirportParking['LengthOfStayDays'] = AirportParking['ExitDate']-AirportParking['EntryDate']

# Shows the last 5 records

AirportParking.tail()

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
      <th>EntryLane</th>
      <th>ExitLane</th>
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>EntryTime</th>
      <th>ExitDate</th>
      <th>ExitTime</th>
      <th>LengthOfStayDays</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29803</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:15:19</td>
      <td>2019-11-19</td>
      <td>2021-03-27 23:51:34</td>
      <td>18 days</td>
    </tr>
    <tr>
      <th>29804</th>
      <td>123</td>
      <td>827</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:54:15</td>
      <td>2019-11-19</td>
      <td>2021-03-27 19:59:19</td>
      <td>18 days</td>
    </tr>
    <tr>
      <th>29805</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:15:19</td>
      <td>2019-11-19</td>
      <td>2021-03-27 23:51:34</td>
      <td>18 days</td>
    </tr>
    <tr>
      <th>29806</th>
      <td>113</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 03:38:18</td>
      <td>2019-11-22</td>
      <td>2021-03-27 20:59:32</td>
      <td>21 days</td>
    </tr>
    <tr>
      <th>29807</th>
      <td>113</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 03:38:18</td>
      <td>2019-11-22</td>
      <td>2021-03-27 20:59:32</td>
      <td>21 days</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gets the LengthOFStayHoursMinutes by subtracting ExitTime minus EntryTime

AirportParking['LengthOfStayHoursMinutes'] = AirportParking['ExitTime']-AirportParking['EntryTime']

# Shows the last 5 records

AirportParking.tail()

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
      <th>EntryLane</th>
      <th>ExitLane</th>
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>EntryTime</th>
      <th>ExitDate</th>
      <th>ExitTime</th>
      <th>LengthOfStayDays</th>
      <th>LengthOfStayHoursMinutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29803</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:15:19</td>
      <td>2019-11-19</td>
      <td>2021-03-27 23:51:34</td>
      <td>18 days</td>
      <td>0 days 14:36:15</td>
    </tr>
    <tr>
      <th>29804</th>
      <td>123</td>
      <td>827</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:54:15</td>
      <td>2019-11-19</td>
      <td>2021-03-27 19:59:19</td>
      <td>18 days</td>
      <td>0 days 10:05:04</td>
    </tr>
    <tr>
      <th>29805</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:15:19</td>
      <td>2019-11-19</td>
      <td>2021-03-27 23:51:34</td>
      <td>18 days</td>
      <td>0 days 14:36:15</td>
    </tr>
    <tr>
      <th>29806</th>
      <td>113</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 03:38:18</td>
      <td>2019-11-22</td>
      <td>2021-03-27 20:59:32</td>
      <td>21 days</td>
      <td>0 days 17:21:14</td>
    </tr>
    <tr>
      <th>29807</th>
      <td>113</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 03:38:18</td>
      <td>2019-11-22</td>
      <td>2021-03-27 20:59:32</td>
      <td>21 days</td>
      <td>0 days 17:21:14</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Shows the first 10 records

AirportParking.head(10)

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
      <th>EntryLane</th>
      <th>ExitLane</th>
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>EntryTime</th>
      <th>ExitDate</th>
      <th>ExitTime</th>
      <th>LengthOfStayDays</th>
      <th>LengthOfStayHoursMinutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>809</td>
      <td>2</td>
      <td>Daily Parking</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:02:15</td>
      <td>2019-11-01</td>
      <td>2021-03-27 12:51:12</td>
      <td>0 days</td>
      <td>0 days 06:48:57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>152</td>
      <td>826</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:02:06</td>
      <td>2019-11-01</td>
      <td>2021-03-27 13:58:05</td>
      <td>0 days</td>
      <td>0 days 07:55:59</td>
    </tr>
    <tr>
      <th>2</th>
      <td>150</td>
      <td>824</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:01:28</td>
      <td>2019-11-01</td>
      <td>2021-03-27 13:52:03</td>
      <td>0 days</td>
      <td>0 days 07:50:35</td>
    </tr>
    <tr>
      <th>3</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:01:12</td>
      <td>2019-11-01</td>
      <td>2021-03-27 15:05:47</td>
      <td>0 days</td>
      <td>0 days 09:04:35</td>
    </tr>
    <tr>
      <th>4</th>
      <td>114</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:00:49</td>
      <td>2019-11-01</td>
      <td>2021-03-27 14:11:34</td>
      <td>0 days</td>
      <td>0 days 08:10:45</td>
    </tr>
    <tr>
      <th>5</th>
      <td>123</td>
      <td>831</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:58:56</td>
      <td>2019-11-01</td>
      <td>2021-03-27 15:07:17</td>
      <td>0 days</td>
      <td>0 days 09:08:21</td>
    </tr>
    <tr>
      <th>6</th>
      <td>116</td>
      <td>814</td>
      <td>3</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:58:35</td>
      <td>2019-11-01</td>
      <td>2021-03-27 14:36:31</td>
      <td>0 days</td>
      <td>0 days 08:37:56</td>
    </tr>
    <tr>
      <th>7</th>
      <td>103</td>
      <td>213</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:57:41</td>
      <td>2019-11-01</td>
      <td>2021-03-27 14:53:06</td>
      <td>0 days</td>
      <td>0 days 08:55:25</td>
    </tr>
    <tr>
      <th>8</th>
      <td>152</td>
      <td>823</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:57:04</td>
      <td>2019-11-01</td>
      <td>2021-03-27 15:40:37</td>
      <td>0 days</td>
      <td>0 days 09:43:33</td>
    </tr>
    <tr>
      <th>9</th>
      <td>103</td>
      <td>213</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:56:57</td>
      <td>2019-11-01</td>
      <td>2021-03-27 12:24:03</td>
      <td>0 days</td>
      <td>0 days 06:27:06</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Removes Duplicates

AirportParking = AirportParking.drop_duplicates(subset=None, inplace=False)

# Removes any EntryDate that is not '2019-11-01'

AirportParking = AirportParking[AirportParking.EntryDate == '2019-11-01']

```


```python
# Shows the number All the column names, along with how many Non-null values there are, and the data type 

AirportParking.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 12350 entries, 0 to 29806
    Data columns (total 10 columns):
     #   Column                    Non-Null Count  Dtype          
    ---  ------                    --------------  -----          
     0   EntryLane                 12350 non-null  int64          
     1   ExitLane                  12350 non-null  int64          
     2   Terminal                  12350 non-null  object         
     3   ParkingType               12350 non-null  object         
     4   EntryDate                 12350 non-null  datetime64[ns] 
     5   EntryTime                 12350 non-null  datetime64[ns] 
     6   ExitDate                  12350 non-null  datetime64[ns] 
     7   ExitTime                  12350 non-null  datetime64[ns] 
     8   LengthOfStayDays          12350 non-null  timedelta64[ns]
     9   LengthOfStayHoursMinutes  12350 non-null  timedelta64[ns]
    dtypes: datetime64[ns](4), int64(2), object(2), timedelta64[ns](2)
    memory usage: 1.0+ MB



```python
# Creates columns and adds a '1' if the criteria is met for each row

AirportParking.loc[AirportParking['LengthOfStayDays'] == '0 Days', 'OneDayOrLess'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '1 Days', 'OneDayOrLess'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '2 Days', 'TwoDaysToFiveDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '3 Days', 'TwoDaysToFiveDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '4 Days', 'TwoDaysToFiveDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '5 Days', 'TwoDaysToFiveDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '6 Days', 'SixDaysToTenDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '7 Days', 'SixDaysToTenDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '8 Days', 'SixDaysToTenDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '9 Days', 'SixDaysToTenDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '10 Days', 'SixDaysToTenDays'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '11 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '12 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '13 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '14 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '15 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '16 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '17 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '18 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '19 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '20 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '21 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '22 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '23 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '24 Days', 'ElevenDaysTo25Days'] = 1
AirportParking.loc[AirportParking['LengthOfStayDays'] == '25 Days', 'ElevenDaysTo25Days'] = 1

```


```python
# Shows the first 5 and last 5 records

AirportParking.head(8900)

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
      <th>EntryLane</th>
      <th>ExitLane</th>
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>EntryTime</th>
      <th>ExitDate</th>
      <th>ExitTime</th>
      <th>LengthOfStayDays</th>
      <th>LengthOfStayHoursMinutes</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>111</td>
      <td>809</td>
      <td>2</td>
      <td>Daily Parking</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:02:15</td>
      <td>2019-11-01</td>
      <td>2021-03-27 12:51:12</td>
      <td>0 days</td>
      <td>0 days 06:48:57</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>152</td>
      <td>826</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:02:06</td>
      <td>2019-11-01</td>
      <td>2021-03-27 13:58:05</td>
      <td>0 days</td>
      <td>0 days 07:55:59</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>150</td>
      <td>824</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:01:28</td>
      <td>2019-11-01</td>
      <td>2021-03-27 13:52:03</td>
      <td>0 days</td>
      <td>0 days 07:50:35</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>103</td>
      <td>214</td>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:01:12</td>
      <td>2019-11-01</td>
      <td>2021-03-27 15:05:47</td>
      <td>0 days</td>
      <td>0 days 09:04:35</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>114</td>
      <td>217</td>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>2021-03-27 06:00:49</td>
      <td>2019-11-01</td>
      <td>2021-03-27 14:11:34</td>
      <td>0 days</td>
      <td>0 days 08:10:45</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>18784</th>
      <td>128</td>
      <td>824</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:51:24</td>
      <td>2019-11-01</td>
      <td>2021-03-27 09:46:45</td>
      <td>0 days</td>
      <td>0 days 03:55:21</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18785</th>
      <td>123</td>
      <td>828</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:49:30</td>
      <td>2019-11-01</td>
      <td>2021-03-27 10:10:23</td>
      <td>0 days</td>
      <td>0 days 04:20:53</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18786</th>
      <td>152</td>
      <td>822</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:45:25</td>
      <td>2019-11-01</td>
      <td>2021-03-27 10:10:45</td>
      <td>0 days</td>
      <td>0 days 04:25:20</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18787</th>
      <td>123</td>
      <td>822</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:45:01</td>
      <td>2019-11-01</td>
      <td>2021-03-27 11:58:48</td>
      <td>0 days</td>
      <td>0 days 06:13:47</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18788</th>
      <td>131</td>
      <td>827</td>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>2021-03-27 05:43:10</td>
      <td>2019-11-01</td>
      <td>2021-03-27 08:23:09</td>
      <td>0 days</td>
      <td>0 days 02:39:59</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>8900 rows × 14 columns</p>
</div>




```python
# Shows the number All the column names, along with how many Non-null values there are, and the data type 

AirportParking.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 12350 entries, 0 to 29806
    Data columns (total 14 columns):
     #   Column                    Non-Null Count  Dtype          
    ---  ------                    --------------  -----          
     0   EntryLane                 12350 non-null  int64          
     1   ExitLane                  12350 non-null  int64          
     2   Terminal                  12350 non-null  object         
     3   ParkingType               12350 non-null  object         
     4   EntryDate                 12350 non-null  datetime64[ns] 
     5   EntryTime                 12350 non-null  datetime64[ns] 
     6   ExitDate                  12350 non-null  datetime64[ns] 
     7   ExitTime                  12350 non-null  datetime64[ns] 
     8   LengthOfStayDays          12350 non-null  timedelta64[ns]
     9   LengthOfStayHoursMinutes  12350 non-null  timedelta64[ns]
     10  OneDayOrLess              8658 non-null   float64        
     11  TwoDaysToFiveDays         3378 non-null   float64        
     12  SixDaysToTenDays          272 non-null    float64        
     13  ElevenDaysTo25Days        42 non-null     float64        
    dtypes: datetime64[ns](4), float64(4), int64(2), object(2), timedelta64[ns](2)
    memory usage: 1.4+ MB



```python
# Groups, Aggregates, and Sums certain fields

AirportParkingCnts = AirportParking[['Terminal', 'ParkingType', 'EntryDate', 'OneDayOrLess', 'TwoDaysToFiveDays', 'SixDaysToTenDays', 'ElevenDaysTo25Days']].groupby(['Terminal', 'ParkingType','EntryDate']).agg(['sum'])

```


```python
# Adds the Terminal numbes to each row

AirportParkingCnts = AirportParkingCnts.reset_index()

# Only shows the first header

AirportParkingCnts.columns = AirportParkingCnts.columns.get_level_values(0)

# Prints the data

AirportParkingCnts

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
      <th>Terminal</th>
      <th>ParkingType</th>
      <th>EntryDate</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>Daily Parking</td>
      <td>2019-11-01</td>
      <td>1390.0</td>
      <td>290.0</td>
      <td>26.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Economy Parking</td>
      <td>2019-11-01</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Level 1</td>
      <td>2019-11-01</td>
      <td>188.0</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>1120.0</td>
      <td>106.0</td>
      <td>8.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Parking Garage</td>
      <td>2019-11-01</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Other</td>
      <td>East Economy Garage A</td>
      <td>2019-11-01</td>
      <td>521.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Other</td>
      <td>East Economy Garage B</td>
      <td>2019-11-01</td>
      <td>636.0</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Other</td>
      <td>East Economy Surface Lot</td>
      <td>2019-11-01</td>
      <td>635.0</td>
      <td>1772.0</td>
      <td>162.0</td>
      <td>22.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Other</td>
      <td>West Park &amp; Walk Lot</td>
      <td>2019-11-01</td>
      <td>286.0</td>
      <td>146.0</td>
      <td>22.0</td>
      <td>4.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Groups, Aggregates, and Sums certain fields

AirportParkingCntsWOParkingType = AirportParkingCnts[['Terminal', 'ParkingType', 'EntryDate', 'OneDayOrLess', 'TwoDaysToFiveDays', 'SixDaysToTenDays', 'ElevenDaysTo25Days']].groupby(['Terminal', 'EntryDate']).agg(['sum'])

# Removes any records that are less than 100

#AirportParkingCntsWOParkingType = AirportParkingCntsWOParkingType.drop([0,4], axis=0)

AirportParkingCntsWOParkingType

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>sum</th>
      <th>sum</th>
      <th>sum</th>
      <th>sum</th>
      <th>sum</th>
    </tr>
    <tr>
      <th>Terminal</th>
      <th>EntryDate</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <th>2019-11-01</th>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>3</th>
      <th>2019-11-01</th>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <th>2019-11-01</th>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>Other</th>
      <th>2019-11-01</th>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Adds the Terminal numbes to each row

AirportParkingCntsWOParkingType = AirportParkingCntsWOParkingType.reset_index()

# Only shows the first header

AirportParkingCntsWOParkingType.columns = AirportParkingCntsWOParkingType.columns.get_level_values(0)


# Prints the data

AirportParkingCntsWOParkingType

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
      <th>Terminal</th>
      <th>EntryDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Other</td>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Import packages --- READING FROM WEBSITE

import pandas as pd  
import numpy as np
import urllib.request
import re

# defines url and the urllib package requests the url
# Steps to update the URL
# 1. Access the Phoenix OpenData website: 
# https://www.phoenixopendata.com/dataset/aviation-flight-information/resource/bf393c3c-3222-46ea-a91d-d83bc592f850
# 2. Click on the "URL" link right under the "AVIATION FLIGHT INFORMATION NOV 2019"
# 3. Copy the link and paste below in the "url = "

url = 'https://og-production-open-data-phoenixaz-892364687672.s3.amazonaws.com/resources/bf393c3c-3222-46ea-a91d-d83bc592f850/aviation-flight-information_aviation-flight-information-nov-2019_flightinfo.csv?Signature=B1XE9haNSBJY1xj5O2OIgyi9myQ%3D&Expires=1616881179&AWSAccessKeyId=AKIAJJIENTAPKHZMIPXQ'

req = urllib.request.Request(url)

# Opens and goes through the data on the web page and removes (splits) "," and "\r\n"

with urllib.request.urlopen(req) as response:
    AirportFlightData = response.read().decode("utf-8")
    AirportFlightData = re.split(',|\r\n',AirportFlightData)

# Displays the data

AirportFlightData

```




    ['Arrive Or Depart',
     'Schedule',
     'Airline',
     'Flight Number',
     'Gate',
     'Terminal',
     'D',
     '11/1/2019 8:00:00 AM',
     'Hawaiian Airlines',
     '35',
     'F10A',
     '3',
     'D',
     '11/1/2019 7:00:00 AM',
     'WestJet',
     '1499',
     'B23',
     '4',
     'D',
     '11/1/2019 6:01:00 AM',
     'Delta Air Lines',
     '970',
     'F1',
     '3',
     'D',
     '11/1/2019 12:35:00 AM',
     'Frontier Airlines',
     '1426',
     'F1',
     '3',
     'D',
     '11/1/2019 6:02:00 AM',
     'Frontier Airlines',
     '2822',
     'F2',
     '3',
     'D',
     '11/1/2019 12:53:00 AM',
     'Spirit Airlines',
     '972',
     'F10',
     '3',
     'D',
     '11/1/2019 6:15:00 AM',
     'Delta Air Lines',
     '1453',
     'F5',
     '3',
     'D',
     '11/1/2019 6:02:00 AM',
     'Delta Air Lines',
     '1994',
     'F3',
     '3',
     'D',
     '11/1/2019 6:00:00 AM',
     'Delta Air Lines',
     '848',
     'F8',
     '3',
     'D',
     '11/1/2019 7:40:00 AM',
     'Delta Air Lines',
     '5751',
     'F7',
     '3',
     'D',
     '11/1/2019 6:00:00 AM',
     'Delta Air Lines',
     '5809',
     'F9',
     '3',
     'D',
     '11/1/2019 7:00:00 AM',
     'Delta Air Lines',
     '909',
     'F6',
     '3',
     'D',
     '11/1/2019 9:16:00 AM',
     'Delta Air Lines',
     '2662',
     'F4',
     '3',
     'A',
     '11/1/2019 11:48:00 AM',
     'Volaris',
     '364',
     'B28',
     '4',
     'A',
     '11/1/2019 3:59:00 PM',
     'Volaris',
     '974',
     'B26',
     '4',
     'D',
     '11/1/2019 1:08:00 PM',
     'Volaris',
     '365',
     'B28',
     '4',
     'D',
     '11/1/2019 5:24:00 PM',
     'Volaris',
     '975',
     'B26',
     '4',
     'A',
     '11/1/2019 11:53:00 PM',
     'Spirit Airlines',
     '971',
     'F10',
     '3',
     'D',
     '11/1/2019 6:10:00 AM',
     'Southwest Airlines',
     '17',
     'D5',
     '4',
     'D',
     '11/1/2019 11:00:00 AM',
     'Southwest Airlines',
     '101',
     'D4',
     '4',
     'D',
     '11/1/2019 9:05:00 PM',
     'Southwest Airlines',
     '102',
     'D2',
     '4',
     'D',
     '11/1/2019 8:30:00 AM',
     'Southwest Airlines',
     '127',
     'C14',
     '4',
     'D',
     '11/1/2019 10:00:00 AM',
     'Southwest Airlines',
     '136',
     'C16',
     '4',
     'D',
     '11/1/2019 6:30:00 AM',
     'Southwest Airlines',
     '139',
     'D2',
     '4',
     'D',
     '11/1/2019 6:05:00 AM',
     'Southwest Airlines',
     '154',
     'C14',
     '4',
     'D',
     '11/1/2019 5:00:00 PM',
     'Southwest Airlines',
     '159',
     'C6',
     '4',
     'D',
     '11/1/2019 8:50:00 PM',
     'Southwest Airlines',
     '180',
     'C8',
     '4',
     'D',
     '11/1/2019 8:30:00 AM',
     'Southwest Airlines',
     '195',
     'C6',
     '4',
     'D',
     '11/1/2019 3:40:00 PM',
     'Southwest Airlines',
     '200',
     'C17',
     '4',
     'D',
     '11/1/2019 3:05:00 PM',
     'Southwest Airlines',
     '214',
     'C6',
     '4',
     'D',
     '11/1/2019 11:55:00 AM',
     'Southwest Airlines',
     '215',
     'C3',
     '4',
     'D',
     '11/1/2019 10:25:00 AM',
     'Southwest Airlines',
     '217',
     'D4',
     '4',
     'D',
     '11/1/2019 9:00:00 PM',
     'Southwest Airlines',
     '249',
     'D5',
     '4',
     'D',
     '11/1/2019 7:00:00 AM',
     'Southwest Airlines',
     '277',
     'D8',
     '4',
     'D',
     '11/1/2019 6:45:00 PM',
     'Southwest Airlines',
     '291',
     'C12',
     '4',
     'D',
     '11/1/2019 8:35:00 AM',
     'Southwest Airlines',
     '296',
     'C18',
     '4',
     'D',
     '11/1/2019 3:40:00 PM',
     'Southwest Airlines',
     '310',
     'C1',
     '4',
     'D',
     '11/1/2019 12:35:00 PM',
     'Southwest Airlines',
     '343',
     'D4',
     '4',
     'D',
     '11/1/2019 8:25:00 AM',
     'Southwest Airlines',
     '378',
     'C16',
     '4',
     'D',
     '11/1/2019 9:05:00 AM',
     'Southwest Airlines',
     '382',
     'D8',
     '4',
     'D',
     '11/1/2019 7:15:00 PM',
     'Southwest Airlines',
     '385',
     'C16',
     '4',
     'D',
     '11/1/2019 2:40:00 PM',
     'Southwest Airlines',
     '398',
     'C1',
     '4',
     'D',
     '11/1/2019 7:15:00 PM',
     'Southwest Airlines',
     '423',
     'C8',
     '4',
     'D',
     '11/1/2019 9:00:00 AM',
     'Southwest Airlines',
     '461',
     'D2',
     '4',
     'D',
     '11/1/2019 10:40:00 AM',
     'Southwest Airlines',
     '477',
     'D7',
     '4',
     'D',
     '11/1/2019 6:20:00 AM',
     'Southwest Airlines',
     '488',
     'D4',
     '4',
     'D',
     '11/1/2019 10:00:00 AM',
     'Southwest Airlines',
     '489',
     'C19',
     '4',
     'D',
     '11/1/2019 11:30:00 AM',
     'Southwest Airlines',
     '498',
     'C7',
     '4',
     'D',
     '11/1/2019 1:45:00 PM',
     'Southwest Airlines',
     '516',
     'C1',
     '4',
     'D',
     '11/1/2019 6:10:00 PM',
     'Southwest Airlines',
     '534',
     'D4',
     '4',
     'D',
     '11/1/2019 5:05:00 PM',
     'Southwest Airlines',
     '544',
     'C17',
     '4',
     'D',
     '11/1/2019 9:10:00 PM',
     'Southwest Airlines',
     '561',
     'C9',
     '4',
     'D',
     '11/1/2019 9:20:00 PM',
     'Southwest Airlines',
     '608',
     'C3',
     '4',
     'D',
     '11/1/2019 5:20:00 PM',
     'Southwest Airlines',
     '610',
     'C7',
     '4',
     'D',
     '11/1/2019 10:30:00 AM',
     'Southwest Airlines',
     '615',
     'C11',
     '4',
     'D',
     '11/1/2019 12:20:00 PM',
     'Southwest Airlines',
     '617',
     'C13',
     '4',
     'D',
     '11/1/2019 4:40:00 PM',
     'Southwest Airlines',
     '627',
     'C6',
     '4',
     'D',
     '11/1/2019 8:20:00 PM',
     'Southwest Airlines',
     '633',
     'C16',
     '4',
     'D',
     '11/1/2019 11:45:00 AM',
     'Southwest Airlines',
     '644',
     'D2',
     '4',
     'D',
     '11/1/2019 1:40:00 PM',
     'Southwest Airlines',
     '651',
     'C7',
     '4',
     'D',
     '11/1/2019 5:50:00 PM',
     'Southwest Airlines',
     '652',
     'C1',
     '4',
     'D',
     '11/1/2019 6:15:00 PM',
     'Southwest Airlines',
     '664',
     'C16',
     '4',
     'D',
     '11/1/2019 8:35:00 AM',
     'Southwest Airlines',
     '686',
     'D6',
     '4',
     'D',
     '11/1/2019 12:25:00 PM',
     'Southwest Airlines',
     '690',
     'C9',
     '4',
     'D',
     '11/1/2019 10:50:00 AM',
     'Southwest Airlines',
     '711',
     'C9',
     '4',
     'D',
     '11/1/2019 7:30:00 AM',
     'Southwest Airlines',
     '744',
     'C1',
     '4',
     'D',
     '11/1/2019 11:50:00 AM',
     'Southwest Airlines',
     '766',
     'C8',
     '4',
     'D',
     '11/1/2019 7:10:00 AM',
     'Southwest Airlines',
     '791',
     'D3',
     '4',
     'D',
     '11/1/2019 9:55:00 PM',
     'Southwest Airlines',
     '799',
     'C4',
     '4',
     'D',
     '11/1/2019 12:50:00 PM',
     'Southwest Airlines',
     '820',
     'C3',
     '4',
     'D',
     '11/1/2019 9:55:00 AM',
     'Southwest Airlines',
     '823',
     'C12',
     '4',
     'D',
     '11/1/2019 9:05:00 AM',
     'Southwest Airlines',
     '830',
     'D4',
     '4',
     'D',
     '11/1/2019 8:25:00 AM',
     'Southwest Airlines',
     '858',
     'C1',
     '4',
     'D',
     '11/1/2019 1:20:00 PM',
     'Southwest Airlines',
     '878',
     'C17',
     '4',
     'D',
     '11/1/2019 7:05:00 AM',
     'Southwest Airlines',
     '882',
     'C17',
     '4',
     'D',
     '11/1/2019 11:35:00 AM',
     'Southwest Airlines',
     '904',
     'C19',
     '4',
     'D',
     '11/1/2019 5:15:00 PM',
     'Southwest Airlines',
     '961',
     'C12',
     '4',
     'D',
     '11/1/2019 12:15:00 PM',
     'Southwest Airlines',
     '965',
     'C1',
     '4',
     'D',
     '11/1/2019 4:00:00 PM',
     'Southwest Airlines',
     '971',
     'C16',
     '4',
     'D',
     '11/1/2019 6:05:00 AM',
     'Southwest Airlines',
     '990',
     'C9',
     '4',
     'D',
     '11/1/2019 2:15:00 PM',
     'Southwest Airlines',
     '991',
     'D7',
     '4',
     'D',
     '11/1/2019 8:50:00 PM',
     'Southwest Airlines',
     '997',
     'D6',
     '4',
     'D',
     '11/1/2019 1:25:00 PM',
     'Southwest Airlines',
     '1017',
     'C8',
     '4',
     'D',
     '11/1/2019 10:40:00 AM',
     'Southwest Airlines',
     '1020',
     'D6',
     '4',
     'D',
     '11/1/2019 1:30:00 PM',
     'Southwest Airlines',
     '1030',
     'C6',
     '4',
     'D',
     '11/1/2019 10:45:00 AM',
     'Southwest Airlines',
     '1034',
     'C8',
     '4',
     'D',
     '11/1/2019 8:25:00 AM',
     'Southwest Airlines',
     '1199',
     'C9',
     '4',
     'D',
     '11/1/2019 7:10:00 PM',
     'Southwest Airlines',
     '1209',
     'C7',
     '4',
     'D',
     '11/1/2019 3:00:00 PM',
     'Southwest Airlines',
     '1219',
     'C14',
     '4',
     'D',
     '11/1/2019 6:00:00 AM',
     'Southwest Airlines',
     '1220',
     'C19',
     '4',
     'D',
     '11/1/2019 10:40:00 AM',
     'Southwest Airlines',
     '1279',
     'D2',
     '4',
     'D',
     '11/1/2019 8:10:00 AM',
     'Southwest Airlines',
     '1293',
     'C7',
     '4',
     'D',
     '11/1/2019 12:55:00 PM',
     'Southwest Airlines',
     '1339',
     'C19',
     '4',
     'D',
     '11/1/2019 8:30:00 AM',
     'Southwest Airlines',
     '1343',
     'D3',
     '4',
     'D',
     '11/1/2019 3:25:00 PM',
     'Southwest Airlines',
     '1354',
     'D2',
     '4',
     'D',
     '11/1/2019 6:45:00 AM',
     'Southwest Airlines',
     '1358',
     'D6',
     '4',
     'D',
     '11/1/2019 10:50:00 AM',
     'Southwest Airlines',
     '1372',
     'C3',
     '4',
     'D',
     '11/1/2019 11:50:00 AM',
     'Southwest Airlines',
     '1397',
     'D7',
     '4',
     'D',
     '11/1/2019 6:55:00 PM',
     'Southwest Airlines',
     '1404',
     'C18',
     '4',
     'D',
     '11/1/2019 10:50:00 AM',
     'Southwest Airlines',
     '1406',
     'C12',
     '4',
     'D',
     '11/1/2019 3:45:00 PM',
     'Southwest Airlines',
     '1415',
     'C7',
     '4',
     'D',
     '11/1/2019 10:45:00 AM',
     'Southwest Airlines',
     '1431',
     'D8',
     '4',
     'D',
     '11/1/2019 2:25:00 PM',
     'Southwest Airlines',
     '1436',
     'D4',
     '4',
     'D',
     '11/1/2019 8:05:00 AM',
     'Southwest Airlines',
     '1484',
     'C14',
     '4',
     'D',
     '11/1/2019 1:45:00 PM',
     'Southwest Airlines',
     '1541',
     'C18',
     '4',
     'D',
     '11/1/2019 2:10:00 PM',
     'Southwest Airlines',
     '1557',
     'C12',
     '4',
     'D',
     '11/1/2019 9:15:00 PM',
     'Southwest Airlines',
     '1576',
     'D4',
     '4',
     'D',
     '11/1/2019 2:05:00 PM',
     'Southwest Airlines',
     '1582',
     'D5',
     '4',
     'D',
     '11/1/2019 9:30:00 AM',
     'Southwest Airlines',
     '1585',
     'C17',
     '4',
     'D',
     '11/1/2019 7:00:00 PM',
     'Southwest Airlines',
     '1602',
     'D5',
     '4',
     'D',
     '11/1/2019 7:15:00 PM',
     'Southwest Airlines',
     '1655',
     'C1',
     '4',
     'D',
     '11/1/2019 5:55:00 PM',
     'Southwest Airlines',
     '1656',
     'C8',
     '4',
     'D',
     '11/1/2019 4:40:00 PM',
     'Southwest Airlines',
     '1662',
     'D7',
     '4',
     'D',
     '11/1/2019 8:50:00 AM',
     'Southwest Airlines',
     '1672',
     'C8',
     '4',
     'D',
     '11/1/2019 3:45:00 PM',
     'Southwest Airlines',
     '1673',
     'D5',
     '4',
     'D',
     '11/1/2019 9:25:00 PM',
     'Southwest Airlines',
     '1704',
     'C16',
     '4',
     'D',
     '11/1/2019 4:15:00 PM',
     'Southwest Airlines',
     '1713',
     'C3',
     '4',
     'D',
     '11/1/2019 12:20:00 PM',
     'Southwest Airlines',
     '1722',
     'D8',
     '4',
     'D',
     '11/1/2019 7:25:00 PM',
     'Southwest Airlines',
     '1757',
     'D4',
     '4',
     'D',
     '11/1/2019 9:10:00 AM',
     'Southwest Airlines',
     '1833',
     'C14',
     '4',
     'D',
     '11/1/2019 10:40:00 AM',
     'Southwest Airlines',
     '1838',
     'D3',
     '4',
     'D',
     '11/1/2019 8:35:00 AM',
     'Southwest Airlines',
     '1855',
     'C12',
     '4',
     'D',
     '11/1/2019 12:40:00 PM',
     'Southwest Airlines',
     '1875',
     'C16',
     '4',
     'D',
     '11/1/2019 7:00:00 PM',
     'Southwest Airlines',
     '1876',
     'D6',
     '4',
     'D',
     '11/1/2019 7:05:00 PM',
     'Southwest Airlines',
     '1899',
     'D8',
     '4',
     'D',
     '11/1/2019 6:10:00 AM',
     'Southwest Airlines',
     '1900',
     'C7',
     '4',
     'D',
     '11/1/2019 7:00:00 AM',
     'Southwest Airlines',
     '1905',
     'C18',
     '4',
     'D',
     '11/1/2019 8:05:00 PM',
     'Southwest Airlines',
     '1912',
     'C3',
     '4',
     'D',
     '11/1/2019 8:55:00 PM',
     'Southwest Airlines',
     '1931',
     'C1',
     '4',
     'D',
     '11/1/2019 5:35:00 AM',
     'Southwest Airlines',
     '1944',
     'C18',
     '4',
     'D',
     '11/1/2019 12:10:00 PM',
     'Southwest Airlines',
     '1949',
     'C17',
     '4',
     'D',
     '11/1/2019 6:45:00 AM',
     'Southwest Airlines',
     '1952',
     'C11',
     '4',
     'D',
     '11/1/2019 11:20:00 AM',
     'Southwest Airlines',
     '1992',
     'C18',
     '4',
     'D',
     '11/1/2019 4:35:00 PM',
     'Southwest Airlines',
     '2014',
     'D4',
     '4',
     'D',
     '11/1/2019 10:05:00 PM',
     'Southwest Airlines',
     '2082',
     'D8',
     '4',
     'D',
     '11/1/2019 4:10:00 PM',
     'Southwest Airlines',
     '2103',
     'D6',
     '4',
     'D',
     '11/1/2019 9:25:00 PM',
     'Southwest Airlines',
     '2120',
     'C14',
     '4',
     'D',
     '11/1/2019 4:25:00 PM',
     'Southwest Airlines',
     '2136',
     'C9',
     '4',
     'D',
     '11/1/2019 3:55:00 PM',
     'Southwest Airlines',
     '2178',
     'D8',
     '4',
     'D',
     '11/1/2019 1:20:00 PM',
     'Southwest Airlines',
     '2191',
     'D2',
     '4',
     'D',
     '11/1/2019 10:45:00 AM',
     'Southwest Airlines',
     '2209',
     'C14',
     '4',
     'D',
     '11/1/2019 10:15:00 AM',
     'Southwest Airlines',
     '2222',
     'C7',
     '4',
     'D',
     '11/1/2019 3:45:00 PM',
     'Southwest Airlines',
     '2225',
     'C18',
     '4',
     'D',
     '11/1/2019 7:10:00 AM',
     'Southwest Airlines',
     '2271',
     'D6',
     '4',
     'D',
     '11/1/2019 9:05:00 PM',
     'Southwest Airlines',
     '2272',
     'D7',
     '4',
     'D',
     '11/1/2019 8:35:00 AM',
     'Southwest Airlines',
     '2279',
     'D7',
     '4',
     'D',
     '11/1/2019 9:10:00 PM',
     'Southwest Airlines',
     '2283',
     'D8',
     '4',
     'D',
     '11/1/2019 6:35:00 PM',
     'Southwest Airlines',
     '2286',
     'C6',
     '4',
     'D',
     '11/1/2019 8:35:00 AM',
     'Southwest Airlines',
     '2298',
     'C7',
     '4',
     'D',
     '11/1/2019 8:50:00 AM',
     'Southwest Airlines',
     '2303',
     'D5',
     '4',
     'D',
     '11/1/2019 6:00:00 AM',
     'Southwest Airlines',
     '2304',
     'D7',
     '4',
     'D',
     '11/1/2019 8:35:00 PM',
     'Southwest Airlines',
     '2306',
     'C6',
     '4',
     'D',
     '11/1/2019 12:15:00 PM',
     'Southwest Airlines',
     '2323',
     'D5',
     '4',
     'D',
     '11/1/2019 7:20:00 PM',
     'Southwest Airlines',
     '2366',
     'D2',
     '4',
     'D',
     '11/1/2019 2:55:00 PM',
     'Southwest Airlines',
     '2373',
     'C9',
     '4',
     'D',
     '11/1/2019 2:10:00 PM',
     'Southwest Airlines',
     '2385',
     'D8',
     '4',
     'D',
     '11/1/2019 10:45:00 AM',
     'Southwest Airlines',
     '2407',
     'C1',
     '4',
     'D',
     '11/1/2019 8:45:00 PM',
     'Southwest Airlines',
     '2415',
     'C19',
     '4',
     'D',
     '11/1/2019 9:40:00 AM',
     'Southwest Airlines',
     '2441',
     'D5',
     '4',
     'D',
     '11/1/2019 6:10:00 AM',
     'Southwest Airlines',
     '2469',
     'C8',
     '4',
     'D',
     '11/1/2019 2:25:00 PM',
     'Southwest Airlines',
     '2475',
     'D6',
     '4',
     'D',
     '11/1/2019 10:45:00 AM',
     'Southwest Airlines',
     '2476',
     'C6',
     '4',
     'D',
     '11/1/2019 8:25:00 PM',
     'Southwest Airlines',
     '2490',
     'C12',
     '4',
     'D',
     '11/1/2019 7:50:00 PM',
     'Southwest Airlines',
     '2571',
     'D7',
     '4',
     'D',
     '11/1/2019 7:05:00 PM',
     'Southwest Airlines',
     '2600',
     'C14',
     '4',
     'D',
     '11/1/2019 4:30:00 PM',
     'Southwest Airlines',
     '2664',
     ...]




```python
# Defines divide_chunks

def divide_chunks(l, n): 
      
    # loops through the data until the end of the list
    
    for i in range(0, len(l), n):  
        yield l[i:i + n] 
  
 # How many elements each list should have.  Breaks the list into sections of 6 fields
    
n = 6
  
# divides the chunks of data up while it passes in the AirportFlightData list and 
# the number of fields to be chunked together
    
AirportFlightData = list(divide_chunks(AirportFlightData, n)) 

# Displays the data

AirportFlightData

```




    [['Arrive Or Depart',
      'Schedule',
      'Airline',
      'Flight Number',
      'Gate',
      'Terminal'],
     ['D', '11/1/2019 8:00:00 AM', 'Hawaiian Airlines', '35', 'F10A', '3'],
     ['D', '11/1/2019 7:00:00 AM', 'WestJet', '1499', 'B23', '4'],
     ['D', '11/1/2019 6:01:00 AM', 'Delta Air Lines', '970', 'F1', '3'],
     ['D', '11/1/2019 12:35:00 AM', 'Frontier Airlines', '1426', 'F1', '3'],
     ['D', '11/1/2019 6:02:00 AM', 'Frontier Airlines', '2822', 'F2', '3'],
     ['D', '11/1/2019 12:53:00 AM', 'Spirit Airlines', '972', 'F10', '3'],
     ['D', '11/1/2019 6:15:00 AM', 'Delta Air Lines', '1453', 'F5', '3'],
     ['D', '11/1/2019 6:02:00 AM', 'Delta Air Lines', '1994', 'F3', '3'],
     ['D', '11/1/2019 6:00:00 AM', 'Delta Air Lines', '848', 'F8', '3'],
     ['D', '11/1/2019 7:40:00 AM', 'Delta Air Lines', '5751', 'F7', '3'],
     ['D', '11/1/2019 6:00:00 AM', 'Delta Air Lines', '5809', 'F9', '3'],
     ['D', '11/1/2019 7:00:00 AM', 'Delta Air Lines', '909', 'F6', '3'],
     ['D', '11/1/2019 9:16:00 AM', 'Delta Air Lines', '2662', 'F4', '3'],
     ['A', '11/1/2019 11:48:00 AM', 'Volaris', '364', 'B28', '4'],
     ['A', '11/1/2019 3:59:00 PM', 'Volaris', '974', 'B26', '4'],
     ['D', '11/1/2019 1:08:00 PM', 'Volaris', '365', 'B28', '4'],
     ['D', '11/1/2019 5:24:00 PM', 'Volaris', '975', 'B26', '4'],
     ['A', '11/1/2019 11:53:00 PM', 'Spirit Airlines', '971', 'F10', '3'],
     ['D', '11/1/2019 6:10:00 AM', 'Southwest Airlines', '17', 'D5', '4'],
     ['D', '11/1/2019 11:00:00 AM', 'Southwest Airlines', '101', 'D4', '4'],
     ['D', '11/1/2019 9:05:00 PM', 'Southwest Airlines', '102', 'D2', '4'],
     ['D', '11/1/2019 8:30:00 AM', 'Southwest Airlines', '127', 'C14', '4'],
     ['D', '11/1/2019 10:00:00 AM', 'Southwest Airlines', '136', 'C16', '4'],
     ['D', '11/1/2019 6:30:00 AM', 'Southwest Airlines', '139', 'D2', '4'],
     ['D', '11/1/2019 6:05:00 AM', 'Southwest Airlines', '154', 'C14', '4'],
     ['D', '11/1/2019 5:00:00 PM', 'Southwest Airlines', '159', 'C6', '4'],
     ['D', '11/1/2019 8:50:00 PM', 'Southwest Airlines', '180', 'C8', '4'],
     ['D', '11/1/2019 8:30:00 AM', 'Southwest Airlines', '195', 'C6', '4'],
     ['D', '11/1/2019 3:40:00 PM', 'Southwest Airlines', '200', 'C17', '4'],
     ['D', '11/1/2019 3:05:00 PM', 'Southwest Airlines', '214', 'C6', '4'],
     ['D', '11/1/2019 11:55:00 AM', 'Southwest Airlines', '215', 'C3', '4'],
     ['D', '11/1/2019 10:25:00 AM', 'Southwest Airlines', '217', 'D4', '4'],
     ['D', '11/1/2019 9:00:00 PM', 'Southwest Airlines', '249', 'D5', '4'],
     ['D', '11/1/2019 7:00:00 AM', 'Southwest Airlines', '277', 'D8', '4'],
     ['D', '11/1/2019 6:45:00 PM', 'Southwest Airlines', '291', 'C12', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '296', 'C18', '4'],
     ['D', '11/1/2019 3:40:00 PM', 'Southwest Airlines', '310', 'C1', '4'],
     ['D', '11/1/2019 12:35:00 PM', 'Southwest Airlines', '343', 'D4', '4'],
     ['D', '11/1/2019 8:25:00 AM', 'Southwest Airlines', '378', 'C16', '4'],
     ['D', '11/1/2019 9:05:00 AM', 'Southwest Airlines', '382', 'D8', '4'],
     ['D', '11/1/2019 7:15:00 PM', 'Southwest Airlines', '385', 'C16', '4'],
     ['D', '11/1/2019 2:40:00 PM', 'Southwest Airlines', '398', 'C1', '4'],
     ['D', '11/1/2019 7:15:00 PM', 'Southwest Airlines', '423', 'C8', '4'],
     ['D', '11/1/2019 9:00:00 AM', 'Southwest Airlines', '461', 'D2', '4'],
     ['D', '11/1/2019 10:40:00 AM', 'Southwest Airlines', '477', 'D7', '4'],
     ['D', '11/1/2019 6:20:00 AM', 'Southwest Airlines', '488', 'D4', '4'],
     ['D', '11/1/2019 10:00:00 AM', 'Southwest Airlines', '489', 'C19', '4'],
     ['D', '11/1/2019 11:30:00 AM', 'Southwest Airlines', '498', 'C7', '4'],
     ['D', '11/1/2019 1:45:00 PM', 'Southwest Airlines', '516', 'C1', '4'],
     ['D', '11/1/2019 6:10:00 PM', 'Southwest Airlines', '534', 'D4', '4'],
     ['D', '11/1/2019 5:05:00 PM', 'Southwest Airlines', '544', 'C17', '4'],
     ['D', '11/1/2019 9:10:00 PM', 'Southwest Airlines', '561', 'C9', '4'],
     ['D', '11/1/2019 9:20:00 PM', 'Southwest Airlines', '608', 'C3', '4'],
     ['D', '11/1/2019 5:20:00 PM', 'Southwest Airlines', '610', 'C7', '4'],
     ['D', '11/1/2019 10:30:00 AM', 'Southwest Airlines', '615', 'C11', '4'],
     ['D', '11/1/2019 12:20:00 PM', 'Southwest Airlines', '617', 'C13', '4'],
     ['D', '11/1/2019 4:40:00 PM', 'Southwest Airlines', '627', 'C6', '4'],
     ['D', '11/1/2019 8:20:00 PM', 'Southwest Airlines', '633', 'C16', '4'],
     ['D', '11/1/2019 11:45:00 AM', 'Southwest Airlines', '644', 'D2', '4'],
     ['D', '11/1/2019 1:40:00 PM', 'Southwest Airlines', '651', 'C7', '4'],
     ['D', '11/1/2019 5:50:00 PM', 'Southwest Airlines', '652', 'C1', '4'],
     ['D', '11/1/2019 6:15:00 PM', 'Southwest Airlines', '664', 'C16', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '686', 'D6', '4'],
     ['D', '11/1/2019 12:25:00 PM', 'Southwest Airlines', '690', 'C9', '4'],
     ['D', '11/1/2019 10:50:00 AM', 'Southwest Airlines', '711', 'C9', '4'],
     ['D', '11/1/2019 7:30:00 AM', 'Southwest Airlines', '744', 'C1', '4'],
     ['D', '11/1/2019 11:50:00 AM', 'Southwest Airlines', '766', 'C8', '4'],
     ['D', '11/1/2019 7:10:00 AM', 'Southwest Airlines', '791', 'D3', '4'],
     ['D', '11/1/2019 9:55:00 PM', 'Southwest Airlines', '799', 'C4', '4'],
     ['D', '11/1/2019 12:50:00 PM', 'Southwest Airlines', '820', 'C3', '4'],
     ['D', '11/1/2019 9:55:00 AM', 'Southwest Airlines', '823', 'C12', '4'],
     ['D', '11/1/2019 9:05:00 AM', 'Southwest Airlines', '830', 'D4', '4'],
     ['D', '11/1/2019 8:25:00 AM', 'Southwest Airlines', '858', 'C1', '4'],
     ['D', '11/1/2019 1:20:00 PM', 'Southwest Airlines', '878', 'C17', '4'],
     ['D', '11/1/2019 7:05:00 AM', 'Southwest Airlines', '882', 'C17', '4'],
     ['D', '11/1/2019 11:35:00 AM', 'Southwest Airlines', '904', 'C19', '4'],
     ['D', '11/1/2019 5:15:00 PM', 'Southwest Airlines', '961', 'C12', '4'],
     ['D', '11/1/2019 12:15:00 PM', 'Southwest Airlines', '965', 'C1', '4'],
     ['D', '11/1/2019 4:00:00 PM', 'Southwest Airlines', '971', 'C16', '4'],
     ['D', '11/1/2019 6:05:00 AM', 'Southwest Airlines', '990', 'C9', '4'],
     ['D', '11/1/2019 2:15:00 PM', 'Southwest Airlines', '991', 'D7', '4'],
     ['D', '11/1/2019 8:50:00 PM', 'Southwest Airlines', '997', 'D6', '4'],
     ['D', '11/1/2019 1:25:00 PM', 'Southwest Airlines', '1017', 'C8', '4'],
     ['D', '11/1/2019 10:40:00 AM', 'Southwest Airlines', '1020', 'D6', '4'],
     ['D', '11/1/2019 1:30:00 PM', 'Southwest Airlines', '1030', 'C6', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '1034', 'C8', '4'],
     ['D', '11/1/2019 8:25:00 AM', 'Southwest Airlines', '1199', 'C9', '4'],
     ['D', '11/1/2019 7:10:00 PM', 'Southwest Airlines', '1209', 'C7', '4'],
     ['D', '11/1/2019 3:00:00 PM', 'Southwest Airlines', '1219', 'C14', '4'],
     ['D', '11/1/2019 6:00:00 AM', 'Southwest Airlines', '1220', 'C19', '4'],
     ['D', '11/1/2019 10:40:00 AM', 'Southwest Airlines', '1279', 'D2', '4'],
     ['D', '11/1/2019 8:10:00 AM', 'Southwest Airlines', '1293', 'C7', '4'],
     ['D', '11/1/2019 12:55:00 PM', 'Southwest Airlines', '1339', 'C19', '4'],
     ['D', '11/1/2019 8:30:00 AM', 'Southwest Airlines', '1343', 'D3', '4'],
     ['D', '11/1/2019 3:25:00 PM', 'Southwest Airlines', '1354', 'D2', '4'],
     ['D', '11/1/2019 6:45:00 AM', 'Southwest Airlines', '1358', 'D6', '4'],
     ['D', '11/1/2019 10:50:00 AM', 'Southwest Airlines', '1372', 'C3', '4'],
     ['D', '11/1/2019 11:50:00 AM', 'Southwest Airlines', '1397', 'D7', '4'],
     ['D', '11/1/2019 6:55:00 PM', 'Southwest Airlines', '1404', 'C18', '4'],
     ['D', '11/1/2019 10:50:00 AM', 'Southwest Airlines', '1406', 'C12', '4'],
     ['D', '11/1/2019 3:45:00 PM', 'Southwest Airlines', '1415', 'C7', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '1431', 'D8', '4'],
     ['D', '11/1/2019 2:25:00 PM', 'Southwest Airlines', '1436', 'D4', '4'],
     ['D', '11/1/2019 8:05:00 AM', 'Southwest Airlines', '1484', 'C14', '4'],
     ['D', '11/1/2019 1:45:00 PM', 'Southwest Airlines', '1541', 'C18', '4'],
     ['D', '11/1/2019 2:10:00 PM', 'Southwest Airlines', '1557', 'C12', '4'],
     ['D', '11/1/2019 9:15:00 PM', 'Southwest Airlines', '1576', 'D4', '4'],
     ['D', '11/1/2019 2:05:00 PM', 'Southwest Airlines', '1582', 'D5', '4'],
     ['D', '11/1/2019 9:30:00 AM', 'Southwest Airlines', '1585', 'C17', '4'],
     ['D', '11/1/2019 7:00:00 PM', 'Southwest Airlines', '1602', 'D5', '4'],
     ['D', '11/1/2019 7:15:00 PM', 'Southwest Airlines', '1655', 'C1', '4'],
     ['D', '11/1/2019 5:55:00 PM', 'Southwest Airlines', '1656', 'C8', '4'],
     ['D', '11/1/2019 4:40:00 PM', 'Southwest Airlines', '1662', 'D7', '4'],
     ['D', '11/1/2019 8:50:00 AM', 'Southwest Airlines', '1672', 'C8', '4'],
     ['D', '11/1/2019 3:45:00 PM', 'Southwest Airlines', '1673', 'D5', '4'],
     ['D', '11/1/2019 9:25:00 PM', 'Southwest Airlines', '1704', 'C16', '4'],
     ['D', '11/1/2019 4:15:00 PM', 'Southwest Airlines', '1713', 'C3', '4'],
     ['D', '11/1/2019 12:20:00 PM', 'Southwest Airlines', '1722', 'D8', '4'],
     ['D', '11/1/2019 7:25:00 PM', 'Southwest Airlines', '1757', 'D4', '4'],
     ['D', '11/1/2019 9:10:00 AM', 'Southwest Airlines', '1833', 'C14', '4'],
     ['D', '11/1/2019 10:40:00 AM', 'Southwest Airlines', '1838', 'D3', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '1855', 'C12', '4'],
     ['D', '11/1/2019 12:40:00 PM', 'Southwest Airlines', '1875', 'C16', '4'],
     ['D', '11/1/2019 7:00:00 PM', 'Southwest Airlines', '1876', 'D6', '4'],
     ['D', '11/1/2019 7:05:00 PM', 'Southwest Airlines', '1899', 'D8', '4'],
     ['D', '11/1/2019 6:10:00 AM', 'Southwest Airlines', '1900', 'C7', '4'],
     ['D', '11/1/2019 7:00:00 AM', 'Southwest Airlines', '1905', 'C18', '4'],
     ['D', '11/1/2019 8:05:00 PM', 'Southwest Airlines', '1912', 'C3', '4'],
     ['D', '11/1/2019 8:55:00 PM', 'Southwest Airlines', '1931', 'C1', '4'],
     ['D', '11/1/2019 5:35:00 AM', 'Southwest Airlines', '1944', 'C18', '4'],
     ['D', '11/1/2019 12:10:00 PM', 'Southwest Airlines', '1949', 'C17', '4'],
     ['D', '11/1/2019 6:45:00 AM', 'Southwest Airlines', '1952', 'C11', '4'],
     ['D', '11/1/2019 11:20:00 AM', 'Southwest Airlines', '1992', 'C18', '4'],
     ['D', '11/1/2019 4:35:00 PM', 'Southwest Airlines', '2014', 'D4', '4'],
     ['D', '11/1/2019 10:05:00 PM', 'Southwest Airlines', '2082', 'D8', '4'],
     ['D', '11/1/2019 4:10:00 PM', 'Southwest Airlines', '2103', 'D6', '4'],
     ['D', '11/1/2019 9:25:00 PM', 'Southwest Airlines', '2120', 'C14', '4'],
     ['D', '11/1/2019 4:25:00 PM', 'Southwest Airlines', '2136', 'C9', '4'],
     ['D', '11/1/2019 3:55:00 PM', 'Southwest Airlines', '2178', 'D8', '4'],
     ['D', '11/1/2019 1:20:00 PM', 'Southwest Airlines', '2191', 'D2', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '2209', 'C14', '4'],
     ['D', '11/1/2019 10:15:00 AM', 'Southwest Airlines', '2222', 'C7', '4'],
     ['D', '11/1/2019 3:45:00 PM', 'Southwest Airlines', '2225', 'C18', '4'],
     ['D', '11/1/2019 7:10:00 AM', 'Southwest Airlines', '2271', 'D6', '4'],
     ['D', '11/1/2019 9:05:00 PM', 'Southwest Airlines', '2272', 'D7', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '2279', 'D7', '4'],
     ['D', '11/1/2019 9:10:00 PM', 'Southwest Airlines', '2283', 'D8', '4'],
     ['D', '11/1/2019 6:35:00 PM', 'Southwest Airlines', '2286', 'C6', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '2298', 'C7', '4'],
     ['D', '11/1/2019 8:50:00 AM', 'Southwest Airlines', '2303', 'D5', '4'],
     ['D', '11/1/2019 6:00:00 AM', 'Southwest Airlines', '2304', 'D7', '4'],
     ['D', '11/1/2019 8:35:00 PM', 'Southwest Airlines', '2306', 'C6', '4'],
     ['D', '11/1/2019 12:15:00 PM', 'Southwest Airlines', '2323', 'D5', '4'],
     ['D', '11/1/2019 7:20:00 PM', 'Southwest Airlines', '2366', 'D2', '4'],
     ['D', '11/1/2019 2:55:00 PM', 'Southwest Airlines', '2373', 'C9', '4'],
     ['D', '11/1/2019 2:10:00 PM', 'Southwest Airlines', '2385', 'D8', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '2407', 'C1', '4'],
     ['D', '11/1/2019 8:45:00 PM', 'Southwest Airlines', '2415', 'C19', '4'],
     ['D', '11/1/2019 9:40:00 AM', 'Southwest Airlines', '2441', 'D5', '4'],
     ['D', '11/1/2019 6:10:00 AM', 'Southwest Airlines', '2469', 'C8', '4'],
     ['D', '11/1/2019 2:25:00 PM', 'Southwest Airlines', '2475', 'D6', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '2476', 'C6', '4'],
     ['D', '11/1/2019 8:25:00 PM', 'Southwest Airlines', '2490', 'C12', '4'],
     ['D', '11/1/2019 7:50:00 PM', 'Southwest Airlines', '2571', 'D7', '4'],
     ['D', '11/1/2019 7:05:00 PM', 'Southwest Airlines', '2600', 'C14', '4'],
     ['D', '11/1/2019 4:30:00 PM', 'Southwest Airlines', '2664', 'C19', '4'],
     ['D', '11/1/2019 6:05:00 PM', 'Southwest Airlines', '2715', 'C3', '4'],
     ['D', '11/1/2019 4:15:00 PM', 'Southwest Airlines', '2717', 'C8', '4'],
     ['D', '11/1/2019 8:55:00 AM', 'Southwest Airlines', '2790', 'C4', '4'],
     ['D', '11/1/2019 2:35:00 PM', 'Southwest Airlines', '2809', 'C16', '4'],
     ['D', '11/1/2019 1:05:00 PM', 'Southwest Airlines', '2929', 'D6', '4'],
     ['D', '11/1/2019 7:20:00 AM', 'Southwest Airlines', '2945', 'C19', '4'],
     ['D', '11/1/2019 7:55:00 AM', 'Southwest Airlines', '2988', 'D4', '4'],
     ['D', '11/1/2019 6:55:00 PM', 'Southwest Airlines', '3125', 'C17', '4'],
     ['D', '11/1/2019 5:35:00 PM', 'Southwest Airlines', '3129', 'D2', '4'],
     ['D', '11/1/2019 7:35:00 PM', 'Southwest Airlines', '3163', 'C6', '4'],
     ['D', '11/1/2019 6:55:00 PM', 'Southwest Airlines', '3196', 'D7', '4'],
     ['D', '11/1/2019 6:45:00 PM', 'Southwest Airlines', '3218', 'C19', '4'],
     ['D', '11/1/2019 7:40:00 PM', 'Southwest Airlines', '3228', 'C19', '4'],
     ['D', '11/1/2019 6:50:00 AM', 'Southwest Airlines', '3234', 'D7', '4'],
     ['D', '11/1/2019 3:20:00 PM', 'Southwest Airlines', '3286', 'C19', '4'],
     ['D', '11/1/2019 9:00:00 PM', 'Southwest Airlines', '3656', 'C7', '4'],
     ['D', '11/1/2019 6:55:00 AM', 'Southwest Airlines', '4450', 'C9', '4'],
     ['D', '11/1/2019 8:30:00 AM', 'Southwest Airlines', '4465', 'C3', '4'],
     ['D', '11/1/2019 4:25:00 PM', 'Southwest Airlines', '4656', 'C12', '4'],
     ['D', '11/1/2019 7:40:00 AM', 'Southwest Airlines', '4659', 'D5', '4'],
     ['D', '11/1/2019 2:30:00 PM', 'Southwest Airlines', '5245', 'C3', '4'],
     ['D', '11/1/2019 9:55:00 PM', 'Southwest Airlines', '5266', 'D6', '4'],
     ['D', '11/1/2019 5:55:00 PM', 'Southwest Airlines', '6299', 'D8', '4'],
     ['D', '11/1/2019 10:45:00 AM', 'Southwest Airlines', '6465', 'D5', '4'],
     ['D', '11/1/2019 10:55:00 AM', 'Southwest Airlines', '6545', 'C17', '4'],
     ['D', '11/1/2019 7:00:00 PM', 'Southwest Airlines', '6987', 'C9', '4'],
     ['A', '11/1/2019 10:05:00 AM', 'Southwest Airlines', '101', 'D5', '4'],
     ['A', '11/1/2019 8:10:00 PM', 'Southwest Airlines', '102', 'D2', '4'],
     ['A', '11/1/2019 8:15:00 AM', 'Southwest Airlines', '105', 'D5', '4'],
     ['A', '11/1/2019 1:55:00 PM', 'Southwest Airlines', '130', 'C1', '4'],
     ['A', '11/1/2019 9:05:00 AM', 'Southwest Airlines', '136', 'C16', '4'],
     ['A', '11/1/2019 11:00:00 PM', 'Southwest Airlines', '138', 'C6', '4'],
     ['A', '11/1/2019 11:25:00 PM', 'Southwest Airlines', '174', 'D8', '4'],
     ['A', '11/1/2019 8:15:00 PM', 'Southwest Airlines', '180', 'C7', '4'],
     ['A', '11/1/2019 7:45:00 AM', 'Southwest Airlines', '195', 'C7', '4'],
     ['A', '11/1/2019 2:55:00 PM', 'Southwest Airlines', '200', 'C17', '4'],
     ['A', '11/1/2019 11:15:00 AM', 'Southwest Airlines', '215', 'C3', '4'],
     ['A', '11/1/2019 9:50:00 AM', 'Southwest Airlines', '217', 'D4', '4'],
     ['A', '11/1/2019 8:05:00 PM', 'Southwest Airlines', '249', 'D5', '4'],
     ['A', '11/1/2019 10:20:00 PM', 'Southwest Airlines', '270', 'C1', '4'],
     ['A', '11/1/2019 5:55:00 PM', 'Southwest Airlines', '291', 'C12', '4'],
     ['A', '11/1/2019 7:45:00 AM', 'Southwest Airlines', '296', 'C18', '4'],
     ['A', '11/1/2019 6:35:00 PM', 'Southwest Airlines', '301', 'C8', '4'],
     ['A', '11/1/2019 3:00:00 PM', 'Southwest Airlines', '310', 'C1', '4'],
     ['A', '11/1/2019 8:30:00 AM', 'Southwest Airlines', '334', 'D8', '4'],
     ['A', '11/1/2019 11:55:00 AM', 'Southwest Airlines', '343', 'D4', '4'],
     ['A', '11/1/2019 7:30:00 AM', 'Southwest Airlines', '377', 'C9', '4'],
     ['A', '11/1/2019 6:40:00 PM', 'Southwest Airlines', '385', 'C16', '4'],
     ['A', '11/1/2019 8:05:00 PM', 'Southwest Airlines', '437', 'D8', '4'],
     ['A', '11/1/2019 7:55:00 AM', 'Southwest Airlines', '439', 'C7', '4'],
     ['A', '11/1/2019 7:35:00 AM', 'Southwest Airlines', '460', 'C8', '4'],
     ['A', '11/1/2019 9:55:00 AM', 'Southwest Airlines', '477', 'D7', '4'],
     ['A', '11/1/2019 8:00:00 PM', 'Southwest Airlines', '478', 'C18', '4'],
     ['A', '11/1/2019 9:20:00 AM', 'Southwest Airlines', '489', 'C19', '4'],
     ['A', '11/1/2019 10:35:00 AM', 'Southwest Airlines', '498', 'D4', '4'],
     ['A', '11/1/2019 1:05:00 PM', 'Southwest Airlines', '516', 'C1', '4'],
     ['A', '11/1/2019 1:35:00 PM', 'Southwest Airlines', '532', 'D4', '4'],
     ['A', '11/1/2019 5:35:00 PM', 'Southwest Airlines', '534', 'D4', '4'],
     ['A', '11/1/2019 4:25:00 PM', 'Southwest Airlines', '544', 'C17', '4'],
     ['A', '11/1/2019 9:00:00 AM', 'Southwest Airlines', '546', 'C9', '4'],
     ['A', '11/1/2019 11:05:00 PM', 'Southwest Airlines', '559', 'C3', '4'],
     ['A', '11/1/2019 8:35:00 PM', 'Southwest Airlines', '561', 'C9', '4'],
     ['A', '11/1/2019 11:40:00 AM', 'Southwest Airlines', '617', 'C14', '4'],
     ['A', '11/1/2019 7:40:00 PM', 'Southwest Airlines', '633', 'C16', '4'],
     ['A', '11/1/2019 11:15:00 PM', 'Southwest Airlines', '641', 'C4', '4'],
     ['A', '11/1/2019 10:50:00 AM', 'Southwest Airlines', '644', 'D2', '4'],
     ['A', '11/1/2019 1:00:00 PM', 'Southwest Airlines', '689', 'C7', '4'],
     ['A', '11/1/2019 9:50:00 PM', 'Southwest Airlines', '712', 'C16', '4'],
     ['A', '11/1/2019 6:55:00 AM', 'Southwest Airlines', '744', 'C12', '4'],
     ['A', '11/1/2019 12:20:00 PM', 'Southwest Airlines', '753', 'D2', '4'],
     ['A', '11/1/2019 4:35:00 PM', 'Southwest Airlines', '754', 'C7', '4'],
     ['A', '11/1/2019 8:00:00 PM', 'Southwest Airlines', '755', 'C1', '4'],
     ['A', '11/1/2019 11:00:00 AM', 'Southwest Airlines', '766', 'C8', '4'],
     ['A', '11/1/2019 10:55:00 PM', 'Southwest Airlines', '777', 'D6', '4'],
     ['A', '11/1/2019 6:30:00 PM', 'Southwest Airlines', '797', 'C7', '4'],
     ['A', '11/1/2019 9:20:00 PM', 'Southwest Airlines', '799', 'C4', '4'],
     ['A', '11/1/2019 11:00:00 PM', 'Southwest Airlines', '801', 'D4', '4'],
     ['A', '11/1/2019 12:10:00 PM', 'Southwest Airlines', '820', 'C3', '4'],
     ['A', '11/1/2019 8:15:00 AM', 'Southwest Airlines', '830', 'D4', '4'],
     ['A', '11/1/2019 7:35:00 AM', 'Southwest Airlines', '858', 'C1', '4'],
     ['A', '11/1/2019 12:35:00 PM', 'Southwest Airlines', '878', 'C17', '4'],
     ['A', '11/1/2019 1:15:00 PM', 'Southwest Airlines', '883', 'D7', '4'],
     ['A', '11/1/2019 10:55:00 AM', 'Southwest Airlines', '904', 'D6', '4'],
     ['A', '11/1/2019 6:55:00 PM', 'Southwest Airlines', '940', 'C6', '4'],
     ['A', '11/1/2019 4:05:00 PM', 'Southwest Airlines', '945', 'C19', '4'],
     ['A', '11/1/2019 8:00:00 PM', 'Southwest Airlines', '964', 'C6', '4'],
     ['A', '11/1/2019 11:35:00 AM', 'Southwest Airlines', '965', 'C1', '4'],
     ['A', '11/1/2019 3:20:00 PM', 'Southwest Airlines', '971', 'C16', '4'],
     ['A', '11/1/2019 7:55:00 PM', 'Southwest Airlines', '997', 'D6', '4'],
     ['A', '11/1/2019 12:45:00 PM', 'Southwest Airlines', '1017', 'C6', '4'],
     ['A', '11/1/2019 9:45:00 AM', 'Southwest Airlines', '1020', 'D6', '4'],
     ['A', '11/1/2019 9:50:00 AM', 'Southwest Airlines', '1034', 'C8', '4'],
     ['A', '11/1/2019 8:05:00 PM', 'Southwest Airlines', '1056', 'C19', '4'],
     ['A', '11/1/2019 10:30:00 PM', 'Southwest Airlines', '1133', 'C19', '4'],
     ['A', '11/1/2019 2:15:00 PM', 'Southwest Airlines', '1219', 'C14', '4'],
     ['A', '11/1/2019 4:55:00 PM', 'Southwest Airlines', '1261', 'C1', '4'],
     ['A', '11/1/2019 9:55:00 AM', 'Southwest Airlines', '1279', 'D2', '4'],
     ['A', '11/1/2019 11:45:00 AM', 'Southwest Airlines', '1282', 'C9', '4'],
     ['A', '11/1/2019 9:00:00 PM', 'Southwest Airlines', '1298', 'C18', '4'],
     ['A', '11/1/2019 9:10:00 PM', 'Southwest Airlines', '1308', 'D7', '4'],
     ['A', '11/1/2019 12:05:00 PM', 'Southwest Airlines', '1338', 'C19', '4'],
     ['A', '11/1/2019 7:45:00 AM', 'Southwest Airlines', '1343', 'C17', '4'],
     ['A', '11/1/2019 2:35:00 PM', 'Southwest Airlines', '1354', 'D2', '4'],
     ['A', '11/1/2019 9:50:00 AM', 'Southwest Airlines', '1371', 'C3', '4'],
     ['A', '11/1/2019 11:00:00 AM', 'Southwest Airlines', '1397', 'D7', '4'],
     ['A', '11/1/2019 9:00:00 PM', 'Southwest Airlines', '1401', 'D6', '4'],
     ['A', '11/1/2019 6:05:00 PM', 'Southwest Airlines', '1404', 'C18', '4'],
     ['A', '11/1/2019 10:00:00 AM', 'Southwest Airlines', '1406', 'C7', '4'],
     ['A', '11/1/2019 2:50:00 PM', 'Southwest Airlines', '1415', 'C7', '4'],
     ['A', '11/1/2019 10:05:00 AM', 'Southwest Airlines', '1431', 'D8', '4'],
     ['A', '11/1/2019 1:05:00 PM', 'Southwest Airlines', '1540', 'C18', '4'],
     ['A', '11/1/2019 1:20:00 PM', 'Southwest Airlines', '1557', 'C12', '4'],
     ['A', '11/1/2019 7:50:00 PM', 'Southwest Airlines', '1568', 'C13', '4'],
     ['A', '11/1/2019 8:40:00 PM', 'Southwest Airlines', '1576', 'D4', '4'],
     ['A', '11/1/2019 1:20:00 PM', 'Southwest Airlines', '1582', 'D5', '4'],
     ['A', '11/1/2019 10:00:00 AM', 'Southwest Airlines', '1584', 'C17', '4'],
     ['A', '11/1/2019 6:30:00 PM', 'Southwest Airlines', '1655', 'C1', '4'],
     ['A', '11/1/2019 5:10:00 PM', 'Southwest Airlines', '1656', 'C8', '4'],
     ['A', '11/1/2019 3:50:00 PM', 'Southwest Airlines', '1662', 'D7', '4'],
     ['A', '11/1/2019 6:15:00 PM', 'Southwest Airlines', '1670', 'C17', '4'],
     ['A', '11/1/2019 7:30:00 AM', 'Southwest Airlines', '1671', 'C19', '4'],
     ['A', '11/1/2019 3:00:00 PM', 'Southwest Airlines', '1673', 'D5', '4'],
     ['A', '11/1/2019 8:50:00 PM', 'Southwest Airlines', '1704', 'C16', '4'],
     ['A', '11/1/2019 3:35:00 PM', 'Southwest Airlines', '1713', 'C3', '4'],
     ['A', '11/1/2019 6:35:00 PM', 'Southwest Airlines', '1757', 'D4', '4'],
     ['A', '11/1/2019 12:40:00 PM', 'Southwest Airlines', '1823', 'C8', '4'],
     ['A', '11/1/2019 8:35:00 AM', 'Southwest Airlines', '1833', 'C14', '4'],
     ['A', '11/1/2019 11:50:00 AM', 'Southwest Airlines', '1875', 'C16', '4'],
     ['A', '11/1/2019 6:20:00 PM', 'Southwest Airlines', '1876', 'D3', '4'],
     ['A', '11/1/2019 8:00:00 PM', 'Southwest Airlines', '1877', 'C17', '4'],
     ['A', '11/1/2019 7:30:00 AM', 'Southwest Airlines', '1887', 'C11', '4'],
     ['A', '11/1/2019 4:55:00 PM', 'Southwest Airlines', '1890', 'D5', '4'],
     ['A', '11/1/2019 4:10:00 PM', 'Southwest Airlines', '1910', 'C6', '4'],
     ['A', '11/1/2019 7:20:00 PM', 'Southwest Airlines', '1912', 'C3', '4'],
     ['A', '11/1/2019 2:50:00 PM', 'Southwest Airlines', '1921', 'C18', '4'],
     ['A', '11/1/2019 4:00:00 PM', 'Southwest Airlines', '1939', 'C6', '4'],
     ['A', '11/1/2019 11:30:00 AM', 'Southwest Airlines', '1949', 'C17', '4'],
     ['A', '11/1/2019 9:35:00 PM', 'Southwest Airlines', '1950', 'C9', '4'],
     ['A', '11/1/2019 5:35:00 PM', 'Southwest Airlines', '1957', 'C19', '4'],
     ['A', '11/1/2019 4:45:00 PM', 'Southwest Airlines', '1968', 'D8', '4'],
     ['A', '11/1/2019 10:40:00 AM', 'Southwest Airlines', '1992', 'C19', '4'],
     ['A', '11/1/2019 3:50:00 PM', 'Southwest Airlines', '2014', 'D4', '4'],
     ['A', '11/1/2019 9:30:00 PM', 'Southwest Airlines', '2082', 'D8', '4'],
     ['A', '11/1/2019 3:30:00 PM', 'Southwest Airlines', '2103', 'D6', '4'],
     ['A', '11/1/2019 8:45:00 PM', 'Southwest Airlines', '2114', 'C14', '4'],
     ['A', '11/1/2019 11:25:00 PM', 'Southwest Airlines', '2115', 'C7', '4'],
     ['A', '11/1/2019 3:45:00 PM', 'Southwest Airlines', '2136', 'C9', '4'],
     ['A', '11/1/2019 3:00:00 PM', 'Southwest Airlines', '2178', 'D8', '4'],
     ['A', '11/1/2019 5:15:00 PM', 'Southwest Airlines', '2203', 'C3', '4'],
     ['A', '11/1/2019 9:25:00 AM', 'Southwest Airlines', '2208', 'C14', '4'],
     ['A', '11/1/2019 5:35:00 PM', 'Southwest Airlines', '2221', 'C16', '4'],
     ['A', '11/1/2019 9:40:00 AM', 'Southwest Airlines', '2222', 'C7', '4'],
     ['A', '11/1/2019 8:30:00 PM', 'Southwest Airlines', '2272', 'D7', '4'],
     ['A', '11/1/2019 7:40:00 AM', 'Southwest Airlines', '2279', 'D7', '4'],
     ['A', '11/1/2019 5:50:00 PM', 'Southwest Airlines', '2286', 'C6', '4'],
     ['A', '11/1/2019 7:55:00 PM', 'Southwest Airlines', '2306', 'C19', '4'],
     ['A', '11/1/2019 11:35:00 AM', 'Southwest Airlines', '2323', 'D5', '4'],
     ['A', '11/1/2019 9:00:00 PM', 'Southwest Airlines', '2346', 'C12', '4'],
     ['A', '11/1/2019 6:15:00 AM', 'Southwest Airlines', '2347', 'C19', '4'],
     ['A', '11/1/2019 6:40:00 PM', 'Southwest Airlines', '2366', 'D2', '4'],
     ['A', '11/1/2019 2:05:00 PM', 'Southwest Airlines', '2373', 'C9', '4'],
     ['A', '11/1/2019 11:35:00 AM', 'Southwest Airlines', '2377', 'D8', '4'],
     ['A', '11/1/2019 1:20:00 PM', 'Southwest Airlines', '2385', 'D8', '4'],
     ['A', '11/1/2019 9:55:00 AM', 'Southwest Airlines', '2407', 'C1', '4'],
     ['A', '11/1/2019 8:10:00 PM', 'Southwest Airlines', '2415', 'C11', '4'],
     ['A', '11/1/2019 7:40:00 AM', 'Southwest Airlines', '2435', 'D2', '4'],
     ['A', '11/1/2019 9:05:00 AM', 'Southwest Airlines', '2441', 'D5', '4'],
     ['A', '11/1/2019 8:20:00 PM', 'Southwest Airlines', '2445', 'D4', '4'],
     ['A', '11/1/2019 1:30:00 PM', 'Southwest Airlines', '2475', 'D6', '4'],
     ['A', '11/1/2019 9:50:00 AM', 'Southwest Airlines', '2476', 'C6', '4'],
     ['A', '11/1/2019 7:50:00 PM', 'Southwest Airlines', '2490', 'C12', '4'],
     ['A', '11/1/2019 6:15:00 PM', 'Southwest Airlines', '2510', 'D8', '4'],
     ['A', '11/1/2019 3:30:00 PM', 'Southwest Airlines', '2532', 'C8', '4'],
     ['A', '11/1/2019 7:15:00 PM', 'Southwest Airlines', '2571', 'D7', '4'],
     ['A', '11/1/2019 6:25:00 PM', 'Southwest Airlines', '2600', 'C14', '4'],
     ['A', '11/1/2019 3:35:00 PM', 'Southwest Airlines', '2664', 'C12', '4'],
     ['A', '11/1/2019 6:10:00 PM', 'Southwest Airlines', '2699', 'D7', '4'],
     ['A', '11/1/2019 3:45:00 PM', 'Southwest Airlines', '2730', 'C14', '4'],
     ['A', '11/1/2019 11:15:00 PM', 'Southwest Airlines', '2788', 'D2', '4'],
     ['A', '11/1/2019 8:10:00 AM', 'Southwest Airlines', '2790', 'C6', '4'],
     ['A', '11/1/2019 1:45:00 PM', 'Southwest Airlines', '2809', 'C16', '4'],
     ['A', '11/1/2019 9:10:00 AM', 'Southwest Airlines', '2819', 'C12', '4'],
     ['A', '11/1/2019 12:25:00 PM', 'Southwest Airlines', '2929', 'D6', '4'],
     ['A', '11/1/2019 6:55:00 AM', 'Southwest Airlines', '2948', 'D5', '4'],
     ['A', '11/1/2019 7:55:00 PM', 'Southwest Airlines', '3195', 'C8', '4'],
     ['A', '11/1/2019 7:00:00 PM', 'Southwest Airlines', '3228', 'C19', '4'],
     ['A', '11/1/2019 6:15:00 AM', 'Southwest Airlines', '3234', 'D7', '4'],
     ['A', '11/1/2019 2:25:00 PM', 'Southwest Airlines', '3237', 'C6', '4'],
     ['A', '11/1/2019 2:30:00 PM', 'Southwest Airlines', '3286', 'C19', '4'],
     ['A', '11/1/2019 4:50:00 PM', 'Southwest Airlines', '3699', 'D2', '4'],
     ['A', '11/1/2019 6:20:00 AM', 'Southwest Airlines', '4450', 'C9', '4'],
     ['A', '11/1/2019 7:40:00 AM', 'Southwest Airlines', '4465', 'D3', '4'],
     ['A', '11/1/2019 7:00:00 AM', 'Southwest Airlines', '4659', 'C17', '4'],
     ['A', '11/1/2019 7:40:00 PM', 'Southwest Airlines', '4660', 'C12', '4'],
     ['A', '11/1/2019 1:50:00 PM', 'Southwest Airlines', '5245', 'C3', '4'],
     ['A', '11/1/2019 8:35:00 PM', 'Southwest Airlines', '5485', 'C3', '4'],
     ['A', '11/1/2019 10:05:00 AM', 'Southwest Airlines', '6545', 'C18', '4'],
     ['A', '11/1/2019 5:55:00 PM', 'Southwest Airlines', '6550', 'C9', '4'],
     ['A', '11/1/2019 11:35:00 PM', 'Southwest Airlines', '6560', 'C8', '4'],
     ['D', '11/1/2019 12:07:00 AM', 'American Airlines', '1542', 'A12', '4'],
     ['D', '11/1/2019 12:40:00 AM', 'American Airlines', '817', 'A5', '4'],
     ['D', '11/1/2019 1:05:00 AM', 'American Airlines', '599', 'A3', '4'],
     ['D', '11/1/2019 5:00:00 AM', 'American Airlines', '507', 'A3', '4'],
     ['D', '11/1/2019 5:00:00 AM', 'United Airlines', '1600', '5', '2'],
     ['D', '11/1/2019 5:05:00 AM', 'United Airlines', '5437', '7', '2'],
     ['D', '11/1/2019 5:15:00 AM', 'American Airlines', '698', 'B6', '4'],
     ['D', '11/1/2019 5:45:00 AM', 'United Airlines', '5963', '3', '2'],
     ['D', '11/1/2019 5:50:00 AM', 'American Airlines', '5836', 'B2', '4'],
     ['D', '11/1/2019 5:55:00 AM', 'Alaska Airlines', '591', '10', '2'],
     ['D', '11/1/2019 6:00:00 AM', 'American Airlines', '655', 'A21', '4'],
     ['D', '11/1/2019 6:26:00 AM', 'United Airlines', '1131', '6B', '2'],
     ['D', '11/1/2019 6:30:00 AM', 'American Airlines', '1059', 'A3', '4'],
     ['D', '11/1/2019 6:35:00 AM', 'American Airlines', '567', 'A22', '4'],
     ['D', '11/1/2019 6:45:00 AM', 'American Airlines', '407', 'A20', '4'],
     ['D', '11/1/2019 6:50:00 AM', 'Alaska Airlines', '2805', '11', '2'],
     ['D', '11/1/2019 7:00:00 AM', 'American Airlines', '601', 'A11', '4'],
     ['D', '11/1/2019 7:00:00 AM', 'American Airlines', '5759', 'B20', '4'],
     ['D', '11/1/2019 7:07:00 AM', 'American Airlines', '1107', 'B8', '4'],
     ['D', '11/1/2019 7:08:00 AM', 'United Airlines', '1774', '7', '2'],
     ['D', '11/1/2019 7:17:00 AM', 'United Airlines', '656', '8', '2'],
     ['D', '11/1/2019 7:20:00 AM', 'American Airlines', '2105', 'A25', '4'],
     ['D', '11/1/2019 7:20:00 AM', 'American Airlines', '5710', 'B3', '4'],
     ['D', '11/1/2019 7:21:00 AM', 'American Airlines', '5766', 'B5', '4'],
     ['D', '11/1/2019 7:22:00 AM', 'American Airlines', '445', 'B14', '4'],
     ['D', '11/1/2019 7:25:00 AM', 'American Airlines', '1765', 'A5', '4'],
     ['D', '11/1/2019 7:26:00 AM', 'American Airlines', '1298', 'A23', '4'],
     ['D', '11/1/2019 7:30:00 AM', 'American Airlines', '2795', 'B10', '4'],
     ['D', '11/1/2019 7:30:00 AM', 'American Airlines', '5936', 'B2', '4'],
     ['D', '11/1/2019 7:35:00 AM', 'American Airlines', '486', 'B9', '4'],
     ['D', '11/1/2019 7:35:00 AM', 'American Airlines', '5802', 'B12', '4'],
     ['D', '11/1/2019 7:38:00 AM', 'American Airlines', '5988', 'A1', '4'],
     ['D', '11/1/2019 7:40:00 AM', 'American Airlines', '438', 'A24', '4'],
     ['D', '11/1/2019 7:40:00 AM', 'American Airlines', '1003', 'A18', '4'],
     ['D', '11/1/2019 7:45:00 AM', 'American Airlines', '711', 'A6', '4'],
     ['D', '11/1/2019 8:11:00 AM', 'American Airlines', '3196', 'B7', '4'],
     ['D', '11/1/2019 8:20:00 AM', 'American Airlines', '739', 'A7', '4'],
     ['A', '11/1/2019 7:32:00 AM', 'Frontier Airlines', '2415', 'F1', '3'],
     ['D', '11/1/2019 8:27:00 AM', 'Frontier Airlines', '2414', 'F1', '3'],
     ['D', '11/1/2019 8:30:00 AM', 'American Airlines', '482', 'A12', '4'],
     ['D', '11/1/2019 8:35:00 AM', 'American Airlines', '1664', 'A30', '4'],
     ['A', '11/1/2019 7:51:00 AM', 'Frontier Airlines', '1731', 'F1', '3'],
     ['D', '11/1/2019 8:36:00 AM', 'Frontier Airlines', '1730', 'F1', '3'],
     ['D', '11/1/2019 8:37:00 AM', 'American Airlines', '532', 'A8', '4'],
     ['D', '11/1/2019 8:37:00 AM', 'American Airlines', '685', 'A20', '4'],
     ['D', '11/1/2019 8:38:00 AM', 'American Airlines', '1762', 'A5', '4'],
     ['D', '11/1/2019 8:39:00 AM', 'American Airlines', '682', 'A13', '4'],
     ['D', '11/1/2019 8:40:00 AM', 'American Airlines', '5878', 'B17', '4'],
     ['D', '11/1/2019 8:41:00 AM', 'American Airlines', '501', 'A9', '4'],
     ['D', '11/1/2019 8:43:00 AM', 'American Airlines', '523', 'B4', '4'],
     ['D', '11/1/2019 8:43:00 AM', 'American Airlines', '2983', 'A1', '4'],
     ['D', '11/1/2019 8:44:00 AM', 'American Airlines', '590', 'A27', '4'],
     ['D', '11/1/2019 8:47:00 AM', 'American Airlines', '372', 'A23', '4'],
     ['D', '11/1/2019 8:47:00 AM', 'American Airlines', '2978', 'B5', '4'],
     ['D', '11/1/2019 8:49:00 AM', 'American Airlines', '554', 'A6', '4'],
     ['D', '11/1/2019 8:50:00 AM', 'American Airlines', '2994', 'B20', '4'],
     ['D', '11/1/2019 8:50:00 AM', 'Sun Country Airlines', '602', 'F3', '3'],
     ['D', '11/1/2019 8:51:00 AM', 'American Airlines', '5914', 'B2', '4'],
     ['D', '11/1/2019 8:53:00 AM', 'American Airlines', '1959', 'B11', '4'],
     ['D', '11/1/2019 8:55:00 AM', 'American Airlines', '1580', 'A21', '4'],
     ['D', '11/1/2019 8:55:00 AM', 'American Airlines', '5782', 'B12', '4'],
     ['D', '11/1/2019 8:56:00 AM', 'American Airlines', '608', 'A25', '4'],
     ['D', '11/1/2019 8:57:00 AM', 'American Airlines', '1968', 'A11', '4'],
     ['D', '11/1/2019 8:58:00 AM', 'American Airlines', '613', 'B9', '4'],
     ['D', '11/1/2019 8:58:00 AM', 'American Airlines', '1887', 'A3', '4'],
     ['D', '11/1/2019 8:59:00 AM', 'American Airlines', '2100', 'B10', '4'],
     ['D', '11/1/2019 9:00:00 AM', 'American Airlines', '558', 'A10', '4'],
     ['D', '11/1/2019 9:02:00 AM', 'American Airlines', '854', 'A29', '4'],
     ['D', '11/1/2019 9:05:00 AM', 'United Airlines', '248', '6A', '2'],
     ['D', '11/1/2019 9:06:00 AM', 'American Airlines', '5824', 'B7', '4'],
     ['D', '11/1/2019 9:10:00 AM', 'American Airlines', '1569', 'A2', '4'],
     ['D', '11/1/2019 9:15:00 AM', 'American Airlines', '2339', 'A19', '4'],
     ['D', '11/1/2019 9:30:00 AM', 'American Airlines', '1897', 'A8', '4'],
     ['D', '11/1/2019 9:35:00 AM', 'Alaska Airlines', '551', '11', '2'],
     ['D', '11/1/2019 9:37:00 AM', 'American Airlines', '760', 'B14', '4'],
     ['D', '11/1/2019 9:40:00 AM', 'United Airlines', '604', '5', '2'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '524', 'A12', '4'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '531', 'A4', '4'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '652', 'A17', '4'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '695', 'A23', '4'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '2083', 'A18', '4'],
     ['D', '11/1/2019 9:50:00 AM', 'American Airlines', '3158', 'B19', '4'],
     ['D', '11/1/2019 9:52:00 AM', 'American Airlines', '2722', 'A13', '4'],
     ['D', '11/1/2019 9:52:00 AM', 'American Airlines', '5768', 'B7', '4'],
     ['D', '11/1/2019 9:56:00 AM', 'American Airlines', '2969', 'B15A', '4'],
     ['D', '11/1/2019 9:58:00 AM', 'American Airlines', '5736', 'B3', '4'],
     ['A', '11/1/2019 8:43:00 AM', 'Frontier Airlines', '755', 'F2', '3'],
     ['D', '11/1/2019 9:58:00 AM', 'Frontier Airlines', '754', 'F2', '3'],
     ['D', '11/1/2019 9:59:00 AM', 'American Airlines', '873', 'A14', '4'],
     ['D', '11/1/2019 9:59:00 AM', 'American Airlines', '5742', 'B5', '4'],
     ['D', '11/1/2019 10:00:00 AM', 'American Airlines', '498', 'A5', '4'],
     ['A', '11/8/2019 8:32:00 PM', 'American Airlines', '5801', 'B20', '4'],
     ['A', '11/8/2019 8:33:00 PM', 'American Airlines', '3258', 'B17', '4'],
     ['A', '11/8/2019 8:35:00 PM', 'United Airlines', '1115', '6B', '2'],
     ['A', '11/8/2019 8:35:00 PM', 'Southwest Airlines', '158', 'C1', '4'],
     ['D', '11/9/2019 8:30:00 AM', 'Hawaiian Airlines', '35', 'F10A', '3'],
     ['A', '11/8/2019 8:35:00 PM', 'Hawaiian Airlines', '36', 'F10A', '3'],
     ['A', '11/8/2019 8:36:00 PM', 'American Airlines', '472', 'A25', '4'],
     ['D', '11/9/2019 10:35:00 AM', 'American Airlines', '1897', 'A25', '4'],
     ['A', '11/8/2019 8:36:00 PM', 'American Airlines', '2450', 'A8', '4'],
     ['D', '11/9/2019 9:40:00 AM', 'American Airlines', '532', 'B6', '4'],
     ['A', '11/8/2019 8:38:00 PM', 'American Airlines', '1792', 'B9', '4'],
     ['D', '11/9/2019 8:20:00 AM', 'American Airlines', '1892', 'A4', '4'],
     ['A', '11/8/2019 8:39:00 PM', 'American Airlines', '1696', 'A7', '4'],
     ['D', '11/9/2019 9:39:00 AM', 'American Airlines', '372', 'B27', '4'],
     ['A', '11/8/2019 8:40:00 PM', 'Southwest Airlines', '2279', 'D7', '4'],
     ['A', '11/8/2019 8:42:00 PM', 'American Airlines', '1766', 'B10', '4'],
     ['D', '11/9/2019 9:54:00 AM', 'American Airlines', '613', 'A6', '4'],
     ['A', '11/8/2019 8:49:00 PM', 'United Airlines', '414', '7', '2'],
     ['A', '11/8/2019 8:50:00 PM', 'Southwest Airlines', '2587', 'C8', '4'],
     ['A', '11/8/2019 8:53:00 PM', 'United Airlines', '308', '8', '2'],
     ['A', '11/8/2019 8:55:00 PM', 'Southwest Airlines', '1517', 'C19', '4'],
     ['A', '11/8/2019 8:55:00 PM', 'Southwest Airlines', '2570', 'C13', '4'],
     ['A', '11/8/2019 8:59:00 PM', 'United Airlines', '5458', '3', '2'],
     ['A', '11/8/2019 9:00:00 PM', 'Southwest Airlines', '700', 'C6', '4'],
     ['A', '11/8/2019 9:00:00 PM', 'Southwest Airlines', '2327', 'D2', '4'],
     ['A', '11/8/2019 9:00:00 PM', 'Southwest Airlines', '1675', 'D7', '4'],
     ['A', '11/8/2019 9:00:00 PM', 'Southwest Airlines', '868', 'D5', '4'],
     ['A', '11/8/2019 9:05:00 PM', 'Southwest Airlines', '3904', 'D6', '4'],
     ['A', '11/8/2019 9:10:00 PM', 'Southwest Airlines', '1581', 'D4', '4'],
     ['A', '11/8/2019 9:10:00 PM', 'Southwest Airlines', '2513', 'C4', '4'],
     ['A', '11/8/2019 9:10:00 PM', 'Southwest Airlines', '2306', 'D8', '4'],
     ['A', '11/8/2019 9:10:00 PM', 'Southwest Airlines', '2452', 'C9', '4'],
     ['A', '11/8/2019 9:13:00 PM', 'American Airlines', '854', 'A22', '4'],
     ['D', '11/9/2019 8:25:00 AM', 'American Airlines', '1765', 'A20', '4'],
     ['A', '11/8/2019 9:15:00 PM', 'Southwest Airlines', '2137', 'C18', '4'],
     ['A', '11/8/2019 9:16:00 PM', 'United Airlines', '594', '6A', '2'],
     ['A', '11/8/2019 9:30:00 PM', 'Southwest Airlines', '1010', 'C7', '4'],
     ['A', '11/8/2019 9:31:00 PM', 'JetBlue Airways', '603', 'F3', '3'],
     ['A', '11/8/2019 9:35:00 PM', 'Southwest Airlines', '1303', 'C11', '4'],
     ['A', '11/8/2019 9:39:00 PM', 'American Airlines', '715', 'A18', '4'],
     ['D', '11/9/2019 9:57:00 AM', 'American Airlines', '1968', 'A22', '4'],
     ['A', '11/8/2019 9:50:00 PM', 'American Airlines', '5794', 'B2', '4'],
     ['A', '11/8/2019 9:50:00 PM', 'American Airlines', '5845', 'B2', '4'],
     ['A', '11/8/2019 9:50:00 PM', 'Southwest Airlines', '2588', 'C1', '4'],
     ['A', '11/8/2019 9:51:00 PM', 'WestJet', '1494', 'B23', '4'],
     ['D', '11/9/2019 8:00:00 AM', 'WestJet', '1499', 'B23', '4'],
     ['A', '11/8/2019 9:56:00 PM', 'American Airlines', '5994', 'B20', '4'],
     ['A', '11/8/2019 9:56:00 PM', 'American Airlines', '5743', 'B5', '4'],
     ['A', '11/8/2019 9:59:00 PM', 'American Airlines', '3048', 'B12', '4'],
     ['A', '11/8/2019 9:59:00 PM', 'Delta Air Lines', '2209', 'F4', '3'],
     ['A', '11/8/2019 10:00:00 PM', 'Southwest Airlines', '4447', 'C7', '4'],
     ['A', '11/8/2019 10:02:00 PM', 'American Airlines', '3055', 'B7', '4'],
     ['A', '11/8/2019 10:02:00 PM', 'American Airlines', '5832', 'B19', '4'],
     ['A', '11/8/2019 10:03:00 PM', 'American Airlines', '480', 'A9', '4'],
     ['D', '11/9/2019 12:24:00 AM', 'American Airlines', '2651', '', '4'],
     ['A', '11/8/2019 10:08:00 PM', 'Delta Air Lines', '1239', 'F10', '3'],
     ['A', '11/8/2019 10:09:00 PM', 'United Airlines', '5656', '6B', '2'],
     ['A', '11/8/2019 10:12:00 PM', 'Delta Air Lines', '2559', 'F9', '3'],
     ['A', '11/8/2019 10:13:00 PM', 'American Airlines', '5962', 'B17', '4'],
     ['A', '11/8/2019 10:14:00 PM', 'American Airlines', '1386', 'A30', '4'],
     ['D', '11/9/2019 8:25:00 AM', 'American Airlines', '1298', 'A14', '4'],
     ['D', '11/9/2019 1:13:00 AM', 'American Airlines', '1542', 'A9', '4'],
     ['A', '11/8/2019 10:15:00 PM', 'American Airlines', '536', 'A26', '4'],
     ['D', '11/9/2019 12:05:00 AM', 'American Airlines', '640', 'A6', '4'],
     ['A', '11/8/2019 10:20:00 PM', 'Southwest Airlines', '248', 'C3', '4'],
     ['A', '11/8/2019 10:20:00 PM', 'Southwest Airlines', '1984', 'D8', '4'],
     ['A', '11/8/2019 10:20:00 PM', 'Southwest Airlines', '2051', 'D5', '4'],
     ['A', '11/8/2019 10:20:00 PM', 'Southwest Airlines', '518', 'C4', '4'],
     ['A', '11/8/2019 10:28:00 PM', 'American Airlines', '1632', 'A18', '4'],
     ['D', '11/9/2019 12:49:00 AM', 'American Airlines', '782', 'A7', '4'],
     ['A', '11/8/2019 10:32:00 PM', 'United Airlines', '483', '8', '2'],
     ['A', '11/8/2019 10:32:00 PM', 'American Airlines', '316', 'B13', '4'],
     ['D', '11/9/2019 10:55:00 AM', 'American Airlines', '498', 'B13', '4'],
     ['A', '11/8/2019 10:35:00 PM', 'Southwest Airlines', '1722', 'C11', '4'],
     ['A', '11/8/2019 10:38:00 PM', 'Delta Air Lines', '2239', 'F8', '3'],
     ['D', '11/9/2019 7:05:00 AM', 'Delta Air Lines', '2042', 'F5', '3'],
     ['A', '11/8/2019 10:41:00 PM', 'Delta Air Lines', '1382', 'F5', '3'],
     ['A', '11/8/2019 10:41:00 PM', 'Alaska Airlines', '2804', '10', '2'],
     ['A', '11/8/2019 10:45:00 PM', 'JetBlue Airways', '1157', 'F2', '3'],
     ['D', '11/8/2019 11:35:00 PM', 'JetBlue Airways', '136', 'F2', '3'],
     ['A', '11/8/2019 10:58:00 PM', 'Delta Air Lines', '5694', 'F7', '3'],
     ['A', '11/8/2019 10:59:00 PM', 'Air Canada', '1835', 'B28', '4'],
     ['D', '11/9/2019 12:05:00 AM', 'Air Canada', '1834', 'B28', '4'],
     ['A', '11/8/2019 10:59:00 PM', 'Alaska Airlines', '608', '11', '2'],
     ['A', '11/8/2019 11:07:00 PM', 'Delta Air Lines', '726', 'F6', '3'],
     ['A', '11/8/2019 11:10:00 PM', 'Southwest Airlines', '1909', 'C17', '4'],
     ['A', '11/8/2019 11:25:00 PM', 'American Airlines', '2097', 'A7', '4'],
     ['D', '11/9/2019 2:05:00 AM', 'American Airlines', '599', 'A5', '4'],
     ['A', '11/8/2019 11:25:00 PM', 'Southwest Airlines', '2189', 'C19', '4'],
     ['A', '11/8/2019 11:25:00 PM', 'Southwest Airlines', '3893', 'D6', '4'],
     ['A', '11/8/2019 11:33:00 PM', 'United Airlines', '2257', '8', '2'],
     ['A', '11/8/2019 11:40:00 PM', 'Southwest Airlines', '2171', 'C16', '4'],
     ['A', '11/8/2019 11:45:00 PM', 'Southwest Airlines', '3229', 'D2', '4'],
     ['A', '11/8/2019 11:45:00 PM', 'Southwest Airlines', '698', 'C3', '4'],
     ['A', '11/8/2019 11:49:00 PM', 'Alaska Airlines', '936', '10', '2'],
     ['D', '11/9/2019 12:37:00 AM', 'JetBlue Airways', '1158', 'F3', '3'],
     ['A', '11/8/2019 11:50:00 PM', 'JetBlue Airways', '135', 'F3', '3'],
     ['A', '11/8/2019 11:52:00 PM', 'Delta Air Lines', '2167', 'F1', '3'],
     ['A', '11/8/2019 11:54:00 PM', 'United Airlines', '1829', '7', '2'],
     ['A', '11/8/2019 11:55:00 PM', 'Southwest Airlines', '2868', 'C4', '4'],
     ['A', '11/8/2019 11:57:00 PM', 'United Airlines', '230', '6B', '2'],
     ['A', '11/8/2019 11:58:00 PM', 'American Airlines', '1677', 'A29', '4'],
     ['D', '11/9/2019 6:15:00 AM', 'American Airlines', '698', 'A29', '4'],
     ['A', '11/8/2019 11:58:00 PM', 'United Airlines', '1193', '5', '2'],
     ['A', '11/9/2019 12:10:00 AM', 'Southwest Airlines', '590', 'D5', '4'],
     ['A', '11/9/2019 12:30:00 AM', 'Southwest Airlines', '747', 'C2', '4'],
     ['A', '11/9/2019 12:05:00 AM', 'Southwest Airlines', '1758', 'C9', '4'],
     ['A', '11/9/2019 12:35:00 AM', 'Southwest Airlines', '1938', 'C14', '4'],
     ['D', '11/9/2019 8:15:00 AM', 'Delta Air Lines', '2738', 'F6', '3'],
     ['D', '11/9/2019 6:00:00 AM', 'Delta Air Lines', '1001', 'F10', '3'],
     ['D', '11/9/2019 7:00:00 AM', 'Delta Air Lines', '2007', 'F4', '3'],
     ['D', '11/9/2019 8:00:00 AM', 'Delta Air Lines', '1945', 'F8', '3'],
     ['D', '11/9/2019 7:55:00 AM', 'Delta Air Lines', '1453', 'F2', '3'],
     ['D', '11/9/2019 8:25:00 AM', 'Delta Air Lines', '5699', 'F7', '3'],
     ['D', '11/9/2019 6:44:00 AM', 'Delta Air Lines', '5809', 'F9', '3'],
     ['D', '11/9/2019 7:00:00 AM', 'Delta Air Lines', '1537', 'F1', '3'],
     ['A', '11/8/2019 10:22:00 AM', 'American Airlines', '1487', 'DX', '4'],
     ['D', '11/8/2019 11:38:00 AM', 'American Airlines', '1437', 'DX', '4'],
     ['A', '11/8/2019 11:29:00 AM', 'American Airlines', '250', 'DX', '4'],
     ['D', '11/8/2019 12:33:00 PM', 'American Airlines', '978', 'DX', '4'],
     ['A', '11/9/2019 1:00:00 AM', 'Southwest Airlines', '3295', 'C1', '4'],
     ['A', '11/9/2019 12:40:00 AM', 'Southwest Airlines', '4450', 'C8', '4'],
     ['A', '11/8/2019 5:54:00 AM', 'American Airlines', '693', 'DX', '4'],
     ['D', '11/9/2019 7:48:00 AM', 'American Airlines', '5855', 'B2', '4'],
     ['D', '11/9/2019 8:43:00 AM', 'American Airlines', '5759', 'B5', '4'],
     ['D', '11/9/2019 6:40:00 AM', 'American Airlines', '5836', 'B5', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A24', '4'],
     ['D', '11/7/2019 7:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 7:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 11:05:00 AM', 'American Airlines', '531', 'A22', '4'],
     ['A', '11/7/2019 10:19:00 AM', 'American Airlines', '625', 'A25', '4'],
     ['D', '11/7/2019 7:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 7:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/8/2019 1:45:00 AM', 'American Airlines', '6024', 'CX', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 7:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['D', '11/7/2019 9:00:00 AM', 'Southwest Airlines', '8502', 'C8', '4'],
     ['A', '11/7/2019 12:42:00 PM', 'Contour Airlines', '512', '3', '2'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['A', '11/7/2019 8:09:00 AM', 'American Airlines', '428', 'A22', '4'],
     ['A', '11/7/2019 5:10:00 PM', 'American Airlines', '428', 'A22', '4'],
     ['D', '11/7/2019 1:30:00 PM', 'Contour Airlines', '512', '3', '2'],
     ['A', '11/7/2019 9:58:00 PM', 'American Airlines', '9644', 'B25', '4'],
     ['D', '11/8/2019 5:00:00 PM', 'American Airlines', '9644', 'A30', '4'],
     ['D', '11/9/2019 12:40:00 AM', 'JetBlue Airways', '1158', 'F3', '3'],
     ['D', '11/9/2019 12:45:00 AM', 'United Airlines', '1976', '6B', '2'],
     ['D', '11/9/2019 12:53:00 AM', 'United Airlines', '1756', '7', '2'],
     ['A', '11/9/2019 12:18:00 AM', 'American Airlines', '441', 'A30', '4'],
     ['D', '11/9/2019 1:40:00 AM', 'American Airlines', '447', 'A30', '4'],
     ['A', '11/9/2019 12:09:00 AM', 'Frontier Airlines', '751', 'F2', '3'],
     ['D', '11/9/2019 1:44:00 AM', 'Frontier Airlines', '1426', 'F2', '3'],
     ['A', '11/9/2019 12:50:00 AM', 'Spirit Airlines', '971', 'F10', '3'],
     ['D', '11/9/2019 1:50:00 AM', 'Spirit Airlines', '972', 'F10', '3'],
     ['D', '11/9/2019 5:10:00 AM', 'United Airlines', '1638', '8', '2'],
     ['D', '11/9/2019 6:00:00 AM', 'Alaska Airlines', '923', '11', '2'],
     ['D', '11/9/2019 6:00:00 AM', 'United Airlines', '1600', '7', '2'],
     ['D', '11/9/2019 6:05:00 AM', 'United Airlines', '5437', '3', '2'],
     ['D', '11/9/2019 6:05:00 AM', 'Southwest Airlines', '1649', 'C16', '4'],
     ['D', '11/9/2019 6:15:00 AM', 'Southwest Airlines', '5282', 'C14', '4'],
     ['D', '11/9/2019 6:20:00 AM', 'United Airlines', '656', '5', '2'],
     ['D', '11/9/2019 6:20:00 AM', 'Southwest Airlines', '425', 'C18', '4'],
     ['D', '11/9/2019 6:20:00 AM', 'Southwest Airlines', '6675', 'C8', '4'],
     ['D', '11/9/2019 6:25:00 AM', 'Southwest Airlines', '2113', 'D3', '4'],
     ['A', '11/9/2019 1:10:00 AM', 'American Airlines', '2756', 'A10', '4'],
     ['D', '11/9/2019 6:30:00 AM', 'American Airlines', '1603', 'A25', '4'],
     ['D', '11/9/2019 6:40:00 AM', 'Southwest Airlines', '3436', 'C6', '4'],
     ['D', '11/9/2019 6:45:00 AM', 'United Airlines', '5963', '6B', '2'],
     ['D', '11/9/2019 6:50:00 AM', 'Southwest Airlines', '5880', 'D7', '4'],
     ['A', '11/9/2019 1:35:00 AM', 'Frontier Airlines', '1429', 'F3', '3'],
     ['D', '11/9/2019 6:55:00 AM', 'Frontier Airlines', '2822', 'F3', '3'],
     ['D', '11/9/2019 7:00:00 AM', 'Southwest Airlines', '2633', 'C13', '4'],
     ['D', '11/9/2019 7:15:00 AM', 'Southwest Airlines', '4254', 'C4', '4'],
     ['D', '11/9/2019 7:20:00 AM', 'Southwest Airlines', '3793', 'D2', '4'],
     ['D', '11/9/2019 7:25:00 AM', 'Alaska Airlines', '959', '10', '2'],
     ['D', '11/9/2019 7:25:00 AM', 'Southwest Airlines', '6128', 'D6', '4'],
     ['D', '11/9/2019 7:26:00 AM', 'United Airlines', '1131', '7', '2'],
     ['D', '11/9/2019 7:45:00 AM', 'Alaska Airlines', '2803', '11', '2'],
     ['D', '11/9/2019 7:45:00 AM', 'Southwest Airlines', '1798', 'C12', '4'],
     ['D', '11/9/2019 7:46:00 AM', 'United Airlines', '2000', '3', '2'],
     ['D', '11/9/2019 7:50:00 AM', 'Southwest Airlines', '5588', 'C19', '4'],
     ['A', '11/9/2019 1:02:00 AM', 'American Airlines', '457', 'A11', '4'],
     ['D', '11/9/2019 8:00:00 AM', 'American Airlines', '601', 'A11', '4'],
     ['D', '11/9/2019 8:00:00 AM', 'Southwest Airlines', '3760', 'C9', '4'],
     ['D', '11/9/2019 8:00:00 AM', 'Southwest Airlines', '5480', 'D4', '4'],
     ['D', '11/9/2019 8:08:00 AM', 'United Airlines', '1774', '8', '2'],
     ['D', '11/9/2019 8:10:00 AM', 'American Airlines', '2105', 'B10', '4'],
     ['D', '11/9/2019 8:20:00 AM', 'American Airlines', '486', 'A26', '4'],
     ['D', '11/9/2019 8:20:00 AM', 'American Airlines', '2990', 'A1', '4'],
     ['D', '11/9/2019 8:24:00 AM', 'American Airlines', '5766', 'B20', '4'],
     ['D', '11/9/2019 8:25:00 AM', 'Southwest Airlines', '3757', 'C1', '4'],
     ['D', '11/9/2019 8:28:00 AM', 'American Airlines', '5988', 'B3', '4'],
     ['D', '11/9/2019 8:35:00 AM', 'Hawaiian Airlines', '35', 'F10A', '3'],
     ['D', '11/9/2019 8:35:00 AM', 'Southwest Airlines', '5371', 'C2', '4'],
     ['D', '11/9/2019 8:36:00 AM', 'American Airlines', '5802', 'B17', '4'],
     ['D', '11/9/2019 8:50:00 AM', 'Southwest Airlines', '4522', 'D7', '4'],
     ['A', '11/9/2019 7:48:00 AM', 'American Airlines', '1861', 'A9', '4'],
     ['D', '11/9/2019 9:00:00 AM', 'American Airlines', '515', 'A9', '4'],
     ['D', '11/9/2019 9:00:00 AM', 'Southwest Airlines', '4833', 'D6', '4'],
     ['D', '11/9/2019 9:05:00 AM', 'Southwest Airlines', '5104', 'C3', '4'],
     ['D', '11/9/2019 9:12:00 AM', 'American Airlines', '3196', 'B7', '4'],
     ['D', '11/9/2019 9:15:00 AM', 'Southwest Airlines', '1555', 'C16', '4'],
     ['A', '11/9/2019 7:47:00 AM', 'American Airlines', '2847', 'A18', '4'],
     ['D', '11/9/2019 9:20:00 AM', 'American Airlines', '739', 'A18', '4'],
     ['D', '11/9/2019 9:20:00 AM', 'Southwest Airlines', '3576', 'D3', '4'],
     ['A', '11/9/2019 8:22:00 AM', 'American Airlines', '1404', 'A8', '4'],
     ['D', '11/9/2019 9:25:00 AM', 'American Airlines', '482', 'A5', '4'],
     ['A', '11/9/2019 7:52:00 AM', 'American Airlines', '361', 'A14', '4'],
     ['D', '11/9/2019 9:29:00 AM', 'American Airlines', '711', 'A21', '4'],
     ['D', '11/9/2019 9:30:00 AM', 'Southwest Airlines', '3768', 'C17', '4'],
     ['D', '11/9/2019 9:30:00 AM', 'Southwest Airlines', '4985', 'D5', '4'],
     ['A', '11/9/2019 8:25:00 AM', 'American Airlines', '2027', 'A22', '4'],
     ['D', '11/9/2019 9:35:00 AM', 'American Airlines', '1664', 'A30', '4'],
     ['A', '11/9/2019 7:49:00 AM', 'American Airlines', '2053', 'A4', '4'],
     ['D', '11/9/2019 9:38:00 AM', 'American Airlines', '1762', 'A8', '4'],
     ['A', '11/9/2019 6:00:00 AM', 'American Airlines', '1945', 'A3', '4'],
     ['D', '11/9/2019 9:39:00 AM', 'American Airlines', '682', 'A3', '4'],
     ['A', '11/9/2019 8:34:00 AM', 'American Airlines', '2658', 'A25', '4'],
     ['D', '11/9/2019 9:41:00 AM', 'American Airlines', '501', 'B8', '4'],
     ['A', '11/9/2019 5:54:00 AM', 'American Airlines', '693', 'A7', '4'],
     ['D', '11/9/2019 9:41:00 AM', 'American Airlines', '685', 'A23', '4'],
     ['D', '11/9/2019 9:42:00 AM', 'American Airlines', '5878', 'B19', '4'],
     ['D', '11/9/2019 9:42:00 AM', 'American Airlines', '5914', 'B17', '4'],
     ['A', '11/9/2019 8:48:00 AM', 'American Airlines', '1688', 'B9', '4'],
     ['D', '11/9/2019 9:43:00 AM', 'American Airlines', '523', 'B9', '4'],
     ['D', '11/9/2019 9:47:00 AM', 'American Airlines', '2978', 'B21', '4'],
     ['A', '11/9/2019 10:07:00 AM', 'American Airlines', '625', 'B6', '4'],
     ['D', '11/9/2019 9:51:00 AM', 'American Airlines', '590', 'A19', '4'],
     ['D', '11/9/2019 9:51:00 AM', 'American Airlines', '1831', 'A12', '4'],
     ['D', '11/9/2019 9:51:00 AM', 'American Airlines', '5782', 'B2', '4'],
     ['A', '11/9/2019 8:46:00 AM', 'American Airlines', '1808', 'B10', '4'],
     ['D', '11/9/2019 9:52:00 AM', 'American Airlines', '1959', 'A25', '4'],
     ['D', '11/9/2019 9:52:00 AM', 'American Airlines', '2983', 'A1', '4'],
     ['A', '11/9/2019 8:42:00 AM', 'American Airlines', '1872', 'A14', '4'],
     ['D', '11/9/2019 9:53:00 AM', 'American Airlines', '554', 'A14', '4'],
     ['D', '11/9/2019 9:53:00 AM', 'American Airlines', '3158', 'B19', '4'],
     ['D', '11/9/2019 9:54:00 AM', 'American Airlines', '2994', 'A1', '4'],
     ['A', '11/9/2019 8:55:00 AM', 'American Airlines', '540', 'A26', '4'],
     ['D', '11/9/2019 9:55:00 AM', 'American Airlines', '1580', 'A13', '4'],
     ['A', '11/9/2019 8:49:00 AM', 'American Airlines', '1910', 'B25', '4'],
     ['D', '11/9/2019 9:55:00 AM', 'American Airlines', '1887', 'B25', '4'],
     ['A', '11/9/2019 9:06:00 AM', 'American Airlines', '464', 'A17', '4'],
     ['D', '11/9/2019 9:57:00 AM', 'American Airlines', '608', 'A17', '4'],
     ['A', '11/9/2019 6:56:00 AM', 'American Airlines', '697', 'B14', '4'],
     ['D', '11/9/2019 10:02:00 AM', 'American Airlines', '854', 'B14', '4'],
     ['D', '11/9/2019 10:05:00 AM', 'United Airlines', '1280', '6A', '2'],
     ['A', '11/9/2019 9:11:00 AM', 'American Airlines', '761', 'A11', '4'],
     ['D', '11/9/2019 10:06:00 AM', 'American Airlines', '1569', 'A4', '4'],
     ['A', '11/9/2019 8:58:00 AM', 'American Airlines', '516', 'B10', '4'],
     ['D', '11/9/2019 10:06:00 AM', 'American Airlines', '2100', 'B26', '4'],
     ['D', '11/9/2019 10:06:00 AM', 'American Airlines', '5824', 'B12', '4'],
     ['A', '11/9/2019 9:03:00 AM', 'American Airlines', '1867', 'A19', '4'],
     ['D', '11/9/2019 10:10:00 AM', 'American Airlines', '760', 'B23', '4'],
     ['D', '11/9/2019 10:25:00 AM', 'Southwest Airlines', '444', 'C1', '4'],
     ['D', '11/9/2019 10:30:00 AM', 'Southwest Airlines', '6650', 'C9', '4'],
     ['D', '11/9/2019 10:30:00 AM', 'Southwest Airlines', '3731', 'C8', '4'],
     ['D', '11/9/2019 10:30:00 AM', 'Southwest Airlines', '3487', 'D4', '4'],
     ['A', '11/9/2019 9:12:00 AM', 'American Airlines', '201', 'A7', '4'],
     ['D', '11/9/2019 10:33:00 AM', 'American Airlines', '2339', 'A2', '4'],
     ['D', '11/9/2019 10:35:00 AM', 'Alaska Airlines', '1937', '10', '2'],
     ['D', '11/9/2019 10:35:00 AM', 'Southwest Airlines', '4773', 'C13', '4'],
     ['D', '11/9/2019 10:35:00 AM', 'Southwest Airlines', '6271', 'D8', '4'],
     ['D', '11/9/2019 10:35:00 AM', 'Southwest Airlines', '4169', 'C11', '4'],
     ['D', '11/9/2019 10:40:00 AM', 'United Airlines', '5643', '7', '2'],
     ['D', '11/9/2019 10:40:00 AM', 'Southwest Airlines', '3375', 'C6', '4'],
     ['D', '11/9/2019 10:40:00 AM', 'Southwest Airlines', '4732', 'D5', '4'],
     ['A', '11/9/2019 9:59:00 AM', 'American Airlines', '1474', 'A9', '4'],
     ['D', '11/9/2019 10:50:00 AM', 'American Airlines', '579', 'A10', '4'],
     ['A', '11/9/2019 7:56:00 AM', 'American Airlines', '675', 'A29', '4'],
     ['D', '11/9/2019 10:50:00 AM', 'American Airlines', '663', 'A26', '4'],
     ['A', '11/9/2019 9:49:00 AM', 'American Airlines', '521', 'A25', '4'],
     ['D', '11/9/2019 10:52:00 AM', 'American Airlines', '2722', 'A8', '4'],
     ['A', '11/9/2019 10:03:00 AM', 'American Airlines', '1958', 'A21', '4'],
     ['D', '11/9/2019 10:54:00 AM', 'American Airlines', '695', 'A17', '4'],
     ['A', '11/9/2019 10:01:00 AM', 'American Airlines', '2026', 'B8', '4'],
     ['D', '11/9/2019 10:55:00 AM', 'American Airlines', '2083', 'A9', '4'],
     ['D', '11/9/2019 10:55:00 AM', 'American Airlines', '5757', 'B22', '4'],
     ['D', '11/9/2019 10:55:00 AM', 'American Airlines', '5768', 'B24', '4'],
     ['D', '11/9/2019 10:56:00 AM', 'American Airlines', '2969', 'B1', '4'],
     ['D', '11/9/2019 10:57:00 AM', 'American Airlines', '2976', 'B7', '4'],
     ['D', '11/9/2019 10:59:00 AM', 'American Airlines', '5736', 'B2', '4'],
     ['A', '11/9/2019 8:50:00 AM', 'American Airlines', '2574', 'A4', '4'],
     ['D', '11/9/2019 11:00:00 AM', 'American Airlines', '558', 'A18', '4'],
     ['A', '11/9/2019 9:59:00 AM', 'American Airlines', '1795', 'B9', '4'],
     ['D', '11/9/2019 11:00:00 AM', 'American Airlines', '1185', 'B9', '4'],
     ['D', '11/9/2019 11:00:00 AM', 'American Airlines', '3132', 'B3', '4'],
     ['D', '11/9/2019 11:00:00 AM', 'American Airlines', '5742', 'B5', '4'],
     ['D', '11/9/2019 11:00:00 AM', 'American Airlines', '5902', 'B2', '4'],
     ['A', '11/9/2019 10:03:00 AM', 'American Airlines', '497', 'A10', '4'],
     ['D', '11/9/2019 11:02:00 AM', 'American Airlines', '546', 'B8', '4'],
     ['A', '11/9/2019 10:06:00 AM', 'American Airlines', '1633', '', '4'],
     ['D', '11/9/2019 11:05:00 AM', 'American Airlines', '531', '', '4'],
     ['A', '11/9/2019 10:04:00 AM', 'American Airlines', '439', 'A3', '4'],
     ['D', '11/9/2019 11:05:00 AM', 'American Airlines', '1844', 'A11', '4'],
     ['D', '11/9/2019 11:05:00 AM', 'Southwest Airlines', '4027', 'C18', '4'],
     ['A', '11/9/2019 10:07:00 AM', 'American Airlines', '442', 'A18', '4'],
     ['D', '11/9/2019 11:07:00 AM', 'American Airlines', '524', 'A12', '4'],
     ['A', '11/9/2019 10:11:00 AM', 'American Airlines', '2176', 'A14', '4'],
     ['D', '11/9/2019 11:13:00 AM', 'American Airlines', '493', 'A14', '4'],
     ['D', '11/9/2019 11:15:00 AM', 'American Airlines', '5890', 'B17', '4'],
     ['D', '11/9/2019 11:15:00 AM', 'Sun Country Airlines', '604', 'F2', '3'],
     ['A', '11/9/2019 9:38:00 AM', 'American Airlines', '455', 'A2', '4'],
     ['D', '11/9/2019 11:20:00 AM', 'American Airlines', '534', 'A3', '4'],
     ['D', '11/9/2019 11:22:00 AM', 'United Airlines', '5435', '3', '2'],
     ['A', '11/9/2019 10:12:00 AM', 'American Airlines', '1040', 'A12', '4'],
     ['D', '11/9/2019 11:23:00 AM', 'American Airlines', '2671', 'A4', '4'],
     ['A', '11/9/2019 10:22:00 AM', 'American Airlines', '1983', 'A4', '4'],
     ['D', '11/9/2019 11:24:00 AM', 'American Airlines', '1971', 'B6', '4'],
     ['D', '11/9/2019 11:25:00 AM', 'Delta Air Lines', '5700', 'F3', '3'],
     ['D', '11/9/2019 11:25:00 AM', 'Southwest Airlines', '5083', 'C3', '4'],
     ['A', '11/9/2019 10:23:00 AM', 'American Airlines', '2260', 'A30', '4'],
     ['D', '11/9/2019 11:27:00 AM', 'American Airlines', '1141', 'A30', '4'],
     ['A', '11/9/2019 10:25:00 AM', 'American Airlines', '1678', 'A17', '4'],
     ['D', '11/9/2019 11:28:00 AM', 'American Airlines', '873', 'A5', '4'],
     ['D', '11/9/2019 11:28:00 AM', 'American Airlines', '3014', 'B21', '4'],
     ['A', '11/9/2019 10:24:00 AM', 'American Airlines', '1552', 'B14', '4'],
     ['D', '11/9/2019 11:30:00 AM', 'American Airlines', '629', 'B14', '4'],
     ['A', '11/9/2019 10:14:00 AM', 'American Airlines', '1672', 'A6', '4'],
     ['D', '11/9/2019 11:30:00 AM', 'American Airlines', '1802', 'A29', '4'],
     ['D', '11/9/2019 11:30:00 AM', 'Southwest Airlines', '6676', 'D4', '4'],
     ['D', '11/9/2019 11:32:00 AM', 'Delta Air Lines', '2354', 'F1', '3'],
     ['A', '11/9/2019 7:42:00 AM', 'American Airlines', '664', 'A24', '4'],
     ['D', '11/9/2019 11:35:00 AM', 'American Airlines', '432', 'A24', '4'],
     ['D', '11/9/2019 11:35:00 AM', 'American Airlines', '3015', 'A1', '4'],
     ['A', '11/9/2019 10:00:00 AM', 'American Airlines', '767', 'A23', '4'],
     ['D', '11/9/2019 11:36:00 AM', 'American Airlines', '692', 'A23', '4'],
     ['A', '11/9/2019 10:48:00 AM', 'American Airlines', '520', 'A19', '4'],
     ['D', '11/9/2019 11:43:00 AM', 'American Airlines', '710', 'A19', '4'],
     ['D', '11/9/2019 11:45:00 AM', 'Southwest Airlines', '5636', 'C19', '4'],
     ['D', '11/9/2019 11:45:00 AM', 'United Airlines', '237', '5', '2'],
     ['D', '11/9/2019 11:45:00 AM', 'United Airlines', '1552', '8', '2'],
     ['A', '11/9/2019 10:07:00 AM', 'American Airlines', '572', 'A8', '4'],
     ['D', '11/9/2019 11:55:00 AM', 'American Airlines', '1885', 'B4', '4'],
     ['A', '11/9/2019 11:10:00 AM', 'WestJet', '1532', 'B27', '4'],
     ['D', '11/9/2019 12:05:00 PM', 'WestJet', '1463', 'B27', '4'],
     ['D', '11/9/2019 12:05:00 PM', 'Southwest Airlines', '4753', 'C16', '4'],
     ['D', '11/9/2019 12:05:00 PM', 'Southwest Airlines', '2718', 'D6', '4'],
     ['D', '11/9/2019 12:07:00 PM', 'American Airlines', '5894', 'B12', '4'],
     ['D', '11/9/2019 12:11:00 PM', 'Delta Air Lines', '1940', 'F4', '3'],
     ['D', '11/9/2019 12:15:00 PM', 'Delta Air Lines', '1918', 'F5', '3'],
     ['D', '11/9/2019 12:26:00 PM', 'United Airlines', '523', '7', '2'],
     ['A', '11/9/2019 11:27:00 AM', 'Air Canada', '1890', 'B26', '4'],
     ['D', '11/9/2019 12:30:00 PM', 'Air Canada', '1891', 'B26', '4'],
     ['D', '11/9/2019 12:30:00 PM', 'Alaska Airlines', '1921', '10', '2'],
     ['D', '11/9/2019 12:30:00 PM', 'Southwest Airlines', '4601', 'C1', '4'],
     ['D', '11/9/2019 12:32:00 PM', 'Delta Air Lines', '1874', 'F6', '3'],
     ['D', '11/9/2019 12:37:00 PM', 'Delta Air Lines', '2154', 'F8', '3'],
     ['D', '11/9/2019 12:40:00 PM', 'Southwest Airlines', '4531', 'C18', '4'],
     ['D', '11/9/2019 12:40:00 PM', 'Southwest Airlines', '3885', 'C13', '4'],
     ['D', '11/9/2019 12:45:00 PM', 'Southwest Airlines', '5515', 'C6', '4'],
     ['D', '11/9/2019 12:45:00 PM', 'Southwest Airlines', '3871', 'C7', '4'],
     ['D', '11/9/2019 12:50:00 PM', 'Southwest Airlines', '2629', 'D4', '4'],
     ['D', '11/9/2019 12:50:00 PM', 'Southwest Airlines', '3805', 'C9', '4'],
     ['D', '11/9/2019 12:50:00 PM', 'Southwest Airlines', '33', 'D5', '4'],
     ['D', '11/9/2019 12:50:00 PM', 'Southwest Airlines', '5559', 'D7', '4'],
     ['D', '11/9/2019 12:50:00 PM', 'Southwest Airlines', '3642', 'C3', '4'],
     ['D', '11/9/2019 12:52:00 PM', 'United Airlines', '2135', '6A', '2'],
     ['A', '11/9/2019 11:44:00 AM', 'Air Canada', '1837', 'B28', '4'],
     ['D', '11/9/2019 12:55:00 PM', 'Air Canada', '1836', 'B28', '4'],
     ['D', '11/9/2019 1:00:00 PM', 'Southwest Airlines', '3387', 'C11', '4'],
     ['D', '11/9/2019 1:00:00 PM', 'Southwest Airlines', '5074', 'D2', '4'],
     ['A', '11/9/2019 12:01:00 PM', 'American Airlines', '384', 'A19', '4'],
     ['D', '11/9/2019 1:05:00 PM', 'American Airlines', '727', 'A13', '4'],
     ['A', '11/9/2019 11:25:00 AM', 'American Airlines', '876', 'B13', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'American Airlines', '615', 'B13', '4'],
     ['A', '11/9/2019 12:04:00 PM', 'American Airlines', '486', 'A10', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'American Airlines', '2450', 'A10', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'American Airlines', '3021', 'A1', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'American Airlines', '5775', 'B19', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'Southwest Airlines', '5414', 'D6', '4'],
     ['D', '11/9/2019 1:10:00 PM', 'Southwest Airlines', '5093', 'C8', '4'],
     ['D', '11/9/2019 1:11:00 PM', 'American Airlines', '5940', 'B21', '4'],
     ['A', '11/9/2019 12:21:00 PM', 'American Airlines', '1863', 'A12', '4'],
     ['D', '11/9/2019 1:14:00 PM', 'American Airlines', '1760', 'A12', '4'],
     ['D', '11/9/2019 1:14:00 PM', 'American Airlines', '5730', 'B12', '4'],
     ['D', '11/9/2019 1:15:00 PM', 'Advanced Air', '213', 'FBO 01', '2'],
     ['D', '11/9/2019 1:15:00 PM', 'Southwest Airlines', '5045', 'C17', '4'],
     ['A', '11/9/2019 12:14:00 PM', 'American Airlines', '474', 'A26', '4'],
     ['D', '11/9/2019 1:17:00 PM', 'American Airlines', '460', 'A19', '4'],
     ['A', '11/9/2019 10:27:00 AM', 'American Airlines', '499', 'A22', '4'],
     ['D', '11/9/2019 1:20:00 PM', 'American Airlines', '1634', 'A22', '4'],
     ['D', '11/9/2019 1:20:00 PM', 'Alaska Airlines', '607', '11', '2'],
     ['D', '11/9/2019 1:20:00 PM', 'Delta Air Lines', '320', 'F9', '3'],
     ['D', '11/9/2019 1:20:00 PM', 'Southwest Airlines', '608', 'D8', '4'],
     ['A', '11/9/2019 11:53:00 AM', 'American Airlines', '1402', 'A4', '4'],
     ['D', '11/9/2019 1:21:00 PM', 'American Airlines', '462', 'A27', '4'],
     ['A', '11/9/2019 12:10:00 PM', 'American Airlines', '1868', 'A13', '4'],
     ['D', '11/9/2019 1:21:00 PM', 'American Airlines', '1743', 'A26', '4'],
     ['A', '11/9/2019 12:18:00 PM', 'American Airlines', '1298', 'B10', '4'],
     ['D', '11/9/2019 1:22:00 PM', 'American Airlines', '570', 'B10', '4'],
     ['A', '11/9/2019 12:37:00 PM', 'Air Canada', '1056', 'B24', '4'],
     ['D', '11/9/2019 1:25:00 PM', 'Air Canada', '1057', 'B24', '4'],
     ['A', '11/9/2019 12:27:00 PM', 'American Airlines', '1831', 'B6', '4'],
     ['D', '11/9/2019 1:30:00 PM', 'American Airlines', '1777', 'B6', '4'],
     ['A', '11/9/2019 12:26:00 PM', 'American Airlines', '1465', 'A27', '4'],
     ['D', '11/9/2019 1:34:00 PM', 'American Airlines', '2434', 'A4', '4'],
     ['D', '11/9/2019 1:35:00 PM', 'Southwest Airlines', '4771', 'C1', '4'],
     ['D', '11/9/2019 1:38:00 PM', 'American Airlines', '5754', 'B7', '4'],
     ['A', '11/9/2019 12:35:00 PM', 'American Airlines', '1798', 'A20', '4'],
     ['D', '11/9/2019 1:40:00 PM', 'American Airlines', '1796', 'A20', '4'],
     ['A', '11/9/2019 12:42:00 PM', 'American Airlines', '2105', 'A8', '4'],
     ['D', '11/9/2019 1:41:00 PM', 'American Airlines', '678', 'A9', '4'],
     ['A', '11/9/2019 12:59:00 PM', 'American Airlines', '742', 'A25', '4'],
     ['D', '11/9/2019 1:50:00 PM', 'American Airlines', '747', 'A8', '4'],
     ['D', '11/9/2019 1:50:00 PM', 'Southwest Airlines', '3836', 'C19', '4'],
     ['A', '11/9/2019 12:51:00 PM', 'WestJet', '1402', 'B27', '4'],
     ['D', '11/9/2019 1:50:00 PM', 'WestJet', '1403', 'B27', '4'],
     ['A', '11/9/2019 12:43:00 PM', 'American Airlines', '711', 'B4', '4'],
     ['D', '11/9/2019 1:52:00 PM', 'American Airlines', '417', 'B4', '4'],
     ['A', '11/9/2019 12:41:00 PM', 'American Airlines', '690', 'A3', '4'],
     ['D', '11/9/2019 1:52:00 PM', 'American Airlines', '2320', 'A11', '4'],
     ['A', '11/9/2019 11:38:00 AM', 'American Airlines', '445', 'A18', '4'],
     ['D', '11/9/2019 1:55:00 PM', 'American Airlines', '510', 'A18', '4'],
     ['D', '11/9/2019 1:55:00 PM', 'Southwest Airlines', '6486', 'C13', '4'],
     ['D', '11/9/2019 1:58:00 PM', 'Delta Air Lines', '1466', 'F10', '3'],
     ['A', '11/9/2019 1:14:00 PM', 'WestJet', '1770', 'B23', '4'],
     ['D', '11/9/2019 2:05:00 PM', 'WestJet', '1771', 'B23', '4'],
     ['D', '11/9/2019 2:10:00 PM', 'Southwest Airlines', '3762', 'C7', '4'],
     ['D', '11/9/2019 2:11:00 PM', 'Delta Air Lines', '5726', 'F5', '3'],
     ['D', '11/9/2019 2:28:00 PM', 'American Airlines', '5722', 'B21', '4'],
     ['D', '11/9/2019 2:30:00 PM', 'American Airlines', '3144', 'B3', '4'],
     ['D', '11/9/2019 2:30:00 PM', 'American Airlines', '3256', 'B22', '4'],
     ['D', '11/9/2019 2:30:00 PM', 'Southwest Airlines', '578', 'D4', '4'],
     ['A', '11/9/2019 1:20:00 PM', 'American Airlines', '2371', 'A23', '4'],
     ['D', '11/9/2019 2:33:00 PM', 'American Airlines', '542', 'A2', '4'],
     ['A', '11/9/2019 12:51:00 PM', 'American Airlines', '866', 'A11', '4'],
     ['D', '11/9/2019 2:34:00 PM', 'American Airlines', '556', 'A3', '4'],
     ['D', '11/9/2019 2:35:00 PM', 'American Airlines', '3134', 'B20', '4'],
     ['A', '11/9/2019 1:43:00 PM', 'Air Canada', '8054', 'B26', '4'],
     ['D', '11/9/2019 2:35:00 PM', 'Air Canada', '8053', 'B26', '4'],
     ['D', '11/9/2019 2:35:00 PM', 'Alaska Airlines', '931', '10', '2'],
     ['D', '11/9/2019 2:40:00 PM', 'Southwest Airlines', '6710', 'D7', '4'],
     ['D', '11/9/2019 2:42:00 PM', 'American Airlines', '5785', 'B12', '4'],
     ['D', '11/9/2019 2:45:00 PM', 'Southwest Airlines', '2965', 'D2', '4'],
     ['D', '11/9/2019 2:45:00 PM', 'Southwest Airlines', '6680', 'C17', '4'],
     ['D', '11/9/2019 2:48:00 PM', 'American Airlines', '3183', 'A1', '4'],
     ['D', '11/9/2019 2:48:00 PM', 'United Airlines', '1587', '7', '2'],
     ['A', '11/9/2019 1:51:00 PM', 'American Airlines', '562', 'B14', '4'],
     ['D', '11/9/2019 2:50:00 PM', 'American Airlines', '883', 'B14', '4'],
     ['A', '11/9/2019 2:00:00 PM', 'American Airlines', '476', 'B10', '4'],
     ['D', '11/9/2019 2:50:00 PM', 'American Airlines', '1200', 'B10', '4'],
     ['D', '11/9/2019 2:50:00 PM', 'Southwest Airlines', '3022', 'C16', '4'],
     ['D', '11/9/2019 2:50:00 PM', 'Southwest Airlines', '5514', 'C6', '4'],
     ['A', '11/9/2019 1:37:00 PM', 'American Airlines', '2229', 'A7', '4'],
     ['D', '11/9/2019 2:55:00 PM', 'American Airlines', '899', 'A25', '4'],
     ['D', '11/9/2019 3:00:00 PM', 'Delta Air Lines', '1002', 'F8', '3'],
     ['A', '11/9/2019 2:00:00 PM', 'American Airlines', '482', 'A26', '4'],
     ['D', '11/9/2019 3:04:00 PM', 'American Airlines', '2197', 'A23', '4'],
     ['D', '11/9/2019 3:04:00 PM', 'American Airlines', '5839', 'A1', '4'],
     ['D', '11/9/2019 3:10:00 PM', 'Southwest Airlines', '4560', 'C13', '4'],
     ['D', '11/9/2019 3:13:00 PM', 'American Airlines', '2989', 'B2', '4'],
     ['D', '11/9/2019 3:13:00 PM', 'United Airlines', '725', '3', '2'],
     ['D', '11/9/2019 3:15:00 PM', 'American Airlines', '5956', 'B7', '4'],
     ['D', '11/9/2019 3:15:00 PM', 'Delta Air Lines', '1255', 'F6', '3'],
     ['D', '11/9/2019 3:17:00 PM', 'American Airlines', '3265', 'B22', '4'],
     ['A', '11/9/2019 1:48:00 PM', 'American Airlines', '1897', 'A2', '4'],
     ['D', '11/9/2019 3:18:00 PM', 'American Airlines', '580', 'B6', '4'],
     ['D', '11/9/2019 3:20:00 PM', 'Southwest Airlines', '5349', 'D2', '4'],
     ['A', '11/9/2019 1:51:00 PM', 'American Airlines', '407', 'A19', '4'],
     ['D', '11/9/2019 3:21:00 PM', 'American Airlines', '694', 'A19', '4'],
     ['A', '11/9/2019 2:08:00 PM', 'American Airlines', '623', 'A24', '4'],
     ['D', '11/9/2019 3:25:00 PM', 'American Airlines', '491', 'A24', '4'],
     ['D', '11/9/2019 3:25:00 PM', 'Southwest Airlines', '2784', 'C9', '4'],
     ['A', '11/9/2019 2:13:00 PM', 'American Airlines', '1218', 'B6', '4'],
     ['D', '11/9/2019 3:29:00 PM', 'American Airlines', '1476', 'A26', '4'],
     ['D', '11/9/2019 3:30:00 PM', 'Southwest Airlines', '5644', 'C8', '4'],
     ['D', '11/9/2019 3:35:00 PM', 'Alaska Airlines', '2805', '11', '2'],
     ['D', '11/9/2019 3:37:00 PM', 'American Airlines', '3063', 'B20', '4'],
     ['A', '11/9/2019 2:30:00 PM', 'American Airlines', '524', 'A11', '4'],
     ['D', '11/9/2019 3:38:00 PM', 'American Airlines', '1147', 'A4', '4'],
     ['A', '11/9/2019 1:52:00 PM', 'American Airlines', '1970', 'A30', '4'],
     ['D', '11/9/2019 3:40:00 PM', 'American Airlines', '643', 'A7', '4'],
     ['A', '11/9/2019 2:52:00 PM', 'Frontier Airlines', '2150', 'F1', '3'],
     ['D', '11/9/2019 3:44:00 PM', 'Frontier Airlines', '2151', 'F1', '3'],
     ['D', '11/9/2019 3:50:00 PM', 'Southwest Airlines', '4461', 'C18', '4'],
     ['D', '11/9/2019 3:58:00 PM', 'American Airlines', '5972', 'B3', '4'],
     ['D', '11/9/2019 4:01:00 PM', 'American Airlines', '2063', 'B27', '4'],
     ['D', '11/9/2019 4:05:00 PM', 'Southwest Airlines', '4001', 'C11', '4'],
     ['D', '11/9/2019 4:05:00 PM', 'Southwest Airlines', '6330', 'C1', '4'],
     ['D', '11/9/2019 4:05:00 PM', 'Southwest Airlines', '5802', 'C9', '4'],
     ['D', '11/9/2019 4:06:00 PM', 'United Airlines', '756', '5', '2'],
     ['A', '11/9/2019 1:55:00 PM', 'American Airlines', '195', 'B25', '4'],
     ['D', '11/9/2019 4:10:00 PM', 'American Airlines', '194', 'B25', '4'],
     ['D', '11/9/2019 4:10:00 PM', 'American Airlines', '2988', 'B22', '4'],
     ['D', '11/9/2019 4:10:00 PM', 'American Airlines', '3022', 'B3', '4'],
     ['A', '11/9/2019 2:37:00 PM', 'American Airlines', '2369', 'A4', '4'],
     ['D', '11/9/2019 4:15:00 PM', 'American Airlines', '155', 'B4', '4'],
     ['D', '11/9/2019 4:15:00 PM', 'American Airlines', '3074', 'B1', '4'],
     ['D', '11/9/2019 4:19:00 PM', 'American Airlines', '3081', 'B2', '4'],
     ['A', '11/9/2019 2:37:00 PM', 'American Airlines', '2259', 'A20', '4'],
     ['D', '11/9/2019 4:20:00 PM', 'American Airlines', '981', 'A20', '4'],
     ['A', '11/9/2019 2:58:00 PM', 'American Airlines', '2671', 'A13', '4'],
     ['D', '11/9/2019 4:20:00 PM', 'American Airlines', '2319', 'A5', '4'],
     ['D', '11/9/2019 4:25:00 PM', 'Southwest Airlines', '3612', 'D6', '4'],
     ['A', '11/9/2019 2:32:00 PM', 'American Airlines', '777', '', '4'],
     ['D', '11/9/2019 4:27:00 PM', 'American Airlines', '2408', 'A30', '4'],
     ['D', '11/9/2019 4:30:00 PM', 'Alaska Airlines', '691', '10', '2'],
     ['D', '11/9/2019 4:30:00 PM', 'Southwest Airlines', '6540', 'C9', '4'],
     ['A', '11/9/2019 3:30:00 PM', 'American Airlines', '695', 'A10', '4'],
     ['D', '11/9/2019 4:31:00 PM', 'American Airlines', '492', 'A21', '4'],
     ['A', '11/9/2019 3:28:00 PM', 'American Airlines', '652', 'B10', '4'],
     ['D', '11/9/2019 4:33:00 PM', 'American Airlines', '2763', 'B10', '4'],
     ['A', '11/9/2019 3:32:00 PM', 'American Airlines', '765', 'A18', '4'],
     ['D', '11/9/2019 4:35:00 PM', 'American Airlines', '891', 'A18', '4'],
     ['D', '11/9/2019 4:35:00 PM', 'Southwest Airlines', '6760', 'C3', '4'],
     ['D', '11/9/2019 4:40:00 PM', 'Southwest Airlines', '4011', 'C6', '4'],
     ['A', '11/9/2019 3:30:00 PM', 'American Airlines', '546', 'B8', '4'],
     ['D', '11/9/2019 4:41:00 PM', 'American Airlines', '791', 'B8', '4'],
     ['D', '11/9/2019 4:45:00 PM', 'Delta Air Lines', '1839', 'F7', '3'],
     ['D', '11/9/2019 4:45:00 PM', 'Southwest Airlines', '3856', 'D7', '4'],
     ['D', '11/9/2019 4:46:00 PM', 'American Airlines', '5746', 'B7', '4'],
     ['A', '11/9/2019 3:35:00 PM', 'American Airlines', '1880', 'A3', '4'],
     ['D', '11/9/2019 4:47:00 PM', 'American Airlines', '604', 'A3', '4'],
     ['A', '11/9/2019 3:47:00 PM', 'American Airlines', '2083', 'A12', '4'],
     ['D', '11/9/2019 4:50:00 PM', 'American Airlines', '1689', 'A12', '4'],
     ['D', '11/9/2019 4:50:00 PM', 'Southwest Airlines', '6633', 'C17', '4'],
     ['D', '11/9/2019 4:51:00 PM', 'United Airlines', '2203', '7', '2'],
     ['A', '11/9/2019 3:34:00 PM', 'American Airlines', '2722', 'A5', '4'],
     ['D', '11/9/2019 4:56:00 PM', 'American Airlines', '1388', 'A13', '4'],
     ['D', '11/9/2019 5:00:00 PM', 'Delta Air Lines', '2206', 'F10', '3'],
     ['D', '11/9/2019 5:00:00 PM', 'Southwest Airlines', '3821', 'D8', '4'],
     ['D', '11/9/2019 5:05:00 PM', 'Southwest Airlines', '510', 'C18', '4'],
     ['D', '11/9/2019 5:10:00 PM', 'Southwest Airlines', '2651', 'C19', '4'],
     ['D', '11/9/2019 5:15:00 PM', 'Southwest Airlines', '4962', 'D8', '4'],
     ['D', '11/9/2019 5:15:00 PM', 'Southwest Airlines', '6122', 'D5', '4'],
     ['D', '11/9/2019 5:15:00 PM', 'Southwest Airlines', '4718', 'C13', '4'],
     ['D', '11/9/2019 5:17:00 PM', 'American Airlines', '5801', 'B17', '4'],
     ['A', '11/9/2019 3:35:00 PM', 'American Airlines', '475', 'A22', '4'],
     ['D', '11/9/2019 5:20:00 PM', 'American Airlines', '478', 'A22', '4'],
     ['D', '11/9/2019 5:20:00 PM', 'Southwest Airlines', '3779', 'D4', '4'],
     ...]




```python
# Converts the AirportFlightData list into a dataframe

AirportFlightData = pd.DataFrame(AirportFlightData) 

# Adds the first row of the dataframe into the header and drops the first row

AirportFlightData = AirportFlightData.rename(columns=AirportFlightData.iloc[0]).drop(AirportFlightData.index[0])

# Removes the spaces in the header columns

AirportFlightData.columns = AirportFlightData.columns.str.replace(' ', '')

# Shows the first 5 rows

AirportFlightData.head()

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
      <th>ArriveOrDepart</th>
      <th>Schedule</th>
      <th>Airline</th>
      <th>FlightNumber</th>
      <th>Gate</th>
      <th>Terminal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>D</td>
      <td>11/1/2019 8:00:00 AM</td>
      <td>Hawaiian Airlines</td>
      <td>35</td>
      <td>F10A</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>D</td>
      <td>11/1/2019 7:00:00 AM</td>
      <td>WestJet</td>
      <td>1499</td>
      <td>B23</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>11/1/2019 6:01:00 AM</td>
      <td>Delta Air Lines</td>
      <td>970</td>
      <td>F1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>D</td>
      <td>11/1/2019 12:35:00 AM</td>
      <td>Frontier Airlines</td>
      <td>1426</td>
      <td>F1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>D</td>
      <td>11/1/2019 6:02:00 AM</td>
      <td>Frontier Airlines</td>
      <td>2822</td>
      <td>F2</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Shows the number of All the column names, along with how many Non-null values there are, and the data type 

AirportFlightData.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 31981 entries, 1 to 31981
    Data columns (total 6 columns):
     #   Column          Non-Null Count  Dtype 
    ---  ------          --------------  ----- 
     0   ArriveOrDepart  31981 non-null  object
     1   Schedule        31980 non-null  object
     2   Airline         31980 non-null  object
     3   FlightNumber    31980 non-null  object
     4   Gate            31980 non-null  object
     5   Terminal        31980 non-null  object
    dtypes: object(6)
    memory usage: 1.7+ MB



```python
# Removes Duplicates

AirportFlightData = AirportFlightData.drop_duplicates(subset=None, inplace=False)

# Shows the number of All the column names, along with how many Non-null values there are, and the data type 

AirportFlightData.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 31629 entries, 1 to 31981
    Data columns (total 6 columns):
     #   Column          Non-Null Count  Dtype 
    ---  ------          --------------  ----- 
     0   ArriveOrDepart  31629 non-null  object
     1   Schedule        31628 non-null  object
     2   Airline         31628 non-null  object
     3   FlightNumber    31628 non-null  object
     4   Gate            31628 non-null  object
     5   Terminal        31628 non-null  object
    dtypes: object(6)
    memory usage: 1.7+ MB



```python
# Converts Schedule to datetime data type

AirportFlightData[["Schedule"]] = AirportFlightData[["Schedule"]].apply(pd.to_datetime)

```


```python
# Splits the Schedule into TripDate and TripTime columns
    
AirportFlightData[['TripDate','TripTime']] = AirportFlightData.Schedule.apply( 
   lambda x: pd.Series(str(x).split(" "))) 

```


```python
# Displays the data

AirportFlightData

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
      <th>ArriveOrDepart</th>
      <th>Schedule</th>
      <th>Airline</th>
      <th>FlightNumber</th>
      <th>Gate</th>
      <th>Terminal</th>
      <th>TripDate</th>
      <th>TripTime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>D</td>
      <td>2019-11-01 08:00:00</td>
      <td>Hawaiian Airlines</td>
      <td>35</td>
      <td>F10A</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>08:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>D</td>
      <td>2019-11-01 07:00:00</td>
      <td>WestJet</td>
      <td>1499</td>
      <td>B23</td>
      <td>4</td>
      <td>2019-11-01</td>
      <td>07:00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>2019-11-01 06:01:00</td>
      <td>Delta Air Lines</td>
      <td>970</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:01:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>D</td>
      <td>2019-11-01 00:35:00</td>
      <td>Frontier Airlines</td>
      <td>1426</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>00:35:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>D</td>
      <td>2019-11-01 06:02:00</td>
      <td>Frontier Airlines</td>
      <td>2822</td>
      <td>F2</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:02:00</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>31977</th>
      <td>A</td>
      <td>2019-11-08 20:26:00</td>
      <td>Delta Air Lines</td>
      <td>2988</td>
      <td>F2</td>
      <td>3</td>
      <td>2019-11-08</td>
      <td>20:26:00</td>
    </tr>
    <tr>
      <th>31978</th>
      <td>A</td>
      <td>2019-11-08 20:28:00</td>
      <td>American Airlines</td>
      <td>5887</td>
      <td>B1</td>
      <td>4</td>
      <td>2019-11-08</td>
      <td>20:28:00</td>
    </tr>
    <tr>
      <th>31979</th>
      <td>A</td>
      <td>2019-11-08 20:29:00</td>
      <td>American Airlines</td>
      <td>2958</td>
      <td>B2</td>
      <td>4</td>
      <td>2019-11-08</td>
      <td>20:29:00</td>
    </tr>
    <tr>
      <th>31980</th>
      <td>A</td>
      <td>2019-11-08 20:30:00</td>
      <td>Southwest Airlines</td>
      <td>4059</td>
      <td>D7</td>
      <td>4</td>
      <td>2019-11-08</td>
      <td>20:30:00</td>
    </tr>
    <tr>
      <th>31981</th>
      <td></td>
      <td>NaT</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>31629 rows × 8 columns</p>
</div>




```python
# Removes any TripDate that is not '2019-11-01'

AirportFlightData = AirportFlightData[AirportFlightData.TripDate == '2019-11-01']

```


```python
# Displays the data

AirportFlightData

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
      <th>ArriveOrDepart</th>
      <th>Schedule</th>
      <th>Airline</th>
      <th>FlightNumber</th>
      <th>Gate</th>
      <th>Terminal</th>
      <th>TripDate</th>
      <th>TripTime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>D</td>
      <td>2019-11-01 08:00:00</td>
      <td>Hawaiian Airlines</td>
      <td>35</td>
      <td>F10A</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>08:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>D</td>
      <td>2019-11-01 07:00:00</td>
      <td>WestJet</td>
      <td>1499</td>
      <td>B23</td>
      <td>4</td>
      <td>2019-11-01</td>
      <td>07:00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>2019-11-01 06:01:00</td>
      <td>Delta Air Lines</td>
      <td>970</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:01:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>D</td>
      <td>2019-11-01 00:35:00</td>
      <td>Frontier Airlines</td>
      <td>1426</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>00:35:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>D</td>
      <td>2019-11-01 06:02:00</td>
      <td>Frontier Airlines</td>
      <td>2822</td>
      <td>F2</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:02:00</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>16633</th>
      <td>A</td>
      <td>2019-11-01 10:15:00</td>
      <td>Contour Airlines</td>
      <td>6303</td>
      <td>2</td>
      <td>2</td>
      <td>2019-11-01</td>
      <td>10:15:00</td>
    </tr>
    <tr>
      <th>16635</th>
      <td>A</td>
      <td>2019-11-01 23:15:00</td>
      <td>Delta Air Lines</td>
      <td>4196</td>
      <td>F5</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>23:15:00</td>
    </tr>
    <tr>
      <th>16637</th>
      <td>A</td>
      <td>2019-11-01 23:34:00</td>
      <td>Air Canada</td>
      <td>7015</td>
      <td>FBO 03</td>
      <td>4</td>
      <td>2019-11-01</td>
      <td>23:34:00</td>
    </tr>
    <tr>
      <th>16639</th>
      <td>A</td>
      <td>2019-11-01 23:12:00</td>
      <td>Delta Air Lines</td>
      <td>3262</td>
      <td>CX</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>23:12:00</td>
    </tr>
    <tr>
      <th>16640</th>
      <td>A</td>
      <td>2019-11-01 22:55:00</td>
      <td>Delta Air Lines</td>
      <td>4196</td>
      <td>F5</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>22:55:00</td>
    </tr>
  </tbody>
</table>
<p>1060 rows × 8 columns</p>
</div>




```python
# Ignores Warnings

import warnings

warnings.filterwarnings("ignore")
    
```


```python
# Creates columns and adds a '1' if the criteria is met for each row

AirportFlightData.loc[AirportFlightData['ArriveOrDepart'] == 'A', 'Arrival'] = 1
AirportFlightData.loc[AirportFlightData['ArriveOrDepart'] == 'D', 'Departure'] = 1

```


```python
# Renames the Teminal Column

AirportFlightData.rename(columns={'Terminal':'Terminall'}, inplace=True)

# Shows the data

AirportFlightData

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
      <th>ArriveOrDepart</th>
      <th>Schedule</th>
      <th>Airline</th>
      <th>FlightNumber</th>
      <th>Gate</th>
      <th>Terminall</th>
      <th>TripDate</th>
      <th>TripTime</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>D</td>
      <td>2019-11-01 08:00:00</td>
      <td>Hawaiian Airlines</td>
      <td>35</td>
      <td>F10A</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>08:00:00</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>D</td>
      <td>2019-11-01 07:00:00</td>
      <td>WestJet</td>
      <td>1499</td>
      <td>B23</td>
      <td>4</td>
      <td>2019-11-01</td>
      <td>07:00:00</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>2019-11-01 06:01:00</td>
      <td>Delta Air Lines</td>
      <td>970</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:01:00</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>D</td>
      <td>2019-11-01 00:35:00</td>
      <td>Frontier Airlines</td>
      <td>1426</td>
      <td>F1</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>00:35:00</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>D</td>
      <td>2019-11-01 06:02:00</td>
      <td>Frontier Airlines</td>
      <td>2822</td>
      <td>F2</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>06:02:00</td>
      <td>NaN</td>
      <td>1.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>16633</th>
      <td>A</td>
      <td>2019-11-01 10:15:00</td>
      <td>Contour Airlines</td>
      <td>6303</td>
      <td>2</td>
      <td>2</td>
      <td>2019-11-01</td>
      <td>10:15:00</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16635</th>
      <td>A</td>
      <td>2019-11-01 23:15:00</td>
      <td>Delta Air Lines</td>
      <td>4196</td>
      <td>F5</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>23:15:00</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16637</th>
      <td>A</td>
      <td>2019-11-01 23:34:00</td>
      <td>Air Canada</td>
      <td>7015</td>
      <td>FBO 03</td>
      <td>4</td>
      <td>2019-11-01</td>
      <td>23:34:00</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16639</th>
      <td>A</td>
      <td>2019-11-01 23:12:00</td>
      <td>Delta Air Lines</td>
      <td>3262</td>
      <td>CX</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>23:12:00</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16640</th>
      <td>A</td>
      <td>2019-11-01 22:55:00</td>
      <td>Delta Air Lines</td>
      <td>4196</td>
      <td>F5</td>
      <td>3</td>
      <td>2019-11-01</td>
      <td>22:55:00</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>1060 rows × 10 columns</p>
</div>




```python
# Groups, Aggregates, and Sums certain fields

AirportFlightCnts = AirportFlightData[['TripDate', 'Airline', 'Terminall', 'Arrival', 'Departure']].groupby(['TripDate', 'Airline', 'Terminall']).agg(['sum'])

# Displays the data

AirportFlightCnts

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th>sum</th>
      <th>sum</th>
    </tr>
    <tr>
      <th>TripDate</th>
      <th>Airline</th>
      <th>Terminall</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="16" valign="top">2019-11-01</th>
      <th>Advanced Air</th>
      <th>2</th>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>Air Canada</th>
      <th>4</th>
      <td>6.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>Alaska Airlines</th>
      <th>2</th>
      <td>9.0</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>American Airlines</th>
      <th>4</th>
      <td>252.0</td>
      <td>256.0</td>
    </tr>
    <tr>
      <th>British Airways</th>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Contour Airlines</th>
      <th>2</th>
      <td>3.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>Delta Air Lines</th>
      <th>3</th>
      <td>28.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>Frontier Airlines</th>
      <th>3</th>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>Hawaiian Airlines</th>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>JetBlue Airways</th>
      <th>3</th>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>Southwest Airlines</th>
      <th>4</th>
      <td>173.0</td>
      <td>174.0</td>
    </tr>
    <tr>
      <th>Spirit Airlines</th>
      <th>3</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Sun Country Airlines</th>
      <th>3</th>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>United Airlines</th>
      <th>2</th>
      <td>29.0</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>Volaris</th>
      <th>4</th>
      <td>2.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>WestJet</th>
      <th>4</th>
      <td>9.0</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Adds the TripDate to each row

AirportFlightCnts = AirportFlightCnts.reset_index()

# Only shows the first header

AirportFlightCnts.columns = AirportFlightCnts.columns.get_level_values(0)

# Prints the data

AirportFlightCnts

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
      <th>TripDate</th>
      <th>Airline</th>
      <th>Terminall</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-11-01</td>
      <td>Advanced Air</td>
      <td>2</td>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-11-01</td>
      <td>Air Canada</td>
      <td>4</td>
      <td>6.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-11-01</td>
      <td>Alaska Airlines</td>
      <td>2</td>
      <td>9.0</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-11-01</td>
      <td>American Airlines</td>
      <td>4</td>
      <td>252.0</td>
      <td>256.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-11-01</td>
      <td>British Airways</td>
      <td>4</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2019-11-01</td>
      <td>Contour Airlines</td>
      <td>2</td>
      <td>3.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2019-11-01</td>
      <td>Delta Air Lines</td>
      <td>3</td>
      <td>28.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2019-11-01</td>
      <td>Frontier Airlines</td>
      <td>3</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2019-11-01</td>
      <td>Hawaiian Airlines</td>
      <td>3</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2019-11-01</td>
      <td>JetBlue Airways</td>
      <td>3</td>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2019-11-01</td>
      <td>Southwest Airlines</td>
      <td>4</td>
      <td>173.0</td>
      <td>174.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2019-11-01</td>
      <td>Spirit Airlines</td>
      <td>3</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2019-11-01</td>
      <td>Sun Country Airlines</td>
      <td>3</td>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2019-11-01</td>
      <td>United Airlines</td>
      <td>2</td>
      <td>29.0</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2019-11-01</td>
      <td>Volaris</td>
      <td>4</td>
      <td>2.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2019-11-01</td>
      <td>WestJet</td>
      <td>4</td>
      <td>9.0</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Groups, Aggregates, and Sums certain fields

AirportFlightCntsWOAirlines = AirportFlightData[['TripDate', 'Airline', 'Terminall', 'Arrival', 'Departure']].groupby(['TripDate', 'Terminall']).agg(['sum'])

# Displays the data

AirportFlightCntsWOAirlines

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th>sum</th>
      <th>sum</th>
      <th>sum</th>
    </tr>
    <tr>
      <th>TripDate</th>
      <th>Terminall</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">2019-11-01</th>
      <th>2</th>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Adds the TripDate to each row

AirportFlightCntsWOAirlines = AirportFlightCntsWOAirlines.reset_index()

# Only shows the first header

AirportFlightCntsWOAirlines.columns = AirportFlightCntsWOAirlines.columns.get_level_values(0)

# Prints the data

AirportFlightCntsWOAirlines

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
      <th>TripDate</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-11-01</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-11-01</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-11-01</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Import Packages --- READING FROM AN API

import urllib
import json
import pandas as pd
import requests

apikey = '10bc74ab-8008-4357-be93-ccf045b57379'

# Opens the link
webUrl  = urllib.request.urlopen("https://www.phoenixopendata.com/api/3/action/datastore_search?resource_id=" + apikey + '&limit=100000')

# Loads the data from webUrl

data = json.load(webUrl)

data

# Writes max 100,000 records to a txt file

with open('test.txt', 'w') as json_file:
  json.dump(data, json_file)

```


```python
# After modifying the test.txt file to APIreformatted.txt, and then to APIdataReformat.xlsx, reads the APIdataReformat.xlsx file
    
AirportTransportationData = pd.read_excel(r'APIdataReformat.xlsx')

# Displays the first 5 records and last 5 records
    
AirportTransportationData

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
      <th>IDD</th>
      <th>OperatorType</th>
      <th>TripStartTime</th>
      <th>TripEndTime</th>
      <th>Duration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:10</td>
      <td>2019-11-01T00:00:10</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:11</td>
      <td>2019-11-01T00:00:11</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:58</td>
      <td>2019-11-01T00:00:58</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T00:01:26</td>
      <td>2019-11-01T00:17:38</td>
      <td>00:16:12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T00:01:35</td>
      <td>2019-11-01T00:02:54</td>
      <td>00:01:19</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2971</th>
      <td>2966</td>
      <td>Taxi</td>
      <td>2019-11-01T23:55:57</td>
      <td>2019-11-01T23:55:57</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>2972</th>
      <td>2967</td>
      <td>Taxi</td>
      <td>2019-11-01T23:56:00</td>
      <td>2019-11-01T23:56:00</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>2973</th>
      <td>2968</td>
      <td>Taxi</td>
      <td>2019-11-01T23:58:06</td>
      <td>2019-11-01T23:58:06</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>2974</th>
      <td>2969</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:58:25</td>
      <td>2019-11-02T00:00:33</td>
      <td>00:02:08</td>
    </tr>
    <tr>
      <th>2975</th>
      <td>2970</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:59:30</td>
      <td>2019-11-02T00:03:14</td>
      <td>00:03:44</td>
    </tr>
  </tbody>
</table>
<p>2976 rows × 5 columns</p>
</div>




```python
# Shows the number of All the column names, along with how many Non-null values there are, and the data type 

AirportTransportationData.info()

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2976 entries, 0 to 2975
    Data columns (total 5 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   IDD            2976 non-null   int64 
     1   OperatorType   2976 non-null   object
     2   TripStartTime  2976 non-null   object
     3   TripEndTime    2976 non-null   object
     4   Duration       2976 non-null   object
    dtypes: int64(1), object(4)
    memory usage: 116.4+ KB



```python
# Removes Duplicates

AirportTransportationData = AirportTransportationData.drop_duplicates(subset=None, inplace=False)

AirportTransportationData.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 2976 entries, 0 to 2975
    Data columns (total 5 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   IDD            2976 non-null   int64 
     1   OperatorType   2976 non-null   object
     2   TripStartTime  2976 non-null   object
     3   TripEndTime    2976 non-null   object
     4   Duration       2976 non-null   object
    dtypes: int64(1), object(4)
    memory usage: 139.5+ KB



```python
# Splits the TripStartTime and TripEndTime into separate columns

AirportTransportationData[['TripStrtDate','TripStrtTime']] = AirportTransportationData.TripStartTime.apply( 
   lambda x: pd.Series(str(x).split("T"))) 

AirportTransportationData[['TripEnddDate','TripEnddTime']] = AirportTransportationData.TripEndTime.apply( 
   lambda x: pd.Series(str(x).split("T"))) 

AirportTransportationData[['DurationHour','DurationMinute','DurationSeconds']] = AirportTransportationData.Duration.apply( 
   lambda x: pd.Series(str(x).split(":"))) 

# Shows the number of All the column names, along with how many Non-null values there are, and the data type 

AirportTransportationData.info()

```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 2976 entries, 0 to 2975
    Data columns (total 12 columns):
     #   Column           Non-Null Count  Dtype 
    ---  ------           --------------  ----- 
     0   IDD              2976 non-null   int64 
     1   OperatorType     2976 non-null   object
     2   TripStartTime    2976 non-null   object
     3   TripEndTime      2976 non-null   object
     4   Duration         2976 non-null   object
     5   TripStrtDate     2976 non-null   object
     6   TripStrtTime     2976 non-null   object
     7   TripEnddDate     2976 non-null   object
     8   TripEnddTime     2975 non-null   object
     9   DurationHour     2976 non-null   object
     10  DurationMinute   2975 non-null   object
     11  DurationSeconds  2975 non-null   object
    dtypes: int64(1), object(11)
    memory usage: 302.2+ KB



```python
# Converts the TripStrtDate, TripEnddTime, and TripStrtTime to a datetime

AirportTransportationData[["TripStrtDate"]] = AirportTransportationData[["TripStrtDate"]].apply(pd.to_datetime)

AirportTransportationData["TripEnddTime"]= pd.to_datetime(AirportTransportationData["TripEnddTime"]) 

AirportTransportationData["TripStrtTime"]= pd.to_datetime(AirportTransportationData["TripStrtTime"]) 

# Returns the first 5 rows

AirportTransportationData.head()

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
      <th>IDD</th>
      <th>OperatorType</th>
      <th>TripStartTime</th>
      <th>TripEndTime</th>
      <th>Duration</th>
      <th>TripStrtDate</th>
      <th>TripStrtTime</th>
      <th>TripEnddDate</th>
      <th>TripEnddTime</th>
      <th>DurationHour</th>
      <th>DurationMinute</th>
      <th>DurationSeconds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:10</td>
      <td>2019-11-01T00:00:10</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:10</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:10</td>
      <td>00</td>
      <td>00</td>
      <td>00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:11</td>
      <td>2019-11-01T00:00:11</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:11</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:11</td>
      <td>00</td>
      <td>00</td>
      <td>00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Taxi</td>
      <td>2019-11-01T00:00:58</td>
      <td>2019-11-01T00:00:58</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:58</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:00:58</td>
      <td>00</td>
      <td>00</td>
      <td>00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T00:01:26</td>
      <td>2019-11-01T00:17:38</td>
      <td>00:16:12</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:01:26</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:17:38</td>
      <td>00</td>
      <td>16</td>
      <td>12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T00:01:35</td>
      <td>2019-11-01T00:02:54</td>
      <td>00:01:19</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:01:35</td>
      <td>2019-11-01</td>
      <td>2021-03-27 00:02:54</td>
      <td>00</td>
      <td>01</td>
      <td>19</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Creates columns and adds a '1' if the criteria is met for each row

AirportTransportationData.loc[(AirportTransportationData['DurationMinute'] <= '15') & (AirportTransportationData['DurationHour'] == '00'), 'Min15orLess'] = 1

AirportTransportationData.loc[(AirportTransportationData['DurationMinute'] > '15') & (AirportTransportationData['DurationMinute'] <= '30') & (AirportTransportationData['DurationHour'] == '00'), 'Min16to30'] = 1

AirportTransportationData.loc[(AirportTransportationData['DurationMinute'] > '30') & (AirportTransportationData['DurationMinute'] <= '45') & (AirportTransportationData['DurationHour'] == '00'), 'Min31and45'] = 1

AirportTransportationData.loc[(AirportTransportationData['DurationMinute'] > '45') & (AirportTransportationData['DurationMinute'] <= '59') & (AirportTransportationData['DurationHour'] == '00'), 'Min46and59'] = 1

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '01', 'Hour1'] = 1

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '02', 'Hour2'] = 1

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '03', 'Hour3'] = 1                    

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '04', 'Hour4'] = 1 

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '05', 'Hour5'] = 1 

AirportTransportationData.loc[AirportTransportationData['DurationHour'] == '06', 'Hour6'] = 1 

AirportTransportationData.tail(50)

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
      <th>IDD</th>
      <th>OperatorType</th>
      <th>TripStartTime</th>
      <th>TripEndTime</th>
      <th>Duration</th>
      <th>TripStrtDate</th>
      <th>TripStrtTime</th>
      <th>TripEnddDate</th>
      <th>TripEnddTime</th>
      <th>DurationHour</th>
      <th>...</th>
      <th>Min15orLess</th>
      <th>Min16to30</th>
      <th>Min31and45</th>
      <th>Min46and59</th>
      <th>Hour1</th>
      <th>Hour2</th>
      <th>Hour3</th>
      <th>Hour4</th>
      <th>Hour5</th>
      <th>Hour6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2926</th>
      <td>2921</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01T23:18:36</td>
      <td>2019-11-01T23:21:57</td>
      <td>00:03:21</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:18:36</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:21:57</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2927</th>
      <td>2922</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:20:01</td>
      <td>2019-11-01T23:23:20</td>
      <td>00:03:19</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:20:01</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:23:20</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2928</th>
      <td>2923</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:20:24</td>
      <td>2019-11-01T23:22:23</td>
      <td>00:01:59</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:20:24</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:23</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2929</th>
      <td>2924</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:20:34</td>
      <td>2019-11-01T23:22:35</td>
      <td>00:02:01</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:20:34</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:35</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2930</th>
      <td>2925</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:21:48</td>
      <td>2019-11-01T23:23:21</td>
      <td>00:01:33</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:21:48</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:23:21</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2931</th>
      <td>2926</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:22:19</td>
      <td>2019-11-01T23:26:00</td>
      <td>00:03:41</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:19</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:00</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2932</th>
      <td>2927</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:22:30</td>
      <td>2019-11-01T23:24:47</td>
      <td>00:02:17</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:30</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:24:47</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2933</th>
      <td>2928</td>
      <td>Taxi</td>
      <td>2019-11-01T23:22:47</td>
      <td>2019-11-01T23:22:47</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:47</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:22:47</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2934</th>
      <td>2929</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:24:08</td>
      <td>2019-11-01T23:26:38</td>
      <td>00:02:30</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:24:08</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:38</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2935</th>
      <td>2930</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:24:34</td>
      <td>2019-11-01T23:26:20</td>
      <td>00:01:46</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:24:34</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:20</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2936</th>
      <td>2931</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:26:08</td>
      <td>2019-11-01T23:29:55</td>
      <td>00:03:47</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:08</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:29:55</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2937</th>
      <td>2932</td>
      <td>Taxi</td>
      <td>2019-11-01T23:26:40</td>
      <td>2019-11-01T23:26:40</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:40</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:26:40</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2938</th>
      <td>2933</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:27:06</td>
      <td>2019-11-01T23:29:45</td>
      <td>00:02:39</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:27:06</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:29:45</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2939</th>
      <td>2934</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:27:54</td>
      <td>2019-11-01T23:29:59</td>
      <td>00:02:05</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:27:54</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:29:59</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2940</th>
      <td>2935</td>
      <td>Taxi</td>
      <td>2019-11-01T23:28:07</td>
      <td>2019-11-01T23:28:07</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:28:07</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:28:07</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2941</th>
      <td>2936</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01T23:29:08</td>
      <td>2019-11-01T23:33:06</td>
      <td>00:03:58</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:29:08</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:33:06</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2942</th>
      <td>2937</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:29:44</td>
      <td>2019-11-01T23:34:36</td>
      <td>00:04:52</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:29:44</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:34:36</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2943</th>
      <td>2938</td>
      <td>Taxi</td>
      <td>2019-11-01T23:30:09</td>
      <td>2019-11-01T23:30:09</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:30:09</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:30:09</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2944</th>
      <td>2939</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:31:43</td>
      <td>2019-11-01T23:52:38</td>
      <td>00:20:55</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:31:43</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:52:38</td>
      <td>00</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2945</th>
      <td>2940</td>
      <td>Taxi</td>
      <td>2019-11-01T23:32:09</td>
      <td>2019-11-01T23:32:09</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:32:09</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:32:09</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2946</th>
      <td>2941</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:32:35</td>
      <td>2019-11-01T23:34:42</td>
      <td>00:02:07</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:32:35</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:34:42</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2947</th>
      <td>2942</td>
      <td>Taxi</td>
      <td>2019-11-01T23:35:09</td>
      <td>2019-11-01T23:35:09</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:35:09</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:35:09</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2948</th>
      <td>2943</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01T23:35:45</td>
      <td>2019-11-01T23:38:48</td>
      <td>00:03:03</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:35:45</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:38:48</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2949</th>
      <td>2944</td>
      <td>Taxi</td>
      <td>2019-11-01T23:35:57</td>
      <td>2019-11-01T23:35:57</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:35:57</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:35:57</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2950</th>
      <td>2945</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:40:35</td>
      <td>2019-11-01T23:42:41</td>
      <td>00:02:06</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:40:35</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:42:41</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2951</th>
      <td>2946</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:41:05</td>
      <td>2019-11-01T23:43:04</td>
      <td>00:01:59</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:41:05</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:43:04</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2952</th>
      <td>2947</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:41:14</td>
      <td>2019-11-01T23:55:19</td>
      <td>00:14:05</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:41:14</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:55:19</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2953</th>
      <td>2948</td>
      <td>Taxi</td>
      <td>2019-11-01T23:42:37</td>
      <td>2019-11-01T23:42:37</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:42:37</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:42:37</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2954</th>
      <td>2949</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:43:18</td>
      <td>2019-11-01T23:44:58</td>
      <td>00:01:40</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:43:18</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:44:58</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2955</th>
      <td>2950</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:43:32</td>
      <td>2019-11-01T23:46:32</td>
      <td>00:03:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:43:32</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:46:32</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2956</th>
      <td>2951</td>
      <td>Taxi</td>
      <td>2019-11-01T23:44:05</td>
      <td>2019-11-01T23:44:05</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:44:05</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:44:05</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2957</th>
      <td>2952</td>
      <td>Taxi</td>
      <td>2019-11-01T23:44:09</td>
      <td>2019-11-01T23:44:09</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:44:09</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:44:09</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2958</th>
      <td>2953</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:45:16</td>
      <td>2019-11-01T23:47:07</td>
      <td>00:01:51</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:45:16</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:47:07</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2959</th>
      <td>2954</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:46:22</td>
      <td>2019-11-02T00:04:55</td>
      <td>00:18:33</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:46:22</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:04:55</td>
      <td>00</td>
      <td>...</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2960</th>
      <td>2955</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:46:46</td>
      <td>2019-11-01T23:53:00</td>
      <td>00:06:14</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:46:46</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:53:00</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2961</th>
      <td>2956</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:47:28</td>
      <td>2019-11-01T23:50:10</td>
      <td>00:02:42</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:47:28</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:50:10</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2962</th>
      <td>2957</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:47:31</td>
      <td>2019-11-02T00:01:02</td>
      <td>00:13:31</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:47:31</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:01:02</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2963</th>
      <td>2958</td>
      <td>Taxi</td>
      <td>2019-11-01T23:47:33</td>
      <td>2019-11-01T23:47:33</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:47:33</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:47:33</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2964</th>
      <td>2959</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01T23:49:22</td>
      <td>2019-11-01T23:51:53</td>
      <td>00:02:31</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:49:22</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:51:53</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2965</th>
      <td>2960</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:50:03</td>
      <td>2019-11-02T00:05:34</td>
      <td>00:15:31</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:50:03</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:05:34</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2966</th>
      <td>2961</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:50:07</td>
      <td>2019-11-01T23:54:17</td>
      <td>00:04:10</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:50:07</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:54:17</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2967</th>
      <td>2962</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:50:31</td>
      <td>2019-11-01T23:54:22</td>
      <td>00:03:51</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:50:31</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:54:22</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2968</th>
      <td>2963</td>
      <td>Hotel/Motel</td>
      <td>2019-11-01T23:53:16</td>
      <td>2019-11-01T23:55:58</td>
      <td>00:02:42</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:53:16</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:55:58</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2969</th>
      <td>2964</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:53:23</td>
      <td>2019-11-02T00:28:13</td>
      <td>00:34:50</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:53:23</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:28:13</td>
      <td>00</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2970</th>
      <td>2965</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:54:03</td>
      <td>2019-11-01T23:56:27</td>
      <td>00:02:24</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:54:03</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:56:27</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2971</th>
      <td>2966</td>
      <td>Taxi</td>
      <td>2019-11-01T23:55:57</td>
      <td>2019-11-01T23:55:57</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:55:57</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:55:57</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2972</th>
      <td>2967</td>
      <td>Taxi</td>
      <td>2019-11-01T23:56:00</td>
      <td>2019-11-01T23:56:00</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:56:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:56:00</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2973</th>
      <td>2968</td>
      <td>Taxi</td>
      <td>2019-11-01T23:58:06</td>
      <td>2019-11-01T23:58:06</td>
      <td>00:00:00</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:58:06</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:58:06</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2974</th>
      <td>2969</td>
      <td>Off Airport Parking</td>
      <td>2019-11-01T23:58:25</td>
      <td>2019-11-02T00:00:33</td>
      <td>00:02:08</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:58:25</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:00:33</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2975</th>
      <td>2970</td>
      <td>Pre-arranged</td>
      <td>2019-11-01T23:59:30</td>
      <td>2019-11-02T00:03:14</td>
      <td>00:03:44</td>
      <td>2019-11-01</td>
      <td>2021-03-27 23:59:30</td>
      <td>2019-11-02</td>
      <td>2021-03-27 00:03:14</td>
      <td>00</td>
      <td>...</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>50 rows × 22 columns</p>
</div>




```python
# Removes any TripDate that is not '2019-11-01'

AirportTransportationData = AirportTransportationData[AirportTransportationData.TripStrtDate == '2019-11-01']

# Groups, Aggregates, and Sums certain fields

AirportTransportationCnts = AirportTransportationData[['OperatorType', 'TripStrtDate', 'Min15orLess', 'Min16to30', 'Min31and45', 'Min46and59', 'Hour1', 'Hour2', 'Hour3', 'Hour4', 'Hour5', 'Hour6']].groupby(['OperatorType', 'TripStrtDate']).agg(['sum'])

```


```python
# Resets the index

AirportTransportationCnts = AirportTransportationCnts.reset_index()

# Only shows the first header

AirportTransportationCnts.columns = AirportTransportationCnts.columns.get_level_values(0)

# Prints the data

AirportTransportationCnts

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
      <th>OperatorType</th>
      <th>TripStrtDate</th>
      <th>Min15orLess</th>
      <th>Min16to30</th>
      <th>Min31and45</th>
      <th>Min46and59</th>
      <th>Hour1</th>
      <th>Hour2</th>
      <th>Hour3</th>
      <th>Hour4</th>
      <th>Hour5</th>
      <th>Hour6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CHARTER BUS-NEW</td>
      <td>2019-11-01</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Charter Bus</td>
      <td>2019-11-01</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Courtesy</td>
      <td>2019-11-01</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HOTEL/MOTEL-NEW</td>
      <td>2019-11-01</td>
      <td>19.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hotel/Motel</td>
      <td>2019-11-01</td>
      <td>384.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>INTER CITY-NEW</td>
      <td>2019-11-01</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Inter City</td>
      <td>2019-11-01</td>
      <td>89.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Off Airport Parking</td>
      <td>2019-11-01</td>
      <td>644.0</td>
      <td>5.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>PRE-ARRANGED-NEW</td>
      <td>2019-11-01</td>
      <td>123.0</td>
      <td>13.0</td>
      <td>4.0</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pre-arranged</td>
      <td>2019-11-01</td>
      <td>278.0</td>
      <td>37.0</td>
      <td>14.0</td>
      <td>4.0</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01</td>
      <td>118.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Taxi</td>
      <td>2019-11-01</td>
      <td>1205.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Removes columns after Min150rLess

AirportTransportationCnts = AirportTransportationCnts.drop(["Min16to30", "Min31and45", "Min46and59", "Hour1", "Hour2", "Hour3", "Hour4", "Hour5", "Hour6"], axis=1)

# Shows the first 12 rows

AirportTransportationCnts.head(12)

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
      <th>OperatorType</th>
      <th>TripStrtDate</th>
      <th>Min15orLess</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CHARTER BUS-NEW</td>
      <td>2019-11-01</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Charter Bus</td>
      <td>2019-11-01</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Courtesy</td>
      <td>2019-11-01</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>HOTEL/MOTEL-NEW</td>
      <td>2019-11-01</td>
      <td>19.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hotel/Motel</td>
      <td>2019-11-01</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>INTER CITY-NEW</td>
      <td>2019-11-01</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Inter City</td>
      <td>2019-11-01</td>
      <td>89.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Off Airport Parking</td>
      <td>2019-11-01</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>PRE-ARRANGED-NEW</td>
      <td>2019-11-01</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pre-arranged</td>
      <td>2019-11-01</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Taxi</td>
      <td>2019-11-01</td>
      <td>1205.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Removes any records that are less than 100

AirportTransportationCnts = AirportTransportationCnts[AirportTransportationCnts.Min15orLess > 100.0]

# Prints the data

AirportTransportationCnts

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
      <th>OperatorType</th>
      <th>TripStrtDate</th>
      <th>Min15orLess</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>Hotel/Motel</td>
      <td>2019-11-01</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Off Airport Parking</td>
      <td>2019-11-01</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>PRE-ARRANGED-NEW</td>
      <td>2019-11-01</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pre-arranged</td>
      <td>2019-11-01</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>SHARED RIDE VAN-NEW</td>
      <td>2019-11-01</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Taxi</td>
      <td>2019-11-01</td>
      <td>1205.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prints the AirportFlightCntsWOAirlines dataframe

AirportFlightCntsWOAirlines

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
      <th>TripDate</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-11-01</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-11-01</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-11-01</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prints the AirportParkingCntsWOParkingType dataframe

AirportParkingCntsWOParkingType

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
      <th>Terminal</th>
      <th>EntryDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Other</td>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Conacatenates the AirportParkingCntsWOParkingType and AirportFLightCntsWOAirlines and prints out the results

result = pd.concat([AirportParkingCntsWOParkingType, AirportFlightCntsWOAirlines], axis=1, join='outer')

result

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
      <th>Terminal</th>
      <th>EntryDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
      <th>TripDate</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2019-11-01</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>2019-11-01</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>2019-11-01</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Other</td>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Renames the EntryDate column to TripStrtDate

result.rename(columns={'EntryDate':'TripStrtDate'}, inplace=True)

# Merges the Result dataframe with the AirportTransportationCnts dataframe

result1 = pd.merge(result, AirportTransportationCnts, how='inner')

```


```python
# Drops the Terminal column

FinalResults = result1.drop(["TripDate", "Terminal"], axis=1)

# Prints the Final Results

FinalResults

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
      <th>TripStrtDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
      <th>OperatorType</th>
      <th>Min15orLess</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2019-11-01</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2019-11-01</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2019-11-01</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>2019-11-01</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
##### SQLite3 database table creation and updates 

```


```python
# Imports Packages

import sqlite3

# Connects to SQLite3

conn = sqlite3.connect('mydat.sqlite')

```


```python
# Prints out the contents from the database table

pd.read_sql_query("select * from Sky_Harbor_Transportion;", conn)

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
      <th>index</th>
      <th>TripStrtDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
      <th>OperatorType</th>
      <th>Min15orLess</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Removes the Terminall = Other column

conn.execute('DELETE FROM Sky_Harbor_Transportion WHERE Terminall = "Other";',);

# Reads the Sky_Harbor_Transportation table

pd.read_sql_query("select * from Sky_Harbor_Transportion;", conn)

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
      <th>index</th>
      <th>TripStrtDate</th>
      <th>ParkingType</th>
      <th>OneDayOrLess</th>
      <th>TwoDaysToFiveDays</th>
      <th>SixDaysToTenDays</th>
      <th>ElevenDaysTo25Days</th>
      <th>Terminall</th>
      <th>Airline</th>
      <th>Arrival</th>
      <th>Departure</th>
      <th>OperatorType</th>
      <th>Min15orLess</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>2019-11-01 00:00:00</td>
      <td>Daily ParkingEconomy Parking</td>
      <td>1390.0</td>
      <td>292.0</td>
      <td>27.0</td>
      <td>3.0</td>
      <td>2</td>
      <td>United AirlinesUnited AirlinesUnited AirlinesA...</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>2019-11-01 00:00:00</td>
      <td>Level 1Parking Garage</td>
      <td>1308.0</td>
      <td>115.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>3</td>
      <td>Hawaiian AirlinesDelta Air LinesFrontier Airli...</td>
      <td>43.0</td>
      <td>41.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>2019-11-01 00:00:00</td>
      <td>Parking Garage</td>
      <td>3882.0</td>
      <td>1039.0</td>
      <td>53.0</td>
      <td>12.0</td>
      <td>4</td>
      <td>WestJetVolarisVolarisVolarisVolarisSouthwest A...</td>
      <td>443.0</td>
      <td>447.0</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Hotel/Motel</td>
      <td>384.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Off Airport Parking</td>
      <td>644.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>PRE-ARRANGED-NEW</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pre-arranged</td>
      <td>278.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>SHARED RIDE VAN-NEW</td>
      <td>118.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>2019-11-01 00:00:00</td>
      <td>East Economy Garage AEast Economy Garage BEast...</td>
      <td>2078.0</td>
      <td>1932.0</td>
      <td>184.0</td>
      <td>26.0</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Taxi</td>
      <td>1205.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Prints the FinalResults dataframe to a CSV file

FinalResults.to_csv (r'~/Desktop/DSC540/Transport.csv', index = False, header=True)

```


```python
# The visualization is located on "Sky Harbor Data Visualization.pptx"
```
