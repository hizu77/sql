# 5 labwork

1.  Найти среднее количество покупок на чек для каждого покупателя (2 способа).
    1. Способ 1
       ``` sql
        select soh.CustomerId, count(*) / count(distinct sod.SalesOrderId) as 'avg'
        from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
        on sod.SalesOrderID = soh.SalesOrderID
        group by soh.CustomerId
       ```
    2. Способ 2 с ОТВ
       ``` sql
        with tmp (ci, ordercount, totalproductsinorders)
        as
        (
        	select soh.CustomerId, count(distinct sod.SalesOrderId), count(*)
        	from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
        	on sod.SalesOrderID = soh.SalesOrderID
        	group by soh.CustomerId
        )
        
        select tmp.ci, tmp.totalproductsinorders / tmp.ordercount as avgproducts
        from tmp
       ```
2. Найти для каждого продукта и каждого покупателя соотношение количества 
фактов покупки данного товара данным покупателем к общему количеству 
фактов покупки товаров данным покупателем.
    ``` sql
      with CustomerProductsCount (Customer, ProductCount)
      as
      (
      	select soh.CustomerId, count(*)
      	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
      	on soh.SalesOrderID = sod.SalesOrderID
      	group by soh.CustomerId
        having count(*) > 0
      ),
      ProductCustomerBuyCount (ProductId, Customer, BuyCount)
      as
      (
      	select sod.ProductId, soh.CustomerId, count(*)
      	from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
      	on sod.SalesOrderID = soh.SalesOrderID
      	group by sod.ProductID, soh.CustomerId
      )
      
      select pcbc.Customer, pcbc.ProductId, pcbc.BuyCount / cpc.ProductCount as res
      from ProductCustomerBuyCount as pcbc join CustomerProductsCount as cpc
      on pcbc.Customer = cpc.Customer
    ```

3. Вывести на экран следящую информацию: Название продукта, Общее 
количество фактов покупки этого продукта, Общее количество покупателей 
этого продукта
    ``` sql
    with ProductPurchases (ProductId, Purchases)
    as
    (
        select sod.ProductId, count(*)
        from Sales.SalesOrderDetail as sod
        group by sod.ProductID
    ),
    
    ProductCustomersCount (ProductId, CustomersCount)
    as
    (
        select sod.ProductId, count(distinct soh.CustomerId)
        from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
        on sod.SalesOrderID = soh.SalesOrderID
        group by sod.ProductId
    )
    
    select p.ProductId, pp.Purchases, pcc.CustomersCount
    from Production.Product as p left join ProductCustomersCount as pcc 
    on p.ProductID = pcc.ProductId
    left join ProductPurchases as pp
    on pcc.ProductId = pp.ProductId
    ```

4.  Вывести для каждого покупателя информацию о максимальной и минимальной 
стоимости одной покупки, чека, в виде таблицы: номер покупателя, 
максимальная сумма, минимальная сумма.
    ``` sql
    with CustomerPurchases (CustomerId, SalesOrderId, PurchasePrice)
    as
    (
    	select soh.CustomerId, sod.SalesOrderId, sum(sod.UnitPrice * sod.OrderQty)
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    	group by soh.CustomerId, sod.SalesOrderID
    )
    
    select cp.CustomerId, max(cp.PurchasePrice) as MaxPurchase, min(cp.PurchasePrice) as MinPurchase
    from CustomerPurchases as cp
    group by cp.CustomerId
    ```

5. Найти номера покупателей, у которых не было нет ни одной пары чеков с 
одинаковым количеством наименований товаров.
    ``` sql
    with temp (CustomerId, SalesOrderId, Purchases)
    as
    (
    	select soh.CustomerId, sod.SalesOrderId, sod.ProductId
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    )
    
    select s.CustomerId
    from Sales.Customer as s
    where s.CustomerID not in
    (
    	select distinct t1.CustomerId
    	from temp as t1 join temp as t2
    	on t1.CustomerId = t2.CustomerId
    	where t1.SalesOrderId != t2.SalesOrderId and t1.Purchases = t2.Purchases
    )
    ```

6. Найти номера покупателей, у которых все купленные ими товары были 
куплены как минимум дважды, т.е. на два разных чека.
    ``` sql
    with MismatchingProducts (ProductId)
    as
    (
    	select sod.ProductId
    	from Sales.SalesOrderDetail as sod
    	group by sod.ProductId
    	having count(*) < 2
    ),
    CustomerProducts (CustomerId, ProductId)
    as
    (
    	select soh.CustomerId, sod.ProductId
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    )
    
    select distinct cp.CustomerId
    from CustomerProducts as cp
    where not exists
    (
    	select sod.ProductId
    	from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
    	on sod.SalesOrderID = soh.SalesOrderID
    	where soh.CustomerID = cp.CustomerId 
    	and sod.ProductID = any 
    	(
    	(
    		select mp.ProductId
    		from MismatchingProducts as mp
    	)
    	)
    )
    ```

7. TestTask
    ``` sql
    with TotalCategoryCount(CategoryCount) as
    (
    	select count(distinct ProductCategoryId)
    	from Production.ProductCategory
    ),
    
    ProductCategory (ProductId, CategoryId) as
    (
    	select p.ProductId, ps.ProductCategoryId
    	from Production.Product as p join Production.ProductSubcategory as ps
    	on p.ProductSubcategoryID = ps.ProductSubcategoryID
    ),
    
    CustomerCategoryCount (CustomerId, CategoryCount) as 
    (
    	select soh.CustomerId, count(distinct pc.CategoryId)
    	from Sales.SalesOrderHeader as soh join Sales.SalesOrderDetail as sod
    	on soh.SalesOrderID = sod.SalesOrderID
    	join ProductCategory as pc
    	on pc.ProductId = sod.ProductID
    	group by soh.CustomerID
    ),
    
    CustomerPurchases (CustomerId, TotalPurchases, TotalPrice) as 
    (
    	select soh.CustomerId, count(distinct sod.SalesOrderID), sum(sod.UnitPrice * sod.OrderQty)
    	from Sales.SalesOrderDetail as sod join Sales.SalesOrderHeader as soh
    	on soh.SalesOrderId = sod.SalesOrderId
    	group by soh.CustomerID
    )
    
    select ccc.CustomerId, cp.TotalPurchases, cp.TotalPrice / cp.TotalPurchases
    from CustomerCategoryCount as ccc join CustomerPurchases as cp
    on ccc.CustomerId = cp.CustomerId
    where ccc.CategoryCount > (select CategoryCount from TotalCategoryCount) / 2
    ```


