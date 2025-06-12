# 🧠 MÁQUINA FIRSTHACKING

**Dificultad:** 🟢 Muy fácil

---

## 1. Despliegue de la máquina

Ejecutamos la máquina vulnerable:

![máquina vulnerable](./images/1.png)

---

## 2. Verificación de conectividad

Realizamos un **ping** para comprobar si está activa en la red:

![máquina vulnerable](./images/2.png)

---

## 3. Escaneo inicial con Nmap

![máquina vulnerable](./images/3.png)

Explicación detallada de cada parámetro:

- **-p-**: Hace un escaneo de todos los puertos (1-65535).
- **--open**: Muestra solo puertos abiertos.
- **-sS**: Nos permite hacer escaneos sigilosos y evitar la detección de firewall. Hace un escaneo de tipo SYN sin establecer conexión completa, y así evitar la detección del firewall.
- **--min-rate=5000**: Este parámetro nos ayuda a controlar la velocidad de los paquetes enviados y así poder hacer un escaneo más rápido con 5000 paquetes por segundo.
- **-Pn**: No realiza ping porque ya da por hecho que el host está activo.
- **-n**: No hace resolución DNS.
- **-vvv**: Modo verbose, para ir viendo información detallada en tiempo real.


Se encuentra abierto el puerto **21 (FTP)** y el TTL sugiere que estamos ante una máquina **Linux**.

---

## 4. Escaneo detallado al puerto 21

```bash
nmap -p21 -sV -vvv 172.17.0.2
```

![máquina vulnerable](./images/4.png)

## 5. Búsqueda de vulnerabilidades
Con la versión del servicio FTP obtenida, usamos Searchsploit para buscar un exploit:

![máquina vulnerable](./images/5.png)

✅ ¡Exploit encontrado!

Lo copiamos al directorio actual con:

```searchsploit -m unix/49757.py```
📝 El flag -m (mirror) copia el archivo al directorio donde estamos trabajando.

![máquina vulnerable](./images/6.png)

## 6. Ejecución del exploit
Ejecutamos el script:

![máquina vulnerable](./images/7.png)

Estamos dentro como root!

✅ Máquina resuelta exitosamente.

📅 Resuelta el 26/05/25

👩 Por Marcela Jiménez (aka Mar)
🐉