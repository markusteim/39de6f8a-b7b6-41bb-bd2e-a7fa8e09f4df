# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*




# Summary
Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

The university's soundscape seems to be a mix of harmony and cacophony, with students experiencing both the vibrant local music scene and the disruptive noise from fire alarms and frat houses. The positive remarks are fewer, suggesting that while there are pockets of tranquility, particularly on the north side, the overall sentiment leans towards the challenges of dealing with unwanted noise.


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
    AND TRIM(Category) = 'sounds'
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
    AND LOWER(TRIM(Category)) = 'sounds'
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
- **Quiet Mornings:** Mary Graydon offers a peaceful start to the day, transforming into a lively hub as the evening approaches.
- **Music Scene:** The local music scene adds a cultural beat to the university life, providing an escape from academic pressures.
- **Northside Calm:** The north side of campus is appreciated for its quieter reputation, offering a respite from the hustle and bustle.
- **Suburban Silence:** Living in the suburbs provides a serene environment, away from the university's noise.
- **Arena Excitement:** The entire arena comes alive with the sound of screaming fans, showcasing school spirit and enthusiasm.

 

## Negative:
- **Incessant Alarms:** The dorms are plagued by frequent fire alarms, disrupting sleep and study routines.
- **Thin Walls:** The dormitory walls are so thin that privacy is compromised, with every action of the neighbors audible.
- **Loud Southside:** Southside is notorious for being louder, with more disturbances and security concerns.
- **Frat House Ruckus:** The proximity to frat houses means dealing with loud parties and sleepless nights.
- **Noise Violations:** The university is strict about noise violations, yet some noisy neighbors and partygoers seem to evade repercussions.


<br>

## Most Positive Examples:
- "not too noisy"
- "local music scene"
- "north side is much quieter"
- "i live in a suburb where it is quiet"
- "entire arena is filled with screaming fans"


 
## Most Negative Examples:
- "play terrible music"
- "20 fire alarms in the dorm"
- "noisy and rude neighbors"
- "noisy neighbors and drunks got away with everything scott free"
- "but there are quiet hours that you can get in trouble for violating"

<br>



# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sounds'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sounds'
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
WHERE TRIM(LOWER(Category)) = 'sounds'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sounds'
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
WHERE TRIM(LOWER(Category)) = 'sounds'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```



```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sounds'
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
    AND TRIM(LOWER(Category)) = 'sounds' -- Change category as needed
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
        
The feedback from students indicates that there are several sound-related issues within the
university's dormitories and certain areas of the campus. The most prominent concerns
involve the frequency of fire alarms, particularly in the dorms, which are reported to go off
multiple times per week. Additionally, the thinness of the dorm walls is a recurring theme,
leading to a lack of privacy and an environment where students can easily hear their
neighbors. Noise disturbances from neighbors, including loud music and conversations, are
also mentioned, with some areas like southside being identified as noisier than others. There
are references to noise violations and quiet hours, suggesting that while the university has
policies in place, their enforcement may be inconsistent.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Frequent fire alarms in dormitories ("20 fire alarms in the dorm", "dorms have fire alarms
that go off multiple times per week for no reason").
- Thin walls in dorms leading to lack of sound privacy ("dorm walls are incredibly thin", "walls
in the dorms are so thin though, so you can hear everything your neighbors say/do", "you
can hear through the walls").
- Noise disturbances from neighbors and other students ("be prepared to hear every word
your neighbor is saying", "noisy and rude neighbors", "play terrible music", "noisy neighbors
and drunks got away with everything scott free").
- Inconsistent enforcement of noise policies ("they're sometimes a bit too lenient on noises",
"there are times when they can't ignore a room full of drunk kids blasting music").
- Variability in noise levels across different campus areas ("southside is always louder",
"there are parts of campus, such as southside, that are noisier than others, such as
northside").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Review and enhance the fire alarm system to reduce false alarms.
- Investigate and potentially upgrade the soundproofing in dormitory walls.
- Enforce noise violation policies more consistently to ensure quiet hours are respected.
- Provide additional support and resources for students affected by noise disturbances, such
as offering alternative study spaces or soundproofing materials.
- Conduct a noise assessment of different areas on campus to identify and mitigate specific
problem spots


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        

The university environment presents a mix of sound levels across different areas, with some students
appreciating the quieter zones and others expressing frustration with noise disturbances. The
positive feedback indicates that there are specific locations such as the north side of the campus and
Mary Graydon, which offer a quieter atmosphere, especially valued by those residing in the suburbs.
Additionally, the presence of a dedicated quiet floor suggests that the university has made efforts to
accommodate students' needs for silent spaces. However, concerns about frequent fire alarms and
noise from social activities highlight areas for potential improvement.
        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Dedicated quiet areas ("there is a floor dedicated to that sound level")
- Suburban tranquility appreciated by residents ("i live in a suburb where it is quiet")
- Enjoyable local music scene ("local music scene")
- Morning quietude in Mary Graydon ("mary graydon is nicely quiet in the mornings")
- North side recognized for less noise ("north side is much quieter", "on north side you have a quieter
reputation")
- Availability of a quiet floor for studying or relaxation ("there is also the option of utilizing the quiet
floor")
- General satisfaction with manageable noise levels ("not too noisy")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the sound environment on campus, the university could investigate the
frequent fire alarm incidents and implement stricter policies or awareness campaigns to
reduce noise from social activities, especially in residential areas.
- Additionally, promoting the quiet zones and possibly expanding these areas could benefit
students seeking a peaceful study or living environment.


    </ul> 
    </Tab>
</Tabs>