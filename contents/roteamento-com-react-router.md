<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 🧭 Roteamento com React Router 🗺️

O React Router é uma biblioteca de roteamento para aplicações React que permite criar experiências de navegação dinâmicas, semelhantes às de aplicações multi-página, mas mantendo a fluidez de uma Single Page Application (SPA). Esta seção explora como implementar navegação usando React Router. 🚀

## 🤔 Por que Usar um Roteador?

O React, por si só, não inclui funcionalidades de roteamento. Para criar aplicações com múltiplas "páginas" (views), precisamos de uma solução de roteamento:

- 🔀 **Navegação Sem Recarregar**: Mudança entre views sem recarregar completamente a página
- 📚 **Organização de Código**: Separar a aplicação em componentes baseados em rotas
- 🔄 **URLs Compartilháveis**: Permitir acessar diretamente qualquer view da aplicação
- 🏠 **Navegação Natural**: Funcionalidades como voltar/avançar do navegador
- 🧩 **Carregamento Dinâmico**: Carregar código sob demanda (lazy loading)

## 🛠️ Instalação e Configuração

Primeiro, instale o React Router:

```bash
# Para aplicações web
npm install react-router-dom

# Para React Native
npm install react-router-native
```

## 🚀 Roteamento Básico

### Configuração de Rotas

```jsx
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Componentes de página
import Inicio from './paginas/Inicio';
import Sobre from './paginas/Sobre';
import Contato from './paginas/Contato';
import NotFound from './paginas/NotFound';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/sobre" element={<Sobre />} />
        <Route path="/contato" element={<Contato />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### 🧭 Navegação Entre Rotas

```jsx
import { Link, NavLink } from 'react-router-dom';

function Navegacao() {
  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Início</Link>
        </li>
        <li>
          <Link to="/sobre">Sobre</Link>
        </li>
        <li>
          {/* NavLink adiciona classe 'active' quando a rota está ativa */}
          <NavLink 
            to="/contato"
            className={({ isActive }) => isActive ? 'link-ativo' : ''}
          >
            Contato
          </NavLink>
        </li>
      </ul>
    </nav>
  );
}
```

## 📊 Rotas Aninhadas e Layouts

O React Router permite organizar suas rotas em hierarquias, ideal para layouts compartilhados:

```jsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Inicio />} />
          <Route path="blog" element={<Blog />}>
            <Route index element={<PostsRecentes />} />
            <Route path=":postId" element={<PostDetalhe />} />
          </Route>
          <Route path="sobre" element={<Sobre />} />
          <Route path="contato" element={<Contato />} />
          <Route path="*" element={<NotFound />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

function Layout() {
  return (
    <div>
      <Header />
      <nav>
        <Link to="/">Início</Link>
        <Link to="/blog">Blog</Link>
        <Link to="/sobre">Sobre</Link>
        <Link to="/contato">Contato</Link>
      </nav>
      
      {/* O componente Outlet renderiza a rota filha ativa */}
      <Outlet />
      
      <Footer />
    </div>
  );
}
```

## 📝 Parâmetros de Rota

### Parâmetros de URL

```jsx
// Na definição da rota
<Route path="/usuarios/:id" element={<PerfilUsuario />} />

// No componente PerfilUsuario
import { useParams } from 'react-router-dom';

function PerfilUsuario() {
  // Acessa o parâmetro 'id' da URL
  const { id } = useParams();
  
  return (
    <div>
      <h1>Perfil do Usuário</h1>
      <p>ID do usuário: {id}</p>
      {/* Buscar e mostrar dados do usuário */}
    </div>
  );
}
```

### Parâmetros de Consulta (Query Parameters)

```jsx
// Na URL: /produtos?categoria=eletronicos&ordenar=preco
import { useSearchParams } from 'react-router-dom';

function ListaProdutos() {
  const [searchParams, setSearchParams] = useSearchParams();
  
  const categoria = searchParams.get('categoria') || 'todos';
  const ordenar = searchParams.get('ordenar') || 'nome';
  
  // Alterando os parâmetros da URL
  const mudarOrdenacao = (novoOrdenar) => {
    setSearchParams({ 
      categoria, 
      ordenar: novoOrdenar 
    });
  };
  
  return (
    <div>
      <h1>Produtos - Categoria: {categoria}</h1>
      <select 
        value={ordenar} 
        onChange={(e) => mudarOrdenacao(e.target.value)}
      >
        <option value="nome">Nome</option>
        <option value="preco">Preço</option>
        <option value="avaliacao">Avaliação</option>
      </select>
      
      {/* Lista de produtos filtrada e ordenada */}
    </div>
  );
}
```

## 🔄 Navegação Programática

Além de links, podemos navegar programaticamente:

```jsx
import { useNavigate } from 'react-router-dom';

function Autenticacao() {
  const navigate = useNavigate();
  
  const fazerLogin = async (credenciais) => {
    try {
      await authService.login(credenciais);
      // Redireciona para o dashboard após login bem-sucedido
      navigate('/dashboard');
    } catch (erro) {
      console.error('Erro de login:', erro);
      // Permanece na mesma página em caso de erro
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Campos de login */}
      <button type="submit">Entrar</button>
      <button type="button" onClick={() => navigate(-1)}>
        Voltar
      </button>
    </form>
  );
}
```

## 🔒 Rotas Protegidas

Você pode criar rotas protegidas que verificam a autenticação:

```jsx
function RotaProtegida({ children }) {
  const { usuarioAutenticado } = useAuth();
  const navigate = useNavigate();
  
  useEffect(() => {
    if (!usuarioAutenticado) {
      // Redireciona para login se não estiver autenticado
      navigate('/login', { 
        replace: true,
        state: { from: location.pathname }
      });
    }
  }, [usuarioAutenticado, navigate]);
  
  // Só renderiza o conteúdo se o usuário estiver autenticado
  return usuarioAutenticado ? children : null;
}

// Uso
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/login" element={<Login />} />
        <Route 
          path="/dashboard" 
          element={
            <RotaProtegida>
              <Dashboard />
            </RotaProtegida>
          } 
        />
      </Routes>
    </BrowserRouter>
  );
}
```

## 🔄 Redirecionamentos

```jsx
import { Navigate } from 'react-router-dom';

// Redirecionamento simples
<Route path="/home" element={<Navigate to="/" replace />} />

// Redirecionamento condicional em um componente
function PaginaLegada() {
  return <Navigate to="/nova-pagina" replace />;
}
```

## 📱 Estrutura de Projeto Recomendada

Uma boa organização para aplicações com React Router:

```
src/
  ├── components/        # Componentes reutilizáveis
  ├── pages/             # Componentes de página (um por rota)
  │   ├── Home.js
  │   ├── About.js
  │   ├── Contact.js
  │   └── NotFound.js
  ├── layouts/           # Layouts compartilhados
  │   ├── MainLayout.js
  │   └── DashboardLayout.js
  ├── routes/            # Configuração de rotas
  │   ├── index.js       # Configuração principal
  │   ├── PrivateRoute.js # Componente de rota protegida
  │   └── routes.js      # Definições de rotas (opcional)
  ├── App.js             # Componente raiz
  └── index.js           # Ponto de entrada
```

## 🌟 React Router v6 - Novas Funcionalidades

A versão 6 do React Router trouxe mudanças significativas:

### 🧩 Componente Routes

```jsx
// React Router v5
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
</Switch>

// React Router v6
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
</Routes>
```

### 📊 Rotas Aninhadas Simplificadas

```jsx
// React Router v6
<Routes>
  <Route path="/" element={<Layout />}>
    <Route index element={<Home />} />
    <Route path="about" element={<About />} />
    <Route path="*" element={<NotFound />} />
  </Route>
</Routes>
```

### 🎯 No-Match Routes

```jsx
// Captura qualquer rota não correspondida
<Route path="*" element={<NotFound />} />
```

## 📑 Exemplo Completo de Aplicação

Aqui está um exemplo mais completo de uma aplicação React com React Router:

```jsx
// src/App.js
import React from 'react';
import { BrowserRouter, Routes, Route, Navigate, useLocation } from 'react-router-dom';
import { AuthProvider, useAuth } from './contexts/AuthContext';
import MainLayout from './layouts/MainLayout';
import DashboardLayout from './layouts/DashboardLayout';
import Home from './pages/Home';
import About from './pages/About';
import Login from './pages/Login';
import Dashboard from './pages/Dashboard';
import Profile from './pages/Profile';
import Settings from './pages/Settings';
import NotFound from './pages/NotFound';

function RequireAuth({ children }) {
  const { user } = useAuth();
  const location = useLocation();
  
  if (!user) {
    // Redireciona para o login, mas salva a localização atual
    return <Navigate to="/login" state={{ from: location }} replace />;
  }
  
  return children;
}

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          {/* Rotas públicas com MainLayout */}
          <Route path="/" element={<MainLayout />}>
            <Route index element={<Home />} />
            <Route path="about" element={<About />} />
            <Route path="login" element={<Login />} />
          </Route>
          
          {/* Rotas protegidas com DashboardLayout */}
          <Route 
            path="/dashboard" 
            element={
              <RequireAuth>
                <DashboardLayout />
              </RequireAuth>
            }
          >
            <Route index element={<Dashboard />} />
            <Route path="profile" element={<Profile />} />
            <Route path="settings" element={<Settings />} />
          </Route>
          
          {/* Rota 404 */}
          <Route path="*" element={<NotFound />} />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}

export default App;

// src/layouts/MainLayout.js
import { Outlet, Link } from 'react-router-dom';

function MainLayout() {
  return (
    <div>
      <header>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
          <Link to="/login">Login</Link>
        </nav>
      </header>
      
      <main>
        <Outlet />
      </main>
      
      <footer>
        <p>© 2023 Minha Aplicação React</p>
      </footer>
    </div>
  );
}
```

## 🎯 Dicas e Melhores Práticas

1. **Use Lazy Loading**: Carregue componentes sob demanda para melhorar o tempo de carregamento inicial

```jsx
import React, { Suspense, lazy } from 'react';

// Importação dinâmica de componentes
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Carregando...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

2. **Organize Rotas em Módulos**: Para aplicações maiores, separe rotas por recurso/módulo
3. **Use URL Bem Estruturadas**: Crie URLs legíveis e hierárquicas (ex: `/produtos/categorias/eletronicos`)
4. **Mantenha o Estado da Navegação**: Use `state` nos links para passar informações durante a navegação
5. **Use Base URL para Ambientes de Desenvolvimento/Produção**: Configure a base URL com `basename`

```jsx
<BrowserRouter basename="/app">
  {/* Rotas aqui */}
</BrowserRouter>
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 