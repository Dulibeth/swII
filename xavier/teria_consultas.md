## El operador $expr en MongoDB

El operador `$expr` de MongoDB te permite crear expresiones de consulta más complejas y flexibles utilizando operadores de comparación, lógica y aritmética. Es particularmente útil para comparar campos del mismo documento o para realizar cálculos con valores de campo.

**Sintaxis básica:**

```
$expr: { <expresión> }
```

Donde `<expresión>` puede ser una combinación de operadores, valores de campo, literales y variables.

**Casos de uso:**

* **Comparar campos del mismo documento:**

Supongamos que tenemos una colección de empleados con campos `edad` y `llegadasTarde`. Usando `$expr`, podemos obtener aquellos empleados cuya edad sea igual a su cantidad de llegadas tarde:

```
db.empleados.aggregate([
  {
    $match: {
      $expr: { $eq: ['$edad', '$llegadasTarde'] }
    }
  }
])
```

* **Realizar cálculos con valores de campo:**

Podemos calcular el salario bruto mensual de un empleado multiplicando su salario por hora por la cantidad de horas trabajadas al mes:

```
db.empleados.aggregate([
  {
    $addFields: {
      "salarioBrutoMensual": {
        $expr: { $mul: ['$salarioPorHora', '$horasTrabajadasMes'] }
      }
    }
  }
])
```

* **Utilizar variables:**

Las variables permiten reutilizar expresiones o valores en diferentes partes de la consulta. Por ejemplo, podemos calcular el IMC (Índice de Masa Corporal) de un individuo utilizando su peso y altura:

```
db.individuos.aggregate([
  {
    $addFields: {
      "imc": {
        $expr: {
          $divide: [
            "$peso",
            { $pow: ["$altura", 2] }
          ]
        }
      }
    }
  }
])
```

**Ejemplos adicionales:**

* Obtener la diferencia de edad entre dos empleados:

```
db.empleados.aggregate([
  {
    $lookup: {
      from: "empleados",
      localField: "compañeroId",
      foreignField: "_id",
      as: "compañero"
    }
  },
  {
    $unwind: "$compañero"
  },
  {
    $addFields: {
      "diferenciaEdad": {
        $expr: { $subtract: ["$edad", "$compañero.edad"] }
      }
    }
  },
  {
    $match: {
      "diferenciaEdad": { $gt: 0 }
    }
  },
  {
    $project: {
      _id: 0,
      nombre: 1,
      edad: 1,
      "compañero.nombre": 1,
      "compañero.edad": 1,
      "diferenciaEdad": 1
    }
  }
])
```

* Obtener la distancia entre dos puntos utilizando la fórmula de Haversine:

```
db.lugares.aggregate([
  {
    $geoNear: {
      near: {
        type: "Point",
        coordinates: [-74.0060, 40.7128] // Coordenadas de Nueva York
      },
      distanceField: "distancia"
    }
  },
  {
    $addFields: {
      "distanciaKM": {
        $expr: { $multiply: ["$distancia", 0.00621371] } // Convertir a kilómetros
      }
    }
  },
  {
    $project: {
      _id: 0,
      nombre: 1,
      direccion: 1,
      "distanciaKM": 1
    }
  }
])
```

## Operadores de matriz en MongoDB (Basado en la página oficial de MongoDB)

Los operadores de matriz en MongoDB te permiten trabajar con arrays de manera eficiente dentro de tus consultas. Estos operadores te ayudan a filtrar, modificar y agregar elementos a arrays de forma sencilla.

### Operadores principales:

**1. $all:**

* Busca documentos donde el array contenga **todos** los elementos especificados.
* Sintaxis: `{ campo: { $all: [valor1, valor2, ...] } }`

**Ejemplo:** Encontrar documentos donde el array "colores" contenga "rojo" y "azul".

```
db.coleccion.find({ colores: { $all: ["rojo", "azul"] } })
```

**2. $elemMatch:**

* Busca documentos donde el array contenga **al menos un elemento** que coincida con la condición especificada.
* Sintaxis: `{ campo: { $elemMatch: { <condición> } } }`

**Ejemplo:** Encontrar documentos donde el array "frutas" contenga al menos una fruta que sea "cítrica".

```
db.coleccion.find({ frutas: { $elemMatch: { tipo: "cítrica" } } })
```

**3. $size:**

* Obtiene el tamaño (número de elementos) de un array.
* Sintaxis: `{ campo: { $size: <expresión> } }`

**Ejemplo:** Contar el número de documentos donde el array "numeros" tiene 5 elementos.

```
db.coleccion.find({ numeros: { $size: 5 } })
```

### Otros operadores importantes:

* **$push:** Agrega uno o más elementos al final de un array.
* **$addToSet:** Agrega un elemento al array si no existe previamente.
* **$pop:** Elimina el último elemento de un array.
* **$pull:** Elimina del array todas las instancias del valor especificado.
* **$unshift:** Agrega uno o más elementos al inicio de un array.
* **$slice:** Extrae un subconjunto de elementos del array.

## Proyecciones en MongoDB (Basado en la página oficial de MongoDB)

Las proyecciones en MongoDB te permiten especificar qué campos deseas incluir o excluir en los resultados de una consulta. Esto es útil para reducir la cantidad de datos devueltos y mejorar el rendimiento, especialmente cuando solo necesitas un subconjunto de información.

### Sintaxis básica:

```
db.coleccion.find({ <condición> }, { <proyección> })
```

* **`{}`:** Representa la inclusión o exclusión de campos.
* **1:** Indica la inclusión de todos los campos.
* **0:** Indica la exclusión de todos los campos.
* **NombreDelCampo:** Incluye el campo especificado.
* **-NombreDelCampo:** Excluye el campo especificado.

### Ejemplos:

**1. Incluir campos específicos:**

Seleccionar solo los campos "nombre" y "edad" de todos los documentos:

```
db.coleccion.find({}, { nombre: 1, edad: 1 })
```

**2. Excluir campos específicos:**

Excluir los campos "_id" y "direccion" de todos los documentos:

```
db.coleccion.find({}, { _id: 0, direccion: 0 })
```

**3. Incluir y excluir campos:**

Seleccionar los campos "nombre", "edad" y "ciudad", excluyendo el campo "_id":

```
db.coleccion.find({}, { _id: 0, nombre: 1, edad: 1, ciudad: 1 })
```

**4. Renombrar campos:**

Cambiar el nombre del campo "edad" a "edad_años" en los resultados:

```
db.coleccion.find({}, { edad: "edad_años" })
```

**5. Anidar proyecciones para subdocumentos:**

Proyectar solo el campo "tipo" del subdocumento "telefono" en los resultados:

```
db.coleccion.find({}, { telefono: { tipo: 1 } })
```

### Operadores de proyección:

* **$slice:** Limitar el número de elementos en un array.
* **$elemMatch:** Filtrar elementos dentro de un array.
* **$cond:** Aplicar una condición para incluir o excluir campos.
* **$meta:** Incluir información de metadatos sobre la consulta.

## Agregaciones en MongoDB (Basado en la página oficial de MongoDB)

El framework de agregaciones de MongoDB te permite realizar operaciones de transformación de datos más complejas y sofisticadas que las simples consultas con `find()`. 

Las agregaciones se ejecutan en etapas, denominadas "pasos", que procesan los datos de un conjunto de documentos y los transforman en un nuevo resultado. 

### Pasos principales:

1. **$match:** Filtra los documentos que cumplen con la condición especificada.
2. **$project:** Selecciona o excluye campos de los documentos.
3. **$unwind:** Descomprime arrays en documentos separados.
4. **$group:** Agrupa documentos según un criterio y realiza cálculos.
5. **$sort:** Ordena los documentos según un campo o campos específicos.
6. **$limit:** Limita el número de documentos devueltos.
7. **$skip:** Omite un número especificado de documentos al principio.

### Sintaxis básica:

```
db.coleccion.aggregate([
  { $paso1: { <parámetros> } },
  { $paso2: { <parámetros> } },
  ...,
  { $ultimoPaso: { <parámetros> } }
])
```

### Ejemplos:

**1. Calcular el salario total por departamento:**

```
db.empleados.aggregate([
  { $group: { _id: "$departamento", salarioTotal: { $sum: "$salario" } } },
  { $project: { _id: 1, departamento: 1, salarioTotal: 1 } }
])
```

**2. Encontrar el producto con mayor venta por categoría:**

```
db.productos.aggregate([
  { $unwind: "$ventas" },
  { $group: { _id: { $concat: ["$categoria", "-", "$producto"] }, ventasTotales: { $sum: "$ventas.cantidad" } } },
  { $sort: { ventasTotales: -1 } },
  { $limit: 1 },
  { $project: { _id: { $split: ["$_id", "-"] }, ventasTotales: 1 } }
])
```

**3. Obtener la distribución de usuarios por edad en rangos:**

```
db.usuarios.aggregate([
  { $group: { _id: { $bucket: { in: "$edad", boundaries: [0, 18, 30, 45, 60], includeUpper: true } }, total: { $count: 1 } } },
  { $project: { _id: "$_id.groupBy", total: 1 } }
])
```

## Ejemplo de una agregación simple en MongoDB

**Objetivo:** Calcular el promedio de la edad de los usuarios en la colección `db.usuarios`.

**Pipeline de agregación:**

```
db.usuarios.aggregate([
  { $group: { _id: null, promedioEdad: { $avg: "$edad" } } },
  { $project: { _id: 0, promedioEdad: 1 } }
])
```

**Explicación:**

1. **Etapa 1: $group:**
   * **`_id`:** Se establece en `null` ya que no se requiere agrupar por ningún campo.
   * **`promedioEdad`:** Se calcula el promedio de la edad de todos los usuarios utilizando el operador `$avg`.

2. **Etapa 2: $project:**
   * **`_id`:** Se elimina el campo `_id` generado en la etapa anterior.
   * **`promedioEdad`:** Se mantiene el campo `promedioEdad` que contiene el valor promedio calculado.

**Resultado esperado:**

```json
[
  { "promedioEdad": 32.5 } // Ejemplo de valor promedio
]
```
