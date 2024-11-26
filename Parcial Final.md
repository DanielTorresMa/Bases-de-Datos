# V1


## HackerRank

Realizar las 3 consultas asignadas de la página [Sql](https://www.hackerrank.com/domains/sql?filters%5Bskills%5D%5B%5D=SQL%20%28Basic%29&filters%5Bdifficulty%5D%5B%5D=easy) y una que selecciones. En total 4 consultas. 


| **No.** |                **Consulta**                | **No.** |               **Consulta**               |
|:-------:|:------------------------------------------:|:-------:|:----------------------------------------:|
|    1    | Weather Observation Station 9              |    19   | Select All                               |
|    2    | Weather Observation Station 15             |    20   | Revising Aggregations - Averages         |
|    3    | Weather Observation Station 8              |    21   | Select By ID                             |
|    4    | Weather Observation Station 6              |    22   | Higher Than 75 Marks                     |
|    5    | Weather Observation Station 10             |    23   | Revising Aggregations - The Sum Function |
|    6    | African Cities                             |    24   | Japan Population                         |
|    7    | Weather Observation Station 17             |    25   | Weather Observation Station 14           |
|    8    | Weather Observation Station 4              |    26   | Weather Observation Station 12           |
|    9    | Revising Aggregations - The Count Function |    27   | Population Density Difference            |
|    10   | Weather Observation Station 3              |    28   | Employee Names                           |
|    11   | Japanese Cities' Names                     |    29   | Weather Observation Station 1            |
|    12   | Japanese Cities' Attributes                |    30   | Average Population of Each Continent     |
|    13   | The Blunder                                |    31   | Employee Salaries                        |
|    14   | Type of Triangle                           |    32   | Revising the Select Query I              |
|    15   | Weather Observation Station 11             |    33   | Revising the Select Query II             |
|    16   | Average Population                         |    34   | Weather Observation Station 16           |
|    17   | Top Earners                                |    35   | Weather Observation Station 7            |
|    18   | Population Census                          |    36   | Weather Observation Station 13           |
|         |                                            |    37   | Weather Observation Station 2            |
|         |                                            |         |                   

##CONSULTAS ASIGNADAS
Top Earners
Population Census
Select All
Select By ID (escogida)

###Top Earners
```sql
SELECT MAX(MONTHS*SALARY), COUNT(*)
FROM EMPLOYEE
WHERE (MONTHS*SALARY) = (SELECT MAX(MONTHS*SALARY)
                         FROM EMPLOYEE);
```
###Population Census  
```sql
SELECT SUM(CITY.POPULATION)
FROM CITY 
JOIN COUNTRY ON CITY.COUNTRYCODE = COUNTRY.CODE
WHERE COUNTRY.CONTINENT = 'ASIA';
```

###Select All
```sql
select * from City;
```

###Select By ID
```sql
select * from City where ID=1;
```

## Consultas Básicas

### 1. Listar los títulos únicos de los empleados que empiezan por 'S'
```sql
SELECT DISTINCT t.title
FROM employees e
JOIN titles t ON e.emp_no = t.emp_no
WHERE e.first_name LIKE 'M%';
```

### 2. Contar el número total de empleados en el departamento 'Sales' que empezaron después del '1989-01-01'

```sql
SELECT COUNT(*) AS total_empleados FROM employees e
JOIN dept_emp de ON e.emp_no = de.emp_no
WHERE from_date > '1989-01-01';
```

### 3. Calcular el salario promedio de los empleados que ganan más de 39.000. Imprimir solo 2 decimales redondeadas(comando ROUND())
```sql
SELECT AVG(s.salary) AS avg_salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no
WHERE s.salary > 39000;
```

### 4. Mostrar el número de empleados por departamento
```sql
SELECT dept_name,count(de.emp_no) FROM departments d
JOIN dept_emp de ON d.dept_no=de.dept_no
GROUP BY de.dept_no;
```

### 5. Filtrar empleados con salarios entre 39.000 y 40.000 y organizarlos en orden ascendente por salario
```sql
SELECT first_name, last_name, salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no
WHERE s.salary BETWEEN 39000 AND 40000;
```

### 6. Encontrar el salario máximo y mínimo en la tabla `salaries`. Además, renombrarlos a `salario_maximo` y `salario_minimo`
```sql
SELECT MAX(salary) AS salario_maximo, MIN(salary) AS salario_minimo FROM salaries;
```

### 7. Ordenar los empleados por fecha de contratación desde la más reciente a la más antigua
```sql
SELECT *
FROM employees
ORDER BY hire_date DESC;
```

### Vistas

Estás consultas deben implementarse como vistas. Deben implementar la vista y llamarla.

#### 8. Buscar empleados cuyo nombre contenga la silaba 'est', organizarlos en orden Alfabético
```sql
CREATE VIEW empleados_con_est AS
SELECT *
FROM employees e
WHERE e.first_name LIKE '%est%';
```

#### 9. Mostrar la cantidad de empleados con más de un título asignado. El empleado no debe salir repetido
```sql
CREATE VIEW empleados_muchos_titulos AS
SELECT emp_no, count(title)
FROM titles
GROUP BY emp_no
HAVING count(title) > 1;
```

### Rutinas

Está consulta deben implementarse como una rutina. Deben implementar la rutina y llamarla.

#### 10. Obtener los empleados que fueron contratados después de 1989-06-06 dado un genero del cual depende la rutina

```sql

```

#### 11. Obtener los títulos de los empleados que empezaron entre dos fechas que define el usuario, ejemplo: 1985-06-01 hasta 1985-12-01

```sql

```

## Consultas Nivel Medio (Usando JOIN)

### 12. Listar los nombres de los empleados junto con sus salarios actuales (con la fecha to_date más grande). El nombre debe aparecer en una sola columna (usar CONCAT)
```sql

```

### 13. Listar los empleados que tienen múltiples títulos en la empresa
```sql

```

### 14. Encuentra el salario promedio de los empleados actuales por cada departamento.  
   ```sql

   ```


### 15. Encontrar los empleados que han trabajado en más de un departamento y su apellido empiece por G
```sql

```

### 16. Listar los nombres de los departamentos y la cantidad de empleados en cada uno. Además, organizar ascendentemente por el número total de empleados
```sql

``` 

