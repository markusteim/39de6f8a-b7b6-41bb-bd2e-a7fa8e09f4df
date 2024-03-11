# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*


# Overview American University


 ```sql titles
 select * from hotels.titles 
 WHERE date >= '2009-01-01' AND date <= '2023-12-31'
 ```


1. Aesthetics (<Value data={category_propositions} column=percentage row=0/>%)
2. Belonging & welcomed (<Value data={category_propositions} column=percentage row=1/>%)
3. Comfort & clean (<Value data={category_propositions} column=percentage row=2/>%)
4. Empowerment, success & influence (<Value data={category_propositions} column=percentage row=3/>%)
5. Fun & stress-free (<Value data={category_propositions} column=percentage row=4/>%)
6. Impressions (<Value data={category_propositions} column=percentage row=5/>%)
7. Safety (<Value data={category_propositions} column=percentage row=6/>%)
8. Self-worth, pride & self-awareness (<Value data={category_propositions} column=percentage row=7/>%)
9. Smells (<Value data={category_propositions} column=percentage row=8/>%)
10. Sounds (<Value data={category_propositions} column=percentage row=9/>%)
11. Tastes (<Value data={category_propositions} column=percentage row=10/>%)
12. Value & values (<Value data={category_propositions} column=percentage row=11/>%)
13. Visibility (<Value data={category_propositions} column=percentage row=12/>%)
14. Well-being (<Value data={category_propositions} column=percentage row=13/>%)

# Summary

## General Impressions
&quot;The overall sentiment from the university reviews presents a mixed bag of experiences, with a
notable lean towards positive aspects, particularly in relation to the school&#39;s academic offerings,
diversity, and location in Washington D.C. However, there are recurring negative sentiments
regarding social life, housing, and certain administrative services.

**Impressions:**
Positive experiences at the university are often tied to its academic strengths, especially in political
science, international relations, and business programs. Students appreciate the variety of
concentrations and the high level of education they receive. The location in D.C. is frequently
mentioned as a significant draw, offering a plethora of internship opportunities and cultural
experiences that enhance the educational journey.
On the flip side, negative impressions are largely associated with the social aspects of university life.
Greek life receives mixed reviews, with some students feeling it&#39;s not as prominent or satisfying as at
other schools. Housing and dining services are often criticized for inefficiency and lack of quality. The
transition to online learning during the pandemic was also a point of contention, with students
expressing dissatisfaction with platforms like Blackboard.

**Academics:**
Academics are a strong suit, with students praising the variety of programs and the quality of
education. The university&#39;s focus on political science and international relations stands out, and
many students feel well-prepared for their future careers.

**Social Life:**
Social life at the university seems to be less favorable, with Greek life and on-campus activities not
meeting some students&#39; expectations. The lack of a traditional college sports experience also
contributes to a sense of dissatisfaction for some.

**Housing:**
Housing is a contentious issue, with many students finding the lottery system and the quality of
dorms less than ideal. The push to move off-campus is common among upperclassmen seeking more
freedom and privacy.

**Administration and Services:**
Administrative services, particularly financial aid and health services, are often described as difficult
to work with. Students express frustration with bureaucracy and inefficiency.
In summary, while the university excels academically and benefits from its D.C. location, it faces
challenges in fostering a vibrant social scene and providing satisfactory student services.&quot;


```sql category_propositions
WITH AllCategories AS (
    SELECT 'aesthetics' AS Category
    UNION ALL SELECT 'belonging & welcomed'
    UNION ALL SELECT 'comfort & clean'
    UNION ALL SELECT 'empowerment, success & influence'
    UNION ALL SELECT 'fun & stress-free'
    UNION ALL SELECT 'impressions'
    UNION ALL SELECT 'safety'
    UNION ALL SELECT 'self-worth, pride & self-awareness'
    UNION ALL SELECT 'sexual'  Stdeu
    UNION ALL SELECT 'smells'
    UNION ALL SELECT 'sounds'
    UNION ALL SELECT 'tastes'
    UNION ALL SELECT 'value & values'
    UNION ALL SELECT 'visibility'
    UNION ALL SELECT 'well-being'
),
CategoryCounts AS (
    SELECT
        TRIM(LOWER(Category)) AS Category,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    GROUP BY
        TRIM(LOWER(Category))
),
TotalReviewCount AS (
    SELECT
        SUM(category_count) AS total
    FROM
        CategoryCounts
)
SELECT ac.Category,
       COALESCE(cc.category_count, 0) AS category_count,
       CASE WHEN trc.total > 0 THEN ROUND((cc.category_count * 100.0) / trc.total, 2) ELSE 0 END AS percentage
FROM AllCategories ac
LEFT JOIN CategoryCounts cc ON ac.Category = cc.Category
LEFT JOIN TotalReviewCount trc ON 1 = 1
ORDER BY ac.Category


```


```sql sum_by_category
SELECT
    TRIM(LOWER(Category)) AS category_name,
    SUM(CAST(value AS INTEGER)) AS category_value_sum,
    polarity
FROM
    hotels.titles
WHERE date >= '2009-01-01' AND date <= '2023-12-31'
GROUP BY
    TRIM(LOWER(Category)),
    polarity
ORDER BY
    CASE polarity
        WHEN 'very negative' THEN 1
        WHEN 'negative' THEN 2
        WHEN 'neutral' THEN 3
        WHEN 'positive' THEN 4
        WHEN 'very positive' THEN 5
    END,
    category_name ASC
```

<BarChart 
    data={sum_by_category} 
    swapXY=true 
    x=category_name 
    y=category_value_sum 
    series=polarity
    sort=false
    colorPalette={
        [
        "#85144B", // A shade of dark red
        "#FF4136", // A shade of red
        "#AAAAAA", // A shade of grey
        "#2ECC40", // A shade of bright green
        "#3D9970"  // A shade of dark green
        ]
    }
/>

## Student sentiment distribution (2019-2023)
```sql sum_by_polarity
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS Negative,
  COUNT(DISTINCT CASE WHEN polarity = 'neutral' THEN id ELSE NULL END) AS Neutral,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS Positive
FROM
  titles
WHERE
  date BETWEEN '2009-01-01' AND '2023-12-31'
GROUP BY
  Category
ORDER BY 
  Category ASC

```

<DataTable data={sum_by_polarity} rows={20}>
    <Column id="Category" title="Category" />
    <Column id="Negative" title="Negative" contentType=colorscale scaleColor=red/>
    <Column id="Neutral" title="Neutral" contentType=colorscale scaleColor=grey/>
    <Column id="Positive" title="Positive" contentType=colorscale scaleColor=green/>
</DataTable>

## Number of students with NEGATIVE experiences (2022-2023)
```sql negative_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2022-01-01' AND '2022-12-31'AND
  polarity in ('negative', 'very negative')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

```sql negative_reviews_2023
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('negative', 'very negative') THEN id ELSE NULL END) AS negative_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2023-01-01' AND '2023-12-31'AND
  polarity in ('negative', 'very negative')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

<div style="display: flex; justify-content: space-between;">
  <div style="width: 50%;">
    <!-- Table for 2022 -->
    <BarChart 
        data={negative_reviews_2022} 
        swapXY=true 
        x=Category
        y=negative_count 
        series=polarity
        sort=false
        title=2022
        colorPalette={
        [
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
        ]
    }
    />
  </div>
  <div style="width: 50%;">
    <!-- Table for 2023 -->
    <BarChart 
        data={negative_reviews_2023} 
        swapXY=true 
        x=Category
        y=negative_count 
        series=polarity
        sort=false
        title=2023
        colorPalette={
        [
        '#FF4136',      // A shade of red
        '#85144B'  // A shade of dark red
        ]
    }
        
    />
  </div>
</div>

## Number of students with POSITIVE experiences (2022-2023)
```sql positive_reviews_2022
-- For the year 2022
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2022-01-01' AND '2022-12-31'AND
  polarity in ('positive', 'very positive')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

```sql positive_reviews_2023
SELECT
  Category,
  COUNT(DISTINCT CASE WHEN polarity IN ('positive', 'very positive') THEN id ELSE NULL END) AS positive_count,
  polarity
FROM
  titles
WHERE
  date BETWEEN '2023-01-01' AND '2023-12-31'AND
  polarity in ('positive', 'very positive')
GROUP BY
  Category,
  polarity
ORDER BY 
  Category ASC
```

<div style="display: flex; justify-content: space-between;">
  <div style="width: 50%;">
    <!-- Table for 2022 -->
    <BarChart 
        data={positive_reviews_2022} 
        swapXY=true 
        x=Category
        y=positive_count 
        series=polarity
        sort=false
        title=2022
        colorPalette={
        [
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        ]
    }
    />
  </div>
  <div style="width: 50%;">
    <!-- Table for 2023 -->
    <BarChart 
        data={positive_reviews_2023} 
        swapXY=true 
        x=Category
        y=positive_count 
        series=polarity
        sort=false
        title=2023
        colorPalette={
        [
        '#3D9970',  // A shade of dark green
        '#2ECC40',      // A shade of bright green
        ]
    }
    />
  </div>
</div>

