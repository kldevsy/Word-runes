# Word Runes - RPG 2D em Mundo Aberto

Um jogo RPG 2D robusto e profissional desenvolvido com **HTML5, CSS3 e JavaScript puro**.

## 🎮 Características Principais

### 🌍 Mundo Aberto
- Mapa 100x100 com geração procedural de terrenos
- Sistema de câmera suave que segue o jogador
- Diferentes tipos de terreno (grama, árvore, água, pedra)
- Minimap em tempo real

### 👤 Sistema de Jogador
- **Atributos**: Força, Destreza, Constituição, Inteligência, Sabedoria, Carisma
- **Leveling**: Sistema de experiência e aumento de nível
- **Inventário**: 20 slots para itens
- **Equipamento**: Armas, armaduras, anéis
- **Barras Dinâmicas**: HP, Mana, XP

### ⚔️ Sistema de Combate Avançado
- IA de 4 estados (Idle, Patrol, Chase, Attack)
- 4 tipos de inimigos: Goblin, Orc, Skeleton, Troll
- Cálculo de dano com atributos
- Sistema de crítico (Crítica causa 1.5x de dano)
- Loot procedural após derrotar inimigos

### 🎭 NPCs e Diálogos
- NPCs interativos
- Sistema de diálogos com múltiplas opções
- Estrutura escalável para missões (quests)

### 📊 Interface Completa
- HUD com status do jogador
- Modal de inventário
- Modal de status com atributos
- Modal de mapa
- Sistema de notificações
- Dialog box para NPCs

### 💾 Persistência
- Salvamento automático a cada 30 segundos
- Save/Load via localStorage
- Exportação e importação de saves

## 🎮 Controles

```
Setas ou WASD     → Mover o personagem
Click Direito      → Atacar inimigo
Click Esquerdo     → Interagir com NPC
I                  → Abrir/Fechar Inventário
M                  → Abrir/Fechar Mapa
C                  → Abrir/Fechar Status
ESC                → Menu de Pausa
Ctrl + S           → Salvar Jogo
```

## 🏗️ Arquitetura

### Padrões de Design Utilizados

#### Observer Pattern (Event Bus)
Sistema pub/sub centralizado para comunicação entre sistemas:
```javascript
EventBus.subscribe(GAME_EVENTS.PLAYER_DIED, (data) => {
    console.log('Player morreu!');
});

EventBus.emit(GAME_EVENTS.PLAYER_ATTACKED, { damage: 20 });
```

#### Manager Pattern
- **InputManager**: Gerencia entrada do teclado/mouse
- **AssetManager**: Carregamento e gerenciamento de assets
- **UIManager**: Controle de interface
- **SaveManager**: Persistência de dados

#### System Pattern
- **CollisionSystem**: Detecção de colisão AABB
- **CombatSystem**: Cálculos de dano e combate
- **DialogSystem**: Gerenciamento de diálogos

#### Herança e Composição
```
Entity (classe base)
├── Player
├── Enemy
└── NPC
```

## 📁 Estrutura de Pastas

```
word-runes/
├── index.html
├── css/
│   └── style.css
├── js/
│   ├── core/
│   │   ├── eventBus.js
│   │   ├── constants.js
│   │   └── utils.js
│   ├── managers/
│   │   ├── assetManager.js
│   │   ├── inputManager.js
│   │   ├── uiManager.js
│   │   └── saveManager.js
│   ├── systems/
│   │   ├── collisionSystem.js
│   │   ├── combatSystem.js
│   │   └── dialogSystem.js
│   ├── entities/
│   │   ├── entity.js
│   │   ├── player.js
│   │   ├── enemy.js
│   │   └── npc.js
│   ├── world/
│   │   └── world.js
│   └── game.js
└── README.md
```

## 🚀 Como Executar

1. Clonar o repositório:
```bash
git clone https://github.com/kldevsy/word-runes.git
cd word-runes
```

2. Abrir `index.html` em um navegador moderno (Chrome, Firefox, Edge)

3. Pronto! O jogo começará automaticamente

## 🎮 Gameplay

### Começar
- Você inicia no centro do mapa
- Use WASD ou Setas para se mover
- Seu nível, HP e XP são mostrados no HUD

### Combate
- Inimigos aparecem aleatoriamente no mapa
- Click direito próximo a um inimigo para atacar
- Derrotar inimigos dá XP e ouro
- Alguns inimigos soltam loot

### Leveling
- Ganhe XP derrotando inimigos
- Quando alcança a quantidade de XP necessária, sobe de nível
- Ao subir de nível: aumenta atributos, HP máximo e restaura vida

### NPCs
- Encontre NPCs no mapa (com chapéus!)
- Click esquerdo próximo para conversar
- Escolha opções de diálogo

## 🔧 Extensibilidade

### Adicionar Novo Inimigo
```javascript
// Em constants.js, adicionar a ENEMY_TYPES:
CYCLOPS: {
    name: 'Cyclops',
    hp: 80,
    damage: 10,
    xp: 100,
    loot_chance: 0.7,
    color: '#6B4C3A',
    speed: 110
}
```

### Adicionar Novo Item
```javascript
// Em constants.js, adicionar a ITEM_TYPES:
POTION_STRENGTH: 'potion_strength'
```

### Adicionar Nova Habilidade
```javascript
class Skill {
    constructor(name, cooldown, manaCost) {
        this.name = name;
        this.cooldown = cooldown;
        this.manaCost = manaCost;
    }
    
    execute(player, target) {
        // Implementar lógica
    }
}
```

## 📊 Fórmulas de Gameplay

### Dano Causado
```
Dano = BaseDamage + (STR - 10) / 2 - (CON_inimigo - 10) / 2
```

### Chance de Crítico
```
CritChance = 5% + (DEX - 10) * 2%
CritDamage = 150% do dano normal
```

### XP para Subir de Nível
```
XP_Necessário = 100 * 1.1^(nível-1)
```

## 🔧 Otimizações Implementadas

- **Culling**: Apenas renderiza tiles e entidades visíveis
- **Delta Time**: Movimento independente de FPS
- **Spatial Partitioning**: Colisões otimizadas
- **Canvas Rendering**: Renderização eficiente em 2D

## 🐛 Debugging

- **FPS Counter**: Mostrado no canto inferior esquerdo
- **Posição do Jogador**: Coordenadas X, Y exibidas
- **Contador de Entidades**: Mostra quantas entidades estão ativas
- **Console Logs**: Eventos importantes são registrados

## 📱 Responsividade

- Adaptado para diferentes tamanhos de tela
- Interface redimensionável
- Touch-friendly em dispositivos móveis

## 🎨 Visual

- Estilo pixel art customizável
- Cores baseadas em tema profissional
- Animações suaves
- Efeitos visuais feedback

## 🔐 Segurança

- Dados salvos em localStorage (cliente)
- Validação de versão de save
- Proteção contra corrupção de dados

## 📈 Próximas Melhorias

- [ ] Sistema de som e música
- [ ] Mais tipos de inimigos
- [ ] Sistema de skills/magias
- [ ] Dungeons procedurais
- [ ] Multiplayer local
- [ ] Temas escuros/claros
- [ ] Animações de personagem

## 🤝 Contribuindo

Contribuições são bem-vindas! Feel free para:
- Reportar bugs
- Sugerir novas features
- Melhorar performance
- Adicionar conteúdo

## 📄 Licença

MIT License - veja LICENSE.md para detalhes

## 👨‍💻 Desenvolvedor

**kldevsy** - [GitHub](https://github.com/kldevsy)

---

**Desenvolvido com ❤️ para amantes de RPG!**

Versão: 1.0.0
Última atualização: 2026
