# 🧠 MÁQUINA TRUST

**Dificultad:** 🟢 Muy fácil

🔗 Puedes descargar la máquina desde aquí: [https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Despliegue de la máquina vulnerable

![máquina](./images/1.png)

---

## 2. Reconocimiento con **NMAP**

![máquina](./images/2.png)

---
El puerto 80 y el 22 están abiertos, ahora voy a escanear esos dos puertos para ver las versiones que corren detrás:

![máquina](./images/3.png)

![máquina](./images/4.png)

--- 

## 3. Enumeración de rutas con **FFUZ**
Lo primero que haré es ejecutar **ffuf** para que me enumere rutas ocultas que corren detrás de Apache, por ejemplo: php, html:

![máquina](./images/5.png)

---
Y efectivamente me ha descubierto un archivo **secret.ph**:

![máquina](./images/6.png)

---

Y la ruta nos da un nombre:

![máquina](./images/7.png)

---

## 4. Explotación de vulnerabilidades

Ahora utilizaré fuerza bruta con hydra ya que tenemos un nombre:

![máquina](./images/8.png)

---

Y nos ha dado una contraseña, ahora intentaremos entrar por ssh:

![máquina](./images/9.png)

---

## 5. Escalada de Privilegios

Ya estando dentro, ejecutaré este comando para ver si puedo ejecutar algún comando como root sin contraseña:

![máquina](./images/10.png)

---

Esto significa que puedo ejecutar vim como root usando sudo, sin necesidad de contraseña.

El siguiente paso es obtener una shell como root ejecutando:

![máquina](./images/11.png)

---

Y ya somos root, máquina resuelta exitosamente.


📅 Resuelta el 29/05/25

👩 Por Marcela Jiménez (aka Mar)
🐉









