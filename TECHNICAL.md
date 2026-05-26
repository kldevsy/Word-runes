# Word Runes - Guia Técnico

## 🏗️ Arquitetura

O projeto utiliza uma arquitetura modular baseada em:

### 1. **Observer Pattern (Event Bus)**
```javascript
// Subscrever a eventos
EventBus.subscribe(GAME_EVENTS.PLAYER_DIED, (data) => {
    console.log('Player morreu!');
});

// Emitir eventos
EventBus.emit(GAME_EVENTS.PLAYER_ATTACKED, { damage: 20 });
```

### 2. **Herança e Composição**
- `Entity` → classe base
- `Player`, `Enemy`, `NPC` → herdam de Entity

### 3. **Manager Pattern**
- `InputManager` - Gerencia entrada
- `AssetManager` - Gerencia carregamento de assets
- `UIManager` - Gerencia interface
- `EventManager` - Gerencia eventos

### 4. **System Pattern**
- `CollisionSystem` - Detecção de colisão
- `CombatSystem` - Cálculos de combate
- `DialogSystem` - Gerencia diálogos
- `SaveSystem` - Persistência de dados

## 🔄 Game Loop

```
┌─────────────────────────────┐
│   requestAnimationFrame     │
└──────────────┬──────────────┘
               │
         ┌─────▼─────┐
         │   Update  │
         │  (deltaT) │
         └─────┬─────┘
               │
         ┌─────▼─────┐
         │   Render  │
         │  (draw)   │
         └──────┬────┘
               │
         ┌─────▼──────────┐
         │  Loop Continua │
         └────────────────┘
```

## 🎮 Fluxo de Combate

```javascript
// Detecção de ataque
handleCanvasRightClick(e) {
    // 1. Encontra inimigos próximos
    // 2. Executa CombatSystem.attack()
    // 3. Calcula dano com modificadores
    // 4. Aplica dano
    // 5. Emite eventos
}

// Cálculo de dano
CombatSystem.calculateDamage(attacker, defender) {
    // damage = base + (str - def) + crítico
    // retorna dano final
}
```

## 📦 Sistema de Eventos

### Principais Eventos

| Evento | Dados | Descrição |
|--------|-------|-----------|
| `PLAYER_MOVED` | `{ player, x, y }` | Jogador se moveu |
| `PLAYER_ATTACKED` | `{ player, damage }` | Jogador atacou |
| `PLAYER_TOOK_DAMAGE` | `{ entity, damage }` | Entidade recebeu dano |
| `PLAYER_DIED` | `{ entity }` | Entidade morreu |
| `ENEMY_SPAWNED` | `{ enemy }` | Inimigo foi spawnado |
| `ITEM_PICKED_UP` | `{ player, item }` | Item foi coletado |
| `DIALOG_STARTED` | `{ npc, player }` | Diálogo iniciado |

## 🧮 Atributos e Modificadores

### Atributos Base
- **Força** (STR): Aumenta dano
- **Destreza** (DEX): Aumenta acerto e crítico
- **Constituição** (CON): Aumenta vida
- **Inteligência** (INT): Aumenta mana
- **Sabedoria** (WIS): Aumenta defesa mágica
- **Carisma** (CHA): Afeta negociações

### Fórmulas

#### Dano
```
Dano = BaseDamage + (STR - 10) / 2 - (CON - 10) / 2
```

#### Chance de Acerto
```
HitChance = 70% + (DEX - 10) * 1%
```

#### Chance de Crítico
```
CritChance = 5% + (DEX - 10) * 2%
CritDamage = 150% do dano normal
```

## 💾 Sistema de Save

### Estrutura de Save

```json
{
  "version": 1,
  "timestamp": "2024-01-15T10:30:00Z",
  "player": {
    "id": "player_1",
    "x": 100,
    "y": 100,
    "health": 85,
    "level": 5,
    "experience": 250,
    "inventory": [...]
  },
  "world": {
    "width": 100,
    "height": 100
  },
  "enemies": [...]
}
```

### Carregamento de Save

```javascript
// Carregar
const saveData = SaveSystem.load();

// Verificar se existe
if (SaveSystem.hasSave()) {
    // Restaurar jogo
}

// Deletar
SaveSystem.deleteSave();
```

## 🎨 Renderização

### Ordem de Renderização

1. **Fundo** (Tilemap)
2. **Entidades** (Player, Enemies, NPCs)
3. **Efeitos** (Partículas, blood splats)
4. **UI** (HUD, notificações)
5. **Debug Info** (FPS, coordenadas)

### Otimizações de Renderização

- **Culling**: Apenas renderiza o que está na câmera
- **Batch Rendering**: Agrupa desenhos similares
- **Canvas Transform**: Usa transformações da API

```javascript
// Renderização otimizada
for (const entity of this.visibleEntities) {
    ctx.save();
    ctx.translate(screenX, screenY);
    entity.draw(ctx);
    ctx.restore();
}
```

## 🔧 Extensibilidade

### Adicionar Novo Tipo de Entidade

```javascript
class Boss extends Enemy {
    constructor(x, y) {
        super(x, y, 'boss');
        this.phase = 1;
        this.skills = [
            { name: 'roar', damage: 50 },
            { name: 'stomp', damage: 40 }
        ];
    }
    
    updateAI(deltaTime, player) {
        // AI customizada para boss
        if (this.health < this.maxHealth / 2) {
            this.phase = 2;
            // Comportamento mais agressivo
        }
    }
}
```

### Adicionar Novo Sistema

```javascript
class LootSystem {
    static generateLoot(enemy) {
        const rarity = this.determineRarity(enemy.level);
        const items = this.createItemsForRarity(rarity);
        return items;
    }
}
```

## 📊 Performance Metrics

### Métricas Monitoradas
- **FPS**: Frames por segundo
- **Entidades Ativas**: Número de entidades no mapa
- **Memória**: Uso de RAM
- **Delta Time**: Tempo entre frames

### Bottlenecks Comuns
1. **Muitas entidades**: Limite com spatial partitioning
2. **Renderização**: Culling reduz draw calls
3. **Colisões**: Quadtree otimiza checks

## 🧪 Testes

### Como Testar

```javascript
// Teste de colisão
const rect1 = { x: 0, y: 0, width: 32, height: 32 };
const rect2 = { x: 16, y: 16, width: 32, height: 32 };
console.assert(CollisionSystem.aabb(rect1, rect2) === true);

// Teste de dano
const player = new Player(0, 0);
const enemy = new Enemy(0, 0);
CombatSystem.attack(player, enemy);
console.assert(enemy.health < enemy.maxHealth);
```

## 📱 Responsive Design

### Breakpoints
- **Mobile**: < 768px
- **Tablet**: 768px - 1024px
- **Desktop**: > 1024px

## 🚀 Deployment

### Build para Produção

```bash
# Minificar CSS
# Minificar JavaScript
# Otimizar imagens

# Servir via HTTPS
```

---

**Desenvolvido com ❤️ para RPG lovers!**
