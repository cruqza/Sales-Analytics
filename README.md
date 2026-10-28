# Electronics Sales Analysis

Run [sales_analysis.ipynb](sales_analysis.ipynb) from the project root. It reads `data/electronics_sales.csv`, which contains 50 sales records and the columns `sale_id`, `sale_date`, `store`, `product`, `quantity`, `unit_price_kes`, and `payment_method`.

## 1. Read the CSV

`read_sales(path)` uses Python's `csv.DictReader` to return rows as dictionaries, then prints the row count and CSV column names. No pandas is used for the main CSV-reading step.

## 2. Parse and clean numeric values

`parse_number(value)` returns a number for values it can interpret safely and `None` for values it cannot. It trims surrounding whitespace, ignores letter case, recognizes a leading `KES` label, converts spelled-out number words from zero through ten, and removes commas only when they form valid thousands groups (for example, `38,999`). These transformations preserve the stated numeric meaning of the source values. Empty strings and `N/A`, `NONE`, `NULL`, and `-` represent missing values; malformed numbers, invalid comma grouping, and non-finite numbers are not guessed and are treated as unparseable.

`convert_rows(rows)` converts quantity and unit price, derives each row's revenue as quantity × unit price, and returns both usable rows and skipped rows. A row with an unparseable quantity or unit price is excluded and recorded with its CSV row number, original data, and reason. On the supplied data, `two` is repaired to 2; 47 rows are kept and three are skipped: `S-3030` (invalid unit price), `S-3035` (invalid quantity), and `S-3039` (invalid unit price).

## 3. Total revenue

`total_revenue(rows)` sums quantity × unit price across usable rows. The total is **KES 1,317,210.00**, printed with two decimal places and thousands separators.

## 4. Revenue by category

`revenue_by(rows, field)` uses a plain dictionary to total revenue by product, store, or payment method. Product revenue is printed in descending revenue order; the store and payment-method totals are also printed.

## 5. Top three products

`top_n(totals, n)` uses `sorted()` and returns `(product, revenue)` tuples from highest revenue down. Equal revenues are ordered alphabetically by product name without regard to case.

## 6. High-value transactions

`high_value(rows, threshold)` uses a list comprehension and includes transactions with revenue greater than or equal to the threshold. The report uses **KES 50,000.00**. A dictionary comprehension counts qualifying sales for each store, including stores with zero qualifying sales.

## 7. Transaction revenue statistics

The custom `mean`, `median`, and `minimum_maximum` functions operate on per-transaction revenue. Each raises `ValueError` with a clear message for an empty list. `minimum_maximum` finds both endpoints without calling the `min()` or `max()` built-ins.

## 8. Pandas verification

`verify_with_pandas(path, my_results)` reads the CSV with pandas, applies the same `parse_number` cleaning rules, derives transaction revenue, and prints `MATCH` or `MISMATCH` for total revenue, mean, median, minimum, and maximum. The verification cells print one status for each headline metric (total revenue, mean, median, minimum, and maximum). All five match for the supplied CSV.
