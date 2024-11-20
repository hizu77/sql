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
