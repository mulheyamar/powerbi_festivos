# 01. FESTIVOS

datos: 
    - url: "https://www.cuandoenelmundo.com/calendario/espana/2023"


# SOLUCION

### INSERTAR CONSULTA

Como tenemos que usar solo lenguaje M:
- Abrimos transformar datos.
- ABrimos icono Editor avanzado
- Usamos Web.Contents para incorporar la url:
  ![webcontents](../img/01-festivos.png)

- Nos aparece esto:
  ![conexion](../img/02-festivos.png)
  Una vez ahí pinchamos en inicio y volvemos a editor avanzado.

  ![nav](../img/03-festivos.png)

#### Paso Avanzado: Mostrar datos obtenidos en la web
Para que nos muestre los datos de la navegación web añadimos otra línea. Las lineas se separan con ",". En la siguiente línea debemos poner nav = Web.Page(web)
El código es el siguiente:
```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web)
in
    nav
```
**let:**

Esta palabra clave inicia una expresión let en M. Las expresiones let se usan para definir una o más variables locales y sus valores.

**web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"):**

Esta línea define una variable llamada web.
Utiliza la función Web.Contents para realizar una solicitud HTTP GET al URL proporcionado (https://www.cuandoenelmundo.com/calendario/espana/2023).
La función devuelve el contenido de la página web como un valor binario.

**nav = Web.Page(web):**

Esta línea define una variable llamada nav.
Utiliza la función Web.Page para convertir el contenido binario de la variable web en una tabla navegable. La función Web.Page analiza el HTML y extrae los elementos de la página web en un formato tabular que puede ser utilizado en Power Query.

**in nav:**

Esta línea especifica el valor de retorno de la expresión let.
El valor de retorno será el contenido de la variable nav, que es la tabla navegable resultante de la página web.

Ya deberíamos ver la tabla creada
![tabla](../img/04-festivos.png)


#### Siguiente paso avanzado: 

Seleccionar las tablas que tengan los festivos:

Volvemos a editor avanzado y continuamos con otra variable, le llamamos (SeleccionFilas):

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays")
in
    SelecionFilas

```
![](../img/05-festivos.png)

1. **Table.SelectRows:**

Es una función de Power Query/M que se utiliza para seleccionar filas de una tabla según una condición especificada.
nav:

Este es el nombre de la tabla que se quiere filtrar. En este caso, **nav** es la tabla generada en el paso anterior que contiene los datos extraídos de la página web.

2. **each [ClassName] = "hdays":**

**each** es un operador que se utiliza en Power Query/M para aplicar una función a cada fila de la tabla.

**[ClassName] = "hdays"** es la condición que se aplica a cada fila. Esta condición verifica si el valor en la columna ClassName de la fila actual es igual a "hdays".
En conjunto, la instrucción **Table.SelectRows(nav, each [ClassName] = "hdays")** selecciona todas las filas de la tabla nav donde el valor de la columna ClassName es igual a "hdays".

El resultado sería:
![](../img/05_b-festivos.png)

#### Siguiente paso avanzado: 
Seleccionar solo el campo Data que contiene las tablas por expandir.

![](../img/06-festivos.png)

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data]
in
    Tablas
```

Estamos viendo estas dos tablas de la web y tenemos que unirlas.
![](../img/07-tablas.png)

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data],
    Combinartablas = Table.Combine ({Tablas{0},Tablas{1}})
in
    Combinartablas
```

**{Tablas{0}, Tablas{1}}:**

Esta es una lista que contiene dos elementos. En M, las listas se definen utilizando llaves {}.

**Tablas{0} y Tablas{1}** son referencias a los elementos en la lista llamada Tablas.
**Tablas{0}** se refiere al primer elemento de la lista Tablas y **Tablas{1}** se refiere al segundo elemento de la lista Tablas.
En conjunto, la instrucción **Table.Combine({Tablas{0}, Tablas{1}})** toma dos tablas (el primer y el segundo elemento de la lista Tablas) y las combina en una sola tabla.

![](../img/08-tablas.png)

El resultado que deberíamos ver en este momento es la combinación de ambas tablas:
![](../img/08-resultadocombi.png)


#### Siguiente paso avanzado: Obtener fechas

Tenemos los festivos, el mes y el número de día pero nos falta un campo fecha:

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data],
    Combinartablas = Table.Combine ({Tablas{0},Tablas{1}}),
    Fecha = Table.AddColumn (Combinartablas, "Fecha", each [Column1] & "/" & [Column2] & "/" & "2023")
in
    Fecha
```

**Table.AddColumn(Combinartablas, "Fecha", each [Column1] & "/" & [Column2] & "/" & "2023"):**

Esta es una función que agrega una nueva columna a una tabla existente. Desglosando la función:

- **Table.AddColumn:**
    - Es una función de Power Query/M que se utiliza para agregar una nueva columna a una tabla existente.
    - Esta función toma tres argumentos:
      - 1. La tabla a la que se le va a agregar la nueva columna.
      - 2. El nombre de la nueva columna.
      - 3. Una función que define los valores de la nueva columna para cada fila.
- **Combinartablas:**
    - Esta es la tabla a la que se le va a agregar la nueva columna. En este caso, es la tabla resultante de la combinación de las tablas en la variable ***Combinartablas.***
- **"Fecha":**
    - Este es el nombre de la nueva columna que se va a agregar a la tabla. En este caso, la nueva columna se llamará "Fecha".
- **each [Column1] & "/" & [Column2] & "/" & "2023":**
    - **each** es un operador que se utiliza en Power Query/M para aplicar una función a cada fila de la tabla.
    - **[Column1] & "/" & [Column2] & "/" & "2023"** es la expresión que define el valor de la nueva columna para cada fila.
    - sta expresión concatena el valor de Column1, una barra "/", el valor de Column2, otra barra "/", y el año "2023". Es decir, está construyendo una cadena de texto que representa una fecha en el formato "dd/MM/yyyy", donde Column1 es el día, Column2 es el mes, y "2023" es el año.
  
![](../img/09-fechas.png)


El resultado es:

![](../img/10-calendario.png)

#### Siguiente paso avanzado: Cambiar tipo

Hay que cambiar el tipo a Formato fecha

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data],
    Combinartablas = Table.Combine ({Tablas{0},Tablas{1}}),
    Fecha = Table.AddColumn (Combinartablas, "Fecha", each [Column1] & "/" & [Column2] & "/" & "2023"),
    Tipo = Table.TransformColumnTypes ( Fecha ,{"Fecha", type date} )
in
    Tipo
```
**Table.TransformColumnTypes(Fecha, {"Fecha", type date}):**
Esta es una función que cambia el tipo de datos de una o más columnas en una tabla. Desglosando los términos:
- **Table.TransformColumnTypes**
    - Es una función de Power Query/M que se utiliza para cambiar el tipo de datos de una o más columnas en una tabla.
    - Esta función toma dos argumentos:
      - 1. La tabla en la que se cambiarán los tipos de columnas.
      - 2. Una lista de pares de valores que especifican el nombre de la columna y el nuevo tipo de datos para esa columna.
- **Fecha**
    - Esta es la tabla en la que se cambiarán los tipos de columnas
- **{"Fecha", type date}**
    - Este es un par de valores que se encuentra en una lista. En Power Query/M, las listas se definen utilizando llaves {}.
    - **"Fecha"** es el nombre de la columna cuyo tipo de datos se va a cambiar. En este caso, es la columna "Fecha" que se agregó en el paso anterior.
    - **type date** es el nuevo tipo de datos que se asignará a la columna "Fecha". type date especifica que los valores en la columna "Fecha" deben ser interpretados como fechas.
  
![](../img/11-tipo.png)

#### Siguiente paso avanzado: Quitar Column1 y Column2

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data],
    Combinartablas = Table.Combine ({Tablas{0},Tablas{1}}),
    Fecha = Table.AddColumn (Combinartablas, "Fecha", each [Column1] & "/" & [Column2] & "/" & "2023"),
    Tipo = Table.TransformColumnTypes ( Fecha ,{"Fecha", type date} ),
    TablaSinColumnas = Table.RemoveColumns(Tipo, {"Column1", "Column2"})
in
    TablaSinColumnas
```
Con la última instrucción quitamos las columnas que no nos interesan


#### Siguiente paso avanzado: Cambiar nombre de campo "Column2" a "Festivos"

```c++
let
    web = Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023"),
    nav = Web.Page(web),
    SeleccionFilas = Table.SelectRows(nav , each [ClassName]= "hdays"),
    Tablas = SeleccionFilas [Data],
    Combinartablas = Table.Combine ({Tablas{0},Tablas{1}}),
    Fecha = Table.AddColumn (Combinartablas, "Fecha", each [Column1] & "/" & [Column2] & "/" & "2023"),
    Tipo = Table.TransformColumnTypes ( Fecha ,{"Fecha", type date} ),
    TablaSinColumnas = Table.RemoveColumns(Tipo, {"Column1", "Column2"}),
    Rename = Table.RenameColumns(TablaSinColumnas, {{"Column3", "Festivos"}})
in
    Rename
```
![](../img/12-resultado.png)


