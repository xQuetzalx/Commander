import React, { useState, useEffect, useRef } from ‘react’;
import { Search, Plus, Trash2, Edit2, Users, Play, Home, BookOpen, Download, Upload, X, Check, Swords, Heart, Skull, Menu, ChevronLeft, Save, Copy, Share2, Settings, Zap, Shield, Crown, MessageCircle, Send, MoreVertical, Info } from ‘lucide-react’;

export default function MTGCommanderApp() {
const [currentView, setCurrentView] = useState(‘home’);
const [decks, setDecks] = useState([]);
const [currentGame, setCurrentGame] = useState(null);
const [playerName, setPlayerName] = useState(’’);
const [showImportModal, setShowImportModal] = useState(false);
const [showDeckDetail, setShowDeckDetail] = useState(null);
const [importText, setImportText] = useState(’’);
const [importError, setImportError] = useState(’’);
const [searchQuery, setSearchQuery] = useState(’’);
const [cardDatabase, setCardDatabase] = useState([]);
const [loading, setLoading] = useState(true);
const [showChat, setShowChat] = useState(false);
const [chatMessage, setChatMessage] = useState(’’);
const chatEndRef = useRef(null);

// Cargar base de datos de cartas al inicio
useEffect(() => {
loadCardDatabase();
loadDecks();
loadPlayerName();
}, []);

const loadCardDatabase = async () => {
setLoading(true);
try {
// Cargar cartas populares de Commander desde Scryfall
const response = await fetch(‘https://api.scryfall.com/cards/search?q=format:commander&order=edhrec&unique=cards’);
const data = await response.json();

```
  if (data.data) {
    const cards = data.data.map(card => ({
      name: card.name,
      type: card.type_line,
      colors: card.colors || [],
      image: card.image_uris?.normal || card.card_faces?.[0]?.image_uris?.normal,
      isCommander: card.type_line?.includes('Legendary') && card.type_line?.includes('Creature'),
      manaCost: card.mana_cost,
      set: card.set_name
    }));
    
    setCardDatabase(cards);
    
    // Guardar en storage local para uso offline
    await window.storage.set('mtg-card-database', JSON.stringify(cards));
  }
} catch (error) {
  console.error('Error cargando base de datos:', error);
  // Intentar cargar desde storage local
  try {
    const result = await window.storage.get('mtg-card-database');
    if (result && result.value) {
      setCardDatabase(JSON.parse(result.value));
    }
  } catch (e) {
    console.log('No hay base de datos local');
  }
}
setLoading(false);
```

};

const loadDecks = async () => {
try {
const result = await window.storage.get(‘mtg-decks’);
if (result && result.value) {
setDecks(JSON.parse(result.value));
}
} catch (error) {
console.log(‘No hay mazos guardados’);
}
};

const loadPlayerName = async () => {
try {
const result = await window.storage.get(‘mtg-player-name’);
if (result && result.value) {
setPlayerName(result.value);
}
} catch (error) {
console.log(‘No hay nombre guardado’);
}
};

const saveDecks = async (newDecks) => {
try {
await window.storage.set(‘mtg-decks’, JSON.stringify(newDecks));
setDecks(newDecks);
} catch (error) {
console.error(‘Error guardando mazos:’, error);
}
};

const savePlayerName = async (name) => {
try {
await window.storage.set(‘mtg-player-name’, name);
setPlayerName(name);
} catch (error) {
console.error(‘Error guardando nombre:’, error);
}
};

const parseMoxfieldDeck = (text) => {
setImportError(’’);

```
if (!text.trim()) {
  setImportError('El texto está vacío');
  return null;
}

const lines = text.split('\n').filter(line => line.trim());
const cards = [];
let commander = null;
let deckName = 'Mazo Importado ' + new Date().toLocaleDateString();

console.log('Parseando', lines.length, 'líneas');

for (let line of lines) {
  // Ignorar líneas de sección
  if (line.match(/^(Commander|Deck|Companion|Sideboard):/i)) {
    const match = line.match(/^(Commander|Deck|Companion|Sideboard):\s*(.+)/i);
    if (match && match[1].toLowerCase() === 'deck') {
      deckName = match[2].trim();
    }
    continue;
  }

  // Ignorar líneas vacías o comentarios
  if (!line.trim() || line.startsWith('//') || line.startsWith('#')) {
    continue;
  }

  // Parsear formato: "1 Nombre de Carta" o "1x Nombre de Carta" o "Nombre de Carta"
  let match = line.match(/^(\d+)x?\s+(.+?)(?:\s+\([A-Z0-9]+\))?(?:\s+\d+)?$/);
  
  if (!match) {
    // Intentar sin cantidad (asumir 1)
    match = line.match(/^(.+?)(?:\s+\([A-Z0-9]+\))?(?:\s+\d+)?$/);
    if (match) {
      match = [line, '1', match[1]];
    }
  }

  if (match) {
    const quantity = parseInt(match[1]);
    let cardName = match[2].trim();
    
    // Limpiar nombre (remover set codes y números)
    cardName = cardName.replace(/\s+\([A-Z0-9]+\)\s*\d*$/, '').trim();
    
    if (cardName) {
      // El primer carta legendaria es probablemente el comandante
      const cardInfo = cardDatabase.find(c => 
        c.name.toLowerCase() === cardName.toLowerCase()
      );
      
      if (!commander && cardInfo && cardInfo.isCommander) {
        commander = cardName;
      }
      
      cards.push({ 
        name: cardName, 
        quantity,
        image: cardInfo?.image || null
      });
      
      console.log('Carta añadida:', cardName, 'x', quantity);
    }
  }
}

if (cards.length === 0) {
  setImportError('No se encontraron cartas válidas. Asegúrate de usar el formato: "1 Nombre de Carta"');
  return null;
}

console.log('Total cartas importadas:', cards.length);
console.log('Comandante detectado:', commander);

return { 
  name: deckName, 
  commander, 
  cards, 
  createdAt: new Date().toISOString(),
  totalCards: cards.reduce((sum, c) => sum + c.quantity, 0)
};
```

};

const handleImportDeck = () => {
const newDeck = parseMoxfieldDeck(importText);

```
if (newDeck) {
  const updatedDecks = [...decks, { ...newDeck, id: Date.now().toString() }];
  saveDecks(updatedDecks);
  setImportText('');
  setShowImportModal(false);
  setImportError('');
}
```

};

const handleDeleteDeck = (deckId) => {
if (confirm(’¿Estás seguro de eliminar este mazo?’)) {
const updatedDecks = decks.filter(d => d.id !== deckId);
saveDecks(updatedDecks);
}
};

const startGame = async (deck) => {
if (!playerName.trim()) {
alert(‘Por favor introduce tu nombre primero’);
return;
}

```
const gameId = 'game-' + Date.now();
const game = {
  id: gameId,
  players: [{
    id: Date.now().toString(),
    name: playerName,
    life: 40,
    commanderDamage: {},
    poison: 0,
    deck: deck,
    color: '#8b5cf6' // Púrpura por defecto
  }],
  messages: [],
  turn: 1,
  activePlayer: 0,
  createdAt: new Date().toISOString()
};

try {
  await window.storage.set(gameId, JSON.stringify(game), true);
  setCurrentGame(game);
  setCurrentView('game');
} catch (error) {
  console.error('Error creando partida:', error);
  alert('Error al crear la partida');
}
```

};

const joinGame = async () => {
const gameId = prompt(‘Introduce el ID de la partida (ej: game-1234567890):’);
if (!gameId) return;

```
if (!playerName.trim()) {
  alert('Por favor introduce tu nombre primero');
  return;
}

try {
  const result = await window.storage.get(gameId, true);
  if (result && result.value) {
    const game = JSON.parse(result.value);
    
    // Verificar si ya está en la partida
    const existingPlayer = game.players.find(p => p.name === playerName);
    if (!existingPlayer) {
      // Colores disponibles
      const colors = ['#8b5cf6', '#ec4899', '#10b981', '#f59e0b', '#3b82f6', '#ef4444'];
      const usedColors = game.players.map(p => p.color);
      const availableColor = colors.find(c => !usedColors.includes(c)) || colors[0];
      
      game.players.push({
        id: Date.now().toString(),
        name: playerName,
        life: 40,
        commanderDamage: {},
        poison: 0,
        deck: null,
        color: availableColor
      });
      
      await window.storage.set(gameId, JSON.stringify(game), true);
    }
    
    setCurrentGame(game);
    setCurrentView('game');
  } else {
    alert('No se encontró la partida con ese ID');
  }
} catch (error) {
  console.error('Error uniéndose a la partida:', error);
  alert('Error al unirse a la partida');
}
```

};

const updateGame = async (updatedGame) => {
try {
await window.storage.set(updatedGame.id, JSON.stringify(updatedGame), true);
setCurrentGame(updatedGame);
} catch (error) {
console.error(‘Error actualizando partida:’, error);
}
};

const updatePlayerLife = (playerId, amount) => {
const updatedGame = {
…currentGame,
players: currentGame.players.map(p =>
p.id === playerId ? { …p, life: Math.max(0, p.life + amount) } : p
)
};
updateGame(updatedGame);
};

const updateCommanderDamage = (playerId, fromPlayerId, amount) => {
const updatedGame = {
…currentGame,
players: currentGame.players.map(p => {
if (p.id === playerId) {
const currentDamage = p.commanderDamage[fromPlayerId] || 0;
return {
…p,
commanderDamage: {
…p.commanderDamage,
[fromPlayerId]: Math.max(0, currentDamage + amount)
}
};
}
return p;
})
};
updateGame(updatedGame);
};

const updatePoison = (playerId, amount) => {
const updatedGame = {
…currentGame,
players: currentGame.players.map(p =>
p.id === playerId ? { …p, poison: Math.max(0, Math.min(10, p.poison + amount)) } : p
)
};
updateGame(updatedGame);
};

const sendMessage = () => {
if (!chatMessage.trim()) return;

```
const updatedGame = {
  ...currentGame,
  messages: [
    ...currentGame.messages,
    {
      id: Date.now().toString(),
      player: playerName,
      text: chatMessage,
      timestamp: new Date().toISOString()
    }
  ]
};
updateGame(updatedGame);
setChatMessage('');
```

};

const copyGameId = () => {
navigator.clipboard.writeText(currentGame.id);
alert(‘ID copiado al portapapeles’);
};

// Poll para actualizar la partida
useEffect(() => {
if (!currentGame) return;

```
const interval = setInterval(async () => {
  try {
    const result = await window.storage.get(currentGame.id, true);
    if (result && result.value) {
      const game = JSON.parse(result.value);
      setCurrentGame(game);
    }
  } catch (error) {
    console.error('Error cargando partida:', error);
  }
}, 3000);

return () => clearInterval(interval);
```

}, [currentGame?.id]);

// Auto-scroll del chat
useEffect(() => {
if (showChat && chatEndRef.current) {
chatEndRef.current.scrollIntoView({ behavior: ‘smooth’ });
}
}, [currentGame?.messages, showChat]);

const filteredDecks = decks.filter(deck =>
deck.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
deck.commander?.toLowerCase().includes(searchQuery.toLowerCase())
);

if (loading) {
return (
<div className="min-h-screen bg-gradient-to-br from-slate-950 via-violet-950 to-slate-950 flex items-center justify-center">
<div className="text-center">
<div className="w-16 h-16 border-4 border-violet-400 border-t-transparent rounded-full animate-spin mx-auto mb-4"></div>
<p className="text-violet-300 text-lg font-semibold">Cargando base de datos…</p>
<p className="text-violet-400/60 text-sm mt-2">{cardDatabase.length} cartas cargadas</p>
</div>
</div>
);
}

// Vista principal
if (currentView === ‘home’) {
return (
<div className="min-h-screen bg-gradient-to-br from-slate-950 via-violet-950 to-slate-950">
{/* Header con efecto glassmorphism */}
<div className="relative overflow-hidden">
<div className="absolute inset-0 bg-gradient-to-r from-violet-600/20 via-fuchsia-600/20 to-violet-600/20 blur-3xl"></div>
<div className="relative backdrop-blur-xl bg-black/40 border-b border-violet-500/20">
<div className="px-6 py-8">
<div className="flex items-center gap-3 mb-2">
<Crown className="w-10 h-10 text-amber-400" />
<h1 className=“text-4xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-violet-200 via-fuchsia-200 to-violet-200” style={{fontFamily: ‘Palatino, serif’, letterSpacing: ‘0.02em’}}>
Commander Arena
</h1>
</div>
<p className="text-violet-300/70 text-sm ml-13">Tu mesa digital de Magic: The Gathering</p>
</div>
</div>
</div>

```
    <div className="px-6 py-8 space-y-6">
      {/* Sección de jugador */}
      <div className="backdrop-blur-xl bg-white/5 border border-violet-500/20 rounded-2xl p-6 shadow-2xl">
        <label className="flex items-center gap-2 text-violet-300 text-sm font-semibold mb-3">
          <Users className="w-4 h-4" />
          Tu Identidad
        </label>
        <input
          type="text"
          value={playerName}
          onChange={(e) => savePlayerName(e.target.value)}
          placeholder="Introduce tu nombre de invocador..."
          className="w-full bg-slate-900/50 border border-violet-500/30 rounded-xl px-4 py-3 text-white placeholder-violet-400/40 focus:outline-none focus:border-violet-400 focus:ring-2 focus:ring-violet-400/20 transition-all"
        />
      </div>

      {/* Cards de acción */}
      <div className="grid gap-4">
        <button
          onClick={() => setCurrentView('decks')}
          className="group relative overflow-hidden backdrop-blur-xl bg-gradient-to-r from-violet-600/20 to-fuchsia-600/20 hover:from-violet-600/30 hover:to-fuchsia-600/30 border border-violet-500/30 rounded-2xl p-6 transition-all hover:scale-[1.02] active:scale-[0.98] shadow-xl"
        >
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-4">
              <div className="p-3 bg-violet-500/20 rounded-xl">
                <BookOpen className="w-7 h-7 text-violet-300" />
              </div>
              <div className="text-left">
                <h3 className="text-xl font-bold text-white mb-1">Mis Mazos</h3>
                <p className="text-violet-300/70 text-sm">{decks.length} mazos guardados</p>
              </div>
            </div>
            <ChevronLeft className="w-6 h-6 text-violet-300/50 rotate-180 group-hover:translate-x-1 transition-transform" />
          </div>
        </button>

        <button
          onClick={joinGame}
          className="group relative overflow-hidden backdrop-blur-xl bg-gradient-to-r from-fuchsia-600/20 to-pink-600/20 hover:from-fuchsia-600/30 hover:to-pink-600/30 border border-fuchsia-500/30 rounded-2xl p-6 transition-all hover:scale-[1.02] active:scale-[0.98] shadow-xl"
        >
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-4">
              <div className="p-3 bg-fuchsia-500/20 rounded-xl">
                <Users className="w-7 h-7 text-fuchsia-300" />
              </div>
              <div className="text-left">
                <h3 className="text-xl font-bold text-white mb-1">Unirse a Partida</h3>
                <p className="text-fuchsia-300/70 text-sm">Juega con tus amigos</p>
              </div>
            </div>
            <ChevronLeft className="w-6 h-6 text-fuchsia-300/50 rotate-180 group-hover:translate-x-1 transition-transform" />
          </div>
        </button>
      </div>

      {/* Info card */}
      <div className="backdrop-blur-xl bg-violet-500/5 border border-violet-500/20 rounded-2xl p-6">
        <div className="flex items-start gap-3 mb-4">
          <Info className="w-5 h-5 text-violet-400 mt-0.5" />
          <div>
            <h3 className="text-violet-200 font-semibold mb-2">Cómo Empezar</h3>
            <ul className="space-y-2 text-violet-300/70 text-sm">
              <li className="flex items-start gap-2">
                <span className="text-violet-400 font-bold">1.</span>
                <span>Importa tus mazos en formato .txt desde Moxfield</span>
              </li>
              <li className="flex items-start gap-2">
                <span className="text-violet-400 font-bold">2.</span>
                <span>Crea una partida y comparte el ID con amigos</span>
              </li>
              <li className="flex items-start gap-2">
                <span className="text-violet-400 font-bold">3.</span>
                <span>¡Disfruta jugando Commander online!</span>
              </li>
            </ul>
          </div>
        </div>
      </div>

      {/* Stats */}
      <div className="grid grid-cols-2 gap-4">
        <div className="backdrop-blur-xl bg-white/5 border border-violet-500/10 rounded-xl p-4">
          <p className="text-violet-400/60 text-xs mb-1">Base de Datos</p>
          <p className="text-2xl font-bold text-white">{cardDatabase.length}</p>
          <p className="text-violet-300/50 text-xs">cartas</p>
        </div>
        <div className="backdrop-blur-xl bg-white/5 border border-violet-500/10 rounded-xl p-4">
          <p className="text-violet-400/60 text-xs mb-1">Tus Mazos</p>
          <p className="text-2xl font-bold text-white">{decks.length}</p>
          <p className="text-violet-300/50 text-xs">colecciones</p>
        </div>
      </div>
    </div>
  </div>
);
```

}

// Vista de mazos
if (currentView === ‘decks’) {
return (
<div className="min-h-screen bg-gradient-to-br from-slate-950 via-violet-950 to-slate-950">
{/* Header */}
<div className="sticky top-0 z-20 backdrop-blur-xl bg-black/40 border-b border-violet-500/20">
<div className="px-6 py-4 flex items-center justify-between">
<div className="flex items-center gap-3">
<button
onClick={() => setCurrentView(‘home’)}
className=“text-violet-300 hover:text-violet-200 transition-colors”
>
<ChevronLeft className="w-6 h-6" />
</button>
<h2 className="text-2xl font-bold text-white">Mis Mazos</h2>
</div>
<button
onClick={() => setShowImportModal(true)}
className=“flex items-center gap-2 bg-gradient-to-r from-violet-600 to-fuchsia-600 hover:from-violet-500 hover:to-fuchsia-500 text-white px-4 py-2 rounded-xl transition-all shadow-lg”
>
<Plus className="w-5 h-5" />
<span className="hidden sm:inline">Importar</span>
</button>
</div>

```
      {/* Búsqueda */}
      <div className="px-6 pb-4">
        <div className="relative">
          <Search className="absolute left-3 top-1/2 -translate-y-1/2 w-5 h-5 text-violet-400" />
          <input
            type="text"
            value={searchQuery}
            onChange={(e) => setSearchQuery(e.target.value)}
            placeholder="Buscar mazos..."
            className="w-full pl-10 pr-4 py-3 bg-slate-900/50 border border-violet-500/30 rounded-xl text-white placeholder-violet-400/40 focus:outline-none focus:border-violet-400 focus:ring-2 focus:ring-violet-400/20 transition-all"
          />
        </div>
      </div>
    </div>

    {/* Lista de mazos */}
    <div className="p-6 space-y-4">
      {filteredDecks.length === 0 ? (
        <div className="text-center py-16">
          <BookOpen className="w-20 h-20 text-violet-400/30 mx-auto mb-4" />
          <p className="text-violet-300/70 text-lg mb-2">
            {searchQuery ? 'No se encontraron mazos' : 'No tienes mazos guardados'}
          </p>
          <p className="text-violet-400/50 text-sm">
            {searchQuery ? 'Intenta con otra búsqueda' : 'Importa un mazo desde Moxfield para comenzar'}
          </p>
        </div>
      ) : (
        filteredDecks.map(deck => (
          <div 
            key={deck.id} 
            className="group backdrop-blur-xl bg-white/5 border border-violet-500/20 hover:border-violet-400/40 rounded-2xl p-5 transition-all hover:shadow-2xl hover:shadow-violet-500/10"
          >
            <div className="flex items-start justify-between mb-4">
              <div className="flex-1">
                <h3 className="text-xl font-bold text-white mb-2">{deck.name}</h3>
                {deck.commander && (
                  <div className="flex items-center gap-2 mb-2">
                    <Crown className="w-4 h-4 text-amber-400" />
                    <span className="text-violet-300">{deck.commander}</span>
                  </div>
                )}
                <div className="flex items-center gap-4 text-sm text-violet-400/60">
                  <span>{deck.totalCards || deck.cards.reduce((sum, c) => sum + c.quantity, 0)} cartas</span>
                  <span>•</span>
                  <span>{new Date(deck.createdAt).toLocaleDateString('es-ES')}</span>
                </div>
              </div>
              <div className="flex gap-2">
                <button
                  onClick={() => setShowDeckDetail(deck)}
                  className="p-2 text-violet-400 hover:text-violet-300 hover:bg-violet-500/10 rounded-lg transition-all"
                >
                  <MoreVertical className="w-5 h-5" />
                </button>
              </div>
            </div>
            
            <button
              onClick={() => startGame(deck)}
              className="w-full bg-gradient-to-r from-violet-600 to-fuchsia-600 hover:from-violet-500 hover:to-fuchsia-500 text-white rounded-xl px-4 py-3 flex items-center justify-center gap-2 transition-all shadow-lg font-semibold"
            >
              <Play className="w-5 h-5" />
              Iniciar Partida
            </button>
          </div>
        ))
      )}
    </div>

    {/* Modal de importación */}
    {showImportModal && (
      <div className="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 z-50">
        <div className="backdrop-blur-xl bg-slate-900/95 border border-violet-500/30 rounded-2xl max-w-2xl w-full max-h-[90vh] overflow-hidden flex flex-col shadow-2xl">
          <div className="px-6 py-5 border-b border-violet-500/20 flex items-center justify-between">
            <h3 className="text-2xl font-bold text-white">Importar Mazo</h3>
            <button 
              onClick={() => {
                setShowImportModal(false);
                setImportError('');
                setImportText('');
              }} 
              className="text-violet-400 hover:text-violet-300 transition-colors"
            >
              <X className="w-6 h-6" />
            </button>
          </div>
          
          <div className="p-6 flex-1 overflow-y-auto">
            <div className="mb-4">
              <p className="text-violet-300 text-sm mb-2">
                Pega el contenido de tu archivo .txt exportado desde Moxfield:
              </p>
              <div className="bg-violet-500/10 border border-violet-500/20 rounded-lg p-3 mb-4">
                <p className="text-violet-300/70 text-xs font-mono">
                  Formato esperado:<br/>
                  1 Sol Ring<br/>
                  1 Command Tower<br/>
                  1 Arcane Signet<br/>
                  ...
                </p>
              </div>
            </div>
            
            <textarea
              value={importText}
              onChange={(e) => setImportText(e.target.value)}
              placeholder="1 Sol Ring&#10;1 Command Tower&#10;1 Arcane Signet&#10;1 Sakura-Tribe Elder&#10;..."
              className="w-full h-80 bg-slate-800/50 border border-violet-500/30 rounded-xl px-4 py-3 text-white placeholder-violet-400/40 focus:outline-none focus:border-violet-400 focus:ring-2 focus:ring-violet-400/20 resize-none font-mono text-sm transition-all"
            />
            
            {importError && (
              <div className="mt-4 bg-red-500/10 border border-red-500/30 rounded-lg p-4">
                <p className="text-red-300 text-sm">{importError}</p>
              </div>
            )}
          </div>
          
          <div className="px-6 py-5 border-t border-violet-500/20 flex gap-3">
            <button
              onClick={() => {
                setShowImportModal(false);
                setImportError('');
                setImportText('');
              }}
              className="flex-1 bg-slate-700/50 hover:bg-slate-700 text-white rounded-xl px-4 py-3 transition-all font-semibold"
            >
              Cancelar
            </button>
            <button
              onClick={handleImportDeck}
              className="flex-1 bg-gradient-to-r from-violet-600 to-fuchsia-600 hover:from-violet-500 hover:to-fuchsia-500 text-white rounded-xl px-4 py-3 flex items-center justify-center gap-2 transition-all shadow-lg font-semibold"
            >
              <Download className="w-5 h-5" />
              Importar Mazo
            </button>
          </div>
        </div>
      </div>
    )}

    {/* Modal de detalle de mazo */}
    {showDeckDetail && (
      <div className="fixed inset-0 bg-black/80 backdrop-blur-sm flex items-center justify-center p-4 z-50">
        <div className="backdrop-blur-xl bg-slate-900/95 border border-violet-500/30 rounded-2xl max-w-2xl w-full max-h-[90vh] overflow-hidden flex flex-col shadow-2xl">
          <div className="px-6 py-5 border-b border-violet-500/20 flex items-center justify-between">
            <h3 className="text-2xl font-bold text-white">{showDeckDetail.name}</h3>
            <button 
              onClick={() => setShowDeckDetail(null)} 
              className="text-violet-400 hover:text-violet-300 transition-colors"
            >
              <X className="w-6 h-6" />
            </button>
          </div>
          
          <div className="p-6 flex-1 overflow-y-auto">
            <div className="mb-6">
              {showDeckDetail.commander && (
                <div className="flex items-center gap-2 mb-3">
                  <Crown className="w-5 h-5 text-amber-400" />
                  <span className="text-violet-200 font-semibold">{showDeckDetail.commander}</span>
                </div>
              )}
              <p className="text-violet-400/60 text-sm">
                {showDeckDetail.totalCards || showDeckDetail.cards.reduce((sum, c) => sum + c.quantity, 0)} cartas totales
              </p>
            </div>
            
            <div className="space-y-2">
              {showDeckDetail.cards.map((card, index) => (
                <div 
                  key={index} 
                  className="flex items-center justify-between bg-slate-800/30 border border-violet-500/10 rounded-lg px-4 py-3 hover:bg-slate-800/50 transition-all"
                >
                  <span className="text-violet-200">{card.name}</span>
                  <span className="text-violet-400/60 text-sm">x{card.quantity}</span>
                </div>
              ))}
            </div>
          </div>
          
          <div className="px-6 py-5 border-t border-violet-500/20 flex gap-3">
            <button
              onClick={() => {
                if (confirm('¿Estás seguro de eliminar este mazo?')) {
                  handleDeleteDeck(showDeckDetail.id);
                  setShowDeckDetail(null);
                }
              }}
              className="flex-1 bg-red-500/20 hover:bg-red-500/30 border border-red-500/30 text-red-300 rounded-xl px-4 py-3 flex items-center justify-center gap-2 transition-all font-semibold"
            >
              <Trash2 className="w-5 h-5" />
              Eliminar
            </button>
            <button
              onClick={() => {
                startGame(showDeckDetail);
                setShowDeckDetail(null);
              }}
              className="flex-1 bg-gradient-to-r from-violet-600 to-fuchsia-600 hover:from-violet-500 hover:to-fuchsia-500 text-white rounded-xl px-4 py-3 flex items-center justify-center gap-2 transition-all shadow-lg font-semibold"
            >
              <Play className="w-5 h-5" />
              Jugar
            </button>
          </div>
        </div>
      </div>
    )}
  </div>
);
```

}

// Vista de juego
if (currentView === ‘game’ && currentGame) {
const currentPlayer = currentGame.players.find(p => p.name === playerName);

```
return (
  <div className="min-h-screen bg-gradient-to-br from-slate-950 via-violet-950 to-slate-950 pb-20">
    {/* Header de partida */}
    <div className="sticky top-0 z-20 backdrop-blur-xl bg-black/40 border-b border-violet-500/20">
      <div className="px-4 py-3 flex items-center justify-between">
        <div className="flex items-center gap-3">
          <button 
            onClick={() => {
              if (confirm('¿Salir de la partida?')) {
                setCurrentView('home');
                setCurrentGame(null);
              }
            }} 
            className="text-violet-300 hover:text-violet-200 transition-colors"
          >
            <ChevronLeft className="w-6 h-6" />
          </button>
          <div>
            <h2 className="text-lg font-bold text-white">Partida en Curso</h2>
            <p className="text-xs text-violet-400">Turno {currentGame.turn}</p>
          </div>
        </div>
        <div className="flex items-center gap-2">
          <button
            onClick={copyGameId}
            className="p-2 text-violet-400 hover:text-violet-300 hover:bg-violet-500/10 rounded-lg transition-all"
          >
            <Copy className="w-5 h-5" />
          </button>
          <button
            onClick={() => setShowChat(!showChat)}
            className="relative p-2 text-violet-400 hover:text-violet-300 hover:bg-violet-500/10 rounded-lg transition-all"
          >
            <MessageCircle className="w-5 h-5" />
            {currentGame.messages.length > 0 && (
              <span className="absolute -top-1 -right-1 w-3 h-3 bg-fuchsia-500 rounded-full"></span>
            )}
          </button>
        </div>
      </div>
    </div>

    {/* Grid de jugadores */}
    <div className="p-4 grid grid-cols-1 md:grid-cols-2 gap-4">
      {currentGame.players.map((player) => (
        <div
          key={player.id}
          className="backdrop-blur-xl bg-white/5 border rounded-2xl p-5 transition-all"
          style={{
            borderColor: player.id === currentPlayer?.id ? player.color : 'rgba(139, 92, 246, 0.2)',
            boxShadow: player.id === currentPlayer?.id ? `0 0 20px ${player.color}40` : 'none'
          }}
        >
          {/* Header del jugador */}
          <div className="flex items-center justify-between mb-4">
            <div className="flex items-center gap-3">
              <div 
                className="w-3 h-3 rounded-full"
                style={{ backgroundColor: player.color }}
              ></div>
              <div>
                <h3 className="text-lg font-bold text-white">{player.name}</h3>
                {player.deck?.commander && (
                  <p className="text-xs text-violet-400 flex items-center gap-1">
                    <Crown className="w-3 h-3 text-amber-400" />
                    {player.deck.commander}
                  </p>
                )}
              </div>
            </div>
            {player.id === currentPlayer?.id && (
              <span className="bg-violet-600/30 border border-violet-500/50 text-violet-200 text-xs px-3 py-1 rounded-full font-semibold">
                Tú
              </span>
            )}
          </div>

          {/* Vida */}
          <div className="mb-4">
            <div className="flex items-center justify-between mb-3">
              <div className="flex items-center gap-2">
                <Heart className="w-5 h-5 text-red-400" />
                <span className="text-sm text-violet-300 font-semibold">Vida</span>
              </div>
              <span 
                className="text-4xl font-bold tabular-nums"
                style={{
                  color: player.life > 20 ? '#10b981' : player.life > 10 ? '#f59e0b' : '#ef4444'
                }}
              >
                {player.life}
              </span>
            </div>
            {player.id === currentPlayer?.id && (
              <div className="grid grid-cols-4 gap-2">
                <button
                  onClick={() => updatePlayerLife(player.id, -10)}
                  className="bg-red-500/20 border border-red-500/30 hover:bg-red-500/30 text-red-300 rounded-lg py-2 text-sm font-semibold transition-all"
                >
                  -10
                </button>
                <button
                  onClick={() => updatePlayerLife(player.id, -1)}
                  className="bg-red-500/20 border border-red-500/30 hover:bg-red-500/30 text-red-300 rounded-lg py-2 text-sm font-semibold transition-all"
                >
                  -1
                </button>
                <button
                  onClick={() => updatePlayerLife(player.id, 1)}
                  className="bg-green-500/20 border border-green-500/30 hover:bg-green-500/30 text-green-300 rounded-lg py-2 text-sm font-semibold transition-all"
                >
                  +1
                </button>
                <button
                  onClick={() => updatePlayerLife(player.id, 10)}
                  className="bg-green-500/20 border border-green-500/30 hover:bg-green-500/30 text-green-300 rounded-lg py-2 text-sm font-semibold transition-all"
                >
                  +10
                </button>
              </div>
            )}
          </div>

          {/* Daño de comandante */}
          {currentGame.players.length > 1 && (
            <div className="mb-4">
              <div className="flex items-center gap-2 mb-2">
                <Swords className="w-4 h-4 text-orange-400" />
                <span className="text-xs text-violet-300 font-semibold">Daño de Comandante</span>
              </div>
              <div className="space-y-2">
                {currentGame.players.filter(p => p.id !== player.id).map(opponent => (
                  <div key={opponent.id} className="flex items-center justify-between text-sm bg-slate-800/30 rounded-lg px-3 py-2">
                    <span className="text-violet-400 flex items-center gap-2">
                      <div 
                        className="w-2 h-2 rounded-full"
                        style={{ backgroundColor: opponent.color }}
                      ></div>
                      {opponent.name}
                    </span>
                    <div className="flex items-center gap-2">
                      <span 
                        className="font-bold tabular-nums"
                        style={{
                          color: (player.commanderDamage[opponent.id] || 0) >= 15 ? '#ef4444' : '#a78bfa'
                        }}
                      >
                        {player.commanderDamage[opponent.id] || 0}
                      </span>
                      {player.id === currentPlayer?.id && (
                        <div className="flex gap-1">
                          <button
                            onClick={() => updateCommanderDamage(player.id, opponent.id, 1)}
                            className="w-7 h-7 bg-orange-500/20 border border-orange-500/30 hover:bg-orange-500/30 text-orange-300 rounded text-xs font-bold transition-all"
                          >
                            +
                          </button>
                          <button
                            onClick={() => updateCommanderDamage(player.id, opponent.id, -1)}
                            className="w-7 h-7 bg-orange-500/20 border border-orange-500/30 hover:bg-orange-500/30 text-orange-300 rounded text-xs font-bold transition-all"
                          >
                            -
                          </button>
                        </div>
                      )}
                    </div>
                  </div>
                ))}
              </div>
            </div>
          )}

          {/* Veneno */}
          <div className="flex items-center justify-between bg-slate-800/30 rounded-lg px-3 py-2">
            <div className="flex items-center gap-2">
              <Skull className="w-4 h-4 text-green-400" />
              <span className="text-sm text-violet-300 font-semibold">Veneno</span>
            </div>
            <div className="flex items-center gap-2">
              <span 
                className="text-lg font-bold tabular-nums"
                style={{
                  color: player.poison >= 8 ? '#ef4444' : player.poison >= 5 ? '#f59e0b' : '#10b981'
                }}
              >
                {player.poison}
              </span>
              {player.id === currentPlayer?.id && (
                <div className="flex gap-1">
                  <button
                    onClick={() => updatePoison(player.id, 1)}
                    className="w-7 h-7 bg-green-500/20 border border-green-500/30 hover:bg-green-500/30 text-green-300 rounded text-xs font-bold transition-all"
                  >
                    +
                  </button>
                  <button
                    onClick={() => updatePoison(player.id, -1)}
                    className="w-7 h-7 bg-green-500/20 border border-green-500/30 hover:bg-green-500/30 text-green-300 rounded text-xs font-bold transition-all"
                  >
                    -
                  </button>
                </div>
              )}
            </div>
          </div>
        </div>
      ))}
    </div>

    {/* Chat flotante */}
    {showChat && (
      <div className="fixed bottom-20 left-0 right-0 mx-4 md:mx-auto md:max-w-2xl z-30">
        <div className="backdrop-blur-xl bg-slate-900/95 border border-violet-500/30 rounded-2xl shadow-2xl overflow-hidden">
          <div className="px-4 py-3 bg-violet-500/10 border-b border-violet-500/20 flex items-center justify-between">
            <h3 className="text-white font-semibold">Chat</h3>
            <button 
              onClick={() => setShowChat(false)}
              className="text-violet-400 hover:text-violet-300 transition-colors"
            >
              <X className="w-5 h-5" />
            </button>
          </div>
          
          <div className="h-64 overflow-y-auto p-4 space-y-3">
            {currentGame.messages.length === 0 ? (
              <p className="text-violet-400/50 text-sm text-center py-8">No hay mensajes aún</p>
            ) : (
              currentGame.messages.map((msg) => (
                <div 
                  key={msg.id}
                  className={`flex flex-col gap-1 ${msg.player === playerName ? 'items-end' : 'items-start'}`}
                >
                  <span className="text-xs text-violet-400">{msg.player}</span>
                  <div 
                    className={`max-w-[80%] px-4 py-2 rounded-xl ${
                      msg.player === playerName 
                        ? 'bg-violet-600 text-white' 
                        : 'bg-slate-800 text-violet-200'
                    }`}
                  >
                    <p className="text-sm">{msg.text}</p>
                  </div>
                </div>
              ))
            )}
            <div ref={chatEndRef} />
          </div>
          
          <div className="p-4 bg-slate-800/50 border-t border-violet-500/20">
            <div className="flex gap-2">
              <input
                type="text"
                value={chatMessage}
                onChange={(e) => setChatMessage(e.target.value)}
                onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
                placeholder="Escribe un mensaje..."
                className="flex-1 bg-slate-900/50 border border-violet-500/30 rounded-xl px-4 py-2 text-white placeholder-violet-400/40 focus:outline-none focus:border-violet-400 transition-all text-sm"
              />
              <button
                onClick={sendMessage}
                className="bg-violet-600 hover:bg-violet-500 text-white rounded-xl px-4 py-2 transition-all"
              >
                <Send className="w-5 h-5" />
              </button>
            </div>
          </div>
        </div>
      </div>
    )}

    {/* Botón flotante de chat */}
    {!showChat && (
      <button
        onClick={() => setShowChat(true)}
        className="fixed bottom-24 right-4 bg-gradient-to-r from-violet-600 to-fuchsia-600 hover:from-violet-500 hover:to-fuchsia-500 text-white p-4 rounded-full shadow-2xl transition-all z-20"
      >
        <MessageCircle className="w-6 h-6" />
        {currentGame.messages.length > 0 && (
          <span className="absolute -top-1 -right-1 bg-red-500 text-white text-xs w-5 h-5 rounded-full flex items-center justify-center font-bold">
            {currentGame.messages.length}
          </span>
        )}
      </button>
    )}
  </div>
);
```

}

return null;
}
