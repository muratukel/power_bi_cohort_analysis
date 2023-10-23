# Let's Learn How to Create Cohort Analysis Using Power BI 📊

### What is Cohort Analysis?

☑️ Cohort analysis is a subset of behavioral analytics that takes the data from a given eCommerce platform, web application, or online game and rather than looking at all users as one unit, it breaks them into related groups for analysis. These related groups, or cohorts, usually share common characteristics or experiences within a defined time-span.

☑️ Cohort analysis is a tool to measure user engagement over time. It helps to know whether user engagement is actually getting better over time or is only appearing to improve because of growth.

☑️ Cohort analysis proves to be valuable because it helps to separate growth metrics from engagement metrics as growth can easily mask engagement problems. In reality, the lack of activity of the old users is being hidden by the impressive growth numbers of new users, which results in concealing the lack of engagement from a small number of people.

I can convey it to you better with this visual: 

![image](https://github.com/muratukel/power_bi_cohort_analysis/assets/136103635/dd29039f-0365-407b-a215-5d2484f9aaa8)

☑️ In this chart, we are looking at the retained count of customers who signed up for an e-commerce website over a 24-month period. The chart is divided into groups based on the month in which the customers signed up. Each row represents a different group, and each column represents a different month.

☑️ The numbers in the chart represent the count of customers who are still active (i.e., not churned) from each group for each month. For example, in the first row (the group of customers who signed up in December), 985 customers were active in the first month, but by the 12th month, only 359 customers were still active.

☑️ By looking at this chart, we can see that retention rates tend to decrease over time for all cohorts, but some cohorts may have higher retention rates than others. This information can help us identify which groups are the most valuable and which may require more attention to improve retention rates.

☑️ As we read down the columns, things start to get more interesting, and potentially more complicated. But bear with us. In the chart below, there is a big drop in numbers for most months between columns three and four, noticeable by the sudden change in shading between those columns: darker to lighter.

☑️ What we can take from this is that the critical time for customer retention is three months after the first purchase. Armed with this knowledge, the eCommerce business can now start to address why this is happening and start rolling out new measures to halt the trend. For instance, it might develop a loyalty campaign around the three-month mark to draw customers back in and ward off the march towards ever-fading attention. Adding seasonal campaigns can also increase the number of active customers by attracting their attention.

# Let's do the Steps to Conduct Cohort Analysis 🔄📊

❗ Before conducting cohort analysis, it's crucial to have a good understanding of the dataset. Once you are well-versed in the dataset's metadata, your workload in cohort analysis will be reduced. In other words, it's essential to perform data cleaning and preprocessing meticulously. Here are some things I pay attention to in my own work on this analysis:

✏️ Correcting the data types of columns (such as CustomerID and InvoiceDate).

✏️ Filtering out customers who have canceled their subscriptions, as they could lead to misinterpretations in cohort analysis (I filtered out customers with Customer IDs starting with 'C').

### Step 1️⃣: Creating a Cohort Table

The first step in conducting cohort analysis is to create a cohort table. This table includes a group column that allows you to group customers based on the month they registered or the product they purchased. Additionally, when creating this table, you should generate calculated columns such as "Active Customers" and "New Customers."

Active Customers: This column represents the total number of active customers within each cohort. Active customers refer to those who continue to make transactions within a specific cohort. This helps identify customer loyalty and those who provide continuous revenue.

New Customers: This column represents the number of customers who started making transactions within a specific time period in each cohort. This column is essential for measuring customer acquisition and monitoring the behavior of new customers.

When creating the cohort table, each customer should be assigned to their respective cohort using date fields like the registration date or invoice date. This table enables you to make comparisons between cohorts and analyze customer behaviors in the subsequent steps.

```DAX
Active Customers = DISTINCTCOUNT(FactSales[Customer ID]
```

```DAX
New Customers = 
    CALCULATE(
        [Active Customers],
        FactSales[Months Since First Transaction] = 0 )
````
