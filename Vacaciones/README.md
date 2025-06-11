# 🧠 MÁQUINA VACACIONES

**Difícultad**: 🟢 Muy fácil

🔗 Puedes descargar la máquina desde aquí: [https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Despliegue de la máquina vulnerable

![máquina](./images/1.png)

---

## 2. Reconocimiento inicial con NMAP

![máquina](./images/2.png)

Encontramos abiertos los puertos 22 y 80, vamos a escribir en el navegador: **http://172.17.0.2:80** para ver que hay en el puerto 80:

---

## 3. Búsqueda de vulnerabilidades

![máquina](./images/3.png)

Al entrar en el navegador, he inspeccionado la página y nos han dejado una pista con esta frase...

---

## 4. Explotación de vulnerabilidades

Ahora aplicaré fuerza bruta con hydra, con el nombre de **Camilo** porque según la pista el mensaje es de **Juan** para **Camilo**:

![máquina](./images/4.png)

---

Ahora que nos ha dado una contraseña, vamos a entrar por ssh:

![máquina](./images/5.png)

---

Ahora ejecutaré **sudo -l** para ver si el usuario **camilo** puede ejecutar algún comando como root:

![máquina](./images/6.png)

Y como hemos visto este usuario no puede ejecutar ningún comando como root.


Ahora lo que haré es buscar rutas donde puedan haber correos, por el nombre de la máquina para ver si encontramos alguna pista, que normalmente pueden estar en **/var/mail**:

![máquina](./images/7.png)

Y si, nos han dado una pista, ahora vamos a entrar con **su** con el usuario de **Juan**:

![máquina](./images/8.png)

## 5. Escalada de privilegios

Primero vamos a ver que puede ejecutar **Juan** como root:

![máquina](./images/9.png)

Y ahora que hemos visto que podemos ejecutar ruby, procedemos:

![maáquina](./images/10.png)

Y ya somos root!

Máquina resuelta exitosamente:)

📅 Resuelta el 11/06/25

👩 Por Marcela Jiménez (aka Mar)
🐉








