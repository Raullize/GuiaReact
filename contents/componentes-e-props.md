<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Componentes e Props no React

Os componentes são os blocos de construção fundamentais de qualquer aplicação React. Esta seção aborda o que são componentes, como criá-los e como passamos dados para eles usando props.

## O que são Componentes?

**Componentes** são peças isoladas e reutilizáveis de código que retornam elementos React para serem renderizados na tela. Eles permitem dividir a UI em partes independentes, reutilizáveis e gerenciáveis.

### Vantagens da Componentização:

- **Reutilização** - Crie um componente uma vez e use-o em vários lugares
- **Modularidade** - Quebre a UI em partes gerenciáveis
- **Separação de preocupações** - Cada componente tem sua função específica
- **Testabilidade** - Mais fácil testar partes isoladas da aplicação

## Tipos de Componentes

No React, existem duas principais formas de definir componentes:

### 1 Componentes de Função (Recomendado)

Mais simples e, com Hooks, agora tão poderosos quanto componentes de classe:

```jsx
function Saudacao(props) {
  return <h1>Olá, {props.nome}!</h1>;
}

// Com arrow function (equivalente)
const Saudacao = (props) => {
  return <h1>Olá, {props.nome}!</h1>;
};

// Versão curta para funções simples
const Saudacao = props => <h1>Olá, {props.nome}!</h1>;
```

### 2 Componentes de Classe

Método mais antigo, ainda suportado, mas menos recomendado para novos códigos:

```jsx
import React, { Component } from 'react';

class Saudacao extends Component {
  render() {
    return <h1>Olá, {this.props.nome}!</h1>;
  }
}
```

## Props: Passando Dados para Componentes

**Props** (abreviação de "properties" ou propriedades) são o mecanismo para passar dados de um componente pai para um componente filho.

### Passando Props:

```jsx
// Renderizando o componente com props
<Saudacao nome="Maria" />
<Saudacao nome="João" />
```

### Recebendo Props:

```jsx
// Em componente de função
function Saudacao(props) {
  return <h1>Olá, {props.nome}!</h1>;
}

// Usando desestruturação (mais limpo)
function Saudacao({ nome }) {
  return <h1>Olá, {nome}!</h1>;
}
```

### Tipos Comuns de Props:

```jsx
// Strings (use chaves apenas para expressões JS)
<Botao texto="Clique aqui" />

// Números (use chaves para valores não-string)
<Contador valor={5} />

// Booleanos
<Toggle ativo={true} />

// Arrays
<Lista itens={['maçã', 'banana', 'laranja']} />

// Objetos
<Usuario info={{ nome: 'João', idade: 25 }} />

// Funções
<Botao onClick={() => console.log('Clicado!')} />
```

### Props Especiais:

#### Children

A prop especial `children` representa o conteúdo entre as tags de abertura e fechamento de um componente:

```jsx
function Painel({ children }) {
  return <div className="painel">{children}</div>;
}

// Uso:
<Painel>
  <h2>Título</h2>
  <p>Algum conteúdo aqui...</p>
</Painel>
```

## Validação de Props

Use `prop-types` para validar as props recebidas por um componente:

```jsx
import PropTypes from 'prop-types';

function Saudacao({ nome }) {
  return <h1>Olá, {nome}!</h1>;
}

Saudacao.propTypes = {
  nome: PropTypes.string.isRequired
};
```

### Tipos comuns de PropTypes:

```jsx
MyComponent.propTypes = {
  // Tipos primitivos
  optionalString: PropTypes.string,
  optionalNumber: PropTypes.number,
  optionalBool: PropTypes.bool,
  
  // Outros tipos
  optionalArray: PropTypes.array,
  optionalObject: PropTypes.object,
  optionalFunction: PropTypes.func,
  
  // Requerido
  requiredString: PropTypes.string.isRequired,
  
  // Nodos React
  optionalNode: PropTypes.node,
  
  // Elemento React
  optionalElement: PropTypes.element,
  
  // Tipos personalizados
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number
  ]),
  
  // Arrays de tipos específicos
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),
  
  // Objetos com propriedades específicas
  optionalObjectWith: PropTypes.shape({
    name: PropTypes.string,
    age: PropTypes.number
  })
};
```

## Props Padrão

Defina valores padrão para props não fornecidas:

```jsx
function Saudacao({ nome = 'Visitante' }) {
  return <h1>Olá, {nome}!</h1>;
}

// Ou usando defaultProps
Saudacao.defaultProps = {
  nome: 'Visitante'
};
```

## Props Imutáveis

Uma regra fundamental do React: **nunca modifique props**. Componentes React devem ser "puros" em relação às suas props.

```jsx
// ❌ ERRADO
function MeuComponente(props) {
  props.valor = props.valor + 1; // Nunca faça isso!
  return <div>{props.valor}</div>;
}

// ✅ CORRETO
function MeuComponente(props) {
  const novoValor = props.valor + 1;
  return <div>{novoValor}</div>;
}
```

## Composição de Componentes

O React favorece a composição sobre a herança. Crie componentes maiores combinando componentes menores:

```jsx
function App() {
  return (
    <div>
      <Header />
      <main>
        <Sidebar />
        <Content>
          <Article />
          <Comments />
        </Content>
      </main>
      <Footer />
    </div>
  );
}
```

## Especialização de Componentes

Crie componentes genéricos e especialize-os conforme necessário:

```jsx
function Botao({ children, tamanho = 'medio', cor = 'primaria', ...props }) {
  return (
    <button className={`botao botao-${tamanho} botao-${cor}`} {...props}>
      {children}
    </button>
  );
}

// Uso:
<Botao>Clique</Botao>
<Botao tamanho="grande" cor="secundaria">Enviar</Botao>
<Botao onClick={handleClick} disabled={isLoading}>Salvar</Botao>
```

## Exemplos Práticos

### Card de Produto

```jsx
function ProdutoCard({ produto }) {
  return (
    <div className="produto-card">
      <img src={produto.imagem} alt={produto.nome} />
      <h3>{produto.nome}</h3>
      <p className="preco">R$ {produto.preco.toFixed(2)}</p>
      <button>Adicionar ao Carrinho</button>
    </div>
  );
}

// Uso:
const produto = {
  id: 1,
  nome: "Smartphone Galaxy",
  preco: 1299.99,
  imagem: "galaxy.jpg"
};

<ProdutoCard produto={produto} />
```

### Formulário de Login

```jsx
function CampoTexto({ label, id, ...props }) {
  return (
    <div className="campo-grupo">
      <label htmlFor={id}>{label}</label>
      <input id={id} {...props} />
    </div>
  );
}

function FormularioLogin({ onSubmit }) {
  return (
    <form onSubmit={onSubmit}>
      <CampoTexto 
        label="Email" 
        id="email" 
        type="email" 
        required 
      />
      <CampoTexto 
        label="Senha" 
        id="senha" 
        type="password" 
        required 
        minLength={6}
      />
      <button type="submit">Entrar</button>
    </form>
  );
}
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>