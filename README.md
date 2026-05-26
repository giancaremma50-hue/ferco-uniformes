# FERCO — Control de Uniformes

Sistema web para gestionar pedidos, inventario y resurtido de uniformes de Ferco Ceramica y sus marcas relacionadas.

## Tecnologías

| Capa | Tecnología |
|------|-----------|
| Frontend | React 18 + Babel Standalone (sin build step) |
| Base de datos | Firebase Firestore |
| Autenticación | Firebase Authentication |
| Hosting | Netlify |
| Funciones serverless | Netlify Functions (Node.js) |
| IA | Claude API (Anthropic) — extracción de tickets |

## Estructura del proyecto

```
ferco-uniformes/
├── index.html                   # Aplicación completa (React SPA)
├── netlify.toml                 # Configuración de Netlify
└── netlify/
    └── functions/
        └── claude.js            # Proxy hacia la API de Claude
```

## Funcionalidades

### Pedidos
- Crear pedidos de uniformes por empleado (nombre, país, sucursal, departamento, puesto)
- Importar pedidos desde tickets de Jira usando IA (Claude extrae los datos automáticamente)
- Flujo de estados: `Pendiente → En producción → En ruta → Entregado / Descartado`
- Filtro por país: GT, HN, SV, MX

### Inventario
- Visualización del stock actual por tipo, color, marca, talla y género
- Alertas de stock bajo (≤ 5 unidades)
- Inicialización automática en Firebase al primer acceso

### Resurtido
- Registro de entradas de inventario con notas (ej. número de factura)
- Actualización automática del stock al guardar

### Roles
| Rol | Acceso |
|-----|--------|
| `admin` | Ve todos los pedidos, gestiona inventario y resurtidos |
| `reclutamiento` | Solo ve sus propios pedidos |

## Catálogo de uniformes

| Tipo | Color | Marcas disponibles |
|------|-------|--------------------|
| CAMISA | NEGRA | FERCO CERAMICA, FERCO |
| CAMISA | BLANCA | FERCO CERAMICA, EL REY DE LA CERAMICA, MCP |
| BLUSA | NEGRA | FERCO CERAMICA, FERCO |
| BLUSA | BLANCA | FERCO CERAMICA, EL REY DE LA CERAMICA, MCP |
| POLO | GRIS | FERCO CERAMICA, REY DE LA CERAMICA, MCP |

**Tallas disponibles:** XS, S, M, L, XL, 2XL, y talla especial (medida personalizada)

## Variables de entorno

Configurar en el panel de Netlify antes de hacer deploy:

| Variable | Descripción |
|----------|-------------|
| `ANTHROPIC_API_KEY` | API Key de Anthropic para la función de extracción por IA |

La configuración de Firebase está embebida directamente en `index.html`.

## Deploy

El proyecto se despliega automáticamente en Netlify al hacer push a `main`.

- **Directorio publicado:** `.` (raíz del repositorio)
- **Funciones:** `netlify/functions/`
- No requiere paso de build

## Inicialización de Firebase

Al iniciar sesión por primera vez, el sistema verifica si la colección `inventario` en Firestore está vacía. Si es así, crea automáticamente los registros iniciales para las tallas S, M, L y XL de todos los productos del catálogo, con 6 unidades por combinación.

Las entradas de inventario para talla **2XL** se crean automáticamente en el primer acceso posterior a su incorporación, con stock inicial de 0 unidades, para los siguientes productos: CAMISA NEGRA / BLANCA y BLUSA NEGRA / BLANCA en todas las marcas aplicables.
