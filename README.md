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


🔥 Power BI – Consecutive Defect Trend Conditional Formatting (DAX)

This project demonstrates how to apply sequential, per-defect conditional formatting in a Power BI Matrix using DAX.

The goal is to color each month based on consecutive defect occurrences, not raw values.

🎯 Business Requirement

For each Defect across Year → Month columns:

Consecutive Month	Color
1st month	⚪ White
2nd month	🟡 Light Yellow
3rd month	🟨 Dark Yellow
4th month	🟧 Orange
5th+ month	🔴 Red
Gap (no defect)	Reset to White

✔ Each defect must be calculated independently
✔ Gaps reset the sequence
✔ Year transitions (Dec → Jan) are handled correctly

🧱 Data Model Assumptions
Tables

Fact Table
Contains defect records and quantity

Date Table
Proper calendar table with a relationship to the Fact table

Required Columns

'Date Table'[Date]

'Defect Table'[Defect Description]

Measure: [Sum QTY Cars]

1️⃣ Month Index (continuous timeline)

Creates a numeric month key so that months across years are comparable.

Month Index =
YEAR ( MIN ( 'Date Table'[Date] ) ) * 12
+ MONTH ( MIN ( 'Date Table'[Date] ) )

2️⃣ Consecutive Defect Streak (PER DEFECT)

This is the core logic.
It calculates consecutive months without breaking defect context.

Defect Consecutive Streak =
VAR CurrentMonth = [Month Index]

VAR MonthsWithData =
    FILTER (
        CALCULATETABLE (
            VALUES ( 'Date Table'[Month Index] ),
            ALLEXCEPT (
                'Defect Table',
                'Defect Table'[Defect Description]
            )
        ),
        CALCULATE ( [Sum QTY Cars] ) > 0
    )

VAR LastBreakMonth =
    MAXX (
        FILTER (
            MonthsWithData,
            [Month Index] < CurrentMonth
                && NOT (
                    [Month Index] + 1
                        IN MonthsWithData
                )
        ),
        [Month Index]
    )

RETURN
IF (
    [Sum QTY Cars] > 0,
    COUNTROWS (
        FILTER (
            MonthsWithData,
            [Month Index] <= CurrentMonth
                && (
                    ISBLANK ( LastBreakMonth )
                        || [Month Index] > LastBreakMonth
                )
        )
    )
)

💡 Why this works

ALLEXCEPT keeps calculation locked to the current defect

Gaps reset the streak

No global streak contamination

Works across years

3️⃣ Conditional Formatting Color Measure
Defect Color =
VAR Seq = [Defect Consecutive Streak]
RETURN
SWITCH (
    TRUE(),
    ISBLANK ( Seq ), BLANK(),
    Seq = 1, "#FFFFFF",   -- White
    Seq = 2, "#FFF2CC",   -- Light Yellow
    Seq = 3, "#FFD966",   -- Dark Yellow
    Seq = 4, "#F4B183",   -- Orange
    Seq >= 5, "#C00000"   -- Red
)

4️⃣ Apply Conditional Formatting in Power BI

Select Matrix visual

Values → [Sum QTY Cars]

Format pane → Conditional formatting

Background color → Format by: Field value

Based on field → Defect Color

🖼 Example Visual (add your screenshot here)
📷 Image suggestion:
Matrix with:
- Rows: Defect Description
- Columns: Year → Month
- Values: Sum QTY Cars
- Background colors showing white → yellow → orange → red


Example behavior (Software defect):

Month	Qty	Color
Feb	4	White
Mar	3	Light Yellow
Apr	1	Dark Yellow
May	21	Orange
Jun	156	Red
Jul	12	Red
Aug	(no data)	Reset
Sep	16	White
Oct	3	Light Yellow
Nov	21	Dark Yellow
Dec	6	Orange
🧪 Debug Tip (Highly Recommended)

Add this temporary measure to the matrix:

DEBUG Streak = [Defect Consecutive Streak]


You should see:

1 → 2 → 3 → 4 → 5 → reset → 1


If not:

Check the defect column in ALLEXCEPT

Check relationships

🚫 Common Mistakes (and Fixes)
Problem	Cause	Fix
All cells red	Global streak	Use ALLEXCEPT
Wrong restart	Gap not detected	Month Index logic
Year totals between Dec/Jan	Column subtotal on	Turn column subtotals off


<img width="733" height="534" alt="image" src="https://github.com/user-attachments/assets/22da524a-7c25-44df-beb9-b1b77841251a" />





🔷🔷 DYNAMIC COLUMN RENAME USING COLUMN INDEX IN POWER QUERY 🔷🔷


🟦 Step 1: Promote Headers

First, promote the headers from your source table.

= Table.PromoteHeaders(LOG_Sheet, [PromoteAllScalars=true])
🟦 Custom Step 1: Get the List of Column Names

Click on fx to create a new step and enter the following formula:

= Table.ColumnNames(#"Promoted Headers")

✅ This step returns a list of all column headers.

🟦 Custom Step 2: Reference a Specific Column by Index

Power Query lists use zero-based indexing, meaning the first column starts at 0.

Example (to get the 12th column):

= Table.ColumnNames(#"Promoted Headers"){11}

⚠️ Important:
Column numbering starts from 0, not 1.

This allows us to dynamically identify the column header we want to rename.

🟦 Custom Step 3: Rename the Column Dynamically
1️⃣ Insert a New Step

Click fx again and reference the promoted headers:

= #"Promoted Headers"
2️⃣ Rename the Column Once (Auto-Generated Step)

Manually rename the column so Power Query generates this formula:

= Table.RenameColumns(
    Custom2,
    {{"Protean Number (If applicable)/AM LOG", "Protean Number/AM LOG/URPW NUMBER"}}
)
3️⃣ Replace Static Column Name with Dynamic Reference

Now replace the original column name with the dynamic column reference from Custom Step 2:

= Table.RenameColumns(
    Custom2,
    {{Table.ColumnNames(#"Promoted Headers"){11}, "Protean Number/AM LOG/URPW NUMBER"}}
)

✅ This ensures the column is renamed based on its position, not a fixed header name.

🟦 Final Step: Detect Data Types

Go to the Transform tab in the ribbon

Click Detect Data Type

🟦 Cleanup (Optional but Recommended)

After confirming everything works correctly:

Delete Custom Step 1

Delete Custom Step 2

🧹 These steps are no longer required once the column rename is complete.

helpful youtube video:https://www.youtube.com/watch?v=gJPA_Faotkk





Power BI – Rename or Remove Column (Power Query)
Rename a Column

UI method:

Go to Transform Data
Right-click column → Rename

M code:

Table.RenameColumns(#"Previous Step", {{"Old Name", "New Name"}})
Remove a Column

UI method:

Go to Transform Data
Select column → Remove Columns

M code:

Table.RemoveColumns(#"Previous Step", {"Column Name"})
Important Notes
Column names must match exactly (including spaces/line breaks)
After renaming/removing, update or delete steps like:
Changed Type
Renamed Columns
Avoid special characters and line breaks in column names
