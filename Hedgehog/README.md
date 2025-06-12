# 🧠 MÁQUINA HEDGEHOG

**Dificultad:** 🟢 Muy fácil

🔗 Puedes descargar la máquina desde aquí: [https://dockerlabs.es/](https://dockerlabs.es/)

---

## 1. Despliegue de la máquina vulnerable

![máquina](./images/1.png)

---

## 2. Reconocimiento con **NMAP**

![máquina](./images/2.png)

---

Explicación detallada de cada parámetro:

- **-p-**: Hace un escaneo de todos los puertos (1-65535).
- **--open**: Muestra solo puertos abiertos.
- **-sS**: Nos permite hacer escaneos sigilosos y evitar la detección de firewall. Hace un escaneo de tipo SYN sin establecer conexión completa, y así evitar la detección del firewall.
- **--min-rate=5000**: Este parámetro nos ayuda a controlar la velocidad de los paquetes enviados y así poder hacer un escaneo más rápido con 5000 paquetes por segundo.
- **-Pn**: No realiza ping porque ya da por hecho que el host está activo.
- **-n**: No hace resolución DNS.
- **-vvv**: Modo verbose, para ir viendo información detallada en tiempo real.


Ahora que hemos visto que el puerto 22 y el 80 están abiertos, vamos a ver sus versiones con el parámetro *-sV* ejecutando:

```bash
nmap -p22,80 -sV -n -vvv 172.17.0.2
```


![máquina](./images/3.png)

---

El puerto 80 está abierto, vamos a escribir en el navegador: **http://172.17.0.2:80** y nos ha dado este nombre:

![máquina](./images/4.png)

---

## 3. Explotación de vulnerabilidades

Ahora lo que haré es aplicar fuerza bruta con hydra con ese nombre.

Lo siguiente que haré es usar el comando **tac** ya que la contraseña podría estar en las últimas combinaciones de rockyou:

![máquina](./images/5.png)

**tac**: Es un comando de linux que nos muestra el contenido en orden inverso, es decir(linea por línea, desde la última hasta la primera).

y lo voy a redirigir con **>>** al archivo **new.txt** si el archivo no existe, lo crea y si existe, añade el contenido al final del archivo sin sobreescribir sobre el.

Luego utilizo el comando **sed** que es un editor de flujo que permite procesar y editar texto. El flag **-i** nos editará el archivo directamente en su lugar, modificando el archivo original, sin hacer ninguna copia. **'s/ //g'**: Es una expresión que indica la operación de búsqueda y reemplazo: **s**: Indica una operación de sustitución.
**/**: El espacio dentro de las primeras barras indica que se busca un espacio en blanco.
**/g/**: El modificador g significa *global*, lo que significa es que reemplazará todos los espacios encontrados en cada línea del archivo. Lo que el comando completo hace es eliminar todos los espacios en blanco del archivo.

---

Ahora vamos a utilizar fuerza bruta con **hydra** aplicando lo de el comando **tac** en el nuevo archivo:

![máquina](./images/6.png)

----

Ya que tenemos la contraseña de ese usuario, vamos a entrar por **ssh**:

![máquina](./images/7.png)

---

## 4. Escalada de Privilegios

Ahora lo primero que haré es ver si puedo ejecutar algún comando como root con este usuario:

![máquina](./images/8.png)

Y lo primero que vemos que el usuario **sonic** puede ejecutar comandos como root, entonces nos convertimos en sonic y el siguiente paso es escalar con sudo.

Máquina resuelta exitosamente :)

📅 Resuelta el 01/06/25

👩 Por Marcela Jiménez (aka Mar)
🐉