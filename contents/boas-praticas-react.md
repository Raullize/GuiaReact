<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 🏆 Boas Práticas no React 📋

O desenvolvimento com React oferece grande flexibilidade, mas seguir boas práticas é essencial para criar aplicações sustentáveis, escaláveis e fáceis de manter. Esta seção apresenta diretrizes, padrões e estruturas recomendadas para projetos React. 🚀

## 🧠 Princípios Fundamentais

### 📦 Componentização Eficiente

- 🧩 **Componentes pequenos e focados**: Cada componente deve ter uma única responsabilidade
- 🔄 **Componentes reutilizáveis**: Projete pensando em reuso
- 🏗️ **Composição sobre herança**: Construa componentes complexos combinando componentes simples
- 🧱 **Separe lógica de apresentação**: Use padrões como Container/Presentational ou hooks personalizados

```jsx
// ❌ Componente muito grande e com múltiplas responsabilidades
function UserDashboard() {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);
  
  // Lógica de busca de usuário
  // Lógica de busca de posts
  // Lógica de ordenação de posts
  // Lógica de filtragem de posts
  // UI com muitos elementos
  
  return (/* 200+ linhas de JSX */);
}

// ✅ Componentes menores e composição
function UserDashboard() {
  return (
    <DashboardLayout>
      <UserProfile userId={123} />
      <PostsList userId={123} />
    </DashboardLayout>
  );
}
```

### 🔍 Estado e Props

- 🧬 **Estado mínimo**: Mantenha apenas o essencial no estado
- 📊 **Estado único**: Evite duplicação de estado
- ⚡ **Elevação de estado**: Coloque estado no ancestral comum mais próximo
- 🔒 **Props imutáveis**: Nunca modifique props recebidas
- 📝 **Props descritivas**: Use nomes claros e descritivos
- 🎯 **Valores padrão**: Forneça valores padrão para props opcionais

```jsx
// ❌ Props mal nomeadas
<Button x={true} y="click me" z={() => console.log("clicked")} />

// ✅ Props descritivas
<Button 
  isPrimary={true} 
  text="Click me" 
  onClick={() => console.log("clicked")} 
/>

// ✅ Valores padrão com defaultProps ou desestruturação
function Button({ isPrimary = false, text = "Button", onClick }) {
  // ...
}
```

### 🧹 Limpeza e Organização

- 📁 **Estrutura de arquivos lógica**: Organize por recurso ou tipo
- 🧬 **Separação de responsabilidades**: Cada arquivo deve ter um propósito claro
- 📏 **Consistência de estilo**: Use ferramentas como ESLint e Prettier
- 📚 **Documentação**: Comente código complexo e use JSDoc para APIs públicas
- 🏷️ **Nomeação clara**: Nomes descritivos para componentes, funções e variáveis

```jsx
// ❌ Nome genérico e pouco descritivo
function Comp() { /* ... */ }

// ✅ Nome descritivo que explica o propósito
function ProductRecommendationCard() { /* ... */ }
```

## 🏗️ Estrutura de Projeto

Uma estrutura bem organizada facilita a navegação, manutenção e escalabilidade:

### 📂 Estrutura de Diretórios Baseada em Recursos

```
src/
├── assets/              # Arquivos estáticos (imagens, fontes, etc.)
├── components/          # Componentes compartilhados
│   ├── Button/
│   │   ├── Button.jsx
│   │   ├── Button.test.jsx
│   │   ├── Button.module.css
│   │   └── index.js
│   └── ...
├── hooks/               # Hooks personalizados
├── contexts/            # Contextos React
├── services/            # Serviços e APIs
├── utils/               # Funções utilitárias
├── pages/               # Componentes de página/rotas
│   ├── Home/
│   ├── Products/
│   └── ...
├── features/            # Módulos específicos de recursos
│   ├── auth/            # Feature de autenticação
│   │   ├── components/  # Componentes específicos da feature
│   │   ├── hooks/       # Hooks específicos da feature
│   │   ├── services/    # Serviços específicos da feature
│   │   ├── utils/       # Utilitários específicos da feature
│   │   └── index.js     # API pública da feature
│   └── ...
├── store/               # Gerenciamento de estado global
├── styles/              # Estilos globais
├── App.jsx              # Componente raiz
└── index.jsx            # Ponto de entrada
```

### 📄 Estrutura de Arquivos de Componentes

Organize componentes com arquivos relacionados:

```
Button/
├── Button.jsx           # Implementação do componente
├── Button.test.jsx      # Testes
├── Button.module.css    # Estilos (ou styled-components)
├── Button.stories.jsx   # Histórias do Storybook (opcional)
└── index.js             # Exportação pública
```

O arquivo `index.js` serve como API pública:

```jsx
// Button/index.js
export { default } from './Button';
export * from './Button';
```

Isso permite importações limpas:

```jsx
import Button from 'components/Button';
```

## 🔄 Padrões de Componentes

### 🧩 Componentes de Função vs. Classe

Prefira componentes de função com hooks:

```jsx
// ✅ Componente de função moderno com hooks
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    async function fetchUser() {
      setLoading(true);
      try {
        const data = await userService.getUser(userId);
        setUser(data);
      } catch (error) {
        console.error(error);
      } finally {
        setLoading(false);
      }
    }
    
    fetchUser();
  }, [userId]);
  
  if (loading) return <Spinner />;
  if (!user) return <NotFound />;
  
  return (
    <div className="profile">
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
    </div>
  );
}
```

### 🧠 Padrão Container/Presentational

Separe lógica de apresentação:

```jsx
// Componente container (lógica)
function UserProfileContainer({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    // Lógica de busca de dados
    // ...
  }, [userId]);
  
  return (
    <UserProfilePresentation 
      user={user}
      loading={loading}
    />
  );
}

// Componente de apresentação (UI)
function UserProfilePresentation({ user, loading }) {
  if (loading) return <Spinner />;
  if (!user) return <NotFound />;
  
  return (
    <div className="profile">
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
    </div>
  );
}
```

### 🧰 Hooks Personalizados

Extraia lógica reutilizável para hooks:

```jsx
// Hook personalizado
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let isMounted = true;
    
    async function fetchUser() {
      setLoading(true);
      try {
        const data = await userService.getUser(userId);
        if (isMounted) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (isMounted) {
          setError(err);
          setUser(null);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    }
    
    fetchUser();
    
    return () => {
      isMounted = false;
    };
  }, [userId]);
  
  return { user, loading, error };
}

// Uso do hook
function UserProfile({ userId }) {
  const { user, loading, error } = useUser(userId);
  
  if (loading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!user) return <NotFound />;
  
  return (
    <div className="profile">
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
    </div>
  );
}
```

### 🧮 Padrão Render Props

Compartilhe lógica entre componentes:

```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    function handleMouseMove(event) {
      setPosition({ x: event.clientX, y: event.clientY });
    }
    
    window.addEventListener('mousemove', handleMouseMove);
    
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []);
  
  return render(position);
}

// Uso
<MouseTracker 
  render={({ x, y }) => (
    <div>Cursor position: {x}, {y}</div>
  )}
/>
```

### 🧱 Composição com Children

Use `children` para criar componentes flexíveis:

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <div className="card-header">
        <h2>{title}</h2>
      </div>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
}

// Uso
<Card title="Perfil de Usuário">
  <UserAvatar src={user.avatar} />
  <UserDetails name={user.name} email={user.email} />
  <UserStats posts={user.posts} followers={user.followers} />
</Card>
```

## 🔄 Gerenciamento de Estado

### 🧠 Escolhendo a Abordagem Certa

- **useState**: Para estado local simples
- **useReducer**: Para estado local mais complexo ou com lógica de atualização
- **Context API**: Para estado compartilhado entre componentes próximos
- **Redux/Zustand/Jotai/etc**: Para estado global em aplicações maiores

### 📱 Estado Local vs. Global

Prefira estado local sempre que possível:

```jsx
// ❌ Usando estado global para algo local
// redux/slices/counterSlice.js
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1 },
    decrement: (state) => { state.value -= 1 }
  }
});

// ✅ Usando estado local para algo local
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
    </div>
  );
}
```

### 🧬 Fragmentação de Estado

Divida o estado em pedaços lógicos:

```jsx
// ❌ Estado monolítico
function UserForm() {
  const [formState, setFormState] = useState({
    name: '',
    email: '',
    isSubmitting: false,
    errors: {},
    isValidated: false,
    submissionCount: 0
  });
  
  // Atualizações complexas e propensas a erros
  // ...
}

// ✅ Estado fragmentado
function UserForm() {
  const [values, setValues] = useState({ name: '', email: '' });
  const [errors, setErrors] = useState({});
  const [status, setStatus] = useState({
    isSubmitting: false,
    isValidated: false
  });
  
  // Atualizações mais simples e isoladas
  // ...
}
```

## 📱 Organização de Código

### 🧠 Ordem Lógica em Componentes

Mantenha uma estrutura consistente:

```jsx
function MyComponent({ propA, propB }) {
  // 1. Hooks (useState, useEffect, etc.)
  const [state, setState] = useState(initialState);
  
  // 2. Cálculos e variáveis derivadas
  const derivedValue = useMemo(() => {
    return complexCalculation(state, propA);
  }, [state, propA]);
  
  // 3. Manipuladores de eventos
  const handleClick = useCallback(() => {
    setState(newState);
  }, [setState]);
  
  // 4. Efeitos secundários
  useEffect(() => {
    // Efeito colateral
  }, [dependencies]);
  
  // 5. Renderização condicional
  if (loading) return <Spinner />;
  
  // 6. Renderização principal
  return (
    <div>
      {/* JSX */}
    </div>
  );
}
```

### 🔄 Desestruturação e Espalhamento

Use desestruturação para código mais limpo:

```jsx
// ❌ Sem desestruturação
function UserCard(props) {
  return (
    <div>
      <h2>{props.user.name}</h2>
      <p>{props.user.email}</p>
      <Avatar src={props.user.avatar} size={props.size} />
    </div>
  );
}

// ✅ Com desestruturação
function UserCard({ user, size }) {
  const { name, email, avatar } = user;
  
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
      <Avatar src={avatar} size={size} />
    </div>
  );
}
```

Use o operador spread com cautela:

```jsx
// ❌ Spread de props descontrolado
function Button(props) {
  // Qual props estão sendo passadas para o button?
  return <button {...props} />;
}

// ✅ Spread controlado com rest
function Button({ className, ...rest }) {
  return <button className={`btn ${className}`} {...rest} />;
}
```

### 📂 Importações Organizadas

Organize suas importações por categoria:

```jsx
// Bibliotecas externas
import React, { useState, useEffect } from 'react';
import PropTypes from 'prop-types';
import classNames from 'classnames';

// Componentes
import { Card, Avatar } from 'components';

// Hooks e utilitários
import { useUser } from 'hooks';
import { formatDate } from 'utils';

// Estilos
import styles from './UserProfile.module.css';
```

## 🔍 Performance

### 🧮 Memoização Eficiente

Use `React.memo`, `useMemo` e `useCallback` para prevenir renderizações desnecessárias:

```jsx
// Memoizar um componente
const MemoizedComponent = React.memo(function MyComponent({ value, onClick }) {
  // ...
});

// Memoizar um valor calculado
function ProductList({ products, filter }) {
  // Recalcula apenas quando products ou filter mudam
  const filteredProducts = useMemo(() => {
    return products.filter(product => 
      product.category === filter
    );
  }, [products, filter]);
  
  // ...
}

// Memoizar uma função para referenciar igualdade
function UserActions({ userId }) {
  // Recria função apenas quando userId muda
  const handleDeleteUser = useCallback(() => {
    deleteUser(userId);
  }, [userId]);
  
  return <Button onClick={handleDeleteUser}>Deletar</Button>;
}
```

### 🔎 Virtualização para Listas Grandes

Use bibliotecas como `react-window` ou `react-virtualized` para listas longas:

```jsx
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      <div>{items[index].name}</div>
    </div>
  );
  
  return (
    <FixedSizeList
      height={500}
      width={300}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </FixedSizeList>
  );
}
```

### 🚀 Code Splitting

Divida seu código com `React.lazy` e `Suspense`:

```jsx
import React, { Suspense, lazy } from 'react';

// Carregamento sob demanda
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

## 🧪 Testabilidade

### 📝 Código Testável

Escreva código fácil de testar:

```jsx
// ❌ Difícil de testar: efeitos colaterais embutidos
function UserStatus({ userId }) {
  const [isOnline, setIsOnline] = useState(false);
  
  useEffect(() => {
    // Efeito colateral direto no componente
    const checkStatus = async () => {
      const status = await fetchUserStatus(userId);
      setIsOnline(status.online);
    };
    
    checkStatus();
  }, [userId]);
  
  return <div>{isOnline ? '🟢 Online' : '🔴 Offline'}</div>;
}

// ✅ Mais testável: lógica extraída para hook
function useUserStatus(userId) {
  const [isOnline, setIsOnline] = useState(false);
  
  useEffect(() => {
    let isMounted = true;
    
    const checkStatus = async () => {
      const status = await fetchUserStatus(userId);
      if (isMounted) {
        setIsOnline(status.online);
      }
    };
    
    checkStatus();
    
    return () => {
      isMounted = false;
    };
  }, [userId]);
  
  return isOnline;
}

function UserStatus({ userId }) {
  const isOnline = useUserStatus(userId);
  
  return <div>{isOnline ? '🟢 Online' : '🔴 Offline'}</div>;
}
```

### 🧰 Test-Driven Development (TDD)

Considere escrever testes antes da implementação:

```jsx
// 1. Escrever teste primeiro
test('mostra status do usuário baseado na resposta da API', async () => {
  // Mock da API
  fetchUserStatus.mockResolvedValue({ online: true });
  
  render(<UserStatus userId="123" />);
  
  // Verifica estado inicial
  expect(screen.getByText('Loading...')).toBeInTheDocument();
  
  // Verifica estado após carregamento
  await waitFor(() => {
    expect(screen.getByText('🟢 Online')).toBeInTheDocument();
  });
});

// 2. Implementar componente para passar no teste
function UserStatus({ userId }) {
  const [isOnline, setIsOnline] = useState(null);
  
  useEffect(() => {
    // ...implementação...
  }, [userId]);
  
  if (isOnline === null) return <div>Loading...</div>;
  
  return <div>{isOnline ? '🟢 Online' : '🔴 Offline'}</div>;
}
```

## 🧩 Exemplo de Aplicação Completa

Uma demonstração de projeto estruturado com boas práticas:

```jsx
// src/features/auth/hooks/useAuth.js
import { useState, useEffect } from 'react';
import { authService } from '../services/authService';

export function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const checkAuth = async () => {
      try {
        const userData = await authService.getCurrentUser();
        setUser(userData);
      } catch (error) {
        setUser(null);
      } finally {
        setLoading(false);
      }
    };
    
    checkAuth();
  }, []);
  
  const login = async (credentials) => {
    setLoading(true);
    try {
      const userData = await authService.login(credentials);
      setUser(userData);
      return { success: true };
    } catch (error) {
      return { success: false, error };
    } finally {
      setLoading(false);
    }
  };
  
  const logout = async () => {
    try {
      await authService.logout();
      setUser(null);
    } catch (error) {
      console.error('Logout error:', error);
    }
  };
  
  return {
    user,
    loading,
    login,
    logout,
    isAuthenticated: !!user
  };
}

// src/features/auth/context/AuthContext.js
import React, { createContext, useContext } from 'react';
import { useAuth } from '../hooks/useAuth';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const auth = useAuth();
  
  return (
    <AuthContext.Provider value={auth}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuthContext() {
  const context = useContext(AuthContext);
  
  if (!context) {
    throw new Error('useAuthContext must be used within an AuthProvider');
  }
  
  return context;
}

// src/features/auth/components/LoginForm.jsx
import React, { useState } from 'react';
import { useAuthContext } from '../context/AuthContext';
import { Button, TextField, Alert } from 'components';
import styles from './LoginForm.module.css';

export function LoginForm() {
  const { login } = useAuthContext();
  const [credentials, setCredentials] = useState({ email: '', password: '' });
  const [error, setError] = useState(null);
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setCredentials(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setError(null);
    setIsSubmitting(true);
    
    const result = await login(credentials);
    
    if (!result.success) {
      setError(result.error.message || 'Falha ao fazer login');
    }
    
    setIsSubmitting(false);
  };
  
  return (
    <form className={styles.form} onSubmit={handleSubmit}>
      <h2>Login</h2>
      
      {error && <Alert type="error">{error}</Alert>}
      
      <TextField
        label="Email"
        type="email"
        name="email"
        value={credentials.email}
        onChange={handleChange}
        required
      />
      
      <TextField
        label="Senha"
        type="password"
        name="password"
        value={credentials.password}
        onChange={handleChange}
        required
      />
      
      <Button 
        type="submit" 
        isPrimary 
        isLoading={isSubmitting}
        disabled={isSubmitting}
      >
        {isSubmitting ? 'Entrando...' : 'Entrar'}
      </Button>
    </form>
  );
}

// src/App.jsx
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { AuthProvider } from 'features/auth/context/AuthContext';
import { ThemeProvider } from 'contexts/ThemeContext';
import { ProtectedRoute } from 'components/ProtectedRoute';
import { 
  HomePage, 
  LoginPage, 
  DashboardPage, 
  SettingsPage,
  NotFoundPage 
} from 'pages';

export default function App() {
  return (
    <ThemeProvider>
      <AuthProvider>
        <BrowserRouter>
          <Routes>
            <Route path="/" element={<HomePage />} />
            <Route path="/login" element={<LoginPage />} />
            <Route 
              path="/dashboard" 
              element={
                <ProtectedRoute>
                  <DashboardPage />
                </ProtectedRoute>
              } 
            />
            <Route 
              path="/settings" 
              element={
                <ProtectedRoute>
                  <SettingsPage />
                </ProtectedRoute>
              } 
            />
            <Route path="*" element={<NotFoundPage />} />
          </Routes>
        </BrowserRouter>
      </AuthProvider>
    </ThemeProvider>
  );
}
```

## 🔍 Depuração e Solução de Problemas

### 🐛 DevTools e Ferramentas

- **React DevTools**: Inspecione componentes, props, estado e performance
- **Redux DevTools**: Monitore ações e estado quando usar Redux
- **React Error Boundaries**: Capture e trate erros em componentes

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error("Erro capturado:", error, errorInfo);
    // Opcional: enviar erro para serviço de monitoramento
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-container">
          <h2>Algo deu errado 😢</h2>
          <button onClick={() => window.location.reload()}>
            Tentar novamente
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Uso
<ErrorBoundary>
  <MeuComponente />
</ErrorBoundary>
```

### 🔍 Logging e Depuração

- Use `console.log` estrategicamente (remova em produção)
- Implemente registros estruturados para depuração avançada
- Configure breakpoints no DevTools do navegador

## 🌟 Dicas Finais

1. **Mantenha-se atualizado**: O ecossistema React evolui rapidamente
2. **Evite otimização prematura**: Primeiro faça funcionar, depois otimize
3. **Documente decisões arquiteturais**: Explique "por quê", não apenas "como"
4. **Busque feedback**: Revisões de código são valiosas
5. **Siga princípios SOLID**: Especialmente responsabilidade única e inversão de dependência
6. **Use TypeScript**: Adiciona segurança de tipos e melhora a manutenção
7. **Automatize tarefas repetitivas**: Use scripts e ferramentas para ganhar produtividade
8. **Pratique refatoração contínua**: Melhore o código incrementalmente

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 