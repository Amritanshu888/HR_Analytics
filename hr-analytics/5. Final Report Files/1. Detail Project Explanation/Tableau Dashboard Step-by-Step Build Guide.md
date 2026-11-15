# Tableau HR Analytics Dashboard: Step-by-Step Build Guide

This guide documents how to connect the HR dataset, build the worksheets and KPI cards, and assemble the dashboard in Tableau Desktop. It follows the project workbook, [Hr Analytics.twbx](../../4.%20Tableau%20File/Hr%20Analytics.twbx), and the source data in [Hr Analytics.csv](../../2.%20Dataset/Hr%20Analytics.csv).

> Tableau menu names can differ slightly between versions. The workbook included in this project uses a fixed dashboard size of **1500 x 850**.

## 1. Open the project or start a new workbook

To inspect the finished project, open `4. Tableau File\Hr Analytics.twbx` in Tableau Desktop. To recreate it from the source:

1. Launch Tableau Desktop.
2. On the start page, under **Connect**, click **Text file**.
3. Browse to `2. Dataset\Hr Analytics.csv`, select it, and click **Open**.
4. On the **Data Source** page, confirm the preview contains the HR columns, including `Age`, `Attrition`, `EmployeeCount`, `JobSatisfaction`, `MonthlyIncome`, and `YearsAtCompany`.
5. Click **Sheet 1** at the bottom to begin building worksheets.

The CSV uses compact column names such as `MonthlyIncome`. When entering formulas, use the field names Tableau shows in the Data pane; the workbook formulas use names such as `[MonthlyIncome]` and `[EmployeeCount]`.

## 2. Create age groups

The dashboard compares five age ranges: 18–25, 26–35, 36–45, 46–55, and 56–60.

1. In the Data pane, open the field menu for **Age** (right-click, or click its drop-down arrow).
2. Click **Create Calculated Field**.
3. Name the field `Age (group)`.
4. Enter this calculation:

   ```text
   IF [Age] >= 18 AND [Age] <= 25 THEN "18 to 25"
   ELSEIF [Age] >= 26 AND [Age] <= 35 THEN "26 to 35"
   ELSEIF [Age] >= 36 AND [Age] <= 45 THEN "36 to 45"
   ELSEIF [Age] >= 46 AND [Age] <= 55 THEN "46 to 55"
   ELSEIF [Age] >= 56 AND [Age] <= 60 THEN "56 to 60"
   ELSE "Other"
   END
   ```

5. Confirm Tableau reports that the calculation is valid, then click **OK**.
6. If the dataset contains any values outside the listed ranges, check the `Other` group before presenting the dashboard.

## 3. Build the age-wise employee count sheet

1. Rename the sheet **AgeWise Employee Count** (double-click the sheet tab).
2. Drag **Age (group)** from the Data pane to **Rows**.
3. Drag **EmployeeCount** to **Columns**. Confirm its pill reads `SUM(EmployeeCount)`.
4. On the **Marks** card, choose **Bar** if Tableau has not selected bars automatically.
5. Drag **Attrition** to **Color** on the Marks card. This creates a stacked comparison of employees who stayed and left.
6. Drag **EmployeeCount** to **Label** if the counts are not shown on the bars.
7. Use the toolbar **Sort** button if needed so age ranges appear in ascending order.
8. Edit the sheet title to **Agewise Employee Count**.

## 4. Build the employee count by experience sheet

1. Click **New Worksheet** and rename it **Employee Count by Experience**.
2. Drag **YearsAtCompany** to **Columns**.
3. Drag **EmployeeCount** to **Rows** and confirm the aggregation is `SUM`.
4. Set the Marks type to **Bar**.
5. Drag **Attrition** to **Color** to split each year's employee count by attrition status.
6. Drag **EmployeeCount** to **Label** if you want to show the values on the bars.
7. Set the title to **Employee Count by Experience**.

## 5. Build the employee count by income histogram

1. Click **New Worksheet** and rename it **Employee Count by Income**.
2. In the Data pane, right-click **MonthlyIncome** and select **Create > Bins**.
3. Name the bin field **Monthly Income (bin)** and set **Size of bins** to `2000`; click **OK**.
4. Drag **Monthly Income (bin)** to **Columns**.
5. Drag **MonthlyIncome** to **Rows**. Open the pill menu and select **Measure > Count** so the vertical axis counts employees in each income bin.
6. Set the Marks type to **Bar**.
7. Drag **Attrition** to **Color** so each bin is split between `No` and `Yes`.
8. Drag the count measure to **Label** if the bin counts are not already visible.
9. Set the title to **Employee Count by Income**.

## 6. Create the income comparison calculations

Create these calculated fields from the Data pane using **Create Calculated Field**. Click **OK** after each valid calculation.

### Average monthly income for each age group

Name: `Avg per age group`

```text
{ FIXED [Age (group)] : AVG([MonthlyIncome]) }
```

### Overall average monthly income

Name: `Total Average`

```text
{ FIXED : AVG([MonthlyIncome]) }
```

### Difference between the overall average and each age group

Name: `Amt from Group Average`

```text
SUM([Total Average]) - SUM([Avg per age group])
```

The difference is calculated as **overall average minus age-group average**. A positive value means the age group's average is below the company average; a negative value means it is above.

## 7. Build the average monthly income by age sheet

1. Click **New Worksheet** and rename it **Average Monthly Income by age**.
2. Drag **Age (group)** to **Columns**.
3. Drag **Avg per age group** to **Rows**.
4. Drag **Amt from Group Average** to **Rows** as a second measure. Keep both measures in separate panes, as in the project workbook; do not combine them into a dual axis.
5. For each Marks card, choose **Bar** and turn on **Show Mark Labels** if the values are not visible.
6. Set the first pane's title/axis label to identify the average income, and the second to identify the difference from the overall average.
7. Set the worksheet title to **Average Monthly Income by Age**.

## 8. Create the KPI calculations and sheets

### Attrition employee count

Create a calculated field named `Attrition Employee Count`:

```text
IF [Attrition] = "Yes" THEN [EmployeeCount]
ELSE 0
END
```

### Attrition rate

Create a calculated field named `Attrition Rate`:

```text
SUM([Attrition Employee Count]) / SUM([EmployeeCount])
```

Format this field as a percentage with two decimal places.

### Make the four KPI sheets

Create one worksheet for each KPI below. For each sheet, put the relevant measure on **Text** on the Marks card (or use **Show Me > Text Table** and remove any unwanted row/column headers), then set an appropriate sheet title.

| Sheet name | Measure to display | Aggregation / format |
| --- | --- | --- |
| `Total Employee Count` | `EmployeeCount` | Sum; whole number |
| `Attrition Rate` | `Attrition Rate` | Percentage; two decimal places |
| `Job Satisfaction` | `JobSatisfaction` | Average; two decimal places |
| `Avg Monthly Income` | `MonthlyIncome` | Average; whole number with thousands separator |

These are separate sheets so they can be placed and sized as individual dashboard cards.

## 9. Assemble the dashboard

1. Click **New Dashboard** at the bottom of the Tableau window.
2. In the **Size** section of the Dashboard pane, select **Fixed Size** and enter **1500** wide by **850** high.
3. From **Objects**, use **Horizontal** and **Vertical** containers to create three areas:
   - a left column for **Employee Count by Experience** above **AgeWise Employee Count**;
   - a middle column for **Employee Count by Income** above **Average Monthly Income by age**;
   - a narrow right column for the heading, attrition legend, and four KPI cards.
4. In the **Sheets** list, drag each worksheet into its intended container. Watch for the blue placement indicator to confirm whether the sheet will be tiled in the correct container.
5. Resize the columns and rows by dragging their borders. Keep the two analysis columns wide enough for their axes and labels, and keep the KPI column narrow but readable.
6. From **Objects**, drag a **Text** object to the top of the right column. Enter **Hr Analytics Dashboard**, then click **OK**.
7. Drag the four KPI sheets into the right column, in this order: average monthly income, job satisfaction, attrition rate, total employee count.
8. Keep the **Attrition** color legend visible so the colors remain interpretable. In the project screenshots, `No` is purple and `Yes` is turquoise.
9. If a sheet title is duplicated or not needed, open that sheet's drop-down menu on the dashboard and toggle **Title**.

The finished view should have experience and age counts on the left, the income histogram and age-income comparison in the center, and the legend and headline cards on the right.

## 10. Add dashboard filtering and click interactions

The charts use `Attrition` color to distinguish employees who stayed from those who left. To add a visible attrition filter:

1. Select the worksheet containing the `Attrition` field and open the pill's menu on the Filters shelf.
2. Choose both `Yes` and `No`, then click **OK**.
3. Open the worksheet's drop-down menu on the dashboard and choose **Filters > Attrition** to show the filter control.
4. Open the filter card's menu and select **Apply to Worksheets > All Using This Data Source**.

To make a chart selection filter the other dashboard views:

1. On the dashboard, hover over a chart.
2. Click the **Use as Filter** funnel icon in the chart's upper-right toolbar.
3. Repeat for each chart that should drive filtering.
4. Click a bar or mark to filter the other views; click the selected mark again or use the dashboard's **Revert** control to clear the selection.

Use the filter control to select `Yes`, `No`, or both. Test the selection and clear it before saving so the dashboard opens in its unfiltered state.

## 11. Apply project formatting

Use the project's [Formatting.md](../../7.%20Miscellaneous%20Files/Formatting.md) as the style reference:

1. Set the dashboard/background to light gray (`#e6e6e6`).
2. Use purple (`#ad8bc9`) and turquoise (`#6dccda`) for the chart palette, keeping the attrition mapping consistent across sheets.
3. Use dark text (`#1b1b1b`) and Trebuchet MS.
4. Use approximately 8 pt for visualization labels, 15/12 pt for KPI values and labels, and 16 pt for the dashboard heading.
5. Check every title, axis, legend, and KPI at the fixed 1500 x 850 size. Increase a sheet's space or shorten a title if text is clipped.

## 12. Verify and save

Before delivery, check that:

- all five age groups appear in the intended order;
- attrition colors are consistent and the legend is visible;
- income bins are 2,000 wide;
- the age-income comparison has both measures and the difference has the correct sign;
- the KPI sheets show total employee count, attrition rate, average job satisfaction, and average monthly income;
- any filter or chart selection updates the intended views and can be cleared.

The supplied dashboard shows reference values of **1,470 employees**, **16.12% attrition**, **2.73 average job satisfaction**, and **6,503 average monthly income**. Small display differences may result from number formatting or rounding.

1. Choose **File > Save As**.
2. Select **Tableau Packaged Workbook (`.twbx`)** as the file type if Tableau prompts for a format.
3. Save the workbook in `4. Tableau File` and retain the CSV source in `2. Dataset` so the workbook can be refreshed.
