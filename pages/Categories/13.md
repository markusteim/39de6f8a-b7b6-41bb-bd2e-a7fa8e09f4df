# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*




# Summary
Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 

In the collected university reviews, the aesthetics of the university's material aspects ranging from dorms and architecture to the natural environment‚ leans heavily towards the positive. Students often praise the beauty of the campus, its green spaces, and the architecture, while criticisms tend to focus on specific buildings or facilities needing renovation or the small size of the campus.


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT Snippet) AS category_count
    FROM
        hotels.titles
    WHERE date >= '2009-01-01' AND date <= '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetics'
    GROUP BY
        CASE
            WHEN LOWER(TRIM(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN LOWER(TRIM(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN LOWER(TRIM(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END
),
TotalReviews AS (
    SELECT
        SUM(category_count) AS total
    FROM
        MergedCategoryCounts
)
SELECT
    COALESCE(mcc.CleanCategory, 'other') AS Category,
    COALESCE(mcc.category_count, 0) AS category_count,
    COALESCE(ROUND((mcc.category_count * 100.0) / tr.total, 2), 0.00) AS percentage
FROM
    (SELECT 'positive' AS Category UNION ALL SELECT 'negative' UNION ALL SELECT 'neutral') ct
LEFT JOIN MergedCategoryCounts mcc ON ct.Category = mcc.CleanCategory
CROSS JOIN TotalReviews tr
ORDER BY
    Category
```

```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'aesthetics'
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
    title="Distribution of customer sentiment"
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



<br>


## Positive:
- **Campus Beauty**: Students are enamored with the campus's overall beauty, describing it as an oasis with white buildings and classic architecture, complemented by lush greenery and gardens.
- **Dorm Quality**: Many find the dorms to be nice, especially the upperclassmen suites and newly renovated buildings, contrasting them favorably against typical freshman accommodations.
- **Natural Environment**: The presence of parks, an arboretum, and a variety of plants, including cherry blossoms, contributes to a vibrant and refreshing atmosphere that students cherish.
- **Architectural Appreciation**: The design and upkeep of new buildings, such as the School of International Service, receive accolades for their modernity and aesthetic appeal.
- **Location Perks**: The university's location is frequently highlighted as superb, offering a mix of suburban tranquility and city life, with the added bonus of beautiful views and proximity to cultural attractions.


## Negative:
- **Facility Issues**: Some students express dissatisfaction with certain buildings and facilities, noting that they are outdated, under renovation, or have maintenance issues that are not promptly addressed.
- **Inconsistent Dorms**: While some dorms are praised, others are criticized for being old, unrenovated, or not as aesthetically pleasing as their counterparts.
- **Campus Size**: A common complaint is the small size of the campus, with some students wishing for more space or finding the compactness less than ideal.
- **Construction Disruptions**: Ongoing construction projects are a source of frustration for some, as they can detract from the campus's beauty and cause inconvenience.
- **Diversity Concerns**: A few reviews mention a lack of diversity in the student body's appearance, which for some detracts from the overall campus experience.


<br>

## Most Positive Examples:
- "campus is an oasis"
- "beautiful especially in the spring time"
- "it's like walking in a garden"
- "campus is absolutely beautiful"
- "beautiful campus"

 

## Most Negative Examples:
- "dorms vary from being gorgeous to totally trashy"
- "library is one of the most unattractive buildings you'll see"
- "it's definitely not at a state school level"
- "nothing state of the art that i've seen"
- "equivalent to army barracks"

# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
WHERE TRIM(LOWER(Category)) = 'aesthetics'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'aesthetics'
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
    AND TRIM(LOWER(Category)) = 'aesthetics' -- Change category as needed
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
    sort = false
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
        The university&#39;s physical environment appears to be facing several challenges, as reflected
in the student reviews. Concerns range from the aesthetics and size of the campus to the
condition of the facilities. The campus is described as small and not particularly attractive,
with some buildings and dorms in need of renovation. The surrounding area is characterized
as residential and lacking in vibrancy. There is also mention of ongoing construction, which
seems to be a source of disruption. Additionally, there are reports of maintenance issues
within the residence halls and other facilities, with broken amenities not being promptly
repaired. The reviews also touch on a perceived lack of diversity among the student body
and a culture that values a certain standard of attire.


        <br>
        <br>

<b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Campus size and aesthetics (e.g., &quot;campus is pretty small&quot;, &quot;campus isn&#39;t as pretty as it
usually is&quot;, &quot;it just looks like a big fenced area of dirt&quot;).
- Condition and appearance of buildings (e.g., &quot;dorms are terrible&quot;, &quot;some buildings are old
and look old&quot;, &quot;library is one of the most unattractive buildings you&#39;ll see&quot;).
- Ongoing construction and maintenance issues (e.g., &quot;currently, au is under a lot of
construction&quot;, &quot;it seems like they&#39;re always doing maintenance on something&quot;, &quot;facilities are
always breaking down and don&#39;t get fixed for months&quot;).
- Residential hall concerns (e.g., &quot;dorms can either be very nice or pretty ugly&quot;, &quot;letts is a
freshman residence hall building which should be avoided until it is renovated&quot;, &quot;things in the
lounges like ovens and heating will be left broken for months&quot;).
- Lack of diversity and culture of appearance (e.g., &quot;for example, in my residence hall out of
400 students maybe 10 are black&quot;, &quot;it&#39;s also not rare to see all these fellow eagles in
designer clothing or carrying a louis vuitton bag&quot;).


</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these concerns, the university could prioritize renovations of the older buildings
and dorms to improve their appearance and functionality. 
- The ongoing construction projects should be managed in a way that minimizes disruption to students. A maintenance schedule
that ensures timely repairs would also be beneficial. 
- To enhance the campus&#39;s appeal, landscaping and aesthetic improvements could be made. Additionally, the university could
consider initiatives to foster a more inclusive and diverse community, potentially addressing
the cultural concerns raised in the reviews.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university's aesthetic appeal seems to be a mix of modern renovations and average, expected
college facilities. Students appreciate the campus's greenery and the efforts to maintain a beautiful
environment, with specific mentions of the campus being an arboretum and having a variety of
plants. Renovations are ongoing, with some dorms already benefiting from updates, and there is a
sense of continuous improvement. The location is frequently praised for its mix of suburban and
urban qualities, and the proximity to cultural and historical sites adds to the appeal. While some
facilities are noted as breaking down, the overall sentiment is that the campus is well-kept and
aesthetically pleasing, with a range of housing options that cater to different preferences.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Campus greenery and arboretum status (campus is a national arboretum, filled with trees, gardens)
- Ongoing renovations and modern facilities (recent renovations, new dorms like Cassell and
Nebraska)
- Attractive location with suburban and urban mix (located in NW D.C., near cultural attractions)
- Variety of housing options (suite style, apartment style, traditional dorms)
- Well-maintained and clean campus (campus is beautiful and welcoming, very well taken care of)
- Historical and cultural richness (located on Embassy Row, in one of the wealthiest areas of the city)
- Efforts to be environmentally friendly (green roofs, capturing rainwater for reuse)

</ul>

<br>

<b>Suggestions:</b>
<br>

<ul style="list-style-type: decimal; margin-left: 20px;">

- To further improve the university's aesthetics, the management could prioritize timely
repairs of facilities, ensuring that broken amenities in lounges and common areas are fixed
promptly.
- Additionally, as renovations continue, it would be beneficial to keep the student body
informed of progress and timelines to manage expectations and maintain a sense of
momentum in the university's development.
- Expanding green spaces and maintaining the campus's arboretum status could also enhance
the overall aesthetic appeal.

</ul> 
    </Tab>
</Tabs>