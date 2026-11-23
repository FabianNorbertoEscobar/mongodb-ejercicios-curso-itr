# Prácticas de MongoDB

Ejercicios de un curso de MongoDB centrados en el modelado y la consulta de datos de facturación. El repositorio reúne apuntes en JavaScript para la consola de MongoDB y material de estudio; no es una aplicación ni una API.

## Stack y herramientas

- **MongoDB**: base de datos documental NoSQL, con colecciones y documentos anidados.
- **JavaScript en la shell de MongoDB**: consultas, actualizaciones, funciones y recorridos de cursores.
- **MongoDB Database Tools**: `mongoimport` permite cargar los datos de práctica.
- **Material del curso**: presentaciones, enunciados, soluciones y guías de instalación en [`archivos/`](archivos/).

El material incluye una guía para MongoDB 4.2 y los ejercicios usan comandos de la shell clásica `mongo`. Versiones actuales de MongoDB y `mongosh` pueden requerir cambios en algunos ejemplos, especialmente en métodos antiguos como `insert`, `update`, `remove` y `save`, así como en consultas geoespaciales y la estructura de los resultados de `explain()`. Consultá la documentación de la versión que uses.

## Contenido y temas practicados

| Archivo | Prácticas |
| --- | --- |
| [Clase 2: consultas](ejercicios%20clase%202%20consultas.js) | Filtros por fechas y rangos, campos anidados, proyecciones, ordenamiento, paginación y expresiones regulares. |
| [Clase 3: consultas y CRUD](ejercicios%20clase%203%20consultas.js) | Búsquedas, inserciones, eliminaciones y actualizaciones con `$set` y `$inc`. |
| [Clase 4: arrays](ejercicios%20clase%204%20operaciones%20sobre%20arrays.js) | Modificación de ítems con `$push`, `$pop`, `$pull` y `$each`. |
| [Clase 5: índices](ejercicios%20clase%205%20%C3%ADndices.js) | Índices únicos, compuestos, de texto, TTL y geoespaciales; planes de consulta con `explain()` y selección de índices con `hint()`. |
| [Clase 6: funciones](ejercicios%20clase%206%20funciones.js) | Funciones JavaScript, cursores, operaciones con `upsert`, funciones almacenadas y generación de secuencias con `findAndModify`. |
| [Clase 7: aggregation framework](ejercicios%20clase%207%20aggregation%20framework.js) | Pipelines con `$match`, `$project`, `$unwind`, `$group`, `$sort`, `$skip` y `$limit`; acumulaciones y cálculos de ingresos. |

Entre los problemas abordados se encuentran la búsqueda de facturas por cliente, región o producto; la modificación de sus ítems; la comparación de consultas con y sin índices; y el cálculo de cantidades e ingresos por región o producto.

## Modelo de datos

La colección principal es `facturas`. Los ejemplos consultan datos como `nroFactura`, `fechaEmision`, `fechaVencimiento` y `condPago`, junto con un documento `cliente` y un array `item` de productos:

```javascript
{
  nroFactura: 1000,
  fechaEmision: ISODate("2014-02-24T00:00:00Z"),
  condPago: "CONTADO",
  cliente: { nombre: "Ana", apellido: "Pérez", region: "CENTRO" },
  item: [{ producto: "CORREA 10mm", cantidad: 2, precio: 10 }]
}
```

Es un ejemplo ilustrativo, no un esquema de validación. Según el ejercicio también se utilizan `items`, `clientes`, `solicitudes`, `secuencias` y `comercios`. Algunas se crean o completan durante la práctica; para los ejemplos geoespaciales se necesitan además datos de comercios con ubicaciones compatibles.

## Preparación

1. Instalá MongoDB, una shell compatible (`mongo` o `mongosh`) y las [MongoDB Database Tools](https://www.mongodb.com/docs/database-tools/installation/installation/) si vas a importar los datos. Iniciá el servidor y conectate a una instancia local de práctica.
2. Extraé [`archivos/facturas (1).rar`](archivos/facturas%20%281%29.rar) con una herramienta compatible con RAR. El archivo contiene `facturas.json`. Revisá su formato antes de importarlo.
3. Importá el JSON en una base **descartable**, por ejemplo `practica_mongodb`. Ejecutá el siguiente comando desde la terminal del sistema, reemplazando la ruta por la ubicación del archivo extraído:

   ```bash
   mongoimport --db practica_mongodb --collection facturas --file "/ruta/a/facturas.json"
   ```

   Si el archivo contiene un único array JSON, agregá `--jsonArray`. Si tu servidor requiere una conexión o autenticación distinta de la local predeterminada, indicá los parámetros correspondientes a `mongoimport`.
4. Abrí la shell, seleccioná la base y comprobá la carga:

   ```javascript
   use practica_mongodb
   db.facturas.countDocuments({})
   db.facturas.findOne()
   ```

Revisá también que las fechas se hayan importado como fechas BSON: los filtros con `ISODate(...)` no se comportan igual si las fechas quedaron como cadenas. Los ejemplos asumen campos y valores específicos del conjunto de datos; con otros datos puede ser necesario adaptar las consultas.

## Cómo usar los ejercicios

Los archivos están organizados por clase y por inciso. **No están preparados para ejecutarse completos como scripts**: incluyen rótulos sueltos (`a`, `b`, números), métodos antiguos y algunos fragmentos con errores de sintaxis o lógica. Leé cada consigna en el material disponible, seleccioná el fragmento que quieras estudiar y adaptalo antes de ejecutarlo en la shell. Por ejemplo:

```javascript
db.facturas.find(
  { "cliente.region": "CENTRO" },
  { _id: 0, nroFactura: 1, cliente: 1 }
).sort({ nroFactura: 1 }).limit(5)
```

**Trabajá sobre una copia de los datos.** Hay ejercicios que insertan, modifican o eliminan documentos, crean índices o cambian varias colecciones. El índice TTL de `solicitudes` puede eliminar documentos automáticamente; un índice único puede fallar si hay valores duplicados. Algunas prácticas dependen de datos o cambios realizados en incisos anteriores, por lo que conviene revisar su estado y restaurar la colección cuando sea necesario.

## Material complementario

La carpeta [`archivos/`](archivos/) incluye introducciones a Big Data, NoSQL y bases documentales; guías de instalación; presentaciones de consultas y operaciones; y enunciados y soluciones de prácticas CRUD y modelado de facturas. Son materiales de apoyo del curso y pueden describir versiones anteriores de MongoDB.
