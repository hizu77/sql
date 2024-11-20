# Labwork 6

1.  Найти долю продаж каждого продукта (цена продукта * количество продукта), 
    на каждый чек, в денежном выражении.

    ``` sql
    select 
    	ProductId,
    	SalesOrderId,
    	OrderQty * UnitPrice / 
    	sum(OrderQty * UnitPrice) over (partition by SalesOrderId)
    from Sales.SalesOrderDetail
    ```

2. Вывести на экран список продуктов, их стоимость, а также разницу между 
   стоимостью этого продукта и стоимостью самого дешевого продукта в той же 
   подкатегории, к которой относится продукт.

    ``` sql
    select 
    		ProductId,
    		ListPrice,
    		ListPrice - FIRST_VALUE(ListPrice) 
    		over (partition by ProductSubcategoryId order by ListPrice) as diff
    from Production.Product
    ```

3. Вывести три колонки: номер покупателя, номер чека покупателя 
   (отсортированный по возрастанию даты чека) и искусственно введенный 
   порядковый номер текущего чека, начиная с 1, для каждого покупателя.

    ``` sql
    select 
    	soh.CustomerID,
    	soh.SalesOrderNumber,
    	row_number() over (partition by soh.CustomerId order by soh.OrderDate) as num
    from Sales.SalesOrderHeader as soh

    ```

4.  Вывести номера продуктов, таких что их цена выше средней цены продукта в 
    подкатегории, к которой относится продукт. Запрос реализовать двумя 
    способами. В одном из решений допускается использование обобщенного 
    табличного выражения.

    ``` sql
    select p.ProductId
    from Production.Product as p
    where p.ListPrice > 
    (
        select distinct avg(p1.ListPrice) over (partition by p1.ProductSubcategoryId)
        from Production.Product as p1
        where p1.ProductSubcategoryID = p.ProductSubcategoryID
    )
    ```

5.  Вывести на экран номер продукта, название продукта, а также информацию о 
    среднем количестве этого продукта, приходящихся на три последних по дате 
    чека, в которых был этот продукт.


    ``` sql
      select 
    	p.ProductId,
    	p.Name,
    	avg(sod.OrderQty) over 
    	(partition by p.ProductId
    	order by soh.OrderDate 
    	rows between 2 preceding and current row)
    
    from Production.Product as p join Sales.SalesOrderDetail as sod
    on p.ProductID = sod.ProductID
    join Sales.SalesOrderHeader as soh
    on sod.SalesOrderID = soh.SalesOrderID
    ```
