# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*


# Summary
Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of students who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the university reviews regarding the sense of belonging, care, and support is predominantly positive. Students frequently express feelings of being welcomed, supported by faculty, and finding their community within various groups and organizations. However, there are instances of negative experiences where students felt isolated, uncared for, or found the social atmosphere lacking.

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
    AND LOWER(TRIM(Category)) = 'belonging & welcomed'
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
    AND LOWER(TRIM(Category)) = 'belonging & welcomed'
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



## Positive:
- **Community Building:** Students often find their place within the university through clubs, Greek life, and residence halls, creating tight-knit communities and meaningful friendships.
- **Faculty Engagement:** Many professors are praised for being approachable, responsive, and genuinely caring about students' well-being, often going beyond academic support.
- **Diverse Inclusion:** The university's diverse student body and faculty contribute to an environment where different backgrounds are celebrated, fostering a sense of belonging for international and minority students.
- **Academic Support:** There is a strong emphasis on academic assistance, with numerous resources available on campus, and professors who are willing to help students understand course material.
- **Personal Growth:** The university environment encourages personal and professional connections, internships, and a broader perspective, contributing to students' growth and future opportunities.


## Negative:
- **Social Challenges:** Some students experience feelings of isolation, difficulty fitting in, or find the social scene lacking, particularly if they are not part of Greek life or specific social groups.
- **Faculty Indifference:** A few students report encounters with faculty who seem apathetic or uninterested in student success, contrasting with the otherwise positive faculty reviews.
- **Financial Struggles:** There are mentions of the university not being accommodating to students facing financial difficulties, affecting their sense of support.
- **Exclusivity Issues:** Negative experiences include feeling unwelcome in certain groups, facing discrimination, or dealing with a competitive atmosphere that hinders inclusivity.
- **Administrative Barriers:** Some students find the administration unhelpful or bureaucratic, which can detract from the sense of community and support.


<br>

## Most Positive Examples:

- "people here really care for you"
- "you'll get friends no matter what"
- "professors were responsive to online school changes"
- "students and profs are kind and welcoming"
- "i have a more worldly and humanitarian perspective"


## Most Negative Examples:

- "faculty don't really care about its students"
- "you will not fit in"
- "they don't seem to care much about the students"
- "i feel like everyone is not friendly or stingy"
- "not student centric or accommodating to those with financial struggles"

<br>



<br>

# Headlines and corresponding snippets from reviews 

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'belonging & welcomed'
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
    AND TRIM(LOWER(Category)) = 'belonging & welcomed' -- Change category as needed
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
        
The student reviews suggest that there is a perceived lack of inclusivity and community at
the university. Students have reported feeling isolated, particularly if they are not part of
specific social groups such as athletics, Greek life, or certain majors. There is a sentiment
that the administration and faculty are indifferent to student needs and that the campus
culture can be unwelcoming and cliquey. Additionally, there are concerns about
discrimination and a lack of support for students with diverse political views or those who do
not conform to the dominant social norms.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Perceived indifference from staff and faculty (e.g., "administration does not care," "faculty
don't really care about its students").
- Social isolation and difficulty finding a sense of belonging (e.g., "can feel socially lacking,"
"hard to make friends," "isolated a little due to not being a party person").
- Cliquey and exclusive social groups (e.g., "diversity but no one intermingles," "group can
be cliquey," "people stick to their own groups").
- Lack of support for non-traditional or minority students (e.g., "don't seem to care much
about graduate students," "hard for a minority to find and connect").
- Discrimination and narrow-mindedness (e.g., "administration and services are overly
bureaucratic and unhelpful," "can be a bit unaccepting of certain minorities").
- Unwelcoming campus environment (e.g., "campus environment is cold and unwelcoming,"
"students are very unfriendly").
- Issues with inclusivity and political affiliation (e.g., "inclusion is a problem when it comes to
political affiliation," "much of the school is not very understanding and inclusive").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Implement training programs for staff and faculty to enhance their engagement with and
support for students.
- Create more inclusive social events and programs that encourage interaction across
different groups.
- Establish mentorship programs to help new students integrate into the university
community.
- Offer workshops and discussions on diversity and inclusivity to address issues of
discrimination and narrow-mindedness.
- Review and improve administrative processes to ensure they are student-centric and
supportive.
- Encourage student organizations to adopt more inclusive policies and practices.
- Provide platforms for students to voice their concerns and actively work on addressing
them.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university appears to foster a strong sense of community and
belonging among its students. The diverse and inclusive environment is highlighted by the presence
of international students and the acceptance of various backgrounds and identities. The faculty's
approachability and dedication to student success are frequently mentioned, as well as the
university's efforts to ensure student safety and well-being. The availability of clubs, organizations,
and Greek life offers multiple avenues for students to find their niche and build lasting relationships.
The university's commitment to diversity, both in its student body and faculty, contributes to a rich
educational experience where students feel supported and valued.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Sense of community and belonging ("always felt part of the au community", "great sense of
community on campus")
- Inclusivity and diversity ("au is a very open-minded campus", "diversity of students on campus
reflects a dignity in personalities")
- Supportive faculty and staff ("professors are approachable and flexible", "counselors and staff have
been so helpful & friendly")
- Strong student-faculty relationships ("great connection between professors and students",
"professors make every effort to get to know each of their students")
- Opportunities for social engagement and networking ("clubs for everything", "participating in greek
life has really allowed me to find my community")
- Safety and well-being initiatives ("public safety is always available and helpful", "university tries to
create a sense of community in the dorms")
- International student representation ("au is incredibly friendly towards international students", "we
have a very diverse student body")
- Academic flexibility and customization ("i am a double major and was able to tailor the program",
"help for course selection")
- Alumni and peer support ("we have a great alumni network", "lot of feedback provided by the
alumni or upperclassmen")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the sense of belonging and support within the university, it could be
beneficial to continue promoting diversity and inclusivity initiatives, ensuring that all
students feel represented and heard.
- Expanding mentorship programs and peer support networks can provide additional guidance
and connection for students.
- Additionally, maintaining and improving communication channels between students and
faculty can foster a more collaborative and engaging learning environment.
- Encouraging student feedback and actively incorporating it into university policies and
practices can also contribute to a more responsive and student-centered campus culture.


    </ul> 
    </Tab>
</Tabs>