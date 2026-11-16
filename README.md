# Evaluación del consumo de recursos de GNU/Linux antes y después de detener temporalmente un servicio no esencial y desarrollo de un servidor web local en una máquina virtual

**Universidad Manuela Beltrán · Sistemas Operativos · Grupo 1**  
**Integrantes:** Nelson Julián Martínez Bedoya, Mauricio Esteban Varela Cañón y William David Vivas Maldonado  
**Repositorio:** [prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1](https://github.com/Estteban-xc/prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1 )

## Descripción del microproyecto

El proyecto evalúa el consumo de recursos de una máquina virtual GNU/Linux mediante la observación del sistema antes y después de detener temporalmente un servicio que el equipo no utiliza. También implementa un portal web estático local para consultar materiales del proyecto.

La evaluación se realizó en una instalación persistente de Ubuntu 26.10 ejecutada en Oracle VirtualBox. El servicio analizado fue `cups-browsed`, relacionado con el descubrimiento de impresoras CUPS en red. Como el equipo no utiliza impresoras, el servicio se detuvo temporalmente durante las mediciones y se inició nuevamente al finalizar. No se deshabilitó de forma permanente ni se desinstaló.

El proyecto no modifica el kernel ni plantea una optimización general del sistema. Su propósito es registrar y analizar una intervención pequeña, reversible y limitada a una máquina virtual.

## Necesidad y justificación

Un sistema GNU/Linux puede mantener activos servicios que no se utilizan en una configuración particular. Sin embargo, detener un servicio sin conocer su función puede afectar otras tareas o componentes del sistema. Por ello, el proyecto utiliza una máquina virtual de prueba, registra su configuración y realiza una comparación reversible de un servicio que el equipo no requiere.

La medición permite observar qué ocurrió en ese entorno específico y documentar sus limitaciones. Los resultados no deben generalizarse a otros equipos, distribuciones o cargas de trabajo.

## Objetivos

### Objetivo general

Evaluar el consumo de recursos de una máquina virtual GNU/Linux antes y después de detener temporalmente el servicio `cups-browsed`, y desarrollar un portal web local para consultar los materiales del microproyecto.

### Objetivos específicos

- Registrar la configuración de hardware y software de la máquina virtual.
- Observar la memoria disponible y el uso de memoria informado para el servicio mientras está activo y mientras se encuentra detenido temporalmente.
- Restaurar el servicio al terminar la prueba y verificar que continúe habilitado para el inicio automático.
- Implementar un portal estático con HTML y CSS, servido localmente con Python 3.
- Documentar los resultados y sus limitaciones sin atribuir al servicio cambios que no puedan demostrarse.

## Entorno de ejecución

| Elemento | Configuración registrada |
|---|---|
| Sistema operativo invitado | Ubuntu 26.10 (Stonking Stingray), instalación persistente |
| Hipervisor | Oracle VirtualBox 7.2.22 r175245 en Windows |
| CPU virtual | 1 vCPU |
| Memoria asignada | 2048 MB |
| Disco virtual | 25 GB, formato VDI |
| Red virtual | NAT |
| Python | 3.14.8 |
| systemd | 261 (261.3-0ubuntu3) |
| Servicio evaluado | `cups-browsed` |

El registro detallado se encuentra en [`entorno_vm/configuracion_vm.csv`](entorno_vm/configuracion_vm.csv). El disco virtual, las ISO y los snapshots de VirtualBox no forman parte del repositorio.

## Solución implementada

La solución está compuesta por los siguientes elementos:

1. **Máquina virtual:** entorno persistente con Ubuntu 26.10 en VirtualBox.
2. **Evaluación del servicio:** comparación de `cups-browsed` en estado activo y detenido temporalmente, con posterior restauración.
3. **Portal web local:** página estática desarrollada en `src/codigo_fuente/index.html`, con estructura HTML y estilos CSS.
4. **Servidor web:** módulo estándar `http.server` de Python 3, utilizado para servir temporalmente los archivos en la interfaz local `127.0.0.1`.
5. **Registros:** archivos CSV con la configuración de la VM y las mediciones observadas.

El portal se comprobó desde la misma máquina virtual. La solicitud a la página respondió con estado **HTTP 200**. Esta prueba confirma la respuesta de la página principal; no implica por sí sola que todos los enlaces internos hayan sido verificados.

## Metodología resumida

La configuración de la VM se mantuvo constante durante la comparación. Se registraron tres lecturas con `cups-browsed` activo, se detuvo temporalmente el servicio y se registraron tres lecturas adicionales. Después, el servicio se inició nuevamente y se verificó que quedara en estado `active` y `enabled`.

La prueba del portal se realizó por separado para no mezclar el consumo del proceso Python con la comparación del servicio. El tiempo de arranque se registró como una observación independiente y no se utilizó para atribuir un cambio de rendimiento a `cups-browsed`.

Las mediciones están disponibles en [`pruebas/plantilla_mediciones.csv`](pruebas/plantilla_mediciones.csv).

## Resultados

### Comparación de `cups-browsed`

| Condición | Lecturas | Memoria disponible | `MemoryCurrent` |
|---|---:|---:|---:|
| Servicio activo | 3 | 1097 MiB | 13 934 592 bytes |
| Servicio detenido temporalmente | 3 | 1099 MiB | No establecido mientras la unidad está inactiva |

La diferencia observada en memoria disponible fue de **2 MiB**. Es una variación pequeña registrada en esta VM y no demuestra un ahorro estable ni una mejora general. El servicio se inició nuevamente y quedó `active` y `enabled`.

### Portal local

La solicitud de prueba al portal respondió **HTTP 200** y devolvió el HTML esperado. El proceso Python registró los siguientes valores de RSS en tres observaciones: **23 360 KB, 22 496 KB y 22 504 KB**. En la lectura registrada con el servidor detenido, el proceso no apareció en la consulta de procesos. La memoria disponible del sistema fue de **1184 MiB** tanto en las lecturas con el portal activo como con el servidor detenido; con estos datos no se observó una variación a la resolución utilizada.

### Tiempo de arranque

`systemd-analyze time` informó un tiempo total de arranque de **17,831 s**: 1,123 s para el kernel, 2,003 s para initrd y 14,705 s para userspace. Se trata de una medición independiente y única, insuficiente para relacionar el tiempo de inicio con el estado de `cups-browsed`.

## Tecnologías utilizadas

- **Oracle VirtualBox:** virtualización del entorno de prueba.
- **Ubuntu GNU/Linux:** sistema operativo invitado.
- **systemd y systemctl:** consulta y control temporal del servicio.
- **Python 3 y `http.server`:** servidor web local temporal.
- **HTML y CSS:** implementación del portal.
- **`free -m` y `systemd-analyze`:** consulta de memoria y registro del tiempo de arranque.
- **Git y GitHub:** organización y almacenamiento del código y la documentación.

## Estructura del repositorio

La entrega reúne el código fuente, los archivos de configuración y medición, los diagramas y un único informe consolidado en PDF. El nombre del archivo PDF conserva el título completo del proyecto.

```text
.
├── README.md
├── LICENSE
├── documentacion/
│   ├── [informe final consolidado en PDF]
│   └── diagramas/
├── entorno_vm/
│   └── configuracion_vm.csv
├── src/
│   └── codigo_fuente/
│       └── index.html
└── pruebas/
    └── plantilla_mediciones.csv
```

El informe final puede consultarse en el [PDF consolidado](documentacion/Evaluaci%C3%B3n%20del%20consumo%20de%20recursos%20de%20GNU-Linux%20antes%20y%20despu%C3%A9s%20de%20detener%20temporalmente%20un%20servicio%20no%20esencial%20y%20desarrollo%20de%20un%20servidor%20web%20local%20en%20una%20m%C3%A1quina%20virtual.pdf).

La VM, su disco virtual y sus snapshots permanecen fuera del repositorio.

## Alcance y limitaciones

La evaluación corresponde a una sola VM y a un único servicio. No se compararon varias distribuciones, equipos anfitriones, cargas de trabajo ni servicios adicionales. No se realizó una medición controlada de CPU por intervalo ni una prueba de rendimiento con tráfico concurrente.

La memoria disponible del sistema y `MemoryCurrent` describen indicadores distintos y no son medidas intercambiables. Además, `CPUUsageNSec` es un contador acumulado; las lecturas realizadas no permiten calcular por sí solas una tasa de uso de CPU.

Por estas razones, los resultados describen únicamente las condiciones observadas. No permiten concluir que detener otros servicios produzca mayores ahorros ni que el mismo efecto ocurra en otros equipos.

## Seguridad

El portal se sirvió únicamente en `127.0.0.1`; no se configuró para escuchar en `0.0.0.0` ni para recibir conexiones mediante reenvío de puertos. El servicio `cups-browsed` se detuvo temporalmente y se restauró al finalizar; no se eliminó ni se deshabilitó permanentemente.

## Licencia

El proyecto se distribuye bajo la licencia MIT. Consulta el archivo [`LICENSE`](LICENSE).
