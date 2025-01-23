### Get all the data from the sales table

       SELECT *
       FROM warehouse_and_retail_sales;

### Change column names and set the correct data types

       ALTER TABLE warehouse_and_retail_sales
       CHANGE `ITEM DESCRIPTION` item_description VARCHAR(255),
       CHANGE `ITEM CODE` item_code INT,
       CHANGE `ITEM TYPE` item_type VARCHAR(255),
       CHANGE `RETAIL SALES` sales DECIMAL(10, 2),
       CHANGE `RETAIL TRANSFER` transfers INT,
       CHANGE `WAREHOUSE SALES` warehouse_sales INT;

### Show sales grouped by year, month, supplier, item type

       SELECT YEAR, MONTH, SUPPLIER, item_type, sales
       FROM warehouse_and_retail_sales
       GROUP BY YEAR, MONTH, SUPPLIER, item_type, sales
       ORDER BY YEAR, sales DESC;

### Count how many sales there are for each supplier and item type
       SELECT YEAR, SUPPLIER, item_type, COUNT(sales) AS count_sales
       FROM warehouse_and_retail_sales
       GROUP BY YEAR, SUPPLIER, item_type
       ORDER BY YEAR DESC, count_sales DESC
       LIMIT 5;

### Show the total sales for each supplier and item type
       SELECT YEAR, SUPPLIER, item_type, SUM(sales) AS total_sales
       FROM warehouse_and_retail_sales
       GROUP BY YEAR, SUPPLIER, item_type
       ORDER BY YEAR DESC, total_sales DESC
       LIMIT 5;

### Show total sales by year and supplier
       SELECT Year, Supplier, SUM(Sales) AS total_sales
       FROM warehouse_and_retail_sales
       GROUP BY Year, Supplier
       ORDER BY Year, total_sales DESC;

### Show total sales for each item type in 2020
       SELECT Item_Type, SUM(Sales) AS total_sales
       FROM warehouse_and_retail_sales
       WHERE Year = 2020
       GROUP BY Item_Type
       ORDER BY total_sales DESC;

### Show the average sales for each item type
       SELECT Item_Type, ROUND(AVG(Sales),2) AS avg_sales
       FROM warehouse_and_retail_sales
       GROUP BY Item_Type
       ORDER BY avg_sales DESC;

### Count how many transactions there are per year
       SELECT Year, COUNT(*) AS transaction_count
       FROM warehouse_and_retail_sales
       GROUP BY Year
       ORDER BY Year;

### Find the highest sales for each month and year
       SELECT Year, Month, MAX(Sales) AS max_sales
       FROM warehouse_and_retail_sales
       GROUP BY Year, Month
       ORDER BY Year, max_sales DESC;

### Show the percentage of sales for each item type per year
       SELECT Year, 
       Item_Type, 
       SUM(Sales) AS total_sales, 
       ROUND((SUM(Sales) / (SELECT SUM(Sales) 
       FROM warehouse_and_retail_sales 
       WHERE Year = w.Year) * 100), 2) AS percentag


### Calculate averages and categorize data based on those averages
    SELECT 
    AVG(ReadingTime) AS avg_reading,
    AVG(WorkoutTime) AS avg_workout,
    AVG(PhoneTime) AS avg_phone
    FROM sleeptime_prediction_dataset;

### Categorize the data based on the averages and count how many records fall into each category
       SELECT 
    CASE 
        WHEN ReadingTime < (SELECT AVG(ReadingTime) FROM sleeptime_prediction_dataset) THEN 'Below Average Reading'
        WHEN ReadingTime = (SELECT AVG(ReadingTime) FROM sleeptime_prediction_dataset) THEN 'Average Reading'
        ELSE 'Above Average Reading'
    END AS reading_category,

### Categorizing WorkoutTime,PhoneTime based on the calculated average
    CASE 
        WHEN WorkoutTime < (SELECT AVG(WorkoutTime) FROM sleeptime_prediction_dataset) THEN 'Below Average Workout'
        WHEN WorkoutTime = (SELECT AVG(WorkoutTime) FROM sleeptime_prediction_dataset) THEN 'Average Workout'
        ELSE 'Above Average Workout'
    END AS workout_category,

    CASE 
        WHEN PhoneTime < (SELECT AVG(PhoneTime) FROM sleeptime_prediction_dataset) THEN 'Below Average Phone Use'
        WHEN PhoneTime = (SELECT AVG(PhoneTime) FROM sleeptime_prediction_dataset) THEN 'Average Phone Use'
        ELSE 'Above Average Phone Use'
    END AS phone_category,

### Counting how many records fall into each category and grouping by the categorized values to count the occurrences
    COUNT(*) AS category_count
    FROM sleeptime_prediction_dataset
    GROUP BY 
    reading_category, 
    workout_category, 
    phone_category
    ORDER BY 
    reading_category, 
    workout_category, 
    phone_category;
    
    SELECT 
    PhoneTime,
    NTILE(4) OVER (ORDER BY PhoneTime ASC) AS phone_decile,
    CASE 
        WHEN NTILE(4) OVER (ORDER BY PhoneTime ASC) = 1 THEN 'Lowest 10%'
        WHEN NTILE(4) OVER (ORDER BY PhoneTime ASC) = 10 THEN 'Highest 10%'
        ELSE CONCAT('Decile ', NTILE(4) OVER (ORDER BY PhoneTime ASC))
    END AS phone_category
    FROM sleeptime_prediction_dataset;

### groups data by rounded phone time, calculates the count per group and categorizes the phone time into quartiles, with custom labels for the lowest and highest quartiles.
    SELECT 
    FLOOR(PhoneTime) AS rounded_phone_time, -- Rounding PhoneTime down to the nearest integer
    COUNT(*) AS count_per_group, -- Counting occurrences per group
    NTILE(4) OVER (ORDER BY FLOOR(PhoneTime) ASC) AS phone_decile, -- Categorizing rounded values into quartiles
    CASE 
        WHEN NTILE(4) OVER (ORDER BY FLOOR(PhoneTime) ASC) = 1 THEN 'Lowest Phone Time'
        WHEN NTILE(4) OVER (ORDER BY FLOOR(PhoneTime) ASC) = 4 THEN 'Highest Phone Time'
        ELSE CONCAT('Phone Time ', NTILE(4) OVER (ORDER BY FLOOR(PhoneTime) ASC))
    END AS phone_category
    FROM sleeptime_prediction_dataset
    GROUP BY FLOOR(PhoneTime)
    ORDER BY rounded_phone_time;

 
