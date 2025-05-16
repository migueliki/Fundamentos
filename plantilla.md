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
- ¿Cómo se hacen las copias completas semanales?

### c. Destino de las copias
- ¿Dónde se almacenan?  
- ¿Cómo se organizan las carpetas por PC?

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
- Alerta por email: ¿Cómo se configura?
- Prueba de restauración: ¿Cada cuánto tiempo? ¿Cómo?

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

