---
title: "How to Add a Fiscal Year Column in Power Query Editor"
date: 2026-08-04T19:53:00+08:00
draft: false
tags:
  - tech
  - power-query
  - excel
  - data-analysis
summary: "Add a fiscal year column in Power Query using a simple M code formula based on your fiscal start month."
description: "Step-by-step guide to creating a fiscal year custom column in Power Query Editor with M code formulas for April-start and October-start fiscal years."
---

# How to Add a Fiscal Year Column in Power Query Editor

To add a fiscal year column in Power Query Editor, use a Custom Column with an M code formula tailored to your fiscal start month.

## Steps to Add the Column

1. Go to the **Add Column** tab in the top ribbon.
2. Click on **Custom Column**.
3. Type a name for your new column (e.g., `FiscalYear`).
4. Enter the formula based on when your fiscal year starts, then click **OK**.

## Formulas by Fiscal Start Month

### Fiscal Year Starts in April (April 1 – March 31)

If the month is January through March (month number ≤ 3), the fiscal year matches the calendar year; otherwise, it is the next year.

```powerquery
= if Date.Month([Date]) <= 3 then Date.Year([Date]) else Date.Year([Date]) + 1
```

*(Replace `[Date]` with the exact name of your date column.)*

### Fiscal Year Starts in October (October 1 – September 30)

If the month is October or later (month number ≥ 10), add 1 to the calendar year; otherwise, use the current calendar year.

```powerquery
= if Date.Month([Date]) >= 10 then Date.Year([Date]) + 1 else Date.Year([Date])
```
