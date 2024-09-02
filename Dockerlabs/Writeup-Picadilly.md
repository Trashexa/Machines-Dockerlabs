# Writeup-Maquina-Amor-
# DOCKERLABS | [PICADILLY](https://dockerlabs.es/#/)

# ANTES DE COMENZAR
#### Haremos un PING para commprobar la conexion

# ESCANEO DE PUERTOS
Con la herramienta NMAP haremos un escaneo de puertos
<br>
<br>
![image](https://github.com/user-attachments/assets/5fd1ba33-070a-4889-b8b5-3983c441c015)

# ANALIZANDO EL CONTENIDO DE LOS SERVICIOS
Dentro del puerto <b>80</b> tendremos lo siguiente
<br> <br>
![image](https://github.com/user-attachments/assets/898070b6-803b-469a-9328-2032759d0a11)
<br>
Dentro del archivo backup.txt, tendremos un usuario `mateo`, una "contraseña" y un <b>ENIGMA</b>
<br>
Y dentro del servidor montado en el puerto 443, tendremos lo siguiente
<br> <br>
![image](https://github.com/user-attachments/assets/b98e49b7-d585-4361-badf-709ddc106d84)
<br>
Un servidor en donde podremos cargar archivos

# BUSCANDO SUB-DOMINIOS 
Usaremos la herramienta `gobuster` para ver si hay algun sub-dominio oculto
<br>
Yo usare el siguiente comando: <br>
`gobuster dir -u https://172.17.0.2/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-lowercase-2.3-small.txt -k -t 20 -x html,php,txt,php.bak`
<br>
Unicamente encontre sub-dominio ocultos en el servidor montado en el puerto 443 por lo que tuve que usar `-k` para poder <b>IGNORAR</b> los certificados SSL
<br>
Estos fueron los sub-dominios encontrados: <br>
<br>
![image](https://github.com/user-attachments/assets/fce7c191-0da2-4dd4-b60b-1d02d039f6d2)
<br>
Con esto nos damos cuenta que podemos acceder a los archivos que cargamos en la web

# SHELL REVERSE
Intentaremos subir, en este caso una shell reverse PHP, con la ayuda de nuestra herramienta de shellreverse seleccionaremos la opcion [PHP-PentestedMonkey](https://www.revshells.com/) para editar nuestra shell reverse
<br> <br>
![image](https://github.com/user-attachments/assets/fd335a64-bf64-47b8-9518-4bce3d5feb49)
<br>
Una vez tenido el codigo, lo copiaremos y lo pegaremos en un nuevo archivo, luego subiremos nuestro archivo a la web y nos dirigiremos a la url uploads -> `https://172.17.0.2/uploads`
<br>
Una vez en esta pagina, dentro de nuestra ventana de comandos haremos un `nc -lnpv XXX` en donde XXX remplazara el puerto elegido para establecer comunicacion en la reverse shell y le daremos click a nuestro archivo subido para establecer una comunicacion

# DENTRO DE LA MAQUINA OBJETIVO
Una vez dentro de la maquina haremos un `whoami` para saber que usuario tenemos y parece ser que somos usario `data`
<br>
Veremos que si hacemos un `sudo -l` nos dira que necesitamos una contraseña para acceder
<br>
Si inspeccionamos las carpetas nos encontramos que en /home/ tenemos un usuario Mateo y cuando queremos acceder a el con la contraseña otorgada anteriormente nos da error

# DESCIFRANDO EL ENIGMA Y OBTENCION DE LA CONTRASEÑA
Tenemos un enigma:
<br>
#### " To solve this riddle, think of an ancient Roman emperor and his simple method of shifting letters "
<br>
Por lo tanto usaremos la herramienta DCODE para descifrar la contraseña 
<br>
Procedo a colocar en Google <b>DCODE ROMANO</b> ya que no sabremos que tipo de cifrado puede ser
<br>
Encontramos la siguiente web:
<br> <br>
<img src="https://github.com/user-attachments/assets/08143b4c-8d92-492c-8081-6819e959d04a">
<br>
Dentro de ella peguaremos la contraseña dada y veremos los resultados
<br> <br>
<img src="https://github.com/user-attachments/assets/ad46591c-4c1b-484a-aaa7-1033e613edfd">
<br>
<img src="https://github.com/user-attachments/assets/31344cd3-acea-4a99-a6b5-5cb6fca6eac3">
<br>
En total tendremos 25 tipos de cadena de texto diferentes que pueden ser nuestra contraseña para el usuario Mateo

# ACCESO AL SISTEMA 
Luego de probar las 25 contraseñas nos daremos cuenta que <b>NINGUNA</b> nos permite entrar por lo que observaremos que en la contraseña `easycrxazy` <br>
Le borraremos la " X " para tener la cadena de texto bien escrita `easycrazy` y la probaremos
<br> <br>
![image](https://github.com/user-attachments/assets/9d9f88b9-8c89-429f-96c3-d55ade832d13)
<br>
Haremos un `whoami` para comprobar nuestro nuevo usuario y veremos que accedemos como el usuario Mateo

# ESCALADA DE PRIVILEGIOS
Nuevamente haremos un `sudo -l` para ver los comandos que podemos ejecutar sin ser super usuario y veremos que podemos ejecutar el siguiente
<br> <br>
![image](https://github.com/user-attachments/assets/63acc766-694c-458e-8b96-09313951a669)
<br>
Por lo tanto usaremos la herramienta [GTFOBins](https://gtfobins.github.io)
<br>
Buscaremos en el buscador " PHP " y ejecutaremos el codigo que nos convierte en sudo
<br> <br>
![image](https://github.com/user-attachments/assets/4e2b920c-8647-4524-a68c-1b25c5b363d0)
<br>
`CMD="/bin/sh"
sudo php -r "system('$CMD');"`
<br>
Una vez hecho esto, ya seremos usuario ROOT !!





