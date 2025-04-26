<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 📋 Renderização Condicional no React 🔀

A renderização condicional permite mostrar ou ocultar elementos com base em condições, tornando suas interfaces dinâmicas e adaptáveis. Esta seção explora várias técnicas para implementar renderização condicional em seus componentes React. 🚀

## 📝 O que é Renderização Condicional?

Renderização condicional significa exibir diferentes elementos ou componentes dependendo de condições específicas. Isso é fundamental para construir UIs interativas que respondem ao estado da aplicação e às ações do usuário.

## 🔄 Métodos de Renderização Condicional

### 1️⃣ Usando Declarações `if`

A maneira mais simples é usar uma declaração `if` para determinar o que retornar:

```jsx
function MensagemBoasVindas({ estaLogado }) {
  if (estaLogado) {
    return <h1>Bem-vindo de volta!</h1>;
  }
  return <h1>Por favor, faça login.</h1>;
}
```

### 2️⃣ Operador Ternário

Para condições simples dentro do JSX, o operador ternário é a escolha ideal:

```jsx
function MensagemStatus({ estaOnline }) {
  return (
    <div>
      <h1>O usuário está {estaOnline ? 'online' : 'offline'}</h1>
      <span className={estaOnline ? 'status-online' : 'status-offline'} />
    </div>
  );
}
```

### 3️⃣ Operador Lógico AND (`&&`)

Útil quando você só precisa renderizar algo quando a condição for verdadeira:

```jsx
function ListaDeNotificacoes({ mensagens }) {
  return (
    <div>
      <h1>Mensagens Recentes</h1>
      {mensagens.length > 0 && (
        <div>
          Você tem {mensagens.length} mensagem(ns) não lida(s).
        </div>
      )}
    </div>
  );
}
```

⚠️ **Cuidado**: Tenha cuidado com valores numéricos ao usar `&&`. Se `mensagens.length` for `0`, React renderizará `0` em vez de nada.

```jsx
{mensagens.length && <p>Tem mensagens</p>} {/* Renderiza "0" se length for 0 */}
{mensagens.length > 0 && <p>Tem mensagens</p>} {/* Melhor abordagem */}
```

### 4️⃣ Operador Lógico OR (`||`)

Útil para fornecer valores padrão:

```jsx
function SaudacaoUsuario({ nome }) {
  return <h1>Olá, {nome || 'Visitante'}!</h1>;
}
```

### 5️⃣ Atribuição com Variáveis

Para lógica condicional mais complexa, use variáveis:

```jsx
function StatusPedido({ status }) {
  let mensagem;
  let corStatus;
  
  if (status === 'confirmado') {
    mensagem = 'Seu pedido foi confirmado.';
    corStatus = 'green';
  } else if (status === 'processando') {
    mensagem = 'Seu pedido está sendo processado.';
    corStatus = 'orange';
  } else if (status === 'enviado') {
    mensagem = 'Seu pedido foi enviado!';
    corStatus = 'blue';
  } else {
    mensagem = 'Status do pedido desconhecido.';
    corStatus = 'gray';
  }
  
  return (
    <div style={{ color: corStatus }}>
      <h2>Status do Pedido</h2>
      <p>{mensagem}</p>
    </div>
  );
}
```

### 6️⃣ IIFE (Expressão de Função Imediatamente Invocada)

Para lógica condicional complexa dentro do JSX:

```jsx
function ItemProduto({ produto }) {
  return (
    <div className="produto">
      <h3>{produto.nome}</h3>
      <p>R$ {produto.preco.toFixed(2)}</p>
      
      {(() => {
        if (produto.estoque === 0) {
          return <span className="esgotado">Esgotado</span>;
        } else if (produto.estoque < 5) {
          return <span className="pouco-estoque">Últimas unidades</span>;
        } else {
          return <span className="em-estoque">Disponível</span>;
        }
      })()}
    </div>
  );
}
```

⚠️ **Nota**: Use esta abordagem com moderação, pois pode prejudicar a legibilidade.

### 7️⃣ Objetos de Mapeamento

Útil para várias condições sem muitos `if/else`:

```jsx
function Mensagem({ tipo }) {
  const mensagens = {
    sucesso: <div className="sucesso">Operação concluída com sucesso!</div>,
    erro: <div className="erro">Ocorreu um erro. Tente novamente.</div>,
    aviso: <div className="aviso">Atenção: esta ação não pode ser desfeita.</div>,
    info: <div className="info">O sistema será atualizado em breve.</div>
  };
  
  return mensagens[tipo] || <div>Mensagem desconhecida</div>;
}
```

## 🎯 Casos de Uso Comuns

### 🔒 Renderização de Conteúdo Baseada em Permissões

```jsx
function PainelAdmin({ usuario }) {
  if (!usuario) {
    return <p>Carregando...</p>;
  }
  
  if (!usuario.permissoes.inclui('admin')) {
    return <p>Acesso negado. Você não tem permissões de administrador.</p>;
  }
  
  return (
    <div>
      <h1>Painel de Administração</h1>
      <ConfiguracoesAvancadas />
      <GerenciamentoUsuarios />
    </div>
  );
}
```

### 🔄 Estados de Carregamento

```jsx
function CarregamentoDados({ carregando, erro, dados }) {
  if (carregando) {
    return (
      <div className="loading">
        <Spinner />
        <p>Carregando dados...</p>
      </div>
    );
  }
  
  if (erro) {
    return (
      <div className="erro">
        <p>Erro ao carregar dados: {erro.mensagem}</p>
        <button onClick={tentarNovamente}>Tentar Novamente</button>
      </div>
    );
  }
  
  if (!dados || dados.length === 0) {
    return <p>Nenhum dado encontrado.</p>;
  }
  
  return (
    <div className="resultados">
      {dados.map(item => <ItemDado key={item.id} item={item} />)}
    </div>
  );
}
```

### 📱 Renderização Responsiva

```jsx
function ResponsiveLayout({ isMobile }) {
  return (
    <div className="layout">
      <Header />
      {isMobile ? <MobileNavigation /> : <DesktopNavigation />}
      <main>
        {isMobile ? (
          <CompactView />
        ) : (
          <>
            <Sidebar />
            <Content />
          </>
        )}
      </main>
      <Footer />
    </div>
  );
}
```

### 📝 Formulários Dinâmicos

```jsx
function FormularioPagamento({ metodoPagamento }) {
  return (
    <form>
      <h2>Informações de Pagamento</h2>
      
      <select
        value={metodoPagamento}
        onChange={e => setMetodoPagamento(e.target.value)}
      >
        <option value="cartao">Cartão de Crédito</option>
        <option value="boleto">Boleto Bancário</option>
        <option value="pix">PIX</option>
      </select>
      
      {metodoPagamento === 'cartao' && (
        <div className="campos-cartao">
          <input type="text" placeholder="Número do cartão" />
          <input type="text" placeholder="Nome no cartão" />
          <div className="linha">
            <input type="text" placeholder="Validade" />
            <input type="text" placeholder="CVV" />
          </div>
        </div>
      )}
      
      {metodoPagamento === 'boleto' && (
        <div className="campos-boleto">
          <p>O boleto será gerado após a confirmação do pedido.</p>
        </div>
      )}
      
      {metodoPagamento === 'pix' && (
        <div className="campos-pix">
          <p>Um QR Code PIX será gerado para pagamento imediato.</p>
        </div>
      )}
      
      <button type="submit">Finalizar Pagamento</button>
    </form>
  );
}
```

## 🔍 Técnicas Avançadas

### 🎭 Componentes de Alto Nível Condicionais

```jsx
// Componente de alto nível condicional
function RequireAuth({ usuario, children }) {
  if (!usuario) {
    return <Navigate to="/login" />;
  }
  
  return children;
}

// Uso
function App() {
  const usuario = useAuth();
  
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/registro" element={<Registro />} />
      <Route 
        path="/perfil" 
        element={
          <RequireAuth usuario={usuario}>
            <PerfilUsuario usuario={usuario} />
          </RequireAuth>
        } 
      />
    </Routes>
  );
}
```

### 🎮 Renderizando Dinamicamente Componentes

```jsx
function Pagina({ secoes }) {
  // Mapeamento de tipos de seção para componentes
  const componentesPorTipo = {
    banner: BannerPrincipal,
    cards: GridDeCards,
    formulario: FormularioContato,
    texto: BlocoDeTexto,
    video: PlayerDeVideo
  };
  
  return (
    <div className="pagina">
      {secoes.map((secao, index) => {
        const Componente = componentesPorTipo[secao.tipo];
        
        // Verifique se o componente existe para o tipo
        if (!Componente) {
          console.warn(`Tipo de seção desconhecido: ${secao.tipo}`);
          return null;
        }
        
        return <Componente key={index} {...secao.props} />;
      })}
    </div>
  );
}
```

### 🧩 Renderização por Rotas Condicionais

```jsx
import { Routes, Route, Navigate } from 'react-router-dom';

function AppRoutes({ isAuthenticated }) {
  return (
    <Routes>
      <Route path="/login" element={
        isAuthenticated 
          ? <Navigate to="/dashboard" replace /> 
          : <Login />
      } />
      
      <Route path="/dashboard" element={
        isAuthenticated 
          ? <Dashboard /> 
          : <Navigate to="/login" replace />
      } />
      
      <Route path="/admin" element={
        isAuthenticated && isAdmin
          ? <AdminPanel />
          : <AcessoNegado />
      } />
      
      <Route path="/" element={<Home />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

## 🛑 Pitfalls e Soluções Comuns

### ⚠️ Usando Números e Valores Falsy

Cuidado com valores falsy em renderização condicional:

```jsx
// ❌ Problema: Renderiza "0" se count for 0
{count && <p>Contador: {count}</p>}

// ✅ Solução: Use comparações explícitas
{count > 0 && <p>Contador: {count}</p>}

// Ou use o operador ternário
{count ? <p>Contador: {count}</p> : <p>Contador vazio</p>}
```

### ⚠️ Renderização Lenta com Muitos Condicionais

Se você tem muitas condições aninhadas, sua renderização pode ficar confusa:

```jsx
// ❌ Difícil de ler e manter
function ComponenteComplicado({ a, b, c, d, e }) {
  return (
    <div>
      {a ? (
        b ? (
          c ? <ComponenteA /> : <ComponenteB />
        ) : (
          d ? <ComponenteC /> : <ComponenteD />
        )
      ) : (
        e ? <ComponenteE /> : <ComponenteF />
      )}
    </div>
  );
}

// ✅ Melhor: Use funções auxiliares ou variáveis
function ComponenteSimplificado({ a, b, c, d, e }) {
  let componente;
  
  if (a) {
    if (b) {
      componente = c ? <ComponenteA /> : <ComponenteB />;
    } else {
      componente = d ? <ComponenteC /> : <ComponenteD />;
    }
  } else {
    componente = e ? <ComponenteE /> : <ComponenteF />;
  }
  
  return <div>{componente}</div>;
}
```

### ⚠️ Erros com Condições de Curto-Circuito

```jsx
// ❌ Problema: Se obj for null/undefined, isso causará um erro
{obj && obj.propriedade && <p>{obj.propriedade}</p>}

// ✅ Solução: Use operador de encadeamento opcional (?.), introduzido no ES2020
{obj?.propriedade && <p>{obj.propriedade}</p>}
```

## 🌟 Dicas e Melhores Práticas

### 🎯 Mantenha a Lógica Simples

A lógica condicional deve ser clara e fácil de entender:

```jsx
// ❌ Complicado
{isA && isB && !isC && isD ? <ComponenteA /> : null}

// ✅ Mais claro
{podeExibirComponenteA() && <ComponenteA />}

// Função auxiliar em outro lugar
function podeExibirComponenteA() {
  return isA && isB && !isC && isD;
}
```

### 🔄 Extraia Componentes Condicionais

Se a lógica condicional se torna complexa, extraia-a em componentes separados:

```jsx
// ✅ Componente específico para cada estado
function EstadoPedido({ status, pedido }) {
  if (status === 'aguardando') return <PedidoAguardando pedido={pedido} />;
  if (status === 'enviado') return <PedidoEnviado pedido={pedido} />;
  if (status === 'entregue') return <PedidoEntregue pedido={pedido} />;
  return <PedidoDesconhecido />;
}
```

### 🧩 Use Componentes como Props para Casos Complexos

```jsx
function Layout({ 
  header, 
  sidebar, 
  content, 
  footer,
  showSidebar = true
}) {
  return (
    <div className="layout">
      <header>{header}</header>
      <div className="main">
        {showSidebar && <aside>{sidebar}</aside>}
        <main>{content}</main>
      </div>
      <footer>{footer}</footer>
    </div>
  );
}

// Uso
<Layout
  header={<Header />}
  sidebar={<Sidebar />}
  content={<Content />}
  footer={<Footer />}
  showSidebar={!isMobile}
/>
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>