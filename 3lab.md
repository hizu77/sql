3 lab

1. Найти и вывести на экран название продуктов и название категорий товаров, к 
    которым относится этот продукт, с учетом того, что в выборку попадут только 
    товары с цветом Red и ценой не менее 100.

    ``` sql
    select p.Name, pc.Name
    from production.Product as p join Production.ProductSubcategory as ps
    on p.ProductSubcategoryID = ps.ProductSubcategoryID 
    join Production.ProductCategory as pc
    on pc.ProductCategoryID = ps.ProductCategoryID
    where p.Color = 'Red' and p.ListPrice >= 100
    ```

2. Вывести на экран названия подкатегорий с совпадающими именами.

   ``` sql
   select ps1.Name
   from Production.ProductSubcategory as ps1, Production.ProductSubcategory as ps2
   where ps1.ProductSubcategoryID != ps2.ProductSubcategoryID and ps1.Name = ps2.Name
   ```

3. Вывести на экран название категорий и количество товаров в данной 
    категории.

   ``` sql
   select pc.Name, count(*)
   from Production.Product as p join Production.ProductSubcategory as ps
   on p.ProductSubcategoryID = ps.ProductSubcategoryID
   join Production.ProductCategory as pc
   on ps.ProductCategoryID = pc.ProductCategoryID
   group by pc.Name
   ```
