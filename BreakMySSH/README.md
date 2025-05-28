# 🧠 MÁQUINA BREAKMYSSH

**Dificultad:** 🟢 Muy fácil!

---

## 1. Despliegue de la máquina

Ejecutamos la máquina vulnerable:

![máquina vulnerable](./images/1.png)

---
## 2. Verfifico la conectividad de la máquina

Realizo un **ping** para ver si está activa en la red:

![máqina](./images/2.png)

## 3. Reconocimiento con **NMAP**

Hago un escaneo completo para saber que puertos están abiertos: 

![máquina](./images/3.png)

---

El puerto 22 está abierto, entonces ahora hago un escaneo al puerto 22 para ver las versiones que corren por detrás:

![máquina](./images/4.png)

---

## 4. Búsqueda de vulnerabilidades

Ahora que ya tengo la versión voy a buscar exploits para esta versión:

![máquina](./images/5.png)

---

Ahora abriré Metasploit para enumerar usuarios, usando la herramienta:

![máquina](./images/6.png)

---

Ahora ejecuto el siguiente comando para poder entrar en la enumeración de usuarios y setear algunas cosas:

```bash
use auxiliary/scanner/ssh/ssh_enumusers
```

El siguiente paso es setear el RHOSTS y poner la ip de la víctima:

![máquina](./images/7.png)

---
También tenemos que setear USER_FILE donde buscará los usuarios:

![máquina](./images/8.png)

---

Luego verificamos con **show options** para ver los cambios:

![máquina](./images/9.png)

---

Luego ejecutamos run para lanzar el exploit:

![máquina](./images/10.png)

---

Nos ha detectado el usuario *lovely*

## 5. Explotación de vulnerabilidades

Ahora utilizaremos fuerza bruta con hydra, para el descubrimiento de la contraseña del usuario encontrado:

![máquina](./images/11.png)

---

Nos ha descubierto la contraseña, el siguiente paso es entrar con ssh con el usuario *lovely*:

![máquina](./images/12.png)

---

Entramos al opt para ver si hay algo oculto que nos ayude y vemos un .hash:

![máquina](./images/13.png)

---
![máquina](./images/14.png)

---

Ahora pondré este hash en la web en la página **crackstation.net**:

![máquina](./images/15.png)

Nos ha dado la contraseña!!

## 6. Escalada de privilegios

Y ahora escalamos con su:

![máquina](./images/16.png)

---

Máquina resuelta exitosamente :) 

📅 Resuelta el 28/05/25

👩 Por Marcela Jiménez (aka Mar)
🐉






