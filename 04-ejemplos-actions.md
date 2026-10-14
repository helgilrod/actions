# 4. Ejemplos de workflows de clase

## 4.1. Compilar C con Make

Archivo: `.github/workflows/compilar.yml`.

```yaml
name: Compilar programa C

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  compilar:
    runs-on: ubuntu-latest
    steps:
      - name: Descargar repositorio
        uses: actions/checkout@v4

      - name: Comprobar GCC
        run: gcc --version

      - name: Compilar programa
        run: make

      - name: Ejecutar prueba
        run: make test

      - name: Guardar ejecutable
        uses: actions/upload-artifact@v4
        with:
          name: estadistiques
          path: estadistiques
          retention-days: 7
```

Este ejemplo presupone un `Makefile` que compila el programa y define el objetivo `test`.

### Makefile de ejemplo

```makefile
CC = gcc
CFLAGS = -Wall -Wextra -Werror

all: estadistiques

estadistiques: estadistiques.c
	$(CC) $(CFLAGS) estadistiques.c -o estadistiques

test: estadistiques
	./estadistiques

clean:
	rm -f estadistiques
```

Las líneas de comandos del Makefile empiezan con una tabulación real.

- `all`: objetivo predeterminado.
- `estadistiques`: genera el ejecutable.
- `test`: ejecuta una prueba básica.
- `clean`: elimina el ejecutable.
- `-Wall -Wextra`: activa advertencias.
- `-Werror`: convierte las advertencias en errores.

## 4.2. Python con PyInstaller

```yaml
name: Compilar Python

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build-linux:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - run: pip install pyinstaller

      - run: pyinstaller --onefile comprova_desplegaments.py
```

En la práctica multiplataforma se definen jobs separados para `ubuntu-latest`, `macos-latest` y `windows-latest`. PyInstaller genera el ejecutable en `dist/`; el resultado depende del sistema operativo de compilación.

## 4.3. Linter de JavaScript

Ejemplo basado en la actividad que usa la rama `nodejs_linter_job`:

```yaml
name: main_workflow

on:
  push:
    branches: [nodejs_linter_job]

jobs:
  linter-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Ejecutar linter
        uses: github/super-linter@v4
        env:
          DEFAULT_BRANCH: nodejs_example
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          LINTER_RULES_PATH: /
          JAVASCRIPT_ES_CONFIG_FILE: .eslintrc.json
          VALIDATE_JAVASCRIPT_ES: true
```

Detalles:

1. La rama del evento debe coincidir con la actividad.
2. `DEFAULT_BRANCH` indica la rama base configurada para la herramienta.
3. `GITHUB_TOKEN` permite las operaciones autorizadas del workflow.

## 4.4. Pruebas con Jest

```yaml
name: Jest tests

on:
  push:
    branches: [nodejs_jest_job]

jobs:
  test-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v3
        with:
          node-version: 20

      - run: npm install jest

      - run: npm test
```

El `package.json` necesita un script de pruebas, por ejemplo:

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

## 4.5. Saludo mediante una variable de entorno

```yaml
jobs:
  environment_test_execution:
    runs-on: ubuntu-latest
    env:
      DESTINATION_PERSON: "2DAW"
    steps:
      - name: Saludo
        run: echo "Hello $DESTINATION_PERSON"
```

La variable de entorno se define en el job y se lee desde el comando.

## 4.6. Reglas para resolver ejercicios

- Lee qué evento activa el workflow y qué rama pide.
- Identifica los jobs y el orden requerido.
- Añade checkout cuando se necesitan archivos del repositorio.
- Instala las herramientas necesarias antes de usarlas.
- Ejecuta compilación o pruebas con `run`.
- Usa `needs` para ordenar jobs.
- Guarda artefactos solo después de generar sus archivos.
- Comprueba las rutas exactas de los archivos.
- Mantén la indentación YAML.
- Si el enunciado da nombres y versiones concretos, respétalos.