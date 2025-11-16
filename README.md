# POWER-BI
📊 Visualizing Improvement & Negative Trends in Power BI (Step 1)

This repository demonstrates how to show improvement (positive trends) and decline (negative trends) in a Power BI bar chart using DAX measures and conditional formatting.
The goal is to help analysts highlight which metrics are improving versus declining across time, categories, or KPIs.

<img width="1283" height="674" alt="image" src="https://github.com/user-attachments/assets/c52278ba-4244-4d82-adc1-713785dc4900" />

🧠 Objective

We want to:

Compare the current period with the previous period (e.g., this month vs. last month, this year vs. last year).

Calculate the percentage change between the two periods.

Use colors (like green for improvement, red for decline) to visually emphasize the trend in a bar chart.

⚙️ Step 1 — Create Key DAX Measures
1️⃣ Total Value Measure

This is your base measure — e.g., total sales, total profit, or any metric you want to track.

Total Sales = SUM(Sales[SalesAmount])

2️⃣ Previous Period Value

Use the CALCULATE and DATEADD functions to get the previous period’s value (for example, previous month).

Previous Period Sales =

CALCULATE(
    [Total Sales],
    DATEADD('Date'[Date], -1, MONTH)
)

3️⃣ Difference (Current vs. Previous)

This measure calculates the absolute difference between the two periods.

Sales Difference = [Total Sales] - [Previous Period Sales]

4️⃣ Percentage Change

To visualize trend strength, calculate the percentage growth or decline.

Sales % Change =
DIVIDE([Sales Difference], [Previous Period Sales], 0)


💡 Note: DIVIDE() safely handles divide-by-zero errors.


🎨 Step 2 — Apply Conditional Formatting

Create a bar or column chart in Power BI.

Place the category (e.g., Month) on the X-axis.

Place Total Sales (or your base measure) on the Y-axis.

In the Data colors section:

Choose Conditional formatting → Format by: Field value.

Select a new DAX measure that defines the color logic.
5️⃣ DAX for Color Logic

Color Logic =
SWITCH(
    TRUE(),
    [Sales % Change] > 0, "#2ECC71",  // Green for improvement
    [Sales % Change] < 0, "#E74C3C",  // Red for decline
    "#95A5A6"                         // Gray for no change
)


Apply this measure to the Data color of the bar chart.
Now each bar will automatically reflect positive or negative trends based on performance.

📈 Step 3 — Final Result

✅ Bars turn green when the metric has improved (positive trend).
❌ Bars turn red when the metric has declined (negative trend).
⚪ Bars turn gray when there’s no change.

This visual instantly communicates which areas are performing well and which need attention, making dashboards more insightful and actionable.
