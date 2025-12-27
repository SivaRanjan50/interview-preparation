# interview-preparation

### 1.	What is the difference between CALCULATE and CALCULATETABLE? When would you use one over the other?
```
In Dax, Calculate and calculatetable are both filter-modifying functions,
but they return different types of results and used in different contexts.
  Calculate:
      i. returns a scalar value(single value)
      ii. used in measures and calculated columns
        syntax: CALCULATE(<expression>, <filter1>, ...)
      iii. modified filter context for scalar computation

  Calculatetable:
      i. returns a table
      ii. used to create a table
        Syntax: CALCULATETABLE(<table>, <filter1>, ...)
      iii. modifies filter context for table computation

when to use:
    calculate:
        i. creating measures
        ii. computing aggregations like sum,avg,count with modify filters.
        iii. writing scalar calculations in calculated columns
            ex:
                Total Sales Europe = 
                                CALCULATE(
                                    SUM(Sales[Amount]),
                                    Sales[Region] = "Europe"
                                )
    calculate tables:
        i. creating calculated tables in powerbi desktop
        ii. defining table variables inside dax expressions
        iii. feeding filter table into functions like filter, addcolumns or summarize
        iv. using a source for relationships or other table operations
            ex:
                European Customers = 
                                CALCULATETABLE(
                                    Customers,
                                    Customers[Country] = "Germany" || Customers[Country] = "France"
                                )
```

### 2.	How do you handle role-playing dimensions (e.g., a Date table connected to OrderDate and ShipDate)?
```
For role-playing dimensions like a Date table connected to OrderDate and ShipDate, I use the multiple date tables approach. 
I create separate physical date tables for each role - one for OrderDate, another for ShipDate.
```

###### Why?
```
This approach keeps things simple and intuitive. Users can easily drag 'Order Date' fields from the OrderDate table and
'Ship Date' fields from the ShipDate table without confusing the context. Each table has its own relationship to the fact table, 
so calculations work naturally without needing complex DAX workarounds.
```

###### maintainance?
```
While this means maintaining multiple date tables, in practice I create them all from a single source using Power Query,
so any updates to date logic only need to be made once and propagate to all tables.
```
```
It improves report performance since each calculation uses a direct relationship
It makes the model self-documenting - anyone looking at the relationships can immediately understand the design

Overall, this approach balances technical efficiency with user experience, making reports intuitive for business users while
keeping the data model performant and maintainable.
```
###### If they ask follow-up questions, you're ready:
```
"What if you have 10 date roles?" → "I'd still create separate tables, but automate the generation in Power Query."
"Isn't this data duplication?" → "Yes, but it's minimal - date tables are usually small, and the clarity gains
outweigh storage costs."
"How do you handle time intelligence?" → "Each date table can have its own time intelligence measures, or
I use calculation groups for shared logic."
```


### 3.	Explain row-level security (RLS) and how you would implement it dynamically based on a user's login ID.
###### What is RLS?
```
Row-Level Security is a feature in Power BI that restricts data access at the row level based on user identity.
It ensures users only see data they're authorized to view, like a salesperson seeing only their region's sales data.
```
###### Dynamic RLS Implementation Based on Login ID:
###### 1. Basic Structure:
```
-- Fact table relationships:
Users[UserID] ←→ UserRoles[UserID]
UserRoles[RoleID] ←→ Roles[RoleID]
Roles[RegionID] ←→ Regions[RegionID]
```
###### 2. Implementation Steps:
###### Step 1: Capture User Identity
```
-- In Power BI Service:
CurrentUser = USERPRINCIPALNAME()  -- e.g., "john@company.com"
-- Or:
CurrentUserID = USERNAME()         -- e.g., "JSMITH"
```

###### Step 2: Create Security Table
```
UserAccess Table:
UserEmail        | Region     | Department
john@company.com | Northeast  | Sales
sarah@company.com| West       | Marketing
```
###### Step 4: Write DAX Filter Rules
```
-- Region-based security:
[Region] = LOOKUPVALUE(
    UserAccess[Region],
    UserAccess[UserEmail], USERPRINCIPALNAME()
)

-- Department-based security:
[Department] IN VALUES(UserAccess[Department])
```







