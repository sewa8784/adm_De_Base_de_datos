# Rescatando la Base de Datos de TecnoStore

TecnoStore es una tienda online que ha crecido rápidamente durante el último año. Su sistema utiliza una base de datos para almacenar información de clientes, productos, ventas y usuarios. La base de datos funciona correctamente, pero fue construida rápidamente y sin aplicar estándares claros de administración. Actualmente existen objetos con nombres como:
* tabla1
* datosx
* clientes2026
* user1
* prueba
* indice1

Además, diferentes integrantes del equipo realizan modificaciones directamente sobre la base de datos
sin dejar registro de los cambios.
La empresa tampoco posee una estrategia formal de respaldo. Ocasionalmente un desarrollador
genera una copia de la base de datos y la guarda en el mismo servidor.
La gerencia está preocupada porque una modificación incorrecta o una falla del servidor podría
provocar pérdida de información.


# Parte 1: Organización y nomenclatura

Analice los siguientes nombres:
* tabla1  
* datosx  
* clientes2026 
* user1 
* prueba 
* indice1

Para cada uno:

• Indique qué problema presenta.

• Proponga un nombre más descriptivo.

• Explique brevemente por qué el nuevo nombre es mejor.

Utilice nombres claros, consistentes y que permitan comprender fácilmente el propósito del objeto.




## Parte 1: Organización y Nomenclatura

A continuaacion, se analizaron los nombres de existentes, se identifican sus problemas y se propuieron nombres adecuados.


| nombre actual | problema | nombre propuesto | justificacion |
| --- | --- | --- | --- |
| tabla1 | El nombre no describe que datos esta almacenando dicha tabla (es un nombre muy generico). | clientes | El nombre es claro y permite entender con claridad los datos que recibira la tabla. |
|datosx | En si el nombre es muy general y no se da a entender que "Datos" recibira en si la tabla. | productos | Refleja de manera clara que datos seran ingresados a la tabla, sin generar cnfusiones. |
| clientes2026 | Incluir el año sugiere que los datos sean especificos de ese año complicando asi su mantenimiento. | clientes | Se elimino el año para que dicha tabla sea duradera y no se tenga que hacer una nueva anualmente |
| user1 | **nombre muy generico**. El nombre no dice en si que rol esta cumpliendo dicho usuario  | usuarios_ventas | Define claramente el rol del usuario y a que funciones podra acceder. |
| prueba | **Problema** | **Nombre nuevo** | **justificacion**
| indice1 | **Problema**| **nombre nuevo** | **Justificacion** |

---


## Parte 2: Estrategia de Respaldo

Se propone la siguiente estrategia de respaldo para garantizar la recuperación de datos ante cualquier eventualidad.

- **¿Cada cuanto realizaria un respaldo?**
    * **Respaldo Completo:** Una vez a la semana (Cada dia viernes a las 7:00 PM).
    * **Respaldo Diferencial:** Diariamente, capturando los cambios desde el ultimo respaldo completo.
    * **Respaldo de Log de Transacciones:** Cada 4-6 horas para minimizar la perdida de datos en caso de fallo.

* **¿Donde almacenaria los respaldos?**
    * **Local:** En un servidor de archivos, en una ubicacion fisica diferente al servidor de la base de datos.
    * **Externo :** En un servicio de almacenamiento en la nube (ej. AWS S3, Azure Blob Storage, Google Cloud Storage) para garantizar la disponibilidad ante desastres.

- **¿Como identificaria cada archivo?**
    * Usar un formato de nombre consistente, los cuales incluyan:
        * **Tipo de respaldo:** FULL, DIFF, LOG
        * **Nombre de la base de datos:** TecnoStore
        * **Fecha y hora:** YYYY-MM-DD_HH-MM

- **¿Por que seroa importante probar periodicamente la restauracion?**
    - **Verificar la integridad de los datos:** Asegurar que los archivos de respaldo no esten corruptos.
    - **Validar el proceso:** Comprobar que el procedimiento de restauracion funciona correctamente.
    - **Medir el tiempo de recuperacion:** Conocer el tiempo que tomaria restaurar la base de datos, lo cual es critico para los objetivos de tiempo de recuperacion .

---


## Parte 3: Registro de Cambios (Bitacora)

Se registra el cambio de creacion de un nuevo usuario para el equipo de ventas.

| Campo | Información |
| --- | --- |
| **Fecha y hora** | 2026-08-17 14:55:00 |
| **Responsable** | sebastian  (Administrador de BD) |
| **Cambio realizado** | Creación de un nuevo usuario en la base de datos. |
| **Script/comando utilizado** | ```sql<br>CREATE USER 'usuario_ventas'@'%' IDENTIFIED BY 'ContraseñaSegura123!';<br>GRANT SELECT ON TecnoStore.* TO 'usuario_ventas'@'%';<br>``` |
| **Motivo del cambio** | El equipo de ventas necesita consultar información de la base de datos para su trabajo diario. Se requiere un usuario con permisos de solo lectura. |

---

## Parte 4: Situación Final

**Desarrollador:** *“Necesito modificar una tabla de producción ahora mismo. Es un cambio pequeño, así que no creo que sea necesario hacer respaldo ni registrarlo.”*

**Respuesta del DBA:**

- **¿Autorizaría el procedimiento?**
    - **No.** No se autoriza un cambio en producción sin seguir los procedimientos establecidos.

- **¿Qué debería realizar antes del cambio?**
    1.  **Crear un respaldo:** Realizar un respaldo de la base de datos o de la tabla específica a modificar.
    2.  **Documentar el cambio:** Registrar el cambio en la bitácora, detallando la modificación, el motivo y quién la realiza.
    3.  **Probar en un entorno de desarrollo/pruebas:** El cambio debe ser verificado en un entorno que no sea producción para asegurar que no cause efectos secundarios no deseados.
    4.  **Redactar el script de cambio:** Escribir el script SQL que se ejecutará, incluyendo un `BEGIN TRANSACTION` para poder deshacer el cambio en caso de error.

- **¿Qué debería registrar después?**
    1.  **Script de cambio ejecutado:** Guardar el script exacto que se utilizó.
    2.  **Resultado de la ejecución:** Indicar si fue exitoso y cualquier detalle relevante (ej. número de filas afectadas).
    3.  **Script de reversión (Rollback):** Registrar el script que permitiría deshacer el cambio si fuera necesario.

- **Justifique brevemente su decisión.**
    Incluso un cambio "pequeño" puede tener consecuencias inesperadas y provocar la pérdida o corrupción de datos en producción. Los procedimientos de respaldo, registro y pruebas no son opcionales, sino medidas de seguridad esenciales para garantizar la estabilidad y la integridad de la base de datos, así como para mantener una trazabilidad que permita auditar y revertir cambios si es necesario.

---
