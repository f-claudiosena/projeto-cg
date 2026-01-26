# 🎮 Balãozin - Trabalho de Computação Gráfica

Um jogo arcade 2D interativo desenvolvido com técnicas clássicas de computação gráfica, incluindo renderização por pixel com anti-aliasing, algoritmos de desenho e transformações 3D.

## 📋 Descrição do Projeto

**Balãozin** é um jogo educacional de tiro ao alvo onde o jogador controla um balão que deve desviar de obstáculos (ouriços) e coletar estrelas para ganhar pontos. O projeto demonstra implementações práticas de conceitos fundamentais de computação gráfica.

### 🎯 Objetivo do Jogo
- Sobreviva o máximo de tempo desviando dos ouriços
- Colete estrelas para ganhar 100 pontos extras
- Evite a mina (aparece após 5.000 pontos)
- Acompanhe seu progresso no viewport/radar em tempo real

## 🎨 Técnicas de Computação Gráfica Implementadas

### 1. **Renderização por Pixel (SetPixel)**
- Manipulação direta do buffer de pixels
- Cada pixel é controlado através da função `setPixel()`
- Permite total controle sobre a renderização

### 2. **Anti-Aliasing (AA)**
- **Função drawLineAA()**: Desenha linhas com anti-aliasing usando algoritmo de Bresenham modificado
- **Função fillCircleAA()**: Preenche círculos com suavização de bordas
- **Função fillBalloonAA()**: Renderiza o balão com anti-aliasing para elipses
- **Função blendPixel()**: Interpolação alpha para suavização de bordas

### 3. **Algoritmo de Bresenham**
- Utilizado em `drawLineAA()` para desenhar linhas com precisão
- Implementação clássica com modificações para anti-aliasing
- Cálculo de gradiente para interpolação suave

### 4. **Preenchimento por Scanline**
- `fillCircleAA()` varre linhas (scanlines) horizontalmente
- `fillBalloonAA()` usa scanlines para preencher a elipse do balão
- Interpolação alpha para bordas suaves

### 5. **Transformações 3D**
- **Mina com rotação 3D no eixo Z**
  - Cálculo de profundidade (coordenada Z)
  - Separação de elementos entre frente e trás
  - Nuances de cor para efeito de profundidade
  - Órbita lenta dos nódulos ao redor do centro

### 6. **Viewport / Radar em Tempo Real**
- Mini-mapa que mostra a posição de todos os objetos
- Renderização dupla com viewports separados
- Escala 0.4x do mapa original

## 📁 Arquivos do Projeto

### `trabalho_cg.html`
Arquivo principal do jogo com implementação completa de:
- Renderização gráfica por pixel
- Lógica do jogo (colisões, pontuação)
- Sistema de menus
- Viewport/Radar
- Viewport/Radar em tempo real

## 🕹️ Controles

| Tecla | Ação |
|-------|------|
| `⬆️ / ⬇️` | Mover o balão para cima/baixo |
| `P` | Pausar o jogo |
| `R` | Reiniciar a partida |
| `ESC` | Abrir menu principal |

## 🎓 Estrutura do Código

### Seções Principais

#### 1. **Configurações Iniciais**
```javascript
const W = 500, H = 400;           // Dimensões do jogo
const MAP_W = 200, MAP_H = 160;   // Dimensões do mapa
const BALLOON_SPEED = 7.0;         // Velocidade do balão
```

#### 2. **Funções de Renderização com SetPixel**

##### `setPixel(x, y, r, g, b, a)`
- Escreve um pixel diretamente no buffer
- Base para todas as operações gráficas
- Coordenadas devem estar dentro dos limites

##### `blendPixel(x, y, r, g, b, alphaFactor)`
- Blend alpha do pixel com o fundo
- Usado para anti-aliasing
- Interpolação linear entre cores

##### `drawLineAA(x0, y0, x1, y1, color)`
- Desenha linhas com anti-aliasing
- Implementa Bresenham modificado
- Suaviza bordas através de blendPixel

##### `fillCircleAA(cx, cy, r, color)`
- Preenche círculo com anti-aliasing
- Usa scanline + cálculo de distância
- Anti-aliasing nas bordas

##### `fillBalloonAA(cx, cy, rx, ry, color)`
- Preenche elipse com anti-aliasing
- Scanline com equação de elipse
- Anti-aliasing nas bordas da elipse

#### 3. **Objetos do Jogo**

##### Balão
- Centro em x=100 (fixo horizontalmente)
- Posição Y controlada pelo jogador
- Raio: 18 pixels
- Renderizado com `fillBalloonAA()`

##### Ouriços
- 5 objetos que se movem de direita para esquerda
- Velocidade aumenta com a pontuação
- Rotação no eixo Z com `s.angle`
- 8 espinhos ao redor do corpo

##### Mina (Rotação 3D)
```javascript
// Rotação 3D com efeito de profundidade
- 8 nódulos que orbitam ao redor
- Cálculo de Z (profundidade) usando seno
- Nódulos de trás aparecem mais escuros
- Nódulos da frente aparecem mais claros
- Órbita lenta: mine.angle * 0.1
```

##### Estrela
- Aparece aleatoriamente no mapa
- Oferece +100 pontos
- Pulsa de tamanho (animação senoidal)
- Renderizada com 5 pontas

#### 4. **Sistema de Mapa (Viewport/Radar)**

##### `mapSet(x, y, r, g, b)`
- Escreve pixel no buffer do mapa
- Escala 0.4x do jogo principal

##### `renderMap()`
- Renderiza todos os objetos no mini-mapa
- Mostra em tempo real as posições
- Escala: 0.4x

#### 5. **Lógica do Jogo**

##### `update()`
- Atualiza posições de todos os objetos
- Detecta colisões com `Math.hypot()`
- Controla dificuldade progressiva
- Gerencia aparição da mina (após 5.000 pontos)

##### `gameOver()`
- Encerra o jogo ao colidir
- Salva high score em localStorage
- Mostra menu de fim de jogo

#### 6. **Loop de Renderização**

##### `render()`
- **renderGame()**: Desenha jogo principal
- **renderMap()**: Desenha viewport
- Converte pixels para ImageData
- Mostra no canvas 2D

### Estados do Jogo
```javascript
GAME_STATE = {
  INTRO: -1,      // Tela de introdução
  MENU: 0,        // Menu principal
  PLAYING: 1,     // Jogo em execução
  PAUSED: 2,      // Jogo pausado
  GAME_OVER: 3    // Fim de jogo
}
```

## 📊 Renderização Gráfica

### Pipeline de Renderização

```
┌─────────────────────────────────────────┐
│ Buffer de Pixels (500x400x4 bytes)      │
│ Array Uint8Array 'pixels'               │
└────────────┬────────────────────────────┘
             │
             ├─► setPixel() ────────┐
             │   (escrita direta)   │
             │                      │
             ├─► blendPixel() ──────┤
             │   (com alpha)        │
             │                      │
             ├─► drawLineAA() ──────┤
             │   (Bresenham+AA)     │
             │                      │
             ├─► fillCircleAA() ────┤
             │   (scanline+AA)      │
             │                      │
             └─► fillBalloonAA() ───┤
                 (scanline elipse+AA)
                                    │
                                    ▼
                        ctx.putImageData()
                                    │
                                    ▼
                            Canvas 2D Display
```

## 🎯 Detalhes Técnicos

### Coordenadas 3D da Mina
```javascript
// Para cada nódulo:
angle = mine.angle + (2π * i) / 8
z = sin(angle) * (r + 8)

// Renderização:
if (z < 0) cor_escura();  // Trás
else       cor_clara();    // Frente
```

### Anti-Aliasing em Linhas
```javascript
// Bresenham modificado com AA
for cada pixel na linha:
  calcular_fração_de_pixel()
  blend_com_fundo(fração)
```

### Preenchimento Scanline
```javascript
for cada linha y:
  for cada coluna x:
    distância = sqrt((x-cx)² + (y-cy)²)
    if distância < raio:
      setPixel() ou blendPixel()
```

## 📈 Progressão de Dificuldade

| Pontos | Evento |
|--------|--------|
| 0 | Começa com 5 ouriços |
| 2.000 | Velocidade aumenta para 3.0 |
| 5.000 | Mina começa a aparecer |
| 12.000 | Velocidade máxima 4.5 |

## 💾 Persistência

- **High Score**: Salvo em `localStorage.getItem('cgHighScore')`
- Persiste entre sessões do navegador

## 🌐 Compatibilidade

- **Navegadores**: Chrome, Firefox, Edge, Safari (versões recentes)
- **Requisitos**: JavaScript ES6+, Canvas 2D
- **Sem dependências**: Código puro vanilla JavaScript

## 🚀 Como Usar

1. Abra `trabalho_cg.html` em um navegador moderno
2. Clique em "JOGAR"
3. Use as setas para mover o balão
4. Pressione P para pausar
5. Pressione R para reiniciar

## 📝 Notas de Implementação

### Por que SetPixel?
- Controle total sobre cada pixel
- Implementação educacional
- Compreensão profunda de renderização
- Demonstra algoritmos clássicos

### Por que Anti-Aliasing?
- Suaviza bordas de formas
- Reduz artefatos de aliasing
- Melhora qualidade visual
- Implementa técnica profissional

### Por que Transformação 3D na Mina?
- Demonstra rotação 3D em 2D
- Criar ilusão de profundidade
- Técnica clássica de renderização isométrica
- Efeito visual atraente

## 🎓 Aprendizados de CG

Este projeto ensina:
1. ✅ Manipulação direta de pixels
2. ✅ Anti-aliasing e Bresenham
3. ✅ Preenchimento por scanline
4. ✅ Transformações 2D/3D
5. ✅ Blend alpha e composição
6. ✅ Otimização de renderização
7. ✅ Sistemas de coordenadas
8. ✅ Detecção de colisões

## 📄 Licença

Este projeto é fornecido como material educacional.

## 👨‍💻 Autor
Francisco Cláudio da Silva Sena Filho

Desenvolvido como trabalho prático de Computação Gráfica.

---

**Divirta-se jogando e aprendendo CG!** 🎮✨
