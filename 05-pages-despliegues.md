# 5. GitHub Pages y despliegues

## 5.1. Qué es GitHub Pages

GitHub Pages publica sitios web estáticos a partir de un repositorio de GitHub. Puede servir HTML, CSS y JavaScript del lado del cliente.

## 5.2. Publicación desde una rama

Crear un archivo de entrada:

```bash
nano index.html
```

Ejemplo mínimo:

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Mi web</title>
</head>
<body>
  <h1>Mi primera web</h1>
  <p>Publicada con GitHub Pages.</p>
</body>
</html>
```

Guardar y subir:

```bash
git add .
git commit -m "Crear página inicial"
git push
```

En GitHub:

1. Abrir el repositorio.
2. Entrar en **Settings → Pages**.
3. En la fuente de publicación, seleccionar la rama, normalmente `main`.
4. Elegir `/(root)` si `index.html` está en la raíz, o `/docs` si está dentro de `docs/`.
5. Guardar y esperar a que termine el despliegue.
6. Abrir la URL que muestra Pages.

## 5.3. Repositorio personal y de proyecto

| Tipo | Nombre | URL habitual |
|---|---|---|
| Sitio personal | `usuario.github.io` | `https://usuario.github.io/` |
| Sitio de proyecto | `mi-proyecto` | `https://usuario.github.io/mi-proyecto/` |

Sustituye `usuario` por el usuario de GitHub real.

## 5.4. Publicar desde /docs

Estructura posible:

```text
repositorio/
├── README.md
├── src/
└── docs/
    ├── index.html
    ├── css/
    │   └── estilos.css
    └── js/
        └── script.js
```

En **Settings → Pages** configura:

- Branch: `main`.
- Folder: `/docs`.

La carpeta seleccionada debe contener el `index.html` que inicia el sitio.

## 5.5. Compilar no es desplegar

| Fase | Función | Ejemplo |
|---|---|---|
| Checkout | Descargar el código | `actions/checkout@v4` |
| Instalar | Instalar dependencias | `npm install` |
| Compilar | Generar archivos finales | `npm run build` |
| Probar | Ejecutar pruebas | `npm test` |
| Artefacto | Guardar archivos generados | `actions/upload-artifact` |
| Desplegar | Publicar el sitio | GitHub Pages u otro servicio |

En proyectos React, la compilación suele generar `build/` o `dist/`, según la herramienta. Hay que configurar el despliegue para que use la carpeta correcta.

## 5.6. Flujo de compilación y transferencia de artefacto

Este ejemplo construye la aplicación y transfiere los archivos entre jobs. **No publica por sí solo en GitHub Pages**; para publicar de verdad hacen falta los pasos específicos de configuración y despliegue de Pages.

```yaml
name: Build y deploy

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

      - name: Instalar dependencias
        run: npm install

      - name: Construir aplicación
        run: npm run build

      - name: Guardar archivos
        uses: actions/upload-artifact@v4
        with:
          name: sitio
          path: dist/

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Descargar archivos
        uses: actions/download-artifact@v4
        with:
          name: sitio
          path: dist/
```

En las prácticas que indiquen una acción específica de despliegue —por ejemplo, Surge— hay que utilizar la acción y los parámetros indicados en el enunciado.

## 5.7. Comprobaciones cuando Pages falla

- Existe un `index.html` en la carpeta publicada.
- La rama seleccionada es la correcta.
- La carpeta seleccionada es la correcta: raíz o `/docs`.
- Los cambios están subidos a GitHub.
- El workflow de despliegue ha terminado correctamente, si se usa Actions.
- Las rutas a CSS, JavaScript e imágenes funcionan desde la URL publicada.
- Se ha esperado a que termine la publicación.

## 5.8. Qué debes distinguir

- **Commit:** guarda cambios en el historial Git.
- **Push:** sube commits al remoto.
- **Build/compilación:** genera archivos finales.
- **Artifact/artefacto:** conserva archivos generados para usarlos después.
- **Deploy/despliegue:** publica la aplicación.
- **GitHub Pages:** servicio de publicación de sitios estáticos.