# Microproyecto: evaluación de recursos GNU/Linux y portal local en una VM

**Universidad Manuela Beltrán · Sistemas Operativos · Grupo 1**  
**Integrantes:** Nelson Julián Martínez Bedoya, Mauricio Esteban Varela Cañón y William David Vivas Maldonado.  
**Tiempo estimado:** 2–3 días.  
**Repositorio:** `prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1`

## Descripción contextual y técnica

El microproyecto se desarrollará en una máquina virtual (VM) GNU/Linux disponible para el equipo. Se registrará la configuración de esa VM y se observarán indicadores básicos de memoria y arranque. Como utilidad práctica, se construirá un portal estático en HTML/CSS que reúne enlaces al README, la documentación del proyecto y los archivos de prueba. La página se servirá temporalmente con Python 3 desde la raíz del repositorio y solo será accesible desde la propia VM, mediante `127.0.0.1`.

El producto no es una nueva distribución Linux ni un optimizador automático. No se modifica el kernel. Un cambio en un servicio del sistema es opcional y solo se considerará si el equipo identifica su función y confirma que el cambio es seguro y reversible. No se presupone que las pruebas produzcan ahorro de recursos.

## Necesidad y problemática

Para estudiar Sistemas Operativos resulta útil observar procesos y recursos en un entorno realista, pero modificar servicios sin conocer su función puede afectar el funcionamiento del sistema. Además, instalar un servidor web completo o construir una aplicación compleja excedería el tiempo disponible para este microproyecto.

La propuesta aborda esa necesidad con una prueba pequeña y aislada: utiliza una VM existente, registra su configuración, sirve un portal estático con herramientas estándar y compara observaciones con el servidor detenido y activo. Los resultados describirán únicamente la VM probada; no se generalizarán a otros equipos.

## Objetivo

**Evaluar** el comportamiento de una VM GNU/Linux al ejecutar un portal web local ligero y registrar indicadores básicos del sistema, considerando como opcional un cambio seguro y reversible en un servicio.

## Funcionalidades principales

- Registrar distribución, versión, hipervisor y recursos asignados a la VM en `entorno_vm/configuracion_vm.csv`.
- Mostrar un portal local con enlaces al README, los tres documentos PDF, los diagramas y las plantillas CSV.
- Servir temporalmente el repositorio con Python 3 en `127.0.0.1`, sin exponerlo a la red local o a Internet.
- Registrar memoria con `free -h` con el servidor detenido y activo.
- Registrar el indicador de arranque con `systemd-analyze time`.
- Guardar las observaciones reales en `pruebas/plantilla_mediciones.csv`.
- Probar, solo si se justifica y es reversible, un cambio opcional dentro de la VM; restaurar el snapshot local al finalizar.

## Estructura de trabajo del prototipo

1. **Preparar el entorno:** seleccionar una VM GNU/Linux existente y anotar su distribución, versión, hipervisor, CPU, memoria, disco y modo de red en `entorno_vm/configuracion_vm.csv`.
2. **Crear un punto de recuperación:** generar un snapshot local antes de cualquier cambio. El snapshot no se sube al repositorio.
3. **Tomar la línea base:** con el servidor detenido, ejecutar `free -h` y `systemd-analyze time`; anotar las condiciones en la plantilla de mediciones.
4. **Ejecutar la utilidad:** iniciar el portal local, abrirlo desde el navegador de la misma VM y comprobar los enlaces a la documentación y los archivos del equipo.
5. **Medir el uso activo:** mientras el servidor está activo, registrar nuevamente `free -h`; detener el servidor con `Ctrl+C`.
6. **Analizar y documentar:** comparar las observaciones y explicar sus límites. Si no se identifica un servicio cuyo cambio sea claramente seguro y reversible, no cambiar ninguno.
7. **Cerrar la prueba:** restaurar el snapshot si hubo modificaciones y guardar capturas reales en `evidencias/capturas/`.

## Tecnologías y herramientas previstas

- **Sistema operativo:** GNU/Linux con `systemd`, ejecutado como sistema invitado de una VM.
- **Virtualización:** VirtualBox, VMware o KVM, según la herramienta disponible en el equipo. La configuración usada se anotará en el CSV.
- **Portal:** HTML y CSS para la página estática.
- **Servidor local:** Python 3 y el módulo estándar `http.server`; no se requieren frameworks ni bases de datos.
- **Observación del sistema:** `free -h` para consultar memoria; `systemd-analyze time` para registrar el indicador de arranque; `systemctl` solo para inspeccionar un servicio candidato, si aplica.
- **Registro de datos:** archivos CSV separados para configuración de la VM y mediciones.
- **Control de versiones y entrega:** Git y GitHub.
- **Evidencias:** capturas de pantalla reales guardadas por el equipo en `evidencias/capturas/`.

## Documentación del proyecto

Cada documento cubre un aspecto distinto y se entrega en PDF, sin copias equivalentes en Word o Markdown:

- [Descripción del proyecto](documentacion/descripcion_proyecto.pdf): necesidad, justificación, objetivos, alcance y beneficiarios.
- [Requerimientos](documentacion/requerimientos.pdf): requisitos funcionales y no funcionales, tecnologías y criterios de aceptación.
- [Arquitectura inicial](documentacion/arquitectura_inicial.pdf): componentes, estructura del repositorio, flujo de trabajo, configuración y recuperación de la VM.

Los diagramas están en `documentacion/diagramas/` y también se incluyen en el PDF de arquitectura.

## Estructura del repositorio

```text
prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1/
├── README.md
├── LICENSE
├── .gitignore
├── documentacion/
│   ├── descripcion_proyecto.pdf
│   ├── requerimientos.pdf
│   ├── arquitectura_inicial.pdf
│   └── diagramas/
│       ├── arquitectura_inicial.png
│       └── estructura_producto.png
├── entorno_vm/
│   └── configuracion_vm.csv       # Registro, no imagen de la VM
├── src/
│   └── codigo_fuente/
│       └── index.html             # Portal estático
├── pruebas/
│   └── plantilla_mediciones.csv
└── evidencias/
    └── capturas/                  # Capturas reales del equipo
```

Esta organización se adapta al trabajo en una VM: el repositorio conserva la configuración descriptiva y las evidencias, pero no el disco virtual ni los snapshots, que permanecen localmente en el computador de cada integrante. Los archivos de máquinas virtuales e ISO están excluidos en `.gitignore`. No se crea una carpeta genérica de recursos de imágenes porque los diagramas y las capturas ya tienen carpetas específicas.

## Ejecutar el portal dentro de la VM

Desde la raíz del repositorio, iniciar el servidor temporal:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

En el navegador de la misma VM abrir:

```text
http://127.0.0.1:8000/src/codigo_fuente/
```

Al terminar, regresar a la terminal del servidor y pulsar `Ctrl+C`. No usar `0.0.0.0`, no reenviar puertos y no publicar el servidor en la red local o en Internet. Antes de servir el repositorio, comprobar que no contenga credenciales ni datos personales.

## Repositorio y estado actual

El repositorio público del equipo se llama `prototipo_evaluacion_recursos_vm_gnu_linux_sistemas_operativos_g1`. La propuesta y las plantillas están organizadas, pero aún no se han registrado mediciones experimentales. El equipo debe completar los CSV con datos reales, añadir sus capturas y registrar los avances posteriores mediante commits propios.
