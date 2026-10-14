# 2. Archivo .gitignore

## 2.1. Función y ubicación

El archivo `.gitignore` indica a Git qué archivos o rutas debe ignorar cuando todavía no están siendo controlados por el repositorio. Se suele colocar en la raíz del proyecto.

Crear o editarlo:

```bash
nano .gitignore
```

## 2.2. Ejemplo

```gitignore
# Archivos compilados de Python
__pycache__/
*.py[cod]

# Entornos virtuales
venv/
.venv/

# Carpetas de compilación
dist/
build/

# Dependencias de Node.js
node_modules/

# Configuración privada
.env

# Archivos del sistema
.DS_Store

# Archivos temporales y logs
*.tmp
*.log
```

## 2.3. Patrones

| Patrón | Función |
|---|---|
| `archivo.txt` | Ignora archivos que coincidan con ese patrón |
| `*.log` | Ignora archivos terminados en `.log` |
| `*.exe` | Ignora archivos terminados en `.exe` |
| `carpeta/` | Ignora una carpeta y su contenido |
| `__pycache__/` | Ignora directorios de caché de Python |
| `/dist/` | Ignora `dist` en la raíz del repositorio |
| `!importante.log` | Excepción que deja de ignorar un archivo coincidente |
| `# comentario` | Comentario explicativo |

Cada regla se escribe en una línea distinta. El asterisco `*` representa una secuencia de caracteres. Las barras sirven para especificar rutas.

## 2.4. Comprobarlo

```bash
git status
git add .gitignore
git add .
git status
```

**Importante:** `.gitignore` no deja de seguir un archivo que Git ya controla. Para quitar un archivo del índice sin borrarlo del disco:

```bash
git rm --cached archivo
```

Después hay que confirmar el cambio con un commit.

No se deben subir contraseñas, tokens ni claves privadas. Ignorar `.env` ayuda a evitar errores, pero no sustituye una gestión segura de secretos.