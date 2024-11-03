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
