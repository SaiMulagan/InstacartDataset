# Instacart Data Exploration by Sai Mulagan

This project looks into data pertaining to instacart. We will examine its quality, resolve inconsistencies, and subsequently look into insights that could reshape how we understand online shopping. As we navigate through the data's labyrinth, you'll discover shopping peak times, the most coveted products, and habits that define a shopper's journey on Instacart. So, strap in and prepare for a data-driven excursion that promises to be as enlightening as it is engaging.


```python
import pandas as pd
import matplotlib.pyplot as plt
```

```python
instacart_orders = pd.read_csv('/datasets/instacart_orders.csv', delimiter=';')
display(instacart_orders.head())
instacart_orders.info(show_counts=True)
```

```python
products = pd.read_csv('/datasets/products.csv', delimiter=';', error_bad_lines=False)
print(products.head())
products.info(show_counts=True)
```

       product_id                                       product_name  aisle_id  \
    0           1                         Chocolate Sandwich Cookies        61   
    1           2                                   All-Seasons Salt       104   
    2           3               Robust Golden Unsweetened Oolong Tea        94   
    3           4  Smart Ones Classic Favorites Mini Rigatoni Wit...        38   
    4           5                          Green Chile Anytime Sauce         5   
    
       department_id  
    0             19  
    1             13  
    2              7  
    3              1  
    4             13  
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 49694 entries, 0 to 49693
    Data columns (total 4 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   product_id     49694 non-null  int64 
     1   product_name   48436 non-null  object
     2   aisle_id       49694 non-null  int64 
     3   department_id  49694 non-null  int64 
    dtypes: int64(3), object(1)
    memory usage: 1.5+ MB



```python
aisles = pd.read_csv('/datasets/aisles.csv', delimiter=';', error_bad_lines=False)
print(aisles.head())
aisles.info(show_counts=True)
```

       aisle_id                       aisle
    0         1       prepared soups salads
    1         2           specialty cheeses
    2         3         energy granola bars
    3         4               instant foods
    4         5  marinades meat preparation
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 134 entries, 0 to 133
    Data columns (total 2 columns):
     #   Column    Non-Null Count  Dtype 
    ---  ------    --------------  ----- 
     0   aisle_id  134 non-null    int64 
     1   aisle     134 non-null    object
    dtypes: int64(1), object(1)
    memory usage: 2.2+ KB



```python
departments = pd.read_csv('/datasets/departments.csv', delimiter=';', error_bad_lines=False)
print(departments.head())
departments.info(show_counts=True)
```

       department_id department
    0              1     frozen
    1              2      other
    2              3     bakery
    3              4    produce
    4              5    alcohol
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21 entries, 0 to 20
    Data columns (total 2 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   department_id  21 non-null     int64 
     1   department     21 non-null     object
    dtypes: int64(1), object(1)
    memory usage: 464.0+ bytes



```python
order_products = pd.read_csv('/datasets/order_products.csv', delimiter=';', error_bad_lines=False)
print(order_products.head())
order_products.info(show_counts=True)
```

       order_id  product_id  add_to_cart_order  reordered
    0   2141543       11440               17.0          0
    1    567889        1560                1.0          1
    2   2261212       26683                1.0          1
    3    491251        8670               35.0          1
    4   2571142        1940                5.0          1
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4545007 entries, 0 to 4545006
    Data columns (total 4 columns):
     #   Column             Non-Null Count    Dtype  
    ---  ------             --------------    -----  
     0   order_id           4545007 non-null  int64  
     1   product_id         4545007 non-null  int64  
     2   add_to_cart_order  4544171 non-null  float64
     3   reordered          4545007 non-null  int64  
    dtypes: float64(1), int64(3)
    memory usage: 138.7 MB


```python
# Check for duplicated orders
duplicates = instacart_orders.duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 15

```python
# Check for all orders placed Wednesday at 2:00 AM (assuming Wednesday is represented by 3)
wed_2am_orders = instacart_orders[(instacart_orders['order_dow'] == 3) & (instacart_orders['order_hour_of_day'] == 2)]
print(wed_2am_orders)
```

            order_id  user_id  order_number  order_dow  order_hour_of_day  \
    4838     2766110   162084            41          3                  2   
    5156     2190225   138285            18          3                  2   
    15506     553049    58599            13          3                  2   
    18420     382357   120200            19          3                  2   
    24691     690242    77357             2          3                  2   
    ...          ...      ...           ...        ...                ...   
    457013   3384021    14881             6          3                  2   
    458816    910166   164782            18          3                  2   
    459635   1680532   106435             6          3                  2   
    468324    222962    54979            59          3                  2   
    477526   2592344    46860            38          3                  2   
    
            days_since_prior_order  
    4838                      16.0  
    5156                      11.0  
    15506                      7.0  
    18420                     11.0  
    24691                      9.0  
    ...                        ...  
    457013                    30.0  
    458816                     4.0  
    459635                    21.0  
    468324                     3.0  
    477526                     3.0  
    
    [121 rows x 6 columns]



```python
# Remove duplicate orders
instacart_orders = instacart_orders.drop_duplicates()
```


```python
# Double check for duplicate rows
print(f"After removal, number of duplicated rows: {instacart_orders.duplicated().sum()}")
```

    After removal, number of duplicated rows: 0



```python
# Double check for duplicate order IDs only
print(f"Number of duplicated order IDs: {instacart_orders['order_id'].duplicated().sum()}")
```

    Number of duplicated order IDs: 0


Checking for Duplicates
We started by checking for completely duplicated rows in the orders dataframe. These are rows where all values across all columns match another row exactly.

Checking Orders on Wednesday at 2:00 AM
This is a random check. By focusing on a specific time (and assuming that '3' represents Wednesday), we can manually inspect some entries and get a feel for the data's quality.

Removing Duplicates
If any fully duplicated rows are found, they are removed. It's essential to eliminate duplicates to ensure data integrity. Two orders being entirely identical is suspicious and probably indicates a data error.

Double-Checking
After removing duplicates, we double-checked for any remaining ones. This is to confirm that our cleaning was successful.

### `products` data frame


```python
# Check for fully duplicate rows
duplicates = products.duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0


```python
# Check for just duplicate product IDs
print(f"Number of duplicate product IDs: {products['product_id'].duplicated().sum()}")
```

    Number of duplicate product IDs: 0



```python
# Check for just duplicate product names (convert names to lowercase to compare better)
products['product_name_lower'] = products['product_name'].str.lower()
print(f"Number of duplicate product names: {products['product_name_lower'].duplicated().sum()}")
```

    Number of duplicate product names: 1361



```python
# Check for duplicate product names that aren't missing
non_missing_names = products['product_name_lower'].dropna()
print(f"Number of non-missing duplicate product names: {non_missing_names.duplicated().sum()}")
```

    Number of non-missing duplicate product names: 104

Checking for Fully Duplicate Rows
Similar to the orders dataframe, we began by identifying and removing any fully duplicated rows.

Checking Product IDs and Names
We checked for duplicate product IDs and names separately because:

Two products shouldn't have the same ID, even if other information about them differs.
Product names might be duplicated due to variations in casing or accidental reentries, so converting them to lowercase can reveal such issues.

### `departments` data frame


```python
duplicates = departments.duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0



```python
# If duplicates are found, they can be removed with:
# departments = departments.drop_duplicates()
```

```python
duplicates = departments['department_id'].duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0


### `aisles` data frame


```python
# Check for fully duplicate rows
duplicates = aisles.duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0



```python
# If duplicates are found, they can be removed with:
# aisles = aisles.drop_duplicates()
```


```python
# Check for fully duplicate rows
duplicates = aisles['aisle_id'].duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0


Both these dataframes likely have fewer unique entries (as there are limited numbers of departments and aisles). So, the primary concern is checking for and removing fully duplicated rows.

### `order_products` data frame


```python
# Check for fullly duplicate rows
duplicates = order_products.duplicated()
print(f"Number of duplicated rows: {duplicates.sum()}")
```

    Number of duplicated rows: 0



```python
# Double check for any other tricky duplicates
# Find instances where the same product has both reordered = 1 and reordered = 0 for the same order
grouped_reordered = order_products.groupby(['order_id', 'product_id'])['reordered'].nunique().reset_index()

# Filter for cases where nunique is more than 1, indicating inconsistent reordered flags
inconsistent_reordered = grouped_reordered[grouped_reordered['reordered'] > 1]

print(inconsistent_reordered)
```

    Empty DataFrame
    Columns: [order_id, product_id, reordered]
    Index: []


```python
duplicate_rows = order_products.duplicated(subset=['order_id', 'product_id'], keep=False)

# Display the duplicates
duplicates = order_products[duplicate_rows]
print(duplicates)
```

    Empty DataFrame
    Columns: [order_id, product_id, add_to_cart_order, reordered]
    Index: []


Checking for Fully Duplicate Rows
Given that this dataframe links products to orders, it's possible for some rows to look the same if a user orders the same product in different orders. Still, fully duplicated rows (same order and product IDs) indicate a potential error and should be removed.
Reordered Flag Inconsistencies:
If the same product is added multiple times to an order, the reordered flag should be consistent. If it's not, this might be a data inconsistency.

## Find and remove missing values


### `products` data frame


```python
# Are all of the missing product names associated with aisle ID 100?
missing_names_aisle_100 = products[(products['product_name'].isnull()) & (products['aisle_id'] == 100)]
print(f"Missing product names associated with aisle ID 100: {missing_names_aisle_100.shape[0]}")
```

    Missing product names associated with aisle ID 100: 1258


All of the missing product names are associated with aisle ID 100


```python
# Are all of the missing product names associated with department ID 21?
missing_names_dept_21 = products[(products['product_name'].isnull()) & (products['department_id'] == 21)]
print(f"Missing product names associated with department ID 21: {missing_names_dept_21.shape[0]}")
```

    Missing product names associated with department ID 21: 1258


All of the missing product names are also associated with department ID 21


```python
# What is this ailse and department?
# Getting the aisle name for aisle ID 100
aisle_name = aisles[aisles['aisle_id'] == 100]['aisle'].values[0]
print(f"Aisle ID 100 corresponds to: {aisle_name}")

# Getting the department name for department ID 21
dept_name = departments[departments['department_id'] == 21]['department'].values[0]
print(f"Department ID 21 corresponds to: {dept_name}")

total_missing_names = products['product_name'].isnull().sum()
print(f"Total missing product names: {total_missing_names}")
```

    Aisle ID 100 corresponds to: missing
    Department ID 21 corresponds to: missing
    Total missing product names: 1258


The product name for the ailse and department containing the missing product names is missing

```python
# Fill missing product names with 'Unknown'
products['product_name'].fillna('Unknown', inplace=True)
```

### `orders` data frame


```python
# Are there any missing values where it's not a customer's first order?
missing_values_not_first_order = instacart_orders[(instacart_orders['order_number'] > 1) & (instacart_orders['days_since_prior_order'].isnull())]
print(f"Number of missing 'days_since_prior_order' values where it's not a customer's first order: {missing_values_not_first_order.shape[0]}")

```

    Number of missing 'days_since_prior_order' values where it's not a customer's first order: 0


```python

instacart_orders[(instacart_orders['order_number'] != 1)].isna().sum()


```

If the number is non-zero, it would mean there are some inconsistencies in the data where days_since_prior_order is missing, even though it isn't the customer's first order.

### `order_products` data frame


```python

```


```python
# What are the min and max values in this column?
min_value = order_products['add_to_cart_order'].min()
max_value = order_products['add_to_cart_order'].max()

print(f"Minimum value in 'add_to_cart_order': {min_value}")
print(f"Maximum value in 'add_to_cart_order': {max_value}")

```

    Minimum value in 'add_to_cart_order': 1.0
    Maximum value in 'add_to_cart_order': 64.0



```python
# Save all order IDs with at least one missing value in 'add_to_cart_order'
orders_with_missing_values = order_products[order_products['add_to_cart_order'].isnull()]['order_id'].unique()
print(f"Number of orders with missing 'add_to_cart_order': {len(orders_with_missing_values)}")

```

    Number of orders with missing 'add_to_cart_order': 70



```python
# Do all orders with missing values have more than 64 products?
# Count products for each order
order_product_counts = order_products.groupby('order_id').size()

# Filter orders with missing values and check if they have more than 64 products
orders_above_64_products = order_product_counts[orders_with_missing_values][order_product_counts > 64].index

print(f"Number of orders with missing values that have more than 64 products: {len(orders_above_64_products)}")

```

    Number of orders with missing values that have more than 64 products: 70


Yes, every single order that has a missing 'add_to_cart_order' also has more than 64 products.


```python
# Replace missing values with 999 and convert column to integer type
order_products['add_to_cart_order'].fillna(999, inplace=True)
order_products['add_to_cart_order'] = order_products['add_to_cart_order'].astype(int)

```

### [A1] Verify that the `'order_hour_of_day'` and `'order_dow'` values in the `orders` tables are sensible (i.e. `'order_hour_of_day'` ranges from 0 to 23 and `'order_dow'` ranges from 0 to 6)


```python
# Verifying 'order_hour_of_day'
valid_hours = instacart_orders['order_hour_of_day'].between(0, 23).all()
print(f"All values in 'order_hour_of_day' are between 0 and 23: {valid_hours}")
```

    All values in 'order_hour_of_day' are between 0 and 23: True



```python
# Verifying 'order_dow'
valid_dow = instacart_orders['order_dow'].between(0, 6).all()
print(f"All values in 'order_dow' are between 0 and 6: {valid_dow}")
```

    All values in 'order_dow' are between 0 and 6: True


'order_hour_of_day' and 'order_dow' values in the orders tables are sensible


### [A2] What time of day do people shop for groceries?


```python
order_counts_by_hour = instacart_orders['order_hour_of_day'].value_counts().sort_index()

order_counts_by_hour.plot(kind='bar', figsize=(10, 5))
plt.title("Number of Orders for Each Hour of the Day")
plt.xlabel("Hour of the Day")
plt.ylabel("Number of Orders")
plt.show()

```


    
![png](output_86_0.png)
    


Plot that shows how many people place orders for each hour of the day. It can be inferred that most people shop from 10:00am to 6:00pm.


### [A3] What day of the week do people shop for groceries?


```python
order_counts_by_dow = instacart_orders['order_dow'].value_counts().sort_index()
days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]

order_counts_by_dow.index = days
order_counts_by_dow.plot(kind='bar', figsize=(10, 5))
plt.title("Number of Orders for Each Day of the Week")
plt.xlabel("Day of the Week")
plt.ylabel("Number of Orders")
plt.show()
```


    
![png](output_90_0.png)
    


Plot that shows what day of the week people shop for groceries. We can ssee that Sunday and Mondday are the most popular days of the week to shop. Wednesday and Thurday is when there are the least orders.


### [A4] How long do people wait until placing another order?


```python
time_until_next_order = instacart_orders['days_since_prior_order'].value_counts().sort_index()

time_until_next_order.plot(kind='bar', figsize=(15, 5))
plt.title("Time Until Placing Next Order")
plt.xlabel("Days Since Prior Order")
plt.ylabel("Number of Orders")
plt.show()
```


    
![png](output_94_0.png)
    



```python
print(f"Minimum waiting time: {instacart_orders['days_since_prior_order'].min()}")
print(f"Maximum waiting time: {instacart_orders['days_since_prior_order'].max()}")
```

    Minimum waiting time: 0.0
    Maximum waiting time: 30.0


These plots and validations should provide a clear insight into the shopping patterns of Instacart users. Most people tend to wait one full month or one full week to order instacart again. 


### [B1] Is there a difference in `'order_hour_of_day'` distributions on Wednesdays and Saturdays? Plot the histograms for both days and describe the differences that you see.


```python
# Filter the dataframe for orders on Wednesday and Saturday
wednesday_orders = instacart_orders[instacart_orders['order_dow'] == 3]['order_hour_of_day']
saturday_orders = instacart_orders[instacart_orders['order_dow'] == 6]['order_hour_of_day']
```

```python
# Plot the histograms
plt.figure(figsize=(12, 6))
plt.hist(wednesday_orders, bins=24, alpha=0.5, label='Wednesday')
plt.hist(saturday_orders, bins=24, alpha=0.5, label='Saturday')
plt.title("Order Hour Distribution on Wednesdays vs Saturdays")
plt.xlabel("Hour of the Day")
plt.ylabel("Number of Orders")
plt.legend(loc="upper right")
plt.show()
```


    
![png](output_101_0.png)
    


Plots depicted when orders typically occur during the day, which days are most popular for shopping, and the intervals between customer orders.


### [B2] What's the distribution for the number of orders per customer?


```python
# Group the dataframe by user_id and count their orders
order_counts = instacart_orders.groupby('user_id').size()
```


```python
# Plot a histogram
plt.figure(figsize=(12, 6))
order_counts.value_counts().sort_index().plot(kind='bar')
plt.title("Distribution of Number of Orders per Customer")
plt.xlabel("Number of Orders")
plt.ylabel("Number of Customers")
plt.show()
```


    
![png](output_106_0.png)
    


A histogram was used to illustrate the number of orders various customers placed, shedding light on customer retention. We can see how most customers will only make 1 instcart order. Most costumers tend to order less orders.


### [B3] What are the top 20 popular products (display their id and name)?


```python
# Merge products and order_products to get names of products
merged_data = order_products.merge(products, on='product_id')

# Group by product name and count
top_products = merged_data['product_name'].value_counts().head(20)

print(top_products)
```

    Banana                      66050
    Bag of Organic Bananas      53297
    Organic Strawberries        37039
    Organic Baby Spinach        33971
    Organic Hass Avocado        29773
    Organic Avocado             24689
    Large Lemon                 21495
    Strawberries                20018
    Limes                       19690
    Organic Whole Milk          19600
    Organic Raspberries         19197
    Organic Yellow Onion        15898
    Organic Garlic              15292
    Organic Zucchini            14584
    Organic Blueberries         13879
    Cucumber Kirby              13675
    Organic Fuji Apple          12544
    Organic Lemon               12232
    Apple Honeycrisp Organic    11993
    Organic Grape Tomatoes      11781
    Name: product_name, dtype: int64



```python
# Plot the top 20 products
plt.figure(figsize=(15, 10))
top_products.plot(kind='barh', color='skyblue')
plt.title("Top 20 Most Frequently Ordered Products")
plt.xlabel("Number of Orders")
plt.ylabel("Product Name")
plt.gca().invert_yaxis()  # This is to have the product with the highest orders at the top
plt.show()
```


    
![png](output_111_0.png)
    


To understand product popularity, we extracted and showcased the top 20 products that customers ordered most frequently. This not only identified the bestsellers but also provided a visual representation to quickly gauge customer preferences. We can see how bananas are the best selling product.



### [C1] How many items do people typically buy in one order? What does the distribution look like?


```python
# Group by order_id to get the number of products per order
items_per_order = order_products.groupby('order_id')['product_id'].count()
```


```python
plt.figure(figsize=(12, 6))
plt.hist(items_per_order, bins=70, edgecolor='k', alpha=0.7)
plt.title('Distribution of Items per Order')
plt.xlabel('Number of Items')
plt.ylabel('Number of Orders')
plt.show()
```


    
![png](output_117_0.png)
    

### [C2] What are the top 20 items that are reordered most frequently (display their names and product IDs)?


```python
reordered_products = order_products[order_products['reordered'] == 1]
```


```python
top_20_reordered = reordered_products['product_id'].value_counts().head(20)
```


```python
top_20_reordered_names = products[products['product_id'].isin(top_20_reordered.index)][['product_id', 'product_name']]
```
    
    
> products[products['product_id'].isin(top_20_reordered.index)
    

```python
print(top_20_reordered_names)
```

           product_id              product_name
    5875         5876             Organic Lemon
    8276         8277  Apple Honeycrisp Organic
    13175       13176    Bag of Organic Bananas
    16796       16797              Strawberries
    21136       21137      Organic Strawberries
    21902       21903      Organic Baby Spinach
    22934       22935      Organic Yellow Onion
    24851       24852                    Banana
    24963       24964            Organic Garlic
    26208       26209                     Limes
    27844       27845        Organic Whole Milk
    27965       27966       Organic Raspberries
    28203       28204        Organic Fuji Apple
    39274       39275       Organic Blueberries
    45006       45007          Organic Zucchini
    47208       47209      Organic Hass Avocado
    47625       47626               Large Lemon
    47765       47766           Organic Avocado
    49234       49235       Organic Half & Half
    49682       49683            Cucumber Kirby


This analysis identifies the 20 products that customers tend to repurchase most frequently, indicating popular and potentially essential items.

### [C3] For each product, what proportion of its orders are reorders?


```python
total_orders_per_product = order_products['product_id'].value_counts()
reorders_per_product = reordered_products['product_id'].value_counts()
```


```python
reorder_proportion = (reorders_per_product / total_orders_per_product).reset_index()
reorder_proportion.columns = ['product_id', 'reorder_proportion']
```


```python
reorder_proportion_merged = pd.merge(reorder_proportion, products[['product_id', 'product_name']], on='product_id')
print(reorder_proportion_merged.head())
```

       product_id  reorder_proportion  \
    0           1            0.564286   
    1           2                 NaN   
    2           3            0.738095   
    3           4            0.510204   
    4           7            0.500000   
    
                                            product_name  
    0                         Chocolate Sandwich Cookies  
    1                                   All-Seasons Salt  
    2               Robust Golden Unsweetened Oolong Tea  
    3  Smart Ones Classic Favorites Mini Rigatoni Wit...  
    4                     Pure Coconut Water With Orange  




### [C4] For each customer, what proportion of their products ordered are reorders?


```python
merged_order_products = pd.merge(order_products, instacart_orders, on="order_id")
customer_reordered = merged_order_products.groupby('user_id')['reordered'].mean().reset_index()
```


```python
print(customer_reordered.head())
```

       user_id  reordered
    0        2   0.038462
    1        4   0.000000
    2        5   0.666667
    3        6   0.000000
    4        7   0.928571


### [C5] What are the top 20 items that people put in their carts first? 


```python
first_items = order_products[order_products['add_to_cart_order'] == 1]
```


```python
top_20_first_items = first_items['product_id'].value_counts().head(20)
top_20_first_items_names = products[products['product_id'].isin(top_20_first_items.index)][['product_id', 'product_name']]
```


```python
print(top_20_first_items_names)
```

           product_id                 product_name
    195           196                         Soda
    5784         5785  Organic Reduced Fat 2% Milk
    12340       12341                Hass Avocados
    13175       13176       Bag of Organic Bananas
    16796       16797                 Strawberries
    19659       19660                 Spring Water
    21136       21137         Organic Strawberries
    21902       21903         Organic Baby Spinach
    22934       22935         Organic Yellow Onion
    24851       24852                       Banana
    26208       26209                        Limes
    27085       27086                  Half & Half
    27844       27845           Organic Whole Milk
    27965       27966          Organic Raspberries
    38688       38689     Organic Reduced Fat Milk
    44631       44632   Sparkling Water Grapefruit
    47208       47209         Organic Hass Avocado
    47625       47626                  Large Lemon
    47765       47766              Organic Avocado
    49234       49235          Organic Half & Half





Throughout our in-depth exploration of the Instacart dataset, we addressed data quality by managing duplicates and filling in missing values, ensuring a solid foundation for analysis. Our investigation illuminated key shopper behaviors, revealing peak shopping hours and preferred days, as well as shopper product preferences, which are vital for operational decisions. Further, by analyzing reordering trends and shopping patterns, we gained insights into customer loyalty and purchasing habits. We can use this information to optimize the services.

