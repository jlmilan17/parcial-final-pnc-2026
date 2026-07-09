# Jose Luis Hernandez Milan 00144723

## Indicaciones

Recientemente, se utilizó AI para crear un sistema de gestion de una biblioteca, el cual ha generado varios errores, su trabajo es arreglarlo. Dado el siguiente caso de uso, explique y/o resuelva cada problema según se le pida.

---

## Consideraciones

La libreria crea automaticamente un correo con los nombres de la persona

---

## Problemas

### 1. Filtro por autor y género (10%)

QA ha reportado que el endpoint para obtener los libros puede filtrar por **autor** y por **género**, o por cualquiera de los dos de manera individual.

Actualmente:

- Filtrar únicamente por autor funciona correctamente.
- Filtrar únicamente por género funciona correctamente.
- Filtrar por **autor y género al mismo tiempo** provoca que el servidor falle.

**Instrucción:** Explique la causa del problema y resuélvalo.

**Solución:** El método del repositorio estaba declarado como `findByAuthorAndGenre(String author, String genre)`, pero el campo genre de la entidad Book es un enum (Genre), no un string. 


---

### 2. Error al volver a prestar un libro (10%)

Un usuario reportó que al pedir prestado el libro **The Selfish Gene**, devolverlo e intentar pedirlo prestado nuevamente, el servidor falla.

**Instrucción:** Explique la causa del problema y resuélvalo.

**Solución:** The Selfish Gene tiene la cuenta en 1. Al prestarlo, baja a 0 y se marca como false. Al devolverlo, en el servicio solo se incrementaba la cuenta pero nunca se seteaba en true nuevamente.
---

### 3. Cantidad de libros por género (10%)

Existe un endpoint que devuelve la cantidad de libros disponibles por género. Sin embargo, actualmente dicho endpoint falla.

**Instrucción:** Explique la causa del problema y resuélvalo.

**Solución:** El libro "The Art of War" tiene el género en NULL. En el servicio para obtenerlos se recorría cada libro llamando directamente `book.getGenre().name()`; al encontrar un libro con género null,  el endpoint falla.

---

### 4. Error al consultar un libro por ID (10%)

Un miembro del equipo de frontend reporta que la siguiente llamada falla:

```http
GET /books?id=ed16ed1e-7017-4697-a08a-d28c09a74acf
```

**Instrucción:** Explique la causa del problema.

**Solución:** La llamada usa `id` como query param, pero en el controller la búsqueda por id está definida como path variable . 

---

### 5. Error al crear un libro (10%)

QA ha reportado que el siguiente payload enviado al endpoint `POST /books` provoca un error:

```json
{
  "title": "Clean Code",
  "author": "Robert C. Martin",
  "genre": "classic",
  "isbn": "978-0132350884",
  "available": true,
  "availableCount": 5
}
```

**Instrucción:** Explique la causa del problema.

**Solución:** El payload envía el género en minúsculas . En el servicio se convierte el texto a enum que distingue mayúsculas y minúsculas, y las constantes del enum están en mayúsculas (CLASSIC, ect). Por lo tanto, se produce el error. 

---

### 6. Devolución de libros no prestados (20%)

QA ha reportado que un usuario es capaz de devolver libros que nunca ha solicitado en préstamo.

**Instrucción:**

- Confirme si este comportamiento es realmente posible.
- Si es posible, explique la causa y resuelva el problema.
- Si no es posible, explique por qué, haciendo referencia al código correspondiente.

**Solución:**

Si, si es posible solo se incrementaba el stock y creaba el movimiento, sin verificar que el lector tuviera un préstamo pendiente de ese libro. 

Se corrigió agregando el método countByLectorAndBookAndType en el repositorio y, antes de aceptar la devolución, se comparan los préstamos y devoluciones de ese lector para ese libro.

---