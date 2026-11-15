# Evaluación del consumo de recursos de GNU/Linux antes y después de desactivar servicios no esenciales

**Universidad Manuela Beltrán · Sistemas Operativos · Grupo 1**  
**Integrantes:** Nelson Julián Martínez Bedoya, Mauricio Esteban Varela Cañón y William David Vivas Maldonado.  
**Repositorio:** `prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1`

## Descripción del microproyecto

El proyecto evalúa recursos de una máquina virtual GNU/Linux mediante una comparación antes y después de detener temporalmente un servicio que el equipo no utiliza. La prueba se realizó en una instalación persistente de Ubuntu 26.10 dentro de Oracle VirtualBox. Como utilidad complementaria, se implementó un portal estático HTML/CSS que el equipo sirve localmente con Python 3 para consultar los materiales del repositorio.

La prueba del servicio se hizo con `cups-browsed`, que descubre impresoras CUPS de red. El equipo confirmó que no utiliza impresoras. El servicio se detuvo solo durante las lecturas y luego se volvió a iniciar; no se deshabilitó al arranque ni se desinstaló. El propósito es observar y reportar el efecto en esta VM, no prometer ahorros generales ni modificar el kernel.

## Necesidad y objetivo

En un sistema GNU/Linux pueden quedar activos servicios que el usuario no necesita. Sin embargo, detener servicios sin conocer su función puede afectar el sistema. Este microproyecto usa una VM y un cambio temporal, reversible y limitado para observar recursos de forma segura.

**Objetivo:** evaluar el consumo de recursos de una VM GNU/Linux antes y después de detener temporalmente el servicio `cups-browsed`, comprobar que las tareas básicas continúen funcionando y complementar la demostración con un portal web local ligero.

## Entorno probado

| Elemento | Valor registrado |
|---|---|
| Sistema invitado | Ubuntu 26.10 (Stonking Stingray), instalación persistente |
| Hipervisor | Oracle VirtualBox 7.2.22 r175245 en Windows |
| CPU virtual | 1 vCPU |
| Memoria asignada | 2048 MB |
| Disco virtual | 25 GB (VDI) |
| Red virtual | NAT |
| Python | 3.14.8 |
| systemd | 261.3-0ubuntu3 |
| Servicio evaluado | `cups-browsed` |

El registro detallado está en [`entorno_vm/configuracion_vm.csv`](entorno_vm/configuracion_vm.csv). No se incluyen la ISO, el disco virtual ni snapshots en el repositorio.

## Solución y componentes

1. **Máquina virtual:** Ubuntu persistente ejecutado en VirtualBox.
2. **Evaluación del servicio:** comparación de `cups-browsed` en estado activo y detenido temporalmente, usando `free -m` y `systemctl show`.
3. **Portal local:** página HTML/CSS en `src/codigo_fuente/index.html`, servida por el módulo estándar `http.server` de Python 3.
4. **Registro:** configuración y lecturas en archivos CSV dentro de `entorno_vm/` y `pruebas/`.

El portal se limita a `127.0.0.1`; no se expone a la red local ni a Internet.

## Procedimiento de la prueba del servicio

Se identificó `cups-browsed` como candidato porque el equipo no usa impresoras. Se tomaron tres lecturas con el servicio activo. Después se ejecutó `sudo systemctl stop cups-browsed.service` y se tomaron tres lecturas mientras estaba detenido. Finalmente se restauró con `sudo systemctl start cups-browsed.service` y se verificó que estuviera `active` y `enabled`.

Comandos de consulta utilizados:

```bash
free -m
systemctl show cups-browsed.service -p ActiveState -p MemoryCurrent -p CPUUsageNSec
systemctl is-active cups-browsed.service
systemctl is-enabled cups-browsed.service
```

`CPUUsageNSec` es un contador acumulado y no se interpretó como consumo de CPU por intervalo. El CSV registra las salidas obtenidas y sus tiempos.

## Resultados observados

En las tres lecturas con el servicio activo, `free -m` reportó **1097 MiB disponibles** y `MemoryCurrent` reportó **13 934 592 bytes** para el servicio. En las tres lecturas con `cups-browsed` detenido temporalmente, se reportaron **1099 MiB disponibles** y `MemoryCurrent` quedó sin establecer. La diferencia observada de memoria disponible fue de **2 MiB**. El servicio se reinició y se confirmó nuevamente `active` y `enabled`.

El resultado indica que el servicio ocupa memoria mientras está activo y que, en las condiciones observadas, detenerlo coincidió con un aumento pequeño de la memoria disponible. No se concluye que otros servicios produzcan un ahorro mayor ni que el mismo resultado aplique a otros equipos.

En una medición de arranque independiente, `systemd-analyze time` informó **17,831 s**: 1,123 s de kernel, 2,003 s de initrd y 14,705 s de userspace. Esa lectura no se usó para atribuir un cambio en el tiempo de arranque a `cups-browsed`.

Los registros están en [`pruebas/plantilla_mediciones.csv`](pruebas/plantilla_mediciones.csv). Las pruebas del portal se registran por separado de la comparación del servicio.

## Portal local

Desde la raíz del repositorio, inicia el servidor:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

En el navegador de la misma VM, abre:

```text
http://127.0.0.1:8000/src/codigo_fuente/
```

La petición de prueba al portal devolvió **HTTP 200** y el HTML esperado. En la VM, si `curl` no está instalado, puede verificarse con Python:

```bash
python3 -c "from urllib.request import urlopen; r=urlopen('http://127.0.0.1:8000/src/codigo_fuente/'); print('HTTP', r.status); print(r.read().decode()[:500])"
```

Detén el servidor con `Ctrl+C`. No cambies `127.0.0.1` por `0.0.0.0`, no configures reenvío de puertos y no sirvas archivos con credenciales o datos personales.

## Estructura del repositorio

```text
├── README.md
├── LICENSE
├── documentacion/
│   ├── descripcion_proyecto.pdf
│   ├── requerimientos.pdf
│   ├── arquitectura_inicial.pdf
│   └── diagramas/
├── entorno_vm/
│   └── configuracion_vm.csv
├── src/
│   └── codigo_fuente/
│       └── index.html
└── pruebas/
    └── plantilla_mediciones.csv
```

La documentación PDF y los diagramas están en `documentacion/`. El portal contiene enlaces locales a los recursos del repositorio.

## Alcance y limitaciones

La evaluación se limita a una VM con una configuración específica y a un solo servicio que el equipo no utiliza. El servicio se detuvo temporalmente y se restauró; no se hizo una desactivación permanente. Las variaciones pequeñas de memoria disponible no se deben generalizar. No se modificó el kernel, no se desactivaron servicios esenciales y no se pretende reemplazar una evaluación de rendimiento controlada en múltiples equipos.

## Estado final

Se verificó la instalación persistente de Ubuntu, el funcionamiento del portal local, la respuesta HTTP 200, la medición independiente del arranque y la comparación de `cups-browsed` activo/detenido. Al finalizar, el servicio quedó activo y habilitado. Para la entrega final, el video técnico debe mostrar el entorno, el procedimiento, los resultados reales y sus limitaciones.
