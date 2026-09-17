# Sección 1. Fundamentos: filtrado y agregación

## Pregunta 1 — Catálogo comercial activo

**Enunciado:** Obtén los productos que **no** están descatalogados y cuyo precio unitario esté entre 10 y 50 euros, ambos incluidos. Muestra el nombre del producto y su precio redondeado a dos decimales, ordenado de mayor a menor precio.

**Consulta:**

```sql
-- Productos no descatalogados con precio entre 10 y 50 ordenados descendentemente
SELECT product_name, ROUND(unit_price::numeric, 2)
FROM products
WHERE (unit_price::numeric BETWEEN 10 AND 50)
AND discontinued = 0
ORDER BY unit_price::numeric DESC
```

**Resultado:**

![Descripción de la imagen](img/p01.png)

**Comentario:** He usado 2 filtros en la clausula WHERE, uno para el precio y otro para incluir solo los productos activos. Por ello he envuelto en parénteis la primera condicion para clarificar la consulta. Por último he ordenado descendentemente para que los productos más caros aparezcan arriba

## Pregunta 2 — Concentración geográfica de la cartera

**Enunciado:** Cuenta cuántos clientes hay en cada país y muestra únicamente aquellos países con 5 o más clientes, ordenados de mayor a menor. Indica también cuántas ciudades distintas hay en cada uno de esos países.
**Consulta:**

```sql
-- Países con 5 o más clientes, ordenados de mayor a menor descendentemente, y cuántas ciudades hay en ellos
SELECT country AS pais, COUNT(customer_id) AS num_clientes, COUNT(DISTINCT city)
FROM customers
AS num_ciudades
GROUP BY country
HAVING(COUNT(customer_id)) > 5
ORDER BY COUNT(customer_id) DESC
```

**Resultado:**

![Descripción de la imagen](img/p02.png)

**Comentario:** Comentario: He agrupado los datos por país para poder calcular dos cosas: cuántos clientes hay y cuántas ciudades únicas (usando DISTINCT) existen en cada uno. Después, en lugar de usar WHERE, he utilizado la cláusula HAVING porque estoy filtrando sobre un cálculo (el recuento de clientes) para quedarme solo con los países que tienen más de 5 clientes. Por último, he ordenado los resultados de forma descendente para que los países con mayor cantidad de clientes aparezcan arriba del todo.

## Pregunta 3 — Alerta de reposición

**Enunciado:** Localiza los productos activos cuyas unidades en stock sean inferiores o iguales a su nivel de reposición. Muestra el nombre, las unidades en stock, el nivel de reposición, las unidades ya pedidas al proveedor y una columna de texto que indique 'CRÍTICO' cuando el stock sea 0 y 'AVISO' en el resto de casos.
**Consulta:**

```sql
-- -- Productos activos con stock igual o inferior a su nivel de reposición, detallando unidades y estado de alerta ('CRÍTICO' o 'AVISO')
SELECT product_name AS producto,
	units_in_stock AS stock,
	reorder_level AS nivel_reposicion,
	units_on_order AS pedido_a_proveedor,
	CASE WHEN units_in_stock = 0 THEN 'CRÍTICO'
	ELSE 'AVISO'
	END AS situacion
FROM products
WHERE 
(discontinued = 0 AND units_in_stock <= reorder_level)
```

**Resultado:**

![Descripción de la imagen](img/p03.png)

**Comentario:** He filtrado en el WHERE los productos activos con el stock al límite o por debajo del nivel de reposición. Además, he utilizado una estructura CASE para generar la nueva columna de alerta, marcando 'CRÍTICO' si el stock es 0 y 'AVISO' en el resto de casos.

# Sección 2. INNER JOIN

## Pregunta 4 — Ficha completa de producto

**Enunciado:** Para los productos suministrados por empresas de Italia, Francia o España, muestra el nombre del producto, el nombre de la categoría, el nombre del proveedor, su país y su ciudad. Ordena por país y, dentro de cada país, por nombre de producto.
**Consulta:**

```sql
-- -- Productos de proveedores en Italia, Francia o España con su categoría y origen, ordenados por país y producto
SELECT product_name AS producto,
	units_in_stock AS stock,
	reorder_level AS nivel_reposicion,
	units_on_order AS pedido_a_proveedor,
	CASE WHEN units_in_stock = 0 THEN 'CRÍTICO'
	ELSE 'AVISO'
	END AS situacion
FROM products
WHERE 
(discontinued = 0 AND units_in_stock <= reorder_level)
```

**Resultado:**

![Descripción de la imagen](img/p04.png)

**Comentario:** He filtrado en el WHERE los productos activos con el stock al límite o por debajo del nivel de reposición. Además, he utilizado una estructura CASE para generar la nueva columna de alerta, marcando 'CRÍTICO' si el stock es 0 y 'AVISO' en el resto de casos.