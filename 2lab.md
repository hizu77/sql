# 2 lab
1. Найти и вывести на экран количество товаров каждого цвета, исключив из 
поиска товары, цена которых меньше 30.

    ``` sql
    select color, count(*) as 'amount'
    from production.product
    where color is not null and listprice >= 30
    group by color
    ```

2. Найти и вывести на экран список, состоящий из цветов товаров, таких, что 
минимальная цена товара данного цвета более 100.
    ``` sql
    select color
    from production.product
    where color is not null
    group by color
    having min(listprice) > 100
    ```

3. Найти и вывести на экран номера подкатегорий товаров и количество товаров 
   в каждой категории.

   ``` sql
   select productsubcategoryid, count(*) as 'amount'
   from production.product
   group by productsubcategoryid
   ```

4. Найти и вывести на экран номера товаров и количество фактов продаж данного 
   товара (используется таблица SalesORDERDetail).

   ``` sql
   select productid, count(*) as 'count'
   from sales.salesorderdetail
   group by productid
   ```

5. Найти и вывести на экран номера товаров, которые были куплены более пяти 
   раз.

   ``` sql
   select productid
   from sales.salesorderdetail
   group by productid
   having count(*) > 5
   ```

6. Найти и вывести на экран номера покупателей, CustomerID, у которых 
   существует более одного чека, SalesORDERID, с одинаковой датой.

    ``` sql
    select customerid
    from sales.salesorderheader
    group by orderdate, customerid
    having count(*) > 1
    ```

7. Найти и вывести на экран все номера чеков, на которые приходится более трех 
   продуктов.

   ``` sql
   select salesorderid
   from sales.salesorderdetail
   group by salesorderid
   having count(*) > 3
   ```

8. Найти и вывести на экран все номера продуктов, которые были куплены более 
   трех раз.

   ``` sql
   select productid
   from sales.salesorderdetail
   group by productid
   having count(*)	> 3
   ```

9. Найти и вывести на экран все номера продуктов, которые были куплены или 
   три или пять раз.

   ``` sql
   select productid
   from sales.salesorderdetail
   group by productid
   having count(*) = 3 or count(*) = 5
   ```

10. Найти и вывести на экран все номера подкатегорий, в которым относится 
    более десяти товаров.

    ``` sql
    select productsubcategoryid
    from production.product
    where productsubcategoryid is not null
    group by productsubcategoryid
    having count (*) > 10
    ```

11. Найти и вывести на экран номера товаров, которые всегда покупались в 
    одном экземпляре за одну покупку.

    ``` sql
    select productid
    from sales.salesorderdetail
    group by productid
    having max(orderqty) = 1
    ```

12. Найти и вывести на экран номер чека, SalesORDERID, на который приходится 
    с наибольшим разнообразием товаров купленных на этот чек.

    ``` sql
    select top 1 with ties salesorderid
    from sales.salesorderdetail
    group by salesorderid
    order by count(distinct productid) desc
    ```




