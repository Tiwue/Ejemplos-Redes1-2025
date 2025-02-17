# Configuración Básica de Switches Cisco

Este documento proporciona ejemplos de configuraciones básicas para switches Cisco.

## 1. Configuración Inicial

```bash
# Acceder al modo de configuración global
enable
configure terminal

# Configurar el nombre del switch
hostname NuevoNombre

# Deshabilitar la búsqueda DNS para evitar demoras por errores al escribir
do no ip domain-lookup

# Crear una contraseña para el modo privilegiado
enable secret MiContrasena


# Guardar la configuración
write memory
```

## 2. Verificación de Configuraciones

```bash
# Mostrar la configuración actual
show running-config

# Mostrar la configuración que se carga en el arranque del switch
show startup-config

# Verificar los puertos activos
show interfaces status
```

## 3.Diferencia entre `show running-config` y `show startup-config`

La diferencia entre `show running-config` y `show startup-config` en switches y routers Cisco radica en **dónde está almacenada la configuración y cuándo se aplica**:

1. **`show running-config`**  
   - Muestra la configuración **actual** en ejecución en la memoria RAM del dispositivo.  
   - Cualquier cambio que se haga en el modo de configuración global se refleja de inmediato en `running-config`.  
   - Si el dispositivo se reinicia sin guardar los cambios, esta configuración **se pierde**.  

2. **`show startup-config`**  
   - Muestra la configuración **guardada** en la memoria **NVRAM** (memoria no volátil).  
   - Esta es la configuración que el dispositivo cargará **cuando se reinicie**.  
   - Para guardar cambios de `running-config` en `startup-config`, se debe usar el comando:  

     ```bash
     write memory
     ```

     o  

     ```bash
     copy running-config startup-config
     ```

### 🛠️ Resumen

| Comando                | Ubicación        | Se pierde al reiniciar | Se aplica al arrancar |
|------------------------|-----------------|------------------------|-----------------------|
| `show running-config`  | RAM             | ✅ Sí                   | ❌ No                 |
| `show startup-config`  | NVRAM           | ❌ No                   | ✅ Sí                 |

Para **preservar cambios**, recuerden siempre guardarlos con `write memory`.
