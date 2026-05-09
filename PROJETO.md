# VersoVivo

> Editor visual para criação de vídeos poéticos — combine imagens com poemas e exporte como vídeo animado.

---

## Índice

1. [O que é este sistema](#1-o-que-é-este-sistema)
2. [Como acessar e usar](#2-como-acessar-e-usar)
3. [Estrutura de arquivos](#3-estrutura-de-arquivos)
4. [Arquitetura e funcionamento](#4-arquitetura-e-funcionamento)
5. [Funcionalidades detalhadas](#5-funcionalidades-detalhadas)
6. [Tecnologias utilizadas](#6-tecnologias-utilizadas)
7. [Guia educativo — entenda cada parte](#7-guia-educativo--entenda-cada-parte)
8. [Como dar continuidade ao projeto](#8-como-dar-continuidade-ao-projeto)

---

## 1. O que é este sistema

O **VersoVivo** é um editor visual de vídeos poéticos que roda diretamente no navegador. O usuário carrega imagens, escreve ou cola um poema, ajusta a fonte e o posicionamento do texto, e exporta um vídeo onde as imagens passam como um slideshow com o poema sobreposto.

**Em linguagem simples:** você escolhe suas fotos, digita seu poema, e o sistema cria um vídeo artístico com o texto animado sobre as imagens — pronto para compartilhar nas redes sociais ou guardar como memória.

### O que ele faz

- Importar múltiplas imagens
- Escrever ou colar texto poético diretamente sobre as imagens
- Ajustar fonte, tamanho, alinhamento e cor do texto
- Posicionar o texto livremente sobre o canvas
- Exportar como vídeo (arquivo de vídeo animado)

### Estado atual do projeto

O VersoVivo é o projeto em estágio mais inicial da pasta. A interface de boot (tela de entrada) e o editor principal estão implementados. O sistema tem base sólida para evoluir com novas funcionalidades.

---

## 2. Como acessar e usar

Não precisa de instalação. Abra diretamente no navegador:

```bash
# Opção 1: Abrir diretamente
# Duplo clique em index.html

# Opção 2: Via servidor local (recomendado)
npx serve .
# Acesse: http://localhost:3000
```

### Fluxo de uso

```
1. Tela de boot → animação de entrada com o nome "VersoVivo"
2. Tela home → botão para criar novo projeto
3. Editor → carregue imagens + escreva o poema
4. Ajuste visual → fonte, tamanho, posição, alinhamento
5. Exportar → baixe o vídeo gerado
```

---

## 3. Estrutura de arquivos

```
versovivo/
│
├── index.html     # Sistema completo em um único arquivo HTML
│                  # (inclui HTML + CSS + JavaScript embutidos)
│
└── PROJETO.md     # Este arquivo
```

> O VersoVivo é um sistema de arquivo único — toda a estrutura (HTML, CSS e JavaScript) está dentro de `index.html`. Essa abordagem foi escolhida para máxima portabilidade: qualquer pessoa pode compartilhar o arquivo e ele funciona em qualquer computador com um navegador.

---

## 4. Arquitetura e funcionamento

### Estrutura interna do index.html

```
index.html
│
├── <style>                      # Todo o CSS embutido
│   ├── Variáveis de cor (--bg, --primary, --second, --grad)
│   ├── Tela de boot (animação de entrada)
│   ├── Tela home (criar novo projeto)
│   ├── Editor (topbar + canvas-area + painel lateral)
│   └── Componentes (botões, painéis, inputs)
│
├── <div id="boot">              # Tela de carregamento animada
├── <div id="home">              # Tela inicial (criar projeto)
├── <div id="editor">            # Editor principal
│   ├── .topbar                  # Barra superior (nome, contador de imagens, botão download)
│   ├── .canvas-area             # Área do canvas (imagem + texto sobreposto)
│   │   ├── <canvas id="cv">     # Canvas HTML5 (renderiza as imagens)
│   │   └── <div id="cv-overlay"> # Overlay HTML (caixa de texto interativa)
│   └── Painel lateral           # Configurações (fonte, cor, alinhamento)
│
└── <script>                     # Todo o JavaScript embutido
    ├── Estado do editor (objeto S e TBOX)
    ├── Gerenciamento de imagens
    ├── Renderização do canvas (função draw())
    ├── Renderização do texto (função drawText())
    ├── Controles de fonte e alinhamento
    └── Exportação de vídeo
```

### Como o canvas funciona

O editor usa dois elementos sobrepostos:

```
┌─────────────────────────────────────┐
│  <canvas id="cv">                   │  ← Renderiza as imagens (HTML Canvas API)
│                                     │
│  <div id="cv-overlay">              │  ← Caixa de texto arrastável (HTML div)
│    <div id="text-box">              │
│      [Seu poema aqui]               │
│    </div>                           │
│  </div>                             │
└─────────────────────────────────────┘
```

O canvas cuida das imagens, o overlay HTML cuida do texto — essa separação permite que o texto seja editável e arrastável de forma nativa.

### Estado interno do editor

```javascript
// Estado principal
const S = {
  images: [],        // Array de imagens carregadas
  currentImg: 0,     // Índice da imagem atual
  text: '',          // Texto do poema
  font: 'serif',     // Família de fonte
  fontSize: 48,      // Tamanho da fonte
  color: '#FFFFFF',  // Cor do texto
  align: 'center'    // Alinhamento (left, center, right)
};

// Estado da caixa de texto (posição e tamanho)
const TBOX = {
  x: 0.5,           // Posição horizontal (0 a 1, fração do canvas)
  y: 0.8,           // Posição vertical (0 a 1)
  show: false        // Se a caixa está visível
};
```

### Loop de renderização

```javascript
function tick(ts) {
  draw();                        // Desenha o frame atual
  requestAnimationFrame(tick);   // Agenda o próximo frame
}
requestAnimationFrame(tick);     // Inicia o loop
```

O canvas é redesenhado continuamente usando `requestAnimationFrame` — a mesma técnica usada em jogos para animações suaves.

---

## 5. Funcionalidades detalhadas

### Boot (tela de entrada)

- Animação letra por letra do nome "VersoVivo"
- Subtítulo com efeito fade in
- Barra de progresso animada
- Transição suave para a tela home

### Tela Home

- Botão "Novo Projeto" com efeito hover elaborado
- Dica textual sobre o propósito do editor

### Editor — Topbar

- Nome da aplicação (VersoVivo)
- Contador de imagens carregadas
- Botão de download do vídeo gerado (desativado até ter conteúdo)

### Editor — Canvas

- Renderização das imagens em sequência
- Texto sobreposto com renderização via Canvas 2D
- Alinhamento automático do texto ao centro/esquerda/direita
- Quebra de linha automática para textos longos

### Editor — Caixa de texto interativa

- Edição direta do poema sobre o canvas
- Caixa arrastável para reposicionar o texto
- Sincronização em tempo real com o canvas renderizado

### Editor — Painel de configurações

- Seleção de fonte (inclui campo de busca de fontes)
- Preview visual da fonte selecionada
- Ajuste de tamanho
- Seleção de cor
- Alinhamento do texto (esquerda, centro, direita)
- Área de digitação do poema (textarea)

### Exportação

- Geração de vídeo diretamente no navegador
- Download automático do arquivo

---

## 6. Tecnologias utilizadas

| Tecnologia | Função | Detalhe |
|------------|--------|---------|
| HTML5 Canvas | Renderização das imagens e texto | Canvas 2D API nativa do navegador |
| CSS3 | Interface visual com tema roxo/rosa escuro | Variáveis CSS, gradientes, animações |
| JavaScript Vanilla | Toda a lógica do editor | requestAnimationFrame, File API, MediaRecorder |
| Google Fonts | Tipografia (Playfair Display) | Fonte elegante para o branding |
| File API | Importação de imagens locais | Leitura de arquivos do computador do usuário |
| MediaRecorder API | Exportação do vídeo | Grava o canvas como stream de vídeo |

### Paleta de cores

```css
--bg:      #09090F;   /* Fundo escuro quase preto */
--surface: #10101A;   /* Superfície ligeiramente mais clara */
--card:    #17172A;   /* Cards e painéis */
--border:  #252540;   /* Bordas */
--muted:   #5B5B80;   /* Texto secundário */
--text:    #EDEDF8;   /* Texto principal */
--primary: #8B5CF6;   /* Roxo principal */
--second:  #EC4899;   /* Rosa secundário */
--grad:    linear-gradient(135deg, #8B5CF6, #EC4899); /* Gradiente marca */
```

---

## 7. Guia educativo — entenda cada parte

### O que é o HTML Canvas?

O Canvas é um elemento HTML que funciona como uma tela em branco onde você pode desenhar com JavaScript. Diferente do HTML normal (onde você coloca elementos como botões e textos), no Canvas você desenha pixel por pixel — ou usando formas como retângulos, círculos, imagens e textos.

```javascript
const canvas = document.getElementById('cv');
const ctx = canvas.getContext('2d'); // pega o "pincel"

// Desenhar uma imagem no canvas
ctx.drawImage(minhaImagem, 0, 0, canvas.width, canvas.height);

// Escrever texto
ctx.fillStyle = 'white';
ctx.font = '48px serif';
ctx.fillText('Meu poema', 100, 200);
```

### O que é requestAnimationFrame?

É uma função do navegador que permite criar animações suaves. Em vez de usar `setInterval` (que roda em intervalos fixos), o `requestAnimationFrame` sincroniza com a taxa de atualização da tela (geralmente 60 vezes por segundo), resultando em animações mais fluidas e eficientes.

```javascript
function animar() {
  // Limpa e redesenha o canvas
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  desenharFrame();
  
  // Agenda o próximo redesenho
  requestAnimationFrame(animar);
}

requestAnimationFrame(animar); // começa o loop
```

### O que é a File API?

A File API permite que páginas web acessem arquivos do computador do usuário (com permissão — o usuário precisa selecionar o arquivo). No VersoVivo, ela é usada para carregar as imagens:

```javascript
// O input de arquivo dispara quando o usuário seleciona imagens
document.getElementById('img-input').addEventListener('change', function(e) {
  const arquivos = e.target.files; // lista de arquivos selecionados
  
  arquivos.forEach(arquivo => {
    const reader = new FileReader();
    reader.onload = function(evt) {
      const img = new Image();
      img.src = evt.target.result; // URL da imagem em memória
      S.images.push(img); // adiciona ao estado
    };
    reader.readAsDataURL(arquivo); // lê o arquivo como URL
  });
});
```

### O que é a MediaRecorder API?

É a API do navegador que permite gravar streams de áudio e vídeo. No VersoVivo, ela é usada para "gravar" o canvas como se fosse um vídeo:

```javascript
// Captura o canvas como um stream de vídeo
const stream = canvas.captureStream(30); // 30 frames por segundo
const recorder = new MediaRecorder(stream);

// Coleta os dados gravados
const chunks = [];
recorder.ondataavailable = e => chunks.push(e.data);

// Quando parar, monta o arquivo de vídeo
recorder.onstop = () => {
  const blob = new Blob(chunks, { type: 'video/webm' });
  // faz download do blob como arquivo .webm
};

recorder.start(); // começa a gravação
```

### O que é Playfair Display?

Playfair Display é uma fonte tipográfica elegante do estilo "serifa" (com pequenos traços decorativos nas extremidades das letras), muito usada em contextos literários, editoriais e artísticos. É carregada do Google Fonts e usada no branding do VersoVivo para criar uma identidade visual sofisticada e poética.

---

## 8. Como dar continuidade ao projeto

### Para um novo desenvolvedor começar

1. Abra `index.html` em qualquer editor de código (VS Code, Notepad++, etc.)
2. Todo o código está em um único arquivo — HTML no topo, CSS no `<style>`, JavaScript no `<script>`
3. Abra no navegador e use o DevTools (F12) para inspecionar e debugar
4. O estado principal está nos objetos `S` e `TBOX` no JavaScript

### Onde estão as principais partes

| O que procurar | Onde encontrar no código |
|----------------|--------------------------|
| Configuração de cores | Variáveis CSS no início do `<style>` |
| Tela de boot | `#boot` no HTML + `.boot-*` no CSS |
| Loop de animação | Função `tick()` no JavaScript |
| Renderização da imagem | Função `draw()` |
| Renderização do texto | Função `drawText()` |
| Upload de imagens | `#img-input` e seu event listener |
| Exportação | Busque por `MediaRecorder` ou `download` |

### Próximos passos naturais para evolução

- **Transições entre imagens:** efeitos de fade, slide ou zoom ao trocar de imagem
- **Múltiplas fontes:** integrar mais famílias tipográficas do Google Fonts
- **Música de fundo:** permitir adicionar áudio ao vídeo exportado
- **Templates:** oferecer layouts pré-definidos (poema centralizado, texto lateral, etc.)
- **Ajuste de timing:** controlar por quanto tempo cada imagem aparece no vídeo
- **Compartilhamento direto:** exportar em formatos otimizados para Instagram, TikTok, etc.
- **Salvar projeto:** persistir o trabalho no localStorage para retomar depois
- **Efeitos de texto:** sombra, contorno, gradiente no texto

---

*Documento gerado em maio de 2026 — VersoVivo*
