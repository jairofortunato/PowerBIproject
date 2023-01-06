# PowerBIproject
More information can be found it here: https://jairofortunato.notion.site/Power-BI-Project-Super-Store-Dashboard-9ded1f6da8a74a66a3c365fe0072061a

# Prepare and process data

### Fixing decimal values

The preprocessing was to be made in Power Query and Excel but the columns with number values come with problems, the data type is text value and when imported all the “.” disappear, so SQL was used to transform the data before importing to Power BI:

```sql
UPDATE samplesuperstore
SET Sales = ROUND(Sales, 2);
UPDATE samplesuperstore
SET Profit = ROUND(Profit, 2);
```

The solution was to change the points to commas but first we change the data type because we cant UPDATE characters in INT data type:

```sql
ALTER TABLE samplesuperstore MODIFY COLUMN Sales VARCHAR(10) NOT NULL, 
    MODIFY Discount VARCHAR(10) NOT NULL, 
    MODIFY Profit VARCHAR(10)NOT NULL;
```

Then we can change the “.” to “,”:

```sql
UPDATE samplesuperstore
SET Sales = REPLACE(Sales, '.', ',');

UPDATE samplesuperstore
SET Profit = REPLACE(Profit, '.', ',');

UPDATE samplesuperstore
SET Discount = REPLACE(Discount, '.', ',');
```

Now when imported to Power Query and Excel we get the right values.


### Fixing date data type

The columns with dates are in text data type, when transforming to date type we get a lot of values with error. After analise was perceived that all the error values worent model as day**,** then month**,** then the year**:** DD-MM-YYYY, my Power Bi identify date only this way and a lot of values like 04/20;/2017 as MM-DD-YYYY couldnt by recognize. Was fixed following these steps:

1. In the Power BI desktop, select the "File" menu and then select "Options and settings."
2. In the Options and settings window, select "Current File" from the left menu.
3. Under the "Data Load" section, you will see a dropdown for "Date formatting options." Select "mm/dd/yyyy" from this dropdown.
4. Click "OK" to apply the changes.

# Filters to easily navigation

The columns that can be filtered are quarter, segment, state, region, category, profit/loss.

First we create new columns to find values that dont have columns:

**Quarter and month**

```
Quarter = "Q" & FORMAT([Order Date], "Q") & " " & FORMAT([Order Date], "yyyy")
```

```
Month = FORMAT([Order Date], "MMM") & " " & FORMAT([Order Date], "yyyy")
```


# Cards with key metrics


For total loss i did this measure:

```
Losses = SUMX(FILTER(samplesuperstore, [Profit] < 0), [Profit]
```

For Profit Margin was created this column:

```
Profit Margin = ROUND(([Profit] / [Sales]) * 100,2) / 100
```

### Visual graphics
[samplesuperstoredashboard.pdf](https://github.com/jairofortunato/PowerBIproject/files/10360815/samplesuperstoredashboard.pdf)
![superstore print](https://user-images.githubusercontent.com/71857060/211035365-8fb6b9ac-dbaf-4a95-a7e0-9678b7cc08f3.png)

![superstore print2](https://user-images.githubusercontent.com/71857060/211035398-4cc7d506-0ca3-4e27-bc43-e8a652d65436.png)

