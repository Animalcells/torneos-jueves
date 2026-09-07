# Jueves de Torneo FC

Web app local-first para registrar torneos de videojuegos de fútbol entre dos jugadores, llevar el marcador histórico y conocer el rendimiento de cada equipo utilizado.

Aplicación publicada:

https://animalcells.github.io/torneos-jueves/

## Objetivo del proyecto

La app está pensada para las noches de torneo entre dos personas. Cada jugador escoge la misma cantidad de equipos, desde 2 hasta 10. Durante la noche los equipos van quedando eliminados; cuando un jugador se queda sin equipos, el otro gana el torneo.

Cada torneo ganado cuenta como una victoria. Diez torneos ganados equivalen a un campeonato.

Los nombres de perfil son editables. Los valores iniciales son:

- Perfil 1: Eric
- Perfil 2: Aletz

## Estado actual

- Aplicación estática, sin backend.
- Funciona directamente en el navegador.
- Los datos se guardan en `localStorage` del dispositivo y navegador actual.
- Se puede usar en escritorio y móvil.
- Tiene tema visual de fútbol arcade 8-bit/16-bit.
- El estadio pixel art se utiliza como fondo.
- El encabezado utiliza `jueves-header.png`.
- El catálogo local está separado en `teams-fc27.js`.
- La versión publicada usa GitHub Pages desde la rama `main`.

## Funciones existentes

### Crear torneo

El botón `+ Iniciar torneo` abre el formulario para:

1. Cambiar el nombre de ambos perfiles.
2. Elegir la fecha.
3. Elegir equipos por jugador: de 2 a 10.
4. Escribir una nota o nombre opcional para el torneo.
5. Seleccionar equipos mediante autocompletado del catálogo local.

La app evita nombres de perfil iguales y equipos repetidos dentro del mismo torneo.

### Torneo activo

En la pantalla principal se muestran dos columnas independientes, una por jugador.

Los equipos aparecen como un roster visible para cada jugador, con contador de equipos vivos y una barra de progreso. Los equipos eliminados permanecen visibles, tachados y atenuados para que se pueda seguir la evolución de la noche.

El flujo principal de enfrentamientos está diseñado como un marcador de videojuego:

1. Se toca un equipo vivo del roster de cada jugador.
2. La app muestra el próximo partido y el enfrentamiento seleccionado.
3. Se toca directamente el equipo que ganó; no hace falta indicar manualmente el nombre del jugador.
4. Se confirma el resultado.
5. El equipo perdedor se marca automáticamente como eliminado y el cruce queda guardado en el log.

La eliminación manual se conserva como acción de emergencia dentro del botón `⋮` de cada equipo vivo. Así no compite visualmente con el registro normal del partido.

### Resumen

La vista principal contiene:

- Torneos jugados.
- Torneos ganados por el primer jugador.
- Torneos ganados por el segundo jugador.
- Torneos restantes para el siguiente campeonato.
- Racha reciente.
- Gráfica de resultados.
- Marcador histórico entre los perfiles.
- Equipos eliminados por jugador.

### Últimos torneos

La pestaña `Últimos torneos` muestra los torneos registrados ordenados por fecha. Al seleccionar uno se abre su detalle.

El detalle muestra:

- Fecha.
- Cantidad de equipos por jugador.
- Ganador final.
- Equipos usados por cada perfil.
- Log de enfrentamientos.
- Equipo ganador y equipo perdedor de cada cruce.

### Estadísticas de equipos

La pestaña `Estadísticas de equipos` separa los equipos por jugador y muestra:

- Cuántos torneos jugó cada equipo.
- Victorias del equipo.
- Derrotas del equipo.

Las victorias y derrotas se calculan usando los enfrentamientos registrados. Si un equipo fue marcado perdido directamente y no existe un cruce asociado, se registra como derrota igualmente.

### Deshacer, rehacer y respaldos

El engrane contiene:

- Deshacer.
- Rehacer.
- Exportar respaldo JSON.
- Importar respaldo JSON.
- Limpiar datos.

Las acciones de modificación importantes generan snapshots para permitir deshacer. El historial de deshacer conserva hasta 30 estados en memoria durante la sesión actual.

## Estructura de archivos

```text
torneos-jueves/
├── index.html              # HTML, CSS y JavaScript principal
├── teams-fc27.js           # Catálogo local de equipos
├── jueves-header.png       # Logotipo pixel art del encabezado
├── stadium-background.png  # Fondo pixel art del estadio
└── README.md               # Documentación del proyecto
```

No hay `package.json`, framework, bundler ni proceso de compilación. Es una aplicación estática autocontenida.

## Arquitectura actual

### HTML

`index.html` contiene:

- Encabezado visual.
- Botón de inicio.
- Menú de configuración.
- Tarjetas de estadísticas.
- Panel de torneo activo.
- Pestañas de navegación.
- Gráfica.
- Marcador histórico.
- Estadísticas de equipos.
- Historial de torneos.
- Modal para crear torneos.
- Modal para consultar detalles.

### CSS

El CSS está dentro de `index.html`.

La capa `style#retro-theme` contiene el rediseño visual más reciente:

- Tipografías `Press Start 2P` y `VT323`, con fallback a `Courier New`.
- Paneles azul marino.
- Marcos de 2 píxeles.
- Sombras sólidas tipo pixel.
- Bordes rectos o con redondeo mínimo.
- Colores por jugador.
- Fondo de estadio más visible.
- Ajustes responsive para móvil.
- Menú de pestañas inspirado en consolas retro.

La capa visual no cambia la lógica del programa.

### JavaScript

El JavaScript está al final de `index.html` y usa JavaScript vanilla.

Funciones y responsabilidades principales:

- `readJSON`: lectura segura de datos desde `localStorage`.
- `normalizeTeams`: normalización de equipos.
- `normalizeActive`: normalización del torneo activo.
- `normalizeRecord`: normalización de torneos terminados.
- `persist`: guardado de datos.
- `remember`: creación de snapshots para deshacer.
- `render`: actualización general de la pantalla.
- `renderActive`: render del torneo actual.
- `renderChart`: render de la gráfica.
- `renderHistory`: render del historial.
- `renderTeamStats`: cálculo y render de estadísticas por equipo.
- `openTournamentModal`: apertura del formulario de nuevo torneo.
- `openDetail`: apertura del detalle de un torneo.
- `toggleLoss`: marcar o desmarcar un equipo como perdido.

## Modelo de almacenamiento

La aplicación utiliza estas claves de `localStorage`:

```text
racha-fc-data-v1
racha-fc-active-v1
racha-fc-profiles-v1
```

### Perfil

```json
{
  "one": "Eric",
  "two": "Aletz"
}
```

### Torneo activo

```json
{
  "date": "2026-09-07",
  "format": 3,
  "note": "Noche del clásico",
  "teamsOne": [
    { "name": "Real Madrid", "lost": false }
  ],
  "teamsTwo": [
    { "name": "Barcelona", "lost": false }
  ],
  "matches": []
}
```

### Torneo terminado

```json
{
  "date": "2026-09-07",
  "format": 3,
  "winner": "me",
  "players": {
    "one": "Eric",
    "two": "Aletz"
  },
  "note": "Noche del clásico",
  "teams": [
    "Real Madrid",
    "Barcelona"
  ],
  "teamOwners": [
    {
      "name": "Real Madrid",
      "owner": "Eric",
      "ownerKey": "one"
    },
    {
      "name": "Barcelona",
      "owner": "Aletz",
      "ownerKey": "two"
    }
  ],
  "eliminatedTeams": [
    "Barcelona"
  ],
  "matches": [
    {
      "one": "Real Madrid",
      "two": "Barcelona",
      "winnerTeam": "Real Madrid",
      "winnerPlayer": "Eric",
      "loserTeam": "Barcelona"
    }
  ]
}
```

Los valores históricos `winner` principales son `me` y `bro`, aunque el nombre visible se toma de `players.one` y `players.two` para permitir perfiles personalizados.

## Catálogo de equipos

`teams-fc27.js` define una variable global:

```js
window.FC27_TEAMS = [
  "Ajax",
  "Barcelona",
  "Real Madrid"
];
```

El archivo se carga antes del script principal. `index.html` transforma ese arreglo en un `<datalist>` para ofrecer coincidencias mientras se escribe.

Para agregar equipos:

1. Editar `teams-fc27.js`.
2. Agregar el nombre dentro del arreglo.
3. Mantener `window.FC27_TEAMS`.
4. Evitar duplicados.
5. Publicar nuevamente el archivo.

El catálogo actual incluye clubes y selecciones, con nombres en español e inglés según la fuente de datos utilizada.

## Reglas de negocio actuales

- Solo puede existir un torneo activo a la vez.
- Ambos jugadores deben tener la misma cantidad de equipos.
- La cantidad permitida es de 2 a 10 equipos por jugador.
- No se permiten equipos repetidos dentro del mismo torneo.
- No se permiten nombres de perfil iguales.
- Un jugador gana cuando el rival se queda sin equipos.
- Un torneo terminado se agrega al historial.
- Diez torneos ganados representan un campeonato.
- Los datos pertenecen al navegador y dispositivo donde se registraron.
- GitHub Pages publica la aplicación, pero no sincroniza los datos entre usuarios.

## Limitaciones importantes

Actualmente no existe:

- Base de datos remota.
- Inicio de sesión.
- Sincronización entre dispositivos.
- Sincronización entre Eric y Aletz en tiempo real.
- Edición de torneos ya terminados.
- Eliminación individual de un cruce del log.
- Importación de catálogos desde una API externa.
- Pruebas automatizadas.
- Framework o sistema de componentes separado.

El respaldo JSON es la forma actual de mover los datos entre dispositivos.

## Despliegue

Repositorio:

https://github.com/Animalcells/torneos-jueves

Configuración actual:

- Repositorio: `Animalcells/torneos-jueves`
- Rama publicada: `main`
- Fuente de GitHub Pages: raíz de `main`
- Hosting: GitHub Pages

Flujo de publicación utilizado:

```powershell
Copy-Item outputs/index.html work/torneos-jueves-publish/index.html -Force
Copy-Item outputs/teams-fc27.js work/torneos-jueves-publish/teams-fc27.js -Force
Copy-Item outputs/jueves-header.png work/torneos-jueves-publish/jueves-header.png -Force
Copy-Item outputs/stadium-background.png work/torneos-jueves-publish/stadium-background.png -Force

git add .
git commit -m "Descripción del cambio"
git push origin main
```

Después del push, GitHub Pages puede tardar unos segundos en actualizarse. Para comprobar una versión nueva durante desarrollo se puede usar un parámetro de consulta, por ejemplo `?v=abc123`.

## Cómo pedir una revisión a otro modelo

Se puede entregar este README junto con estos archivos:

```text
index.html
teams-fc27.js
jueves-header.png
stadium-background.png
```

Prompt sugerido:

> Revisa este proyecto como una aplicación web real ya funcional. No cambies nada todavía. Analiza la arquitectura actual, el modelo de datos, las reglas de negocio, la experiencia móvil, accesibilidad, consistencia visual, seguridad de localStorage, calidad del catálogo de equipos y facilidad de mantenimiento. Identifica errores o riesgos concretos y propón mejoras ordenadas por prioridad. Separa claramente las mejoras visuales, funcionales, técnicas y de producto. Considera que la app es local-first, está publicada como sitio estático en GitHub Pages y actualmente no tiene backend ni autenticación.

## Próximas mejoras razonables

Estas son áreas que convendría evaluar antes de ampliar el proyecto:

1. Separar HTML, CSS y JavaScript en archivos independientes.
2. Añadir pruebas para las reglas de victoria, derrotas y estadísticas.
3. Crear una capa de dominio para calcular resultados sin depender directamente del DOM.
4. Añadir edición o corrección de torneos y cruces ya guardados.
5. Hacer más explícito el formato del respaldo JSON y versionarlo.
6. Agregar validación de equipos contra el catálogo, no solo autocompletado.
7. Añadir estadísticas de enfrentamientos directos entre equipos.
8. Mejorar la navegación móvil y el foco accesible de los modales.
9. Evaluar una base de datos o sincronización solo si realmente necesitan compartir datos entre dispositivos.
10. Mantener el catálogo en un archivo o fuente versionada independiente de la interfaz.

## Nota de mantenimiento

El proyecto debe seguir siendo sencillo de respaldar y usar. Cualquier cambio futuro debería preservar:

- Las claves actuales de `localStorage`.
- La compatibilidad con respaldos JSON existentes.
- Los nombres personalizados de los perfiles.
- Los torneos de 2 a 10 equipos.
- El catálogo externo `teams-fc27.js`.
- La posibilidad de usar la app sin servidor propio.
