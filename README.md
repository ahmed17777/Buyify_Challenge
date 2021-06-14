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
> "On Buyify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis."

**Question 1.**

a) **Think about what could be going wrong with our calculation. Think about a better way to evaluate this data.**

  ![1](https://user-images.githubusercontent.com/45697471/121947571-2d739b80-cd24-11eb-9e6e-8738e472933e.jpg)

My initial thought is to try to figure out where the $3145 AOV is derived from. By importing the excel spreadsheet as a CSV into Jupyter Notebook, I can further investigate this.

  ![2](https://user-images.githubusercontent.com/45697471/121947602-33697c80-cd24-11eb-99e2-780aa0d6ec6f.jpg)


Using the describe ( ) function in Pandas, we can see the summary statistics of the Order Amount table. As we can see from the results of our query, the $3145 AOV is accurate based on the given data. However, we see that there is a standard deviation of $41,282, which tells us that on average, our values vary 41,282 from the mean. This makes the mean an inaccurate representation of the AOV.

The describe ( ) function has also given us other insight into the data, other than the mean and standard deviation. We can see that the minimum value for order amount is 90, and the maximum goes all the way up to 704,000. Comparing these values to the median (284), first quartile (163) and third quartile (390), we can see that there is a significant increase from the upper quartile to the maximum value of 704,000. With about 5,000 records in our dataset, we can speculate that there are outliers that may be inflating our mean.

To further investigate this, I have visualized the data using a boxplot.

  ![3](https://user-images.githubusercontent.com/45697471/121947711-5bf17680-cd24-11eb-9b00-cf486b3c70bd.jpg)
  ![4](https://user-images.githubusercontent.com/45697471/121947716-5e53d080-cd24-11eb-8dad-55b97afc8568.jpg)


The boxplot shows us that there is one outlier that is much greater than the rest of the data, which is the maximum value of 704,000. We can also see by zooming in that the "box" component of the boxplot is much lower the rest of the values in the dataset. This means that there could be a lot of outliers, which we can further look into by using the groupby ( ) function in Pandas.

  ![5](https://user-images.githubusercontent.com/45697471/121947931-a07d1200-cd24-11eb-98e2-f91f7f84cb28.jpg)


Using the groupby ( ) function, I was able to group the order amount values and create a column for the count of values sorted in descending order as shown above. As we can see from the table, some of the larger order amounts such as 704000, 77175, 51450, and 25725 are repeated frequently. We can gather insight by looking into these rows from the dataset.

  ![6](https://user-images.githubusercontent.com/45697471/121948058-c7d3df00-cd24-11eb-94b5-d927e734c13e.jpg)


We can see that our maximum order amount occurs frequently and at the exact same time of 4:00 AM each day between the same shop id and user id. We see similar patterns for the other high order amounts that we queried. We can speculate that some of these large transactions could be some supplier purchasing at bulk, as the order amount is consistently the same as well as the time of purchase and user id.

b) **What metric would you report for this dataset?**

Due to the fact that we have extreme points that are inflating our mean, it would be a better idea to use another metric to evaluate our data. The most accurate representation of order data would be to use the median value of a truncated dataset that removes outliers from the dataset. We can use the 1.5 IQR rule to truncate our dataset and create a more accurate representation of order value, as this will filter out most outliers.

c) **What is its value?**

As we can see below, our truncated date frame utilizes the 1.5 IQR which drops outliers which are 1.5 \* IQR above the third quartile, and 1.5 \* IQR below the first quartile. In the end, we have a median value of 280, which would be the best representation of order values. We have also decreased the standard deviation to 144, which is a lot more reasonable.

  ![7](https://user-images.githubusercontent.com/45697471/121948198-ee921580-cd24-11eb-91a3-379640b50706.jpg)
  ![8](https://user-images.githubusercontent.com/45697471/121948204-f0f46f80-cd24-11eb-8136-3a2b1719fdae.jpg)


**Question 2.**

a) **How many orders were shipped by Speedy Express in total?**

By doing a simple SELECT statement, we can see that the number of orders is in the &#39;Orders&#39; table, and shipper information for Speedy Express is in the Shippers table. We can join both tables on Shipper ID to get the number of orders shipped by Speedy Express. The select statement will look like the following:


  ![9](https://user-images.githubusercontent.com/45697471/121948413-39ac2880-cd25-11eb-9e1a-47539d0bc428.jpg)


From this query, the total number of orders is 54.

b) **What is the last name of the employee with the most orders?**

The employee last names are stored in the 'Employees' table, and the order information is stored in the 'Orders' table. We can join both tables on Employee ID and use a GROUP BY statement to group the last names by number of orders.


  ![10](https://user-images.githubusercontent.com/45697471/121948643-7d069700-cd25-11eb-8b95-8371853f341c.jpg)


Based on this query, Peacock had the most orders, with 40 orders.

c) **What product was ordered the most by customers in Germany?**

In order to get the final answer to this question, we have to perform multiple joins, as the data necessary to answer this question is scattered across different tables in this database. Our first query joins the 'Orders' table with the 'Customers' table on Customer ID, which returns a table with two columns: Order Id and Country.


  ![11](https://user-images.githubusercontent.com/45697471/121948897-d7075c80-cd25-11eb-955a-08475995f877.jpg)


The next join tells us which item was ordered the most. We join &#39;Orders&#39;, &#39;Customers&#39;, and &#39;OrderDetails&#39; to find the total quantity of each product by filtering the table by Country and grouping &#39;OrderDetails&#39; by Product ID. The result of this query is a table with three columns: Country, Product ID, and TotalOrdered. I also sorted the TotalOrdered column by descending to show the product that was ordered most.


  ![12](https://user-images.githubusercontent.com/45697471/121948922-e090c480-cd25-11eb-8b60-dfedc5357431.jpg)
  
  ![13](https://user-images.githubusercontent.com/45697471/121949058-0cac4580-cd26-11eb-8e6b-5ce9788160ed.jpg)


Now, all that is left is to figure out what product corresponds to the top Product ID. To get our final answer, we need to join the &#39;Products&#39; table with our previous table.

  ![14](https://user-images.githubusercontent.com/45697471/121949069-0fa73600-cd26-11eb-9caf-23bfc0d7c4e1.jpg)
  
  ![15](https://user-images.githubusercontent.com/45697471/121949074-1170f980-cd26-11eb-943e-8b2d88aaed5d.jpg)


From our final table, we can see that the most ordered product in Germany is Boston Crab Meat, with 160 units ordered.

## SQL Database ERD

![QuickDBD-export](https://user-images.githubusercontent.com/45697471/121945764-264b8e00-cd22-11eb-9abb-72c98b981ac8.png)


### Contact 
Email: mishaal22s@gmail.com 
