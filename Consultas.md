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
![](./imagenes/01.png?raw=true)

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
![](./imagenes/02.png?raw=true)

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
![](./imagenes/03.png?raw=true)

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
![](./imagenes/04.png?raw=true)

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
![](./imagenes/05.png?raw=true)

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
![](./imagenes/06.png?raw=true)

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
![](./imagenes/07.png?raw=true)

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
![](./imagenes/08.png?raw=true)

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
![](./imagenes/09.png?raw=true)

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
![](./imagenes/10.png?raw=true)

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
![](./imagenes/11.png?raw=true)

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
![](./imagenes/12.png?raw=true)

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
![](./imagenes/13.png?raw=true)

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
![](./imagenes/14.png?raw=true)

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
![](./imagenes/15.png?raw=true)

## Ejercicio - Consultas Propuestas

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
![](./imagenes/p01.png?raw=true)

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
![](./imagenes/p02.png?raw=true)

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
![](./imagenes/p03.png?raw=true)

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
![](./imagenes/p04.png?raw=true)

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
![](./imagenes/p05.png?raw=true)
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
![](./imagenes/p06.png?raw=true)

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
![](./imagenes/p07.png?raw=true)

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
![](./imagenes/p08.png?raw=true)

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
![](./imagenes/p09.png?raw=true)

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
![](./imagenes/p10.png?raw=true)
