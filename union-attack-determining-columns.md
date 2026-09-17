# SQL injection UNION attack, determining the number of columns returned by the query

**Source:** PortSwigger Web Security Academy
**Category:** SQL injection
**Date:** 2026-09

## The target

<!-- Dos lineas: que hace esta pagina (filtra productos por categoria) y cual
era el objetivo del lab (averiguar cuantas columnas devuelve la consulta que hay
detras del filtro). -->

## Reconnaissance

<!-- Que viste: la URL lleva ?category=..., asi que ese valor entra en una
consulta SQL. Como confirmaste que era inyectable (cuando comentaste el filtro
con ' -- te aparecieron tambien los productos unreleased). -->

## What I tried that did not work

<!-- AQUI ESTA TU MEJOR MATERIAL. Dos callejones sin salida reales:
  1. Fuiste directo con UNION SELECT * FROM users. Por que no funciona: asumias
     el nombre de la tabla y con * no controlas el numero de columnas, asi que la
     base de datos rechaza el UNION.
  2. Estuviste un rato en el lab equivocado (el de "XML encoding"), donde todo
     devolvia 200 y ningun payload hacia nada, hasta que te diste cuenta mirando
     el titulo del lab. Leccion: verifica que atacas el objetivo correcto antes
     de pelearte con el payload. -->

## The method

<!-- La idea de UNION SELECT NULL: por que NULL (es un comodin que encaja con
cualquier tipo de columna) y por que vas subiendo de uno en uno. Explica la regla
del UNION: las dos consultas tienen que devolver el mismo numero de columnas. -->

## Exploitation

<!-- La secuencia real que hiciste en el Repeater, mirando el codigo de estado:
  - UNION SELECT NULL           -> 500
  - UNION SELECT NULL,NULL      -> 500
  - UNION SELECT NULL,NULL,NULL -> 200, y sale la pagina
  Pon tu payload final y di que la consulta tiene 3 columnas. Menciona que lo
  hiciste con Burp Repeater y que la senal fue el salto de 500 a 200. -->

## Root cause / Takeaway

<!-- El matiz que entendiste al final: NO cuentas las columnas de la tabla, sino
las que DEVUELVE la consulta original (la tabla puede tener muchas mas). Y una
frase de que te llevas para la proxima. -->
