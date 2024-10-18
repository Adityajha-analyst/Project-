# Project-
This project uses SQL queries to analyze business data, focusing on employee hierarchy, invoice distribution by country, top revenue transactions, and customer preferences by genre. It ranks customers by spending and highlights insights using grouping, filtering, and ranking techniques.
 --Question Number 1 : Who is the senior most employee based on the job title?

  SELECT * FROM DBO.employee;
  select * from employee where levels = (select max(levels) from employee);

 --Question Number 2 : Which countries have the most invoices?

  Select * FROM DBO.invoice;
  select count(invoice_id) as Orders , billing_country from invoice
  group by billing_country 
  order by Orders desc ;


 --Question Number 3 : What are top 3 values of total income?

  Select * From invoice;
  select top 3 * from invoice 
  order by total DESC;

 --Question Number 4 : Tell one city that has the highest sum of invoice totals retrun both the city name & sum of all invoice totals.

  Select * From invoice;
  select top 3 sum(total) as Revenue, billing_city from invoice
  group by billing_city
  order by revenue desc ;

 --Question Number 5 : Write a query that returns the person who has spent the most money;

  select top 1 customer.customer_id , customer.first_name ,customer.last_name , sum(invoice.total ) as Revenue
  from customer join invoice ON INVOICE.customer_id = customer.customer_id
  group by customer.customer_id , customer.first_name , customer.last_name  order by Revenue desc ;


 --Question Number 6 : Write query to return the email, first name , last name , genere of all rock music listeners . Return your list ordered alphabetically by email starting with A.

  select distinct customer.first_name, customer.last_name, customer.email from customer
  join invoice on customer.customer_id = invoice.customer_id 
  join invoice_line on invoice.invoice_id = Invoice_line.invoice_id 
  join track on invoice_line.track_id = track.track_id 
  join genre on track.genre_id = genre.genre_id 
  where track.genre_id = 1 
  order by customer.email;

 --Question Number 7 : Write the Artist name who have written the most rock music in our datsaet and also gives total track count of the top 10 rock bands .

  select top 10 Artist.name, ARTIST.artist_id , count(track.name) as total_track from ARTIST
  join album on artist.artist_id = album.artist_id 
  join track on album.album_id = track.album_id
  join genre on track.genre_id = genre.genre_id 
  where genre.genre_id = 1
  group by genre.genre_id , Artist.name, ARTIST.artist_id
  order by count(track.name) desc ;

 --Question Number 8 :Return all the track names that have a song length longer than the average song length . Order by the song length.
  
  select * from track;
  select name , milliseconds from track
  where milliseconds > (select avg(milliseconds) from track)
  order by milliseconds desc;

 --Question Number 9 : Find how much amount spent by each customer on artist ? Write a query to return customer name , aritst name and total spent.

   select customer.customer_id, customer.first_name AS Customer_first_name, customer.last_name AS Customer_last_name, 
   SUM(invoice_line.unit_price * invoice_line.quantity) AS total_revenue, artist.name AS Artist_name, artist.artist_id AS Artist_unique_code 
    FROM customer 
    JOIN invoice ON customer.customer_id = invoice.customer_id 
    JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id 
    JOIN track ON invoice_line.track_id = track.track_id 
    JOIN album ON track.album_id = album.album_id 
    JOIN artist ON album.artist_id = artist.artist_id
    GROUP BY customer.customer_id, artist.name, artist.artist_id, customer.first_name, customer.last_name
	order by SUM(invoice_line.unit_price * invoice_line.quantity)  desc;


 --Question Number 10 : Write the query that returns each counrty along with their most purchase genres.
 
   with Short AS (SELECT 
    customer.country, 
	genre.genre_id,
    genre.name, 
    count( invoice_line.quantity) AS total_purchase, 
	ROW_NUMBER() over(partition by customer.country order by  count( invoice_line.quantity) desc ) AS RANKS
FROM invoice
join customer on invoice.customer_id = customer.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id 
JOIN genre ON track.genre_id = genre.genre_id
GROUP BY   customer.country, 
	genre.genre_id,
    genre.name,
	customer.country
	) 
   SELECT * FROM SHORT where RANKS = 1 order by total_purchase desc;

 --Question Number 11 : Find the highest amount of spending that customer made for country basis.

   WITH CUSTOMER_DATA AS (select customer.customer_id , customer.first_name, customer.last_name , invoice.billing_country , sum(invoice.total) AS TOTAL_SPENT,
   row_NUMBER() over(partition by invoice.billing_country order by sum(invoice.total) desc ) as RANKS
   from customer 
   join invoice on customer.customer_id = invoice.customer_id 
   group by 
   customer.customer_id , customer.first_name, customer.last_name , invoice.billing_country
   )

  SELECT * fROM CUSTOMER_DATA WHERE ranks = 1
  ORDER BY TOTAL_SPENT DESC;
