# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the snippets regarding safety and security at the university is mixed, with a lean towards positive experiences. Students generally feel safe on campus, citing low crime rates and good security measures. However, there are concerns about safety off-campus and the occasional negative incident that affects the sense of security.


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
    AND TRIM(Category) = 'safety'
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
    AND LOWER(TRIM(Category)) = 'safety'
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
- **Campus Safety:** Students feel secure with the presence of campus police, blue light emergency phones, and public safety officers patrolling after 9pm. Examples include the quick response to emergency calls and the visibility of security vehicles.
- **Neighborhood Security:** The university is situated in a safe and quiet neighborhood, contributing to students' comfort. The proximity to Homeland Security and low crime rates in the surrounding area are often mentioned.
- **Safety Measures:** The university's efforts in safety training, such as self-defense classes and preventative training, are appreciated. Additionally, the security system is described as top-notch, with locked closets in dorms and emergency systems installed across campus.
- **Night Safety:** Many students report feeling safe walking alone at night on campus, thanks to well-lit paths and constant patrols.
- **Security Alerts:** The university's communication regarding safety concerns, including text alerts and helicopter searches during emergencies, is seen as a positive aspect of campus security.

 

## Negative:
- **Off-Campus Concerns:** Students express unease once leaving campus, with some areas described as sketchy or tense, and advice against walking alone late at night.
- **Theft Issues:** There are reports of theft, particularly of unattended laptops, and recommendations to use U-locks for bikes and to lock dorm doors.
- **Substance Abuse:** While the drug scene is described as almost non-existent, there are warnings about obtaining drugs from trusted sources and the need to be cautious.
- **Safety Incidents:** A few students mention incidents such as armed non-students on campus and thefts, which have impacted their sense of security.
- **Party Safety:** Some students feel less safe at frat parties and advise against walking alone to and from such events.

<br>

## Most Positive Examples:
- "campus crime is extremely low"
- "feel safe even when i am walking alone across the campus"
- "security is really good about notifying everyone about any security concerns"
- "safety staff are always on call"
- "they had helicopters within the hour to search campus"


## Most Negative Examples:
- "it can be very sketchy"
- "theft has been a problem"
- "we had a small gunner scare (it ended up being a mistake)"
- "only thing safe about it is people obtaining drugs from 'a guy they trust'"
- "if the environment doesn't feel right, don't come here"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
WHERE TRIM(LOWER(Category)) = 'safety'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'safety'
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
    AND TRIM(LOWER(Category)) = 'safety' -- Change category as needed
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
        
The university appears to have a range of safety and security concerns expressed by
students. Issues range from thefts, particularly of laptops, to concerns about walking alone at
night and the presence of crime in surrounding areas. Students have also mentioned the
need to be cautious with belongings and the importance of locking doors. There are
references to incidents on campus and in nearby neighborhoods that contribute to a feeling
of unease. Some students have indicated that the university tries to enforce safety policies,
but there is a sentiment that more proactive measures could be beneficial. The presence of
emergency blue light poles and campus security is noted, but there are still reports of feeling
unsafe and incidents of crime.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Difficulty in obtaining accommodations (accommodations are incredibly difficult to obtain).
- Incidents around the school (all the incidents around the school).
- Theft in dorms due to unlocked doors (all the laptop being stolen in the dorms because
students decided not to deadbolt their doors).
- Concerns about post-graduation job prospects (although finding a job post-grad looks slim
for most graduates).
- Perception of administration's attentiveness to student safety (although I made numerous
friends at American University and have been given amazing opportunities I wish the
administration were more attentive to the safety of each student).
- Advice to always lock doors and walk with a friend (always lock your door to prevent thefts,
always walk with a friend).
- Sketchy areas and unsafe neighborhoods (can be sketchy at times, can be unsafe).
- Drug and alcohol visibility (however, as far as alcohol visibility and drug use).
- High racial tension (high racial tension).
- Concerns about safety at frat parties (I feel less safe at frat parties).
- Theft of unattended belongings (theft in the library when unattended laptops are left for the
public).
- Incidents near metro stops (there is going to be some crime near the metro stops).
- History of violence and crime rates in D.C. (history of violence, crime rates in D.C. is worse
than you think).
- Presence of pests in dorms (most of my friends have had mice or rats in their dorms).


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To address these concerns, the university could consider enhancing security
measures, such as increasing patrols in areas where incidents are reported and
improving the security of dormitories with better locks or access control systems.
- Additionally, providing more resources and support for students seeking
accommodations could alleviate some of the stress associated with feeling unsafe.
- The university might also benefit from offering more safety education programs,
including how to secure belongings and awareness of surroundings.
- Improving communication about safety incidents and the measures taken in
response could help build trust between the administration and the student body.
- Finally, fostering a more proactive approach to safety and security, rather than a
reactive one, could contribute to a safer campus environment.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
Based on the provided snippets, the university appears to maintain a level of safety and security that
is satisfactory to many students. The campus is described as being in a safe neighborhood with
visible security measures such as campus police patrols, emergency call boxes, and a blue light
system. Students feel comfortable walking around campus at various hours, and there is a sense of
community safety. The university also seems to provide ample opportunities for internships and jobs,
which contributes to a sense of security regarding future prospects. While there are mentions of
incidents and concerns, these do not dominate the overall sentiment expressed by the students.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Safe neighborhood (AU is in one of the safest neighborhoods in D.C.)
- Visible security measures (at least one security vehicle is always within sight, blue light system,
campus police patrols)
- Comfortable campus environment (students feel safe walking around, campus is well lit and
patrolled)
- Internship and job opportunities (plentiful in D.C., administration assists with city navigation)
- Community and diversity (students of different backgrounds feel connected and respected)
- Health and safety measures (AU has taken action to prevent incidents, free sexual-assault defense
classes)
- Reliable transportation (AU shuttles are pretty reliable)
- Preparedness for emergencies (public safety is only a call away, panic buttons available)

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance safety and security, the university could consider increasing the visibility
of security measures in areas where students have reported feeling less secure.
- Additionally, addressing concerns about theft and unauthorized access to buildings could
improve the sense of security.
- Offering more workshops on personal safety and security, as well as promoting the existing
resources more actively, could also help ensure that all students are aware of how to stay
safe on campus.

</ul> 
    </Tab>
</Tabs>