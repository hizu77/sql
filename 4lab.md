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
    select soh1.CustomerID
    from Sales.SalesOrderHeader as soh1
    where soh1.SalesOrderID = 
    (
      select top 1 soh2.SalesOrderID
      from Sales.SalesOrderHeader as soh2 join Sales.SalesOrderDetail as sod
      on soh2.SalesOrderID = sod.SalesOrderID
      group by soh2.SalesOrderID
      order by max(sod.OrderQty * sod.UnitPrice) desc
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
