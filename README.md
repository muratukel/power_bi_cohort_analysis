# Let's Learn How to Create Cohort Analysis Using Power BI 📊

### What is Cohort Analysis?

☑️ Cohort analysis is a subset of behavioral analytics that takes the data from a given eCommerce platform, web application, or online game and rather than looking at all users as one unit, it breaks them into related groups for analysis. These related groups, or cohorts, usually share common characteristics or experiences within a defined time-span.

☑️ Cohort analysis is a tool to measure user engagement over time. It helps to know whether user engagement is actually getting better over time or is only appearing to improve because of growth.

☑️ Cohort analysis proves to be valuable because it helps to separate growth metrics from engagement metrics as growth can easily mask engagement problems. In reality, the lack of activity of the old users is being hidden by the impressive growth numbers of new users, which results in concealing the lack of engagement from a small number of people.

I can convey it to you better with this visual: 

![image](https://github.com/muratukel/power_bi_cohort_analysis/assets/136103635/57a44cba-8f05-45cb-bf19-e0983945efbb)

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

☑️ The first step in conducting cohort analysis is to create a cohort table. This table includes a group column that allows you to group customers based on the month they registered or the product they purchased. Additionally, when creating this table, you should generate calculated columns such as "Active Customers" and "New Customers."

☑️ Active Customers: This column represents the total number of active customers within each cohort. Active customers refer to those who continue to make transactions within a specific cohort. This helps identify customer loyalty and those who provide continuous revenue.

☑️ New Customers: This column represents the number of customers who started making transactions within a specific time period in each cohort. This column is essential for measuring customer acquisition and monitoring the behavior of new customers.

☑️ When creating the cohort table, each customer should be assigned to their respective cohort using date fields like the registration date or invoice date. This table enables you to make comparisons between cohorts and analyze customer behaviors in the subsequent steps.

```DAX
Active Customers = DISTINCTCOUNT(FactSales[Customer ID]
```

```DAX
New Customers = 
    CALCULATE(
        [Active Customers],
        FactSales[Months Since First Transaction] = 0 )
````

### Step 2️⃣: Creating a Period Table and Calendar Table



The next step is to create a period table, which will help you visualize the external months as a list. This list will create a table field ranging from 0 to 24.

To create a Period Table using the 'New Table' function in the Power BI Ribbon, use the following DAX:

```DAX
Months Since First Transaction = 
DATEDIFF(
    RELATED(DimCustomers[First Transaction Month]),
    RELATED(DimDate[Start of Month]),
    MONTH
)
```

This DAX expression calculates the month difference between the "First Transaction Month" column and the "Start of Month" column. Here's a step-by-step explanation of the expression:

- `DATEDIFF`: This function calculates the difference between two dates. It can calculate the difference in the specified unit.

- `RELATED(DimCustomers[First Transaction Month])`: First, this expression retrieves the value of the "First Transaction Month" column from the related table, which represents the month when customers made their first transaction.

- `RELATED(DimDate[Start of Month])`: Then, it fetches the value of the "Start of Month" column from the related table, which represents the beginning of the month where the transaction occurred.

- `MONTH`: Finally, it's the third argument of the DATEDIFF function and ensures that the difference is calculated in months.

As a result, this DAX expression calculates the month difference between the month when customers made their first transaction and the start of the transaction month. This difference is useful for determining how many months a customer has been active since their first transaction.

### Step 3️⃣: Create the Cohort Measure

```DAX
Cohort Performance = 

 var _minDate = MIN(DimDate[Start of Month])
 var _maxDate = MAX(DimDate[Start of Month])
return 
    CALCULATE(
        [Active Customers],
        REMOVEFILTERS(DimDate[Start of Month]), 
        RELATEDTABLE(DimCustomers),
        DimCustomers[First Transaction Month] >= _minDate
            && DimCustomers[First Transaction Month] <= _maxDate
        )
```
This DAX code creates the cohort measurement that will be used as the third step in cohort analysis. This measurement is used to calculate the number of customers in each group for a specific month. Let's break down the code step by step:

- The `_minDate` and `_maxDate` variables retrieve the smallest and largest dates from the "Start of Month" column in the `DimDate` table. This determines the time range for the analysis.

- Next, we begin calculating the measurement with the `RETURN` statement. The `CALCULATE` function performs a series of filtering operations and calculates the resulting count of customers.

- First, the `REMOVEFILTERS` function clears all filters on the "Start of Month" column. This signifies that operations are to be performed for a specific month.

- Subsequently, the `RELATEDTABLE` function returns the "DimCustomers" table, capturing the customers who were active in that month.

- Finally, by using the "First Transaction Month" column from the `DimCustomers` table, it checks the customers' first transaction month within the range of `_minDate` and `_maxDate`. This calculates the number of customers in a specific cohort.

This DAX code is used to calculate how many customers are in a specific cohort for a given month. This measurement is valuable in cohort analysis for observing differences and changes between groups.

### Step 4️⃣: Visualizing the Cohort

The fourth step is to visualize the results of the cohort analysis. The visualization process includes calculating the Retention Rate to observe the percentage change between 0 to 24 months. This step is essential to visually express how long customers remain active in specific cohorts.

The Retention Rate calculation is done using the following DAX formula:

```DAX
Retention Rate = 
    DIVIDE([Cohort Performance],[New Customers])
```

![image](https://github.com/muratukel/power_bi_cohort_analysis/assets/136103635/6e6ec3d9-e315-4a0f-bca1-56311d5cb3a1)

## Step 5️⃣: Analyzing the Results

- After visualizing the cohort, you can analyze the results to gain insights into customer behavior over time. When examining aggregate data, you can identify trends and patterns in customer behavior that might not be immediately apparent. For instance, you might notice that customers who sign up in a particular month have a higher likelihood of churning compared to those who sign up in other months. Alternatively, you may observe that customers who purchase a specific product are more likely to make repeat purchases compared to those who purchase other products.

- Cohort analysis helps you uncover and understand these nuances in customer behavior, allowing you to make data-driven decisions and strategies to improve customer retention and engagement.
