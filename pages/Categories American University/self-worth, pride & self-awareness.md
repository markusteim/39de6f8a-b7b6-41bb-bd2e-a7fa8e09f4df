# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


In the snippets provided, the overall sentiment regarding self-worth, pride, self-confidence, and self-awareness at the university is predominantly positive. Students express a strong sense of pride and accomplishment, often linked to the university's reputation, academic programs, and opportunities for personal growth. However, there are instances of negative experiences, particularly related to perceived elitism and social dynamics.


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
    AND TRIM(Category) = 'self-worth, pride & self-awareness'
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
    AND LOWER(TRIM(Category)) = 'self-worth, pride & self-awareness'
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
- **Academic Pride:** Students feel a sense of pride and self-worth from attending a university with a "wonderful reputation," especially in fields like international relations and communications. The presence of "top professors" and "nationally ranked" schools within the university bolsters this sentiment.
- **Personal Growth:** The university environment has allowed students to "grow as a person both academically and socially," with many expressing gratitude for the personal development they've experienced.
- **Career Confidence:** Opportunities like internships and networking with companies on campus have boosted students' self-confidence in their career paths, with some already securing jobs and feeling "special" about their prospects.
- **Scholarly Satisfaction:** Students who engage with the coursework and participate actively in classes report a genuine sense of accomplishment and knowledge gain, enhancing their self-awareness and academic self-esteem.
- **Financial Recognition:** Merit scholarships and financial aid contribute to students' sense of self-worth, making them feel valued and recognized for their academic achievements.


## Negative:
- **Social Discomfort:** Some students feel alienated or looked down upon due to wealth disparities, with a sense of snobbery and moral pretentiousness from peers affecting their university experience.
- **Elitism Perception:** A common theme among negative experiences is the perception of elitism, with students feeling that there is a "false air of superiority" among their peers.
- **Gender Dynamics:** Negative comments about the dating scene and inflated egos suggest that social interactions can sometimes undermine self-confidence for some students.
- **Overlooked Groups:** There is a sentiment that certain groups, such as grad students or those not fitting a particular social or economic profile, feel overlooked and undervalued.
- **Pressure to Perform:** The competitive atmosphere and high expectations can lead to feelings of inadequacy for those who struggle to meet the university's standards or secure internships.


<br>

## Most Positive Examples:
- "American University has a wonderful reputation."
- "I've never felt more special."
- "I am very proud."
- "I'm so grateful."
- "I am extremely proud to be a part of this community."


 

## Most Negative Examples:
- "Almost every single person at this school is so morally pretentious."
- "Easily a 6, but because there are more girls than straight guys on campus, they suddenly think they're a 10."
- "Everyone seems to put on a false air of superiority."
- "People can be a little self-righteous and preachy."
- "Everyone in the program is pretentious and self-obsessed."

<br>





<br>

## Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness'
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
    AND TRIM(LOWER(Category)) = 'self-worth, pride & self-awareness' -- Change category as needed
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
        
The student reviews suggest a perception of elitism and exclusivity within the university
community. There are indications of a socio-economic divide, with references to wealth and
privilege affecting the student experience. Concerns about the university's reputation and the
attitudes of the student body, particularly among males, are recurrent themes. The reviews
also hint at dissatisfaction with the communication program and a lack of support for
graduate students. While these insights are based on individual experiences, they highlight
areas where the university may need to foster a more inclusive and supportive environment.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Perception of elitism and exclusivity (e.g., "kind of stuck up," "people are snob," "elitism is a
common theme").
- Socio-economic divide affecting student experience (e.g., "especially those who are not
wealthy white, and able-bodied," "if you are one of the few students that attend au that are
not in the upper-class of wealth").
- University's reputation not widely recognized (e.g., "it's not a big name outside d.c.").
- Negative attitudes among the student body (e.g., "au attracts some snobby people,"
"everyone seems to put on a false air of superiority").
- Dissatisfaction with the communication program (e.g., "didn't get what i wanted out of the
com program").
- Lack of support for graduate students (e.g., "grad students are distinctly overlooked").
- Gender dynamics and attitudes (e.g., "guys are very often the negging types," "straight
guys have inflated egos").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these issues, the university could implement diversity and inclusion
training programs to promote a more welcoming environment for all students.
- Enhancing financial aid and scholarship opportunities may help alleviate the
socio-economic divide.
- Improving the communication program through curriculum review and student
feedback could address specific academic concerns.
- Offering more resources and support for graduate students could improve their
university experience. Finally, conducting workshops on gender dynamics and
respectful behavior could help change the negative attitudes identified in the reviews.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to be fostering an environment where students feel confident and proud of
their academic and professional achievements. The presence of strong programs in politics,
international relations, communications, and law contributes to this sentiment. Students are taking
advantage of internship opportunities, which seem to be abundant and integral to the university
experience. The ability to succeed in a competitive yet not overly aggressive environment is noted, as
well as the satisfaction with sports and club activities. The university's support for students through
scholarships also contributes to a sense of gratitude and self-worth among the student body.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Strong academic programs (e.g., "american is especially great if you are interested in politics,
international relations or communications")
- Confidence boost and satisfaction (e.g., "boosted my confidence", "i'm very satisfied with my
experience here")
- Internship opportunities (e.g., "internships rule all", "i've had four internships since i transferred
here")
- Competitive but supportive environment (e.g., "great, competitive school to attend", "lots of
overachieving people but i wouldn't say its super competitive")
- Sports and club activities (e.g., "our club sports are just as good as our varsity", "our sports teams
are pretty good")
- Career-focused education (e.g., "my career goals are to become a tv producer", "worked for a
lobbying firm as a paid social media intern")
- Scholarships and financial support (e.g., "i have gotten lucky and received scholarships through
schools and departments the past 2 years")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could focus on expanding its
internship program to ensure that all students have the opportunity to engage in practical
work experiences.
- Additionally, increasing awareness and access to scholarships and financial aid could help
more students feel supported and valued.
- The university might also consider implementing programs or workshops that encourage
students to be more outgoing and engaged from the first semester, especially for those who
may be shy or from low-income backgrounds.

</ul> 
    </Tab>
</Tabs>