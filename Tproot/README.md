# 🧠 MÁQUINA TPROOT

**Difícultad**: 🟢 Muy fácil

🔗 Puedes descargar la máquina desde aquí: [https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Despliegue de la máquina vulnerable

![máquina](./images/1.png)

---

## 2. Reconocimiento con NMAP

![máquina](./images/2.png)

---

Ahora voy a ver las versiones que corren detrás del puerto 21 y 80 con el parámetro **-sV** con NMAP, este es el comando completo:

```bash
nmap -sV -vvv 172.17.0.2
```


![máquina](./images/3.png)

## 3. Búsqueda de vulnerabilidades

Ahora vamos a buscar algún **exploit** para el servicio que corre detrás del puerto 21:

![máquina](./images/4.png)

Y como hemos encontrado un **exploit** vamos a seleccionarlo:

![máquina](./images/5.png)

## 4. Escalada de privilegios

Y ahora ejecutamos el exploit y hemos entrado

![máquina](./images/6.png)

y ya somos root, máquina resuelta exitosamente :)

---


📅 Resuelta el 11/06/25

👩 Por Marcela Jiménez (aka Mar)
🐉











