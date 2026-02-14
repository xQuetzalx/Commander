# Commander Arena 👑⚔️

Aplicación web progresiva (PWA) para jugar Magic: The Gathering Commander online con tus amigos.

## ✨ Características

### 📱 Instalable en Móvil

- **iOS**: Safari → Compartir → “Añadir a pantalla de inicio”
- **Android**: Chrome → Menú → “Instalar aplicación”
- Funciona **offline** después de la primera carga
- Experiencia como app nativa

### 🃏 Gestión de Mazos

- ✅ Importa mazos desde Moxfield (formato .txt)
- ✅ Detecta automáticamente el comandante
- ✅ Base de datos local con 175+ cartas populares
- ✅ Editor visual de mazos
- ✅ Búsqueda de cartas

### 🎮 Partidas Multijugador

- ✅ 2-4 jugadores en tiempo real
- ✅ Sincronización automática cada 3 segundos
- ✅ Comparte ID de partida con amigos
- ✅ Indicadores de color por jugador

### 📊 Tracking Completo

- ❤️ Contador de vida (40 inicial)
- ⚔️ Daño de comandante individual
- ☠️ Contadores de veneno
- 💬 Chat en tiempo real
- 🕐 Contador de turnos

## 🚀 Cómo Usar

### 1️⃣ Exportar Mazo desde Moxfield

1. Ve a tu mazo en [Moxfield](https://www.moxfield.com)
1. Click en “Export” → “Text”
1. Copia todo el contenido

**Formato esperado:**

```
1 Sol Ring
1 Command Tower
1 Arcane Signet
1 Sakura-Tribe Elder
...
```

### 2️⃣ Importar en la App

1. Abre la app → “Mis Mazos”
1. Click en “Importar”
1. Pega el texto completo
1. Click en “Importar Mazo”

### 3️⃣ Crear Partida

1. Selecciona un mazo
1. Click en “Iniciar Partida”
1. **Copia el ID de la partida** (ej: `game-1234567890`)
1. Comparte el ID con tus amigos

### 4️⃣ Unirse a Partida

1. Desde la pantalla principal → “Unirse a Partida”
1. Introduce el ID que te compartieron
1. ¡Empieza a jugar!

## 📲 Instalación en Móvil

### iPhone/iPad

1. Abre Safari
1. Navega a la URL de la app
1. Toca el botón “Compartir” (cuadrado con flecha)
1. Desplázate y selecciona “Añadir a pantalla de inicio”
1. Toca “Añadir”
1. ¡La app aparecerá en tu pantalla de inicio!

### Android

1. Abre Chrome
1. Navega a la URL de la app
1. Toca el menú (tres puntos)
1. Selecciona “Instalar aplicación” o “Añadir a pantalla de inicio”
1. Toca “Instalar”
1. ¡La app aparecerá como cualquier otra app!

## 🎨 Interfaz

- **Diseño místico** con gradientes violeta/fucsia
- **Tema oscuro** optimizado para jugar de noche
- **Tipografía elegante** Palatino serif
- **Animaciones suaves** y transiciones fluidas
- **Colores distintivos** por jugador
- **Feedback visual** para acciones importantes

## 🔧 Tecnología

- **React 18** - Framework UI
- **Tailwind CSS** - Estilos
- **Lucide Icons** - Iconografía
- **Scryfall API** - Base de datos de cartas
- **Storage API** - Persistencia de datos
- **Service Worker** - Funcionalidad offline
- **PWA** - Instalable en móvil

## 📝 Formato de Importación

La app acepta el formato estándar de exportación de Moxfield:

```
Deck: Nombre del Mazo

Commander:
1 Atraxa, Praetors' Voice

Main:
1 Sol Ring
1 Command Tower
1 Arcane Signet
2 Forest
3 Island
...
```

**También funciona con formato simple:**

```
1 Sol Ring
1 Command Tower
1 Arcane Signet
1x Sakura-Tribe Elder
```

## 🎯 Consejos

- **Comparte el ID completo**: Incluye el prefijo `game-`
- **Mantén la app abierta**: Para recibir actualizaciones en tiempo real
- **Usa el chat**: Comunícate con otros jugadores
- **Colores de vida**:
  - 🟢 Verde: > 20 vida
  - 🟡 Amarillo: 11-20 vida
  - 🔴 Rojo: ≤ 10 vida
- **Límites de daño**:
  - Comandante: 21 puntos = eliminado
  - Veneno: 10 contadores = eliminado

## 🐛 Solución de Problemas

**“No se importan las cartas”**

- Verifica que el formato sea correcto (número + nombre)
- Asegúrate de copiar TODO el texto del mazo
- Evita líneas vacías al inicio/final

**“No se actualiza la partida”**

- Verifica tu conexión a internet
- Refresca la página (pull to refresh)
- Comprueba que todos usen el mismo ID

**“La app no se instala”**

- iOS: Usa Safari (no Chrome)
- Android: Usa Chrome (no otros navegadores)
- Verifica que tengas espacio en tu dispositivo

## 🔐 Privacidad

- ✅ Todos los datos se guardan **localmente** en tu dispositivo
- ✅ Las partidas usan almacenamiento **compartido temporal**
- ✅ **No hay servidor** - todo es P2P vía storage
- ✅ **No se recopilan datos personales**
- ✅ **No hay anuncios ni tracking**

## 📱 Compatibilidad

- ✅ iOS 12+
- ✅ Android 5+
- ✅ Chrome, Safari, Firefox, Edge
- ✅ Tablets y móviles
- ✅ Modo oscuro nativo

## 🎮 Próximas Características

- [ ] Dado virtual
- [ ] Contador de maná
- [ ] Mulligan tracker
- [ ] Historial de partidas
- [ ] Estadísticas de mazos
- [ ] Temas personalizables
- [ ] Notificaciones push
- [ ] Modo espectador

## 💡 Créditos

- **Cartas**: [Scryfall API](https://scryfall.com)
- **Iconos**: [Lucide Icons](https://lucide.dev)
- **Inspiración**: La comunidad de Magic: The Gathering

-----

**¡Que ganen los mejores planeswalkers!** 👑⚔️

Para reportar bugs o sugerir mejoras, usa el sistema de feedback de la app.
