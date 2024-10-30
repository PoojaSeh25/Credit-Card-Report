# Credit Card Report 

Weekly Status Report

# Project Objective:
To develop a comprehensive credit card weekly dashboard that provides real-time insights into key performance metrics and trends, enabling stakeholders to monitor and analyze credit card operations effectively.

# Table Structures
# 1. Customer Table (customer)

The customer table holds customer-specific details:

Client_Num: Unique identifier for each customer.

Customer_Age: Age of the customer.

Gender: Customer’s gender.

Dependent_Count: Number of dependents for each customer.

Education_Level: Customer’s highest education level.

Marital_Status: Marital status of the customer.

state_cd: State code representing customer’s location.

Zipcode: Customer’s residential postal code.

Car_Owner: Indicates if the customer owns a car (Yes/No).

House_Owner: Indicates if the customer owns a house (Yes/No).

Personal_loan: Indicates if the customer has a personal loan.

contact: Contact preference or status of the customer.

Customer_Job: Job type or title of the customer.

Income: Annual income of the customer.

Cust_Satisfaction_Score: Satisfaction score based on customer feedback.

# 2. Credit Card Table (credit_card)

The credit_card table holds transaction and financial details for each customer:

Client_Num: Unique identifier that links to customer table.

Card_Category: Category/type of the credit card.

Annual_Fees: Annual fee associated with the card.

Activation_30_Days: Indicates if the card was activated within 30 days.

Customer_Acq_Cost: Cost to acquire the customer.

Week_Start_Date: Start date of the week for transaction records.

Week_Num: Week number in the calendar year.

Qtr: Financial quarter.

current_year: Current year of transaction.

Credit_Limit: Credit limit on the card.

Total_Revolving_Bal: Total revolving balance on the card.

Total_Trans_Amt: Total transaction amount for the week.

Total_Trans_Vol: Total volume of transactions for the week.

Avg_Utilization_Ratio: Average utilization ratio of the card.

Use Chip: Indicates if a chip was used in transactions.

Exp Type: Expenditure type/category.

Interest_Earned: Interest earned on the card balance.

Delinquent_Acc: Number of delinquent accounts.

# DAX Calculations in Power BI

The following DAX queries were created in Power BI to further analyze and segment data:

AgeGroup

Classifies customers into age groups:

AgeGroup = SWITCH(TRUE(),

                customer[Customer_Age]<30,"20-30", 
                
                customer[Customer_Age] >= 30 && customer[Customer_Age] < 40, "30-40", 
                
                customer[Customer_Age] >= 40 && customer[Customer_Age] < 50, "40-50",
                
                customer[Customer_Age] >= 50 && customer[Customer_Age] < 60, "50-60",
                
                customer[Customer_Age] >= 60 && customer[Customer_Age] < 70, "60-70",
                
                customer[Customer_Age] >= 70, "70+",
                
                "Unknown"
                
                )

