![blocks-T3mKJXfdims-unsplash](https://github.com/Md-Kaif-Tahir/Music-Data-Analysis-SQL/assets/110182266/7cbf5387-7da5-42f6-bb9f-d74bfd3f6ba3)

# Music-Store-SQL-Project
**Project Overview**

Welcome to the realm of data magic! 🎩✨ This repository is your gateway to a treasure trove of **SQL queries** and solutions that unravel the mysteries of data analysis. From the hierarchy of our esteemed employees to the beats that resonate across borders, dive into the world of SQL and let the data unfold its stories.

**About The Dataset**

Behold the blueprint of our data symphony! 📊🎶 The schema paints a vivid picture of connections, with each table holding a key to the harmonious dance of information. Explore the beats and bytes of the [**Music Store Dataset**](https://www.kaggle.com/datasets/mohammadkaiftahir/music-dataset) that fuels our SQL adventure.

# Database Schema
![image](https://github.com/Md-Kaif-Tahir/Music-Data-Analysis-SQL/assets/110182266/ac279b10-b336-41c6-95f4-4445c877435c)


**Queries**

**Question Set 1: Employee Seniority, Top Countries, Invoice Values, Best City, Best Customer**

1. **Who is the most experienced employee based on job title?**
   - Find the employee with the highest job title level.

   ```sql
   SELECT * FROM employee
   ORDER BY levels DESC
   LIMIT 1;
   ```

2. **Which countries have the most invoices?**
   - List countries with the highest number of invoices.

   ```sql
   SELECT COUNT(*) AS invoice_count, billing_country 
   FROM invoice
   GROUP BY billing_country
   ORDER BY invoice_count DESC;
   ```

3. **What are the top 3 values of total invoices?**
   - Get the three highest invoice amounts.

   ```sql
   SELECT total FROM invoice
   ORDER BY total DESC
   LIMIT 3;
   ```

4. **Which city has the best customers for a Music Festival?**
   - Find the city with the highest total invoice amount.

   ```sql
   SELECT SUM(total) AS total_invoices, billing_city 
   FROM invoice
   GROUP BY billing_city
   ORDER BY total_invoices DESC
   LIMIT 1;
   ```

5. **Who is the top-spending customer?**
   - Identify the customer who spent the most money.

   ```sql
   SELECT customer.customer_id, customer.first_name, customer.last_name, SUM(invoice.total) AS total_spent
   FROM customer
   JOIN invoice ON customer.customer_id = invoice.customer_id
   GROUP BY customer.customer_id
   ORDER BY total_spent DESC
   LIMIT 1;
   ```

**Question Set 2: Rock Music Listeners, Top Rock Bands, Above-Average Song Lengths**

1. **List Rock music listeners with their details.**
   - Retrieve email, first name, last name of Rock music listeners.

   ```sql
   SELECT DISTINCT email, first_name, last_name
   FROM customer
   JOIN invoice ON customer.customer_id = invoice.customer_id
   JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
   WHERE track_id IN (
       SELECT track_id FROM track
       JOIN genre ON track.genre_id = genre.genre_id
       WHERE genre.name LIKE 'Rock'
   )
   ORDER BY email;
   ```

2. **Invite top Rock bands based on song count.**
   - Get the top 10 Rock bands with the most songs.

   ```sql
   SELECT artist.artist_id, artist.name, COUNT(artist.artist_id) AS song_count
   FROM track
   JOIN album ON album.album_id = track.album_id
   JOIN artist ON artist.artist_id = album.artist_id
   JOIN genre ON genre.genre_id = track.genre_id
   WHERE genre.name LIKE 'Rock'
   GROUP BY artist.artist_id
   ORDER BY song_count DESC
   LIMIT 10;
   ```

3. **List tracks with above-average song lengths.**
   - Retrieve tracks with song lengths above the average.

   ```sql
   SELECT name, Milliseconds
   FROM track
   WHERE Milliseconds > (
       SELECT AVG(Milliseconds) AS avg_track_length
       FROM track
   )
   ORDER BY Milliseconds DESC;
   ```

**Question Set 3: Customer Spending, Popular Music Genre, Top Spending Customers**

1. **Find total spending by each customer on artists.**
   - Return customer name, artist name, and total spending.

   ```sql
   WITH best_selling_artist AS (
       SELECT artist.artist_id, artist.name, SUM(invoice_line.unit_price * invoice_line.quantity) AS total_sales
       FROM invoice_line
       JOIN track ON track.track_id = invoice_line.track_id
       JOIN album ON album.album_id = track.album_id
       JOIN artist ON artist.artist_id = album.artist_id
       GROUP BY 1
       ORDER BY 3 DESC
       LIMIT 1
   )
   SELECT c.customer_id, c.first_name, c.last_name, bsa.artist_name, SUM(il.unit_price * il.quantity) AS amount_spent
   FROM invoice i
   JOIN customer c ON c.customer_id = i.customer_id
   JOIN invoice_line il ON il.invoice_id = i.invoice_id
   JOIN track t ON t

  - **Q2: Determine the most popular music Genre for each country.**
```sql
WITH popular_genre AS (
    SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name, genre.genre_id, 
    ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS RowNo 
    FROM invoice_line 
    JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
    JOIN customer ON customer.customer_id = invoice.customer_id
    JOIN track ON track.track_id = invoice_line.track_id
    JOIN genre ON genre.genre_id = track.genre_id
    GROUP BY 2, 3, 4
    ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM popular_genre WHERE RowNo <= 1;
```

- **Q3: Find the customer that has spent the most on music for each country.**
```sql
WITH Customer_with_country AS (
    SELECT customer.customer_id, first_name, last_name, billing_country, SUM(total) AS total_spending,
    ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(total) DESC) AS RowNo 
    FROM invoice
    JOIN customer ON customer.customer_id = invoice.customer_id
    GROUP BY 1, 2, 3, 4
    ORDER BY 4 ASC, 5 DESC
)
SELECT * FROM Customer_with_country WHERE RowNo <= 1;
```

