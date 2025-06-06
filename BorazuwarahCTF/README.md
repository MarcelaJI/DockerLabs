# 🧠 MÁQUINA BorazuwarahCTF

**Dificultad:** 🟢 Muy fácil

🔗 Puedes descargar la máquina desde aquí: [https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Despliegue de la máquina vulnerable

![máquina](./images/1.png)

---

## 2. Verificación de conectividad

Realizamos un **ping** para comprobar si está activa en la red:

![máquina](./images/2.png)

---

## 3. Escaneo inicial con Nmap

![máquina](./images/3.png)

Se encuentran abiertos los puertos 22 y 80, lo siguiente que haré es ir al navegador para ver que nos encontramos en el puerto 80.

---

## 4. Búsqueda de vulnerabilidades

![máquina](./images/4.png)

He inspeccionado la imagen y no hay nada en el navegador, la descargaré y vemos que datos podemos encontrar..

---

![máquina](./images/5.png)

Primero he utilizado **steghide** que es una herramienta que nos ayuda a ocultar y extraer archivos dentro de una imagen o archivos de sonido, el comando **extract** es una de las operaciones que steghide puede realizar, en este caso nos ayuda a extraer un archivo oculto que esté escondido dentro d ela imagen y la flag **-sf** stego file (archivo esteganográfico). Es decir, el archivo que aparentemente es "normal", pero que posiblemente contiene algo escondido.

Seguimos recopilando información de la imagen con esta pista...


![máquina](./images/6.png)

---

Ahora utilizamos **exiftool**, es una herramienta que nos permite leer, escribir y editar metadatos de archivos, funciona con imágenes, vídeos, audios, PDFs, etc. Con esta herramienta podemos descubrir datos sensibles como por ejemplo: quién creó el documento, dónde se tomó una foto, si la imagen fue editada, si hay metadatos maliciosos o inusuales, etc. 

---

## 5. Explotación de vulnerabilidades

Ahora utilizaré fuerza bruta con hydra ya que tenemos un nombre:

![máquina](./images/7.png)

---

Y nos ha dado una contraseña, ahora intentaremos entrar por ssh:

![máquina](./images/8.png)

## 6. Escalada de privilegios

Ahora veremos que comando podemos ejecutar como root:

![máquina](./images/9.png)

Podemos ejecutar cualquier comando (ALL). como cualquier usuario y podemos hacer ```sudo /bin/bash``` sin que me pida password.

![máquina](./images/10.png)

Máquina resuelta exitosamente :)

📅 Resuelta el 06/06/25

👩 Por Marcela Jiménez (aka Mar)
🐉




