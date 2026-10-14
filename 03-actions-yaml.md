# 3. GitHub Actions: estructura y YAML

## 3.1. Conceptos

- **Workflow:** archivo de automatización.
- **Evento:** acción que activa el workflow.
- **Job:** trabajo dentro del workflow.
- **Runner:** máquina donde se ejecuta un job.
- **Step:** paso dentro de un job.
- **Action:** componente reutilizable invocado normalmente con `uses`.
- **Artifact:** archivo generado y guardado por el workflow.

Los workflows se guardan en `.github/workflows/`, con extensión `.yml` o `.yaml`.

Crear la ruta:

```bash
mkdir -p .github/workflows
nano .github/workflows/mi-workflow.yml
```

## 3.2. Estructura general completa

```yaml
name: Mi workflow

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Descargar repositorio
        uses: actions/checkout@v4

      - name: Mostrar mensaje
        run: echo "Workflow ejecutado"
```

## 3.3. Significado de los elementos

| Elemento | Significado |
|---|---|
| `name` | Nombre del workflow o de un paso |
| `on` | Eventos que activan el workflow |
| `push` | Activación al subir cambios |
| `branches: [main]` | Limita la activación a la rama `main` |
| `workflow_dispatch` | Permite ejecución manual desde GitHub |
| `jobs` | Conjunto de trabajos |
| `build` | Identificador del job; puede tener otro nombre |
| `runs-on` | Runner donde se ejecuta |
| `steps` | Pasos ejecutados en orden dentro del job |
| `uses` | Utiliza una acción |
| `run` | Ejecuta un comando de terminal |
| `with` | Configura parámetros de una acción |
| `env` | Define variables de entorno |
| `needs` | Define dependencias entre jobs |
| `if` | Condiciona la ejecución |
| `permissions` | Define permisos del token del workflow |
| `strategy.matrix` | Repite un job para varias combinaciones de valores |
| `secrets.NOMBRE` | Accede a un secreto configurado en GitHub |

## 3.4. Eventos

Solo al hacer push a main:

```yaml
on:
  push:
    branches: [main]
```

Ejecución manual:

```yaml
on:
  workflow_dispatch:
```

Ambos:

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

La rama debe coincidir exactamente con la que pida el enunciado.

## 3.5. Jobs, runners y steps

```yaml
jobs:
  compilar:
    runs-on: ubuntu-latest
    steps:
      - name: Descargar código
        uses: actions/checkout@v4

      - name: Compilar
        run: make
```

Runners habituales:

- `ubuntu-latest`: Linux.
- `windows-latest`: Windows.
- `macos-latest`: macOS.

Los pasos dentro de un job se ejecutan en orden. Los jobs diferentes pueden ejecutarse en paralelo salvo que se configure una dependencia.

## 3.6. Diferencia entre uses, run y with

```yaml
- uses: actions/checkout@v4
```

Usa una acción para descargar el código del repositorio en el runner.

```yaml
- run: make
```

Ejecuta un comando en la terminal del runner.

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.12'
```

Usa una acción y le pasa parámetros mediante `with`.

## 3.7. Variables y secretos

Variable de entorno:

```yaml
env:
  DESTINATION_PERSON: "2DAW"
```

Usarla en un comando:

```yaml
- run: echo "$DESTINATION_PERSON"
```

Usar un secreto:

```yaml
- name: Ejecutar script con token
  env:
    TOKEN: ${{ secrets.TELEGRAM_TOKEN }}
  run: python enviar_mensaje.py
```

El secreto debe configurarse en GitHub. Nunca se debe escribir su valor directamente en el YAML.

## 3.8. Dependencias y condiciones

Un job que depende de otro:

```yaml
jobs:
  linter:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Comprobando código"

  tests:
    needs: linter
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Ejecutando pruebas"
```

Condiciones frecuentes:

- `if: always()`: ejecutar incluso si hay fallos previos.
- `if: success()`: ejecutar si los pasos previos han tenido éxito.
- `if: failure()`: ejecutar si falla algún paso previo relevante.
- `if: steps.test.outcome == 'failure'`: comprobar si el paso con ID `test` falló.

Para referenciar un paso mediante `steps.test`, debe tener `id: test`.

```yaml
steps:
  - name: Ejecutar pruebas
    id: test
    run: npm test

  - name: Avisar del error
    if: failure()
    run: echo "Las pruebas han fallado"
```

## 3.9. Matriz de versiones

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm test
```

La matriz repite el job con cada versión indicada.

## 3.10. Artefactos

Subir un archivo generado:

```yaml
- name: Subir ejecutable
  uses: actions/upload-artifact@v4
  with:
    name: programa-compilado
    path: estadistiques
    retention-days: 7
```

Descargarlo en otro job:

```yaml
- name: Descargar ejecutable
  uses: actions/download-artifact@v4
  with:
    name: programa-compilado
    path: ./descarga
```

- `name`: nombre del artefacto.
- `path`: ruta del archivo a guardar o destino de descarga.
- `retention-days`: días que se conserva.

Un artefacto no es lo mismo que un commit ni que una web publicada.

## 3.11. Errores frecuentes

- Indentación incorrecta: YAML depende de los espacios.
- Usar tabulaciones en YAML.
- Guardar el archivo fuera de `.github/workflows/`.
- Nombre de rama incorrecto.
- Usar `uses` para ejecutar un comando o `run` para invocar una acción.
- Olvidar checkout cuando se necesitan los archivos del repositorio.
- Ejecutar `npm test` sin configurar el script y las dependencias.
- Subir un artefacto desde una ruta que no existe.
- Usar un secreto no configurado.
- Olvidar `needs` cuando un job depende de otro.

**En el examen:** conserva las versiones de las acciones indicadas en el enunciado, aunque en proyectos nuevos existan versiones más recientes.