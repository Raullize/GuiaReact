<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 📝 Listas e Chaves no React 🔑

A renderização de listas é uma das operações mais comuns em aplicações React. Esta seção explora como trabalhar eficientemente com coleções de dados, renderizar elementos a partir delas e entender a importância das chaves (keys) nesse processo. 🚀

## 🔁 Renderizando Múltiplos Componentes

No React, você pode criar coleções de elementos e incluí-los no JSX usando chaves `{}`:

```jsx
function ListaNumeros() {
  const numeros = [1, 2, 3, 4, 5];
  
  const elementosLista = numeros.map((numero) => 
    <li>{numero}</li>
  );
  
  return (
    <ul>{elementosLista}</ul>
  );
}
```

Este é o padrão básico para renderizar listas no React, embora esteja faltando algo importante: as **chaves**.

## 🔑 Chaves (Keys): O Que São e Por Que São Importantes

As chaves ajudam o React a identificar quais itens foram alterados, adicionados ou removidos. Elas devem ser atribuídas aos elementos dentro de um array para dar a eles uma identidade estável:

```jsx
function ListaNumeros() {
  const numeros = [1, 2, 3, 4, 5];
  
  // Adicionando chaves
  const elementosLista = numeros.map((numero) => 
    <li key={numero}>{numero}</li>
  );
  
  return (
    <ul>{elementosLista}</ul>
  );
}
```

### 🎯 Por que chaves são necessárias?

1. **Desempenho**: Ajudam o React a otimizar a renderização identificando quais elementos mudaram
2. **Estado Consistente**: Mantêm o estado dos componentes quando são re-renderizados
3. **Reconciliação**: Permitem que o algoritmo de reconciliação do React identifique mudanças com precisão

### ⚠️ Avisos e erros relacionados a chaves:

Sem chaves, você verá este aviso no console:
```
Warning: Each child in a list should have a unique "key" prop.
```

## 🎨 Selecionando Boas Chaves

### ✅ Boas opções para chaves:

1. **IDs únicos de banco de dados**:
```jsx
<li key={item.id}>{item.texto}</li>
```

2. **IDs gerados por UUID/nanoid**:
```jsx
import { nanoid } from 'nanoid';

const itens = dados.map(item =>
  <li key={nanoid()}>{item.texto}</li>
);
```

3. **Strings únicas nos dados**:
```jsx
<li key={usuario.email}>{usuario.nome}</li>
```

### ❌ O que evitar:

1. **Nunca use índices de array como chaves** (exceto como último recurso):
```jsx
// ❌ Não recomendado, a menos que a lista seja estática
const itens = dados.map((item, index) =>
  <li key={index}>{item.texto}</li>
);
```

2. **Nunca use valores aleatórios ou timestamps gerados durante a renderização**:
```jsx
// ❌ Muito ruim - cria uma nova chave a cada renderização
<li key={Math.random()}>{item.texto}</li>
```

3. **Nunca use valores não únicos**:
```jsx
// ❌ Péssimo - valores podem se repetir
<li key={item.categoria}>{item.nome}</li>
```

## 🧩 Padrões de Uso de Listas e Chaves

### 📋 Renderizando Listas Diretamente no JSX

```jsx
function ListaDeTarefas({ tarefas }) {
  return (
    <ul>
      {tarefas.map(tarefa => (
        <li key={tarefa.id}>
          {tarefa.titulo}
        </li>
      ))}
    </ul>
  );
}
```

### 🔄 Usando Componentes para Itens de Lista

```jsx
function ItemTarefa({ tarefa }) {
  return (
    <li>
      <input type="checkbox" checked={tarefa.concluida} />
      <span style={{ textDecoration: tarefa.concluida ? 'line-through' : 'none' }}>
        {tarefa.titulo}
      </span>
    </li>
  );
}

function ListaDeTarefas({ tarefas }) {
  return (
    <ul>
      {tarefas.map(tarefa => (
        <ItemTarefa key={tarefa.id} tarefa={tarefa} />
      ))}
    </ul>
  );
}
```

Observe que a chave deve estar no elemento que você está repetindo, não no componente filho.

### 📊 Listas Aninhadas

Para listas aninhadas, cada nível precisa de suas próprias chaves únicas:

```jsx
function ListaCategorias({ categorias }) {
  return (
    <div>
      {categorias.map(categoria => (
        <div key={categoria.id}>
          <h2>{categoria.nome}</h2>
          <ul>
            {categoria.itens.map(item => (
              <li key={item.id}>{item.nome}</li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}
```

### 🗂️ Agrupando Dados em Seções

```jsx
function ListaProdutos({ produtos }) {
  // Agrupa produtos por categoria
  const produtosPorCategoria = {};
  
  produtos.forEach(produto => {
    if (!produtosPorCategoria[produto.categoria]) {
      produtosPorCategoria[produto.categoria] = [];
    }
    produtosPorCategoria[produto.categoria].push(produto);
  });
  
  return (
    <div>
      {Object.entries(produtosPorCategoria).map(([categoria, produtosCategoria]) => (
        <div key={categoria}>
          <h2>{categoria}</h2>
          <ul>
            {produtosCategoria.map(produto => (
              <li key={produto.id}>{produto.nome} - R${produto.preco.toFixed(2)}</li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}
```

## 🧠 Manipulando Dados de Lista

### 🗑️ Filtrando Itens

```jsx
function ListaTarefasFiltradas({ tarefas }) {
  const [filtro, setFiltro] = useState('todas'); // 'todas', 'ativas', 'concluídas'
  
  const tarefasFiltradas = tarefas.filter(tarefa => {
    if (filtro === 'todas') return true;
    if (filtro === 'ativas') return !tarefa.concluida;
    if (filtro === 'concluidas') return tarefa.concluida;
    return true;
  });
  
  return (
    <div>
      <div>
        <button onClick={() => setFiltro('todas')}>Todas</button>
        <button onClick={() => setFiltro('ativas')}>Ativas</button>
        <button onClick={() => setFiltro('concluidas')}>Concluídas</button>
      </div>
      
      <ul>
        {tarefasFiltradas.map(tarefa => (
          <li key={tarefa.id}>
            <input 
              type="checkbox" 
              checked={tarefa.concluida} 
            />
            {tarefa.titulo}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 🔄 Ordenando Itens

```jsx
function ListaOrdenavel({ itens }) {
  const [criterioOrdenacao, setCriterioOrdenacao] = useState('nome');
  const [direcao, setDirecao] = useState('asc'); // 'asc' ou 'desc'
  
  const itensCopia = [...itens];
  
  itensCopia.sort((a, b) => {
    const fatorDirecao = direcao === 'asc' ? 1 : -1;
    
    if (a[criterioOrdenacao] < b[criterioOrdenacao]) return -1 * fatorDirecao;
    if (a[criterioOrdenacao] > b[criterioOrdenacao]) return 1 * fatorDirecao;
    return 0;
  });
  
  const alternarDirecao = () => {
    setDirecao(direcao === 'asc' ? 'desc' : 'asc');
  };
  
  return (
    <div>
      <div>
        <button onClick={() => setCriterioOrdenacao('nome')}>
          Ordenar por Nome
        </button>
        <button onClick={() => setCriterioOrdenacao('preco')}>
          Ordenar por Preço
        </button>
        <button onClick={alternarDirecao}>
          {direcao === 'asc' ? '↑ Crescente' : '↓ Decrescente'}
        </button>
      </div>
      
      <ul>
        {itensCopia.map(item => (
          <li key={item.id}>
            {item.nome} - R$ {item.preco.toFixed(2)}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 🔍 Buscando em Listas

```jsx
function ListaComBusca({ itens }) {
  const [termoBusca, setTermoBusca] = useState('');
  
  const itensFiltrados = itens.filter(item =>
    item.nome.toLowerCase().includes(termoBusca.toLowerCase())
  );
  
  return (
    <div>
      <input
        type="text"
        placeholder="Buscar..."
        value={termoBusca}
        onChange={(e) => setTermoBusca(e.target.value)}
      />
      
      <ul>
        {itensFiltrados.map(item => (
          <li key={item.id}>{item.nome}</li>
        ))}
      </ul>
      
      {itensFiltrados.length === 0 && (
        <p>Nenhum resultado encontrado para "{termoBusca}"</p>
      )}
    </div>
  );
}
```

## 🚀 Técnicas Avançadas

### 🔄 Lista com Adição e Remoção de Itens

```jsx
function ListaEditavel() {
  const [itens, setItens] = useState([
    { id: 1, texto: 'Item 1' },
    { id: 2, texto: 'Item 2' },
    { id: 3, texto: 'Item 3' },
  ]);
  
  const [novoItem, setNovoItem] = useState('');
  
  const adicionarItem = () => {
    if (!novoItem.trim()) return;
    
    const novoId = Math.max(0, ...itens.map(item => item.id)) + 1;
    setItens([...itens, { id: novoId, texto: novoItem }]);
    setNovoItem('');
  };
  
  const removerItem = (id) => {
    setItens(itens.filter(item => item.id !== id));
  };
  
  return (
    <div>
      <div>
        <input
          type="text"
          value={novoItem}
          onChange={(e) => setNovoItem(e.target.value)}
          placeholder="Novo item..."
        />
        <button onClick={adicionarItem}>Adicionar</button>
      </div>
      
      <ul>
        {itens.map(item => (
          <li key={item.id}>
            {item.texto}
            <button onClick={() => removerItem(item.id)}>Remover</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 📋 Lista com Edição Inline

```jsx
function ListaComEdicao() {
  const [itens, setItens] = useState([
    { id: 1, texto: 'Aprender React', editando: false },
    { id: 2, texto: 'Criar uma aplicação', editando: false },
    { id: 3, texto: 'Compartilhar com amigos', editando: false },
  ]);
  
  const alternarEdicao = (id) => {
    setItens(itens.map(item => 
      item.id === id ? { ...item, editando: !item.editando } : item
    ));
  };
  
  const atualizarTexto = (id, novoTexto) => {
    setItens(itens.map(item =>
      item.id === id ? { ...item, texto: novoTexto } : item
    ));
  };
  
  const salvarEdicao = (id) => {
    setItens(itens.map(item =>
      item.id === id ? { ...item, editando: false } : item
    ));
  };
  
  return (
    <ul>
      {itens.map(item => (
        <li key={item.id}>
          {item.editando ? (
            <>
              <input
                type="text"
                value={item.texto}
                onChange={(e) => atualizarTexto(item.id, e.target.value)}
              />
              <button onClick={() => salvarEdicao(item.id)}>Salvar</button>
            </>
          ) : (
            <>
              {item.texto}
              <button onClick={() => alternarEdicao(item.id)}>Editar</button>
            </>
          )}
        </li>
      ))}
    </ul>
  );
}
```

### 🖱️ Lista com Drag and Drop

```jsx
// Com a biblioteca react-beautiful-dnd
import { DragDropContext, Droppable, Draggable } from 'react-beautiful-dnd';

function ListaArrastavel() {
  const [itens, setItens] = useState([
    { id: 'item-1', conteudo: 'Item 1' },
    { id: 'item-2', conteudo: 'Item 2' },
    { id: 'item-3', conteudo: 'Item 3' },
  ]);
  
  const reordenarItens = (resultado) => {
    // Descarta se não houver destino ou se soltar no mesmo lugar
    if (!resultado.destination || 
        resultado.destination.index === resultado.source.index) {
      return;
    }
    
    const itensReordenados = Array.from(itens);
    const [itemRemovido] = itensReordenados.splice(resultado.source.index, 1);
    itensReordenados.splice(resultado.destination.index, 0, itemRemovido);
    
    setItens(itensReordenados);
  };
  
  return (
    <DragDropContext onDragEnd={reordenarItens}>
      <Droppable droppableId="lista">
        {(provided) => (
          <ul
            {...provided.droppableProps}
            ref={provided.innerRef}
          >
            {itens.map((item, index) => (
              <Draggable key={item.id} draggableId={item.id} index={index}>
                {(provided) => (
                  <li
                    ref={provided.innerRef}
                    {...provided.draggableProps}
                    {...provided.dragHandleProps}
                  >
                    {item.conteudo}
                  </li>
                )}
              </Draggable>
            ))}
            {provided.placeholder}
          </ul>
        )}
      </Droppable>
    </DragDropContext>
  );
}
```

## 🛠️ Dicas e Melhores Práticas

### 🚫 Evitar Trabalhar com Índices como Chaves

```jsx
// ❌ Problemático em listas dinâmicas
{minhaLista.map((item, index) => (
  <ListItem key={index} {...item} />
))}

// ✅ Melhor - use IDs estáveis
{minhaLista.map(item => (
  <ListItem key={item.id} {...item} />
))}
```

### 🏗️ Evite Criar Componentes dentro do Map

```jsx
// ❌ Ruim: cria uma nova função a cada renderização
{itens.map(item => (
  <li key={item.id}>
    {item.nome}
    <button onClick={() => {
      // Lógica complexa aqui
    }}>
      Clique
    </button>
  </li>
))}

// ✅ Melhor: extraia para um componente separado
function ItemLista({ item, onClick }) {
  return (
    <li>
      {item.nome}
      <button onClick={onClick}>Clique</button>
    </li>
  );
}

// No componente principal
{itens.map(item => (
  <ItemLista 
    key={item.id} 
    item={item} 
    onClick={() => handleClick(item.id)} 
  />
))}
```

### 🧠 Use Memoização para Listas Grandes

```jsx
import React, { useMemo } from 'react';

function ListaGrande({ itens, filtro }) {
  // A lista processada só é recalculada quando itens ou filtro mudam
  const itensFiltrados = useMemo(() => {
    console.log('Calculando itens filtrados');
    return itens.filter(item => item.nome.includes(filtro));
  }, [itens, filtro]);
  
  return (
    <ul>
      {itensFiltrados.map(item => (
        <li key={item.id}>{item.nome}</li>
      ))}
    </ul>
  );
}
```

### 🔄 Virtualização para Listas Muito Grandes

Para listas com milhares de itens, use bibliotecas de virtualização como `react-virtualized` ou `react-window`:

```jsx
import { FixedSizeList } from 'react-window';

function ListaVirtualizada({ itens }) {
  const renderItem = ({ index, style }) => (
    <div style={style}>
      Item {itens[index].id}: {itens[index].nome}
    </div>
  );
  
  return (
    <FixedSizeList
      height={400}
      width={300}
      itemCount={itens.length}
      itemSize={35}
    >
      {renderItem}
    </FixedSizeList>
  );
}
```

## 🐞 Depurando Problemas Comuns

### 🔄 Problema: Componentes não Re-renderizam Quando Esperado

Se você modificar o array diretamente, React pode não detectar a mudança:

```jsx
// ❌ Modificação direta: React não detecta
function addItem() {
  itens.push({ id: 123, nome: 'Novo Item' }); // Modifica o array original
  setItens(itens); // React não detecta mudança, referência é a mesma
}

// ✅ Correto: criar um novo array
function addItem() {
  setItens([...itens, { id: 123, nome: 'Novo Item' }]); // Nova referência
}
```

### 🗝️ Problema: Warning de Chaves Duplicadas

```jsx
// ❌ Pode gerar chaves duplicadas
const pessoas = [
  { nome: "João" },
  { nome: "Maria" },
  { nome: "João" } // Mesmo nome
];

{pessoas.map(pessoa => (
  <li key={pessoa.nome}>{pessoa.nome}</li> // Chave duplicada!
))}

// ✅ Solução: Use IDs únicos ou adicione um índice junto com outro valor
{pessoas.map((pessoa, index) => (
  <li key={`${pessoa.nome}-${index}`}>{pessoa.nome}</li>
))}
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 