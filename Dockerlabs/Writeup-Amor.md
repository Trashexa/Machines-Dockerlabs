
# Writeup-Maquina-Amor-
# DOCKERLABS | [AMOR](https://dockerlabs.es/#/)

# ANTES DE COMENZAR
#### Haremos un PING para commprobar la conexion

# ESCANEO DE PUERTOS
Haremos un scan de puertos por NMAP y encontraremos dos puertos abiertos
<br> 
<br>
<img src="https://github.com/user-attachments/assets/4520b4ec-07d8-4267-b15c-ca87ee290173">

# VER EL PUERTO 80
Dentro del puerto 80 tendremos lo siguiente
<br>
<br>
![image](https://github.com/user-attachments/assets/3c4052bb-6011-4e11-9415-c2fe7d97a705)
<br>
Con gobuster no encontraremos nada interesante por lo tanto revisaremos a fondo la informacion que nos ofrece la pagina web
<br>
<br>
Tendremos dos usuarios en estos momentos: Juan y Carlota

# FUERZA BRUTA CON HYDRA
Ya que tenemos dos usuarios intentaremos ver si algun usuario nos ayuda para encontrar una contraseña y obtener acceso al puerto 22 (SSH)
<br>
<br>
![image](https://github.com/user-attachments/assets/8e9f3d7e-aad9-4cb3-b589-a62355cb502c)

# CREDENCIALES OBTENIDAS
Usuario: carlota 
<br>
Contraseña: babygirl

# ACCEDIENDO AL PUERTO 22 (SSH)
Ya que tenemos un usuario y contraseña haremos conexion al puerto 22.
<br> 
Nos aparecera este WARNING por lo que copiaremos y pegaremos el comando que no dice
<br> 
<br>
![image](https://github.com/user-attachments/assets/fbfe1274-b813-407f-9e81-e5c7233d71d5)
<br>
<br>
Comando: `ssh-keygen -f '/root/.ssh/known_hosts' -R '172.17.0.2'`
<br>
<br>
Luego de esto volveremos a hacer la conexion al servidor SSH y ahora si podremos entrar
<br>
<br>
Probaremos hacer un `sudo -l` y veremos que NO tenemos ningun comando con privilegios como root

# EXPLORACION DE CARPETA
Al no tener un acceso facil al usuario root, buscaremos en las carpetas archivos que nos puedan servir
<br>
<br>
Encontraremos dentro de la ruta `home/carlota/Desktop/fotos/vacaciones` un archivo llamado `imagen.jpg`

# RECOLECCION DE METADATOS Y DECODIFICACION
Al tener esta imagen deberemos montar un servidor interno para poder descargar la imagen por lo que haremos lo siguiente:
<br>
## Montar server y descarga de imagen: 
`python3 -m http.server 9999` -> Correremos este comando dentro de la carpeta que contiene la imagen
<br>
<br>
En la terminal de nuestra maquina atacante pondremos el siguiente comando 
<br>
`wget http://172.17.0.2:9999/imagen.jpg` -> Esto hara que en nuestra maquina se descargue la imagen (Acordar de modificar la IP si es necesario)
<br>
## Conseguir los metadatos
Para esto usaremos primero exiftool para ver los metadatos de la imagen, en este caso no hay nada que nos pueda servir
<br>
<br>
![image](https://github.com/user-attachments/assets/9d8f9a99-502f-4259-96a5-3fd965a399b1)
<br>
Usaremos entonces la herramienta <b>steghide</b> para ver si podemos extraer algun dato oculto
<br>
`steghide extract -sf imagen.jpg`
<br>
<br>
Encontraremos el siguiente archivo: <b>secret.txt</b> que contiene la siguiente cadena de texto:
<br>
![image](https://github.com/user-attachments/assets/1c6908eb-35a3-44cc-9d1f-9475f74b9cae)
<br>
## DECODIFICACION DE LA CADENA DE TEXTO
En esta oportunidad nos dan una pista de que es un codigo en base24 (b24) por lo que tendremos que decodificarla a partir de este codigo
<br>
Usaremos el codigo `cat secret.txt | base24 -d`
<br>
<br>
![image](https://github.com/user-attachments/assets/5d826c80-e0cc-4aac-9478-48051590a622)
<br>
Obtendremos una nueva cadena de texto <b> "eslacasadepinypon" </b> intentemos usar esta cadena de texto en el servidor SSH

# DESENLACE FINAL
Accederemos al usuario oscar con un `su oscar` y colocaremos la contraseña anteriormente encontrada
<br>
Haremos un `sudo -l` para saber si tenemos comandos con privilegios y veremos lo siguiente:
<br>
<br>
![image](https://github.com/user-attachments/assets/89fa0832-0552-4ed3-8620-0bb1c5de2f21)
<br>
Por lo que accederemos a la pagina [GTFOBins](https://gtfobins.github.io/gtfobins/ruby/#sudo)
<br> <br>
Dentro de esta buscaremos <b>RUBY</b> y buscaremos la opcion de <b>SUDO</b>
<br> <br>
Buscaremos el script que nos ayudara a escalar privilegios: `sudo ruby -e 'exec "/bin/sh"'`
<br> <br>
Pegaremos el script 
<br> <br>
ENHORABUENA SOMOS ROOT !!
