# practica-1.3

# Despliegue de una aplicación web LAMP sencilla

##  1. Introducción
En esta practica realizaremos el despliegue de una aplicación web  de la pila LAMP (Linux, Apache, MySQL y PHP), utilizando el script realizado en la practica 1, en una instancia EC2 de AWS con Ubuntu Server. Automatizaremos la instalación de la pila LAMP y la configuración de la aplicación web mediante scripts de Bash.

## 2.Creacion de una instancia EC2 en AWS e instalacion de Pila LAMP
Para la reaizacion de ete apartado seguiremos los pasos detallados en la practica-iaw-1.1 y utilizaremos el script ``` install_lamp.sh ```.

  ![XAFmFL0REO](https://github.com/user-attachments/assets/c18ab7e9-c095-429e-a1f3-16270a55b96e)


[Practica-iaw-1.1](https://github.com/marinaferb92/practica-iaw-1.1/tree/main)
[Script Install LAMP](https://github.com/marinaferb92/practica-iaw-1.1/blob/main/scripts/install_lamp.sh)

Una vez hecho esto nos aseguraremos de que la Pila LAMP esta funcionando correctamente.

- Verificaremos el estado de apache.

  ![zFbjTnu4bO](https://github.com/user-attachments/assets/0794dca3-3187-4728-89ae-d8b5ab3485b9)


- Entramos en mysql desde la terminal para ver que esta corriendo.

  ![tTzfzKIiQm](https://github.com/user-attachments/assets/48160a85-c2c8-4183-9e7c-8926f5d1c8d4)


- Verificamos la instalacion de PHP

  ![HtI9KkSHKA](https://github.com/user-attachments/assets/3f6ecffa-756a-4ccb-bf96-e16e1e85bf7e)
  ![q4jHqHBlrs](https://github.com/user-attachments/assets/435ca553-17d9-4a61-9376-85a0e937c1f9)


## 3. Despliegue de la aplicacion web

Para el despliegue de la aplicacion web crearemos un nuevo script al que llamaremos ``` deploy.sh ``` . En el escribiremos los comandos necesarios para la correcta instalación de esta.

1. Cargamos el archivo de variables
   
El primer paso de nuestro script sera crear un archivo de variable ``` . env ``` donde iremos definiendo las diferentes variables que necesitemos, y cargarlo en el entorno del script.

``` source.env ```

2. Configuramos el script
   
Configuraremos el script para que en caso de que haya errores en algun comando este se detenga ```-e```, ademas de que para que nos muestre los comando antes de ejecutarlos ```-x```.

``` set -ex ```

3. Eliminamos clonados previos de la aplicación

Eliminaremos cualquier directorio llamado ``` iaw-practica-lamp ``` que exista en ``` /tmp ``` asegurandonos de que no haya una previa en el sistema antes de clonar una nueva.

``` git clone https://github.com/josejuansanchez/iaw-practica-lamp.git /tmp/iaw-practica-lamp ```

4. Clonamos el repositorio de la aplicacion en /tmp
Clonamos el repositorio de GitHub y el codigo fuente de la aplicación se descarga en ```/tmp/iaw-practica-lamp```
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git /tmp/iaw-practica-lamp

5. Movemos el codigo fuente de la aplicacion a /var/www/html

Mueve todos los archivos que haya en el directorio **src** del repositorio que hemos clonado antes, a la carpeta ```/var/www/html``` .

```mv /tmp/iaw-practica-lamp/src/* /var/www/html```


6. Configuramos el archivo config.php
   
Con ```sed -i``` usamos el editor de flujos sed para buscar y reemplazar texto en el archivo ```config.php```.
- La primera línea reemplaza **database_name_here** con el valor de la variable **$DB_NAME**.
- La segunda línea reemplaza **username_here** con **$DB_USER**.
- La tercera línea reemplaza **password_here** con **$DB_PASSWORD**.

Estas variables (**DB_NAME** ,**DB_USER**, **DB_PASSWORD**) estarán configuradas en el archivo ```  .env  ```
```
sed -i "s/database_name_here/$DB_NAME/" /var/www/html/config.php
sed -i "s/username_here/$DB_USER/" /var/www/html/config.php
sed -i "s/password_here/$DB_PASSWORD/" /var/www/html/config.php
````

7. Creamos base de datos de ejemplo
Con ```mysql -u root``` nos conectamos a MySQL con el usuario root y despues eliminamos con ```"DROP DATABASE IF EXISTS $DB_NAME"``` la base de datos definida si existe y despues la creamos ```"CREATE DATABASE $DB_NAME"```

En ```.env``` definimos la variable **DB_NAME** con la base de datos que queramos crear.

```  
mysql -u root <<< "DROP DATABASE IF EXISTS $DB_NAME"
mysql -u root <<< "CREATE DATABASE $DB_NAME"
```

8. Creamos usuario para la base de datos de ejemplo
Del mismo modo que antes entramos en la base de datos con el usuario root, nos aseguramos que el usuario no existe previamente, lo volvemos a crear identificandolo con una contraseña y le otorgamos toda clase de privilegios para la base de datos que hemos creado antes ````"GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%'"````

En ```.env``` definimos las variables **DB_USER** Y **DB_PASSWORD**

```
mysql -u root <<< "DROP USER IF EXISTS '$DB_USER'@'%'"
mysql -u root <<< "CREATE USER '$DB_USER'@'%' IDENTIFIED BY '$DB_PASSWORD'"
mysql -u root <<< "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%'" 
```

9. ConfiguraMOS el script de SQL con el nombre de la base de datos

Este comando modifica el archivo ``` /tmp/iaw-practica-lamp/db/database.sql ``` para que se remplace el texto **lamp_db** por **$DB_NAME**.

```
sed  -i "s/lamp_db/$DB_NAME/" /tmp/iaw-practica-lamp/db/database.sql
```

10. Cargamos la base de datos

Ejecuta el archivo **database.sql** para que cree cualquier objeto definido en cliente MySQL que hemos definido antes.

```
mysql -u root < /tmp/iaw-practica-lamp/db/database.sql
```

## 4. Comprobaciones
Vemos la base de datos y la tabla que debería crearse en la app que hemos configurado y entramos a través de un navegador en la dirección IP de nuestra maquina, para ver que efectivamente se ha instalado correctamente la app simple de la pila LAMP con la tabla que se ha copiado desde ``` /tmp/iaw-practica-lamp/db/database.sql ``` 

![0W3IJNY1uQ](https://github.com/user-attachments/assets/1fb2b993-3a05-4679-8ef0-a038ecacfbaa)

![xJ9B6kE3DP](https://github.com/user-attachments/assets/dd04ff7a-4e3f-48c2-9e61-5fcb1f04d5e4)










