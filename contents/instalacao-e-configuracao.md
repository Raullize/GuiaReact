<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Instalação e Configuração do React

Esta seção apresenta diferentes métodos para começar a desenvolver com React, desde ferramentas online até configurações completas de projetos locais. Escolha a opção que melhor se adapta às suas necessidades!

## Opções Online (Sem Instalação)

Quer experimentar React sem instalar nada? Experimente estas ferramentas online:

### CodeSandbox
- [CodeSandbox React](https://codesandbox.io/s/new)
- IDE completo no navegador
- Templates prontos para React
- Colaboração em tempo real

### Stackblitz
- [Stackblitz React](https://stackblitz.com/fork/react)
- Rápido e leve
- Integração com GitHub

## Create React App (CRA)

O método mais popular e recomendado para iniciantes. Configura um ambiente de desenvolvimento React com um único comando:

```bash
# Usando npx (recomendado)
npx create-react-app meu-projeto

# Ou usando npm
npm init react-app meu-projeto

# Ou usando Yarn
yarn create react-app meu-projeto
```

### O que o CRA inclui:
- Configuração otimizada do React
- Webpack e Babel configurados
- Hot reloading (atualização automática)
- Jest para testes
- ESLint para linting
- Progressive Web App por padrão

### Estrutura de um projeto CRA:
```
meu-projeto/
  ├── node_modules/
  ├── public/
  │   ├── favicon.ico
  │   ├── index.html
  │   └── manifest.json
  ├── src/
  │   ├── App.css
  │   ├── App.js
  │   ├── App.test.js
  │   ├── index.css
  │   ├── index.js
  │   ├── logo.svg
  │   └── serviceWorker.js
  ├── .gitignore
  ├── package.json
  └── README.md
```

### Comandos básicos:
```bash
# Iniciar o servidor de desenvolvimento
npm start

# Construir para produção
npm run build

# Executar testes
npm test

# Ejetar (expor todas as configurações)
npm run eject # Cuidado: isso é irreversível!
```

## Vite

Uma alternativa moderna e mais rápida que o Create React App:

```bash
# Criando um projeto React com Vite
npm create vite@latest meu-projeto-vite -- --template react

# Ou usando Yarn
yarn create vite meu-projeto-vite --template react

# Instalando dependências
cd meu-projeto-vite
npm install # ou yarn
```

### Vantagens do Vite:
- Extremamente rápido (inicialização e HMR)
- Bundling otimizado com Rollup
- Sistema de plugins simples
- Configuração mais flexível que o CRA

## Next.js

Framework React completo com renderização do lado do servidor (SSR):

```bash
# Criando um projeto Next.js
npx create-next-app meu-app-next

# Ou com TypeScript
npx create-next-app@latest --typescript
```

### Recursos do Next.js:
- SSR (Server-Side Rendering)
- Roteamento baseado em sistema de arquivos
- SSG (Static Site Generation)
- ISR (Incremental Static Regeneration)
- Otimização de imagens
- API Routes

## Adicionando React a um Site Existente

Se você deseja adicionar React a um site existente sem criar um novo projeto:

### ▶ Usando CDN (para prototipagem rápida):
```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
```

### Usando npm em um projeto existente:
```bash
# Instalar as dependências
npm install react react-dom

# Para JSX, você precisará também do Babel
npm install @babel/core @babel/preset-react babel-loader --save-dev
```

## TypeScript com React

Para adicionar TypeScript ao seu projeto React:

### Com Create React App:
```bash
npx create-react-app meu-app-ts --template typescript
```

### Com Vite:
```bash
npm create vite@latest meu-app-vite -- --template react-ts
```

### Benefícios do TypeScript:
- Tipagem estática
- Menos bugs em runtime
- Melhor documentação de código
- Melhor autocomplete no IDE

## Requisitos do Sistema

Para desenvolver com React localmente, você precisará:

- Node.js versão 14.0.0 ou superior
- npm 5.6 ou superior (ou Yarn)
- Um editor de código (recomendamos VS Code)
- Navegador moderno para testar

---

⚠️ **Nota importante**: Se você encontrar problemas de permissão ao executar comandos npm, tente usar o `npx` ou adicione `--user` aos comandos de instalação global.

## Atualizando o React

Para projetos existentes, atualize o React com:

```bash
# Usando npm
npm update react react-dom

# Usando yarn
yarn upgrade react react-dom
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>