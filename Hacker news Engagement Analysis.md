# HACKER NEWS:

## Introduction: 

I am doing the Engagement analysis on the Hacker news data using python and Jupiter notebook

## About Hacker News: 

Hacker news is a website started by the startup incubator Y Combinator, where users submit stories similar to posts and they receive points and comments similar to reddit

The Posts that make it to the top of the Hacker news site receives more visits as a result

Since the posts are dated in the Eastern Time Zone, the analysis is conducted after converting to the target time zone (CST)

## Posts are categorized into:

+ Ask HN : Users ask the community a specific question
+ Show HN : Users show their projects, products, website developed or things that are interesting to the Hacker news community
+ Other HN:  The posts that are not provided with Show HN or Ask HN will fall into the other HN category


## Goals: 

+ To examine how engaged users are in the Hacker News community
+ To identify the times of day when user activity increases or declines
+ The optimal time to pitch posts for maximum reach and to determine whether Ask posts or Show posts have the greatest reach in the community

#### Loading the data into Pandas dataframe:



```python
# Read in the data

import pandas as pd
df = pd.read_csv("hacker_news.csv")
```

#### Sample data


```python
# sampling the dataframe

print(df.head(2)) 
```

             id                                              title  \
    0  12579008  You have two days to comment if you want stem ...   
    1  12579005                         SQLAR  the SQLite Archiver   
    
                                                     url  num_points  \
    0  http://www.regulations.gov/document?D=FDA-2015...           1   
    1   https://www.sqlite.org/sqlar/doc/trunk/README.md           1   
    
       num_comments    author      created_at  
    0             0   altstar  9/26/2016 3:26  
    1             0  blacksqr  9/26/2016 3:24  


#### Provided Columns, their data types and the number of rows


```python
print(df.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 293119 entries, 0 to 293118
    Data columns (total 7 columns):
     #   Column        Non-Null Count   Dtype 
    ---  ------        --------------   ----- 
     0   id            293119 non-null  int64 
     1   title         293119 non-null  object
     2   url           279256 non-null  object
     3   num_points    293119 non-null  int64 
     4   num_comments  293119 non-null  int64 
     5   author        293119 non-null  object
     6   created_at    293119 non-null  object
    dtypes: int64(3), object(4)
    memory usage: 15.7+ MB
    None


#### Hacker News Data contains: 

`id`: the unique identifier from Hacker News for the post


`title`: the title of the post


`url`: the URL that the posts links to, if the post has a URL


`num_points`: the number of points the post acquired, calculated as the total number of upvotes minus the total number of downvotes


`num_comments`: the number of comments on the post


`author`: the username of the person who submitted the post


`created_at`: the date and time of the post's submission.The time zone is Eastern Time in the US

#### Basic Query to convert the created_date field from object to date_time format 


```python
# The below code shows the created_at field is not at date time format. it is currently at Object format
print(df.info())

df['created_at'] = pd.to_datetime(df['created_at'])

df['created_at'] = df['created_at'].dt.tz_localize('America/New_York',ambiguous='NaT', nonexistent='shift_forward')
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 293119 entries, 0 to 293118
    Data columns (total 7 columns):
     #   Column        Non-Null Count   Dtype 
    ---  ------        --------------   ----- 
     0   id            293119 non-null  int64 
     1   title         293119 non-null  object
     2   url           279256 non-null  object
     3   num_points    293119 non-null  int64 
     4   num_comments  293119 non-null  int64 
     5   author        293119 non-null  object
     6   created_at    293119 non-null  object
    dtypes: int64(3), object(4)
    memory usage: 15.7+ MB
    None



```python
# Convert to CST (Central Standard Time)
df['created_at'] = df['created_at'].dt.tz_convert('America/Chicago')
```

#### Segmenting dataframe by the type of the post


```python
# Seperating the posts according to the category 'Ask', 'Show' & 'Other'
Ask_HN = df[df['title'].str.startswith('Ask HN')]
Show_HN = df[df['title'].str.startswith('Show HN')]
Other_HN = df[~df['title'].str.startswith('Ask HN') & ~df['title'].str.startswith('Show HN')]
```

## Findings:  Ask_HN post VS Show_HN posts

+ The number of Show posts (10,150) is higher compared to the number of Ask posts (9,122), yet the comments are more numerous on Ask posts
+ On average, Ask_HN posts receive 10.41 comments per post, while Show_HN posts receive only 4.89 comments. This suggests that people are more inclined to engage when the post is framed as a question


```python
#To calculate the total number of posts:
print('Total_posts:', len(df['title']))
print('Total Ask_HN posts:',len(Ask_HN))
print('Total Show_HN posts:',len(Show_HN))
print('Total Other_HN posts:',len(Other_HN))
```

    Total_posts: 293119
    Total Ask_HN posts: 9122
    Total Show_HN posts: 10150
    Total Other_HN posts: 273847


#### Number of comments &  posts: Ask_HN


```python
#Calculate the total number of Ask_HN comments

Total_Ask_HN_comments = Ask_HN['num_comments'].sum()
print('Total_no_of_Ask_HN_comments:',Total_Ask_HN_comments)


# calculate the total Ask_HN posts

Total_Ask_HN_posts = len(Ask_HN['num_comments'])
print('Total_no_of_Ask_HN_posts:',Total_Ask_HN_posts)
```

    Total_no_of_Ask_HN_comments: 94930
    Total_no_of_Ask_HN_posts: 9122


#### Number of comments and posts: Show_HN


```python
# Calculate the total number of Show_HN comments

Total_Show_HN_comments = Show_HN['num_comments'].sum()
print('Total_no_of_Show_HN_comments:',Total_Show_HN_comments)


# calculate the total Show_HN posts

Total_Show_HN_posts = len(Show_HN['num_comments'])
print('Total_no_of_Show_HN_posts:',Total_Show_HN_posts)
```

    Total_no_of_Show_HN_comments: 49617
    Total_no_of_Show_HN_posts: 10150


#### Average Number of comments per post - Ask_HN:


```python
# calculate the average number of Ask_HN comments per post

Average_Ask_HN = Total_Ask_HN_comments/ Total_Ask_HN_posts
print('Average no_of_Ask_HN comments per post',Average_Ask_HN.round(2))
```

    Average no_of_Ask_HN comments per post 10.41


#### Average Number of comments per post -  Show_HN:


```python
# calculate the average number of Ask_HN comments per post

Average_Show_HN = Total_Show_HN_comments/ Total_Show_HN_posts
print('Average no_of_Show_HN comments per post',Average_Show_HN.round(2))
```

    Average no_of_Show_HN comments per post 4.89


## The Influence of Hour on the Number of Posts and Comments

#### Separating the hour from the `created_at` field, to analyze trends by hour


```python
import datetime as dt

#Seperating hour from the date time format
Ask_HN['created_at_hour'] = Ask_HN['created_at'].dt.hour
Show_HN['created_at_hour'] = Show_HN['created_at'].dt.hour
Other_HN['created_at_hour'] = Other_HN['created_at'].dt.hour


#Calculating the day of the week
Ask_HN['Day of the week'] = Ask_HN['created_at'].dt.day_name()
Show_HN['Day of the week'] = Show_HN['created_at'].dt.day_name()
Other_HN['Day of the week'] = Other_HN['created_at'].dt.day_name()
```

    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Ask_HN['created_at_hour'] = Ask_HN['created_at'].dt.hour
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Show_HN['created_at_hour'] = Show_HN['created_at'].dt.hour
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:6: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Other_HN['created_at_hour'] = Other_HN['created_at'].dt.hour
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Ask_HN['Day of the week'] = Ask_HN['created_at'].dt.day_name()
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:11: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Show_HN['Day of the week'] = Show_HN['created_at'].dt.day_name()
    /var/folders/16/3tzmlcc129z3xr007ykwjb640000gp/T/ipykernel_2203/156689199.py:12: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Other_HN['Day of the week'] = Other_HN['created_at'].dt.day_name()


## Findings: Ask_HN posts

+ Based on the correlation between `created_at_hour`, `Total posts`, and `Total comments`, it is clear that the number of posts strongly correlates with the hour of creation(0.628457). This suggests that the time of day influences the number of posts made. Meanwhile, Total comments shows a strong correlation with Total posts(0.674450), indicating that as the number of posts increases, the number of comments tends to increase as well. However, `created_at_hour` does not influence `Total comments` as much as `Total posts`; their correlation is relatively low (0.261360).This suggests that the time of day does not influences the number of comments made for the Ask_posts
+ Comments and posts peak at 14:00. Specifically, this indicates that user activity is high between 13:00 and 14:00. Since users are in different time zones, the period covers most time zones' lunchtime. 
+ These times correspond to when people are likely having lunch (12:00 to 13:00)

## Ask _HN Analysis: No.of.comments and posts per hour 

#### Basic query to calculate the Total comments and Total posts: 


```python
#To calculate the total comments and posts per hour
Comments_per_hour_Ask = Ask_HN.groupby('created_at_hour').agg({'num_comments': 'sum','title':'count'})

# To reset the index
Comments_per_hour_Ask = Comments_per_hour_Ask.reset_index()

# To map the column names
Comments_per_hour_Ask = Comments_per_hour_Ask.rename(columns = {'created_at_hour':'created_at_hour','num_comments':'Total_comments', 'title':'Total_posts'})
```


```python
# Top 10 Ask_post comments per hour
print(Comments_per_hour_Ask.sort_values(by=['Total_comments'],ascending= False).head(10))
```

        created_at_hour  Total_comments  Total_posts
    14             14.0           18525          646
    12             12.0            7227          442
    16             16.0            5547          587
    13             13.0            4970          512
    17             17.0            4868          612
    20             20.0            4500          516
    19             19.0            4462          510
    15             15.0            4461          578
    11             11.0            4234          342
    18             18.0            3954          551



```python
## Correlation matrix
Comments_per_hour_Ask.corr()
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
      <th>created_at_hour</th>
      <th>Total_comments</th>
      <th>Total_posts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>created_at_hour</th>
      <td>1.000000</td>
      <td>0.26136</td>
      <td>0.628457</td>
    </tr>
    <tr>
      <th>Total_comments</th>
      <td>0.261360</td>
      <td>1.00000</td>
      <td>0.674450</td>
    </tr>
    <tr>
      <th>Total_posts</th>
      <td>0.628457</td>
      <td>0.67445</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



#### Average comments per hour - Ask_HN


```python
Comments_per_hour_Ask['Average_comments'] = Comments_per_hour_Ask['Total_comments']/Comments_per_hour_Ask['Total_posts']

#Top ten rows
print(Comments_per_hour_Ask.sort_values(by=['Average_comments'],ascending= False).head(10))
```

        created_at_hour  Total_comments  Total_posts  Average_comments
    14             14.0           18525          646         28.676471
    12             12.0            7227          442         16.350679
    11             11.0            4234          342         12.380117
    1               1.0            3053          271         11.265683
    9               9.0            3013          282         10.684397
    3               3.0            2358          242          9.743802
    13             13.0            4970          512          9.707031
    16             16.0            5547          587          9.449744
    7               7.0            2362          257          9.190661
    10             10.0            2794          310          9.012903


### Graph Ask_HN - Total Comments Vs Total posts


```python
fig,(ax1,ax2) = plt.subplots(2,1,figsize=(8, 7))

# Plot Total_comments on the first y-axis
color = 'tab:blue'
ax1.set_xlabel('Hour of Day')
ax1.set_ylabel('Total comments', color=color)
ax1.plot(Comments_per_hour_Ask['created_at_hour'], Comments_per_hour_Ask['Total_comments'], color=color, label='Total Comments')
ax1.tick_params(axis='y', labelcolor=color)

# Create a second y-axis for Total_posts
color = 'tab:red'
ax2.set_xlabel('Hour of Day')
ax2.set_ylabel('Total Posts', color=color)
ax2.plot(Comments_per_hour_Ask['created_at_hour'], Comments_per_hour_Ask['Total_posts'], color=color, linestyle='--', label='Total Posts')
ax2.tick_params(axis='y', labelcolor=color)

# Add titles and legends
ax1.legend(loc='upper right')
ax2.legend(loc='upper right')

# Show the plot
plt.show()
```


    
![png](https://github.com/Muthaln1/User-engagement-Analysis/blob/main/Total%20comments%20Vs%20Total%20posts.png)
    


## Findings: Show_HN posts 

+ Based on the correlation between `created_at_hour`, `Total posts`, and `Total comments`, it is clear that the number of posts and comments strongly correlates with the hour of creation. This suggests that the time of day influences the number of posts and comments made. Meanwhile, Total comments shows a strong correlation with Total posts(0.945513), indicating that as the number of posts increases, the number of comments tends to increase as well for Show Posts
+ Show post data shows higher user activity (in terms of posts) compared to community engagement (in terms of comments)
+ User activity for 'Show' posts is significantly higher between 11:00 and 19:00. These times correspond to when people are likely having lunch, winding down at the end of the workday (14:00 to 15:00), and relaxing after dinner (15:00 to 16:00). Additionally, since users are in various time zones, this range covers most time zones' after work hours

## Show_HN Analysis: No.of.comments and posts per hour 

#### Basic query : 


```python
Comments_per_hour_Show = Show_HN.groupby('created_at_hour').agg({'num_comments': 'sum','num_points':'sum','title':'count'})

Comments_per_hour_Show = Comments_per_hour_Show.reset_index()

Comments_per_hour_Show = Comments_per_hour_Show.rename(columns = {'created_at_hour':'created_at_hour','num_comments':'Total_comments', 'title':'Total_posts','num_points':'Total_points'})

# To identify Top 10 Show_post comments per hour
print(Comments_per_hour_Show.sort_values(by=['Total_comments'],ascending= False).head(10))
```

        created_at_hour  Total_comments  Total_points  Total_posts
    13               13            3839         10503          696
    14               14            3823         11651          834
    15               15            3769         11487          801
    11               11            3609         10787          516
    12               12            3314         10381          610
    17               17            3242          9935          656
    16               16            3228         10560          759
    18               18            2791          8928          556
    10               10            2413          7742          402
    19               19            2183          6948          525



```python
## Correlation matrix
Comments_per_hour_Show.corr()
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
      <th>created_at_hour</th>
      <th>Total_comments</th>
      <th>Total_points</th>
      <th>Total_posts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>created_at_hour</th>
      <td>1.000000</td>
      <td>0.423238</td>
      <td>0.514519</td>
      <td>0.522894</td>
    </tr>
    <tr>
      <th>Total_comments</th>
      <td>0.423238</td>
      <td>1.000000</td>
      <td>0.984441</td>
      <td>0.945513</td>
    </tr>
    <tr>
      <th>Total_points</th>
      <td>0.514519</td>
      <td>0.984441</td>
      <td>1.000000</td>
      <td>0.961011</td>
    </tr>
    <tr>
      <th>Total_posts</th>
      <td>0.522894</td>
      <td>0.945513</td>
      <td>0.961011</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



#### Average comments per hour- Show_HN


```python
Comments_per_hour_Show['Average_comments'] = Comments_per_hour_Show['Total_comments']/Comments_per_hour_Show['Total_posts']

# Top 10 average comments per hour
print(Comments_per_hour_Show.sort_values(by=['Average_comments'],ascending= False).head(10))
```

        created_at_hour  Total_comments  Total_points  Total_posts  \
    11               11            3609         10787          516   
    6                 6            1573          3297          235   
    10               10            2413          7742          402   
    7                 7            1770          4639          315   
    13               13            3839         10503          696   
    12               12            3314         10381          610   
    1                 1            1076          2764          209   
    3                 3             978          2707          194   
    18               18            2791          8928          556   
    17               17            3242          9935          656   
    
        Average_comments  
    11          6.994186  
    6           6.693617  
    10          6.002488  
    7           5.619048  
    13          5.515805  
    12          5.432787  
    1           5.148325  
    3           5.041237  
    18          5.019784  
    17          4.942073  


### Graph Show_HN - Total Comments Vs Total posts


```python
fig,(ax1,ax2) = plt.subplots(2,1,figsize=(8, 7))

# Plot Total_comments on the first y-axis
color = 'tab:blue'
ax1.set_xlabel('Hour of Day')
ax1.set_ylabel('Total comments', color=color)
ax1.plot(Comments_per_hour_Show['created_at_hour'], Comments_per_hour_Show['Total_comments'], color=color, label='Total Comments')
ax1.tick_params(axis='y', labelcolor=color)

# Create a second y-axis for Total_posts
color = 'tab:red'
ax2.set_xlabel('Hour of Day')
ax2.set_ylabel('Total Posts', color=color)
ax2.plot(Comments_per_hour_Show['created_at_hour'], Comments_per_hour_Show['Total_posts'], color=color, linestyle='--', label='Total Posts')
ax2.tick_params(axis='y', labelcolor=color)

# Add titles and legends
ax1.legend(loc='upper right')
ax2.legend(loc='upper right')

# Show the plot
plt.show()
```


    
![png](https://github.com/Muthaln1/User-engagement-Analysis/blob/main/Total%20comments%20Vs%20Total%20Posts%20Show_HN.png)
    


## Day of the week influence on posts and comments
## Findings: 

+ Show_HN:
     + Users are active throughout the week from Monday to Friday between 11:00 & 17:00 covering most time zones lunch and after work hours
     + Fridays at 17:00 people were burning their fuel around the dinner time compared to the rest of the days or weekends
+ Ask_HN
    + Users are active from Monday to Friday at 14:00 covering most time zones lunch time. it has a pattern which is different from the show posts. There are no time ranges, but user activity is only during that time 
    + Activities are very less on weekends
+ Other_HN
    + These are the posts that does not fall into any of the above category or users might not have mentioned the category the post it belongs to
    + it has a similar pattern to show posts.Users are active from Monday to Friday between 11:00 & 17:00
    + Activities are very less on weekends


### Heatmap - Show_HN User Engagement (comments) Trend by Day of the week and Hour:

To better understand whether the day of the week influence the User enagagement, please find below the graphs


### Heatmap - Show_HN User Engagement  Trend by Hour and Day of the week


```python
#pivoting the table

heatmap_Show_data = Show_HN.pivot_table(index='Day of the week', columns='created_at_hour', values='num_comments', aggfunc='sum')

# Sorting the heap map data by days of the week

day_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

heatmap_Show_data = heatmap_Show_data.reindex(day_order)
```


```python
# Importing the packages for the heatmap 
import seaborn as sns
import matplotlib.pyplot as plt

# Create the heatmap
plt.figure(figsize=(8, 6))
sns.heatmap(heatmap_Show_data, cmap='crest')


# Add titles and labels
plt.title('Show posts Heatmap of Values by Day and Hour')
plt.xlabel('Hour')
plt.ylabel('Day')

# Show the plot
plt.show()
```


    
![png](https://github.com/Muthaln1/User-engagement-Analysis/blob/main/Show%20posts%20Heatmap.png)
    


### Heatmap - Ask_HN User Engagement (comments) Trend by Hour and Day of the week


```python
# Pivoting the table

heatmap_Ask_data = Ask_HN.pivot_table(index='Day of the week', columns='created_at_hour', values='num_comments', aggfunc='sum')

# Sorting the heap map data by days of the week

day_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

heatmap_Ask_data = heatmap_Ask_data.reindex(day_order)

```


```python
# Importing the packages for the heatmap 
import seaborn as sns
import matplotlib.pyplot as plt

# Create the heatmap
plt.figure(figsize=(8, 6))
sns.heatmap(heatmap_Ask_data, cmap='crest')


# Add titles and labels
plt.title('Ask posts Heatmap of Values by Day and Hour')
plt.xlabel('Hour')
plt.ylabel('Day')

# Show the plot
plt.show()
```


    
![png](https://github.com/Muthaln1/User-engagement-Analysis/blob/main/Ask%20posts%20HEatmap.png)
    


### Heatmap - Other_HN User Engagement(comments) Trend by Hour and Day of the week


```python
# pivoting the table

heatmap_data_Other = Other_HN.pivot_table(index='Day of the week', columns='created_at_hour', values='num_comments', aggfunc='sum')

# Sorting the heap map data by days of the week

day_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

heatmap_data_Other = heatmap_data_Other.reindex(day_order)
```


```python
# Importing the packages for the heatmap 
import seaborn as sns
import matplotlib.pyplot as plt

# Create the heatmap
plt.figure(figsize=(8, 6))
sns.heatmap(heatmap_data_Other, cmap='crest')


# Add titles and labels
plt.title('Other posts Heatmap of Values by Day and Hour')
plt.xlabel('Hour')
plt.ylabel('Day')

# Show the plot
plt.show()
```


    
![png](https://github.com/Muthaln1/User-engagement-Analysis/blob/main/Other%20posts%20Heatmap.png)
    


## Conclusion

+ In terms of total comments and posts, the number of Show posts (10,150) is higher compared to the number of Ask posts (9,122). However, comments are more numerous on Ask posts.
+ Users are active between lunch and dinner hours for Show_HN. Activity is generally high towards the end of the day. In contrast, users are active only at 14:00 for Ask_HN. There is less weekend activity for both Ask and Show posts.
+ Ask posts receive more user engagement compared to Show posts, as people are more inclined to answer questions when prompted rather than sharing a product, document, or website and asking for opinions.
+ According to the FAQ section on the Hacker News site, posts that users find interesting and believe add value to the community are upvoted and remain on the top 30 list.
+ If a user is posting an Ask post, they should aim to do so right before lunchtime. For Show posts, it is best to post closer to lunchtime or before dinner to maximize community reach.
