# Genesis Modpack

Pack de packwiz para el servidor Genesis. Este repo es la **fuente de verdad** del launcher: cada vez que un jugador hace click en JUGAR, el launcher lee este pack y sincroniza los mods automáticamente.

## Requisitos

- [packwiz CLI](https://github.com/packwiz/packwiz/releases) — descargar y agregar al PATH
- Git

---

## Cómo actualizar los mods

### 1. Actualizar los mods en CurseForge App

Agregá, eliminá o actualizá mods normalmente en la app de CurseForge en tu perfil.

### 2. Exportar desde CurseForge

En la CurseForge App:
`Mis Modpacks → click derecho en el perfil → Export Profile → Export`

Guardá el `.zip`, extraelo. Vas a tener una carpeta con `manifest.json` adentro.
Reemplazá el archivo en: `../genesis-neo/manifest.json`

### 3. Importar a packwiz

```bash
cd genesis-modpack
packwiz curseforge import ..\genesis-neo\manifest.json
```

Esto actualiza todos los archivos `.pw.toml` para que coincidan con tu export de CurseForge.

### 4. Refrescar el índice

**Este paso es obligatorio.** Sin él, el launcher no detecta los cambios.

```bash
packwiz refresh
```

### 5. Push a GitHub

```bash
git add .
git commit -m "chore: update mods from CurseForge export"
git push origin main
```

Listo. La próxima vez que cualquier jugador haga click en **JUGAR**, el launcher baja el pack actualizado, descarga solo lo que cambió y elimina los mods que se quitaron del pack.

---

## Agregar un mod suelto manualmente

```bash
# Desde CurseForge (por project ID — está en la URL del mod en curseforge.com)
packwiz curseforge add --project-id 394468

# Desde Modrinth (por slug o ID)
packwiz modrinth add sodium

# Siempre terminar con refresh y push
packwiz refresh
git add .
git commit -m "feat: add <nombre del mod>"
git push origin main
```

## Eliminar un mod

```bash
# Borrar el archivo .pw.toml del mod
del mods\sodium.pw.toml

packwiz refresh
git add .
git commit -m "chore: remove <nombre del mod>"
git push origin main
```

El launcher elimina automáticamente el `.jar` del cliente en la próxima sincronización.

---

## Qué pasa del lado del jugador

```
Jugador hace click en JUGAR
   └─ El launcher lee pack.toml + index.toml desde GitHub (raw)
   └─ Elimina mods que ya no están en el pack
   └─ Por cada mod: verifica hash local contra el índice
       ├─ Hash coincide → saltar (ya está actualizado)
       └─ Hash distinto o falta el archivo → descargar
   └─ Minecraft inicia con todos los mods sincronizados
```

Los jugadores no necesitan hacer nada. Todo es automático.

---

## Estructura del pack

| Archivo | Para qué sirve |
|---|---|
| `pack.toml` | Metadata del pack (versión de MC, versión de NeoForge) |
| `index.toml` | Lista maestra de todos los archivos con hashes SHA256 |
| `mods/*.pw.toml` | Un archivo por mod — URL de descarga y hash |
| `config/` | Configs distribuidas a todos los jugadores |
