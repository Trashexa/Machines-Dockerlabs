# WriteUp-Maquina-Dockerlabs-
# [DOCKERLABS](https://dockerlabs.es/)

# CONEXION CON LA MAQUINA
Haremos un PING para validar que haya conexion con nuestra maquina objetivo

# ESCANEO DE PUERTOS
Haremos un escaneo de puertos para ver que puertos y servicios tiene abiertos
<br> <br>
![image](https://github.com/user-attachments/assets/25dfd673-a14a-47f0-9cff-8916ad8cb3cf)
<br>
Encontraremos unicamente el puerto 80 abierto por lo que accederemos a la web para ver que contiene

# VISUALIZACION DEL PUERTO 80 Y DETALLES
![image](https://github.com/user-attachments/assets/bee7a2c2-f8df-425e-a158-420bee79b702)
<br>
Veremos que tenemos un clon de la web Dockerlabs, y que no tiene algun tipo de informacion util dentro de esta

# BUSQUEDA DE SUBDOMINIOS
Buscaremos subdominios para ver si tenemos algo interesante escondido <br>
`gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -t 20 -x html,php,txt,php.bak` <br>
Encontraremos los siguientes subdominios
<br> <br>
![image](https://github.com/user-attachments/assets/b5f9a2be-ce75-4f13-b146-5d0e0eba80ac)
<br> 
Al entrar a los subdominios /uploads y /machines.php nos daremos cuenta que podemos cargar archivos (/machine.php) y luego ejectutarlos (/uploads)

# CARGA DE ARCHIVO MALICIOSO
Al saber que el servicio es " .php " podemos cargar algun tipo de script que nos funcione como una revere shell <br>
Accederemos por lo tanto a [REVERSE SHELL](https://www.revshells.com/) e iremos a la opcion de PHP PENTESTER MONKEY <br>
<br>
![image](https://github.com/user-attachments/assets/c8fdd591-7fa8-49ed-9c6c-6d85cd821a66)
<br> 
Lo configuraremos con nuestra IP y puerto de escucha para poder tener acceso a la maquina <br>
Y lo cargaremos dentro de /machine.php <br> <br>
![image](https://github.com/user-attachments/assets/53534e9e-e2ab-4929-87c9-2b61db441fc9)
![image](https://github.com/user-attachments/assets/22198230-dd36-4c62-bdd0-34601b3bd6d3)
<br> 
### ACLARACION LO TENDREMOS QUE SUBIR COMO ARCHIVO ".phar" que es otra variable de extension de PHP
<br> 
Una vez subido el archivo podremos visualizarlo en /uploads.php
<br>
<img src="https://github.com/user-attachments/assets/21aac0e9-08e6-4bbe-b450-3fa9ca75a550")>

# CONEXION POR REVERSE SHELL A LA MAQUINA OBJETIVO
Una vez subido nuestro archivo que provoca la reverse shell, nos pondremos a la escucha por el puerto anteriormente elegido y haremos click al archivo subido <br>
De esta forma obtendremos acceso a la maquina objetivo
<br>
Haremos un `whoami` para saber que usuario somos y un `sudo -l` para ver que privilegios tenemos para ejecutar
<br> <br>
![image](https://github.com/user-attachments/assets/c639c066-43b2-4072-b58c-1f467d460174)
<br>
Por lo que veremos que somos el usuario: data <br>
y podremos ejecutar los comandos "cut" y "grep"

# ESCALADA DE PRIVILEGIOS
Haremos una busqueda rapida de los archivos .txt para ver si encontramos algo interesante ANTES de investigar por las carpetas <br>
Usare el comando `find . -type f -name "*.txt" 2>/dev/null` para encontrar todos los archivos con extension .txt que esten disponibles y tenga permisos para leer o modificar<br>
`find` -> Encuentra los archivos <br>
`.` -> Busca en todos los directorios <br>
`-type f` -> Busca archivos <br>
`-name "*txt` -> Con nombres cuya extension sea .txt <br>
`2>/dev/null` -> Enviara todos los errores en este caso (Archivos con Permisos denegados) a la carpeta /dev/null es decir, los ignorara y no los mostrara
<br>
<br>
![image](https://github.com/user-attachments/assets/5ed7590e-8258-42ae-8ee0-e2c764ef196d)
<br>
Encontraremos el siguiente archivo "nota.txt" ubicado en la carpeta /opt
<br>
Haremos un cat al archivo
<br>
![image](https://github.com/user-attachments/assets/99eb9131-ac0b-4c07-8daf-c02e1f62d57b)
<br>
Por lo que sabremos que tendremos que leer el archivo /root/clave.txt

# LECTURA DEL ARCHIVO Y SORPRESA
Al hacerle un cat al archivo para poder leerlo nos dira que no tenemos los privilegios necesarios
<br>
Entraremos a [GTFOBins](https://gtfobins.github.io/) y buscaremos GREP o CUT y le daremos a la opcion de sudo. Ambas pueden ser usadas para este caso
<br>
Si usamos grep usaremos el siguiente comando:
<br>
![image](https://github.com/user-attachments/assets/0a79c8ea-d4c5-4c3f-b8d3-3036a1aeb1a7)
<br>
`sudo grep '' /root/clave.txt`
<br>
Si usamos CUT usaremos el siguiente comando:
<br>
![image](https://github.com/user-attachments/assets/67db0f6a-1fa3-4523-81d1-7a6e16badc16)
<br>
`sudo cut -d "" -f1 /root/clave.txt`
<br> <br>
Obtendremos la contraseña: <br>
![image](https://github.com/user-attachments/assets/65400cfb-acb8-4fed-9f22-53d90a5a73cb)
<br>
Lo unico que nos queda es hacer un `su` y colocar la contraseña para acceder al usuario "root"
<br>
![image](https://github.com/user-attachments/assets/a8450014-8380-4a7e-a65f-3828d28fc219)
