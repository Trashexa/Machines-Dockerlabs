# WriteUp-Maquina-Obssesion
# [Obssesion](https://dockerlabs.es/#/) | DOCKERLABS

Haremos un PING para verificar la conexion
<br>
<br>
<img src="https://github.com/user-attachments/assets/22f62124-d07c-4983-9f02-49f1149f44ae">


# ESCANEO DE PUERTOS
Haremos un escaneo de puertos para ver cuales estan abiertos
<br>
<img src="https://github.com/user-attachments/assets/10b113d0-eff0-474c-ba52-f9cdf788a0d6">
<br>
Vemos que tenemos abierto el puerto 21, 22 y 80
# BUSQUEDA DE DIRECTORIOS Y SUBDOMINIOS
Al ver que tenemos una web montado en el puerto 80 podremos buscar subdominios con gobuster por ejemplo
<br>
Para esta ocacion use estas configuraciones 
#### gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -t 20 -x html,php,txt,php.bak
<br> 
Y encontre lo siguiente: 
<br>
<img src="https://github.com/user-attachments/assets/fa937875-191d-4d4b-a4fb-abdcb396a849">
<br> 
Dentro de /backups/ encontramos un usuario que nos puede ser util 

# FUERZA BRUTA CON HYDRA
Ya que tenemos un usuario para todos los servicios como especifica en la web podremos ver si podemos obtener una contraseña para entrar al servicio SSH
<br>
En este caso use la herramienta HYDRA y el diccionario ROCKYOU para conseguir la contraseña
<br>
<img src="https://github.com/user-attachments/assets/57522568-db65-40ce-a65c-c2f15000e111">

# CREDENCIALES OBTENIDAS
Usuario: russoski
<br>
Contraseña: iloveme

# ACCESO AL SERVICIO SSH (PUERTO 22)
Con estas credenciales podremos entrar al puerto 22
<br>
#### Comando: ssh russoski@IP-MAQUINA-OBJETIVO
Nos aparecera este mensaje de ALERTA
<br>
<img src="https://github.com/user-attachments/assets/a2959cc5-a613-4c3e-af2e-a26dd9cf91f4">
<br>
Esto nos indica que deberemos usar el comando que nos deja abajo
#### ssh-keygen -f '/root/.ssh/known_hosts' -R '172.17.0.2'
<br>
Una vez hecho esto, volveremos a lanzar una conexion al servidor SSH y nos conectaremos con las credenciales

# ESCALANDO PRIVILEGIOS
Al entrar al servidor colocaremos " whoami " para saber si somos un usuario o un superusuario (root)
<br>
Lanzaremos el comando sudo -l para ver cuales son los comandos que podemos usar y nos encontrmaos con lo siguiente
<br>
<br>
<img src="https://github.com/user-attachments/assets/5419e02a-5745-4c09-ba8d-841cdaa05b15">
<br>
Si leemos la ultima linea nos esta indicando que este usuario (russoski) tiene permisos para ejecutar el comando /usr/bin/vim como root ((root)) sin necesidad de ingresar una contraseña (NOPASSWD).
<br>
<br>
Entraremos entonces a [GTFOBins](https://gtfobins.github.io/gtfobins/vim/#sudo) para poder buscar el codigo que nos hara root en este caso es:
<br>
#### sudo vim -c ':!/bin/sh'
<br>
ENHORABUENA SOMOS ROOT !!