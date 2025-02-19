# Target's Sales Overview 2017-2018 Dashboard
## Business Request and User Stories
The business request for this project is to provide Sales Department with a Sales Overview Dashboard of Target's Business in 2017-2018. Based on the user stories and acceptance criteria below, I will deliver a report that fulfills the business demand.

|#|As a <role>|	I want <request/goal>|	so that I <user value>|	Acceptance Criteria|
|-|----|----|-----|---|
|1|	Sales Representative|	to get insights on customer buying behaviors|	can make appropriate sales targeting.|	A powerBI dashboard which can filter City and Product Category.|  
|2|	Sales Managers|	to get an overview of sales performance|	can track sales performance and set sales KPI.|	A powerBI dashboard which show sales performance and growth and can filter Date.|

## Data Preprocessing using SQL Server
The following tables were extracted using SQL to prepare the necessary data for doing analysis. Below are the SQL statements for cleansing and transforming necessary data.

**Cleansed Order Table:**

```
select 
  a.[order_id] as 'OrderID', 
  upper([order_status]) as 'Order Status', 
  [payment_value] as 'Value', 
  upper(
    replace([payment_type], '_', ' ')
  ) as 'Payment Type',  -- Remove the underscore on the string 
  cast(
    [order_purchase_timestamp] as date
  ) as 'Date', 
  a.[customer_id] as 'CustomerID', 
  [product_id] as 'Product ID', 
  [seller_id] as 'SellerID' 
from 
  [dbo].[orders] a 
  join [dbo].[order_items] b on a.[order_id] = b.[order_id] 
  join [dbo].[customers] c on a.[customer_id] = c.[customer_id] 
  join [dbo].[payments] d on a.[order_id] = d.[order_id] 
where 
  year([order_purchase_timestamp]) > 2016  -- Get the data from 2017
order by 
  'Date' ASC;
```

**Cleansed Customer Table:**

```
select  [customer_id] as 'CustomerID',
        upper(convert(varchar, [customer_city])) COLLATE SQL_Latin1_General_Cp1251_CS_AS as 'City', -- Remove diacritics to ensure string clarity
        case -- Replace the State Abbreviation by State Name
        when [customer_state] = 'AC' then replace([customer_state],'AC','Acre')
        when [customer_state] = 'AL' then replace([customer_state],'AL','Alagoas')
        when [customer_state] = 'AM' then replace([customer_state],'AM','Amazonas')
        when [customer_state] = 'AP' then replace([customer_state],'AP','Amapa')
        when [customer_state] = 'BA' then replace([customer_state],'BA','Bahia')
        when [customer_state] = 'CE' then replace([customer_state],'CE','Ceara')
        when [customer_state] = 'DF' then replace([customer_state],'DF','Distrito Federal')
        when [customer_state] = 'ES' then replace([customer_state],'ES','Espirito Santo')
        when [customer_state] = 'GO' then replace([customer_state],'GO','Goias')
        when [customer_state] = 'MA' then replace([customer_state],'MA','Maranhao')
        when [customer_state] = 'MG' then replace([customer_state],'MG','Minas Gerais')
        when [customer_state] = 'MS' then replace([customer_state],'MS','MatoGrosso do Sul')
        when [customer_state] = 'MT' then replace([customer_state],'MT','MatoGrosso')
        when [customer_state] = 'PA' then replace([customer_state],'PA','Para')
        when [customer_state] = 'PB' then replace([customer_state],'PB','Paraiba')
        when [customer_state] = 'PE' then replace([customer_state],'PE','Pernambuco')
        when [customer_state] = 'PI' then replace([customer_state],'PI','Piaui')
        when [customer_state] = 'PR' then replace([customer_state],'PR','Parana')
        when [customer_state] = 'RJ' then replace([customer_state],'RJ','Rio de Janeiro')
        when [customer_state] = 'RN' then replace([customer_state],'RN','Rio Grande do Norte')
        when [customer_state] = 'RO' then replace([customer_state],'RO','Rondonia')
        when [customer_state] = 'RR' then replace([customer_state],'RR','Roraima')
        when [customer_state] = 'RS' then replace([customer_state],'RS','Rio Grande do Sul')
        when [customer_state] = 'SC' then replace([customer_state],'SC','Santa Catarina')
        when [customer_state] = 'SE' then replace([customer_state],'SE','Sergipe')
        when [customer_state] = 'SP' then replace([customer_state],'SP','Sao Paulo')
        when [customer_state] = 'TO' then replace([customer_state],'TO','Tocantins')
        end as 'State'
from [dbo].[customers];
```

**Cleansed Product Table**

```
select 
  [product_id] as 'ProductID', 
  upper([product_category]) as 'Category' 
from 
  [dbo].[products];
```

**Cleansed Seller Table**

```
select  [seller_id] as 'SellerID',
        upper(convert(varchar, [seller_city])) COLLATE SQL_Latin1_General_Cp1251_CS_AS as 'City', -- Remove diacritics to ensure string clarity
        case -- Replace the State Abbreviation by State Name
        when [seller_state] = 'AC' then replace([seller_state],'AC','Acre')
        when [seller_state] = 'AL' then replace([seller_state],'AL','Alagoas')
        when [seller_state] = 'AM' then replace([seller_state],'AM','Amazonas')
        when [seller_state] = 'AP' then replace([seller_state],'AP','Amapa')
        when [seller_state] = 'BA' then replace([seller_state],'BA','Bahia')
        when [seller_state] = 'CE' then replace([seller_state],'CE','Ceara')
        when [seller_state] = 'DF' then replace([seller_state],'DF','Distrito Federal')
        when [seller_state] = 'ES' then replace([seller_state],'ES','Espirito Santo')
        when [seller_state] = 'GO' then replace([seller_state],'GO','Goias')
        when [seller_state] = 'MA' then replace([seller_state],'MA','Maranhao')
        when [seller_state] = 'MG' then replace([seller_state],'MG','Minas Gerais')
        when [seller_state] = 'MS' then replace([seller_state],'MS','MatoGrosso do Sul')
        when [seller_state] = 'MT' then replace([seller_state],'MT','MatoGrosso')
        when [seller_state] = 'PA' then replace([seller_state],'PA','Para')
        when [seller_state] = 'PB' then replace([seller_state],'PB','Paraiba')
        when [seller_state] = 'PE' then replace([seller_state],'PE','Pernambuco')
        when [seller_state] = 'PI' then replace([seller_state],'PI','Piaui')
        when [seller_state] = 'PR' then replace([seller_state],'PR','Parana')
        when [seller_state] = 'RJ' then replace([seller_state],'RJ','Rio de Janeiro')
        when [seller_state] = 'RN' then replace([seller_state],'RN','Rio Grande do Norte')
        when [seller_state] = 'RO' then replace([seller_state],'RO','Rondonia')
        when [seller_state] = 'RR' then replace([seller_state],'RR','Roraima')
        when [seller_state] = 'RS' then replace([seller_state],'RS','Rio Grande do Sul')
        when [seller_state] = 'SC' then replace([seller_state],'SC','Santa Catarina')
        when [seller_state] = 'SE' then replace([seller_state],'SE','Sergipe')
        when [seller_state] = 'SP' then replace([seller_state],'SP','Sao Paulo')
        when [seller_state] = 'TO' then replace([seller_state],'TO','Tocantins')
        end as 'State'
from [dbo].[sellers];
select  [seller_id] as 'SellerID',
```

## Data Visualization using PowerBI

### Data Model
The extracted data was then imported into PowerBI. Below is a screenshot of the data model after cleansed tables were loaded into Power BI.

![image](https://github.com/user-attachments/assets/62a82195-5436-46a8-ac7d-a9f3569fa2dc)

### Dashboard

![image](https://github.com/user-attachments/assets/83bcdb95-5356-4dcd-b8ce-8f5bc23f28e1)


