<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 💾 Gerenciamento de Estado no React 📊

O gerenciamento de estado é um aspecto crucial no desenvolvimento de aplicações React. À medida que suas aplicações crescem, o controle e fluxo de dados torna-se mais complexo e desafiador. Esta seção explora diferentes abordagens para gerenciar estado em aplicações React, desde soluções nativas até bibliotecas especializadas. 🚀

## 🤔 O que é Estado e Por Que Gerenciá-lo?

**Estado** representa os dados que podem mudar ao longo do tempo em sua aplicação. O gerenciamento eficiente do estado é fundamental porque:

- 🧩 **Complexidade crescente**: Aplicações maiores têm mais dados para controlar
- 🔄 **Compartilhamento de dados**: Componentes em diferentes níveis precisam acessar os mesmos dados
- 🔍 **Previsibilidade**: Mudanças de estado devem ser rastreáveis e previsíveis
- 🧪 **Testabilidade**: Código com estado bem gerenciado é mais fácil de testar
- ⚡ **Desempenho**: Atualizações desnecessárias afetam a experiência do usuário

## 🧠 Níveis de Estado

Podemos categorizar o estado em diferentes níveis:

1. **Estado Local**: Pertence a um único componente
2. **Estado de Árvore**: Compartilhado entre componentes em uma subárvore da aplicação
3. **Estado Global**: Acessível em toda a aplicação
4. **Estado Persistido**: Armazenado além da sessão atual (localStorage, etc.)
5. **Estado Servidor**: Dados mantidos em um servidor remoto

## 🛠️ Abordagens Nativas do React

### 🪝 useState e useReducer

Para estado local simples, `useState` é suficiente:

```jsx
import React, { useState } from 'react';

function Contador() {
  const [contador, setContador] = useState(0);
  
  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={() => setContador(contador + 1)}>
        Incrementar
      </button>
    </div>
  );
}
```

Para lógica de estado mais complexa, `useReducer` oferece mais estrutura:

```jsx
import React, { useReducer } from 'react';

// Definição do reducer
function carrinhoReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM':
      return {
        ...state,
        itens: [...state.itens, action.payload],
        total: state.total + action.payload.preco
      };
    case 'REMOVE_ITEM':
      const itemRemovido = state.itens.find(item => item.id === action.payload);
      return {
        ...state,
        itens: state.itens.filter(item => item.id !== action.payload),
        total: state.total - (itemRemovido ? itemRemovido.preco : 0)
      };
    case 'CLEAR_CART':
      return {
        itens: [],
        total: 0
      };
    default:
      return state;
  }
}

function CarrinhoCompras() {
  // Estado inicial
  const estadoInicial = {
    itens: [],
    total: 0
  };
  
  // useReducer recebe o reducer e o estado inicial
  const [carrinho, dispatch] = useReducer(carrinhoReducer, estadoInicial);
  
  const adicionarItem = (produto) => {
    dispatch({
      type: 'ADD_ITEM',
      payload: produto
    });
  };
  
  const removerItem = (produtoId) => {
    dispatch({
      type: 'REMOVE_ITEM',
      payload: produtoId
    });
  };
  
  const limparCarrinho = () => {
    dispatch({ type: 'CLEAR_CART' });
  };
  
  return (
    <div>
      <h2>Seu Carrinho (Total: R$ {carrinho.total.toFixed(2)})</h2>
      <ul>
        {carrinho.itens.map(item => (
          <li key={item.id}>
            {item.nome} - R$ {item.preco.toFixed(2)}
            <button onClick={() => removerItem(item.id)}>
              Remover
            </button>
          </li>
        ))}
      </ul>
      <button onClick={limparCarrinho}>Limpar Carrinho</button>
      {/* Interface para adicionar produtos */}
    </div>
  );
}
```

### 🧬 Context API

O `Context` permite compartilhar dados entre componentes sem passar props manualmente através de cada nível:

```jsx
import React, { createContext, useContext, useReducer } from 'react';

// Criação do contexto
const CarrinhoContext = createContext();

// Definição do reducer
function carrinhoReducer(state, action) {
  // ...mesmo reducer do exemplo anterior
}

// Provedor do contexto
function CarrinhoProvider({ children }) {
  const [carrinho, dispatch] = useReducer(carrinhoReducer, {
    itens: [],
    total: 0
  });
  
  // Valores e ações a serem compartilhados
  const valor = {
    carrinho,
    adicionarItem: (produto) => {
      dispatch({ type: 'ADD_ITEM', payload: produto });
    },
    removerItem: (produtoId) => {
      dispatch({ type: 'REMOVE_ITEM', payload: produtoId });
    },
    limparCarrinho: () => {
      dispatch({ type: 'CLEAR_CART' });
    }
  };
  
  return (
    <CarrinhoContext.Provider value={valor}>
      {children}
    </CarrinhoContext.Provider>
  );
}

// Hook para acessar o contexto facilmente
function useCarrinho() {
  const contexto = useContext(CarrinhoContext);
  
  if (!contexto) {
    throw new Error('useCarrinho deve ser usado dentro de um CarrinhoProvider');
  }
  
  return contexto;
}

// Uso em componentes
function App() {
  return (
    <CarrinhoProvider>
      <div className="app">
        <Navbar />
        <ListaProdutos />
        <CarrinhoDeCompras />
      </div>
    </CarrinhoProvider>
  );
}

function CarrinhoDeCompras() {
  const { carrinho, removerItem, limparCarrinho } = useCarrinho();
  
  return (
    <div className="carrinho">
      <h2>Seu Carrinho (Total: R$ {carrinho.total.toFixed(2)})</h2>
      <ul>
        {carrinho.itens.map(item => (
          <li key={item.id}>
            {item.nome} - R$ {item.preco.toFixed(2)}
            <button onClick={() => removerItem(item.id)}>
              Remover
            </button>
          </li>
        ))}
      </ul>
      <button onClick={limparCarrinho}>Limpar Carrinho</button>
    </div>
  );
}

function BotaoAdicionarAoCarrinho({ produto }) {
  const { adicionarItem } = useCarrinho();
  
  return (
    <button onClick={() => adicionarItem(produto)}>
      Adicionar ao Carrinho
    </button>
  );
}
```

## 📚 Bibliotecas de Gerenciamento de Estado

Para aplicações maiores e mais complexas, bibliotecas especializadas oferecem recursos adicionais.

### 🔄 Redux

Redux é uma das bibliotecas mais populares para gerenciamento de estado:

```jsx
import { createStore } from 'redux';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Definição do reducer
const contadorReducer = (state = { valor: 0 }, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { valor: state.valor + 1 };
    case 'DECREMENT':
      return { valor: state.valor - 1 };
    default:
      return state;
  }
};

// Criação da store
const store = createStore(contadorReducer);

// Componente provedor
function App() {
  return (
    <Provider store={store}>
      <div>
        <h1>Contador com Redux</h1>
        <ContadorComponente />
      </div>
    </Provider>
  );
}

// Componente que usa a store
function ContadorComponente() {
  // Hook para acessar o estado
  const contador = useSelector(state => state.valor);
  // Hook para disparar ações
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>
        Incrementar
      </button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>
        Decrementar
      </button>
    </div>
  );
}
```

### 📦 Redux Toolkit

O Redux Toolkit simplifica o uso do Redux e é a abordagem recomendada atualmente:

```jsx
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Criação de um slice
const contadorSlice = createSlice({
  name: 'contador',
  initialState: { valor: 0 },
  reducers: {
    incrementar: (state) => {
      state.valor += 1;
    },
    decrementar: (state) => {
      state.valor -= 1;
    },
    incrementarPorValor: (state, action) => {
      state.valor += action.payload;
    }
  }
});

// Exportação das action creators
export const { incrementar, decrementar, incrementarPorValor } = contadorSlice.actions;

// Configuração da store
const store = configureStore({
  reducer: {
    contador: contadorSlice.reducer
  }
});

// Componente provedor
function App() {
  return (
    <Provider store={store}>
      <div>
        <h1>Contador com Redux Toolkit</h1>
        <ContadorComponente />
      </div>
    </Provider>
  );
}

// Componente que usa a store
function ContadorComponente() {
  const contador = useSelector(state => state.contador.valor);
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={() => dispatch(incrementar())}>
        Incrementar
      </button>
      <button onClick={() => dispatch(decrementar())}>
        Decrementar
      </button>
      <button onClick={() => dispatch(incrementarPorValor(5))}>
        Adicionar 5
      </button>
    </div>
  );
}
```

### 🧿 Zustand

Zustand é uma biblioteca minimalista que simplifica o gerenciamento de estado global:

```jsx
import create from 'zustand';

// Criação da store
const useStore = create(set => ({
  contador: 0,
  incrementar: () => set(state => ({ contador: state.contador + 1 })),
  decrementar: () => set(state => ({ contador: state.contador - 1 })),
  resetar: () => set({ contador: 0 })
}));

// Componente que usa a store
function Contador() {
  const { contador, incrementar, decrementar, resetar } = useStore();
  
  return (
    <div>
      <h1>Contador: {contador}</h1>
      <button onClick={incrementar}>Incrementar</button>
      <button onClick={decrementar}>Decrementar</button>
      <button onClick={resetar}>Resetar</button>
    </div>
  );
}
```

### 🔄 Recoil

Recoil é uma biblioteca desenvolvida pelo Facebook que introduz o conceito de átomos:

```jsx
import React from 'react';
import { 
  RecoilRoot, 
  atom, 
  selector, 
  useRecoilState, 
  useRecoilValue 
} from 'recoil';

// Definição de um átomo (unidade básica de estado)
const contadorState = atom({
  key: 'contadorState',
  default: 0
});

// Selector (estado derivado)
const contadorQuadradoState = selector({
  key: 'contadorQuadradoState',
  get: ({get}) => {
    const contador = get(contadorState);
    return contador * contador;
  }
});

function App() {
  return (
    <RecoilRoot>
      <ContadorComponente />
    </RecoilRoot>
  );
}

function ContadorComponente() {
  const [contador, setContador] = useRecoilState(contadorState);
  const contadorQuadrado = useRecoilValue(contadorQuadradoState);
  
  return (
    <div>
      <h1>Contador: {contador}</h1>
      <p>Contador ao quadrado: {contadorQuadrado}</p>
      <button onClick={() => setContador(contador + 1)}>
        Incrementar
      </button>
      <button onClick={() => setContador(contador - 1)}>
        Decrementar
      </button>
    </div>
  );
}
```

### ⚛️ Jotai

Jotai é inspirado no Recoil mas com uma abordagem mais simples:

```jsx
import React from 'react';
import { atom, useAtom } from 'jotai';

// Definição de um átomo
const contadorAtom = atom(0);

// Átomo derivado
const contadorDobradoAtom = atom(
  (get) => get(contadorAtom) * 2
);

function Contador() {
  const [contador, setContador] = useAtom(contadorAtom);
  const [contadorDobrado] = useAtom(contadorDobradoAtom);
  
  return (
    <div>
      <h1>Contador: {contador}</h1>
      <p>Contador dobrado: {contadorDobrado}</p>
      <button onClick={() => setContador(c => c + 1)}>
        Incrementar
      </button>
    </div>
  );
}
```

## 🧠 Estratégias para Escolher uma Solução

A escolha da abordagem de gerenciamento de estado depende da complexidade e requisitos de sua aplicação:

### 📊 Guia de Decisão

1. **Aplicações Pequenas**:
   - Use `useState` e `useReducer` para componentes individuais
   - Use `Context API` para compartilhar estado entre alguns componentes relacionados

2. **Aplicações Médias**:
   - Combine `Context` com `useReducer` para áreas específicas da aplicação
   - Considere Zustand ou Jotai por sua simplicidade

3. **Aplicações Grandes**:
   - Redux Toolkit para gerenciamento robusto e ecossistema maduro
   - Recoil para casos de uso com estado altamente interconectado

### 🎯 Fatores a Considerar

- 🧱 **Complexidade**: Quanto mais complexo o estado, mais estruturada deve ser a solução
- 🔄 **Frequência de atualizações**: Otimizações importantes para atualizações frequentes
- 👥 **Tamanho da equipe**: Soluções com convenções claras ajudam equipes maiores
- 📚 **Curva de aprendizado**: Considere o conhecimento atual da equipe
- 🔍 **Ferramentas de debugging**: Algumas soluções têm suporte melhor para desenvolvimento
- 🧪 **Testabilidade**: Estado previsível é mais fácil de testar

## 📝 Padrões e Práticas para Gerenciamento de Estado

### 🛠️ Command Query Responsibility Segregation (CQRS)

Separe operações que modificam estado (commands) das que apenas leem (queries):

```jsx
// Exemplo com useReducer
function useUsuarioState() {
  const [state, dispatch] = useReducer(usuarioReducer, initialState);
  
  // Commands (modificam o estado)
  const commands = {
    atualizarPerfil: (dadosPerfil) => {
      dispatch({ type: 'ATUALIZAR_PERFIL', payload: dadosPerfil });
    },
    atualizarPreferencias: (preferencias) => {
      dispatch({ type: 'ATUALIZAR_PREFERENCIAS', payload: preferencias });
    }
  };
  
  // Queries (apenas leem o estado)
  const queries = {
    getNomeCompleto: () => `${state.nome} ${state.sobrenome}`,
    getIniciais: () => `${state.nome[0]}${state.sobrenome[0]}`,
    isPremium: () => state.tipoConta === 'premium'
  };
  
  return { state, ...commands, ...queries };
}
```

### 🧩 Normalização de Dados

Para dados complexos ou relacionais, normalize-os para evitar duplicação:

```jsx
// Estrutura normalizada
const estadoInicial = {
  usuarios: {
    byId: {
      'user1': { id: 'user1', nome: 'Ana', departamentoId: 'dept1' },
      'user2': { id: 'user2', nome: 'Bruno', departamentoId: 'dept2' }
    },
    allIds: ['user1', 'user2']
  },
  departamentos: {
    byId: {
      'dept1': { id: 'dept1', nome: 'Marketing' },
      'dept2': { id: 'dept2', nome: 'Engenharia' }
    },
    allIds: ['dept1', 'dept2']
  }
};

// Seletores para acessar dados relacionados
const getUsuariosDoDepartamento = (state, departamentoId) => {
  return state.usuarios.allIds
    .map(id => state.usuarios.byId[id])
    .filter(usuario => usuario.departamentoId === departamentoId);
};
```

### 🎣 Custom Hooks para Lógica de Estado

Encapsule lógica de estado complexa em hooks personalizados:

```jsx
// Hook para formulário com validação
function useFormulario(valoresIniciais, validacoes) {
  const [valores, setValores] = useState(valoresIniciais);
  const [erros, setErros] = useState({});
  const [enviando, setEnviando] = useState(false);
  const [enviado, setEnviado] = useState(false);
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({
      ...valores,
      [name]: value
    });
    
    // Validação em tempo real (opcional)
    if (validacoes[name]) {
      const erro = validacoes[name](value);
      setErros(erros => ({
        ...erros,
        [name]: erro
      }));
    }
  };
  
  const validarTudo = () => {
    const novosErros = {};
    
    Object.keys(validacoes).forEach(campo => {
      const erro = validacoes[campo](valores[campo]);
      if (erro) {
        novosErros[campo] = erro;
      }
    });
    
    setErros(novosErros);
    return Object.keys(novosErros).length === 0;
  };
  
  const handleSubmit = async (e, onSubmit) => {
    e.preventDefault();
    
    if (validarTudo()) {
      setEnviando(true);
      try {
        await onSubmit(valores);
        setEnviado(true);
        // Opcional: resetar formulário
      } catch (erro) {
        setErros({
          submit: erro.message
        });
      } finally {
        setEnviando(false);
      }
    }
  };
  
  return {
    valores,
    erros,
    enviando,
    enviado,
    handleChange,
    handleSubmit,
    setValores
  };
}

// Uso
function FormularioContato() {
  const validacoes = {
    nome: (valor) => 
      valor.length < 3 ? 'Nome deve ter pelo menos 3 caracteres' : null,
    email: (valor) => 
      !/^\S+@\S+\.\S+$/.test(valor) ? 'Email inválido' : null,
    mensagem: (valor) => 
      valor.length < 10 ? 'Mensagem muito curta' : null
  };
  
  const { 
    valores, 
    erros, 
    enviando, 
    enviado,
    handleChange, 
    handleSubmit 
  } = useFormulario(
    { nome: '', email: '', mensagem: '' },
    validacoes
  );
  
  const enviarFormulario = async (dados) => {
    // Lógica para enviar dados ao servidor
    await api.post('/contato', dados);
  };
  
  return (
    <form onSubmit={(e) => handleSubmit(e, enviarFormulario)}>
      {/* Campos do formulário */}
      <div>
        <label>Nome:</label>
        <input
          name="nome"
          value={valores.nome}
          onChange={handleChange}
        />
        {erros.nome && <p className="erro">{erros.nome}</p>}
      </div>
      
      {/* Outros campos... */}
      
      <button type="submit" disabled={enviando}>
        {enviando ? 'Enviando...' : 'Enviar'}
      </button>
      
      {enviado && <p className="sucesso">Mensagem enviada com sucesso!</p>}
      {erros.submit && <p className="erro">{erros.submit}</p>}
    </form>
  );
}
```

## 🧪 Testando o Gerenciamento de Estado

Testar adequadamente seu gerenciamento de estado é crucial:

```jsx
// Testando um reducer
import { carrinhoReducer } from './carrinhoReducer';

describe('Carrinho Reducer', () => {
  test('adiciona item ao carrinho', () => {
    const estadoInicial = { itens: [], total: 0 };
    const novoProduto = { id: '1', nome: 'Produto Teste', preco: 50 };
    
    const novoEstado = carrinhoReducer(estadoInicial, {
      type: 'ADD_ITEM',
      payload: novoProduto
    });
    
    expect(novoEstado.itens).toHaveLength(1);
    expect(novoEstado.itens[0]).toEqual(novoProduto);
    expect(novoEstado.total).toBe(50);
  });
  
  test('remove item do carrinho', () => {
    const estadoInicial = {
      itens: [{ id: '1', nome: 'Produto Teste', preco: 50 }],
      total: 50
    };
    
    const novoEstado = carrinhoReducer(estadoInicial, {
      type: 'REMOVE_ITEM',
      payload: '1'
    });
    
    expect(novoEstado.itens).toHaveLength(0);
    expect(novoEstado.total).toBe(0);
  });
});

// Testando um hook personalizado
import { renderHook, act } from '@testing-library/react-hooks';
import { useContador } from './useContador';

describe('useContador', () => {
  test('inicia com valor padrão', () => {
    const { result } = renderHook(() => useContador());
    expect(result.current.contador).toBe(0);
  });
  
  test('incrementa o contador', () => {
    const { result } = renderHook(() => useContador());
    
    act(() => {
      result.current.incrementar();
    });
    
    expect(result.current.contador).toBe(1);
  });
  
  test('reseta o contador', () => {
    const { result } = renderHook(() => useContador(10));
    
    act(() => {
      result.current.resetar();
    });
    
    expect(result.current.contador).toBe(0);
  });
});
```

## 🌟 Dicas e Melhores Práticas

1. **Mantenha o estado mínimo**: Guarde apenas o que realmente precisa ser um estado
2. **Coloque o estado o mais próximo possível de onde é usado**: Não eleve estado sem necessidade
3. **Prefira imutabilidade**: Sempre crie novos objetos/arrays em vez de modificar existentes
4. **Derive dados quando possível**: Use propriedades computadas em vez de estado duplicado
5. **Documente a estrutura do estado**: Especialmente em projetos maiores
6. **Use TypeScript**: Tipagem forte ajuda a evitar erros relacionados a estado
7. **Evite aninhamento profundo**: Estados muito aninhados são difíceis de atualizar e manter
8. **Separe UI e lógica de negócios**: Desacople as regras de estado da renderização da UI

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 