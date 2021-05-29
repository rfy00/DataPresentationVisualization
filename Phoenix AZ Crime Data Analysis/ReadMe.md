<b><i>Intro</i></b> - Many people qualify for Social Security throughout the United States.  This project will utilize the dataset that was taken from data.world based on the “Annual report providing Social Security beneficiary population data by state and ZIP code. Report for 2015”.  The project will use various graphs and data tables to show how the data can be split up by ZIP code, County, City/Town, Number of Qualified Recipients, and Amount of Monthly Benefits (in thousands).  This will help answer whether there is a correlation between the Total Monthly Benefits and Total Number of Recipients.  It will also help answer questions about which Counties have the least and most amount of Total Monthly Benfits and Total Number of Recipients.  There were multiple datasets for all the U.S. but I only used the Arizona dataset.

<b><i>Tools</i></b> - Python, CSV, Excel, Word, PowerPoint

<b><i>Data</i></b> - Cities and towns across the world have some type of crime that is reported every year.  Many bigger cities, such as Phoenix, Arizona, have a lot of violent and non-violent crime in different parts of the city.  Various dams that were built in Arizona in the early to mid-1900s, paved the way for Phoenix to become the capitol of Arizona in 1912.  These dams provided power to different areas of the state.  During the second World War, Phoenix started becoming an economic hub for local residents and migrants.  In the 1960s and 70s, there started to be several problems when Phoenix started growing a lot.  For instance, street gangs turned certain parts of Phoenix into areas where you did not want to go due to crime (i.e. South Phoenix and near downtown area).  

Fast forwards to the 1990s and beyond, Phoenix/metro area was becoming and still is a popular city for relocation of individuals and families.  Many bars, restaurants, and shops were/are opening up and/or relocating to the area.  This helped with Phoenix and the surrounding suburbs revitalization, economic, and housing boom.  In 2018, the estimated population of Phoenix was 1.66 million people, the median age was 33.5, and poverty rate was estimated to be at 19.4% (Data USA, 2021).  Also in 2018, the median household income was $57,957 and the median property value was $249,100 (Data USA, 2021).

In 2021, Arizona violent and property crimes have dropped.  “Violent crime incidents dipped from 4.7 per 1,000 people to 4.6, and property crime fell from 26.8 incidents per 1,000 to 24.4 (Edwards, 2021).  Compared to all of the 50 states, Arizona has the fourteenth highest property crime rate and the tenth highest violent crime rate (Edwards, 2021).  Phoenix ranked the thirty-second safest city in Arizona.




<b><i>Methods/Evaluation/Techniques</i></b> - I cleaned the data, summarized the various pieces of useful data, and used various statistical methods to evaluate the data.

<b><i>Conclusion</i></b> - Overall, depending on the groupings for the Total Number of recipient fields, the Top 5 varied a little by ZIP Code and City/Town.  For instance, Sun City and Sun City West are in Maricopa County and are retirement communities.  Surprise is just south of those communities so i would expect the number of total recipients to be high.  Tucson (second populous city in Arizona) and Green Valley (suburb of Tucson) are in Pima County (near the border in the South Quadrants) have the 4th and 5th highest recipients.

What was also interesting is all the cities/towns that were in the bottom 5 were in Apache County which is located in towards the northwest of Arizona next to the New Mexico border.  This may be due to population is not as high as other counties and Indian Reservations.

The correlation between the Number of recipients and the Monthly Benefits (in thousands) seem to be greater when the population is higher.  With Maricopa County being the most populous county and Pima County being the second most populous county, they will have more ZIP Codes that will be outliers compared to the rest of the state.

There were quite a few limitations that i ran into.  The dataset was mainly based on two major categories, Number of Recipients and Monthly Benefits.  There are only so many ways a person can slice and dice the data and look at it using graphs.  Looking at the other states where the same data was gathered for would help compare information.  A person would be able to gain better understanding of where the most people based on each category lived based on ZIP Code and County.  

<b><i>Navigation</i></b> - 

<b>Arizona OASDI Number of People Receiving Benefits.rmd</b> is the main R file

<b>Arizona OASDI Number of People Receiving Benefits and the Amount of Monthly Benefits.pdf</b> is the final output of the R Markdown code

<b>updateoasdi_zip15.xlsx</b> is the file that is uploaded into the rmd file

