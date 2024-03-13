# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*


 
# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


Overall, the sentiment regarding fun and vibrant experiences at the university is mixed, with a notable number of positive examples highlighting the dynamic social life and opportunities for leisure and exploration, particularly due to the university's location in Washington D.C. However, there are also significant negative sentiments expressed about the lack of campus activities, school spirit, and the challenges faced by students seeking a vibrant social life, especially those not involved in Greek life.


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
    AND TRIM(Category) = 'fun & stress-free'
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
    WHERE date BETWEEN '2020-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'fun & stress-free'
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


## Positive:
- **City Exploration:** Students relish the university's proximity to downtown D.C., enjoying the nightlife, cultural events, and the ease of getting around via public transportation.
- **Campus Activities:** There's a wealth of clubs and organizations offering a range of activities, from sports to cultural events, providing a lively student life.
- **Social Scene:** Fraternities and sororities contribute significantly to the social landscape, with parties and events that many find enjoyable.
- **Learning Opportunities:** Academically, the university is praised for its engaging classes and knowledgeable professors, enhancing the overall student experience.
- **Sports and Recreation:** While varsity sports may not draw huge crowds, there are ample opportunities for students to get involved in club sports and intramurals, adding to the fun on campus.

 

## Negative:
- **Limited On-Campus Fun:** Some students feel there's a dearth of exciting activities on campus, leading to a less engaged community.
- **School Spirit Lacking:** A recurring theme is the absence of school spirit, particularly due to the lack of a football team and low attendance at sports games.
- **Greek Life Dominance:** The social life is often described as being dominated by Greek life, leaving non-members feeling excluded from the party scene.
- **Nightlife Accessibility:** Underage students and those not interested in Greek life express difficulty in accessing a satisfying nightlife.
- **Campus Policies:** The university's dry campus policy and strict housing lottery are criticized for stifling the social atmosphere and creating inconveniences.


<br>

## Most Positive Examples:
- "dc is amazing!"
- "life at american university has been treating me great so far"
- "au truly is a work hard play hard atmosphere"
- "you can easily find a group to have fun and party with"
- "there are always speakers and lecture to attend for free"



## Most Negative Examples:
- "there is no school spirit whatsoever"
- "socially, this school is not worth it"
- "easy to not be involved and not attend games"
- "there is not exciting to do on campus"
- "if you lose it, you lose two days of vacation"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'fun & stress-free'
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
    AND TRIM(LOWER(Category)) = 'fun & stress-free' -- Change category as needed
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
        
The student reviews indicate a range of concerns primarily centered around the lack of a
vibrant campus life, limited social activities, and strict policies that may be contributing to a
less enjoyable student experience. Issues with housing, transportation, and campus
amenities are frequently mentioned, as well as dissatisfaction with the party scene and
athletic events. There are also comments about the academic challenges and the transition
difficulties for new students. Additionally, financial concerns and the administrative processes
related to financial aid and class registration appear to be causing significant stress for
students.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Lack of vibrant campus life and school spirit (e.g., "a lack of campus culture", "school spirit
is really weak").
- Inadequate social activities and events (e.g., "campus events are not the best", "nothing
much to do").
- Strict dry campus policy leading to limited party scene (e.g., "it's a dry campus", "social
scene is very restricted").
- Transportation issues, including unreliable shuttles and parking difficulties (e.g., "shuttles
are unreliable", "parking is horrible").
- Housing concerns, such as difficulty finding on-campus housing and off-campus housing
challenges (e.g., "housing lottery is competitive", "it's a huge hassle to find a place to live").
- Limited campus amenities and inconvenient operating hours (e.g., "amenities are a joke",
"limited hours").
- Financial aid and scholarship issues (e.g., "financial aid isnt the best", "difficulty in getting
larger scholarships").
- Academic transition and adjustment challenges (e.g., "admit I had a difficult transition as a
freshman", "classes after 5pm are always full").
- Administrative inefficiencies, particularly in class registration (e.g., "registration process can
be confusing", "scheduling is a nightmare").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Enhance campus life by increasing the number and variety of social events and activities,
including those that do not revolve around alcohol.
- Reevaluate the dry campus policy to explore safe and responsible ways to incorporate
social drinking events that could reduce off-campus risks.
- Improve transportation services by ensuring shuttles run on a reliable schedule and
exploring solutions for parking constraints.
- Review housing policies and processes to make on-campus housing more accessible and
provide better support for students seeking off-campus accommodations.
- Expand campus amenities and adjust their operating hours to better meet student needs,
including late-night dining options.
- Increase transparency and support in the financial aid process, and actively seek additional
scholarship opportunities for students.
- Streamline the class registration process to reduce stress and ensure students can enroll in
required courses.
- Offer more robust orientation and transition programs for new students to help them adjust
academically and socially


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, it appears that American University offers a variety of experiences
that cater to different student interests and needs. The university's location in a suburban part of
D.C. provides easy access to the city's resources, including internships and public transportation. The
academic environment is described as sound and manageable, with passionate professors and a
range of courses. The transition to online classes has been smooth for many, and the availability of
computers and tech support is noted as good. While varsity sports are not a central aspect of student
life, there are mentions of vibrant club and intramural sports, as well as a growing presence of Greek
life. The campus housing process is straightforward, and there are options for off-campus living. The
social scene is characterized by a mix of on-campus events, fraternity parties, and city nightlife, with
an emphasis on academics over partying.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Location and Access to City Resources (American University is a mile away from the Tenleytown
metro; easy access to internships and public transportation)
- Academic Environment (AU is academically sound; classes are interesting; professors are
passionate)
- Online Class Transition (AU has made the transition to online class very easy)
- Technology and Support (Access to computers is very good; AU's library has plenty of desktops; tech
support only an email away)
- Sports and Greek Life (Club and intramural sports are popular; Greek life is growing)
- Housing and Living Options (All freshmen and sophomores are guaranteed on-campus housing;
off-campus living can be cost-effective)
- Social Scene (A mix of on-campus events, fraternity parties, and city nightlife; emphasis on
academics)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider expanding the
availability of on-campus housing for upperclassmen to ease the transition to off-campus
living.
- Additionally, increasing the visibility and support for varsity sports might foster more school
spirit and a stronger sense of community.
- Offering more workshops or resources on managing academic stress could also benefit
students. Lastly, continuing to improve the technological infrastructure, especially the
reliability of Wi-Fi, would support students' academic and social activities.

</ul> 
    </Tab>
</Tabs>