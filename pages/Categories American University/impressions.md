# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The general sentiment from the university reviews presents a mixed bag of experiences, with a notable number of students expressing both satisfaction and dissatisfaction. Positive comments often highlight the university's location, opportunities, and specific programs, while negative remarks tend to focus on issues with housing, dining, and a perceived lack of school spirit or community. The balance between positive and negative sentiments seems to lean slightly towards the positive, but the criticisms are significant and recurrent enough to suggest that the university experience is far from perfect for many students.


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
    AND TRIM(Category) = 'impressions'
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

<br>


```sql sum_by_polarity
WITH PolarityCounts AS (
    SELECT
        LOWER(TRIM(polarity)) AS Polarity,
        COUNT(DISTINCT Snippet) AS Polarity_sum
    FROM
        hotels.titles
    WHERE date BETWEEN '2009-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'impressions'
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
- **Campus Location**: Students love the university's setting in Washington D.C., which provides a wealth of internship opportunities and cultural experiences.
- **Academic Programs**: Certain programs, particularly in international relations and political science, receive high praise for their quality and the passion of both students and faculty.
- **Diversity & Inclusion**: The university is commended for its diverse student body and the presence of a large LGBTQ community, which contributes to an inclusive environment.
- **Study Abroad**: Many students highlight the study abroad programs as a standout feature, offering unique and enriching experiences.
- **Campus Resources**: Resources like the library and certain academic facilities are appreciated, and some students have had positive experiences with faculty and staff.

 

## Negative:
- **Housing & Dining**: Complaints about the housing lottery, dorm conditions, and dining services are common, with many students finding them subpar.
- **Campus Spirit**: A lack of traditional college spirit and engagement in sports is frequently mentioned, with some students feeling disconnected from campus life.
- **Greek Life**: The Greek system receives mixed reviews, with some students enjoying it and others finding it lacking, especially compared to other universities.
- **Cost & Financial Aid**: The high cost of attendance and issues with financial aid are concerns for many, impacting the overall university experience.
- **Administrative Issues**: Students express frustration with administrative processes and some faculty members, feeling that their needs and concerns are not always adequately addressed.


<br>

## Most Positive Examples:
- "i absolutely love it here"
- "it's the best thing that's ever happened to me"
- "i love going to this school!"
- "au really has it all"
- "i would definitely suggest looking into the school"


 

## Most Negative Examples:
- "i regret ever thinking this was my dream school"
- "i had a horrible time from day one"
- "i wouldn't recommend the school to my worst enemy"
- "i've been trying to argue my case ever since"
- "i probably wouldn't go here if it weren't for my major"

<br>





<br>

## Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
WHERE TRIM(LOWER(Category)) = 'impressions'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'impressions'
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
    AND TRIM(LOWER(Category)) = 'impressions' -- Change category as needed
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
        
The student reviews indicate a range of concerns about the university experience, from
dissatisfaction with the administration and specific academic programs to issues with
campus facilities and services. Students express frustration with housing, dining, and
transportation, as well as the effectiveness of the university's support systems. The
sentiment towards Greek life is mixed, with some students finding it lacking or problematic.
There are also comments about the university's integration with the surrounding city and the
perceived lack of school spirit. While some students have found their niche and are satisfied
with certain aspects of their experience, others are considering transferring or regret their
decision to attend. The reviews suggest that the university could benefit from addressing
these concerns to improve the overall student experience.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Dissatisfaction with administration and support systems (e.g., "i do not like the
administration", "my advisor is of little help").
- Housing and dining services inefficiencies (e.g., "housing and dining program at au is
inefficient and ineffective", "housing is a lottery").
- Transportation and parking difficulties (e.g., "parking generally sucks in d.c", "dulles airport
is impossible to get to").
- Greek life issues and lack of housing (e.g., "have not heard many great things about greek
life at au", "sororities can't have their own house").
- Campus facilities under construction (e.g., "also there is construction happening on a few
buildings").
- Weak alumni network (e.g., "alumni network is weaker than those of neighboring schools").
- Lack of school spirit and low campus morale (e.g., "nobody here has any school spirit",
"morale of the campus is constantly low").
- Discontent with the university's integration with the city (e.g., "au is in its own 'box'
sometimes and not a part of the wonderful city of d.c").
- Issues with academic programs and faculty (e.g., "bad teachers are often weeded out
eventually", "certain classes and professor that you don't like").
- Technical and resource limitations (e.g., "au doesn't give you much to work with", "if you
don't have your own laptop").
- Negative experiences leading to transfer considerations (e.g., "have friends who have
literally transferred", "thinking of transferring after my freshman year").

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these concerns, the university could conduct a thorough review of its
administrative processes and support systems to identify and rectify inefficiencies.
- Improving housing and dining services, perhaps through student feedback
mechanisms, could alleviate some dissatisfaction.
- Addressing transportation and parking issues by exploring partnerships with local
transit authorities or enhancing shuttle services could be beneficial.
- The university might also consider investing in Greek life infrastructure and support to
meet student expectations.
- Engaging with the student body to foster a stronger sense of community and school
spirit could improve morale.
- Finally, the university should continue to evaluate and enhance academic programs
and faculty development to ensure a high-quality educational experience.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university seems to have a diverse and active student body with
a variety of interests and academic pursuits. The urban setting of the university is appreciated by
many students, and the presence of nearby amenities such as stores and restaurants adds to the
convenience of campus life. The university's academic offerings, particularly in international relations
and political science, are recognized and valued. The availability of financial aid and the ability to use
campus currency at local establishments are also noted positively. While there are mentions of a
vibrant Greek life, it is not overwhelming, allowing students to choose their level of involvement. The
university's efforts in providing a conducive learning environment, despite challenges such as the
COVID-19 pandemic, are acknowledged by the students.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Urban university setting with nearby amenities (best buy, the container store, payless, etc.)
- Diverse student body with a significant percentage of female students (60+ percent female)
- Acceptance of campus currency at local establishments (a few take eagle bucks)
- Academic programs that meet expectations, even if different than initially expected (a lot of people
end up switching out of sis, not because it's a bad program but because it's different than they
expected)
- Inclusive environment for students who choose not to drink (a lot of people who choose not to
drink at all)
- Availability of financial aid (as an incoming student, they do meet fafsa requirements for those who
have good merit)
- Recognition of the university's reputation in the community (au has a reputation in this community)
- Proximity to the city and its resources (american is a medium school in a big city, again, it's d.c)
- Variety in housing options and dining (a single dining hall, additional buildings in the surrounding
area)
- Academic satisfaction with certain fields of study (academics are okay, as a language and area
studies major, specializing in francophone africa)
- Opportunities for internships and jobs in D.C. (always tons of competition for jobs in d.c)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could explore expanding
on-campus dining options, as there is mention of only a single dining hall.
- Additionally, ensuring that academic programs are clearly communicated to prospective
students could help manage expectations and reduce the number of students switching
majors.
- The university might also consider increasing support for students during the transition to
college life, as indicated by the need for adjustment on both students' and professors' parts.
- Lastly, continuing to foster an inclusive environment that accommodates diverse lifestyles,
such as those who abstain from alcohol, will contribute to a positive campus culture.

</ul> 
    </Tab>
</Tabs>