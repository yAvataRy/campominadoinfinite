# Campo Minado (Minesweeper)

Este é um projeto de um jogo de Campo Minado, com a funcionalidade de um tabuleiro que se expande infinitamente, construído com Vue.js 3 e estilizado com Tailwind CSS.

## Sobre o Jogo

O jogo recria a experiência clássica do Campo Minado com uma reviravolta: o tabuleiro cresce dinamicamente à medida que você se aproxima das bordas, proporcionando uma experiência de jogo sem fim. O objetivo é revelar todas as células seguras sem detonar nenhuma mina.

## Tecnologias Utilizadas

- **Vue.js 3**: Framework progressivo para a criação da interface do usuário.
- **Tailwind CSS**: Framework CSS utilitário para estilização rápida e responsiva.
- **Vite**: Ferramenta de build rápida e moderna para desenvolvimento frontend.

## Funcionalidades

- **Tabuleiro Infinito**: O tabuleiro se expande automaticamente em direção à área clicada quando o jogador se aproxima das bordas.
- **Timer**: Cronômetro que acompanha o tempo de jogo para balancear o desafio do tabuleiro crescente.
- **Contagem de Bandeiras**: Um contador de bandeiras mostra quantas minas restam a serem sinalizadas.
- **Detecção de Vitórias e Derrotas**: O jogo informa quando você pisa em uma mina (Game Over) ou quando limpa todas as células seguras (Você Venceu!).
- **Botão de Reiniciar**: Permite iniciar um novo jogo a qualquer momento.
- **Números Coloridos**: Cada número tem uma cor específica para melhor visualização.
- **Animações**: Efeitos visuais para vitória e derrota.

## Como Executar o Projeto

Para rodar este projeto localmente, siga os passos abaixo:

### Pré-requisitos

Certifique-se de ter o Node.js e o npm (ou yarn) instalados em sua máquina.

### Instalação

1. Clone este repositório para o seu computador:
```bash
git clone [URL_DO_SEU_REPOSITORIO]
cd campo-minado-web
```

2. Instale as dependências do projeto:
```bash
npm install
# ou
yarn install
```

### Rodando o Servidor de Desenvolvimento

Execute o seguinte comando para iniciar o servidor local:
```bash
npm run dev
# ou
yarn dev
```

O jogo estará disponível em `http://localhost:3000` (ou na porta que for indicada).

### Build para Produção

Para criar uma versão otimizada para produção:
```bash
npm run build
# ou
yarn build
```

## Como Jogar

- **Clique Esquerdo**: Revela uma célula. Se for uma célula vazia, ela revelará automaticamente as células vizinhas.
- **Clique Direito**: Coloca ou remove uma bandeira em uma célula para marcar uma possível mina.
- **Botão "Novo Jogo"**: Reinicia o tabuleiro.
- **Expansão Automática**: O tabuleiro se expande quando você clica próximo às bordas.

## Estrutura do Projeto

```
/
├── public/
│   └── vite.svg               # Ícone do Vite
├── src/
│   ├── assets/
│   │   └── main.css           # Estilos principais com Tailwind
│   ├── components/
│   │   └── MinesweeperBoard.vue # Componente do tabuleiro
│   ├── App.vue                # Componente raiz do Vue
│   └── main.js                # Ponto de entrada da aplicação
├── index.html                 # Arquivo HTML principal
├── package.json               # Dependências e scripts
├── vite.config.js             # Configuração do Vite
├── tailwind.config.js         # Configuração do Tailwind CSS
├── postcss.config.js          # Configuração do PostCSS
└── README.md                  # Este arquivo
```

## Características Técnicas

- **Reatividade**: Utiliza o sistema de reatividade do Vue.js 3 para atualizações eficientes da interface.
- **Responsividade**: Interface adaptável para diferentes tamanhos de tela.
- **Performance**: Otimizado com Vite para desenvolvimento rápido e builds eficientes.
- **Acessibilidade**: Suporte a clique direito e prevenção de seleção de texto nas células.

## Aproveite o jogo!

