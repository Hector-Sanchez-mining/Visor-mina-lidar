# Visor Mina LiDAR

Visor web de levantamientos LiDAR capturados con iPhone (SiteScape) para obras de la mina. Funciona directamente desde GitHub Pages sin servidor ni base de datos.

---

## Tecnología

- [Three.js r128](https://threejs.org/) con `OBJLoader` y `MTLLoader`
- PWA (Progressive Web App) — instalable en móvil y escritorio
- Archivos estáticos — no requiere backend

---

## Estructura del repositorio

```
/
├── index.html          ← Visor principal
├── manifest.json       ← Configuración PWA
├── sw.js               ← Service Worker (caché offline)
├── icon-192.png        ← Ícono de la app
├── zones.json          ← Lista de zonas (ver formato abajo)
│
├── Zona Norte/
│   ├── models.json
│   ├── Rebaje metalero 2.obj
│   ├── Rebaje metalero 2.mtl
│   └── Rebaje metalero 2.jpg
│
├── Zona Sur/
│   ├── models.json
│   ├── Galería principal.obj
│   ├── Galería principal.mtl
│   └── Galería principal.jpg
│
└── ...
```

---

## Formato de archivos de configuración

### `zones.json` — en la raíz del repositorio

Lista todas las zonas disponibles en el visor:

```json
{
  "zones": [
    "Zona Norte",
    "Zona Sur"
  ]
}
```

El nombre de cada zona debe coincidir **exactamente** con el nombre de la carpeta correspondiente.

---

### `models.json` — dentro de cada carpeta de zona

Lista los levantamientos disponibles en esa zona:

```json
{
  "models": [
    "Rebaje metalero 2.obj",
    "Galería principal.obj"
  ]
}
```

---

## Exportación desde SiteScape

Cada levantamiento se exporta desde SiteScape como un `.zip` que contiene:

| Archivo | Descripción |
|---|---|
| `nombre.obj` | Geometría del mesh reconstruido |
| `nombre.mtl` | Referencia al material y textura |
| `nombre.jpg` | Textura fotorrealista (~15 MB) |

Los tres archivos deben tener el **mismo nombre base** y estar en la misma carpeta dentro del repositorio.

---

## Agregar un nuevo levantamiento

1. Exportar el levantamiento desde SiteScape (formato OBJ+MTL+JPG)
2. Crear la carpeta de zona si no existe (ej. `Zona Norte/`)
3. Copiar los tres archivos `.obj`, `.mtl` y `.jpg` a esa carpeta
4. Editar `models.json` de la zona y agregar la entrada:
   ```json
   { "models": ["Levantamiento nuevo.obj"] }
   ```
5. Si es una zona nueva, agregarla también a `zones.json` en la raíz
6. Hacer commit y push — GitHub Pages actualizará automáticamente

---

## Agregar una zona nueva

1. Crear la carpeta con el nombre de la zona
2. Agregar un `models.json` dentro con la lista de modelos
3. Agregar el nombre de la zona a `zones.json` en la raíz:
   ```json
   {
     "zones": [
       "Zona Norte",
       "Zona Sur",
       "Nueva Zona"
     ]
   }
   ```

---

## Archivos reutilizados del visor STL

Los siguientes archivos se toman del visor de modelos STL y requieren ajustes menores:

### `manifest.json`
Cambiar `name` y `short_name`:
```json
{
  "name": "Visor Mina LiDAR",
  "short_name": "LiDAR Mina",
  ...
}
```

### `sw.js`
Cambiar el nombre del caché para que no colisione con el visor STL:
```js
const CACHE_NAME = 'visor-lidar-v1';
```

---

## Controles de navegación

| Acción | Escritorio | Móvil |
|---|---|---|
| Rotar | Clic + arrastrar | Un dedo |
| Zoom | Rueda del mouse | Pellizcar |
| Mover | — | Dos dedos |
| Medir | Botón Medidor + clic | Botón Medidor + pulsación larga |

---

## Limitaciones conocidas

- Los modelos se cargan de forma **secuencial** (uno a la vez) para evitar saturar el navegador con archivos de 15-30 MB cada uno
- El visor no tiene georeferenciación — cada levantamiento se centra automáticamente en el origen al cargarse
- GitHub Pages tiene un límite de **100 MB por archivo** — si algún `.jpg` o `.obj` supera ese tamaño, comprimirlo antes de subir

---

## Repositorio relacionado

[Visor Mina 3D](https://github.com/tu-usuario/visor-mina-3d) — visor de modelos STL georeferenciados por zona
