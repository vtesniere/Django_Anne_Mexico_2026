# Contexto del Proyecto — Django_Anne_Mexico_2026

> Leer este archivo al inicio de cada conversación sobre este proyecto.
> Actualizar cuando se añadan funciones, se tomen decisiones o el usuario dé feedback.

---

## Qué es este proyecto

Mapa interactivo de un viaje de 24 noches por Yucatán y Quintana Roo (México), para Django & Anne.
Aplicación HTML estática de un solo archivo, desplegada en GitHub Pages.

**URL en vivo:** https://vtesniere.github.io/Django_Anne_Mexico_2026/
**Ruta local:** `/Users/vladimirtesniere/Dev/Claude/Django_Anne_Mexico_2026/`
**Repo GitHub:** `vtesniere/Django_Anne_Mexico_2026` (SSH push vía `~/.ssh/id_ed25519`)

---

## Fechas y Bases

| Parada | Fechas | Noches | Hotel |
|--------|--------|--------|-------|
| 🔵 Puerto Morelos | 11–18 jul 2026 | 8 (máx. 3 alojamientos) | Base: Puerto Morelos |
| 🟢 Mérida | 19–26 jul 2026 | 8 (máx. 1 noche fuera) | Fiesta Americana Mérida |
| 🟠 Cancún | 27 jul – 3 ago 2026 | 8 (máx. 1 noche fuera) | Fiesta Americana Villas Cancún |

**Vuelo de regreso:** Aeropuerto de Cancún (CUN), 3 de agosto 2026.

---

## Rutas de tránsito

| De → A | Fecha | Km | Tiempo | Ruta |
|--------|-------|-----|--------|------|
| Puerto Morelos → Mérida | 18–19 jul | ~350 km | ~4 h | Hwy 307 norte → 180D oeste |
| Valladolid → Cancún | 26–27 jul | ~160 km | ~2 h | Hwy 180D este |

**Nota estratégica:** La noche del 25 jul en Valladolid permite visitar Chichén Itzá al amanecer el 26 jul (40 km) y llegar a Cancún esa misma tarde.

---

## Excursiones por base

### Puerto Morelos (base azul #06b6d4)
- Parque Nacional Arrecife Puerto Morelos (en sitio)
- Akumal — nado con tortugas (50 km)
- Cenote Dos Ojos / Jardín del Edén (55 km)
- Ruinas de Tulum (130 km) — **noche en Tulum recomendada**
- Parque del Jaguar / Tulum (130 km)
- Reserva Biosfera Sian Ka'an (155 km, mejor desde Tulum)

Alojamientos sugeridos: Puerto Morelos (11–14 jul) + Tulum (15–17 jul) + Puerto Morelos (18 jul)

### Mérida (base verde #22c55e)
Itinerario optimizado para minimizar backtracking (espiral desde Mérida):
- Centro Histórico Mérida (en ciudad)
- Progreso (35 km norte)
- Celestún + Ojo de Agua Baldiosera (93 km oeste) — flamingos rosados
- Uxmal + Cenote Xcanché (80 km sur)
- Izamal — Ciudad Amarilla (70 km este) 🔄 intercambiable
- San Crisanto — manglares y flamingos (100 km, vía Izamal)
- Chichén Itzá (120 km este) 🔄 intercambiable
- Valladolid + Cenote Ik Kil + Cenote Zací (160 km este) 🔄 — **noche 25 jul**

### Cancún (base naranja #f97316)
- Holbox — tiburones ballena + flamingos (160 km + ferry) — **1 noche recomendada**
- Xcaret eco-parque (75 km sur)
- Isla Mujeres (ferry ~30 min)
- Zona Arqueológica El Rey (en Cancún)
- Cobá — pirámide escalable (160 km sur)
- Chichén Itzá 🔄 (190 km, si no se hizo desde Mérida)
- Valladolid + cenotes 🔄 (160 km, si no se hizo desde Mérida)
- Izamal 🔄 (230 km, si no se hizo desde Mérida)

---

## Notas especiales
- 🦈 **Julio = temporada tiburones ballena en Holbox** — reservar con antelación
- 🦩 Celestún: salir antes de las 8h para flamencos
- 🏛 Chichén Itzá: antes de las 9h (calor + grupos)
- 🔄 Highlights marcados como "intercambiables" pueden hacerse desde Mérida o Cancún
- 📍 **Ojo de Agua Baldiosera** está en Celestún, Yucatán (CP 97367)

---

## Arquitectura del código (index.html)

### Estructura de datos

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `bases[]` | Array(3) | Cada base: `{num, id, name, lat, lng, color, dateRange, hotel, desc, sleepingPlaces[], highlights[], tip}` |
| `bases[i].highlights[]` | Array | Cada excursión: `{name, time, lat, lng, overnight, inter}` |
| `bases[i]._hlsStart` | Number | Índice inicial en `_hls[]` para esta base (calculado al inicio) |
| `transits[]` | Array(2) | Rutas de tránsito entre bases |

### Funciones clave

| Función | Descripción |
|---------|-------------|
| `fetchRoute(fromLng,fromLat,toLng,toLat)` | Llama OSRM, devuelve `[[lat,lng],…]` |
| `loadTransits()` | Carga rutas de tránsito en paralelo al inicio |
| `window.showHL(idx)` | Carga y dibuja ruta desde base hasta excursión `idx` |
| `clearHL()` | Elimina capa de excursión activa |
| `buildPopup(base)` | HTML del popup Leaflet para cada base |
| `mobView(view)` | Toggle mapa/sidebar en móvil |
| `hexToRgb(hex)` | Helper para background con opacidad en region headers |

### Registro de highlights
`window._hls[]` se pre-llena al inicio (antes de construir markers y sidebar):
```js
bases.forEach(base => {
  base._hlsStart = window._hls.length;
  base.highlights.forEach(h => window._hls.push({baseLat, baseLng, lat, lng, name, time}));
});
```
Los índices se usan tanto en el popup HTML (`onclick="showHL(idx)"`) como en los event listeners del sidebar.

### Colores
- Puerto Morelos: `#06b6d4` (cian)
- Mérida: `#22c55e` (verde)
- Cancún: `#f97316` (naranja)
- Tránsito: `#475569` (gris, línea discontinua)
- Excursión activa: `#38bdf8` (azul cielo)

---

## GitHub / Deploy
- SSH: `~/.ssh/id_ed25519` ("macbook M2")
- Deploy: GitHub Actions (`.github/workflows/pages.yml`) en cada push a `main`
- URL del site: `https://vtesniere.github.io/Django_Anne_Mexico_2026/`

---

## Historial de sesiones

| Fecha | Cambio |
|-------|--------|
| Mar 2026 | Creación inicial: mapa con 3 bases, rutas OSRM, highlights intercambiables, layout móvil |
