# Business-Questions-Qql-Olist



&nbsp;       -- Operadores de comparação (Exercicio sobre a base de dados Olist (Kaggle) )

&nbsp;       

&nbsp;       

&nbsp;       

&nbsp;       

&nbsp;       

&nbsp;       -- As perguntas do CEO 

&nbsp;       





select

&nbsp;   count(distinct(customer\_id)) as numero\_clientes

from

&nbsp;   customer

where

&nbsp;   customer\_state = 'SP';





-- Qual o número total de pedidos únicos feitos no dia 08 de Outubro de 2016?



select count(distinct(order\_id))

from orders

where DATE (order\_purchase\_timestamp) = '2016-10-08'; --aplicando função date pra converter o valor e podere comparar.





-- Qual o número total de pedidos únicos feitos a partir do dia 08 de Outubro de 2016?



select count(distinct(order\_id))

from orders

where date( order\_purchase\_timestamp) > '2016-10-08';



-- Qual o número total de pedidos únicos feitos com a data limite de envio, a partir do dia 08 de Outubro de 2016 incluso?



select count(distinct(order\_id))

from order\_items

where date( shipping\_limit\_date) > '2016-10-08';



-- Qual é o número total de pedidos únicos e o valor médio do frete para pedidos com valor abaixo de R$ 1.100?



select count(distinct(order\_id)) as total\_pedidos\_unicos, avg(freight\_value) as valor\_medio\_fre

from order\_items

where price < 1100;





-- Qual é o número total de pedidos únicos, a data mínima e máxima de limite de envio, 

--e os valores máximo, mínimo e médio do frete para pedidos com valor abaixo de R$ 1.100,

--agrupados por cada vendedor?





select \* from order\_items; -- consulta exploratoria dos nomes das colunas





select 

seller\_id as vendeor,

count(distinct(order\_id)) as total\_pedidos\_unicos, 

min(shipping\_limit\_date) as data\_minima, 

max(shipping\_limit\_date) as data\_maxima, 



max(freight\_value) as frete\_maximo,

min(freight\_value) as frete\_minimo, 

avg(freight\_value) as media\_do\_frete

from order\_items

where price< 1100

group by seller\_id;







&nbsp;                   -- Operadores Booleanos

&nbsp;                   





-- Qual o número de clientes únicos nos estado de Minas Gerais ou Rio de Janeiro?



select customer\_state as estados, count(distinct(customer\_id)) as clientes\_unicos

from customer

where customer\_state = 'MG' or customer\_state = 'RJ'

group by customer\_state;





-- Qual a quantidade de cidades únicas dos vendedores no estado de São Paulo ou Riode Janeiro com a latitude maior que -24.54 e longitude menor que -45.63?



select geolocation\_state ,count(distinct(geolocation\_city)) as ciade\_unicas

from geolocation

where (geolocation\_state = 'SP' or geolocation\_state = 'RJ') and geolocation\_lat > -24.54 and geolocation\_lng < -45.63

group by geolocation\_state;





--  Qual o número total de pedidos únicos, o número total de produtos e o preço médio

--dos pedidos com o preço de frete maior que R$ 20 e a data limite de envio entre os dias 1 e

--31 de Outubro de 2016? OBS: Use a função DATE() para converter a data no formato

--timestamp (data e hora) para data.



select count(distinct order\_id), count(product\_id), avg( price )

from order\_items

where freight\_value > 20 

and date(shipping\_limit\_date) >= '2016-10-01' 

and date(shipping\_limit\_date) <='2016-10-31';





--Mostre a quantidade total dos pedidos e o valor total do pagamento, para pagamentos 

--entre 1 e 5 prestações ou um valor de pagamento acima de R$ 5000. Agrupe por

--quantidade de prestações.



select payment\_installments, count (order\_id) , payment\_value

from order\_payments

where payment\_installments >= 1 and payment\_installments <= 5 or payment\_value > 5000

group by payment\_installments;





--Qual a quantidade de pedidos com o status em processamento ou cancelada

--acontecem com a data estimada de entrega maior que 01 de Janeiro de 2017 ou menor que

--23 de Novembro de 2016?



select order\_status from orders;  -- consulta investigativa pra formular a de baixo.



select order\_status, count(order\_id) as pedidos

from orders

where (order\_status ='processing' or order\_status = 'canceled') 

and 

(order\_estimated\_delivery\_date >'2017-01-01' or  order\_estimated\_delivery\_date <'2016-11-23')

group by order\_status;





-- Quantos produtos estão cadastrados nas categorias: perfumaria, brinquedos, esporte

--lazer, cama mesa e banho e móveis de escritório que possuem mais de 5 fotos, um peso

--maior que 5 g, um altura maior que 10 cm, uma largura maior que 20 cm?



select product\_category\_name, count (product\_id)

from  products

where (

product\_category\_name = 'perfumaria'

or product\_category\_name = 'brinquedos' 

or product\_category\_name = 'esporte\_lazer' 

or product\_category\_name = 'cama\_mesa\_banho' 

or product\_category\_name = 'moveis\_escritorio') 

and ( product\_photos\_qty > 5 and product\_weight\_g > 5 and product\_height\_cm > 10 and product\_width\_cm > 20)

group by product\_category\_name;





&nbsp;                       







&nbsp;					-- Operadores de Lógica de Intervalos



--  Quantos clientes únicos tiveram seu pedidos com status de “processingˮ, “shippedˮ e

--“deliveredˮ, feitos entre os dias 01 e 31 de Outubro de 2016. Mostrar o resultado somente se

--o número total de clientes for acima de 5.



select order\_status, count(distinct(customer\_id))

from orders

where order\_status in ('processing', 'shipped', 'delivered') and  order\_purchase\_timestamp between '2016-10-01 and' and '2016-10-31'

group by order\_status

having count(distinct(customer\_id))> 5;



-- Mostre a quantidade total dos pedidos e o valor total do pagamento, para pagamentos entre 1 e 5 prestações ou um valor de pagamento acima de R$ 5000.



select payment\_installments, count(order\_id) as total\_pedidos , sum(payment\_value) as total\_pagamentos

from order\_payments 

where (payment\_installments between 1 and 5) or (payment\_value > 5000)

group by payment\_installments;







--Quantos produtos estão cadastrados nas categorias: perfumaria, brinquedos, esporte 

--lazer e cama mesa, que possuem entre 5 e 10 fotos, um peso que não está entre 1 e 5 g, um

--altura maior que 10 cm, uma largura maior que 20 cm. Mostra somente as linhas com mais

--de 10 produtos únicos.



select 

product\_category\_name, 

count(distinct product\_id) as produtos\_unicos

from products

where product\_category\_name in ('perfumaria','brinquedos','esporte\_lazer','cama\_mesa\_banho') 

and product\_photos\_qty between 5 and 10

and product\_weight\_g not between 1 and 5

and  product\_height\_cm > 10

and product\_width\_cm > 20

group by product\_category\_name

having count( distinct product\_id ) > 10;







-- Qual a quantidade de cidades únicas dos vendedores no estado de São Paulo ou Rio de Janeiro com a latitude maior que -24.54 e longitude menor que -45.63?



select geolocation\_state, count( distinct geolocation\_city) as cidades

from geolocation 

where geolocation\_state in ('SP', 'RJ') 

and (geolocation\_lat > -24.54 and geolocation\_lng < -45.63)

group by geolocation\_state;







--Quantos produtos estão cadastrados em qualquer categorias que comece com a letra

--“aˮ e termine com a letra “oˮ e que possuem mais de 5 fotos? Mostrar as linhas com mais

--de 10 produtos.



select product\_category\_name    , count(distinct product\_id ) as produtos

from products 

where product\_category\_name like  'a%o' and product\_photos\_qty > 5

group by product\_category\_name 

having count(distinct product\_id ) > 10;







--Qual o número de clientes únicos, agrupados por estado e por cidades que comecem

--com a letra “mˮ, tem a letra “oˮ e terminem com a letra “aˮ? Mostrar os resultados somente

--para o número de clientes únicos maior que 10.



select  customer\_city, customer\_state , count(distinct customer\_id )

from customer

WHERE customer\_city  like 'm%o%a'

group by customer\_city, customer\_state

having  count(distinct customer\_id ) > 10















