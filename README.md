# Microproyecto: recursos GNU/Linux y utilidad local en una VM

**Universidad Manuela Beltrán · Sistemas Operativos**  
**Integrantes:** Nelson Julián Martínez Bedoya, Mauricio Esteban Varela Cañón y William David Vivas Maldonado.  
**Tiempo estimado:** 2–3 días.

El proyecto se ejecutará en una máquina virtual (VM) GNU/Linux que ya tenga el equipo. Se registrarán sus características y algunos indicadores de memoria/arranque, y se demostrará un portal HTML local servido temporalmente con Python 3. Un cambio de servicio es opcional y solo se hará si se comprueba que es seguro y reversible.

## Documentos en PDF

Cada archivo trata un aspecto distinto; no hay copias equivalentes en Word o Markdown:

- [Descripción del proyecto](documentacion/descripcion_proyecto.pdf): necesidad, justificación, objetivos, alcance y beneficiarios.
- [Requerimientos](documentacion/requerimientos.pdf): requisitos funcionales/no funcionales, tecnologías y criterios de aceptación.
- [Arquitectura inicial](documentacion/arquitectura_inicial.pdf): organización, componentes, flujo de pruebas, configuración y recuperación de la VM.

Los diagramas están en `documentacion/diagramas/` y también se insertan en el PDF de arquitectura.

## Por qué la estructura se adapta a la VM

La VM no se sube a GitHub: su disco y sus snapshots pueden ser muy pesados y son locales a cada integrante. En cambio, `entorno_vm/configuracion_vm.csv` permite registrar distribución, hipervisor, CPU, RAM, disco y nombre local del snapshot sin incluir esos archivos. `pruebas/` conserva las mediciones; `evidencias/capturas/`, las capturas tomadas por el equipo. Los diagramas ya tienen una carpeta propia, por lo que no se crea una carpeta genérica de imágenes.

## Ejecutar el portal desde la VM

Desde la raíz del repositorio:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

Abrir `http://127.0.0.1:8000/src/codigo_fuente/` en el navegador de la misma VM. El portal enlaza a los tres PDF, el README, el CSV de configuración y la plantilla de mediciones. Detener el servidor con `Ctrl+C`. No usar `0.0.0.0`, no reenviar puertos y no publicar el servidor en Internet o LAN. Antes de servir el repositorio, comprobar que no contenga datos privados.

## Estructura del repositorio

```text
evaluacion-consumo-recursos-gnu-linux/
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
│   └── configuracion_vm.csv       # Registro, no imagen de VM
├── src/
│   └── codigo_fuente/
│       └── index.html             # Portal fuente
├── pruebas/
│   └── plantilla_mediciones.csv
└── evidencias/
    └── capturas/                  # Evidencias reales del equipo
```

No se versionan archivos de discos virtuales (`.vdi`, `.vmdk`, `.ova`), snapshots ni ISO. Tampoco se crea `recursos/imagenes/`: las imágenes de arquitectura están en `documentacion/diagramas/` y las capturas en `evidencias/capturas/`.

## GitHub y estado

Crear un repositorio público vacío llamado `evaluacion-consumo-recursos-gnu-linux`, extraer el ZIP y cargar su contenido. Verificar que los tres PDF, el portal, los CSV y el README estén visibles. El equipo aún debe registrar la configuración y completar las mediciones con datos reales; los commits posteriores deben corresponder a avances propios.
