# customer_churn_analysis
## A collection of the SQL queries used to analyze customer churn for Maven telecommunication company 

- Analyzing Maven Telecomm data for customer churn patterns

- *Counting total customers*
```SQL
SELECT  COUNT(Customer_ID) As total_customers
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
```

- *How many customers joined the company in the last quarter?*
```SQL
SELECT Customer_Status,
       COUNT(Customer_Status) AS customer_count,
       ROUND(COUNT(Customer_Status) * 100 / SUM(COUNT(Customer_Status)) OVER(), 1) AS status_percent
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 GROUP BY Customer_Status
 ORDER BY status_percent DESC
```

- What seem to be the key drivers of customer churn?

  *What percentage of churned customers subscribe to internet service?*
```SQL
SELECT Internet_Service,
       COUNT(Internet_Service) AS churn_count,
       ROUND(COUNT(Internet_Service) * 100 / SUM(COUNT(Internet_Service)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Internet_Service
ORDER BY churn_percentage DESC
```

- What percent of churned customers on internet service subscribe to online security?
```SQL
SELECT Online_Security,
       ROUND(COUNT(Online_Security) * 100 / SUM(COUNT(Online_Security)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Online_Security
ORDER BY churn_percentage  DESC
```

- What percent of churned customers on internet service subscribe to Streaming TV? --
```SQL
SELECT Streaming_TV,
       ROUND(COUNT(Streaming_TV) * 100 / SUM(COUNT(Streaming_TV)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Streaming_TV
ORDER BY churn_percentage  DESC
```

- What percent of churned customers on internet service subscribe to Streaming Music?
```SQL
SELECT Streaming_Music,
       ROUND(COUNT(Streaming_Music) * 100 / SUM(COUNT(Streaming_Music)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Streaming_Music
ORDER BY churn_percentage  DESC
```

- What percent of churned customers on internet service subscribe to Streaming Movies?
```SQL
SELECT Streaming_Movies,
       ROUND(COUNT(Streaming_Movies) * 100 / SUM(COUNT(Streaming_Movies)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Streaming_Movies
ORDER BY churn_percentage  DESC
```

- What percent of churned customers on internet service subscribe to the Device Protection Plan
```SQL
SELECT Device_Protection_Plan,
       ROUND(COUNT(Device_Protection_Plan) * 100 / SUM(COUNT(Device_Protection_Plan)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Device_Protection_Plan
ORDER BY churn_percentage  DESC
```

- Which internet type did churn customers have?
```SQL
SELECT Internet_Type,
       ROUND(COUNT(Internet_Type) * 100 / SUM(COUNT(Internet_Type)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Internet_Type
ORDER BY churn_percentage DESC
```

- Are churn customers getting Premium Tech Support?
```SQL
SELECT Premium_Tech_Support,
       ROUND(COUNT(Premium_Tech_Support) * 100 / SUM(COUNT(Premium_Tech_Support)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Premium_Tech_Support
ORDER BY churn_percentage  DESC
```

- Which contract is dominating among churned customers?
```SQL
SELECT Contract,
       COUNT(*) AS churn_count,
       ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Contract
ORDER BY churn_percentage  DESC
```

- Which marketing offer did most churn customers have or accept?
```SQL
SELECT Offer,
       ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Offer
ORDER BY churn_percentage DESC 
```

- City with the highest churn rate?
  
-  *What we've to consider the telecom company has customers across 1106 cities 
     with customers below 30 in 1088 cities and customers above 30 in these 18 cities,
     decisions have to be made either to involve all cities or those with customers above 30
     to avoid bias*

```SQL
SELECT City,
       COUNT(*) AS churn_customers_no,
       ROUND(COUNT(City) * 100 / SUM(COUNT(City)) OVER(), 1) AS percentage_churned
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned" 
GROUP BY City
ORDER BY percentage_churned DESC
LIMIT 5
```

- What's a typical churned customer tenure

  *Creating a TEMP TABLE of churned customers with Tenure categorization*
```SQL  
WITH churn_data as (
    SELECT *,
    CASE 
        WHEN Tenure_in_Months  <= 6 THEN "6 Months"
        WHEN Tenure_in_Months  <= 12 THEN "1 Year"
        WHEN Tenure_in_Months  <= 24 THEN "2 Years"
        WHEN Tenure_in_Months  <= 36 THEN "3 Years"
        ELSE "> 3 Years "
        END AS Tenure,
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data` AS customerchurn_data
 WHERE Customer_Status = "Churned"
)
 *Querying the TEMP TABLE to answer business questions*
 *What's a typical tenure of a churned customer*

```SQL
SELECT churn_data.Tenure,
     ROUND(COUNT(churn_data.Customer_ID) * 100 / SUM(COUNT(churn_data.Customer_ID)) OVER(),1) AS Percentage_Churned
   FROM churn_data
   GROUP BY churn_data.Tenure
   ORDER BY Percentage_Churned DESC
```

- What percentage of total revenue was lost to churned customers?
```SQL
SELECT Customer_Status,
       ROUND(SUM(Total_Revenue),0) AS revenue_per_status,
       ROUND(SUM(Total_Revenue) * 100 / SUM(SUM(Total_Revenue)) OVER(), 1) AS Revenue_percentage  
 FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
 GROUP BY Customer_Status
```

 - Why are customers leaving?
 
 - *What's the most dominant general reason for churning?*
```SQL
SELECT Churn_Category,
       ROUND(SUM(Total_Revenue), 0) AS churned_revenue,
       ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(), 1) AS churn_percentage 
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Churn_Category 
ORDER BY churn_percentage DESC
```

- Specific reasons why customers churn
```SQL
SELECT Churn_Category,
       Churn_Reason,
       ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(),1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Churn_Category,
         Churn_Reason
         ORDER BY churn_percentage DESC
         LIMIT 5
```

- What internet type do customers lose to competitors?
```SQL
SELECT Internet_Type,
       Churn_Category,
       ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(),1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
     AND Churn_Category = "Competitor"
GROUP BY  Churn_Category,
          Internet_Type
         ORDER BY churn_percentage DESC
         LIMIT 5
```

- Is the company losing high-value customers? If so, how can they retain them?

- *creating criteria and filtering high-value customers into a TEMP TABLE*
```SQL
WITH high_value_data as (
  SELECT *
  FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
  WHERE Tenure_in_Months >= 12 AND 
        Monthly_Charge >= 
        (
    SELECT (PERCENTILE_DISC(Monthly_Charge, 0.5)OVER())
    FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
    LIMIT 1
  )
   AND
        Number_of_Referrals >0
)

*What percentage of high-value customers churned or stayed?*

SELECT high_value_data.Customer_Status,
       COUNT(*) AS no_customer,
       ROUND(COUNT(high_value_data.Customer_Status) * 100 / SUM(COUNT(high_value_data.Customer_Status)) OVER(),1) AS customer_percent
FROM high_value_data
GROUP BY high_value_data.Customer_Status
ORDER BY customer_percent DESC
```

- What's the possibility that high-value customers that stayed might churn?

  *Categorizing high-value customers that stayed into three Risk groups of possible churn, 'High', 'Medium', and 'Low', 
   setting conditions for each group and finding the percentage of high-value customers that stayed*
```SQL
      SELECT 
      CASE 
          WHEN (num_conditions >= 3) THEN 'High Risk'
          WHEN num_conditions = 2 THEN 'Medium Risk'
          ELSE 'Low Risk'
          END AS Risk_Level,
          COUNT(Customer_ID) AS customer_count,
          ROUND(COUNT(Customer_ID) * 100 / SUM(COUNT(Customer_ID)) OVER(),1) AS risk_percent
      FROM 
          (
            SELECT Customer_ID,
                  SUM(CASE WHEN Offer = "Offer E" OR Offer ="None" THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Internet_Type = "Fiber Optic" THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Premium_Tech_Support = FALSE THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Contract = "Month-to-Month" THEN 1 ELSE 0 END) AS num_conditions 

      FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
      WHERE Tenure_in_Months >= 9 AND 
            Monthly_Charge >= 
        (
          SELECT (PERCENTILE_DISC(Monthly_Charge, 0.5)OVER())
          FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
          LIMIT 1
        ) AND
             Number_of_Referrals > 0 AND
             Customer_Status = "Stayed"
      GROUP BY Customer_ID
      HAVING      SUM(CASE WHEN Offer = "Offer E" OR Offer = "None" THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Internet_Type = "Fiber Optic" THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Premium_Tech_Support = FALSE THEN 1 ELSE 0 END)+
                  SUM(CASE WHEN Contract = "Month-to-Month" THEN 1 ELSE 0 END) >= 1
          ) AS subquery
      GROUP BY    CASE 
          WHEN (num_conditions >= 3) THEN 'High Risk'
          WHEN num_conditions = 2 THEN 'Medium Risk'
          ELSE 'Low Risk'
          END       
```

-  What's a customer profile for a customer that churned, stayed, and joined, are they different?
*A churned customer profile*

*What's the age of the customer profile that churned?*
```SQL
SELECT 
       CASE
       WHEN Age <= 30 THEN 'Adult'
       WHEN Age <= 50 THEN 'Middle_Age'
       WHEN Age >= 51 THEN 'Old_Age'
       END AS Age_Group,
       ROUND(COUNT(Age) * 100 / SUM(COUNT(Age)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Age_Group
ORDER BY churn_percentage  DESC
```

- What's the customer profile that churned?
```SQL
SELECT Gender,
       ROUND(COUNT(Gender) * 100 / SUM(COUNT(Gender)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Gender
ORDER BY churn_percentage  DESC
```

- What's the marital status of a customer profile that churned?
```SQL
SELECT Married,
       ROUND(COUNT(Married) * 100 / SUM(COUNT(Married)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Married
ORDER BY churn_percentage  DESC
```

- How many dependents does a customer profile that churned have?
```SQL
SELECT 
        CASE 
        WHEN Number_of_Dependents = 0 THEN 'Yes'
        ELSE 'No'
        END AS Dependents,
ROUND(COUNT(Number_of_Dependents) * 100 / SUM(COUNT(Number_of_Dependents)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Dependents
ORDER BY churn_percentage  DESC
```

- Are churned customers referring others to the business?
```SQL
SELECT 
       CASE 
       WHEN Number_of_Referrals = 0 THEN 'Yes'
       ELSE 'No'
       END AS Referral,
       ROUND(COUNT(Number_of_Referrals) * 100 / SUM(COUNT(Number_of_Referrals)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Referral
ORDER BY churn_percentage  DESC
```

- Did customers' profile that churned subscribe for phone service?
```SQL
SELECT Phone_Service,
       ROUND(COUNT(Phone_Service) * 100 / SUM(COUNT(Phone_Service)) OVER(), 1) AS churn_percentage
FROM `customer-churn-analysis-381016.maven_telecomm_data.telecom_customerchurn_data`
WHERE Customer_Status = "Churned"
GROUP BY Phone_Service
ORDER BY churn_percentage  DESC
```
