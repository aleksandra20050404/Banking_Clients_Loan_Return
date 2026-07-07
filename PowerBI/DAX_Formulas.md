
---

## Calculated Table: `DimDate`

Built with **Modeling** tab → **New table**:

```dax
DimDate =
ADDCOLUMNS(
    CALENDAR(DATE(YEAR(MIN(final_data[Joined_Bank])),1,1), DATE(YEAR(MAX(final_data[Joined_Bank])),12,31)),
    "Year", YEAR([Date]),
    "MonthNo", MONTH([Date]),
    "MonthName", FORMAT([Date],"MMMM"),
    "Quarter", "Q" & ROUNDUP(MONTH([Date])/3,0)
)
```
Then marked as the model's official Date Table (**Modeling** tab → **Mark as date table**), using `Date` as the date column.

**Relationship:** `final_data[Joined_Bank]` (Many) → `DimDate[Date]` (One), single-direction cross-filter, active.

---

## Measures — Home Folder

```dax
Total Clients = DISTINCTCOUNT(final_data[Client_ID])
```

```dax
Total Deposits = SUM(final_data[Bank_Deposits])
```

```dax
Total Loans = SUM(final_data[Bank_Loans])
```

```dax
Total Credit Card Balance = SUM(final_data[Credit_Card_Balance])
```

```dax
Total Superannuation = SUM(final_data[Superannuation_Savings])
```

```dax
Avg Estimated Income = AVERAGE(final_data[Estimated_Income])
```

```dax
Clients Joined (Selected Period) = CALCULATE([Total Clients])
```

```dax
% of Total Clients =
DIVIDE([Total Clients], CALCULATE([Total Clients], ALL(final_data)))
```

```dax
Client Growth YoY =
VAR CurrentYearClients = [Total Clients]
VAR PriorYearClients = CALCULATE([Total Clients], SAMEPERIODLASTYEAR(DimDate[Date]))
RETURN DIVIDE(CurrentYearClients - PriorYearClients, PriorYearClients)
```

---

## Measures — Loan Folder

```dax
Total Business Lending = SUM(final_data[Business_Lending])
```

```dax
Avg Risk Weighting = AVERAGE(final_data[Risk_Weighting])
```

```dax
Total Properties Owned = SUM(final_data[Properties_Owned])
```

```dax
Avg Credit Cards Held = AVERAGE(final_data[Amount_of_Credit_Cards])
```

```dax
% High Risk Clients =
DIVIDE(CALCULATE([Total Clients], final_data[RiskCategory] = "Very High"), [Total Clients])
```

---

## Measures — Deposits Folder

```dax
Total Savings Accounts = SUM(final_data[Saving_Accounts])
```

```dax
Total Checking Accounts = SUM(final_data[Checking_Accounts])
```

```dax
Total Foreign Currency = SUM(final_data[Foreign_Currency_Account])
```

---

## Format Strings Applied (for reference when rebuilding manually)

| Measure | Format String |
|---|---|
| Total Clients, Total Properties Owned | `#,##0` |
| Total Deposits, Total Loans, Total Credit Card Balance, Total Business Lending, Total Savings Accounts, Total Checking Accounts, Total Foreign Currency, Total Superannuation | `$#,##0,,"M"` |
| Avg Estimated Income | `$#,##0` |
| Avg Risk Weighting, Avg Credit Cards Held | `0.0` |
| Clients Joined (Selected Period) | `#,##0` |
| % of Total Clients, % High Risk Clients, Client Growth YoY | `0.0%` |

---

## Not Rebuilt as DAX (already existed in your source data)
`IncomeCategory` and `RiskCategory` were already derived columns present in `final_data` when I first connected — I didn't recreate these, so their formulas aren't listed here. If you want their exact expressions, I can pull those from the model too — just say the word.

---

## How to Recreate Any of These Manually
1. **Home** ribbon or **Modeling** ribbon → **New measure**.
2. Type the formula bar exactly as shown above (everything after the `=`).
3. Press **Enter**.
4. With the measure selected in the Data pane, use the **Measure tools** contextual ribbon tab → **Formatting** group to set the format string shown in the table above.

