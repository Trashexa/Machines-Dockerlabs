# WriteUp-Maquina-Borazuwarahctf-
<h1><b>Plataforma: DOCKERLABS</b></h1>

<h2><b>Escaneo de Puertos</b></h2>
<p>Haremos un escaneo de puertos para ver cuales estan abierto</p>
<br>
<img src="https://github.com/user-attachments/assets/1589a484-28bf-4ab6-88c1-f047518807e1">
<p>Vemos que tenemos los puertos 22 y 80</p>
<p>Entramos al servidor montando en el puerto 80</p>
<img src="https://github.com/user-attachments/assets/5c984bb6-f249-4041-b5ed-0a758131f422">
<p>Realmente no parece ver nada interesante en la pagina por lo que haremos una busqueda de subdominio pero que al finalizar nos daremos cuenta que no hay nada relevante</p>

<h2><b>Descarga y análisis de la Imagen</b></h2>
<p>Descargaremos la imagen dentro del servidor en el puerto 80</p>
<p>Una ves descargada y guardada en el nuestro ordenador procederemos a mirar si guarda algun archivo oculto dentro</p>
<p>Usaremos el comando steghide extract -sF nombreArchivo.jpge</p>
<img src="https://github.com/user-attachments/assets/bc354d07-a117-4041-9516-451c173422ed">
<p>Veremos que pudimos extraer un archivo llamado secreto.txt, por lo que haremos un <b>cat</b> para ver en su interior</p>
<img src="https://github.com/user-attachments/assets/d55735de-29cc-4139-b007-74a970b91240">
<p>Seguiremos buscando mas informacion como nos indica pero esta vez con la herramienta exiftool para mirar los metadatos </p>
<img src="https://github.com/user-attachments/assets/e47806a5-9ea6-4717-b994-6e522777d1b8">
<p>Como resultado tendremos un usuario: <b>" borazuwarah " </b></p>

<h2><b>Fuerza bruta con Hydra</b></h2>
<p>Utilizamos hydra para realizar un ataque de fuerza bruta, intentando encontrar la contraseña para luego acceder al puerto 22</p>
<img src="https://github.com/user-attachments/assets/04783c8a-fcd6-492f-8c94-2122049a20c7">

<h2><b>Credenciales de Acceso</b></h2>
<p>Tendremos las credenciales para entrar al servidor montado en el puerto 22</p>
<p>Usuario: borazuwarah</p>
<p>Contraseña: 123456</p>
<h2><b>Accesos al sistema</b></h2>
<p>Con las credenciales obtenidas, accedemos al servicio SSH </p>
<p>Al hacer un " sudo -l " para mirar los la lista de comandos que el usuario borazuwarah puede ejecutar con privilegios elevados (como superusuario) en el sistema veremos lo siguiente: </p>
<img src="https://github.com/user-attachments/assets/0954e497-4d28-4c75-8432-753a25714e2b">
<p>Esto nos esta indicando que el usuario " BORAZUWARAH " puede ejecutar cualquier comando como si fuese un usuario root</p>
<p>Por lo que podemos ejecutar sin ningun problema el comando <b>" sudo -u root /bin/bash "<b/></p>
<img src="https://github.com/user-attachments/assets/3678fc11-7fd9-4cb6-b6e5-c8199d003c00">
<p>Ahora si, somos usuarios root y al igual que el usuario BORAZUWARAH tendremos acceso ilimitado </p>