# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

In the realm of non-material values and value for money at the university, the sentiment is mixed with a tilt towards the negative. Students appreciate the diversity, inclusivity, and academic strength, particularly in political science and international relations. However, there's a strong undercurrent of dissatisfaction regarding the high cost of attendance, perceived lack of support for non-political majors, and administrative issues. The university's liberal atmosphere is both a draw and a point of contention, depending on individual political leanings.


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    WHERE date >= '2009-01-01' AND date <= '2023-12-31'
    AND TRIM(Category) = 'value & values'
    GROUP BY
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        MergedCategoryCounts
),
CategoryTemplate AS (
    SELECT 'positive' AS Category
    UNION ALL SELECT 'negative'
    UNION ALL SELECT 'neutral'
)
SELECT
    ct.Category,
    COALESCE(mcc.category_count, 0) AS category_count,
    COALESCE(ROUND((mcc.category_count * 100.0) / tr.total, 2), 0) AS percentage
FROM
    CategoryTemplate ct
LEFT JOIN MergedCategoryCounts mcc ON ct.Category = mcc.CleanCategory
CROSS JOIN TotalReviews tr
ORDER BY
    ct.Category
```


```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'value & values'
    GROUP BY
        LOWER(TRIM(polarity))
)
SELECT
    Polarity,
    Polarity_sum
FROM PolarityCounts
ORDER BY
    CASE Polarity
        WHEN 'very negative' THEN 1
        WHEN 'negative' THEN 2
        WHEN 'neutral' THEN 3
        WHEN 'positive' THEN 4
        WHEN 'very positive' THEN 5
    END
```

<BarChart 
    data={sum_by_polarity} 
    swapXY=false
    x=Polarity
    y=Polarity_sum 
    series=Polarity
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
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>


<br>

## Positive:
- **Cultural Diversity:** Students celebrate the university's commitment to diversity and inclusivity, with a rich cultural environment that welcomes international students and supports LGBTQ+ individuals.
- **Academic Excellence:** The quality of education, particularly in political science and international relations, is highly regarded, with professors bringing real-world experience into the classroom.
- **Financial Aid:** While the cost of attendance is high, the university does offer merit-based financial aid and scholarships, which some students find generous and helpful.
- **Campus Resources:** The availability of resources, including technology, study abroad programs, and internship opportunities in D.C., is a highlight for many students.
- **Inclusive Policies:** The university's policies on sustainability, social justice, and diversity are praised for creating a respectful and intellectually stimulating environment.


## Negative:
- **High Costs:** The overwhelming consensus is that the university is expensive, with many students struggling with the financial burden and feeling that the cost does not match the value received.
- **Limited Support:** Students outside of political science and international relations feel underserved, with a lack of support and resources for other majors.
- **Administrative Issues:** There are numerous complaints about the inefficiency and unhelpfulness of the administration, particularly the financial aid office.
- **Lack of Spirit:** Many students express a desire for more school spirit and community, feeling disconnected from the larger university experience.
- **Political Overemphasis:** The strong focus on politics can be alienating for some, with students feeling pressured to conform to the university's liberal stance.


## Most positive examples:
- "very diverse university"
- "professors in the school of international service are really good"
- "academically, au is strong"
- "rich cultural environment"
- "quality professors"


## Most negative examples:
- "au is very liberal as well as political"
- "tuition is way too expensive"
- "school administration is horrible"
- "au is expensive"
- "administration is awful"





<br>


# Headlines and corresponding snippets from reviews


```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
ORDER BY Snippet ASC
```

<Tabs>
    <Tab label="Positive Headlines">
        <DataTable data="{positive_headlines}" search="true" rows=18 rowShading=true/>
    </Tab>
    <Tab label="Positive Snippets">
        <DataTable data="{positive_snippets}" search="true" rows=18 rowShading=true/>
    </Tab>
</Tabs>

<br>


```sql neutral_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
ORDER BY Snippet ASC
```

<Tabs>
    <Tab label="Neutral Headlines">
        <DataTable data="{neutral_headlines}" search="true" rows=40 rowShading=true/>
    </Tab>
    <Tab label="Neutral Snippets">
        <DataTable data="{neutral_snippets}" search="true" rows=15 rowShading=true/>
    </Tab>
</Tabs>

<br>

```sql negative_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
ORDER BY Snippet ASC
```


<Tabs>
    <Tab label="Negative Headlines">
        <DataTable data="{negative_headlines}" search="true" rows=40 rowShading=true/>
    </Tab>
    <Tab label="Negative Snippets">
        <DataTable data="{negative_snippets}" search="true" rows=15 rowShading=true/>
    </Tab>
</Tabs>

<br>

# Student sentiment distribution (2020-2023)

```sql sentiment_distribution
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    Year, -- Extract the year from the date
    COUNT(DISTINCT Snippet) AS ReviewCount, -- Count unique review IDs
    CASE
      WHEN TRIM(LOWER(polarity)) = 'very negative' THEN 1
      WHEN TRIM(LOWER(polarity)) = 'negative' THEN 2
      WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 3
      WHEN TRIM(LOWER(polarity)) = 'positive' THEN 4
      WHEN TRIM(LOWER(polarity)) = 'very positive' THEN 5
      ELSE 6 -- For any other case, if needed
    END AS OrderIndex
  FROM
    hotels.titles
  WHERE
    date BETWEEN '2020-01-01' AND '2023-12-31'
    AND TRIM(LOWER(Category)) = 'value & values' -- Change category as needed
  GROUP BY
    TRIM(LOWER(polarity)), 
    Year
)

SELECT
  Polarity,
  Year,
  ReviewCount
FROM Polarity_Ordered
ORDER BY
  Year,
  OrderIndex
```

<BarChart 
    data={sentiment_distribution} 
    x="Polarity" 
    y="ReviewCount"
    series="Year" 
    groupBy="Year" 
    type="grouped"
    sort=false
    echartsOptions={{
    xAxis: {
      axisLabel: {
        rotate: 45 // Rotate labels by 45 degrees if on mobile
      }
    }
  }}
/>


<Tabs>
    <Tab label="What does NOT work">
                       <b>Summary:</b>
        <br>
        
The university appears to be facing a multitude of challenges that affect student satisfaction
across various domains. Concerns range from financial burdens, such as high tuition fees
and expensive living costs, to dissatisfaction with administrative support and the handling of
immaterial values like ethics and diversity. Students have expressed frustration with the
perceived prioritization of the university's image over substantive reforms, ineffective
communication and support from advisors, and a lack of school spirit. Additionally, there are
reports of inadequate responses to issues such as hate crimes, drug and alcohol policy
enforcement, and a lack of support for students with disabilities. The narrative from the
snippets suggests a disconnect between the administration's actions and the students'
needs and expectations.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- High tuition and living costs (e.g., "au is the second most expensive school in the nation",
"living on campus is incredibly expensive").
- Inadequate financial aid and scholarship support (e.g., "financial aid department is terrible",
"administration does not care about minorities on campus").
- Poor administrative support and responsiveness (e.g., "administration is awful", "advisors
don't know what they are doing").
- Lack of school spirit and community (e.g., "au has no school spirit", "barely any sense of
community").
- Ineffective handling of diversity and inclusion issues (e.g., "administration tries hard to give
the appearance of maintaining a safe environment on campus", "au lacks dignity").
- Inadequate response to hate crimes and safety concerns (e.g., "administration does not
take hate crime seriously when it occurs on campus").
- Insufficient support for students with disabilities (e.g., "au really needs to work harder to
address these concerns").
- Perceived prioritization of university image over substantive reforms (e.g., "administration
appears to prioritize the university's public image over substance reforms").
- Inconsistent enforcement of drug and alcohol policies (e.g., "au has very strict policies
concerning drug use and alcohol on campus; we're also non-smoking").
- Discontent with the handling of academic accommodations (e.g., "academic
accommodations are a joke").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Review and potentially increase financial aid and scholarship offerings to better support
students in need.
- Enhance administrative support by providing additional training for advisors and ensuring
they are knowledgeable and responsive to student inquiries.
- Foster a stronger sense of community and school spirit through more inclusive events and
activities that engage the entire student body.
- Implement a more transparent and effective approach to handling diversity and inclusion
issues, ensuring that all students feel represented and supported.
- Improve the response to hate crimes and safety concerns by establishing clear protocols
and communication channels for reporting and addressing such incidents.
- Offer more robust support for students with disabilities, including accessible facilities and
academic accommodations.
- Reevaluate the enforcement of drug and alcohol policies to ensure consistency and
fairness, while also providing education and resources for substance abuse prevention.
- Increase transparency regarding the use of tuition fees and actively involve students in
discussions about university reforms and budget allocations.

</ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university exhibits a strong commitment to academic integrity, a
diverse range of academic programs, and a supportive environment for LGBTQ+ students. The
enforcement of academic policies and the importance placed on academics suggest a serious
educational atmosphere. The presence of various religious services and political activism indicates a
campus that is engaged and respectful of different beliefs and causes. Additionally, the availability of
technology and resources for students is noted as positive. However, there are concerns regarding
the administration's response to incidents of racism, antisemitism, and sexual assault, as well as the
overall diversity of the student body.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Academic integrity and importance (e.g., "academics are much more important here," "au is pretty
stiff about academic violations such as plagiarism").
- Support for LGBTQ+ community (e.g., "au is extremely lgbt friendly," "we have a very large glbtq
community that comes with a very large ally community as well").
- Diversity of academic programs (e.g., "au doesn't just cater to just those majors," "au offers a
variety of majors").
- Political activism and engagement (e.g., "au is very oriented towards political science and
international relations," "students are very politically involved regardless of major").
- Religious services and respect for beliefs (e.g., "au offers of 25 different religious services on
campus," "there is a very strong gay and lesbian population on campus").
- Technological resources (e.g., "computers are always available," "dorms have their own computer
labs with printers").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further improve the university's environment, the administration could take more
proactive measures in addressing and preventing incidents of racism, antisemitism, and
sexual assault.
- Efforts to increase the diversity of the student body, particularly in terms of race and
socioeconomic status, could also be enhanced.
- Additionally, fostering a more inclusive atmosphere where all students feel represented and
heard could contribute to a more positive campus culture.


</ul> 
    </Tab>
</Tabs>