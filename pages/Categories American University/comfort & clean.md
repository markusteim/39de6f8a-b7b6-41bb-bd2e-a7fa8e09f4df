# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary 

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 



The reviews present a mixed bag of sentiments regarding comfort and cleanliness at the university. While there are numerous positive remarks about spacious and clean dorms, well-kept facilities, and mild weather, there are also significant complaints about cramped rooms, pest issues, and unpredictable weather patterns. The positive experiences seem to slightly outweigh the negative ones, but the concerns raised are not trivial and point to areas needing improvement.


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
    AND TRIM(Category) = 'comfort & clean'
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
    AND LOWER(TRIM(Category)) = 'comfort & clean'
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

- Spacious Accommodations: Students appreciate the larger-than-average room sizes, with ample built-in storage and comfortable living spaces.
- Clean Facilities: Many reviews highlight the cleanliness of dorms, bathrooms, and public areas, noting daily cleaning routines.
- Climate Control: The ability to control room temperature with AC/heating units is praised, as well as the mildness of the DC weather compared to other regions.
- Well-Maintained Gyms: The new Cassell gym and other athletic facilities receive commendations for being in great condition and well-kept.
- Pleasant Environment: The campus is described as clean and well-maintained, contributing to a comfortable and enjoyable atmosphere for students.


## Negative:

- Cramped Quarters: Complaints about overcrowded rooms, especially when three people are squeezed into spaces meant for two, are common.
- Pest Problems: Several reviews mention issues with cockroaches, mice, and rats in dorms, which significantly detract from the living experience.
- Weather Woes: The unpredictability of DC weather, including humidity, rain, and temperature fluctuations, is a source of discomfort for some.
- Inadequate Facilities: Students note that some facilities, like the gym, are too small for the student body, and there are reports of malfunctioning appliances.
- Sanitation Concerns: Cleanliness is inconsistent, with some areas being well-maintained while others suffer from neglect, leading to unsanitary conditions.


<br>

## Most Positive Examples:
- "dorms are very clean"
- "rooms are pretty spacious"
- "campus is clean"
- "dormitories are comfortable to live in"
- "public bathrooms and lounges are cleaned everyday which is nice"


 

## Most Negative Examples:
- "dorm living conditions are dismal"
- "apartment is slightly known for its cockroach problem"
- "rooms are crowded"
- "it can be sunny and 80 degrees one day and rainy and 60 degrees the next"
- "some of the dorms have mold"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'comfort & clean'
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
    AND TRIM(LOWER(Category)) = 'comfort & clean' -- Change category as needed
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
        
    The student reviews indicate a range of issues related to comfort and cleanliness at the
university. Concerns about the weather, including cold, heat, humidity, and rain, are
frequently mentioned, affecting both the comfort of the students and the condition of the
facilities. The dormitories are a particular point of contention, with numerous comments
about their size, cleanliness, and general state of disrepair. Problems with pests such as
mice, rats, and cockroaches are noted, as well as issues with mold. The common areas,
including bathrooms and showers, are reported to be unclean and not maintained well.
Additionally, the university's facilities, such as the gym and library, are described as too small
and often overcrowded. The fluctuating weather also seems to exacerbate the discomfort
experienced by students.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Inadequate spacing and overcrowding (e.g., "spacing is hard to deal with," "everyone's
packed together").
- Uncomfortable weather conditions (e.g., "cold in the winter," "hot and humid in the
summer").
- Poor dormitory conditions (e.g., "dorms are not good," "dorm living conditions are dismal").
- Cleanliness issues (e.g., "bathrooms are usually pretty gross," "cleanliness is the biggest
problem").
- Pest infestations (e.g., "prominent mice/rat problem," "apartment is slightly known for its
cockroach problem").
- Maintenance problems (e.g., "old appliances," "showers have terrible water pressure").
- Insufficient facilities (e.g., "gym is pretty small for university size," "library isn't that large").
- Weather-related discomfort (e.g., "au is like a huge wind tunnel," "it rains every
Wednesday").


    </ul>

<br>

<b>Suggestions:</b>
<br>

<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these issues, the university could implement a more rigorous and
    frequent cleaning schedule, especially for common areas and bathrooms.
-    Pest control measures should be intensified to tackle the infestation problems.
-    The dormitories may require renovations to improve living conditions, including better
    space management and maintenance of appliances.
-    The university could also consider expanding or optimizing the use of existing
    facilities to alleviate overcrowding.
-    Weatherproofing measures, such as better insulation and heating systems, could
    improve comfort during extreme weather conditions.
-    Additionally, providing more accurate and timely weather information could help
    students prepare for the day&#39;s conditions.

</ul>
    
</Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The student reviews present a mixed picture of the comfort and cleanliness at the university. While
there are mentions of issues such as pests and cramped conditions, there are also numerous positive
comments about the state of the facilities. The dorms are generally described as clean, well-kept,
and often spacious, with individual AC/heating units being a notable feature. The campus itself is
appreciated for its cleanliness and the condition of its athletic facilities. Off-campus housing options,
particularly the Berkshire apartments, are frequently mentioned as popular and convenient. The
weather is noted for its variability, with all four seasons experienced, but it does not seem to
significantly impact the students' campus experience. Overall, while there are areas for
improvement, many aspects of the university's facilities and living conditions are meeting the
students' needs.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Individual AC/heating units in dorm rooms (All the dorm rooms have individual AC/heating units
though)
- Cleanliness of campus and dorms (Campus is clean, dorms are clean and sufficient)
- Athletic facilities are well-kept (Athletic facilities themselves are very nice and well-kept)
- Daily cleaning of bathrooms (Bathrooms are cleaned daily so they're usually fine)
- Spacious dorm rooms (Dorm rooms are large, rooms are pretty spacious)
- Availability of off-campus housing options (Most popular off campus housing option is the Berkshire
apartments)
- Well-maintained residence halls (Residence halls are clean)
- Ample storage space in living areas (There is ample storage space)
- Clean public areas (Public bathrooms and lounges are cleaned everyday which is nice)
- Variety of room options (Options to live in a single, double or triple rooms)
- Weather is manageable and does not disrupt campus life (Overall, the weather is more than
manageable)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further improve the student experience, the university could address the issues raised
about pests and maintenance problems. Implementing more rigorous pest control measures
and regular maintenance checks could help alleviate concerns about cleanliness and
livability.
- Additionally, considering renovations or updates to the older dorms could improve
perceptions of comfort.
- Expanding on the positive aspects, such as the individual climate control in rooms and the
cleanliness of public spaces, would also enhance the overall student satisfaction with the
university's facilities.

</ul> 
    </Tab>
</Tabs>