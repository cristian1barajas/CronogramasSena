# Manual: Migrar repos personales de HTTPS a SSH

## Prerrequisitos (ya completados)
- Llave SSH personal creada en `~/.ssh/github-personal`
- Llave publica agregada en GitHub > Settings > SSH and GPG Keys
- Archivo `~/.ssh/config` configurado con el Host `github-personal`

---

## Paso a paso para CADA repositorio personal

### 1. Abrir PowerShell y navegar al repositorio

```powershell
cd "C:\ruta\completa\al\repositorio"
```

### 2. Verificar el remote actual (HTTPS)

```powershell
git remote -v
```

Veras algo como:

```
origin  https://github.com/TU-USUARIO/nombre-del-repo.git (fetch)
origin  https://github.com/TU-USUARIO/nombre-del-repo.git (push)
```

Anota el **usuario** y el **nombre del repo** de esa URL.

### 3. Cambiar el remote de HTTPS a SSH

```powershell
git remote set-url origin git@github-personal:TU-USUARIO/nombre-del-repo.git
```

> IMPORTANTE: Usa `github-personal` (NO `github.com`).
> Esto le dice a SSH que use tu llave personal automaticamente.

Ejemplo real: si el remote actual es:
```
https://github.com/cristian1barajas/mi-proyecto.git
```

El comando seria:
```powershell
git remote set-url origin git@github-personal:cristian1barajas/mi-proyecto.git
```

### 4. Verificar que el cambio se aplico

```powershell
git remote -v
```

Ahora deberia mostrar:

```
origin  git@github-personal:TU-USUARIO/nombre-del-repo.git (fetch)
origin  git@github-personal:TU-USUARIO/nombre-del-repo.git (push)
```

### 5. Probar la conexion con un push o pull

```powershell
git push
```

O si no tienes cambios pendientes:

```powershell
git pull
```

La primera vez te preguntara:

```
The authenticity of host 'github.com (IP)' can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Escribe `yes` y Enter. Esto solo pasa la primera vez.

---

## Comando rapido (todo en una linea)

Si ya sabes tu usuario y el nombre del repo, puedes hacerlo rapido:

```powershell
cd "C:\ruta\al\repo" ; git remote set-url origin git@github-personal:TU-USUARIO/nombre-del-repo.git ; git remote -v
```

---

## Ejemplo completo con un repo real

```powershell
# 1. Navegar al repo
cd "C:\Users\crist\OneDrive\1_SENA\ADSO 2025\CRONOGRAMA GENERAL FICHAS\code"

# 2. Ver el remote actual
git remote -v
# origin  https://github.com/cristian1barajas/cronograma-fichas.git (fetch)

# 3. Cambiar a SSH
git remote set-url origin git@github-personal:cristian1barajas/cronograma-fichas.git

# 4. Verificar
git remote -v
# origin  git@github-personal:cristian1barajas/cronograma-fichas.git (fetch)

# 5. Probar
git push
# Everything up-to-date
```

---

## Resumen visual

```
ANTES (HTTPS):
origin  https://github.com/USUARIO/REPO.git
                ^^^^^^^^^^

DESPUES (SSH):
origin  git@github-personal:USUARIO/REPO.git
             ^^^^^^^^^^^^^^^^
             Este es el Host del archivo ~/.ssh/config
             que apunta a tu llave personal
```

---

## Solucion de problemas

### Error: "Permission denied (publickey)"
Verifica que la llave esta cargada:
```powershell
ssh -T git@github-personal
```
Si falla, revisa que el archivo `~/.ssh/config` tenga el Host `github-personal` correcto.

### Error: "Could not resolve hostname github-personal"
El archivo `~/.ssh/config` no existe o no tiene el Host. Verifica:
```powershell
notepad $HOME/.ssh/config
```

### Quiero ver todos mis repos locales para migrarlos
Buscar todas las carpetas `.git` en una ubicacion:
```powershell
Get-ChildItem -Path "C:\Users\crist" -Recurse -Directory -Filter ".git" -ErrorAction SilentlyContinue | ForEach-Object { $_.Parent.FullName }
```

### Quiero migrar varios repos de golpe
Puedes listarlos y cambiarlos asi:
```powershell
# Lista de rutas de tus repos personales
$repos = @(
    "C:\ruta\repo1",
    "C:\ruta\repo2",
    "C:\ruta\repo3"
)

foreach ($repo in $repos) {
    cd $repo
    $remote = git remote get-url origin
    # Reemplazar https://github.com/ por git@github-personal:
    $newRemote = $remote -replace "https://github.com/", "git@github-personal:"
    git remote set-url origin $newRemote
    Write-Host "Migrado: $repo -> $newRemote"
}
```
