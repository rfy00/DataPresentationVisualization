Intro - Exploratory Data Analysis (EDA) involves several steps from cleaning data all the way to identifying statistical and other data visualization methods to present the data.  https://analytics.usa.gov/data/ shows various datasets where participating government departments allow the public to gain insights in the type of data they store. For this EDA project, i chose to use two different time periods for the "Visits to all domains over 30 days" dataset.  The dataset shows all the participating government websites (about 400) that the public went to.

Tools - Python, CSV, PowerPoint

Data - The following dataset that was used is from https://analytics.usa.gov/data/live/all-domains-30-days.csv I selected two different time periods for this dataset: Data pulled on 4-5-20 for the previous 30 days and on 5-17-20 for the previous 30 days

Methods/Evaluation/Techniques - I analyzed the two different time periods for the all-domains-30-days dataset and compared the datasets to each other to see what types. i utilized various methods to evaluate and show only a certain number of visits for the two time frames.  Then, i combined each dataset together to see the differences.  I used various statistical methods and graphs/charts to show the differences and similarities between the datasets.

Conclusion - Before I started doing more of my statistical analysis, I had to do a little cleanup on the datasets before I joined them together.  There were over 2,000 records and I only wanted to analyze the top 50 records from the 4/5/20 data pull with the second dataset matching the domains of the first.  After I joined the two datasets on the Domain (website), I removed the last part of the extra rows that did not include the first 50 Domains (empty information for the 4/5/20 data pull).  Then, I had to update the dates, remove unwanted fields, add zeros where there was NaN, and calculated the difference of variables between the 4/5/20 and 5/17/20 data pulls.  What surprised me about reviewing the initial data after joining the data, was some of the websites that people were going to such as ncbi.nlm.nih.gov (looks like it may point to different links where data is pulled from) and sa.www4.irs.gov (Access denied message).

While reviewing the histograms, there were some outliers, but I believe they should be kept.  If I was evaluating more data in the datasets, the outliers would not appear as outliers.  When I reviewed the means, variance, standard deviations, and mode, I noticed most of the numbers decreased between the different periods of time.  I used the Page Views Per Session variables from the two data pulls while analyzing the PMF.  There was not much of a difference between the means so the graphs look pretty much identical.  The CDF graph curves from the bottom right towards the top of the right-hand side.  It looks there is a fairly high likelihood the average session duration will be 400 or more.  

The type of analytical distribution that I used was log normal.  I based the data on page views per a session for data pull 4/5/20.  It looks like it has a pretty close to normal distribution as the line curves from the upper left-hand side and slopes down to the bottom of the graph.  

After reviewing the VisitsDiff and ExitsDiff scatter plot, most of the dots are centered around the zero area of the graph.  For the most part, there will be a correlation between these variables as people have to go to the websites and exit them. On the next scatterplot, I analyzed the avg_session_duration1 and avg_session_duration2.   There is a small correlation based on events that were going on during certain periods of time but the duration of staying on websites, will vary.

I used the Pearson’s Correlation test to test the hypothesis.  I used the variables pageviews_per_session1 and pageviews_per_session2.  The correlation test showed that the variable will most likely be dependent.


Navigation -

USA gov 30 Day Visits Comparison Presentation.ipynb is the main Python file 

USA gov 30 Day Visits Comparison Presentation.pptx is the final product that was created based off of the data

all-domains-30-days-4-5-20.csv is one of the main datasets that is used in the ipynb file

all-domains-30-days-5-17-20.csv is one of the main datasets that is used in the ipynb file
