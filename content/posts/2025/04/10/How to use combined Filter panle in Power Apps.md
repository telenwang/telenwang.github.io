+++
date = '2025-04-11T10:25:44+02:00'
draft = true
title = 'CombinedFilterPowerApps'
+++
---
layout: post
title: "How to use combined filter panel in Power Apps"
date: 2025-04-09 10:00:00 -0400
categories: [blog, tech]
excerpt: "A brief description or summary of my article."
---
# How to use combined filter panel in Power Apps

#### In this article, I want to explore a way to use combined filter controls and pass the values to dataset.

I used Accounts(Sample data）table for the demostration, you can use your own data if you'd like.

##### Control list.

|Control |Refer to|Source Data|
|---------------|--------------|------------------|
|ComBox   |Business Type|   Account|
|Toggle   |Credit Hold| Account|
|DropDown(Preview)   |  Relationship Type|Account|
|Reset|Reset All conrols|n/a|
|Table(preview)|collection|Account|

The layout will be like this screen shot.

![Combined controls](Controls.png)
![Sample](Filter.gif)
##### Key function is Filter.
Here is from Microsoft.
#### Syntax
Filter(Table*, Formula1 [, *Formula2*, ... ] )

- Table - Required. Table to search.
- Formula(s) - Required. The formula by which each record of the table is evaluated. The function returns all records that result in true. You can reference columns within the table. If you supply more than one formula, the results of all formulas are combined with the And function.

Except the normal operators, you can use in and exactin operators for substring matches.

When use controls like Combox, DropDown, List, etc, the data type of those data are table.
So, when use for example Combox, in operator will be in my choice list.
Below is the data in the table, and I want to filter Business type in ("EMS","OEM").

![Combined controls](businessType.png)

The code could be like :
```sql
FROM Account WHERE 'Business Type' IN ( Combox.dataset)
//It's not a real code, I want to use T-SQL to explain it.
```
The 'in' operator, find a string in a data source, such as a collection or an imported table.
If we inteprete to Power Fx.
```
Filter(
    Account,
    'Business Type' in Combox.SelectedItems
)
```
Or if you want narrow down to the value only, use ShowColumns function, it will remove other columns and only return the columns you want, here it will be value column, Note, here is still a table type even it only has one column.
```
Filter(
    Account,
    'Business Type' in ShowColumns(Combox.SelectedItems,Value)
)
```
Similarly, we can combine other controls as input for the Filter formula.

Below is step by step for the journey.
##### 1. Assign data to Combox.
```
Items = Choices('Business Type (Accounts)')
IsSearchable = false //default is true.
```

##### 2. Assign data to DropDown.
```
Items = Choices('Relationship Type (Accounts)')
DefaultSelectedItems= []
```

##### 3. Reset.
```
OnSelect = Reset(ComboBox1);Reset(DropdownCanvas2);
```

##### 4. Add Button and add code for OnSelect.

```powerapps
ClearCollect(
    myAccount,
    RenameColumns(
        ShowColumns(
            Filter(
                Accounts,
                (IsBlank(ComboBox1.SelectedItems) || 'Business Type' in ComboBox1.SelectedItems) 
                And (IsEmpty(DropdownCanvas2.SelectedItems) || 'Relationship Type' in ShowColumns(
                    DropdownCanvas2.SelectedItems,
                    Value
                )) 
                And 'Credit Hold' = Toggle1.Checked
            ),
            'Account Name',
            'Main Phone',
            'Address 1: City',
            'Primary Contact',
            'Credit Limit'
           
        ),
        name,'Customer Name',
        address1_city,'City',
        creditlimit,'Credit Limit',
        primarycontactid,'Primary Contact',
        telephone1,'Main Phone'
    )
)
```
Enjoy.