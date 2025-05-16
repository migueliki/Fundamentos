# Sistema de Copias de Seguridad Automatizadas Multicapa

## 1. Arquitectura General del Sistema

---
| Elemento               | Descripción                                            |
|------------------------|--------------------------------------------------------|
| **Rol**                | Punto de acceso y creación de datos por parte del usuario. |
| **Dispositivos**       | PCs de escritorio o portátiles.                        |
| **Sistemas Operativos**| Windows, macOS, Linux                                  |


                      ↓

| Elemento             | Descripción                                                        |
|----------------------|--------------------------------------------------------------------|
| **Rol**              | Almacenamiento centralizado en red local, respaldo con redundancia.|
| **Dispositivos**     | NAS, PC con Linux Server o Windows Server.                         |
| **Tecnología clave** | RAID Interno para proteger contra fallos de disco.                 |


                      ↓

| Elemento                  | Descripción                                                                 |
|---------------------------|-----------------------------------------------------------------------------|
| **Rol**                   | Respaldo externo y acceso remoto seguro a los datos.                        |
| **Dispositivos / Servicios** | NAS con sincronización, PCs conectados a la nube, servicios como Google Drive, Dropbox, AWS. |
| **Tecnología clave**      | Cloud Storage, sincronización automática, backup remoto.                    |



---

🔹 Nivel 1: Clientes o Estaciones de Trabajo
Rol:

Este nivel representa los usuarios finales que interactúan con el sistema.

Son quienes crean, editan y acceden a los datos.

Dispositivos típicos:

Computadoras personales (PCs)

Windows

Mac

Linux


🔹 Nivel 2: Servidor Local o Almacenamiento Interno
Rol:

Actúa como un servidor centralizado local para almacenamiento, respaldo o compartición de archivos dentro de una red local.

Maneja RAID interna, lo que ofrece redundancia y protección de datos.

Dispositivos típicos:

NAS (Network Attached Storage)

PC configurado como servidor con:

Linux Server

Windows Server


🔹 Nivel 3: Servidor en la Nube o Almacenamiento Externo
Rol:

Este nivel se encarga del almacenamiento en la nube o del respaldo remoto.

Proporciona acceso a los datos desde fuera de la red local y protección adicional ante desastres.

Dispositivos / Servicios típicos:

Servidor NAS o PC con sincronización hacia:

Servicios en la nube (Google Drive, Dropbox, OneDrive, etc.)

Plataformas de respaldo online

Servidores virtuales en la nube (AWS, Azure, etc.)


---

## 2. Configuración del Nivel 1: PCs Individuales

### a. Selección de software

- Windows: 
Veeam: Soporta copias incrementales, compresión y cifrado. Ideal para backups empresariales.
Cobian: Open-source, permite programación flexible y es liviano.
EaseUS: Interfaz intuitiva, permite clonación de discos y backups rápidos.

- macOS: 
Time Machine: Integrado en macOS, fácil de configurar y permite recuperación completa del sistema.
Carbon Copy Cloner: Crea copias exactas y booteables, útil para migraciones.

- Linux: 
Timeshift: Similar a Time Machine, ideal para restaurar el sistema.
Deja Dup: Interfaz gráfica sencilla, cifrado incluido.

_(Explica por qué has elegido cada uno.)_

### b. Programación de copias
- ¿Cómo se configuran las copias incrementales diarias?  

Abre Veeam y selecciona "File Level Backup".
Elige las carpetas/archivos a respaldar.
En "Schedule", activa la opción "Daily".
Selecciona "Incremental backup" y programa la hora (ej. 20:00 todos los días).
Guarda la configuración.

- ¿Cómo se hacen las copias completas semanales?

1. Windows (Veeam Agent Free)
Abre Veeam y selecciona "Backup Job" → "Entire Computer" (o "File Level" para carpetas).
En "Schedule", elige "Weekly" y marca el día (ej. Domingo).
Asegúrate de seleccionar "Active Full Backup" (no incremental).
Configura la hora (ej. 23:00) y guarda.

2. macOS (Time Machine o Carbon Copy Cloner)
Time Machine: Ya hace copias completas automáticas cuando el disco está conectado (no necesita configuración adicional).
Carbon Copy Cloner:
Crea una nueva tarea → "Clone" (origen y destino).
En "Schedule", elige "Weekly" y el día.
Selecciona "Delete older backups" para evitar saturar el disco.

3. Linux (Timeshift o rsync)
Timeshift:
Abre Timeshift → "Schedule" → Activa "Weekly".
Elige el día y hora (ej. Domingo a las 22:00).
Asegúrate de que el tipo de backup sea "RSync" (para copias completas eficientes).
rsync + cron:

Edita crontab (crontab -e) y añade:
0 22 * * 0 rsync -a --delete /ruta/origen /ruta/backup_completo
(Se ejecutará todos los domingos a las 22:00).

### c. Destino de las copias
- ¿Cómo se organizan las carpetas por PC?

1. En el servidor:
Crear carpeta principal: /backups
Dentro, hacer una carpeta por PC:
/backups/PC1, /backups/PC2, etc.

2. En cada PC:

Windows (Veeam):
Elegir "Backup a carpeta compartida"
Poner ruta: \\IP_servidor\backups\NombrePC
Poner usuario/contraseña del servidor si pide

Mac (Time Machine):
Ir a Time Machine > Seleccionar disco
Elegir "Conectar servidor"
Poner: smb://IP_servidor/backups/NombreMac

Linux (Terminal):
Montar carpeta del servidor:
sudo mount -t cifs //IP_servidor/backups/NombreLinux /mnt/backup -o user=usuario
Usar rsync para copiar:
rsync -av /tus/archivos /mnt/backup

---

## 3. Configuración del Nivel 2: Servidor Local Primario

### a. Tipo de hardware elegido
 - Opción: NAS / Ordenador (elige uno)
 - Marca/modelo (si aplica): Ordenador Básico de ofimática, o incluso un kit de Xeon
 - Sistema operativo: TrueNas Core


### b. Tipo de RAID
- Elegido: RAID 5
- Justificación: Redundancia de datos, rendimiento mejorado y costo-eficiencia, puede soportar la falla de un disco sin pérdida de datos. La paridad distribuida permite reconstruir los datos perdidos en caso de que un disco falle y ofrece un buen rendimiento en lectura y escritura. Aunque no es tan rápido como RAID 0, que se centra exclusivamente en el rendimiento, RAID 5 ofrece una buena combinación de rendimiento y redundancia. 

### c. Software de gestión
- Software elegido: Urbackup
- Función principal: UrBackup es un sistema Open Source para clientes y servidores que permite realizar copias de seguridad consiguiendo la seguridad de los datos y la restauración en un tiempo mínimo. Se ejecuta a través de Windows o Linux y admite diferentes copias de seguridad en los equipos que tengan instalados su cliente, sin depender del sistema operativo.
- ¿Por qué este software?: Rápido: El cliente, al mantenerse en la constante observación de cambios en los archivos, agiliza el proceso de las copias de seguridad incrementales.

Copias de seguridad: UrBackup crea una copia de seguridad mientras el sistema se ejecuta o está en uso. A través de Internet. Permite la posibilidad de configurarlo para respaldar a clientes a través de Internet, lo que beneficia a las copias de seguridad de dispositivos móviles.

Espacio eficiente: No duplica archivos, esto quiere decir que si varios clientes contienen los mismos archivos serán guardados solo una vez, por tanto, ocupa el mínimo espacio de almacenamiento.

Interfaz web: UrBackup tiene una interfaz integrada que facilita el estado de los clientes, las actividades actuales y las estadísticas. Además, permite observar las copias de seguridad de archivos, así como, extraer y restaurar los archivos dentro de las mismas.

Notificaciones: Mediante correo electrónico para avisar de que una máquina cliente no está respaldada.

Multiplataforma: El servidor UrBackup se ejecuta en Windows, GNU / Linux, FreeBSD y algunos sistemas operativos NAS basados en Linux. El cliente UrBackup se ejecuta en Windows, FreeBSD y GNU / Linux.

---

 ## 4. Configuración del Nivel 3: Servidor Secundario Remoto
 ### a. Tipo de servidor remoto
 
- Opción elegida: NAS remoto / Nube / PC remoto
 - Justificación: NAS Remoto con VPN, y de software de VPN escojo WireGuard por su seguridad y porque es gratis.
 
 ### b. Seguridad de la sincronización

 - ¿Cómo se programa la sincronización?
 Se realiza a través de tareas de sincronización en la nube, que permiten configurar horarios y frecuencias para la transferencia de datos a y desde la nube.
- ¿Qué cifrado se usa?
  Para el cifrado, se utiliza ZFS
 - ¿Se puede activar doble autenticación?
Sí, TrueNas ofrece un sistema de autenticación 2FA
---

## 5. Automatización del Proceso

- Script de verificación: _(pega o enlaza un ejemplo)_

#!/bin/bash

# Configuración básica
BACKUP_DIR="/backups"
LOG_FILE="/tmp/backup_check.log"

# Verificar si existe el backup más reciente
if [ -z "$(ls -A $BACKUP_DIR)" ]; then
    echo "[ERROR] No hay backups en $BACKUP_DIR" > $LOG_FILE
    exit 1
fi

# Obtener el backup más nuevo
LATEST=$(ls -t $BACKUP_DIR | head -1)

# Verificar si el backup está vacío o es muy pequeño
if [ $(du -m "$BACKUP_DIR/$LATEST" | cut -f1) -lt 1 ]; then
    echo "[ERROR] El backup $LATEST está vacío o es muy pequeño" > $LOG_FILE
    exit 1
fi

# Si todo está bien
echo "[OK] Backup verificado: $LATEST" > $LOG_FILE
exit 0

- Alerta por email: ¿Cómo se configura?

#!/bin/bash

# Configuración
BACKUP_DIR="/backups"
EMAIL_ADMIN="admin@tudominio.com"
SERVER_NAME=$(hostname)

# Verificar backups
if [ -z "$(ls -A $BACKUP_DIR)" ]; then
    MENSAJE="[ALERTA] No hay backups en $BACKUP_DIR (Servidor: $SERVER_NAME)"
    echo "$MENSAJE" | mail -s "⚠️ Falla en Backup" "$EMAIL_ADMIN"
    exit 1
fi

# Verificar tamaño mínimo (ejemplo: 1MB)
LATEST=$(ls -t $BACKUP_DIR | head -1)
if [ $(du -m "$BACKUP_DIR/$LATEST" | cut -f1) -lt 1 ]; then
    MENSAJE="[ALERTA] Backup $LATEST está vacío/corrupto (Servidor: $SERVER_NAME)"
    echo "$MENSAJE" | mail -s "⚠️ Backup corrupto" "$EMAIL_ADMIN"
    exit 1
fi

# Si todo está bien (opcional: notificación de éxito)
echo "[OK] Backup $LATEST verificado" | mail -s "✓ Backup OK" "$EMAIL_ADMIN"
exit 0

- Prueba de restauración: ¿Cada cuánto tiempo? ¿Cómo?

📅 Frecuencia recomendada para pruebas de restauración

Tipo de Backup	           Frecuencia de Prueba
Backups críticos          (BDs, servidores)	Semanal o mensual
Backups no críticos       (archivos estáticos)	Trimestral
Entornos regulados        (HIPAA, GDPR) Mensual (obligatorio)

---

## 6. Justificación del uso de RAID
🔹 Actividad:
 Explica por qué el RAID no reemplaza a las copias de seguridad. ¿Qué pasaría si sólo tienes RAID pero no backups?
 Porque por mucha redundancia que haya en un RAID 1, 10 o etc, necesitas tener backups en otro dispositivo de almacenamiento, por si los discos de los backups se corrompen, o por si te hacen un ransomware.
 Si solo tienes RAID y te cifran el disco duro, te quedas sin información, en cambio, si tienes backups simplemente lo restauras.

---

## 7. Resumen de Software Recomendado

| Función                          | Software Recomendado                      |
|----------------------------------|-------------------------------------------|
| **Gestión centralizada**         | **UrBackup**                       |
| **Sincronización entre servidores** |  Duplicity              |
| **Monitorización**               | Grafana + Prometheus   |


---

## Bibliografía / Fuentes consultadas

- Fuente 1: [______________________](https://www.profesionalreview.com/mejores-nas-del-mercado/)
- Fuente 2: [______________________](https://www.xataka.com/basics/servidores-nas-que-como-funcionan-que-puedes-hacer-uno)
- Fuente 3: [[______________________]](https://www.tecnozero.com/servidor/tipos-de-raid-cual-elegir/)
- Fuente 4: [[______________________]](https://www.urbackup.org/features.html)
- Fuente 5: [[______________________]](https://cloud.google.com/discover/what-is-prometheus?hl=es-419)
- Fuente 6: [[______________________]](https://wiki.archlinux.org/title/Duplicity)
- Fuente 7: [[[______________________]]](https://genos.es/urbackup-soporte/#:~:text=UrBackup%20es%20un%20software%20para,seguridad%20rápido%2C%20seguro%20y%20eficaz)
