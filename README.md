# practica-1.3

# Despliegue de una aplicación web LAMP sencilla

##  1. Introducción
En esta practica realizaremos el despliegue de una aplicación web  de la pila LAMP (Linux, Apache, MySQL y PHP), utilizando el script realizado en la practica 1, en una instancia EC2 de AWS con Ubuntu Server. Automatizaremos la instalación de la pila LAMP y la configuración de la aplicación web mediante scripts de Bash.

## 2.Creacion de una instancia EC2 en AWS e instalacion de Pila LAMP
Para la reaizacion de ete apartado seguiremos los pasos detallados en la practica-iaw-1.1 y utilizaremos el script ``` install_lamp.sh ```.

[Practica-iaw-1.1](https://github.com/marinaferb92/practica-iaw-1.1/tree/main)
[Script Install LAMP](https://github.com/marinaferb92/practica-iaw-1.1/blob/main/scripts/install_lamp.sh)

Una vez hecho esto nos aseguraremos de que la Pila LAMP esta funcionando correctamente.

- Verificaremos el estado de apache.

- Entramos en mysql desde la terminal para ver que esta corriendo.

- Verificamos la instalacion de PHP

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
























