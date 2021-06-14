# Buyify Challenge

Syed Ahmed 
June 14, 2021

## Overview 

In this project, I will be analyzing a dataset provided by Buyify, an ecommerce online store company that is looking to find out how their sneaker shops are doing using Python and Pandas. Buyify has also requested an analysis of how efficiently their warehouse teams are at shipping orders, which I will be further exploring using SQL. 

## Resources 
Dataset: Buyify_Challenge.csv 
SQL server: https://www.w3schools.com/SQL/TRYSQL.ASP?FILENAME=TRYSQL_SELECT_ALL 

## Shoe Store Performance Analysis 

Buyify has provided us with the following statement: 
> "On Buypify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis."

**Question 1.**

1. Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.



My initial thought is to try to figure out where the $3145 AOV is derived from. By importing the excel spreadsheet as a CSV into Jupyter Notebook, I can further investigate this.

![](RackMultipart20210614-4-g4tryp_html_6933326e5535cd1e.png)

Using the describe ( ) function in Pandas, we can see the summary statistics of the Order Amount table. As we can see from the results of our query, the $3145 AOV is accurate based on the given data. However, we see that there is a standard deviation of $41,282, which tells us that on average, our values vary 41,282 from the mean. This makes the mean an inaccurate representation of the AOV.

The describe ( ) function has also given us other insight into the data, other than the mean and standard deviation. We can see that the minimum value for order amount is 90, and the maximum goes all the way up to 704,000. Comparing these values to the median (284), first quartile (163) and third quartile (390), we can see that there is a significant increase from the upper quartile to the maximum value of 704,000. With about 5,000 records in our dataset, we can speculate that there are outliers that may be inflating our mean.

![](RackMultipart20210614-4-g4tryp_html_b42a850832962122.png) ![](RackMultipart20210614-4-g4tryp_html_2defae96361e8cdf.png)To further investigate this, I have visualized the data using a boxplot.

The boxplot shows us that there is one outlier that is much greater than the rest of the data, which is the maximum value of 704,000. We can also see by zooming in that the &quot;box&quot; component of the boxplot is much lower the rest of the values in the dataset. This means that there could be a lot of outliers, which we can further look into by using the groupby ( ) function in Pandas.

![](RackMultipart20210614-4-g4tryp_html_8c17f25a52cfac75.png)Using the groupby ( ) function, I was able to group the order amount values and create a column for the count of values sorted in descending order as shown above. As we can see from the table, some of the larger order amounts such as 704000, 77175, 51450, and 25725 are repeated frequently. We can gather insight by looking into these rows from the dataset.

![](RackMultipart20210614-4-g4tryp_html_e5e8f508d657e782.png)

We can see that our maximum order amount occurs frequently and at the exact same time of 4:00 AM each day between the same shop id and user id. We see similar patterns for the other high order amounts that we queried. We can speculate that some of these large transactions could be some supplier purchasing at bulk, as the order amount is consistently the same as well as the time of purchase and user id.

1. What metric would you report for this dataset?

Due to the fact that we have extreme points that are inflating our mean, it would be a better idea to use another metric to evaluate our data. The most accurate representation of order data would be to use the median value of a truncated dataset that removes outliers from the dataset. We can use the 1.5 IQR rule to truncate our dataset and create a more accurate representation of order value, as this will filter out most outliers.

1. What is its value?

As we can see below, our truncated date frame utilizes the 1.5 IQR which drops outliers which are 1.5 \* IQR above the third quartile, and 1.5 \* IQR below the first quartile. In the end, we have a median value of 280, which would be the best representation of order values. We have also decreased the standard deviation to 144, which is a lot more reasonable.

![](RackMultipart20210614-4-g4tryp_html_5b64656546001fbe.png) ![](RackMultipart20210614-4-g4tryp_html_32988904f03d1007.png)

**Question 2.**

1. How many orders were shipped by Speedy Express in total?

By doing a simple SELECT statement, we can see that the number of orders is in the &#39;Orders&#39; table, and shipper information for Speedy Express is in the Shippers table. We can join both tables on Shipper ID to get the number of orders shipped by Speedy Express. The select statement will look like the following:

![](RackMultipart20210614-4-g4tryp_html_1c62c53cc1982275.png)

From this query, the total number of orders is 54.

1. What is the last name of the employee with the most orders?

T ![](RackMultipart20210614-4-g4tryp_html_ce0bf91fb8b78dd4.png) he employee last names are stored in the &#39;Employees&#39; table, and the order information is stored in the &#39;Orders&#39; table. We can join both tables on Employee ID and use a GROUP BY statement to group the last names by number of orders.

Based on this query, Peacock had the most orders, with 40 orders.

1. What product was ordered the most by customers in Germany?

In order to get the final answer to this question, we have to perform multiple joins, as the data necessary to answer this question is scattered across different tables in this database. Our first query joins the &#39;Orders&#39; table with the &#39;Customers&#39; table on Customer ID, which returns a table with two columns: Order Id and Country.

![](RackMultipart20210614-4-g4tryp_html_49048f414d43d392.png)

T ![](RackMultipart20210614-4-g4tryp_html_6ddb7faae860a9a.png) he next join tells us which item was ordered the most. We join &#39;Orders&#39;, &#39;Customers&#39;, and &#39;OrderDetails&#39; to find the total quantity of each product by filtering the table by Country and grouping &#39;OrderDetails&#39; by Product ID. The result of this query is a table with three columns: Country, Product ID, and TotalOrdered. I also sorted the TotalOrdered column by descending to show the product that was ordered most.

![](RackMultipart20210614-4-g4tryp_html_a05fd16d22f25d5d.png)

Now, all that is left is to figure out what product corresponds to the top Product ID. To get our final answer, we need to join the &#39;Products&#39; table with our previous table.

![](RackMultipart20210614-4-g4tryp_html_aefa9b00498611e6.png)

![](RackMultipart20210614-4-g4tryp_html_cd2329a1bf4b2226.png)

From our final table, we can see that the most ordered product in Germany is Boston Crab Meat, with 160 units ordered.

## SQL Database ERD

![QuickDBD-export](https://user-images.githubusercontent.com/45697471/121945764-264b8e00-cd22-11eb-9abb-72c98b981ac8.png)
