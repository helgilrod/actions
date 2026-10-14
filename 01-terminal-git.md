# 1. Terminal y Git

## 1.1. Crear e inspeccionar un repositorio

```bash
mkdir proyecto
cd proyecto
ls
ls -la
git init
git status
```

- `mkdir`: crea una carpeta.
- `cd`: entra en una carpeta.
- `ls`: lista archivos visibles.
- `ls -la`: muestra también archivos ocultos, como `.git`.
- `git init`: inicializa el repositorio Git.
- `git status`: muestra archivos modificados, preparados y sin seguimiento.

## 1.2. Configurar Git

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --global core.editor nano
git config --global --list
```

## 1.3. Ciclo de trabajo

1. Modificar o crear archivos.
2. Preparar los cambios con `git add`.
3. Guardarlos en el historial con `git commit`.
4. Enviar los commits al remoto con `git push`.

```bash
git status
git add README.md
git status
git commit -m "Afegit fitxer Readme"
git log --oneline
```

- `git add README.md`: prepara ese archivo.
- `git add .`: prepara los cambios del directorio actual y sus subdirectorios.
- `git commit -m "mensaje"`: crea un commit con los cambios preparados.
- `git diff`: muestra cambios no preparados.
- `git diff --staged`: muestra cambios preparados.

Quitar del staging sin borrar el archivo:

```bash
git restore --staged README.md
```

Descartar modificaciones locales no confirmadas de un archivo:

```bash
git restore README.md
```

Este último comando puede borrar cambios que no estén guardados en un commit.

## 1.4. Historial y formato

```bash
git log
git log --oneline
git log --pretty=format:"%h %an %ad" --date=iso
```

| Código | Significado |
|---|---|
| `%h` | Hash abreviado |
| `%H` | Hash completo |
| `%an` | Nombre del autor |
| `%ae` | Correo del autor |
| `%ad` | Fecha del autor |
| `%s` | Mensaje del commit |

## 1.5. Deshacer el último commit

```bash
git reset --soft HEAD~1
```

Quita el último commit y mantiene los cambios en staging.

```bash
git reset --mixed HEAD~1
```

Quita el último commit y deja los cambios en el directorio de trabajo, fuera del staging. Es el modo predeterminado.

```bash
git reset --hard HEAD~1
```

Quita el último commit y descarta los cambios del staging y del directorio de trabajo. **Puede provocar pérdida de trabajo.**

`HEAD~1` se refiere al commit anterior al actual.

## 1.6. Repositorios remotos y GitHub

```bash
git remote -v
git remote add origin URL_DEL_REPOSITORIO
git branch -M main
git push -u origin main
```

- `git remote -v`: muestra los remotos.
- `git remote add origin`: añade un remoto llamado `origin`.
- `git branch -M main`: renombra la rama actual a `main`.
- `git push -u origin main`: sube la rama y configura el seguimiento remoto.

Para clonar un repositorio existente:

```bash
git clone URL_DEL_REPOSITORIO
cd nombre-repositorio
git pull
```

Después de preparar y confirmar nuevos cambios:

```bash
git add .
git commit -m "Describir cambios"
git push
```

- `git pull`: descarga e integra cambios del remoto.
- `git push`: sube commits locales al remoto.

## 1.7. Errores frecuentes

- Hacer `git commit` sin preparar cambios.
- Olvidar `git add` antes del commit.
- Intentar hacer push antes de configurar el remoto.
- Usar un nombre de rama distinto al esperado.
- Confundir `git restore --staged` con borrar un archivo.
- Usar `git reset --hard` sin comprobar qué cambios se perderán.