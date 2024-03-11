# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the university reviews regarding tastes, food, dining, and restaurants seems to be a mix of satisfaction and dissatisfaction. While some students appreciate the variety and quality of food options available, particularly off-campus, others express disappointment with the on-campus dining experience, citing limited options, repetitive menus, and subpar quality. The positive remarks often highlight the diversity of international cuisine and the presence of popular chain restaurants, whereas the negative comments tend to focus on the lack of variety and the quality of food provided by the university's dining services.

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
    AND TRIM(Category) = 'tastes'
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
    AND LOWER(TRIM(Category)) = 'tastes'
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
- **International Flavors:** Students enjoy a range of international cuisine, from Mexican to Indian, with specific mentions of world-renowned restaurants and a wide swath of cultural cuisines available in the area.
- **Dining Variety:** The dining hall is praised for its variety, and off-campus dining options are described as plentiful, with Tenleytown offering a great selection of restaurants.
- **Affordable Meals:** There are mentions of being able to get a great meal for around $10-$15, indicating that students can find value for money when dining.
- **Meal Swipes:** The tavern and salsa are appreciated for accepting meal swipes, providing alternative dining options on campus.
- **Specialty Options:** The ice cream bar receives special mention for being phenomenal, and there's appreciation for the variety of restaurants and the availability of different food options in the area.

 

## Negative:
- **Limited On-Campus:** Students express frustration with the limited on-campus food options, with some finding it difficult to use up their meal plans and others describing the food as barely edible or repetitive.
- **Quality Concerns:** The quality of campus food is criticized, with some students labeling it as terrible and others suggesting that it lacks dignity.
- **Health and Variety:** There are calls for more gluten-free options and healthier choices, indicating a desire for more diverse and accommodating dining services.
- **Repetitiveness:** Food options are described as not great, with some students feeling that the dining experience could be improved and others finding the food options to be boring and unappetizing.
- **Dining Hall Issues:** The main dining hall, TDR, is mentioned as having its great and horrible days, and there's a sentiment that everyone gets sick of it at some point.


<br>

## Most Positive Examples:
- "world-renowned restaurants"
- "the ice cream bar is phenomenal!"
- "variety of international food to choose from"
- "dining hall with a lot of varieties"
- "great food"


 

## Most Negative Examples:
- "food isn't great"
- "meal plan is impossible to use up"
- "food can get repetitive"
- "sometimes is barely edible"
- "terrible food"

<br>




<br>

## Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
WHERE TRIM(LOWER(Category)) = 'tastes'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'tastes'
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
    AND TRIM(LOWER(Category)) = 'tastes' -- Change category as needed
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
        
The student reviews indicate a consistent dissatisfaction with the on-campus dining options
at the university. The primary concerns revolve around a lack of variety, repetitive menus,
and the quality of food, which is often described as subpar or even inedible. Students
express frustration with the limited healthy choices and the high cost of meal plans relative to
the value received. There are also specific mentions of undercooked food and hygiene
issues, such as mold on bread. The dining experience appears to be a significant area of
discontent among the student body, affecting their overall satisfaction with campus life.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Limited variety and repetitive menus (e.g., "food options become repetitive and boring",
"main dining hall never has a different selection").
- Quality of food perceived as low (e.g., "awful food", "terrible quality", "food is not amazing").
- Health and safety concerns (e.g., "chicken half cooked", "mold on bread").
- High cost and perceived poor value of meal plans (e.g., "not worth 15 dollar meal swipes",
"meal plan is terrible").
- Limited healthy and dietary-specific options (e.g., "not many healthy options", "vegetarian
options are terrible").
- Limited dining locations and hours of operation (e.g., "most of campus dining closes", "only
one main dining hall").
- Hygiene and cleanliness issues (e.g., "campus food options are disgusting, dirty").


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
        
The university's dining services appear to offer a broad range of options, both on and off-campus,
catering to diverse tastes and dietary requirements. Students have access to popular chains, local
eateries, and a variety of cultural cuisines. While the main dining hall, TDR, receives mixed reviews,
there are mentions of specific places and types of food that students enjoy. The inclusion of
Eaglebucks in the meal plan enhances the dining experience by allowing students to eat at
off-campus locations. The narrative suggests that while there is room for improvement, particularly
with the main dining hall, the overall dining scene provides students with sufficient choices to find
something they like.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Variety of dining options (e.g., "endless places to eat", "lots of options at most places", "there are
many places to eat")
- Availability of popular food chains (e.g., "popular chains like chipotle and starbucks everywhere",
"you have panera, starbucks, whole foods, chipotle, mcdonalds")
- Cultural and international cuisine (e.g., "a student can find a wide swath of cultural cuisines", "dc is
full of ethnic cuisine from all over the world")
- Healthy and dietary-friendly choices (e.g., "vegan/vegetarian/gluten-free friendly", "vegetarian
options that are always available")
- Eaglebucks and dining dollars adding flexibility (e.g., "eaglebucks can be used at off-campus places",
"dining dollars can be used at other on-campus eateries")
- Positive mentions of specific restaurants and food types (e.g., "cava is great and healthy", "freshii is
healthy and definitely tasty", "great italian")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the dining experience, the university could focus on improving the
consistency and quality of meals offered at TDR, as it received mixed reviews.
- Additionally, expanding the variety and frequency of menu rotations could address concerns
about repetitiveness.
- Engaging with students to gather feedback on their dining preferences and acting on this
information could lead to more tailored and satisfying dining options.
- Lastly, ensuring cleanliness and food safety should be a priority, as there were a few very
negative comments related to these issues.

</ul> 
    </Tab>
</Tabs>