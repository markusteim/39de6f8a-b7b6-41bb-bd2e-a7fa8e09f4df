# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The reviews present a mixed sentiment regarding sexual experiences at the university. While some students find the campus social scene and the attractiveness of their peers to be positive, there are serious concerns about sexual assault and the gender ratio affecting dating prospects.



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
    AND TRIM(Category) = 'sexual'
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
    AND LOWER(TRIM(Category)) = 'sexual'
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
- **Campus Attractiveness:** Students find peers attractive, noting "guys on campus are usually very well put together" and "gt students are gorgeous."
- **Diverse Options:** There's a variety of individuals ranging from "the typical frat boy to an exotic looking lad," and "lots of adorable gay men."
- **Relationship Culture:** Some students appreciate the "dorm culture" and the fact that "a lot of people are in serious relationships."
- **Style Points:** Positive remarks are made about attire, with students wearing "skirts and cute tops" and being "cute" or "hot."

 

## Negative:
- **Sexual Assault Concerns:** Multiple reviews mention a prevalent problem with "rape and sexual assault," with specific references to "frat parties" and "gross frat guys."
- **Gender Imbalance:** The "effects of the gender ratio are horrible for straight girls," and there's a sentiment that "guys here are horrible."
- **Limited Selection:** Students express disappointment in the dating pool, using terms like "au goggles" to describe settling for less attractive partners due to limited options.
- **Unwanted Attention:** There are reports of "girls are desperate for male attention" and negative experiences such as being "stalked by a graduate student."
- **Campus Reputation:** The university is criticized for not having an "attractive student body" and for the male to female ratio being "way off."


<br>

## Most Positive Examples:
- "guys on campus are usually very well put together"
- "gt students are gorgeous"
- "dorm culture is amazing"
- "beautiful women everywhere on campus and in the city"
- "people in dc also tend to be attractive"


 

## Most Negative Examples:
- "pretty rapey"
- "rape and sexual assault is a prevalent problem"
- "watch out as there have been several sexual assaults at frat parties in recent years"
- "stalked by a graduate student who used his on-campus job to find information"
- "lots of frats are also infamous for sexual assault and making kids sick"

<br>





# Headlines and corresponding snippets from reviews

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```


```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
WHERE TRIM(LOWER(Category)) = 'sexual'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'sexual'
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
    AND TRIM(LOWER(Category)) = 'sexual' -- Change category as needed
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
        
The university is facing challenges related to the social and safety aspects of student life,
particularly concerning sexual experiences. Reviews indicate dissatisfaction among students
regarding the limited pool of potential dating partners, with concerns about the attractiveness
and availability of straight males. Additionally, there are serious concerns about sexual
assault, with multiple instances reported both on and off-campus. The term 'AU goggles'
suggests a perceived need to lower dating standards due to the limited options. The
presence of negative experiences related to fraternity culture and sexual misconduct is also
evident.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Limited dating pool and dissatisfaction with attractiveness of students ("as a female student
this can be very frustrating", "au is not known for having an attractive student body").
- Gender ratio imbalance affecting dating prospects ("effects of the gender ratio are horrible
for straight girls", "male to female ratio is way off").
- Concerns about the prevalence of sexual assault and safety on campus ("occasional
sexual assault issues", "rape and sexual assault is a prevalent problem").
- Negative perceptions of fraternity culture ("gross frat guys", "lots of frats are also infamous
for sexual assault").
- Instances of stalking and harassment ("stalked by a graduate student", "there was a man
on Massachusetts Ave. (near campus) who was sexually assaulting women").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these issues, the university could implement comprehensive sexual
assault prevention and response programs, including mandatory training for all
students and staff. Increasing support services for victims of sexual misconduct, such
as counseling and legal assistance, is crucial.
- The university should also review and enforce strict policies against harassment and
assault, with clear consequences for violations.
- To improve the social climate, the university could facilitate more diverse social
events and activities that encourage interaction among students.
- Additionally, promoting a culture of respect and inclusivity within the student body,
including within fraternity and sorority life, is essential.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university seems to have a mixed environment regarding sexual
experiences. While there are positive comments about the social and dating scene, such as the dorm
culture and the attractiveness of students, there are also serious concerns about sexual assault and
safety. The positive remarks suggest that there is a good co-ed atmosphere and a variety of people to
meet, which can be conducive to forming relationships. However, the negative comments indicate
that there are significant issues with sexual misconduct that need to be addressed.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Dorm culture (e.g., "dorm culture is amazing")
- Attractiveness and style of students (e.g., "girls are very pretty and have great style", "guys on
campus are usually very well put together")
- Variety of people (e.g., "from the typical frat boy to an exotic looking lad", "there's more of a variety
of girls")
- Co-ed atmosphere (e.g., "good co-ed atmosphere")
- LGBTQ+ dating scene (e.g., "especially attractive straight men... so if you're gay you might actually
have some luck!")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To improve the university's environment, it is crucial to address the issues of sexual assault
and misconduct.
- This could include implementing comprehensive sexual assault prevention programs,
providing better support for victims, increasing security measures, and ensuring that there
are clear and enforced consequences for perpetrators.
- Additionally, fostering a culture of respect and consent on campus through education and
awareness campaigns could contribute to a safer and more positive experience for all
students.

</ul> 
    </Tab>
</Tabs>