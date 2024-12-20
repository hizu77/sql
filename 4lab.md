# 4 lab

1.  Найти название самого продаваемого продукта.

    ``` sql
    select p.Name
    from Production.Product as p
    where p.ProductID = 
    (
      select top 1 sod.ProductID
      from Sales.SalesOrderDetail as sod
      group by sod.ProductID
      order by sum(sod.OrderQty) desc
    )
     ```
2. Найти покупателя, совершившего покупку на самую большую сумм, считая 
    сумму покупки исходя из цены товара без скидки (UnitPrice).

   ``` sql
    	select soh.CustomerId
	from Sales.SalesOrderHeader as soh
	where soh.SalesOrderID = 
	(
		select top 1 sod.SalesOrderId
		from Sales.SalesOrderDetail as sod
		group by sod.SalesOrderID
		order by sum(UnitPrice * OrderQty) desc
	)
   ```

3. Найти такие продукты, которые покупал только один покупатель.

   ``` sql
    select p.Name
    from Production.Product as p
    where p.ProductID in 
    (
    	select sod1.ProductId
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod1
    	on soh.SalesOrderID = sod1.SalesOrderID
    	group by sod1.ProductId
    	having count(distinct soh.CustomerId) = 1
    )
   ```

4.  Вывести список продуктов, цена которых выше средней цены товаров в 
    подкатегории, к которой относится товар.

    ``` sql
    select p.Name
    from Production.Product as p
    where p.ListPrice >
    (
        select avg(p1.ListPrice)
        from Production.Product as p1
        where p.ProductSubcategoryID = p1.ProductSubcategoryID
        group by p1.ProductSubcategoryID
    )
    ```

5. Найти такие товары, которые были куплены более чем одним покупателем, при 
    этом все покупатели этих товаров покупали товары только одного цвета и товары 
    не входят в список покупок покупателей, купивших товары только двух цветов.

    ``` sql
    select distinct sod_.ProductId
    from Sales.SalesOrderDetail as sod_ join Sales.SalesOrderHeader as soh_
    on sod_.SalesOrderID = soh_.SalesOrderID
    where 
    (
    	select count(distinct soh.CustomerID)
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    	where sod_.ProductID = sod.ProductID
    ) > 1
    and
    not exists
    (
    	select soh.CustomerId
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    	join Production.Product as p
    	on p.ProductID = sod.ProductID
    	where sod_.ProductID = sod.ProductID
    	group by soh.CustomerID
    	having count(distinct p.Color) > 1
    )
    and sod_.ProductID not in
    (
        select p1.ProductId
    	from Production.Product as p1 join Sales.SalesOrderDetail as sod1
    	on p1.ProductID = sod1.UnitPrice
    	join Sales.SalesOrderHeader as soh1
    	on soh1.SalesOrderID = sod1.SalesOrderID
    	where soh1.CustomerID in
    	(
    		select soh.CustomerID
    		from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    		on soh.SalesOrderID = sod.SalesOrderID
    		join Production.Product as p
    		on p.ProductID = sod.ProductID
    		group by soh.CustomerID
    		having count(distinct p.Color) = 2
    		)
    )
    ```

6. Найти такие товары, которые были куплены такими покупателями, у которых 
   они присутствовали в каждой их покупке.

   ``` sql
	select distinct sod.ProductId
	from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
	on sod.SalesOrderID = soh.SalesOrderID
	group by soh.CustomerID, sod.ProductID
	having count(*) > 1 and count(*) = 
	(
		select count(soh1.CustomerId)
		from Sales.SalesOrderHeader as soh1
		where soh1.CustomerID = soh.CustomerID
	)
    ```

7. Найти покупателей, у которых есть товар, присутствующий в каждой 
   покупке/чеке.

   ``` sql
    select distinct soh_.CustomerId
    from Sales.SalesOrderDetail as sod_ join Sales.SalesOrderHeader as soh_
    on sod_.SalesOrderID = soh_.SalesOrderID
    group by soh_.CustomerID, sod_.ProductID
    having count(*) > 1 and count(*) >=
    (
    	select count(distinct soh.SalesOrderID)
    	from Sales.SalesOrderHeader as soh
    	where soh.CustomerID = soh_.CustomerID
    	
    )
   ```

8. Найти такой товар или товары, которые были куплены не более чем тремя 
   различными покупателями.

   ``` sql
   	select p_.Name
	from Production.Product as p_
	where 
	(
		select count(distinct soh.CustomerId)
		from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
		on sod.SalesOrderID = soh.SalesOrderID
		where sod.ProductID = p_.ProductID
	) between 1 and 3
   ```

9. Найти все товары, такие что их покупали всегда с товаром, цена которого 
   максимальна в своей категории

   ``` sql
   	select distinct p_.ProductId
	from Production.Product as p_ join Sales.SalesOrderDetail as sod_
	on p_.ProductID = sod_.ProductID
	where sod_.SalesOrderID not in
	(
		select sod.SalesOrderId
		from Sales.SalesOrderDetail as sod
		where 
		(
		select top 1 p.ProductId
		from Production.Product as p join Production.ProductSubcategory as ps
		on p.ProductSubcategoryID = ps.ProductSubcategoryID
		where ps.ProductSubcategoryID = p_.ProductSubcategoryID
		order by p.ListPrice desc
		) not in
		(
		select sod.ProductId
		from Sales.SalesOrderDetail as sod
		where sod.SalesOrderID = sod_.SalesOrderID
		)
	)
    ```

10. Найти номера тех покупателей, у которых есть как минимум два чека, и 
    каждый из этих чеков содержит как минимум три товара, каждый из которых как
    минимум был куплен другими покупателями три раза.

    ``` sql
    	select distinct soh_.CustomerId
	from Sales.SalesOrderHeader as soh_
	where
	(
		select count(distinct soh.SalesOrderId)
		from Sales.SalesOrderHeader as soh
		where soh.CustomerID = soh_.CustomerID
	) >= 2
	and 
	(
	select count(*)
	from sales.SalesOrderHeader as soh1 join Sales.SalesOrderDetail as sod1
	on soh1.SalesOrderID = sod1.SalesOrderID
	where soh1.CustomerID = soh_.CustomerID and
		(
			select count(distinct sod.ProductID)
			from Sales.SalesOrderDetail as sod
			where sod.SalesOrderID = soh1.SalesOrderID and
			(
				select count(distinct sod2.SalesOrderID)
				from Sales.SalesOrderDetail as sod2 join Sales.SalesOrderHeader as soh2
				on sod2.SalesOrderID = soh2.SalesOrderID
				where soh1.CustomerID != soh2.CustomerID and sod2.ProductID = sod.ProductID
			) >= 3
		) >= 3
	) = 
	(
	select count(*)
	from Sales.SalesOrderHeader as soh
	where soh.CustomerID = soh_.CustomerID
	)
    ```

11. Найти все чеки, в которых каждый товар был куплен дважды этим же 
    покупателем.

	``` sql
	select distinct soh_.SalesOrderId
	from Sales.SalesOrderHeader as soh_
	where not exists
	(
		select sod.ProductId
		from Sales.SalesOrderDetail as sod
		where soh_.SalesOrderID = sod.SalesOrderID and
		(
			select count(*)
			from Sales.SalesOrderHeader as soh1 join Sales.SalesOrderDetail as sod1
			on soh1.SalesOrderID = sod1.SalesOrderID
			where soh1.CustomerID = soh_.CustomerID and sod1.ProductID = sod.ProductID
		) != 2
	)
 	```

12. Найти товары, которые были куплены минимум три раза различными 
	покупателями.

	``` sql
	select distinct sod_.ProductId
	from Sales.SalesOrderDetail as sod_
	where 
	(
		select count(distinct soh.CustomerID)
		from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
		on sod.SalesOrderID = soh.SalesOrderID
		where sod.ProductID = sod_.ProductID
	) >= 3
 	```

13. Найти такую подкатегорию или подкатегории товаров, которые содержат 
    более трех товаров, купленных более трех раз.

    ``` sql
    	select distinct p_.ProductSubcategoryId
	from Production.Product as p_
	where 
	(
		select count(*)
		from Production.Product as p
		where p.ProductSubcategoryID = p_.ProductSubcategoryID and
		(
			select count(*)
			from Sales.SalesOrderDetail as sod
			where p.ProductID = sod.ProductID
		) > 3
	) > 3
    ```

15. Найти те товары, которые не были куплены более трех раз, и как минимум 
    дважды одним и тем же покупателем.

    ``` sql
	select distinct sod_.ProductId
	from Sales.SalesOrderDetail as sod_
	where 
	(
		select count(distinct sod.SalesOrderId)
		from Sales.SalesOrderDetail as sod
		where sod.ProductID = sod_.ProductID
	) < 3
	and not exists
	(
		select soh.CustomerID
		from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
		on sod.SalesOrderID = soh.SalesOrderID
		where sod.ProductID = sod_.ProductID
		group by soh.CustomerID, sod.ProductID
		having count(*) >= 2
	)
     ``` 


 	

    
