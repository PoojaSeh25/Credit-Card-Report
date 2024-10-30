# Credit Card Report 

Weekly Status Report

# Project Objective:
To develop a comprehensive credit card weekly dashboard that provides real-time insights into key performance metrics and trends, enabling stakeholders to monitor and analyze credit card operations effectively.

# Table Structures
# 1. Customer Table (customer)

The customer table holds customer-specific details:

1. Client_Num: Unique identifier for each customer.

2. Customer_Age: Age of the customer.

3. Gender: Customer’s gender.

4. Dependent_Count: Number of dependents for each customer.

5. Education_Level: Customer’s highest education level.

6. Marital_Status: Marital status of the customer.

7. state_cd: State code representing customer’s location.

8. Zipcode: Customer’s residential postal code.

9. Car_Owner: Indicates if the customer owns a car (Yes/No).

10. House_Owner: Indicates if the customer owns a house (Yes/No).

11. Personal_loan: Indicates if the customer has a personal loan.

12. contact: Contact preference or status of the customer.

13. Customer_Job: Job type or title of the customer.

14. Income: Annual income of the customer.

15. Cust_Satisfaction_Score: Satisfaction score based on customer feedback.

# 2. Credit Card Table (credit_card)

The credit_card table holds transaction and financial details for each customer:

1. Client_Num: Unique identifier that links to customer table.

2. Card_Category: Category/type of the credit card.

3. Annual_Fees: Annual fee associated with the card.

4. Activation_30_Days: Indicates if the card was activated within 30 days.

5. Customer_Acq_Cost: Cost to acquire the customer.

6. Week_Start_Date: Start date of the week for transaction records.

7. Week_Num: Week number in the calendar year.

8. Qtr: Financial quarter.

9. current_year: Current year of transaction.

10. Credit_Limit: Credit limit on the card.

11. Total_Revolving_Bal: Total revolving balance on the card.

12. Total_Trans_Amt: Total transaction amount for the week.

13. Total_Trans_Vol: Total volume of transactions for the week.

14. Avg_Utilization_Ratio: Average utilization ratio of the card.

15. Use Chip: Indicates if a chip was used in transactions.

16. Exp Type: Expenditure type/category.

17. Interest_Earned: Interest earned on the card balance.

18. Delinquent_Acc: Number of delinquent accounts.

# DAX Calculations in Power BI

The following DAX queries were created in Power BI to further analyze and segment data:

1. AgeGroup

Classifies customers into age groups:
```
AgeGroup = SWITCH(TRUE(),

                customer[Customer_Age]<30,"20-30", 
                
                customer[Customer_Age] >= 30 && customer[Customer_Age] < 40, "30-40", 
                
                customer[Customer_Age] >= 40 && customer[Customer_Age] < 50, "40-50",
                
                customer[Customer_Age] >= 50 && customer[Customer_Age] < 60, "50-60",
                
                customer[Customer_Age] >= 60 && customer[Customer_Age] < 70, "60-70",
                
                customer[Customer_Age] >= 70, "70+",
                
                "Unknown"
                
                )
```

2. IncomeGroup
   
Classifies customers by income:

```
IncomeGroup = SWITCH(
                     TRUE(),
                     customer[Income] < 35000, "Low",
                     customer[Income] >= 35000 && customer[Income] <70000, "Medium",
                     customer[Income] >=70000, "High",
                     "Unknown"
                    )
```

3. Revenue Calculations

a. week_num2: Extracts the week number from Week_Start_Date

```
Week_num2 = WEEKNUM(credit_card[Week_Start_Date])
```

b. Revenue: Calculates total revenue based on various card-related metrics

```
Revenue = credit_card[Annual_Fees] +credit_card[Total_Trans_Amt] +credit_card[Interest_Earned]
```

c. Current_week_Revenue and Previous_week_Revenue: Compares weekly revenue

```
Current_Week_Revenue = CALCULATE(
    SUM(credit_card[Revenue]),
    FILTER(
        ALL(credit_card),
        credit_card[Week_num2] = 
        MAX(credit_card[Week_Num2])))
```
```
Previous_Week_Revenue = CALCULATE(
    SUM(credit_card[Revenue]),
    FILTER(
        ALL(credit_card),
        credit_card[Week_num2] = MAX(credit_card[Week_Num2])-1))
```

# SQL Server Updates and Data Preparation

# Credit Card Table

To prepare and update data in the credit_card table for weekly analysis:

```
-- Sample data retrieval
SELECT * FROM credit_card;
SELECT * FROM cc_add;

-- Change Qtr column data type to VARCHAR
ALTER TABLE credit_card ALTER COLUMN Qtr VARCHAR(50);

-- Change other columns as needed
ALTER TABLE credit_card ALTER COLUMN Annual_Fees INT;
ALTER TABLE credit_card ALTER COLUMN Total_Revolving_Bal INT;
ALTER TABLE credit_card ALTER COLUMN Total_Trans_Amt INT;

-- Update Qtr column values
UPDATE credit_card
SET Qtr = 
    CASE 
        WHEN CAST(Qtr AS DECIMAL(3, 0)) = 1 THEN 'Q1'
        WHEN CAST(Qtr AS DECIMAL(3, 0)) = 2 THEN 'Q2'
        WHEN CAST(Qtr AS DECIMAL(3, 0)) = 3 THEN 'Q3'
        WHEN CAST(Qtr AS DECIMAL(3, 0)) = 4 THEN 'Q4'
        ELSE Qtr
    END;

-- Rename Total_Trans_Vol column
EXEC sp_rename 'credit_card.Total_Trans_Vol', 'Total_Trans_Ct', 'COLUMN';

-- Insert new records from `cc_add` into `credit_card`
INSERT INTO credit_card (Client_Num, Card_Category, Annual_Fees, Activation_30_Days, Customer_Acq_Cost, Week_Start_Date, Week_Num, 
    Qtr, current_year, Credit_Limit, Total_Revolving_Bal, 
    Total_Trans_Amt, Total_Trans_Ct, Avg_Utilization_Ratio, Use_Chip, Exp_Type, Interest_Earned, Delinquent_Acc)
SELECT Client_Num, Card_Category, Annual_Fees, Activation_30_Days, Customer_Acq_Cost, Week_Start_Date, Week_Num, 
    Qtr, current_year, Credit_Limit, Total_Revolving_Bal, 
    Total_Trans_Amt, Total_Trans_Ct, Avg_Utilization_Ratio, Use_Chip, Exp_Type, Interest_Earned, Delinquent_Acc
FROM cc_add;
```

# Customer Table

To update the customer table with new data:

```
-- Retrieve data from `customer` and `cust_add`
SELECT * FROM customer;
SELECT * FROM cust_add;

-- Insert new records from `cust_add` into `customer`
INSERT INTO customer (Client_Num, Customer_Age, Gender, Dependent_Count, Education_Level, 
                         Marital_Status, state_cd, Zipcode, Car_Owner, House_Owner, 
                         Personal_loan, contact, Customer_Job, Income, Cust_Satisfaction_Score)
SELECT Client_Num, Customer_Age, Gender, Dependent_Count, Education_Level, 
       Marital_Status, state_cd, Zipcode, Car_Owner, House_Owner, 
       Personal_loan, contact, Customer_Job, Income, Cust_Satisfaction_Score
FROM cust_add;
```
# Dashboard Summary

# Key Metrics

1. Revenue: Total revenue generated from credit card transactions, amounting to 57M.
   
2. Transaction Amount: The cumulative amount spent using credit cards, totaling 46M.
   
3. Total Count: Total number of transactions recorded, amounting to 667K.
   
4. Total Interest: Total interest earned from credit card usage, amounting to 8.0M.

# Features & Filters

1. Filters: Interactive filters for Week_Start_Date, Gender (F/M), Quarter (Q1-Q4), and Card Category (Gold, Silver, Blue, Platinum).

2. Dynamic Visualizations: Includes bar and pie charts that dynamically update based on filter selection, providing deeper insights into segmented data.

# Visualizations

1. Quarterly Revenue & Transaction Count: Highlights revenue and transaction count per quarter, indicating a steady increase across Q1 to Q4.

2. Card Category Summary: Breakdown of revenue, transaction amount, and interest earned across card categories.

3. Revenue by Expenditure Type: Analysis of spending categories, such as Bills, Entertainment, and Fuel, where "Bills" leads with the highest revenue.

4. Revenue by Customer Job: Identifies customer jobs contributing the most to revenue, with "Businessman" at the top.

5. Revenue by Education Level: Analyzes revenue contribution based on education level, where "Graduate" customers generate the highest revenue.

6. Card Usage (Chip/Swipe/Online): Distribution of revenue by transaction type, indicating "Swipe" as the most preferred mode.

# Insights

1. Quarterly Performance: A consistent rise in revenue and transaction count from Q1 to Q4 suggests increased card usage over time.

2. Customer Demographics: Business professionals and graduates generate the highest revenue, reflecting a high credit usage in these segments.

3. Expenditure Type: Bills and entertainment are the primary areas where customers utilize credit cards, indicating potential areas for targeted offers.

4. Card Usage Patterns: Swipe transactions dominate, followed by Chip usage, suggesting a preference for physical over online transactions.

# Conclusion

This dashboard offers a comprehensive view of credit card transaction data, enabling data-driven decisions to optimize credit offerings, tailor marketing strategies, and enhance customer satisfaction. By focusing on high-revenue demographics and expenditure types, businesses can leverage this data for improved customer engagement and revenue growth.


