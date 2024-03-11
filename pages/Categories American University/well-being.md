# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The reviews present a mixed sentiment regarding the physical and mental well-being of students at the university. Positive comments often highlight the convenience and quality of transportation, athletic facilities, and the campus environment. In contrast, negative remarks frequently address inadequate mental health services, dietary concerns, and the challenges of maintaining a healthy lifestyle amidst academic pressures.


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
    AND TRIM(Category) = 'well-being'
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
    AND LOWER(TRIM(Category)) = 'well-being'
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
- **Campus Accessibility:** Students appreciate the compact campus, with easy access to buildings and facilities, including state-of-the-art sports centers and frequent shuttle services.
- **Transportation Options:** The availability of shuttles, metro, and buses is praised for making travel convenient, reducing the need for cars, and supporting an active lifestyle.
- **Athletic Facilities:** The presence of multiple fitness centers, pools, and sports fields is noted as beneficial for staying in shape and participating in recreational activities.
- **Academic Support:** The university's resources, such as the library and study areas, are commended for helping students succeed and maintain a balanced workload.
- **Healthy Living:** Some students highlight the availability of healthy food options and the encouragement of walking, which contributes to a healthier lifestyle.

 

## Negative:
- **Mental Health Concerns:** Reviews criticize the university for poor mental health resources, long wait times for counseling, and a lack of support for students with mental health issues.
- **Dietary Challenges:** Students with dietary restrictions face difficulties finding suitable food options on campus, leading to health concerns.
- **Fitness Barriers:** Despite good facilities, some students struggle to find time to use the gym, and there is criticism of the university's support for recreational fitness.
- **Health Services Critique:** The health center is described as having undertrained staff, being inconvenient, and not meeting students' medical needs effectively.
- **Substance Use:** There are mentions of prevalent drug use among students, which raises concerns about the overall well-being and safety on campus.


<br>

## Most Positive Examples:
- "state of the art facilities for the sports"
- "shuttle comes often"
- "everything is very accessible"
- "athletic facilities are good"
- "campus is relatively small and compact"


 

## Most Negative Examples:
- "adequate mental health services"
- "they also have very poor mental health resources"
- "health center does not have very good doctors/physician assistants"
- "school doesn't care about the student's mental health or wellbeing"
- "getting sick almost everyday"

<br>





# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
WHERE TRIM(LOWER(Category)) = 'well-being'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'well-being'
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
    AND TRIM(LOWER(Category)) = 'well-being' -- Change category as needed
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
        
The student reviews indicate a range of concerns regarding the physical and mental
well-being services at the university. Issues with the student health clinic, including
undertrained and rude staff, are mentioned alongside dissatisfaction with the availability and
quality of mental health resources. There are also references to substance use, such as
Adderall and alcohol, which suggest a potential culture of reliance on these substances for
academic or social purposes. Additionally, students have reported problems with food
quality, dietary restrictions, and the overall healthiness of available options. The lack of
community spirit and the challenges faced by students with disabilities or health issues are
also highlighted. Parking and transportation difficulties, as well as the physical layout of the
campus, appear to contribute to the overall stress experienced by students.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Inadequate student health clinic resources ("student health clinic isn't a good resource
apparently").
- Unprofessional behavior and lack of training among health services staff ("student health
services are filled with undertrained, and rude staff").
- High popularity of Adderall, indicating potential misuse ("Adderall is fairly popular").
- High allergy levels affecting student health ("allergy levels are high in the spring").
- Incidents of over-drinking leading to medical emergencies ("ambulances coming to campus
because of students over-drinking").
- Lack of school spirit and community ("american university lacks school spirit and a sense of
community").
- Insufficient study spaces during finals ("at finals, it can sometimes be hard to find places to
study").
- Parking issues ("better parking is needed").
- Challenges for biking due to campus topography ("biking is sort of rough, since we're in a
hilly part of D.C.").
- Food quality and health concerns ("food is good but sometimes gets kids sick", "food is not
as healthy as you think").
- Mental health struggles and inadequate support ("i have never felt so depressed in my life",
"my friends with mental health issues really struggled too").
- Limited healthy off-campus dining options ("not a lot of healthy off campus dining options").
- Poor mental health resources ("they also have very poor mental health resources").
- Lack of support for immunocompromised students ("they have no support in place for
immunocompromised students").
- Transportation and accessibility issues ("the metro is sometimes very inefficient", "there
should be more buses").
- Negative experiences with campus food services ("global fresh makes everyone sick", "i
have lots of dietary restrictions so its nearly impossible to eat on campus without getting
sick").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Conduct a thorough review and training program for health services staff to ensure
professionalism and competence.
- Expand and enhance mental health services, including increasing the number of
counselors and reducing wait times for appointments.
- Implement programs to address substance misuse and provide support for students
struggling with addiction.
- Review and improve the quality and variety of food options, with a focus on healthy and
dietary-restriction-friendly choices.
- Foster a sense of community and school spirit through events and initiatives that
encourage student engagement.
- Increase the number of study spaces available during peak times such as finals.
- Address parking and transportation issues by improving campus infrastructure and
increasing the frequency and coverage of shuttle services.
- Ensure that the needs of students with disabilities or health issues are met through
adequate support and accommodations.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to have a robust transportation system that is highly appreciated by the
students. The availability of shuttles, proximity to the metro, and overall walkability of the campus
are frequently mentioned as positive aspects. Athletic facilities, including gyms and a swimming pool,
are also well-regarded. The campus's compact nature allows for easy access to various amenities and
academic buildings, contributing to a comfortable and convenient environment for students. While
there are some concerns about health services, these are not the focus of this summary, which is
centered on the positive feedback regarding comfort and cleanliness.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Transportation options (shuttles to metro, campus shuttle, parking availability, metro accessibility)
- Athletic facilities (well-equipped gyms, swimming pool, athletic facilities for both athletes and
non-athletes)
- Campus layout (compact and walkable, easy access to downtown, quiet spaces away from city
noise)
- Living accommodations (housing within walking distance, convenient dorm locations)
- Health and wellness options (availability of dietary-specific foods, health promotion program)
- Study and relaxation spaces (plenty of study spots, outdoor areas for relaxation)
- Campus safety (comfortable walking alone, safe environment)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Addressing the concerns raised about the handling of medical issues and ensuring that
students feel supported and taken care of in times of illness could significantly improve the
overall student experience.
- Additionally, expanding the variety of healthy food options to accommodate all dietary
restrictions could be beneficial.

</ul> 
    </Tab>
</Tabs>