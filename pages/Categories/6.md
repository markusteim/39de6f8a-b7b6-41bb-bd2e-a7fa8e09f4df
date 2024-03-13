# *Confidential and Proprietary Information of SentiScope Ltd., reg. number 14688394. Unauthorized use or disclosure is prohibited.*



# Summary 

Overall of those who left a review in this category **<Value data={polarity_proportions} column=percentage row=2/>%** had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** who had a **negative experience**.


**Negative** Student Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Student Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Student Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


The overall sentiment from the snippets regarding empowerment and influence at the university is predominantly positive. Students feel that the university's location, networking events, and career centers provide them with a wealth of opportunities to gain experience and make important connections. However, there are some negative experiences related to strict policies and financial aid issues.


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
    AND TRIM(Category) = 'empowerment, success & influence'
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
    AND LOWER(TRIM(Category)) = 'empowerment, success & influence'
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
- **Career Advancement:** Students praise the career centers for their effectiveness in providing job and internship opportunities, with one student securing a full-time position as a press aide to a congressman.
- **Networking Success:** The university's location in D.C. is highlighted as a key factor for networking, with students attending balls at embassies and meeting ambassadors.
- **Influential Faculty:** Professors with backgrounds as ambassadors and leaders in their fields are seen as providing valuable insights and connections.
- **Internship Abundance:** The proximity to government and international affairs opportunities in D.C. is appreciated, with students gaining internships at places like Capitol Hill.
- **Leadership Development:** Programs like the School of Public Affairs Leadership Program are commended for developing students' leadership skills.


## Negative:
- **Financial Struggles:** Some students face difficulties with financial aid, with reports of aid being reduced or appeals being rejected.
- **Strict Policies:** There are complaints about strict enforcement of rules, particularly regarding alcohol in dorms and the creation of new clubs.
- **Unpaid Internships:** The prevalence of unpaid internships in the city is a concern for some students.
- **Faculty Issues:** A few students report negative experiences with professors who are described as rude or condescending.
- **Social Challenges:** Some students feel excluded from social groups or face a toxic campus climate.


<br>

## Most Positive Examples:
- "internship opportunities are incredible"
- "endless opportunities for internships, networking, and research"
- "student body of American University will change the world"
- "being here has helped me land a good job"
- "going to school in D.C. opens up an entire world of internships and job opportunities"


 

## Most Negative Examples:
- "au took it away from his housing and dining financial aid"
- "cs program here is weak"
- "at least one verbally abusive teacher received multiple years of bad reviews, still employed"
- "there is also a lot of bureaucratic red tape when creating new clubs and organizations"
- "essentially class i needed for my career and graduation i was not able to take"

<br>



<br>

# Headlines and corresponding snippets from reviews

<br>

```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'positive' OR polarity = 'very positive')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'neutral')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
AND (polarity = 'negative' or polarity = 'very negative')
AND date >= '2009-01-01' 
AND date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'empowerment, success & influence'
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
Student sentiment distribution (2020-2023)

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
    AND TRIM(LOWER(Category)) = 'empowerment, success & influence' -- Change category as needed
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
        
The student reviews indicate a range of concerns related to the university's social
environment, administrative processes, and enforcement of rules. Issues such as the
dominance of Greek life, strict enforcement of parking and dorm regulations, and perceived
rudeness from both staff and students contribute to a less than empowering atmosphere for
some. Additionally, there are mentions of financial aid problems and inflexibility in academic
matters. These snippets suggest that certain aspects of the university experience are
detracting from students' sense of value and influence within the institution.

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ul style="list-style-type: decimal; margin-left: 20px;">

- Dominance of Greek life creating a divisive social atmosphere ("frats and sororities rule the
school", "greeks try to take over").
- Strict enforcement of rules and regulations leading to a sense of surveillance and
punishment ("au tends to be strict about alcohol in dorms", "enforcement is way too stiff").
- Perceived rudeness and unhelpfulness from university staff and administration ("people
that work at au that aren't professors are sometimes unpleasant", "it is best put that it feels
like every member of the administration is paid to be as rude, unhelpful, and condescending
as possible").
- Financial aid issues causing stress and uncertainty ("i keep getting aid taken away from
me", "au is not at all generous or helpful with financial aid").
- Inflexibility in academic and residential matters ("there is also a lot of bureaucratic red tape
when creating new clubs and organizations", "i asked to change but they didnt allow me").
- Parking issues and strict ticketing adding to student frustrations ("parking control tickets
any car that doesen't have a permit every day", "public safety officers give you a ticket").
- Social skills and respect issues among students ("people at au lack social skills", "do not
treat girls with respect").
- Concerns about the overall campus climate ("overall climate of the campus is toxic", "social
aspects of the school are awful").


    </ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- Review and potentially reform Greek life's influence on campus to ensure a more inclusive
environment.
- Re-evaluate enforcement policies to strike a balance between maintaining order and
fostering a trusting community.
- Implement customer service training for staff and administration to improve interactions
with students.
- Increase transparency and consistency in financial aid processes to reduce student anxiety
and dissatisfaction.
- Streamline bureaucratic processes for student initiatives to encourage engagement and
leadership.
- Reassess parking policies and explore solutions to reduce the burden of ticketing on
students.
- Offer workshops on social skills and respect to cultivate a more welcoming and considerate
campus culture.
- Conduct a thorough review of the campus climate with student input to identify and address
underlying issues.


    </ul> 
    </Tab>


    <Tab label="What does work">
        <b>Summary:</b>
        <br>
        
The university appears to be effectively leveraging its location in the nation's capital to provide
students with a wealth of opportunities for internships, networking, and career advancement. The
presence of a strong alumni network and the proximity to a myriad of organizations and government
entities contribute to the students' sense of empowerment and access to influential positions. The
Career Center is frequently mentioned as a valuable resource, offering support with resumes,
interview preparation, and job searches. Professors are noted for their connections and active roles
in assisting students with professional development. The university's emphasis on experiential
learning through internships, many of which are facilitated or encouraged by the institution, is a
recurring theme in the students' experiences.

        
<br>

<br>

    <b>List of positive aspects from customer reviews:</b>

<ul style="list-style-type: decimal; margin-left: 20px;">

- Internship to employment transition ("a lot of interns become employees once they graduate")
- Networking opportunities ("allow for networking")
- Strong alumni presence ("alumni are constantly visiting the campus")
- Career Center effectiveness ("as a student, the career center has been impressive")
- Proximity to real-world events ("at au, you can be constantly plugged in to what's actually
happening in the world")
- Job placement record ("au has a good job placement record")
- Faculty connections ("au helps you to take advantage of that with professors who often work in
d.c.")
- Abundance of internship opportunities ("always plenty of internship opportunities downtown")
- Competitive edge in job market ("american students and graduates are some of the most
competitive job and internship seekers in the d.c. area")
- Access to influential figures ("contact with prominent officials")
- Support for career development ("career center is one of the best in the country")
- Academic credit for internships ("i have had 3 internships in 3 years, and got academic credit for all
of them")
- Mentorship opportunities ("spent my first semester in the mentorship program")
- Financial aid satisfaction ("i have great financial aid")
- Alumni network benefits ("thanks to the large alumni base")

</ul>

<br>

<b>Suggestions:</b>
<br>
<ul style="list-style-type: decimal; margin-left: 20px;">

- To further enhance the student experience, the university could consider expanding paid
internship opportunities, as unpaid internships can be a barrier for some students.
- Additionally, increasing the visibility and accessibility of the Career Center's resources to
ensure all students are aware of and can utilize the support available could be beneficial.
- Encouraging faculty to continue to leverage their professional networks for the benefit of
students and fostering even more connections with local and international organizations
could also provide more diverse opportunities for students.
- Lastly, maintaining and strengthening the alumni network to facilitate mentorship and job
placement for current students and recent graduates would continue to add value to the
university's offerings.


    </ul> 
    </Tab>
</Tabs>