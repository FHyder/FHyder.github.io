---
layout: post
title:      "Module 2 Final Project: The 'n' justifies the means"
date:       2019-02-12 23:56:09 +0000
permalink:  module_2_final_project_the_n_justifies_the_means
---


 
Our Module 2 project took us on a deep dive through the challenge of producing statistically significant results for a predetermined hypothesis. I found this subject to be a lot less dense the multiple linear regression and the results to be much more user friendly. 

We worked with the Northwind Database.  The tables in this database, in general, met the assumptions required for T-Stat testing so I went that route with all of my hypothesis using a significance level of .05. I went out of my way to use pandas as little as possible this time around because I wanted to get more familiar with SQL. To be honest, I prefer pandas because it's much more visibly pleasing but I'm determined to continue to challnge myself. 

I navigated the SQL database using the schema provided for this project. Sometimes, the names of columns in the schema were off by a letter or two so I loaded the whole table into a pandas dataframe and used dataframe.head() to get an overview of the table that I was working with and column names.

Our first challenge was to answer whether quantities ordered for products in this company increased once they were discounted, and if so at what levels of discounts. I created a pandas dataframe with the Order Details table to examine quantities ordered with and without discounts. I created a list of all quantities ordered for discounted items and all quantities ordered with no discounts.  For all of my experiments I conjured the t-stat and t-crtical using the stat formulas from the SCIPY library. I also calculated Cohen's D, and ran A/B tests to confirm that my sample sizes were large enough to report sound results. I found that discounted items were in fact ordered at higher quantities. To answer the second part of the question I first looked at the unique values of the discount series from the table I created from the OrderDetails table and determined that discounts were offered at the 1,2,3,4,5,6,10,15,20, and 25% levels. I created list of quantities ordered for each of these levels and examined the sizes of each of these lists. Off the bat, I was able to elimante 1 - 6 % ommitting 5% because all of these list sizes were less than 5 items. I then ran a t-test on all of these list vs. the 'no discount' list to see if any statistical significant mean differences were detected. In all of these tests, discounted items outperformed non-discounted items.  I created a bar graph plotting the average quantites sold at each discount level to offer a visualization of this finding. 

For my second hypothesis, I wanted to expand on the findings of my first hypothesis to see if unit price had an effect on the quantities sold for discounted items. I would assume that if a higher priced item is offered at a discounted rate it would sell at a higher rate than a lower priced item offered at a discounted rate, since the buyer would be saving more in the former instance. I first calculated the median of the unit prices of products from the order details table and used it as a threshold for dividing 'higher priced' and 'lower priced' items. The threshold was a unit price of $18. At face value, there was a difference, products that had a unit price of $18 or higher sold on average 11% more than products with a unit price of $17 or less. However after running a t-test, this difference was not found to be significant enough to reject the null hypothesis. This means that stake holders would do well to offer higher discounts on cheaper items as they will still sale just based on them being discounted alone and the stake holder will lose less money by offering the discount. 


For my third hypothesis, I tested if price had an effect on whether an items had a high number of units in stock. I created two groups to define "high" units in stock and "low" units in stock. High stock numbers were units that had 100 or more units in stock and low stock numbers were units that had 30 or lower units in stock. Interestingly enough, our null hypothesis is rejected, price does not have an effect on whether an item is being ordered or not. It seems that quality over quantity rules here. 

For my last hypothesis, I tested to see of South American countries sold higher quantities than North American quantites. In this particular case, I ran into a challenge when joining two SQL tables that didn't share a key. I solved the problem by finding a table that share keys with both of the other tables and using it to merge. So for this instance I first joined the customer and orders table using the customer ID key and then joined the aforementioned table with the order details table using the order ID key. This allowed me to access quantities ordered by countries. I created two lists one with South American and North American countries and first calculated their mean difference. On average, South American quantities sold 8.45 more units than North American countries. However, after running  another T-Test, this difference was found to be statistically insignificant. 


I had a much easier time showing my findings visually in this project. It's important to not identify "rejecting the null hypothesis" as "losing" haha. It feel instinctual to try and bend my results so that my hypothesis is correct, I think the lesson to be learned is that ultimately you're not conclusively proving a hypothesis, just inferring to statistical significance based on your limited sample. 



-Fahima Hyder

