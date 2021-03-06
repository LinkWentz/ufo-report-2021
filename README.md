# Introduction
As I'm sure you're aware, there is a lot of hype around UFOs in the United States. This has only been exacerbated by the incoming report from the Pentagon regarding the very same subject. So as the excitement reaches a fever pitch, I've taken it upon myself to do my own report on UFOs by analyzing the National UFO Reporting Center Database.

The National UFO Reporting Center was founded in 1974. This organization collects and catalogs UFO reports made through its hotline or online, and makes those reports publicly available on its website. These claims are given the once over for obvious pranks, but aside from that not fact checked or verified in any way. It's simply a repository for these reports; but a massive repository at that. The database has well over 100,000 entries in it. And as these reports are completely unfiltered, this dataset is the perfect target to answer my main question: What causes UFO reports?

If you want to see the article for which this analysis was done, go to [Medium](https://linkwentz.medium.com/the-june-2021-ufo-report-f1aabc63c827).

# Summary
I've broken the analysis down into three questions, the answers to which give some idea as to what causes UFO reports. These answers won't cover everything, but we can at least find correlations between some variables to give us a general idea of what's behind UFO sightings. The questions are as follows:

1. When have there been spikes in UFO activity?
2. Is there a relationship between the time of year and the amount of UFO sightings?
3. Do more UFO sightings happen during daytime or nighttime?
4. (Bonus) How has the amount of UFO sightings per year changed over time?
5. (Bonus) What state has the most UFO reports per capita?

## Question 1. When have there been spikes in UFO activity?
##### Methodology:
My approach to answering this question was pretty simple. I found the cumulative reports for each day of the year across every year, and plotted them a month at a time. Whenever I found a spike I would look at that month in each year to see if it was a one-time spike or an annual event. If it was the former, the final plot would simply be the report count for each day in the month for that year. If it was the latter, the final plot would show the average reports for each day in the month across all years.

##### Results:
In all, I found one spike related to an annual event and one related to a one off event. The annual event was, quite entertainingly, the 4th of July. 

![1 - July Spike](https://user-images.githubusercontent.com/22262374/122638190-7165f800-d0b8-11eb-8520-ff6cd7366854.png)

The one time event appears to be a U.S. Navy missile launch on November 7th of 2015.

![2 - November 2015 Spike](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/2%20-%20November%202015%20Spike.png)

## Question 2. How have UFO reports changed over time?
##### Methodology:
I decided to take a detailed look at how many reports were made each month. Specifically, I wanted to look at each month's total UFO reports for each year individually. I also wanted to see if there were months that were typically more or less active. To communicate this visually, I decided to use an animation. 

The first part of the animation involved the counts for each year. As the years went on, these counts would be layered over each other, one after the other. However, the counts alone didn't work. As you'll see, UFO sightings have been increasing year over year. To account for this, I decided to express each month's sightings as the percentage of the year's sightings that took place within that month. This made each year's trend comparable. The second part of the animation simply highlights the minimum and maximum value for each month from 2000-2020. Of note is that I filtered out the 4th of July to ensure that July wouldn't obscure the overall trend.

##### Results:
It seems that the latter half of the year accounts for more reports than the former. I expect this is for multiple reasons. Firstly, late Spring and Summer is when people usually spend the most time outside. A likely explanation for the rest of the year is that many people are travelling for the holidays. Both of these explanations would make it more likely for people to experience UFO encounters. However, again, there could be many explanations for this trend, and I can only speculate as to what they would be. One last note: the year 2020 is most certainly an outlier, but I left it in as I believe it actually helps to highlight how strong the typical trend actually is.

![3 - Reports Per Month 2000-2020](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/3%20-%20Reports%20Per%20Month%202000-2020.gif)

## Question 3. Do more UFO sightings happen during day time or night time?
##### Methodology:
The first thing I wanted to have was a plot that compared the amount of sightings when the sun was up to the amount of sightings when the sun was down. To do this, I needed to assign either "day" or "night" to each sighting. This is difficult because there is no set time that day turns to night. It changes based on location and time of year. That meant that whatever solution I used had to account for those factors as well.

To accomplish this, I used the library PyEphem. Using Ephem, I could create "observers" which have spherical coordinates, date and timestamps, and altitudes associated with them. Using those values, Ephem could calculate the altitude of the Sun relative to the observer. If the altitude was negative, that meant that the Sun was below the horizon, and therefore it was nighttime.

However, there were more than a few challenges associated with creating these observers. Firstly, the reports themselves didn't come with latitudes and longitudes. What they did come with were cities. So, I found a dataset containing the coordinates for over 28,000 U.S. cities, created an observer for each city using the city's coordinates, and joined those observers to the sightings using the city and state as a key. If the city the sighting took place in didn't appear in the cities dataset, the most populated city for the state that the sighting took place in was used as a substitute. Reports that didn't have a state associated with them were dropped.

Secondly, Ephem uses UTC timestamps exclusively, while the timestamps for the sightings were in local time. Luckily, that same cities data set also came with time zones. So, I used the time zone for each city to convert the sighting timestamps to UTC.

Once both of these problems had been solved, I finally got what I believe to be a very accurate assessment of the time of day for each sighting. Of course it's not perfect; it doesn't take into account altitude for example. But it is, at the very least, more accurate than an arbitrary cutoff point would have been.

The next thing I wanted was a plot that determined how many sightings occurred in each hour of the day. This was mostly to confirm what I found with the first plot, but ended up being fairly interesting as well. Of course, this plot was much simpler as I just made a histogram using the hour column. What does need to be discussed here is the coloring of the histogram. I decided that I wanted to make a gradient as a rough way to visually depict the fact that some hours would either be classified as day or night depending on the time of year. 

However, a simple gradient from the center wasn't going to cut it, so instead I made two gradients. The first faded from a dark purple to yellow, and ended at what I could find on the internet was the hour in which latest sunrise of the year occurred in the United States. I did the opposite for the tail of the plot, using the earliest sunset. Between the latest sunrise and earliest sunset, I left the bars entirely yellow, as a way of showing that at no point were any of the sightings that took place within those hours during the night time.

##### Results:
As is shown in this plot, the overwhelming majority of sightings (~78%), take place at night. 

![4 - Day v Night Sightings](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/4%20-%20Day%20v%20Night%20Sightings.png)

In the second plot, we can see that there is an incredibly smooth relationship between how typical it is for an hour to be dark and how many sightings occur within that hour; especially if you account for the typical period a person is expected to be asleep. Even more satisfying is the degree to which the amount of sightings during sunny hours remain consistent.

![5 - Reports By Hour](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/5%20-%20Reports%20By%20Hour.png)

## (Bonus) Question 4. How has the amount of UFO sightings per year changed over time?
##### Methodology:
This was another relatively simple question. To answer it I plotted the amount of reports for each year. There was already a very convenient year column in the NUFORC data set, so all I really had to do was make a histogram of that column. As you'll see I limited the plot to a period of 46 years, from 1974 to 2020. The lower limit was decided on simply because NUFORC was founded in 1974, and therefore reports before that date are sparse at best and all retroactively added. I believed this would have added junk to the chart without providing reliable information.

##### Results:
The amount of UFO sightings per year appears to have increased exponentially over time. However, scaling the y-axis accordingly shows us that reports were actually happening at a pretty consistent rate before the mid-90's. I believe the reason for this is the popularization of cell phones and the internet making it much easier to report these sightings, as well as raising awareness of NUFORC.

![6 - Reports Per Year](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/6%20-%20Reports%20Per%20Year.png)

## (Bonus) Question 5. What state has the most UFO reports per capita?
##### Methodology:
For this question, I used census data to divide the amount of sightings in each state by their respective populations. I then plotted these sightings from highest to lowest. There's not much more to say about this question, except to explain why I didn't include it in the final article. From a methodology standpoint, I didn't know if this was an appropriate way to go about measuring reports per capita. The sightings take place over multiple years, but the population measures are estimates from 2019. I didn't feel confident that any trend I found would be reliable, which brings me to the results.

##### Results:
As you can see, there isn't really a strong trend here, at least, not on the surface. Though Washington does have the most sightings per capita, looking at the x-axis there isn't much of a difference between any state. Puerto Rico has the lowest, however that is mostly due to the fact that there are less than 100 reports from Puerto Rico in the dataset. What's more, the plot is very clumsy, with the y-axis being almost illegible. Overall, there was really nothing worthy of reporting here, however, I left it in because I personally find it fun. I also did want to comment that their rankings on this plot seem roughly opposite to their rankings in population density, though that is only a comment, not a claim.

![7 - Reports Per Capita Per State](https://github.com/LinkWentz/UFO-Report-2021/blob/master/Plots/7%20-%20Reports%20Per%20Capita%20Per%20State.png)

# Note on the Dataset
The dataset was initially from the website data.world and specifically a user named khturner. I used that dataset for most of the development of this project. However, this dataset didn't have results after early 2017, so I took it upon myself to update the dataset with the latest sightings from NUFORC. For more detail, and to get the dataset itself, you can go to the [NUFORC-Dataset](https://github.com/LinkWentz/NUFORC-Dataset) repository on my GitHub.

# Sources
- [NUFORC](http://www.nuforc.org/)
- [Dataset I used for much of the development](https://data.world/khturner/national-ufo-reporting-center-reports)
- [US Cities Dataset](https://simplemaps.com/data/us-cities)
- [Latest Sunrise and Earliest Sunset](https://abc7ny.com/winter-solstice-latest-sunrise-earliest-sunset-solar-time/8593291/#:~:text=However%2C%20the%20earliest%20sunset%20date,2021%20at%207%3A20%20a.m.)
- [State Population Data](https://www.census.gov/data/datasets/time-series/demo/popest/2010s-state-total.html)
- [Reference for State Population Editing](https://www.wikipedia.org)

# Dependencies
- [Python 3.9 or higher](https://www.python.org/): The python standard library package zoneinfo is only available in 3.9 and later
- [ipympl Backend](https://github.com/matplotlib/ipympl): Enables the interactive features of matplotlib in the Jupyter notebook and in JupyterLab.
- [NumPy](https://pypi.org/project/numpy/): Package for array computing with Python
- [Pandas](https://pypi.org/project/pandas/): Python package that provides fast, flexible, and expressive data structures
- [matplotlib](https://pypi.org/project/matplotlib/): Comprehensive library for creating static, animated, and interactive visualizations in Python
- [ephem](https://pypi.org/project/ephem/): Python package for performing high-precision astronomy computations
