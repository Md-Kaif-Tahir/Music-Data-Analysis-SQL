![blocks-T3mKJXfdims-unsplash](https://github.com/Md-Kaif-Tahir/Music-Data-Analysis-SQL/assets/110182266/7cbf5387-7da5-42f6-bb9f-d74bfd3f6ba3)

# Music-Store-SQL-Project
**Project Overview**

Welcome to the realm of data magic! 🎩✨ This repository is your gateway to a treasure trove of **SQL queries** and solutions that unravel the mysteries of data analysis. From the hierarchy of our esteemed employees to the beats that resonate across borders, dive into the world of SQL and let the data unfold its stories.

**About The Dataset**

Behold the blueprint of our data symphony! 📊🎶 The schema paints a vivid picture of connections, with each table holding a key to the harmonious dance of information. Explore the beats and bytes of the [**Music Store Dataset**](https://www.kaggle.com/datasets/mohammadkaiftahir/music-dataset) that fuels our SQL adventure.

# Database Schema
![image](https://github.com/Md-Kaif-Tahir/Music-Data-Analysis-SQL/assets/110182266/ac279b10-b336-41c6-95f4-4445c877435c)


**Queries**

*Question Set 1:*

- *Q1: The Job Title Summit! 👑*
  - Find the maestro of job titles, the one who stands tall as the senior most employee.

- *Q2: Invoicing Extravaganza! 🌐💸*
  - Uncover the champions of invoicing, as countries compete for the crown of the most invoices.

- *Q3: The Invoice Peaks! 🏔️💼*
  - Explore the zenith of invoice values with the top 3 contenders stealing the spotlight.

- *Q4: City Showdown! 🌆🎉*
  - Set the stage for a music festival! Discover the city that promises the highest sum of invoice totals.

- *Q5: The Spendthrift Royalty! 👑💰*
  - Crown the best customer, the one who reigns supreme with the most lavish spending.

*Question Set 2:*

- *Q1: Rocking the Inbox! 🎸📬*
  - Compile the list of Rock Music aficionados and get ready to invite them to the ultimate rock party!

- *Q2: Rockstar Leaderboard! 🤘🏆*
  - Unveil the leaders of rock music creation, inviting the top 10 rock bands to claim their throne.

- *Q3: Songs that Break the Clock! ⏰🎶*
  - Embark on a musical journey beyond average song lengths. The stage is set for tracks that defy the norm!

*Question Set 3:*

- *Q1: Spending Extravaganza! 💳🎨*
  - Witness the art of spending as each customer splurges on their favorite artists. It's a spending extravaganza!

- *Q2: Genre Champions Around the Globe! 🌍🏆*
  - Enter the arena of global music genres, crowning the champions in each country. The battle is on!

- *Q3: Country's Big Spenders! 💸🌎*
  - Meet the big spenders from every corner of the globe! Discover who's making it rain in each country. The VIP list of global music spenders awaits!

Feel the pulse of the queries, dance to the rhythm of the solutions, and let the emojis narrate the tale of SQL exploration! 🚀📊

**Question Set 1**

- **Q1: Who's the top dog at the office?**
```sql
SELECT * FROM employee
ORDER BY levels DESC
LIMIT 1;
```
Ever wondered who sits at the peak of the job hierarchy in our music store? 🏢 Behold, the senior-most employee, the wizard of all wizards in the job titles!

- **Q2: Countries in the Invoicing Olympics! 🌍🏆**
```sql
SELECT COUNT(*) AS c, billing_country 
FROM invoice
GROUP BY billing_country
ORDER BY c DESC;
```
Get ready for a global showdown! 🌎🚀 Find out which countries are taking the lead in the Invoicing Olympics! Who's bagging the most invoices? 🏆

- **Q3: Invoices, show me the money! 💸💰**
```sql
SELECT total FROM invoice
ORDER BY total DESC
LIMIT 3;
```
Curious about the top cash cows in our music empire? 💰 Here are the three heavyweight champions of invoice totals! Count them, adore them, and dream about them! 🌟

- **Q4: Battle of the Cities! 🌆**
```sql
SELECT SUM(total) AS invoice_total, billing_city 
FROM invoice
GROUP BY billing_city
ORDER BY invoice_total;
```
Cities, it's time for a showdown! 🏙️ Which city is the ultimate moneymaker? We need to know because we're planning a Music Festival, and we want it to rain dollars! 💵☔

- **Q5: The Customer Royalty! 👑💳**
```sql
SELECT customer.customer_id, customer.first_name, customer.last_name, SUM(invoice.total) AS total
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id
ORDER BY total DESC
LIMIT 1;
```
Bow down to the King or Queen of customers! 👑🎸 Who's spent the most and deserves the royal treatment? It's coronation time in our data kingdom!

**Question Set 2**

- **Q1: Rocking the Inbox! 🎸📬**
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
Let's find the Rock Music enthusiasts in our customer list! 🤘📬 Get ready to invite them to the ultimate rock party!

- **Q2: Rockstar Leaderboard! 🤘🏆**
```sql
SELECT artist.artist_id, artist.name, COUNT(artist.artist_id) AS number_of_songs
FROM track
JOIN album ON album.album_id = track.album_id
JOIN artist ON artist.artist_id = album.artist_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
GROUP BY artist.artist_id
ORDER BY number_of_songs DESC
LIMIT 10;
```
Calling all rockstars! 🎸 Who's leading the pack in creating epic rock music? The leaderboard is out, and it's time to shine! 🌟🎤

- **Q3: Songs that Break the Clock! ⏰🎶**
```sql
SELECT name, Milliseconds
FROM track
WHERE Milliseconds > (
    SELECT AVG(Milliseconds) AS avg_track_length
    FROM track
)
ORDER BY Milliseconds DESC;
```
Ready for a musical journey beyond the average? 🚀🎵 Here are the tracks that break the clock with song lengths that defy the norm!

**Question Set 3**

- **Q1: Customer Spending Extravaganza! 💳🎨**
```sql
WITH best_selling_artist AS (
    SELECT artist.artist_id AS artist_id, artist.name AS artist_name, SUM(invoice_line.unit_price * invoice_line.quantity) AS total_sales
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
JOIN track t ON t.track_id = il.track_id
JOIN album alb ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY 1, 2, 3, 4
ORDER BY 5 DESC;
```
Welcome to the spending extravaganza! 💳✨ Discover how much each customer splurged on their favorite artists. The art of spending meets the science of SQL!

- **Q2: Genre Champions Around the Globe! 🌍🏆**
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
It's the ultimate battle of music genres! 🎶🏆 Find out which genre rules the airwaves in each country. The global champions are crowned!

- **Q3: Country's Big Spenders! 💸🌎**
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
Get ready to meet the big spenders from every corner of the globe! 💸🌍 Find out who's making it rain in each country. It's the VIP list of global music spenders! 🌟
