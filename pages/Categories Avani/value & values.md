

# Summary

Overall **<Value data={polarity_proportions} column=percentage row=2/>%**  of customers had a **positive experience**, in comparison to **<Value data={polarity_proportions} column=percentage row=0/>%** of customers who had a **negative experience**.


**Negative** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=0/> 

**Neutral** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=1/>

**Positive** Customer Experience Count: <Value data={polarity_proportions} column=category_count row=2/> 


```sql polarity_proportions
WITH MergedCategoryCounts AS (
    SELECT
        CASE
            WHEN TRIM(LOWER(polarity)) IN ('positive', 'very positive') THEN 'positive'
            WHEN TRIM(LOWER(polarity)) IN ('negative', 'very negative') THEN 'negative'
            WHEN TRIM(LOWER(polarity)) = 'neutral' THEN 'neutral'
            ELSE 'other'
        END AS CleanCategory,
        COUNT(DISTINCT review_id) AS category_count
    FROM
        hotels.titles
    WHERE travel_date >= '2022-01-01' AND travel_date <= '2023-12-31'
    AND TRIM(Category) = 'value & values'
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
        COUNT(DISTINCT review_id) AS Polarity_sum
    FROM
        hotels.titles
    WHERE travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND LOWER(TRIM(Category)) = 'value & values'
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

1. Ethical Upgrades: Guests appreciated complimentary services like free shuttle, water, and room upgrades without extra costs.
2. Value for Money: Many found the hotel to offer exceptional value, with reasonable pricing and deals that exceeded expectations.
3. Efficient Services: Punctuality and efficiency were highlighted, with on-time services and high-speed internet receiving praise.
4. Language Proficiency: The multilingual communication skills of the staff were noted as a positive aspect, enhancing guest experiences.
5. Staff Dedication: The hardworking nature of the staff was recognized, contributing to the overall positive atmosphere of the hotel.

## Negative:
1. Financial Transparency: Some guests experienced issues with overcharging and billing disputes, feeling the pricing was not transparent.
2. Restrictive Policies: There were complaints about hotel policies, such as restrictions on food delivery services and photography.
3. Service Inconsistencies: A few guests mentioned staffing challenges and unmet expectations regarding hotel services and taxi arrangements.
4. Construction Annoyances: Ongoing construction and lack of upfront information about it caused inconvenience for some guests.
5. Room Problems: Instances of room allocation issues, including being moved to an unsatisfactory room, were a source of frustration.

## Most positive examples:
1. "amazing facility great location and value for money"
2. "best located hotel for its price"
3. "brilliant value for money trip"
4. "definitely worth it"
5. "true value for money what you pay"

## Most negative examples:
1. "feeling fooled by the system algorithm"
2. "if i wanted to have it cleaned i wouldn't put dnd"
3. "they want you to use their taxis that charge double the rate"
4. "refused to honor their original pricing"





<br>


# Headlines and corresponding snippets from reviews


```sql positive_headlines
SELECT Headline, COUNT(*) AS Count
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql positive_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'positive' OR polarity = 'very positive')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql neutral_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'neutral')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
GROUP BY Headline
ORDER BY Count DESC
```

```sql negative_snippets
SELECT Snippet
FROM hotels.titles
WHERE TRIM(LOWER(Category)) = 'value & values'
AND (polarity = 'negative' or polarity = 'very negative')
AND travel_date >= '2022-01-01' 
AND travel_date <= '2023-12-31'
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

# Customer sentiment distribution (2022-2023)

```sql sentiment_distribution
WITH Polarity_Ordered AS (
  SELECT
    TRIM(LOWER(polarity)) AS Polarity,
    Year, -- Extract the year from the travel_date
    COUNT(DISTINCT review_id) AS ReviewCount, -- Count unique review IDs
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
    travel_date BETWEEN '2022-01-01' AND '2023-12-31'
    AND TRIM(LOWER(Category)) = 'value & values' -- Change category as needed
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
        
        The guest reviews indicate a range of concerns primarily centered around pricing
transparency, communication issues, and service conduct. Guests have expressed
dissatisfaction with the perceived value for money, citing instances of being
overcharged or feeling misled by pricing strategies. There are also reports of
inadequate information regarding ongoing construction and its impact on the guest
experience. Additionally, guests have encountered problems with room service
protocols and taxi services provided by the hotel, which are seen as overpriced
compared to metered taxis. 

        <br>
        <br>

    <b>List of issues from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Pricing concerns (e.g., &quot;a little pricy,&quot; &quot;for what I paid, wouldn&#39;t stay again,&quot;
&quot;hotel taxi was around 40 percent more expensive&quot;).</li>
<li>Overcharging incidents (e.g., &quot;chap behind the desk noticed we&#39;d been
overcharged,&quot; &quot;promised us a certain price documented in an email, but yet
charged us a lot more&quot;).</li>
<li>Communication breakdowns (e.g., &quot;clueless about relaying information,&quot; &quot;no
one informed upfront about the construction&quot;).</li>
<li>Room service issues (e.g., &quot;if i set do not disturb sign, there is no need to call
me and ask if i want my room cleaned&quot;).</li>
<li>Misleading sales tactics (e.g., &quot;they will try to convince you to book directly
from their website and will give you a better pricing&quot;).</li>
<li>Construction disturbances (e.g., &quot;they are demolishing buildings behind from
sunrise to midnight&quot;).</li>
<li>Taxi service dissatisfaction (e.g., &quot;guys that gets you taxis they keep saying
there is no meter taxis,&quot; &quot;they want you to use their taxis that charge double
the rate&quot;).</li>
<li>Ethical concerns and staff conduct (e.g., &quot;front of house managers should be
fired and/or replaced,&quot; &quot;two points deducted from the hotel rating, because of
the lebanese restaurant employee&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To address these issues, the hotel management should consider the following
actions:</li>
<li>Review and adjust pricing to ensure it aligns with the value perceived by
guests.</li>
<li>Implement strict protocols to prevent overcharging and honor original pricing
agreements.</li>
<li>Improve communication with guests about any ongoing construction and its
potential impact on their stay.</li>
<li>Respect the &quot;Do Not Disturb&quot; signs and establish clear guidelines for room
service interactions.</li>
<li>Ensure transparency in sales tactics and avoid pressuring guests to book
through specific channels.</li>
<li>Reevaluate the taxi service partnership to offer more competitive rates and
options for guests.</li>
<li>Address staff conduct and ethical concerns by providing additional training or
taking disciplinary actions where necessary.</li>


    </ol> 
    </Tab>


    <Tab label="What does work">
                       <b>Summary:</b>
        <br>
        
        The hotel has received commendations for its value for money, with guests feeling
that they received what they paid for and often more. The availability of
complimentary services such as Wi-Fi, bottled water, and shuttle services to local
attractions has been well-received. The hotel's atmosphere is described as chill and
cool, which suggests a relaxed environment that guests enjoy. The presence of
amenities like laundry and room service adds to the convenience factor. The hotel's
location and the ease of using public transport have also been highlighted positively.
The staff's language skills and punctuality have been noted, indicating good
communication and reliability. Overall, guests seem satisfied with the balance
between cost and the quality of services provided. 

        <br>
        <br>

    <b>List of positive aspects from customer reviews:</b>
    <ol style="list-style-type: decimal; margin-left: 20px;">

    <li>Value for money (e.g., &quot;good value for money option in Dubai&quot;, &quot;full value for
the price and even beyond that&quot;)</li>
<li>Complimentary services (e.g., &quot;complimentary bottled drinking water was in
plenty&quot;, &quot;free shuttle to beach and it’s amazing&quot;)</li>
<li>Good location (e.g., &quot;great location&quot;, &quot;best located hotel for its price&quot;)</li>
<li>Convenient amenities (e.g., &quot;access to laundry, room service etc was very
handy&quot;, &quot;your own kitchen in the room&quot;)</li>
<li>Positive atmosphere (e.g., &quot;atmosphere is chill and cool&quot;, &quot;amazing
atmosphere&quot;)</li>
<li>Reliable and efficient staff (e.g., &quot;always on time&quot;, &quot;they are very
hardworking&quot;)</li>
<li>Language skills of staff (e.g., &quot;she talks in a few languages
(Arab/English/French)&quot;, &quot;they speak very good English to understand them&quot;)</li>
<li>Internet connectivity (e.g., &quot;excellent Wi-Fi&quot;, &quot;wifi is super fast&quot;)</li>
<li>Transportation options (e.g., &quot;easy and cheap with their public transport&quot;,
&quot;there is free bus to everywhere u want to go in Dubai&quot;)</li>


    </ol>

<br>

<b>Suggestions:</b>
<br>
<ol style="list-style-type: decimal; margin-left: 20px;">

    <li>To further enhance guest satisfaction, the hotel could consider expanding its
range of complimentary services or amenities, based on guest feedback.
Additionally, maintaining and possibly improving the current level of service
and amenities will help to ensure that the hotel continues to be seen as
providing excellent value for money.</li>
<li>It may also be beneficial to highlight the hotel&#39;s ethical and sustainable
practices, as this is an area of increasing importance for many travelers</li>


    </ol> 
    </Tab>
</Tabs>