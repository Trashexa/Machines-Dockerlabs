# Writeup-Maquina-SecretJenkins-
# DOCKERLABS | [Secretjenkis](https://dockerlabs.es/#/)

# ANTES DE COMENZAR
#### Haremos un PING para commprobar la conexion
<br>
<img src="https://github.com/user-attachments/assets/95bba54f-bc89-48af-a70e-f6445d243f25">

# ESCANEO DE PUERTOS 
Usaremos NMAP para escaner los puertos
<br> <br>
![image](https://github.com/user-attachments/assets/ee7ef15c-a0e5-4fa3-8514-eaf1d18e09b4)
<br>
Encontraremos abiertos los puertos: 22 y 8080
<br>
Haremos un analisis de vulnerabilidades
<br> <br>
![image](https://github.com/user-attachments/assets/b5464808-b401-4eeb-964b-c2ada6dbb8fe)

# PANEL DE LOGIN
Entraremos al servicio montado en el puerto 8080
<br> <br>
![image](https://github.com/user-attachments/assets/5194f976-ffbe-4684-bfc6-56cc339545d5)
<br>
Tendremos un panel de login <br>
Probaremos con las credenciales habituales y hacer SQL Injection pero vemos que no funciona
<br>

# BUSQUEDA DE SUB-DOMINIOS
Buscaremos sub-dominios para ver si encontramos algun otro acceso 
<br> 
Para este caso use el comando:
<br>
`gobuster dir -u http://172.17.0.2:8080/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-small.txt -k -t 20 -x html,php,txt,php.bak`
<br>
Y pude encontrar los siguientes sub-dominios
<br> <br>
![image](https://github.com/user-attachments/assets/058cd243-8b4f-4113-bdf7-61e24d1ffc46)
<br>
Entraremos a `/robots.txt` y vemos que hace referencia hacia `build` por lo que luego vemos que tenemos un sub-dominio llamado `/builds`

# BUSQUEDA DE EXPLOIT
Al ingresar a <TARGET_IP>:8080/builds veremos lo siguiente
<br> <br>
![image](https://github.com/user-attachments/assets/2dd79dc6-7c42-4947-98a3-70565d91ec09)
<br>
En donde vemos abajo a la derecha la <b>Version</b> utilizada por lo tanto buscaremos algun exploit para este
<br>
Buscaremos y encontraremos el siguiente exploit <br>
[CVE-2024-23897](https://github.com/godylockz/CVE-2024-23897)
<br>
Este exploit nos permitira leer archivos que esten en el sistema de archivos del servidor Jenkins.
<br>
Clonaremos el repositorio y le daremos privilegios con `chmod +x jenkins_fileread.py `
# EJECUTANDO EXPLOIT
Una vez ejecutado el exploit podremos leer el archivo `passwd` contenida en la carpeta `/etc` para saber que usuarios hay dentro del sistema
<br> <br>
![image](https://github.com/user-attachments/assets/c8c3f91a-3d79-44d0-8748-27e9b85afbdf)
<br>
Veremos entonces que dentro de este archivo tenemos 4 usuarios: <br>
<b>root</b> <br>
<b>jenkins</b> <br>
<b>bobby</b> <br>
<b>pinguinito</b> <br>

# FUERZA BRUTA CON HYDRA
Teniendo estos usuarios usaremos la herramienta HYDRA para ver si podemos encontrar la contraseña
<br> <br>
![image](https://github.com/user-attachments/assets/65d58cfe-bba0-4671-9fb6-a44b83ae0351)
<br>
Descubriremos las credenciales del usuario BOBBY <br>
Usuario: boby <br>
CONTRASEÑA: chocolate

# ACCESO AL SERVICIO SSH (PUERTO 22)
Pondremos las credenciales obtenidas
<br>
`ssh bobby@172.17.0.2`
<br>
### SI NOS MUESTRA EL SIGUIENTE WARNING DEBEREMOS HACER LO SIGUIENTE
![image](https://github.com/user-attachments/assets/25b97a85-bd08-4262-8684-2a9619f8ee80)
<br>
Colocar el siguiente comando que se nos indica <br>
`ssh-keygen -f '/root/.ssh/known_hosts' -R '172.17.0.2'`
<br>
Una vez hecho esto volveremos a colocar las credenciales para acceder al servicio SSH

# ESCALADA DE PRIVILEGIOS PARTE 1
Una vez dentro haremos un `sudo -l` para ver los privilegios que tenemos como usuario y veremos que podemos ejecutar el siguiente comando como si fueramos el usuario pinguino:
<br>
`/usr/bin/python3`
<br> <br>
![image](https://github.com/user-attachments/assets/7ca66ed8-5955-4092-858c-6dab23248cd0)
<br>
Por lo que podremos ejecutar una shell en python como usuario pinguino por lo tanto colocaremos el siguiente comando
<br>
`sudo -u pinguinito /usr/bin/python3 -c 'import os; os.system("/bin/sh")'`
<br>
Haremos un `whoami` para verificar si efectivamente ahora somos el usuario pinguinito
<br> <br>
![image](https://github.com/user-attachments/assets/e99f7137-9599-40be-a30a-b8a2a2e7c06f)

# ESCALADA DE PRIVILEGIOS PARTE 2
Ahora que somos el usuario pinguinito volveremos hacer un `sudo -l` para ver si tiene nuevos privilegios y notamos que ahora tenemos tambien lo siguiente
<br> <br>
![image](https://github.com/user-attachments/assets/7dc96153-fe46-4112-9e14-bc17fc950c76)
<br>
Tendremos un archivo `script.py` para poder ejecutar por lo tanto veremos que contiene este archivo con `cat /opt/script.py`
<br> 
Y veremos que su objetivo es copiar un archivo de una ubicación a otra usando la librería shutil de Python por lo tanto vamos a editar el archivo para luego ejecutar una shell como usuario root
<br>
Primero nos ubicaremos en la carpeta /opt/ y verificaremos si tenemos permisos de escritura en el archivo:
<br>
Para esto usaremos el comando `ls -l script.py` <br>
Veremos que nada mas tenemos privilegios de lectura (r), por lo tanto vamso a modificarlo:
<br>
Usaremos el comando `chmod 744 script.py` de esta forma le daremos unicamente privilegios de escritura al usurio "Pinguinito"
<br> 
Modificaremos el archivo que va a creear una shell una vez ejecutado
<br>
### Comandos: 
`echo "import os" > /opt/script.py`  -->  importa el módulo os, el cual permite interactuar con el sistema operativo <br>
`echo 'os.system("bash")' >> /opt/script.py` --> ejecuta una shell de bash cuando se ejecuta el script <br>
<br>
Una vez hecho esto ejecutaremos el script para crear una shell como usuario root
`sudo /usr/bin/python3 /opt/script.py` 
<br>
<b>ENHORABUENA SOMOS USUARIO ROOT !!</b>

