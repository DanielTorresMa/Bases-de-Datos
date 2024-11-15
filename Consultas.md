# Taller Consultas - Nivel Medio

## Consultas

Aquí tienes un conjunto de preguntas de nivel medio que utilizan operaciones relacionales en MySQL aplicadas a la base de datos `world`. Cada una incluye una breve descripción del objetivo de la consulta y su formulación en álgebra relacional y en MySQL:

### 1. Encuentra los países que tienen un idioma oficial.
**Objetivo:** Identificar los países con al menos un idioma oficial registrado en la tabla `countrylanguage`.
- **Álgebra Relacional:** 
  $$\pi_{\text{CountryCode}}(\sigma_{\text{IsOfficial} = 'T'} (\text{CountryLanguage}))$$
- **Consulta en SQL:**
  ```sql
  SELECT c.name AS country_name, MAX(cl.language) AS Official_languages
  FROM country AS c
  JOIN countrylanguage AS cl ON c.code = cl.countrycode
  WHERE cl.isofficial = "t"
  GROUP BY c.code
  HAVING COUNT(cl.isofficial) = 1
  ORDER BY name;
  ```

### 2. Lista los países que tienen más de un idioma oficial.
**Objetivo:** Identificar los países con varios idiomas oficiales.
- **Álgebra Relacional:**
  $$\pi_{\text{CountryCode}} (\sigma_{\text{IsOfficial} = 'T'} (\text{CountryLanguage}))\ \ \text{GROUP BY CountryCode HAVING COUNT(*) > 1}$$
- **Consulta en SQL:**
  ```sql
  SELECT c.name AS country_name, COUNT(cl.isofficial) AS Official_languages
  FROM country AS c
  JOIN countrylanguage AS cl ON c.code = cl.countrycode
  WHERE cl.isofficial = 't'
  GROUP BY c.code
  HAVING COUNT(cl.isofficial) > 1
  ORDER BY name;
  ```

### 3. Encuentra los países que tienen el mismo continente que Japón.
**Objetivo:** Listar los países que comparten el mismo continente que Japón.
- **Álgebra Relacional:**
  $$\pi_{\text{Name}} (\text{Country} \bowtie_{\text{Continent} = 'Asia'} \text{Country})$$
- **Consulta en SQL:**
  ```sql
  SELECT name as Country_Name , continent
  FROM country
  WHERE continent=(SELECT continent
	               FROM country
                 WHERE name="JAPAN");
  ```

### 4. Encuentra las ciudades que tienen población mayor a 5 millones y están en América del Sur.
**Objetivo:** Filtrar ciudades por población y continente.
- **Álgebra Relacional:**
  $$\pi_{\text{City.Name}} (\sigma_{\text{City.Population} > 5000000 \land \text{Country.Continent} = 'South America'}(\text{City} \bowtie \text{Country}))$$
- **Consulta en SQL:**
  ```sql
  SELECT ci.name, ci.population
  FROM city as ci
  JOIN country as co ON co.code=ci.countrycode
  WHERE ci.population>"5000000"
  AND co.continent="South America";
  ```

### 5. Encuentra los países que no tienen ningún idioma oficial.
**Objetivo:** Listar los países sin idioma oficial.
- **Álgebra Relacional:**
  $$\pi_{\text{Country.Code}}(\text{Country}) - \pi_{\text{CountryCode}}(\sigma_{\text{IsOfficial} = 'T'}(\text{CountryLanguage}))$$
- **Consulta en SQL:**
  ```sql
  SELECT c.name AS Country
  FROM country AS c
  LEFT JOIN countrylanguage AS cl ON c.code = cl.countrycode AND cl.IsOfficial = 'T'
  WHERE cl.countrycode IS NULL;
  ```

### 6. Encuentra los idiomas que son oficiales en al menos dos países.
**Objetivo:** Identificar idiomas que sean oficiales en varios países.
- **Álgebra Relacional:**
  $$\pi_{\text{Language}} (\sigma_{\text{IsOfficial} = 'T'} (\text{CountryLanguage}) \ \text{GROUP BY Language HAVING COUNT(DISTINCT CountryCode) >= 2})$$
- **Consulta en SQL:**
  ```sql
  SELECT cl.language AS language_name, COUNT(cl.isofficial) AS Countries_as_official
  FROM country AS c
  JOIN countrylanguage AS cl ON c.code = cl.countrycode
  WHERE cl.isofficial = 't'
  GROUP BY cl.language
  HAVING COUNT(cl.isofficial) > 1
  ORDER BY cl.language;
  ```

### 7. Lista los países y su capital.
**Objetivo:** Obtener la relación entre los países y sus capitales.
- **Álgebra Relacional:**
  $$\pi_{\text{Country.Name}, \text{City.Name}} (\text{Country} \bowtie_{\text{Country.Capital} = \text{City.ID}} \text{City})$$
- **Consulta en SQL:**
  ```sql
  SELECT co.name AS country, ci.name AS capital
  FROM country as co
  JOIN city AS ci ON co.capital=ci.id;
  ```

### 8. Encuentra los países que tienen una población mayor que Alemania.
**Objetivo:** Comparar población con un país específico.
- **Álgebra Relacional:**
  $$\pi_{\text{Name}}(\sigma_{\text{Population} > ( \text{SELECT Population FROM Country WHERE Name = 'Germany'} ) }(\text{Country}))$$
- **Consulta en SQL:**
  ```sql
  SELECT name, population
  FROM country
  WHERE population>(SELECT population
				          FROM country
                  WHERE name="Germany");
  ```

### 9. Encuentra los idiomas oficiales de Europa.
**Objetivo:** Listar los idiomas oficiales de países europeos.
- **Álgebra Relacional:**
  $$\pi_{\text{Language}} (\sigma_{\text{Country.Continent} = 'Europe'} (\text{Country} \bowtie \text{CountryLanguage}))$$
- **Consulta en SQL:**
  ```sql
  SELECT cl.language, COUNT(c.name) AS Official_in_europe
  FROM country AS c
  JOIN countrylanguage AS cl ON c.code = cl.countrycode
  WHERE cl.isofficial = 't'
  AND c.continent="EUROPE"
  GROUP BY cl.language
  ORDER BY cl.language;
  ```

### 10. Encuentra los países sin ciudades registradas en la tabla `City`.
**Objetivo:** Detectar países sin representación en la tabla de ciudades.
- **Álgebra Relacional:**
  $$\pi_{\text{Country.Code}}(\text{Country}) - \pi_{\text{CountryCode}}(\text{City})$$
- **Consulta en SQL:**
  ```sql
  SELECT co.name AS Country
  FROM country AS co
  LEFT JOIN city AS ci ON co.code = ci.countrycode
  WHERE ci.countrycode IS NULL;
  ```

### 11. Muestra la población total de cada continente.
**Objetivo:** Calcular la población por continente.
- **Álgebra Relacional:**
  $$\pi_{\text{Continent}, \text{SUM(Population)}} (\text{Country} \ \text{GROUP BY Continent})$$
- **Consulta en SQL:**
  ```sql
  SELECT continent, SUM(population) as Total_population
  FROM country
  GROUP BY continent
  ORDER BY Total_population DESC;
  ```

### 12. Encuentra los países en los que la esperanza de vida es menor al promedio global.
**Objetivo:** Filtrar países con esperanza de vida baja en comparación con el promedio.
- **Álgebra Relacional:** $$\pi_{\text{Name}}(\sigma_{\text{LifeExpectancy} < \text{AVG(LifeExpectancy)}}(\text{Country}))$$
- **Consulta en SQL:**
  ```sql
  SELECT name AS Country,lifeexpectancy as Life_Expectancy
  FROM country
  WHERE lifeexpectancy < (SELECT AVG(lifeexpectancy) FROM country)
  ORDER BY lifeexpectancy ASC;
  ```

### 13. Encuentra los países en Asia sin idioma oficial registrado.
**Objetivo:** Listar países asiáticos sin idiomas oficiales.
- **Álgebra Relacional:**
  $$\pi_{\text{Country.Code}}(\sigma_{\text{Continent} = 'Asia'} (\text{Country})) - \pi_{\text{CountryCode}}(\sigma_{\text{IsOfficial} = 'T'}(\text{CountryLanguage}))$$
- **Consulta en SQL:**
  ```sql
  SELECT co.name AS Country
  FROM country AS co
  LEFT JOIN countrylanguage AS cl ON co.code = cl.countrycode AND cl.IsOfficial = 'T'
  WHERE cl.countrycode IS NULL AND continent="Asia";
  ```

### 14. Lista los idiomas que son oficiales en países con esperanza de vida mayor a 80.
**Objetivo:** Identificar idiomas en países con alta esperanza de vida.
- **Álgebra Relacional:**
  $$\pi_{\text{Language}} (\sigma_{\text{Country.LifeExpectancy} > 80} (\text{Country} \bowtie \text{CountryLanguage}))$$
- **Consulta en SQL:**
  ```sql
  SELECT language
  FROM countrylanguage as cl
  LEFT JOIN country AS co on co.code=cl.countrycode
  WHERE IsOfficial="t" AND co.lifeexpectancy>"80";
  ```

### 15. Encuentra los países con más de 10 ciudades en la tabla `City`.
**Objetivo:** Identificar países con una gran cantidad de ciudades registradas.
- **Álgebra Relacional:**

$$ \pi_{\text{CountryCode}} (\text{City} \ \text{GROUP BY CountryCode HAVING COUNT(*) > 10}) $$

- **Consulta en SQL:**
  ```sql
  SELECT co.name AS Country, COUNT(countrycode) as Cities
  FROM country as co
  JOIN city AS ci ON co.code = ci.countrycode
  GROUP BY ci.countrycode
  HAVING COUNT(countrycode)>"10"; 
  ```

## Ejercicio

Propongan 10 preguntas, con su respectiva representación en el álgebra relacional, que puedan ser resueltas con consultas a la base de datos **World**. Deben proponer la pregunta y responderla utilizando comandos de MySQL.

  **#1. Encuentra los países que tienen la misma región que Rúsia.**

  **Álgebra relacional**:  

  $$ π_Country_Name,Region(σ_Region = (π_Region(σ_name = 'Russian Federation'(country)))(country)) $$

  **SQL equivalente**:  
  ```sql
SELECT name as Country_Name , Region
	FROM country
	WHERE Region=(SELECT region
	FROM country
  	WHERE name="Russian Federation");
  ```
  **2. Encuentra las ciudades que están en el caribe y dependen de reino unido ordenado ascendentemente por nombre.**

  **Álgebra relacional**:  

  $$ π_City,population(σ_region = 'Caribbean' ∧ HeadOfState = 'Elisabeth II'(city ⨝_countrycode = code country)) $$

  **SQL equivalente**:  
  ```sql
SELECT ci.name as City, ci.population
	FROM city as ci
	JOIN country as co ON co.code=ci.countrycode
	WHERE co.region="Caribbean"
	AND co.HeadOfState="Elisabeth II"
	ORDER BY city;
  ```
  **3. Encuentra las ciudades que están en el caribe y dependen de reino unido ordenado ascendentemente por nombre.**

  **Álgebra relacional**:  

  $$ π_language_name(σ_Countries_as_nonofficial > 1(γ_language, COUNT(isofficial) as Countries_as_nonofficial(σ_isofficial = 'f'(countrylanguage ⨝ country)))) $$

  **SQL equivalente**:  
  ```sql
SELECT cl.language AS language_name, COUNT(cl.isofficial) AS Countries_as_nonofficial
	FROM country AS c
	JOIN countrylanguage AS cl ON c.code = cl.countrycode
	WHERE cl.isofficial = 'f'
	GROUP BY cl.language
	HAVING COUNT(cl.isofficial) > 1
	ORDER BY cl.language;
  ```
  **#4. Encuentra los países en America del sur sin idiomas no oficiales registrado.**


  **Álgebra relacional**:  

  $$ π_Country(σ_continent = 'South America' ∧ countrycode IS NULL(country LEFT JOIN_code = countrycode ∧ IsOfficial = 'f' countrylanguage)) $$

  **SQL equivalente**:  
  ```sql
SELECT co.name AS Country
	FROM country AS co
	LEFT JOIN countrylanguage AS cl ON co.code = cl.countrycode AND cl.IsOfficial = 'f'
	WHERE cl.countrycode IS NULL AND continent="South America";
  ```
  **5. Encuentra los países que tienen entre 5 y 10 ciudades en la tabla City.**

  **Álgebra relacional**:  

  $$ π_Country,Cities(σ_Cities ≥ 5 ∧ Cities ≤ 10(γ_countrycode, COUNT(countrycode) as Cities(country ⨝_code = countrycode city))) $$

  **SQL equivalente**:  
  ```sql
SELECT co.name AS Country, COUNT(countrycode) as Cities
	FROM country as co
	JOIN city AS ci ON co.code = ci.countrycode
	GROUP BY ci.countrycode
	HAVING COUNT(countrycode) BETWEEN "5" AND "10";
  ```
  **#6. Lista los idiomas que son no son oficiales en países con más de 100 millones de habitantes.**

  **Álgebra relacional**:  

  $$ π_language(σ_IsOfficial = 'f' ∧ population > 100000000(countrylanguage LEFT JOIN_countrycode = code country)) $$

  **SQL equivalente**:  
  ```sql
SELECT language
	FROM countrylanguage as cl
	LEFT JOIN country AS co on co.code=cl.countrycode
	WHERE IsOfficial="f" AND co.population>"100000000";
  ```
  **#7. Encuentra los países que tienen una area mayor que Colombia.**


  **Álgebra relacional**:  

  $$ π_name,surfacearea(σ_surfacearea > (π_surfacearea(σ_name = 'Colombia'(country)))(country)) $$

  **SQL equivalente**:  
  ```sql
  SELECT name, surfacearea
	FROM country
	WHERE surfacearea>(SELECT surfacearea
				FROM country
				WHERE name="Colombia");
  ```
  **#8. Lista los países con expectativa de vida debajo del promerio mundial**


  **Álgebra relacional**:  

  $$ π_Country,Life_Expectancy(σ_lifeexpectancy < AVG(lifeexpectancy)(country)) $$

  **SQL equivalente**:  
  ```sql
  SELECT name AS Country,lifeexpectancy as Life_Expectancy
	FROM country
	WHERE lifeexpectancy < (SELECT AVG(lifeexpectancy) FROM country)
	ORDER BY lifeexpectancy ASC;
  ```
  **#9 Muestra el área total de cada continente.**


  **Álgebra relacional**:  

  $$ π_continent,Total_population(γ_continent, SUM(surfacearea) as Total_population(country)) $$

  **SQL equivalente**:  
  ```sql
  SELECT continent, SUM(surfacearea) as Total_population
	FROM country
	GROUP BY continent
	ORDER BY Total_population DESC;
  ```
  **#10 Encuentra los países que tienen la misma forma de gobierno que rusia.**

  **Álgebra relacional**:  

  $$ π_Country_Name,governmentform(σ_governmentform = (π_governmentform(σ_name = 'Russian Federation'(country)))(country)) $$

  **SQL equivalente**:  
  ```sql
  SELECT name as Country_Name , governmentform
	FROM country
	WHERE governmentform=(SELECT governmentform
				FROM country
                	 	WHERE name="Russian Federation");
  ```

## Entregables

Deben entregar un archivo tipo markdown con las respuestas de las preguntas escritas en MySQL, puede llamarse `consultas.md`. En este también deben colocar los pantallazos de los resultados, para ello en la misma carpeta donde esta el archivo creen una carpeta de `imagenes` y guardan alli todos los pantallazos. Para llamarlas dentro del archivo markdown usen el comando:

```html
<div align="center">
  <img src="./imagenes/nombre_imagen.png" width=90%>
</div>
```
## Conclusión

La realización de este taller permite a los estudiantes afianzar sus habilidades en SQL, enfocándose en operaciones relacionales fundamentales en el contexto de bases de datos. A través de las consultas realizadas, se logra una comprensión sólida sobre cómo se estructuran y ejecutan consultas complejas que implican múltiples tablas y condiciones. Este conocimiento es esencial en la gestión de datos en entornos reales, donde la habilidad de interpretar y manipular datos desde diferentes fuentes es crucial para el análisis y la toma de decisiones.

Al explorar distintas formas de unión y filtrado en consultas, los estudiantes desarrollan una visión completa sobre el potencial de SQL en el manejo de datos, preparándolos para enfrentar situaciones más avanzadas en el ámbito profesional. Finalmente, la combinación de SQL y álgebra relacional ofrece una perspectiva integral, permitiendo comprender tanto la implementación técnica como los principios teóricos subyacentes en cada consulta.

## References

### Documentación y BD

- [15.2.13.2 JOIN Clause - MySQL Documentation](https://dev.mysql.com/doc/refman/8.4/en/join.html)
- "world" Sample Database. (n.d.). *MySQL Sample Databases*. Retrieved from [https://dev.mysql.com/doc/index-other.html](https://dev.mysql.com/doc/index-other.html)


### Guías y Tutoriales

- [MySQL Joins - W3School](https://www.w3schools.com/mysql/mysql_join.asp)
- [SQL - HAVING vs. WHERE](https://stackoverflow.com/questions/9253244/sql-having-vs-where)
- [MySQL JOINS - JapaPoint](https://www.javatpoint.com/mysql-join)

### Videos
- [join in mysql - Youtube](https://www.youtube.com/results?search_query=join+in+mysql)
- [MySQL: JOINS are easy (INNER, LEFT, RIGHT) ](https://www.youtube.com/watch?v=G3lJAxg1cy8)



### Libros

- Codd, E. F. (1970). *A Relational Model of Data for Large Shared Data Banks*. Communications of the ACM, 13(6), 377-387.
- Date, C. J. (2003). *An Introduction to Database Systems* (8th ed.). Pearson Education.
- Elmasri, R., & Navathe, S. B. (2011). *Fundamentals of Database Systems* (6th ed.). Pearson Education.
