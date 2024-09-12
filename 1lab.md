# 1lab

1.  **Найти и вывести на экран названия продуктов, их цвет и размер.**

```sql
SELECT p.Name, p.Color, p.Size
FROM Production.Product AS p
```

1. **Найти и вывести на экран названия, цвет и размер таких продуктов, у которых цена более 100.**

```sql
SELECT p.Name, p.Color, p.Size
FROM Production.Product AS p
WHERE p.ListPrice > 100
```

1. **Найти и вывести на экран название, цвет и размер таких продуктов, у которых цена менее 100 и цвет Black.**
    
    ```sql
    SELECT p.Name, p.Color, p.Size
    FROM Production.Product AS p
    WHERE p.ListPrice < 100 AND p.Color = 'Black'
    ```
    
2. **Найти и вывести на экран название, цвет и размер таких продуктов, у которых цена менее 100 и цвет Black, упорядочив вывод по возрастанию стоимости продуктов.**
    
    ```sql
    SELECT p.Name, p.Color, p.Size
    FROM Production.Product AS p
    WHERE p.ListPrice < 100 AND p.Color = 'Black'
    ORDER BY p.ListPrice ASC
    ```
    
3. **Найти и вывести на экран название и размер первых трех самых дорогих
товаров с цветом Black.**
    
    ```sql
    SELECT TOP 3 p.Name, p.Size
    FROM Production.Product AS p
    WHERE p.Color = 'Black'
    ORDER BY p.ListPrice DESC
    ```
    
4. **Найти и вывести на экран название и цвет таких продуктов, для которых определен и цвет, и размер.**
    
    ```sql
    SELECT p.Name, p.Color
    FROM Production.Product AS p
    WHERE p.Color is NOT null 
    		AND
    			p.Size is NOT null
    ```
    
5. **Найти и вывести на экран не повторяющиеся цвета продуктов, у которых цена находится в диапазоне от 10 до 50 включительно.**
    
    ```sql
    SELECT DISTINCT p.Color
    FROM Production.Product AS p
    WHERE p.ListPrice BETWEEN 10 AND 50
    ```
    
6. **Найти и вывести на экран все цвета таких продуктов, у которых в имени первая буква ‘L’ и третья ‘N’.**
    
    ```sql
    SELECT p.Color
    FROM Production.Product AS p
    WHERE p.Name like 'L_N%'
    ```
    
7. **Найти и вывести на экран названия таких продуктов, которых начинаются либо на букву ‘D’, либо на букву ‘M’, и при этом длина имени – более трех символов.**
    
    ```sql
    SELECT p.Name
    FROM Production.Product AS p
    WHERE p.Name like '[DM]%'
    		AND
    			len(p.Name) > 3
    ```
    
8.  **Вывести на экран названия продуктов, у которых дата начала продаж – не позднее 2012 года.**
    
    ```sql
    SELECT p.Name
    FROM Production.Product AS p
    WHERE datepart(YEAR, p.SellStartDate) <= 2012
    ```
    
9.  **Найти и вывести на экран названия всех подкатегорий товаров.**
    
    ```sql
    SELECT ps.Name
    FROM Production.ProductSubcategory AS ps
    
    ```
    
10.  **Найти и вывести на экран названия всех категорий товаров.**
    
    ```sql
    SELECT pc.Name
    FROM Production.ProductCategory AS pc
    
    ```
    
11.  **Найти и вывести на экран имена всех клиентов из таблицы Person, у которых обращение (Title) указано как «Mr.».**
    
    ```sql
    SELECT p.FirstName, p.MiddleName, p.LastName
    FROM Person.Person AS p
    WHERE p.Title = 'Mr.'
    ```
    
12.  **Найти и вывести на экран имена всех клиентов из таблицы Person, для
которых не определено обращение (Title).**
    
    ```sql
    SELECT p.FirstName, p.MiddleName, p.LastName
    FROM Person.Person AS p
    WHERE p.Title is null
    ```