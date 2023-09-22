# MongoDB Commands

## Operadores de Consulta y Proyección

1.  `$eq`: Coincide con los valores que son iguales a un valor especificado.
2.  `$gt`: Coincide con los valores que son mayores que un valor especificado.
3.  `$gte`: Coincide con valores que son mayores o iguales a un valor especificado.
4.  `$in`: Coincide con cualquiera de los valores especificados en un array.
5.  `$lt`: Coincide con los valores que son menores que un valor especificado.
6.  `$lte`: Coincide con valores que son menores o iguales a un valor especificado.
7.  `$ne`: Coincide con todos los valores que no son iguales a un valor especificado.
8.  `$nin`: Coincide con ninguno de los valores especificados en un array.
9.  `$and`: Une cláusulas de consulta con un AND lógico devuelve todos los documentos que coinciden con las condiciones de ambas cláusulas.
10. `$not`: Invierte el efecto de una expresión de consulta y devuelve los documentos que no coinciden con la expresión de consulta.
11. `$nor`: Une cláusulas de consulta con un NOR lógico devuelve todos los documentos que no cumplen con ambas cláusulas.
12. `$or`: Une cláusulas de consulta con un OR lógico devuelve todos los documentos que cumplen las condiciones de cualquiera de las cláusulas.
13. `$exists`: Coincide con los documentos que tienen el campo especificado.
14. `$type`: Selecciona documentos si un campo es del tipo especificado.
15. `$expr`: Permite el uso de expresiones de agregación dentro del lenguaje de consulta.
16. `$jsonSchema`: Valida los documentos en contra del esquema JSON proporcionado.
17. `$mod`: Realiza una operación de módulo en el valor de un campo y selecciona los documentos con un resultado especificado.
18. `$text`: Realiza una búsqueda de texto.
19. `$where`: Evalúa una expresión JavaScript y devuelve los documentos que cumplen la condición.

## Operadores de Consulta de Arrays

20. `$all`: Coincide con los arrays que contienen todos los elementos especificados en la consulta.
21. `$elemMatch`: Selecciona documentos si un elemento en el campo de array coincide con todas las condiciones especificadas en `$elemMatch`.
22. `$size`: Selecciona documentos si el campo de array es de un tamaño especificado.

## Operadores de Consulta Usando Expresiones Regulares

23. `$regex`: Selecciona documentos donde los valores coinciden con una expresión regular especificada.

## Operadores de Consulta Geoespaciales

24. `$geoWithin`: Selecciona objetos geoespaciales que se ajustan dentro de un polígono especificado.
25. `$geoIntersects`: Selecciona objetos geoespaciales que se intersectan con un polígono especificado.
26. `$near`: Devuelve objetos geoespaciales en proximidad a un punto. Requiere un índice geoespacial. El índice 2d no es compatible con las opciones de consulta asociadas.
27. `$nearSphere`: Devuelve objetos geoespaciales en proximidad a un punto en una esfera. Requiere un índice geoespacial. El índice 2d no es compatible con las opciones de consulta asociadas.
28. `$box`: Selecciona documentos con objetos geoespaciales que se ajustan dentro de la caja geométrica.
29. `$center`: Selecciona documentos con objetos geoespaciales que se ajustan dentro del círculo.
30. `$centerSphere`: Selecciona documentos con objetos geoespaciales que se ajustan dentro del círculo en una esfera.
31. `$maxDistance`: Restringe los resultados por distancia, en grados, para $near y $nearSphere.
32. `$polygon`: Selecciona documentos con objetos geoespaciales que se ajustan dentro del polígono.
33. `$uniqueDocs`: Ignorar. MongoDB descontinuó este operador en la versión 2.6.

db.sucursal.aggregate([
    {
        $lookup: {
          from: "sucursal_automovil",
          localField: "ID_Sucursal",
          foreignField: "ID_Sucursal",
          as: "data_Sucursal"
        }
    },
    {
        $unwind: "$data_Sucursal"
    },
    {
        $group: {
          _id: "$data_Sucursal.ID_Sucursal",
          total_Automoviles: {$sum: "$data_Sucursal.Cantidad_Disponible"}
        }
    },
    {
        $project: {
          "_id": 0,
          "ID_Sucursal": "$_id",
          "Nombre": 1,
          "Total_Automoviles": "$total_Automoviles"
        }
    },
    { $sort: { ID_Sucursal: 1 } }
]);
## Desglose de la consulta

1. **`$lookup`**: Esta es una operación de unión que se realiza entre las colecciones 'sucursal' y 'sucursal_automovil'. La unión se realiza en el campo 'ID_Sucursal'. El resultado de la unión se almacena en un nuevo campo llamado 'data_Sucursal' en la colección 'sucursal'.
   
2. **`$unwind`**: Esta operación descompone el campo 'data_Sucursal' que es un array de documentos en documentos individuales. Cada documento ahora tiene un campo 'data_Sucursal' que es un documento en lugar de un array de documentos.
   
3. **`$group`**: Esta operación agrupa los documentos por el campo 'ID_Sucursal' en 'data_Sucursal' y calcula la suma de 'Cantidad_Disponible' para cada 'ID_Sucursal'. El resultado se almacena en un nuevo campo llamado 'total_Automoviles'.
   
4. **`$project`**: Esta operación selecciona los campos a incluir en el resultado final. En este caso, se incluyen los campos 'ID_Sucursal', 'Nombre' y 'Total_Automoviles'. El campo '_id' se excluye del resultado final.
   
5. **`$sort`**: Esta operación ordena los documentos en el resultado final por 'ID_Sucursal' en orden ascendente.

db.automovil.aggregate([
    {
        $lookup: {
          from: "alquiler",
          localField: "ID_Automovil",
          foreignField: "ID_Automovil_id",
          as: "data_Alquiler"
        }
    },
    {
        $match: {
          "data_Alquiler.Estado": "Disponible",
          "Capacidad": { $gte: 5 }
        }
    },
    {
        $project: {
          "_id": 0,
          "data_Alquiler._id": 0,
          "data_Alquiler.ID_Automovil_id": 0
        }
    }
]);
### Desglose de la Consulta 2

1. **`$lookup`**: Esta operación realiza una unión entre las colecciones 'automovil' y 'alquiler'. La unión se realiza en el campo 'ID_Automovil'. El resultado de la unión se guarda en un nuevo campo llamado 'data_Alquiler' en la colección 'automovil'.

2. **`$match`**: Esta operación filtra los documentos según las condiciones especificadas. En este caso, filtra los documentos donde el campo 'data_Alquiler.Estado' tiene el valor "Disponible" y el campo 'Capacidad' es mayor o igual a 5.

3. **`$project`**: Esta operación selecciona los campos que se incluirán en el resultado final. En este caso, se excluye el campo '_id' del resultado y los campos del subdocumento 'data_Alquiler'.

Esta consulta devuelve una lista de automóviles disponibles con una capacidad de 5 o más, excluyendo los campos innecesarios del resultado.


# Manejo de inicio de sesión y roles en MongoDB

MongoDB permite la creación de usuarios y roles para gestionar el acceso a la base de datos. A continuación, se detalla cómo se pueden manejar estos aspectos.

## Creación de usuarios

Para crear un usuario en MongoDB, se utiliza el método `db.createUser()`. Este método toma un documento que especifica el nombre de usuario, la contraseña y los roles. Aquí hay un ejemplo de cómo crear un usuario sin ningún rol:

```javascript
db.createUser({
    user: "tom",
    pwd: passwordPrompt(),
    roles: []
})
```

Para ver los usuarios existentes, puedes usar el método `db.getUsers()`.

## Creación de roles

Los roles en MongoDB pueden ser roles incorporados o roles definidos por el usuario. Un rol incorporado es un rol predefinido que viene con MongoDB. Un rol definido por el usuario es un rol creado por el usuario.

Para crear un rol, puedes usar el método `db.createRole()`. Este método toma un documento que especifica el nombre del rol, los privilegios y los roles heredados. Aquí hay un ejemplo de cómo crear un rol:

```javascript
db.createRole({
    role: "manageOpRole",
    privileges: [
        { resource: { anyResource: true }, actions: [ "anyAction" ] }
    ],
    roles: []
})
```

Para ver los roles existentes, puedes usar el método `db.getRoles()`.

## Asignación de roles a usuarios

Para asignar un rol a un usuario, puedes usar el método `db.grantRolesToUser()`. Este método toma el nombre del usuario y una matriz de roles. Aquí hay un ejemplo de cómo asignar un rol a un usuario:

```javascript
db.grantRolesToUser(
    "tom",
    [ "manageOpRole" ]
)
```

Para ver los roles asignados a un usuario, puedes usar el método `db.getUser()`.

## Control de acceso basado en roles (RBAC)

MongoDB utiliza un modelo de control de acceso basado en roles (RBAC). En este sistema, diferentes niveles de acceso están asociados con roles individuales. Para dar permiso a un usuario para realizar una acción, le concedes la pertenencia a un rol que tiene los privilegios apropiados.