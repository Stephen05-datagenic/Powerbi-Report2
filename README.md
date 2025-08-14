LOAN DEFAULT ANALYSIS DASHBOARD

# Dashboard Link :
https://app.powerbi.com/groups/741a852f-fd69-41b7-ac51-ed2a7c5e1cee/reports/b4d8f901-a055-4779-99a4-b96a58a48f32/10e756261d28bde839e6?experience=power-bi

# Business Objective
The objective of this project is to conduct a comprehensive loan default analysis to understand borrower behavior, financial risk, and repayment trends. By leveraging Power BI visualizations, the dashboard aims to:
- Identify high-risk borrower segments.
- Understand loan distribution patterns by demographic and financial characteristics.
- Track default trends over time to inform risk management strategies.
- Provide data-driven insights for lending policy optimization.

# KPIs Requirement
- Total Loan Amount
- Average Loan Amount
- Median Loan Amount by Credit Score
- Default Rate (%) (overall and by segment)
- Average Income (overall and by defaulted borrowers)
- Number of Loans (by education type, applicant type, etc.)

# Data Cleaning & Preparation Steps
1. Import raw dataset (SQL/SSMS) into Power Query.

2. Enable column quality, distribution, profiling for entire dataset.

3. Apply text cleaning (trim, remove symbols, consistent casing).

4. Correct data types (numeric, categorical, date).

4. Handle nulls, duplicates, outliers, unexpected values.

5. Validate ranges (min/max) for financial columns.

6. Rename & reorder columns for better readability.

7. Created Calculated Columns using DAX.
- Age Group = 
IF('Loan_default'[Age] <= 19 ,"Teen",
IF('Loan_default'[Age]<= 39 ,"Adults",
IF('Loan_default'[Age] <= 59 ,"Middle Age Adults", "Senior Citizens")))

- Credit Score Bins = 
 IF(Loan_default[CreditScore] <=400, "Very Low",
 IF(Loan_default[CreditScore] <= 450,"Low",
 IF(Loan_default[CreditScore] <= 650, "Medium", "High")))

- Income Bracket = 
 SWITCH(
    TRUE(),Loan_default[Income]<30000,"Low Income",
    'Loan_default'[Income]>=30000 && 'Loan_default'[Income]     <60000, 
    "Middle Income"
    ,'Loan_default'[Income]>=60000, "High Income")

- Year = YEAR(Loan_default[Loan Date (DD/MM/YYYY)].[Date])

8. Load cleaned dataset into Power BI model.

# Page-Wise Business Requirements
- Page 1: Loan Default & Overview
- Objective: Provide a high-level summary of loan distribution, applicant profiles, and overall default patterns.

## Business Questions:

1. Which loan purposes generate the highest loan amounts?
2. Which employment types have the highest income and default rates?
3. How do age groups differ in loan behavior?
4. What are the default rate trends across years?

# Measures (DAX Calculations)
- Loan Amt By Purpose = 
 SUMX(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanAmount]))),
 'Loan_default'[LoanAmount])

 - AVG Income By Employment Typ = 
 CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType]))

 - Default Rate By Employment Typ = 
 VAR totalrecords = COUNTROWS(ALL('Loan_default'))
 VAR defaultcases = COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE()))

 RETURN 
 CALCULATE(DIVIDE(defaultcases,totalrecords),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType])) *100

 - AVG Loan By Age Group = 
AVERAGEX(VALUES('Loan_default'[Age Group]),AVERAGE('Loan_default'[LoanAmount]))

- Default Rate By Year = 

 VAR totalrecords = CALCULATE(COUNTROWS('Loan_default'),
 ALLEXCEPT('Loan_default','Loan_default'[Year]))

 VAR defaultcases = CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())),ALLEXCEPT('Loan_default',Loan_default[Year]))

RETURN
DIVIDE(defaultcases,totalrecords)*100

# KPIs & Visualizations:

- Loan Amount by Purpose → Line Chart.
- Average Income by Employment Type → Line/Shaded Chart.
- Default Rate by Employment Type → Line/Shaded Chart.
- Average Loan Amount by Age Group → Line/Shaded Chart.
- Default Rate by Year (2013–2018) → Line/Shaded Chart.

Impact: Helps in identifying loan purposes, employment segments, and age groups that contribute most to lending exposure and repayment risks.

# Page 1 Snap : https://github.com/user-attachments/assets/f9930a72-d67e-42f4-a777-a8be34eff53d

- Page 2: Applicant Demographics & Financial Profile
- Objective: Understand the impact of demographics, education, and credit score on loan approvals and amounts.

## Business Questions:

1. How does median loan amount vary by credit score?
2. How does marital status and age group affect loan size (especially for high credit score applicants)?
3. What is the loan distribution among middle-aged borrowers with/without dependents?
4. How does education level affect number of loans disbursed?

# Measures (DAX Calculations)
- Median By Credit Score Bins = 
MEDIANX('Loan_default',Loan_default[LoanAmount])

- Avg Loan Amount(High Credit) = 
 AVERAGEX(FILTER('Loan_default',Loan_default[Credit Score Bins]="High"),Loan_default[LoanAmount]) 

- AVG Loan By Age Group = 
AVERAGEX(VALUES('Loan_default'[Age Group]),AVERAGE('Loan_default'[LoanAmount]))

- Loans By Edu Type = 
 COUNTROWS(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanID]))))


# KPIs & Visualizations:
- Median Loan Amount by Credit Score Category → Area Chart.
- Loan Amount by Age Group & Marital Status (High Credit Score Applicants) → Donut Chart/Pie Chart.
- Average Income by Age Group → Line Chart.
- Loan Amount for Middle-Age Adults (Dependents vs Non-dependents) → Bar Chart.
- Number of Loans by Education Type → Line Chart.

Impact: Enables banks to design lending policies tailored to borrower demographics, creditworthiness, and financial reliability.

# Page 2 Snap : https://github.com/user-attachments/assets/a00b04c9-538d-4443-9923-67881807d590

- Page 3: Financial Risk Metrics
- Objective: Deep dive into default risk by analyzing credit score, mortgage status, income levels, and applicant type.

## Business Questions:

1. How does default rate vary across credit score categories?
2. Do mortgage holders show lower risk than non-mortgage applicants?
3. What income levels and loan sizes are most associated with defaults?
4. Are single applicants riskier than joint applicants?

# Measures (DAX Calculations)
- YOY Loan Amount Change = 
DIVIDE(
    CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year] = 
    YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))) -

    CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year] = 
    YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1)

    , CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year] = 
    YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1),0) *100

- YOY Default Loans Change = 
 DIVIDE(
    CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())),
    'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)])))-

    CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1)

    ,CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default] = TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan Date (DD/MM/YYYY)]))-1),0) *100

- YTD Loan Amt (c/b,m,s) = 
    
 CALCULATE(SUM('Loan_default'[LoanAmount]),DATESYTD('Loan_default'[Loan Date (DD/MM/YYYY)]),ALLEXCEPT('Loan_default',Loan_default[Credit Score Bins],Loan_default[MaritalStatus]))


## KPIs & Visualizations:

- YOY Loan Amount Change by Year –> Line/Area Chart

- YOY Default Loans Change by Year –> Line/Area Chart

- YTD Loan by Credit Score Bins and Marital Status –> Ribbon Chart

- Loan Distribution by Income Bracket & Employment Type –> Decomposition Tree


Impact: Provides actionable insights into borrower risk, enabling banks to reduce non-performing assets (NPAs) and improve credit risk models.

# Page 3 Snap : https://github.com/user-attachments/assets/0f88c6ca-5da8-4095-b940-c05dfb0e5e8d


# Expected Business Outcomes
- Risk Segmentation: Identify high-risk borrower categories (by credit score, applicant type, income).

- Policy Optimization: Tailor lending policies to reduce defaults.

- Profitability: Focus lending on lower-risk segments for sustainable revenue.

- Decision Support: Provide executives and risk managers with clear, data-driven dashboards for strategic decision-making.


