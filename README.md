# Employee-Health

## 📚 Table of Contents
- [Overview](#overview)
- [Tasks](#tasks)
- [Questions and Solutions](#questions-and-solutions)
- [Dashboard](#dashboard) 

## Overview
This is a data-driven analysis of an HR dataset focused on absenteeism and employee health. The project aims to develop an approach for determining how to provide bonuses and incentives to healthy employees by analyzing absenteeism patterns and health metrics.

## Tasks
- Provide a list of Healthy Individuals & Low Absenteeism for our healthy bonus program
  - Total Budget of $1000
- Calculate a Wage Increase or annual compensation for Non-Smokers
  - Increase budget of $983,221 for all Non_Smokers
- Solve other questions based on Individuals health and Absenteeism.
- Create a Dashboard to understand Absenteeism at work.

## Questions and Solutions

**1. Who are the healthiest employees?**

````sql
SELECT * 
FROM absenteeism_at_work
WHERE Social_drinker = 0 AND Social_smoker = 0
AND Body_mass_index BETWEEN 18.5 AND 24.9
AND Absenteeism_time_in_hours < (SELECT AVG(Absenteeism_time_in_hours) FROM absenteeism_at_work)
;
````
#### Steps:
- Want to find non drinkers and smokers so we look for the value of 0 in their respected categories. 
- A "healthy" BMI according to [The National Heart, Lung, and Blood Institute (NHLBI)](https://www.nhlbi.nih.gov/health/educational/lose_wt/BMI/bmicalc.htm) is between 18.5 AND 24.9.
- Include the employees that have below the average Absenteeism_time_in_hours.   

#### Answer:
- Employees 41, 52, 53, 57, 66, 68, 70, 74, 77, 82, 89, 91, 93, 96, 103, 107, 109, 113, 114, 118, 119, 120, 121, 123, 133, 136, 138, 147, 148, 149, 151, 154, 172, 175, 177, 182, 184, 186, 190, 210, 217, 236, 263, 315, 316, 340, 349, 359, 372, 429, 436, 438, 441, 445, 454, 457, 461, 462, 504, 505, 516, 519, 522, 525, 527, 531, 532, 540, 544, 548, 553, 558, 559, 560, 561, 567, 568, 569, 575, 579, 581, 587, 590, 592, 600, 601, 605, 612, 615, 621, 625, 627, 628, 634, 641, 645, 652, 660, 667, 670, 674, 680, 685, 690, 697, 701, 706, 708, 714, 718, 720 are considered "healthy".

***

**2. Wage Increase or annual compensation for Non-Smokers?**

````sql
SELECT COUNT(*) as non_smoker, (983221 / 686) / 2080 AS per_hour_increase, (983221 / 686) AS total_per_year_increase
FROM absenteeism_at_work
WHERE Social_smoker = 0
;
````
#### Steps:
- Want to find non smokers so we look for the value of 0 in that category. 

#### Answer:
- A $0.68907056 increase per hour for non smokers.
- A $1433.2668 increase yearly for non smokers.

(Takes into account that an average work week of 40hrs per week for 52 weeks in a year)

***

**3. What are the top 5 reasons for absenteeism**

````sql
SELECT Reason, COUNT(*) as total
FROM absenteeism_at_work AS a
JOIN reasons AS r
	ON a.Reason_for_absence = r.number
GROUP BY Reason
ORDER BY total DESC
LIMIT 5
;
````
#### Steps:
- **JOIN** the `absenteeism_at_work` and the `reasons` tables using the Reason_for_absence and number.

#### Answer:
| Reason | total |
| ----------- | ----------- |
| medical consultation   | 149          |
| dental consultation    | 112          |
| physiotherapy          | 69          |
| Diseases of the musculoskeletal system and connective tissue            | 55          |
| Unkown            | 43          |

***

**4. Absenteeism vs. Distance from Residence to Work**

````sql
SELECT 
    CASE 
        WHEN Distance_from_Residence_to_Work <= 10 THEN '0-10 km'
        WHEN Distance_from_Residence_to_Work BETWEEN 11 AND 20 THEN '11-20 km'
        WHEN Distance_from_Residence_to_Work BETWEEN 21 AND 30 THEN '21-30 km'
        WHEN Distance_from_Residence_to_Work BETWEEN 31 AND 40 THEN '31-40 km'
        ELSE '40+ km' 
    END AS Commute_Range,
    AVG(Absenteeism_time_in_hours) AS avg_absenteeism_hours
FROM 
    absenteeism_at_work
GROUP BY 
    Commute_Range
ORDER BY 
    Commute_Range
;
````
#### Steps:
- Use a **CASE** statement to categorize the distance employees commute to work.
- Use **AVG** to find average absenteeism time (in hours) for each Commute_Range category.

#### Answer:
| Commute_Range | avg_absenteeism_hours |
| ----------- | ----------- |
| 0-10 km     | 6.1311      |
| 11-20 km    | 10.0958     |
| 21-30 km    | 5.6682      |
| 31-40 km    | 8.8987      |
| 40+ km      | 5.2238      |

- Seems to be no clear correlation between the commute distance and absenteeism. Absenteeism fluctuates somewhat erratically across different commute distance ranges.
      
***

**5. What is the average Absenteeism by Month**

````sql
SELECT 
    Month_of_absence, 
    AVG(Absenteeism_time_in_hours) AS avg_absenteeism_hours
FROM 
    absenteeism_at_work
WHERE 
    Month_of_absence != 0
GROUP BY 
    Month_of_absence
ORDER BY 
    Month_of_absence
;
````
#### Steps:
- Use **AVG** to find average absenteeism time (in hours) for each month category.                                    

#### Answer:
| Month_of_absence | avg_absenteeism_hours |
| ----------- | ----------- |
| 1  | 10.9552        |
| 2  | 5.3333         |
| 3  | 5.5094         |
| 4  | 4.9155         |
| 5  | 7.5079         |
| 6  | 8.4490         |
| 7  | 4.4400         |
| 8  | 4.0833         |
| 9  | 8.7931         |
| 10  | 9.0943        |
| 11  | 6.2500        |
| 12  | 7.6111        |
      
***

**6. Social smokers Vs. Non-smokers**

````sql
SELECT 
    Social_smoker,
    AVG(Absenteeism_time_in_hours) AS avg_absenteeism_hours
FROM 
    absenteeism_at_work
GROUP BY 
    Social_smoker
;
````
#### Steps:
- Use **AVG** to find average absenteeism time (in hours) for each smoker category.

#### Answer:
| social_smoker | avg_absenteeism_hours |
| ----------- | ----------- |
| 0      | 6.9577           |
| 1      |    6.5000        |
      
***

## Dashboard

- Overview: This dashboard provides insights into employee absenteeism data.

<img src="https://github.com/Tlcke77/pics/blob/main/1731562881358-f3403f3a-42c5-4a64-9e37-eb4531c9367e_1.jpg" alt="Image" width="800" height="520">
