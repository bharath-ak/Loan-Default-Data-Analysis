# 📊 Loan Default Analysis Dashboard

This Power BI project analyzes a loan dataset to understand borrower characteristics, default trends, and financial behaviors.

## 🔗 Live Dashboard

👉 [View Power BI Report](https://app.powerbi.com/view?r=eyJrIjoiYzNmMDEyNmEtMzNmMS00NDI1LTg0NDktMDNiMGM2YWVkMmMyIiwidCI6ImM2MDlhZTI5LTBkMjQtNDU4My04NzRjLTFkYTVhMTg5OTk1ZSJ9)

---

## 🧩 Problem Statement
Financial institutions face significant risks due to loan defaults, which can impact profitability and overall financial stability. Understanding the patterns behind loan defaults — including borrower demographics, credit scores, income levels, and loan purposes — is crucial for better risk assessment and lending decisions.

This project aims to analyze a Loan Default Dataset to uncover key trends and insights about:
- Borrower profiles most likely to default
- Relationships between income, employment, and loan performance
- The influence of credit scores, education, and marital status on default rates
- Year-over-year changes in loan amounts and default rates

The goal is to support data-driven decision-making by visualizing the critical factors influencing loan defaults and providing actionable insights for risk mitigation and strategic lending.

---

## 🪜 Steps Involved
1. Created Dataflow using Power BI Service.

2. Imported data into Power BI Desktop from the Dataflow.

3. Reviewed column definitions and dataset documentation.

4. Switched to “Column profiling based on entire dataset”.
   
5. Enabled full data profiling in Power Query Editor:
- Checked “Column distribution”
- Checked “Column quality”
- Checked “Column profile”

6. Inserted shapes for titles: "Loan Default & Overview", "Applicant Demographics & Financial Profile", "Financial Risk Metrics"

7. Created a calculated column to extract Year from Loan_Date_DD_MM_YYYY.
- Year = YEAR('Loan_default'[Loan_Date_DD_MM_YYYY].[Date])

8. Created a measure: Loan Amount by Purpose and visualized it with a line chart (X-axis: Loan Purpose).
- Loan Amount by Purpose = 
    SUMX(FILTER(Loan_default, NOT(ISBLANK(Loan_default[LoanAmount]))), Loan_default[LoanAmount])

9. Created measure: Average Income by Employment Type with a line chart (X-axis: Employment Type).
- Average Income by Employment Type = 
    CALCULATE(AVERAGE(Loan_default[Income]), ALLEXCEPT(Loan_default, Loan_default[EmploymentType]))

10. Created measure: Default Rate by Employment Type with a line chart (X-axis: Employment Type).
- Default Rate by Employment Type = 
    VAR totalRecords = COUNTROWS(Loan_default)
    VAR defaultCases = COUNTROWS(FILTER(Loan_default, Loan_default[Default] = TRUE()))
    RETURN CALCULATE(DIVIDE(defaultCases, totalRecords), ALLEXCEPT(Loan_default, Loan_default[EmploymentType])) * 100
    
11. Created a new column: Age Group based on Age.
- Age Group = 
    IF(Loan_default[Age]<19,"Teen",
        IF(Loan_default[Age]<=39,"Young Adults",
            IF(Loan_default[Age]<=59,"Adults",
                "Senior Citizens")))
    
12. Created measure: Average Loan by Age Group, plotted on a line chart with Age Group as X-axis.
- Average Loan by Age Group = 
    AVERAGEX(VALUES(Loan_default[Age Group]), AVERAGE(Loan_default[LoanAmount]))
    
13. Created measure: Default Rate by Year, plotted on a line chart with Year as X-axis.
- Default Rate by Year = 
    VAR totalLoans = CALCULATE(COUNTROWS(Loan_default), ALLEXCEPT(Loan_default, Loan_default[Year]))
    VAR default = CALCULATE(COUNTROWS(FILTER(Loan_default, Loan_default[Default] = TRUE())), ALLEXCEPT(Loan_default, Loan_default[Year]))
    RETURN DIVIDE(default, totalLoans) * 100
    
14. Created a new column: Credit Score Bins.
- Credit Score Bins = 
    IF(Loan_default[CreditScore]<=400,"Very Low",
        IF(Loan_default[CreditScore]<=450,"Low",
            IF(Loan_default[CreditScore]<=650,"Medium",
                "High")))
    
15. Created measure: Median by Credit Score Bins, plotted on a line chart with Credit Score Bins as X-axis.
- Median by Credit Score Bins = 
    MEDIANX(Loan_default, Loan_default[LoanAmount])
  
16. Created measure: Average Loan Amount (High Credit), built pie chart with:
- Values: Average Loan Amount (High Credit)
- Legend: Age Group
- Details: Marital Status
- Average Loan Amount (High Credit) = 
    AVERAGEX(FILTER(Loan_default, Loan_default[Credit Score Bins] = "High"), Loan_default[LoanAmount])
  
17. Created measure: Total Loan (Credit Bins) for "Young Adults" and visualized in a line chart (X-axis: Credit Score Bins).
- Total Loan (Credit Bins) = 
    CALCULATE(SUM(Loan_default[LoanAmount]), 
        Loan_default[Age Group] = "Young Adults",
        ALLEXCEPT(Loan_default, Loan_default[Age], Loan_default[Age Group], Loan_default[CreditScore], Loan_default[Credit Score Bins]))
    
18. Created measure: Total Loan (Adults) and visualized in a clustered column chart with:
- Y-axis: Total Loan (Adults)
- X-axis: Has Mortgage
- Legend: Has Dependent
- Total Loan (Adults) = 
    SUMX(FILTER(Loan_default, Loan_default[Age Group] = "Adult"), Loan_default[LoanAmount])

19. Created measure: Loan by Education Type and visualized in a line chart with Education Type as X-axis.
- Loan by Education Type = 
    COUNTROWS(FILTER(Loan_default, NOT(ISBLANK(Loan_default[LoanID]))))

20. Created YOY Loan Amount Change and visualized in a line chart with Year on X-axis.
- YOY Loan Amount Change = 
    DIVIDE(
        CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) - 
        CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
        CALCULATE(SUM('Loan_default'[LoanAmount]), 'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1),
        0
    ) * 100

21. Created YOY Default Loan Change and visualized in a line chart with Year on X-axis.
- YOY Default Loan Change = 
    DIVIDE(
        CALCULATE(COUNTROWS(FILTER(Loan_default, Loan_default[Default] = TRUE())), Loan_default[Year] = YEAR(MAX(Loan_default[Loan_Date_DD_MM_YYYY]))) -
        CALCULATE(COUNTROWS(FILTER(Loan_default, Loan_default[Default] = TRUE())), Loan_default[Year] = YEAR(MAX(Loan_default[Loan_Date_DD_MM_YYYY])) - 1),
        CALCULATE(COUNTROWS(FILTER(Loan_default, Loan_default[Default] = TRUE())), Loan_default[Year] = YEAR(MAX(Loan_default[Loan_Date_DD_MM_YYYY])) - 1),
        0
    ) * 100

22. Created measure: YTD Loan Amount, used in a ribbon chart with:
- Y-axis: YTD Loan Amount
- X-axis: Credit Score Bins
- Legend: Marital Status
- YTD Loan Amount = 
    CALCULATE(
        SUM(Loan_default[LoanAmount]),
        DATESYTD(Loan_default[Loan_Date_DD_MM_YYYY].[Date]),
        ALLEXCEPT(Loan_default, Loan_default[Credit Score Bins], Loan_default[MaritalStatus])
    )
  
23. Created new column: Income Bracket based on Income.
- Income Bracket = 
    SWITCH(
        TRUE(),
        Loan_default[Income]<30000,"Low",
        Loan_default[Income]>=30000 && Loan_default[Income]<60000,"Medium",
        Loan_default[Income]>60000,"High")
  
24. Built decomposition tree:
- Analyze: Loan Amount
- Explain by: Income Bracket, Employment Type

25. Published the Power BI report.

26. Set up scheduled refresh for Dataflow.
