# Maestría en Ciencia de Datos - Ejercicio 2

En este ejercicio se interactua con la BBDD.

1. Crear un directorio para almacenar los archivos de MySQL:

```bash

mkdir mysql_data
``` 
2. Ejecutar el siguiente comando para crear y ejecutar el contenedor de MySQL:

```bash

docker run -d \
  --name my-mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v ./mysql_data:/var/lib/mysql \
  mysql:latest
```


Explicación de las opciones utilizadas: 
- `-d` ejecuta el contenedor en segundo plano. 
- `--name` asigna un nombre al contenedor, en este caso "mysql-container". 
- `-p` asigna el puerto del host al puerto del contenedor, en este caso el puerto 3306. 
- `-e` establece una variable de entorno, en este caso la contraseña de root de MySQL. 
- `-v` monta el directorio `/ruta/a/tu/mysql_data` en el directorio `/var/lib/mysql` dentro del contenedor. 
- `mysql:latest` especifica la imagen que se usará para crear el contenedor. 
3. Verificar que el contenedor se esté ejecutando correctamente:

```bash

docker ps
```



Deberías ver una salida similar a la siguiente:

```bash

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
48a1af3d3c8e        mysql:latest        "docker-entrypoint.s…"   10 seconds ago      Up 9 seconds        0.0.0.0:3306->3306/tcp   mysql-container
```



Si ves la columna STATUS como "Up", significa que el contenedor se está ejecutando correctamente.

¡Listo! Ahora tienes un contenedor de MySQL en ejecución y listo para ser utilizado, el cual está montando un directorio para almacenar los archivos de datos y exponiendo el puerto 3306 al host.

## Conectandose al contenedor 

- Para conectarte al contenedor de MySQL y crear la base de datos y las tablas, puedes seguir los siguientes pasos: 
1. Conéctate al contenedor utilizando el siguiente comando:

```bash

docker exec -it mysql-container mysql -u root -p
```



Esto iniciará una sesión interactiva en el contenedor y te pedirá la contraseña que especificaste al crear el contenedor (en este ejemplo, `123456`). 
2. Crea la base de datos `store` utilizando el siguiente comando:

```sql

CREATE DATABASE store;
``` 
3. Selecciona la base de datos `store`:

```

USE store;
``` 
4. Crea la tabla `book` con los siguientes atributos:

```sql

CREATE TABLE book (
  book_id INT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  release_date DATE NOT NULL,
  quantity INT NOT NULL,
  price DECIMAL(10,2) NOT NULL
);
``` 
5. Crea la tabla `author` con los siguientes atributos:

```sql

CREATE TABLE author (
  author_id INT PRIMARY KEY,
  first_name VARCHAR(255) NOT NULL,
  last_name VARCHAR(255) NOT NULL,
  birth_date DATE NOT NULL
);
``` 
6. Crea la tabla `book_author` que relaciona la tabla `book` y la tabla `author`:

```sql

CREATE TABLE book_author (
  book_id INT NOT NULL,
  author_id INT NOT NULL,
  PRIMARY KEY (book_id, author_id),
  FOREIGN KEY (book_id) REFERENCES book(book_id),
  FOREIGN KEY (author_id) REFERENCES author(author_id)
);
```

¡Listo! Ahora tienes una base de datos `store` con tres tablas: `book`, `author` y `book_author`.

## Llenando la BBDD de ejemplos.

Para llenar las tablas `book`, `author` y `book_author` con algunos ejemplos, puedes seguir los siguientes pasos: 
1. Conéctate al contenedor utilizando el siguiente comando:

```bash

docker exec -it mysql-container mysql -u root -p
```

Esto iniciará una sesión interactiva en el contenedor y te pedirá la contraseña que especificaste al crear el contenedor (en este ejemplo, `secretpassword`). 
2. Selecciona la base de datos `store`:

```

USE store;
``` 
3. Inserta algunos datos en la tabla `book`. En este ejemplo, insertaremos 10 libros:

```sql

INSERT INTO book (book_id, name, release_date, quantity, price) VALUES
  (1, 'The Catcher in the Rye', '1951-07-16', 5, 10.99),
  (2, 'To Kill a Mockingbird', '1960-07-11', 8, 12.99),
  (3, '1984', '1949-06-08', 3, 15.99),
  (4, 'The Great Gatsby', '1925-04-10', 2, 9.99),
  (5, 'One Hundred Years of Solitude', '1967-05-30', 6, 11.99),
  (6, 'Beloved', '1987-09-02', 4, 14.99),
  (7, 'Brave New World', '1932-06-18', 7, 13.99),
  (8, 'Slaughterhouse-Five', '1969-03-31', 9, 12.99),
  (9, 'The Grapes of Wrath', '1939-04-14', 3, 11.99),
  (10, 'For Whom the Bell Tolls', '1940-10-21', 1, 17.99);
``` 
4. Inserta algunos datos en la tabla `author`. En este ejemplo, insertaremos 6 autores:

```sql

INSERT INTO author (author_id, first_name, last_name, birth_date) VALUES
  (1, 'J.D.', 'Salinger', '1919-01-01'),
  (2, 'Harper', 'Lee', '1926-04-28'),
  (3, 'George', 'Orwell', '1903-06-25'),
  (4, 'F. Scott', 'Fitzgerald', '1896-09-24'),
  (5, 'Gabriel', 'García Márquez', '1927-03-06'),
  (6, 'Toni', 'Morrison', '1931-02-18');
``` 
5. Inserta algunos datos en la tabla `book_author`. En este ejemplo, relacionaremos algunos autores con los libros insertados anteriormente:

```sql

INSERT INTO book_author (book_id, author_id) VALUES
  (1, 1), (2, 2), (3, 3), (4, 4), (5, 5), (6, 6), (7, 3), (8, 1), (9, 4), (10, 1),
  (1, 2), (2, 3), (3, 1), (4, 1), (5, 6), (6, 5), (7, 4), (8, 2), (9, 5), (10, 4), (10, 2);
```

Con esto, hemos insertado algunos datos de ejemplo en las tablas `book`, `author` y `book_author` para tener 10 libros con varios autores asociados. Ahora puedes verificar que los datos se hayan insertado correctamente ejecutando consultas SELECT en cada una de las tablas. Por ejemplo:


``` sql

SELECT * FROM book;

```

Debería mostrar una salida similar a la siguiente:
```



+---------+---------------------------+--------------+----------+-------+
| book_id | name | release_date | quantity | price |
+---------+---------------------------+--------------+----------+-------+
| 1 | The Catcher in the Rye | 1951-07-16 | 5 | 10.99 |
| 2 | To Kill a Mockingbird | 1960-07-11 | 8 | 12.99 |
| 3 | 1984 | 1949-06-08 | 3 | 15.99 |
| 4 | The Great Gatsby | 1925-04-10 | 2 | 9.99 |
| 5 | One Hundred Years of Solitude | 1967-05-30 | 6 | 11.99 |
| 6 | Beloved | 1987-09-02 | 4 | 14.99 |
| 7 | Brave New World | 1932-06-18 | 7 | 13.99 |
| 8 | Slaughterhouse-Five | 1969-03-31 | 9 | 12.99 |
| 9 | The Grapes of Wrath | 1939-04-14 | 3 | 11.99 |
| 10 | For Whom the Bell Tolls | 1940-10-21 | 1 | 17.99 |
+---------+---------------------------+--------------+----------+-------+

```