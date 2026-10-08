<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Eventos e Manipulação no React

A manipulação de eventos é uma parte fundamental da criação de interfaces interativas no React. Esta seção explora como capturar e responder a interações do usuário, como cliques, entradas de formulário e muito mais.

## Eventos no React vs. HTML

O React implementa um sistema de eventos sintéticos que são similares, mas não idênticos, aos eventos nativos do DOM. Isso permite que o React trabalhe de forma consistente em diferentes navegadores.

### Principais Diferenças:

```jsx
<!-- HTML tradicional -->
<button onclick="handleClick()">Clique em mim</button>

{/* React */}
<button onClick={handleClick}>Clique em mim</button>
```

Observe as diferenças:
- Nome do evento em **camelCase** no React (`onClick` vs. `onclick`)
- No React, passamos uma **função** como manipulador, não uma string
- No React, não podemos retornar `false` para evitar comportamento padrão (use `preventDefault()`)

## Manipuladores de Eventos Básicos

### Manipulando Cliques

```jsx
function BotaoExemplo() {
  const handleClick = () => {
    console.log('Botão foi clicado!');
    alert('Você clicou no botão!');
  };
  
  return (
    <button onClick={handleClick}>
      Clique em mim
    </button>
  );
}
```

### Eventos de Formulário

```jsx
function FormularioExemplo() {
  const handleSubmit = (evento) => {
    evento.preventDefault(); // Impede o comportamento padrão (recarregar a página)
    console.log('Formulário enviado!');
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input type="text" placeholder="Digite algo" />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

## Lista de Eventos Comuns

O React suporta uma ampla variedade de eventos:

### Eventos do Mouse
- `onClick` - Clique do mouse
- `onDoubleClick` - Clique duplo
- `onMouseEnter` - Mouse entra na área do elemento
- `onMouseLeave` - Mouse sai da área do elemento
- `onMouseMove` - Mouse se move dentro do elemento
- `onMouseDown` - Botão do mouse é pressionado
- `onMouseUp` - Botão do mouse é liberado

### Eventos do Teclado
- `onKeyDown` - Tecla é pressionada
- `onKeyPress` - Tecla é pressionada (apenas caracteres)
- `onKeyUp` - Tecla é liberada

### Eventos de Formulário
- `onChange` - Valor do input é alterado
- `onInput` - Valor do input é alterado (menos comum no React)
- `onSubmit` - Formulário é enviado
- `onFocus` - Elemento recebe foco
- `onBlur` - Elemento perde foco

### Eventos de Toque
- `onTouchStart` - Toque iniciado
- `onTouchMove` - Movimento durante toque
- `onTouchEnd` - Toque finalizado

## Objeto de Evento Sintético

Quando um evento ocorre, o React passa um objeto de evento sintético para o manipulador:

```jsx
function InputExemplo() {
  const handleChange = (evento) => {
    // 'evento' é um objeto de evento sintético
    console.log('Valor:', evento.target.value);
    console.log('Nome do elemento:', evento.target.name);
    console.log('Tipo do evento:', evento.type);
  };
  
  return (
    <input
      type="text"
      name="exemplo"
      onChange={handleChange}
      placeholder="Digite algo"
    />
  );
}
```

### Propriedades e Métodos Comuns do Evento

- `e.target` - O elemento DOM que disparou o evento
- `e.currentTarget` - O elemento que possui o manipulador de eventos
- `e.preventDefault()` - Impede o comportamento padrão do navegador
- `e.stopPropagation()` - Impede a propagação do evento para elementos pai
- `e.nativeEvent` - O evento DOM nativo subjacente

## Binding de `this` em Componentes de Classe

Em componentes de classe, precisamos vincular o `this` corretamente aos manipuladores de eventos:

```jsx
class BotaoClasse extends React.Component {
  constructor(props) {
    super(props);
    // Método 1: Binding no construtor (recomendado)
    this.handleClick = this.handleClick.bind(this);
  }
  
  handleClick() {
    console.log('this no manipulador:', this);
    // 'this' aponta para a instância da classe
  }
  
  // Método 2: Usando propriedades de classe e arrow functions
  handleClickArrow = () => {
    console.log('this em arrow function:', this);
    // 'this' é automaticamente vinculado
  }
  
  render() {
    return (
      <div>
        <button onClick={this.handleClick}>
          Método clássico
        </button>
        
        <button onClick={this.handleClickArrow}>
          Com arrow function
        </button>
        
        {/* Método 3: Binding inline (não recomendado em componentes grandes) */}
        <button onClick={this.handleClick.bind(this)}>
          Binding inline
        </button>
        
        {/* Método 4: Arrow function inline */}
        <button onClick={() => this.handleClick()}>
          Arrow function inline
        </button>
      </div>
    );
  }
}
```

### ⚠️ Observações:
- Os métodos 1 e 2 são mais eficientes, pois o binding acontece apenas uma vez
- Os métodos 3 e 4 criam uma nova função a cada renderização, o que pode afetar o desempenho

## Passando Argumentos para Manipuladores de Eventos

### Em Componentes Funcionais

```jsx
function ListaDeItens() {
  const handleItemClick = (id, evento) => {
    console.log(`Item ${id} clicado`);
    console.log('Evento:', evento);
  };
  
  return (
    <ul>
      <li onClick={(e) => handleItemClick(1, e)}>Item 1</li>
      <li onClick={(e) => handleItemClick(2, e)}>Item 2</li>
      <li onClick={(e) => handleItemClick(3, e)}>Item 3</li>
    </ul>
  );
}
```

### Em Componentes de Classe

```jsx
class ListaDeItens extends React.Component {
  handleItemClick(id, evento) {
    console.log(`Item ${id} clicado`);
    console.log('Evento:', evento);
  }
  
  render() {
    return (
      <ul>
        <li onClick={(e) => this.handleItemClick(1, e)}>Item 1</li>
        <li onClick={(e) => this.handleItemClick(2, e)}>Item 2</li>
        <li onClick={(e) => this.handleItemClick(3, e)}>Item 3</li>
      </ul>
    );
  }
}
```

## Manipulação de Eventos com Estado

A combinação de eventos e estado é onde o React realmente brilha:

```jsx
function Contador() {
  const [contador, setContador] = useState(0);
  
  const incrementar = () => {
    setContador(contador + 1);
  };
  
  const decrementar = () => {
    setContador(prevContador => Math.max(0, prevContador - 1));
  };
  
  const resetar = () => {
    setContador(0);
  };
  
  return (
    <div>
      <p>Contagem: {contador}</p>
      <button onClick={incrementar}>+</button>
      <button onClick={decrementar}>-</button>
      <button onClick={resetar}>Reset</button>
    </div>
  );
}
```

## Trabalhando com Formulários

### Campos de Texto

```jsx
function FormularioNome() {
  const [nome, setNome] = useState('');
  
  const handleChange = (e) => {
    setNome(e.target.value);
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Nome enviado: ${nome}`);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <label>
        Nome:
        <input 
          type="text" 
          value={nome} 
          onChange={handleChange} 
        />
      </label>
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Select (Dropdown)

```jsx
function SeletorDeIdioma() {
  const [idioma, setIdioma] = useState('pt');
  
  const handleChange = (e) => {
    setIdioma(e.target.value);
  };
  
  return (
    <div>
      <label>
        Selecione um idioma:
        <select value={idioma} onChange={handleChange}>
          <option value="pt">Português</option>
          <option value="en">Inglês</option>
          <option value="es">Espanhol</option>
          <option value="fr">Francês</option>
        </select>
      </label>
      <p>Idioma selecionado: {idioma}</p>
    </div>
  );
}
```

### Checkbox

```jsx
function OpcoesDeConfiguracao() {
  const [notificacoes, setNotificacoes] = useState(true);
  const [temaEscuro, setTemaEscuro] = useState(false);
  
  return (
    <div>
      <label>
        <input
          type="checkbox"
          checked={notificacoes}
          onChange={(e) => setNotificacoes(e.target.checked)}
        />
        Receber notificações
      </label>
      <br />
      <label>
        <input
          type="checkbox"
          checked={temaEscuro}
          onChange={(e) => setTemaEscuro(e.target.checked)}
        />
        Tema escuro
      </label>
      <p>Configurações: {JSON.stringify({ notificacoes, temaEscuro })}</p>
    </div>
  );
}
```

### Radio Buttons

```jsx
function OpcaoDePagamento() {
  const [metodoPagamento, setMetodoPagamento] = useState('cartao');
  
  const handleChange = (e) => {
    setMetodoPagamento(e.target.value);
  };
  
  return (
    <div>
      <h3>Método de pagamento:</h3>
      <label>
        <input
          type="radio"
          value="cartao"
          checked={metodoPagamento === 'cartao'}
          onChange={handleChange}
        />
        Cartão de crédito
      </label>
      <br />
      <label>
        <input
          type="radio"
          value="boleto"
          checked={metodoPagamento === 'boleto'}
          onChange={handleChange}
        />
        Boleto bancário
      </label>
      <br />
      <label>
        <input
          type="radio"
          value="pix"
          checked={metodoPagamento === 'pix'}
          onChange={handleChange}
        />
        PIX
      </label>
      <p>Pagamento selecionado: {metodoPagamento}</p>
    </div>
  );
}
```

## Formulário Completo com Múltiplos Campos

```jsx
function FormularioCadastro() {
  const [formData, setFormData] = useState({
    nome: '',
    email: '',
    senha: '',
    idade: '',
    genero: '',
    interesses: {
      esportes: false,
      musica: false,
      tecnologia: false
    },
    observacoes: ''
  });
  
  // Manipulador genérico para campos de texto
  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
  };
  
  // Manipulador específico para checkboxes
  const handleCheckboxChange = (e) => {
    const { name, checked } = e.target;
    setFormData({
      ...formData,
      interesses: {
        ...formData.interesses,
        [name]: checked
      }
    });
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Dados do formulário:', formData);
    // Aqui você enviaria os dados para uma API, etc.
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="nome">Nome:</label>
        <input
          type="text"
          id="nome"
          name="nome"
          value={formData.nome}
          onChange={handleInputChange}
          required
        />
      </div>
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleInputChange}
          required
        />
      </div>
      
      <div>
        <label htmlFor="senha">Senha:</label>
        <input
          type="password"
          id="senha"
          name="senha"
          value={formData.senha}
          onChange={handleInputChange}
          required
        />
      </div>
      
      <div>
        <label htmlFor="idade">Idade:</label>
        <input
          type="number"
          id="idade"
          name="idade"
          value={formData.idade}
          onChange={handleInputChange}
        />
      </div>
      
      <div>
        <label>Gênero:</label>
        <select
          name="genero"
          value={formData.genero}
          onChange={handleInputChange}
        >
          <option value="">Selecione...</option>
          <option value="masculino">Masculino</option>
          <option value="feminino">Feminino</option>
          <option value="outro">Outro</option>
          <option value="prefiro_nao_informar">Prefiro não informar</option>
        </select>
      </div>
      
      <div>
        <label>Interesses:</label>
        <div>
          <label>
            <input
              type="checkbox"
              name="esportes"
              checked={formData.interesses.esportes}
              onChange={handleCheckboxChange}
            />
            Esportes
          </label>
        </div>
        <div>
          <label>
            <input
              type="checkbox"
              name="musica"
              checked={formData.interesses.musica}
              onChange={handleCheckboxChange}
            />
            Música
          </label>
        </div>
        <div>
          <label>
            <input
              type="checkbox"
              name="tecnologia"
              checked={formData.interesses.tecnologia}
              onChange={handleCheckboxChange}
            />
            Tecnologia
          </label>
        </div>
      </div>
      
      <div>
        <label htmlFor="observacoes">Observações:</label>
        <textarea
          id="observacoes"
          name="observacoes"
          value={formData.observacoes}
          onChange={handleInputChange}
        />
      </div>
      
      <button type="submit">Cadastrar</button>
    </form>
  );
}
```

## Eventos Personalizados Entre Componentes

No React, "eventos personalizados" são implementados passando funções como props:

```jsx
// Componente filho que dispara o "evento"
function BotaoAcao({ onAction }) {
  return (
    <button onClick={() => onAction('dados', 123)}>
      Executar Ação
    </button>
  );
}

// Componente pai que escuta o "evento"
function App() {
  const handleAction = (texto, numero) => {
    console.log(`Ação executada com: ${texto}, ${numero}`);
  };
  
  return (
    <div>
      <h1>Exemplo de Eventos Personalizados</h1>
      <BotaoAcao onAction={handleAction} />
    </div>
  );
}
```

## Dicas de Otimização

### Evite Definir Funções Dentro de `render`/JSX

```jsx
// ❌ Não recomendado: nova função em cada renderização
function ComponenteProblemático() {
  return (
    <button onClick={() => {
      console.log('Botão clicado');
      // Lógica complexa aqui...
    }}>
      Clique em mim
    </button>
  );
}

// ✅ Recomendado: defina a função fora do JSX
function ComponenteOtimizado() {
  const handleClick = () => {
    console.log('Botão clicado');
    // Lógica complexa aqui...
  };
  
  return (
    <button onClick={handleClick}>
      Clique em mim
    </button>
  );
}
```

### Use `useCallback` para Memorizar Manipuladores de Eventos

```jsx
import React, { useState, useCallback } from 'react';

function ComponenteOtimizado({ itemId }) {
  const [contagem, setContagem] = useState(0);
  
  // Esta função será recriada apenas quando itemId mudar
  const handleItemClick = useCallback(() => {
    console.log(`Item ${itemId} clicado`);
    setContagem(c => c + 1);
  }, [itemId]);
  
  return (
    <div>
      <p>Contagem: {contagem}</p>
      <button onClick={handleItemClick}>
        Clique no item {itemId}
      </button>
    </div>
  );
}
```

## Depuração de Eventos

### Verificando se o Manipulador é Chamado

```jsx
function DepuracaoDeEventos() {
  const handleClick = (e) => {
    console.log('Evento de clique disparado');
    console.log('Elemento alvo:', e.target);
    console.log('Tipo de evento:', e.type);
    console.log('Coordenadas:', { x: e.clientX, y: e.clientY });
    
    // Pare aqui se estiver usando as DevTools
    debugger;
  };
  
  return (
    <button onClick={handleClick}>
      Clique para Depurar
    </button>
  );
}
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>