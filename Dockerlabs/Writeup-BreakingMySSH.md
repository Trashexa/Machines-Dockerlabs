# Writeup: Máquina "Breaking my SSH"
<h1>Plataforma: <b>DOCKERLABS</b></h1>
<h2><b></b>Escaneo de Puertos</b></h2>
<p>Iniciamos el análisis con un escaneo básico y encontramos el siguiente puerto abierto:</p>
<br>
<img src="https://github.com/user-attachments/assets/d3b87d33-b151-4ac0-94a1-64f571d2b06d">
<br>
<br>
Dado que solo el puerto 22 (SSH) está abierto, no nos queda otra forma que realizar un ataque de fuerza bruta para intentar obtener acceso al sistema.
<br>
<h2>Fuerza Bruta en SSH</h2>
<p>Utilizamos hydra para realizar un ataque de fuerza bruta, intentando encontrar tanto el nombre de usuario como la contraseña.</p>
<br>
<p>En este caso tuve que buscar y usar el diccionario usuarios.txt del siguiente sitio: <a href="https://github.com/hackingyseguridad/diccionarios">Diccionarios</a></p>
<br>
<p>Diccionario Usuario: <b>Usarios.txt</b></p>
<p>Diccionario Contraseña: <b>Rockyou.txt</b></p>
<br>
<img src="https://github.com/user-attachments/assets/88e9b8bf-19bb-4c14-b10c-589ca3354290">
<br>
<h2><b></b>Credenciales Encontradas:</b></h2>
<p>Usuario: root
  <br>
Contraseña: estrella</p>
<h2><b></b>Acceso al Sistema</b></h2>
<p>Con las credenciales obtenidas, accedemos al servicio SSH como usuario  "root":</p>
<br>
<p>Al querer acceder nos encontraremos con la siguiente alerta</p>
<br>
<img src="https://github.com/user-attachments/assets/53c5ab8f-f13f-4dc1-bad7-231578e5ee7d">
<br>
<p>Para poder solucionar esto nos fijaremos en el siguiente enunciado</p>
<img src="https://github.com/user-attachments/assets/93b42d8c-0032-46b1-bdd7-5a109d034e89">
<br>
<p>Por lo tanto haremos lo que se nos indica</p>

<p>Escribimos en la terminal: <b> ssh-keygen -f '/root/.ssh/known_hosts' -R '172.17.0.2' </b> </p>

<p>Una vez hecho esto, haremos de nuevo la conexion al servidor SSH y ahora si podemos colocar la contraseña</p>
<h2>Resultado:</h2>
<p>Entramos al sistema con privilegios de root, lo que significa que ya tenemos control total sobre la máquina</p>