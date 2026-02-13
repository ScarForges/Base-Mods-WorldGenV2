# Base-Mods-WorldGenV2

Base de mod para crear una instancia y empezar a trabajar con el **World Gen V2** de Hytale. Este proyecto proporciona una estructura completa con biomas de ejemplo, configuraciones de densidad, world structures y una instancia de servidor lista para usar.

---

## Estructura del Proyecto

```
Scar.ScarForgesInstancia/
├── manifest.json                    # Manifiesto del mod
├── Biomes/                          # Definiciones de biomas (nodos visuales)
│   ├── Basic.json                   # Bioma básico de referencia
│   ├── Void.json                    # Bioma vacío (sin terreno)
│   ├── Void_Buffer.json             # Buffer de transición al vacío
│   ├── Void_Buffer_Oasis.json       # Buffer con oasis
│   ├── Boreal1/                     # Biomas de bosque boreal
│   ├── Desert1/                     # Biomas de desierto (oasis, ríos, costa...)
│   ├── Examples/                    # Ejemplos de funcionalidades del WorldGen
│   ├── Experimental/                # Biomas experimentales (arcos, dunas, montañas...)
│   ├── Generative/                  # Estructuras generativas (ruinas, pilares, venas...)
│   ├── Ocean1/                      # Biomas de océano
│   ├── Plains1/                     # Biomas de llanuras
│   ├── Taiga1/                      # Biomas de taiga
│   └── Volcanic1/                   # Biomas volcánicos
│
└── Server/
    ├── HytaleGenerator/
    │   ├── Biomes/
    │   │   └── ScarForgesBioma.json # Definición del bioma principal del servidor
    │   ├── Density/
    │   │   ├── Map_Default.json     # Mapa de densidad por defecto (Biome-Map)
    │   │   ├── Map_Tiles.json       # Mapa por tiles
    │   │   └── Map_Tiles_Rivers.json# Mapa por tiles con ríos
    │   ├── Settings/
    │   │   └── Settings.json        # Configuración del generador (view distance, etc.)
    │   └── WorldStructures/
    │       ├── Default.json         # World structure por defecto
    │       └── ScarForges.json      # World structure personalizada
    │
    └── Instances/
        └── ScarForgesInstancia/
            ├── instance.bson         # Datos binarios de la instancia
            ├── chunks/               # Chunks generados
            └── resources/
                ├── InstanceData.json
                ├── PrefabEditSession.json
                ├── ReputationData.json
                ├── SpawnSuppressionController.json
                └── Time.json
```

---

## Requisitos Previos

- **Hytale** con soporte para World Gen V2
- Acceso al sistema de mods de Hytale

---

## Cómo Empezar

### Clonar o copiar el mod

Copia la carpeta `Scar.ScarForgesInstancia` en tu directorio de mods de Hytale.


## Archivos Clave

### World Structure (`WorldStructures/ScarForges.json`)

Define qué biomas se usan y cómo se distribuyen en el mundo:

```json
{
  "Type": "NoiseRange",
  "Biomes": [{
    "Biome": "ScarForgesBioma",
    "Min": -1,
    "Max": 2
  }],
  "DefaultBiome": "ScarForgesBioma",
  "DefaultTransitionDistance": 32,
  "MaxBiomeEdgeDistance": 32,
  "Density": {
    "Type": "Imported",
    "Name": "Biome-Map"
  }
}
```

- **Biomes**: Lista de biomas con rangos de ruido para su distribución.
- **DefaultBiome**: Bioma que se usa si ningún rango coincide.
- **Density → Imported**: Referencia al mapa de densidad exportado desde `Map_Default.json`.
- **ContentFields**: Define alturas base (terreno, agua, bedrock).

### Bioma del Servidor (`Biomes/ScarForgesBioma.json`)

Archivo principal que define la generación de terreno del bioma usando un grafo de nodos de densidad. Contiene:

- **Terrain** → Nodo `DAOTerrain` con el árbol de densidad (noise, curvas, normalizadores, etc.)
- **MaterialProvider** → Qué bloques se usan para el terreno
- **Props / Decoraciones** → Objetos colocados sobre el terreno

### Mapas de Densidad (`Density/`)

Grafos de nodos exportados que controlan la distribución de biomas:

- **Map_Default.json** — Mapa básico con un solo bioma.
- **Map_Tiles.json** — Distribución de biomas por tiles/celdas.
- **Map_Tiles_Rivers.json** — Distribución por tiles con ríos integrados.

---

## Carpeta Biomes/ (Raíz del mod)

Esta carpeta contiene definiciones de biomas en formato de nodos visuales. Son **plantillas y ejemplos** que puedes usar como referencia para crear tus propios biomas.

### Categorías

| Carpeta | Contenido |
|---|---|
| `Examples/` | Ejemplos de características individuales del WorldGen: noise 2D, curvas, mixers, props, fluidos, runtime, etc. |
| `Experimental/` | Biomas experimentales completos: arcos, dunas, glaciares, montañas, mesetas, arrecifes, etc. |
| `Generative/` | Estructuras generativas procedurales: ruinas, pilares, arcos, venas de mineral, edificios. |
| `Boreal1/` | Bioma boreal con variantes (hiedra, henges). |
| `Desert1/` | Bioma desierto con variantes (oasis, río, costa, rocas, stacks). |
| `Plains1/` | Llanuras con variantes (gargantas, montañas, robles, ríos, costa). |
| `Taiga1/` | Taiga con variantes (montañas, secuoyas, ríos, costa). |
| `Volcanic1/` | Volcánico con variantes (caldera, jungla, ríos, costa). |
| `Ocean1/` | Océano. |

### Biomas Útiles para Empezar

- **`Basic.json`** — Bioma mínimo con ruido simplex 2D. Ideal para entender la estructura base.
- **`Void.json`** — Bioma completamente vacío (densidad constante = 0). Útil como plantilla limpia.
- **`Void_Buffer.json`** — Transición al vacío.

---


## Nodos de Densidad Comunes

Los biomas se construyen con un grafo de nodos. Estos son los tipos más usados:

| Nodo | Descripción |
|---|---|
| `SimplexNoise2D` | Ruido Simplex en 2D. Parámetros: `Scale`, `Octaves`, `Persistence`, `Lacunarity`. |
| `CellNoise2D` | Ruido celular (Voronoi). |
| `Sum` | Suma de múltiples inputs de densidad. |
| `Max` / `Min` | Máximo/mínimo entre inputs. |
| `Mix` | Mezcla entre inputs con un factor. |
| `Normalizer` | Remapea un rango (`FromMin`/`FromMax`) a otro (`ToMin`/`ToMax`). |
| `CurveMapper` | Aplica una curva manual a la densidad. |
| `Scale` | Escala los ejes X/Y/Z. |
| `Constant` | Valor constante de densidad. |
| `Exported` | Exporta un nodo con nombre para ser importado en otro lugar. |
| `Imported` | Importa un nodo exportado por nombre. |
| `Cache` | Cachea el resultado del sub-árbol. |
| `YOverride` | Fuerza un valor Y específico (útil para mapas 2D). |
| `FastGradientWarp` | Deformación del espacio con ruido gradient. |
| `DAOTerrain` | Nodo raíz del terreno de un bioma. |


## Licencia

Proyecto base de uso libre para la comunidad de modding de Hytale.
