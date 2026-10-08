<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Estado e Ciclo de Vida no React

O estado e o ciclo de vida são conceitos fundamentais do React que permitem aos componentes armazenar dados internos e responder às mudanças ao longo do tempo. Esta seção explora como gerenciar o estado em componentes e como utilizar os métodos do ciclo de vida para executar código em momentos específicos da "vida" de um componente.

## O que é Estado (State)?

O **estado** é um objeto JavaScript que contém dados específicos de um componente que podem mudar ao longo do tempo. Quando o estado de um componente muda, o React re-renderiza o componente para refletir as mudanças na UI.

### Características do Estado:

- **Privado** - Pertence exclusivamente ao componente que o define
- **Controlado** - Só deve ser atualizado com métodos específicos
- **Reativo** - Mudanças no estado causam re-renderização
- **Local** - Afeta apenas o componente e possivelmente seus filhos

## Estado com Hooks (Abordagem Moderna)

O Hook `useState` é a forma recomendada de trabalhar com estado em componentes funcionais:

```jsx
import React, { useState } from 'react';

function Contador() {
  // Declara uma variável de estado chamada "contagem" com valor inicial 0
  const [contagem, setContagem] = useState(0);
  
  return (
    <div>
      <p>Você clicou {contagem} vezes</p>
      <button onClick={() => setContagem(contagem + 1)}>
        Clique aqui
      </button>
    </div>
  );
}
```

### Aspectos importantes do `useState`:

- A função `useState` retorna um array com dois elementos:
  1. O valor atual do estado (`contagem`)
  2. Uma função para atualizar o estado (`setContagem`)
- O argumento passado para `useState` é o valor inicial do estado
- Podemos usar `useState` múltiplas vezes em um único componente:

```jsx
function Perfil() {
  const [nome, setNome] = useState('');
  const [idade, setIdade] = useState(0);
  const [tema, setTema] = useState('claro');
  
  // ...
}
```

### Estado com Objetos

Quando o estado é um objeto, você precisa garantir que todos os campos sejam preservados ao atualizar apenas um:

```jsx
function FormularioUsuario() {
  const [usuario, setUsuario] = useState({
    nome: '',
    email: '',
    idade: 0
  });
  
  const atualizarNome = (evento) => {
    // Precisamos manter os outros campos ao atualizar apenas nome
    setUsuario({
      ...usuario, // Copia todos os campos existentes
      nome: evento.target.value // Atualiza apenas o nome
    });
  };
  
  // ...
}
```

## Estado em Componentes de Classe (Abordagem Legada)

Em componentes de classe, o estado é definido na propriedade `state` e atualizado com `setState()`:

```jsx
import React, { Component } from 'react';

class Contador extends Component {
  constructor(props) {
    super(props);
    this.state = {
      contagem: 0
    };
  }
  
  incrementar = () => {
    this.setState({ contagem: this.state.contagem + 1 });
  }
  
  render() {
    return (
      <div>
        <p>Você clicou {this.state.contagem} vezes</p>
        <button onClick={this.incrementar}>
          Clique aqui
        </button>
      </div>
    );
  }
}
```

### ⚠️ Cuidados com `setState` em Classes:

- As atualizações de estado podem ser assíncronas
- As atualizações de estado podem ser agrupadas para melhorar o desempenho

```jsx
// ❌ Não garante o valor correto se baseado no estado anterior
this.setState({ contagem: this.state.contagem + 1 });

// ✅ Garante o valor mais recente utilizando uma função
this.setState((estadoAnterior) => {
  return { contagem: estadoAnterior.contagem + 1 };
});
```

## Ciclo de Vida de Componentes

O ciclo de vida de um componente React consiste em diferentes fases, desde a sua criação (montagem) até a sua remoção (desmontagem).

### Usando o Hook `useEffect` (Componentes Funcionais)

O Hook `useEffect` permite executar efeitos colaterais em componentes funcionais, substituindo os métodos de ciclo de vida:

```jsx
import React, { useState, useEffect } from 'react';

function ExemploCicloDeVida() {
  const [recurso, setRecurso] = useState(null);
  
  // Executa após cada renderização
  useEffect(() => {
    console.log('Componente renderizado');
    
    // Função de limpeza (opcional)
    return () => {
      console.log('Limpeza antes da próxima renderização ou desmontagem');
    };
  });
  
  // Executa apenas na montagem (array de dependências vazio)
  useEffect(() => {
    console.log('Componente montado');
    fetch('https://api.exemplo.com/recurso')
      .then(resposta => resposta.json())
      .then(dados => setRecurso(dados));
    
    // Executa na desmontagem
    return () => {
      console.log('Componente será desmontado');
    };
  }, []);
  
  // Executa quando "id" muda
  useEffect(() => {
    console.log('ID mudou');
  }, [props.id]);
  
  // ...
}
```

### Métodos de Ciclo de Vida (Componentes de Classe)

Em componentes de classe, temos métodos específicos que são chamados em diferentes momentos:

#### Fase de Montagem

```jsx
class MeuComponente extends Component {
  constructor(props) {
    super(props);
    this.state = { /* ... */ };
    console.log('1. Constructor: Inicialização');
  }
  
  static getDerivedStateFromProps(props, state) {
    console.log('2. getDerivedStateFromProps: Props -> State');
    return null; // ou um objeto para atualizar o estado
  }
  
  componentDidMount() {
    console.log('4. componentDidMount: Componente montado no DOM');
    // Bom lugar para chamadas API, subscrições...
  }
  
  render() {
    console.log('3. render: Renderizando o componente');
    return <div>Meu Componente</div>;
  }
}
```

#### Fase de Atualização

```jsx
class MeuComponente extends Component {
  // ...
  
  static getDerivedStateFromProps(props, state) {
    // Chamado antes de render() na atualização
    return null;
  }
  
  shouldComponentUpdate(nextProps, nextState) {
    // Permite cancelar a renderização
    return true; // ou false para impedir atualização
  }
  
  getSnapshotBeforeUpdate(prevProps, prevState) {
    // Captura informações do DOM antes da atualização
    return null; // valor passado para componentDidUpdate
  }
  
  componentDidUpdate(prevProps, prevState, snapshot) {
    // Chamado após a atualização do componente
  }
  
  render() {
    // Renderiza o componente
  }
}
```

#### Fase de Desmontagem

```jsx
class MeuComponente extends Component {
  // ...
  
  componentWillUnmount() {
    // Limpeza antes da remoção do componente
    // Cancelar subscrições, timers, etc.
  }
}
```

#### ⚠️ Tratamento de Erros

```jsx
class MeuComponente extends Component {
  // ...
  
  static getDerivedStateFromError(error) {
    // Atualiza o estado para exibir UI de fallback
    return { temErro: true };
  }
  
  componentDidCatch(error, info) {
    // Registra informações do erro
    logErrorToService(error, info);
  }
}
```

## Fluxo de Dados no React

### ⬇️ Fluxo Unidirecional

No React, os dados fluem de cima para baixo (de componentes pais para filhos). Esta abordagem facilita o entendimento de como as informações se movem pela aplicação.

### Elevação de Estado (State Lifting)

Quando componentes irmãos precisam compartilhar estado, elevamos o estado para o componente pai comum:

```jsx
function Pai() {
  const [contagem, setContagem] = useState(0);
  
  return (
    <div>
      <Filho1 contagem={contagem} />
      <Filho2 onIncrement={() => setContagem(contagem + 1)} />
    </div>
  );
}

function Filho1({ contagem }) {
  return <div>Contagem: {contagem}</div>;
}

function Filho2({ onIncrement }) {
  return <button onClick={onIncrement}>Incrementar</button>;
}
```

## Exemplos Práticos

### Formulário Controlado com `useState`

```jsx
function Formulario() {
  const [valores, setValores] = useState({
    nome: '',
    email: '',
    mensagem: ''
  });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({
      ...valores,
      [name]: value
    });
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Dados enviados:', valores);
    // Enviar para API, etc.
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="nome">Nome:</label>
        <input
          type="text"
          id="nome"
          name="nome"
          value={valores.nome}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={valores.email}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="mensagem">Mensagem:</label>
        <textarea
          id="mensagem"
          name="mensagem"
          value={valores.mensagem}
          onChange={handleChange}
        />
      </div>
      
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Cronômetro com `useEffect`

```jsx
function Cronometro() {
  const [segundos, setSegundos] = useState(0);
  const [ativo, setAtivo] = useState(false);
  
  useEffect(() => {
    let intervalo = null;
    
    if (ativo) {
      intervalo = setInterval(() => {
        setSegundos(segundos => segundos + 1);
      }, 1000);
    }
    
    // Limpeza ao desmontar ou quando ativo mudar
    return () => {
      if (intervalo) clearInterval(intervalo);
    };
  }, [ativo]);
  
  const toggleCronometro = () => {
    setAtivo(!ativo);
  };
  
  const resetCronometro = () => {
    setSegundos(0);
    setAtivo(false);
  };
  
  return (
    <div>
      <div>Tempo: {segundos} segundos</div>
      <button onClick={toggleCronometro}>
        {ativo ? 'Pausar' : 'Iniciar'}
      </button>
      <button onClick={resetCronometro}>Reset</button>
    </div>
  );
}
```

### Busca de Dados com `useEffect`

```jsx
function BuscaDados() {
  const [dados, setDados] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  useEffect(() => {
    async function fetchData() {
      try {
        setCarregando(true);
        const resposta = await fetch('https://api.exemplo.com/dados');
        
        if (!resposta.ok) {
          throw new Error('Erro na resposta da API');
        }
        
        const dadosJSON = await resposta.json();
        setDados(dadosJSON);
        setErro(null);
      } catch (erro) {
        setErro('Falha ao carregar dados: ' + erro.message);
        setDados(null);
      } finally {
        setCarregando(false);
      }
    }
    
    fetchData();
  }, []); // Array vazio = executa apenas na montagem
  
  if (carregando) return <p>Carregando...</p>;
  if (erro) return <p>{erro}</p>;
  if (!dados) return <p>Nenhum dado encontrado</p>;
  
  return (
    <div>
      <h2>Dados Carregados</h2>
      <pre>{JSON.stringify(dados, null, 2)}</pre>
    </div>
  );
}
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>