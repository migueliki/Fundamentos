# Sistema de Copias de Seguridad Automatizadas Multicapa

## 1. Arquitectura General del Sistema

- Nivel 1: ______________________________________
- Nivel 2: ______________________________________
- Nivel 3: ______________________________________

_(Explica brevemente la función de cada nivel. Puedes añadir un esquema o diagrama ASCII si lo deseas.)_

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
- Marca/modelo (si aplica): ________________
- Sistema operativo: ________________________

### b. Tipo de RAID
- Elegido: RAID 1 / RAID 5 / RAID 10  
- Justificación:

### c. Software de gestión
- Software elegido: __________________________  
- Función principal: __________________________

---

## 4. Configuración del Nivel 3: Servidor Secundario Remoto

### a. Tipo de servidor remoto
- Opción elegida: NAS remoto / Nube / PC remoto  
- Justificación:

### b. Seguridad de la sincronización
- ¿Cómo se programa la sincronización?
- ¿Qué cifrado se usa?
- ¿Se puede activar doble autenticación?

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

- ¿Por qué no sustituye al backup?
- ¿Qué pasa si solo tenemos RAID?

---

## 7. Resumen de Software Recomendado

| Función                        | Software Recomendado     |
|-------------------------------|--------------------------|
| Gestión centralizada          |                          |
| Sincronización entre servidores|                          |
| Monitorización                |                          |

_(Puedes añadir más si lo crees necesario)_

---

## Bibliografía / Fuentes consultadas

- Fuente 1: ______________________
- Fuente 2: ______________________
- ...

