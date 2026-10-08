<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Hooks Fundamentais no React

Hooks são funções especiais introduzidas no React 16.8 que permitem usar recursos como estado e outras funcionalidades do React sem escrever componentes de classe. Esta seção explora os hooks principais e como utilizá-los para criar componentes funcionais poderosos.

## Por que Hooks?

Antes dos Hooks, componentes com estado precisavam ser escritos como classes. Os Hooks resolvem vários problemas:

- **Reutilização de lógica com estado** entre componentes sem padrões complexos
- **Organização de código** por funcionalidade em vez de ciclo de vida
- **Simplificação de componentes complexos** para melhor legibilidade
- **Uso de recursos React em componentes funcionais** sem conversão para classes

## useState: Gerenciando Estado Local

O hook `useState` permite adicionar estado a componentes funcionais:

```jsx
import React, { useState } from 'react';

function Contador() {
  // Declaração de estado com valor inicial 0
  const [contador, setContador] = useState(0);
  
  return (
    <div>
      <p>Você clicou {contador} vezes</p>
      <button onClick={() => setContador(contador + 1)}>
        Clique aqui
      </button>
    </div>
  );
}
```

### Sintaxe do useState

```jsx
const [variavel, funcaoAtualizadora] = useState(valorInicial);
```

- `variavel` - Valor atual do estado
- `funcaoAtualizadora` - Função que atualiza o estado
- `valorInicial` - O valor inicial do estado (número, string, booleano, array, objeto, etc)

### Estado com Objetos

```jsx
function Formulario() {
  const [formulario, setFormulario] = useState({
    nome: '',
    email: '',
    mensagem: ''
  });
  
  const atualizarCampo = (campo, valor) => {
    // Importante: preservar os outros campos ao atualizar apenas um
    setFormulario({
      ...formulario,
      [campo]: valor
    });
  };
  
  return (
    <form>
      <input
        value={formulario.nome}
        onChange={(e) => atualizarCampo('nome', e.target.value)}
      />
      {/* ... outros campos ... */}
    </form>
  );
}
```

### Atualizações Funcionais

Quando o novo estado depende do estado anterior, use a forma funcional:

```jsx
function ContadorSeguro() {
  const [contador, setContador] = useState(0);
  
  const incrementar = () => {
    // ✅ Forma segura: recebe o estado atual como parâmetro
    setContador(estadoAnterior => estadoAnterior + 1);
  };
  
  return (
    <button onClick={incrementar}>Incrementar</button>
  );
}
```

## useEffect: Efeitos Colaterais

O hook `useEffect` permite executar efeitos colaterais em componentes funcionais:

```jsx
import React, { useState, useEffect } from 'react';

function TituloDocumento() {
  const [contador, setContador] = useState(0);
  
  // Executa após cada renderização
  useEffect(() => {
    // Atualiza o título do documento usando o valor do estado
    document.title = `Você clicou ${contador} vezes`;
  });
  
  return (
    <button onClick={() => setContador(contador + 1)}>
      Clique aqui
    </button>
  );
}
```

### Sintaxe do useEffect

```jsx
useEffect(() => {
  // Código do efeito
  
  // Opcional: função de limpeza
  return () => {
    // Código de limpeza
  };
}, [dependencias]); // Array de dependências (opcional)
```

### Executando Efeitos Condicionalmente

O array de dependências controla quando o efeito é executado:

```jsx
function Perfil({ userId }) {
  const [usuario, setUsuario] = useState(null);
  
  // Executa apenas quando userId muda
  useEffect(() => {
    async function buscarUsuario() {
      const resposta = await fetch(`/api/usuarios/${userId}`);
      const dados = await resposta.json();
      setUsuario(dados);
    }
    
    buscarUsuario();
  }, [userId]); // Array de dependências
  
  if (!usuario) return <p>Carregando...</p>;
  
  return <p>Olá, {usuario.nome}!</p>;
}
```

### Ciclo de Vida com useEffect

Podemos simular os métodos de ciclo de vida com useEffect:

```jsx
function CiclodeVida() {
  // componentDidMount
  useEffect(() => {
    console.log('Componente montado');
    
    // componentWillUnmount
    return () => {
      console.log('Componente será desmontado');
    };
  }, []); // Array vazio = executa apenas na montagem e desmontagem
  
  // Similar ao componentDidUpdate
  useEffect(() => {
    console.log('Componente atualizado');
  }); // Sem array = executa após cada renderização
  
  return <div>Exemplo de ciclo de vida</div>;
}
```

### Limpeza de Efeitos

A função retornada é executada antes do efeito ser aplicado novamente ou quando o componente é desmontado:

```jsx
function TimerComponent() {
  const [segundos, setSegundos] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setSegundos(s => s + 1);
    }, 1000);
    
    // Limpa o intervalo quando o componente é desmontado
    return () => clearInterval(interval);
  }, []);
  
  return <p>Segundos: {segundos}</p>;
}
```

## useContext: Compartilhando Dados

O hook `useContext` permite acessar o contexto sem componentes consumidores:

```jsx
import React, { useContext, createContext } from 'react';

// Criar um contexto com um valor padrão
const TemaContexto = createContext('claro');

function AplicacaoTema() {
  return (
    <TemaContexto.Provider value="escuro">
      <Barra />
    </TemaContexto.Provider>
  );
}

function Barra() {
  // Não precisamos de <Consumer> com useContext
  const tema = useContext(TemaContexto);
  
  return (
    <div className={`barra tema-${tema}`}>
      O tema atual é: {tema}
    </div>
  );
}
```

## useRef: Referências Persistentes

O hook `useRef` cria uma referência mutável que persiste entre renderizações:

```jsx
import React, { useRef, useEffect } from 'react';

function InputComFoco() {
  // Cria uma referência
  const inputRef = useRef(null);
  
  // Após a montagem, foca no input
  useEffect(() => {
    inputRef.current.focus();
  }, []);
  
  return (
    <input 
      ref={inputRef} 
      type="text" 
      placeholder="Este input recebe foco automaticamente"
    />
  );
}
```

### useRef Para Valores Persistentes

Também pode ser usado para armazenar valores que não causam re-renderização:

```jsx
function Temporizador() {
  const [tempo, setTempo] = useState(0);
  // Usando useRef para armazenar o ID do intervalo
  const intervalRef = useRef(null);
  
  const iniciar = () => {
    if (intervalRef.current !== null) return;
    
    intervalRef.current = setInterval(() => {
      setTempo(t => t + 1);
    }, 1000);
  };
  
  const parar = () => {
    clearInterval(intervalRef.current);
    intervalRef.current = null;
  };
  
  // Limpar o intervalo quando o componente for desmontado
  useEffect(() => {
    return () => clearInterval(intervalRef.current);
  }, []);
  
  return (
    <div>
      <p>Tempo: {tempo} segundos</p>
      <button onClick={iniciar}>Iniciar</button>
      <button onClick={parar}>Parar</button>
    </div>
  );
}
```

## useMemo e useCallback: Otimização

### useMemo: Memoização de Valores

O hook `useMemo` memoriza o resultado de um cálculo custoso:

```jsx
import React, { useState, useMemo } from 'react';

function ListaFiltrada({ itens, filtro }) {
  // Recalcula apenas quando itens ou filtro mudam
  const itensFiltrados = useMemo(() => {
    console.log('Filtrando itens...');
    return itens.filter(item => item.includes(filtro));
  }, [itens, filtro]);
  
  return (
    <ul>
      {itensFiltrados.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  );
}
```

### useCallback: Memoização de Funções

O hook `useCallback` memoriza definições de funções:

```jsx
import React, { useState, useCallback } from 'react';

function Contador() {
  const [contador, setContador] = useState(0);
  
  // Função memorizada - recriada apenas quando contador muda
  const incrementar = useCallback(() => {
    setContador(contador + 1);
  }, [contador]);
  
  return (
    <div>
      <p>Contador: {contador}</p>
      <BotaoIncremento onIncremento={incrementar} />
    </div>
  );
}

// Este componente só re-renderiza se onIncremento mudar
const BotaoIncremento = React.memo(({ onIncremento }) => {
  console.log('Botão renderizado');
  return <button onClick={onIncremento}>Incrementar</button>;
});
```

## useReducer: Estado Complexo

O hook `useReducer` é uma alternativa ao `useState` para lógica de estado complexo:

```jsx
import React, { useReducer } from 'react';

// Função reducer define como o estado é atualizado
function reducer(state, action) {
  switch (action.type) {
    case 'incrementar':
      return { contador: state.contador + 1 };
    case 'decrementar':
      return { contador: state.contador - 1 };
    case 'reset':
      return { contador: 0 };
    default:
      throw new Error('Ação não reconhecida');
  }
}

function ContadorAvancado() {
  // useReducer recebe o reducer e o estado inicial
  const [state, dispatch] = useReducer(reducer, { contador: 0 });
  
  return (
    <div>
      <p>Contador: {state.contador}</p>
      <button onClick={() => dispatch({ type: 'incrementar' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrementar' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

## Criando Hooks Personalizados

Você pode criar seus próprios hooks para reutilizar lógica entre componentes:

```jsx
import { useState, useEffect } from 'react';

// Hook personalizado para formulários
function useFormulario(valoresIniciais) {
  const [valores, setValores] = useState(valoresIniciais);
  const [erros, setErros] = useState({});
  const [enviando, setEnviando] = useState(false);
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({ ...valores, [name]: value });
  };
  
  const validar = () => {
    const novosErros = {};
    
    // Lógica de validação aqui
    if (!valores.nome) novosErros.nome = 'Nome é obrigatório';
    if (!valores.email) novosErros.email = 'Email é obrigatório';
    
    setErros(novosErros);
    return Object.keys(novosErros).length === 0;
  };
  
  const handleSubmit = async (e, callback) => {
    e.preventDefault();
    
    if (validar()) {
      setEnviando(true);
      try {
        await callback(valores);
        setValores(valoresIniciais); // Reset após sucesso
      } catch (error) {
        console.error('Erro ao enviar:', error);
      } finally {
        setEnviando(false);
      }
    }
  };
  
  return {
    valores,
    erros,
    enviando,
    handleChange,
    handleSubmit,
    setValores
  };
}

// Uso do hook personalizado
function FormularioContato() {
  const { 
    valores, 
    erros, 
    enviando, 
    handleChange, 
    handleSubmit 
  } = useFormulario({
    nome: '',
    email: '',
    mensagem: ''
  });
  
  const enviarFormulario = async (dados) => {
    // Lógica de envio
    await fetch('/api/contato', {
      method: 'POST',
      body: JSON.stringify(dados)
    });
    alert('Mensagem enviada com sucesso!');
  };
  
  return (
    <form onSubmit={(e) => handleSubmit(e, enviarFormulario)}>
      <div>
        <label>Nome:</label>
        <input
          name="nome"
          value={valores.nome}
          onChange={handleChange}
        />
        {erros.nome && <p className="erro">{erros.nome}</p>}
      </div>
      
      <div>
        <label>Email:</label>
        <input
          name="email"
          value={valores.email}
          onChange={handleChange}
        />
        {erros.email && <p className="erro">{erros.email}</p>}
      </div>
      
      <div>
        <label>Mensagem:</label>
        <textarea
          name="mensagem"
          value={valores.mensagem}
          onChange={handleChange}
        />
      </div>
      
      <button type="submit" disabled={enviando}>
        {enviando ? 'Enviando...' : 'Enviar'}
      </button>
    </form>
  );
}
```

## Exemplo de Hook para Consumo de API

Um hook personalizado para chamadas de API:

```jsx
import { useState, useEffect } from 'react';

function useAPI(url) {
  const [dados, setDados] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  useEffect(() => {
    let mounted = true;
    
    async function fetchDados() {
      setCarregando(true);
      try {
        const resposta = await fetch(url);
        
        if (!resposta.ok) {
          throw new Error(`Erro ${resposta.status}: ${resposta.statusText}`);
        }
        
        const resultado = await resposta.json();
        if (mounted) {
          setDados(resultado);
          setErro(null);
        }
      } catch (error) {
        if (mounted) {
          setErro(error.message);
          setDados(null);
        }
      } finally {
        if (mounted) {
          setCarregando(false);
        }
      }
    }
    
    fetchDados();
    
    // Cleanup function
    return () => {
      mounted = false;
    };
  }, [url]);
  
  return { dados, carregando, erro };
}

// Uso
function ProdutosLista() {
  const { dados, carregando, erro } = useAPI('/api/produtos');
  
  if (carregando) return <p>Carregando produtos...</p>;
  if (erro) return <p>Erro: {erro}</p>;
  if (!dados) return <p>Nenhum produto encontrado</p>;
  
  return (
    <ul>
      {dados.map(produto => (
        <li key={produto.id}>{produto.nome} - R$ {produto.preco}</li>
      ))}
    </ul>
  );
}
```

## Regras dos Hooks

Para usar hooks corretamente, siga estas regras:

1. **Apenas chame hooks no nível superior** - Não use hooks dentro de loops, condições ou funções aninhadas
2. **Apenas chame hooks em componentes funcionais ou hooks personalizados** - Não use hooks em funções regulares ou componentes de classe
3. **Os nomes dos hooks personalizados devem começar com "use"** - Por exemplo, `useFormulario`, `useFetch`

```jsx
// ❌ ERRADO: Hook dentro de condição
function ComponenteErrado(props) {
  if (props.condicao) {
    const [estado, setEstado] = useState(true); // Isso quebrará o React
  }
  
  // ...
}

// ✅ CORRETO: Hooks sempre no nível superior
function ComponenteCerto(props) {
  const [estado, setEstado] = useState(false);
  
  // Você pode usar o estado dentro de condições
  if (props.condicao) {
    // Manipular o estado aqui é permitido
  }
  
  // ...
}
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>