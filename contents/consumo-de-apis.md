<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# Consumo de APIs no React

Conectar aplicações React a APIs web é essencial para criar aplicações dinâmicas que consomem e manipulam dados de servidores externos. Esta seção explora diferentes abordagens para integrar APIs em suas aplicações React.

## O que são APIs e por que usá-las?

**API** (Interface de Programação de Aplicações) é um conjunto de regras que permite que diferentes aplicações se comuniquem entre si.

### Benefícios do Uso de APIs:

- **Separação de responsabilidades**: Frontend e backend desacoplados
- **Reutilização de dados**: Mesma API pode servir múltiplas interfaces
- **Escalabilidade**: Facilita o crescimento independente de cada parte da aplicação
- **Integração**: Permite conectar-se a serviços de terceiros

## Métodos para Consumir APIs

### Fetch API (Nativa)

A Fetch API é nativa do JavaScript moderno e oferece uma maneira simples de fazer requisições HTTP:

```jsx
import React, { useState, useEffect } from 'react';

function ListaUsuarios() {
  const [usuarios, setUsuarios] = useState([]);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  useEffect(() => {
    // Usando Fetch API
    fetch('https://api.exemplo.com/usuarios')
      .then(resposta => {
        // Verifica se a resposta é ok (status 200-299)
        if (!resposta.ok) {
          throw new Error(`Erro HTTP: ${resposta.status}`);
        }
        return resposta.json();
      })
      .then(dados => {
        setUsuarios(dados);
        setCarregando(false);
      })
      .catch(erro => {
        setErro(erro.message);
        setCarregando(false);
      });
  }, []);
  
  if (carregando) return <p>Carregando...</p>;
  if (erro) return <p>Erro: {erro}</p>;
  
  return (
    <ul>
      {usuarios.map(usuario => (
        <li key={usuario.id}>{usuario.nome}</li>
      ))}
    </ul>
  );
}
```

### Fetch com Async/Await

Uma abordagem mais limpa usando `async/await`:

```jsx
import React, { useState, useEffect } from 'react';

function ListaProdutos() {
  const [produtos, setProdutos] = useState([]);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  useEffect(() => {
    // Função assíncrona auto-executável
    (async () => {
      try {
        setCarregando(true);
        const resposta = await fetch('https://api.exemplo.com/produtos');
        
        if (!resposta.ok) {
          throw new Error(`Erro HTTP: ${resposta.status}`);
        }
        
        const dados = await resposta.json();
        setProdutos(dados);
        setErro(null);
      } catch (erro) {
        setErro(erro.message);
        setProdutos([]);
      } finally {
        setCarregando(false);
      }
    })();
  }, []);
  
  // Restante do componente...
}
```

### Axios (Biblioteca Popular)

Axios é uma biblioteca que facilita requisições HTTP e oferece uma API mais amigável:

```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function DetalhesProduto({ id }) {
  const [produto, setProduto] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  useEffect(() => {
    // Configurando instância do Axios
    const api = axios.create({
      baseURL: 'https://api.exemplo.com',
      timeout: 5000,
      headers: {
        'Content-Type': 'application/json'
      }
    });
    
    const buscarProduto = async () => {
      try {
        setCarregando(true);
        const resposta = await api.get(`/produtos/${id}`);
        setProduto(resposta.data);
        setErro(null);
      } catch (erro) {
        setErro(
          erro.response ? `Erro: ${erro.response.status}` : erro.message
        );
        setProduto(null);
      } finally {
        setCarregando(false);
      }
    };
    
    buscarProduto();
  }, [id]);
  
  if (carregando) return <p>Carregando detalhes do produto...</p>;
  if (erro) return <p>Erro: {erro}</p>;
  if (!produto) return <p>Produto não encontrado</p>;
  
  return (
    <div>
      <h2>{produto.nome}</h2>
      <p>{produto.descricao}</p>
      <p>Preço: R$ {produto.preco.toFixed(2)}</p>
    </div>
  );
}
```

## Criando um Hook Personalizado para Requisições

Crie hooks reutilizáveis para diferentes necessidades de requisições:

```jsx
import { useState, useEffect } from 'react';

// Hook personalizado para requisições fetch
function useFetch(url, opcoes = {}) {
  const [dados, setDados] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  const [shouldRefetch, setRefetch] = useState(0);
  
  // Função para forçar uma nova requisição
  const refetch = () => setRefetch(count => count + 1);
  
  useEffect(() => {
    let isMounted = true;
    const controller = new AbortController();
    const { signal } = controller;
    
    const fetchDados = async () => {
      setCarregando(true);
      
      try {
        const resposta = await fetch(url, {
          ...opcoes,
          signal
        });
        
        if (!resposta.ok) {
          throw new Error(`Erro ${resposta.status}: ${resposta.statusText}`);
        }
        
        const resultado = await resposta.json();
        
        if (isMounted) {
          setDados(resultado);
          setErro(null);
        }
      } catch (error) {
        if (error.name !== 'AbortError' && isMounted) {
          setErro(error.message);
          setDados(null);
        }
      } finally {
        if (isMounted) {
          setCarregando(false);
        }
      }
    };
    
    fetchDados();
    
    // Função de limpeza para evitar memory leaks
    return () => {
      isMounted = false;
      controller.abort();
    };
  }, [url, shouldRefetch]);
  
  return { dados, carregando, erro, refetch };
}

// Exemplo de uso
function App() {
  const { dados, carregando, erro, refetch } = useFetch(
    'https://api.exemplo.com/produtos'
  );
  
  return (
    <div>
      <button onClick={refetch}>Atualizar Dados</button>
      
      {carregando && <p>Carregando...</p>}
      {erro && <p>Erro: {erro}</p>}
      {dados && (
        <ul>
          {dados.map(item => (
            <li key={item.id}>{item.nome}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## Estados da Requisição

É importante gerenciar adequadamente os diferentes estados de uma requisição:

```jsx
import React, { useState } from 'react';
import axios from 'axios';

function FormularioEnvio() {
  const [formData, setFormData] = useState({
    nome: '',
    email: '',
    mensagem: ''
  });
  
  const [status, setStatus] = useState({
    enviando: false,
    enviado: false,
    erro: null
  });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    setStatus({ enviando: true, enviado: false, erro: null });
    
    try {
      await axios.post('https://api.exemplo.com/contato', formData);
      
      setStatus({ enviando: false, enviado: true, erro: null });
      // Limpa o formulário após envio bem-sucedido
      setFormData({ nome: '', email: '', mensagem: '' });
      
      // Limpa a mensagem de sucesso após 3 segundos
      setTimeout(() => {
        setStatus(prev => ({ ...prev, enviado: false }));
      }, 3000);
      
    } catch (error) {
      setStatus({
        enviando: false,
        enviado: false,
        erro: error.response?.data?.message || 'Erro ao enviar mensagem'
      });
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Campos do formulário */}
      
      <button 
        type="submit" 
        disabled={status.enviando}
      >
        {status.enviando ? 'Enviando...' : 'Enviar'}
      </button>
      
      {status.enviado && (
        <div className="mensagem-sucesso">
          Mensagem enviada com sucesso!
        </div>
      )}
      
      {status.erro && (
        <div className="mensagem-erro">
          {status.erro}
        </div>
      )}
    </form>
  );
}
```

## Autenticação e Headers

Muitas APIs requerem autenticação. Veja como implementar:

```jsx
import React, { useState, useEffect, createContext, useContext } from 'react';
import axios from 'axios';

// Contexto para gerenciar autenticação
const AuthContext = createContext();

// Provedor de autenticação
function AuthProvider({ children }) {
  const [token, setToken] = useState(localStorage.getItem('authToken'));
  const [usuario, setUsuario] = useState(null);
  
  // Instância do axios com interceptors
  const api = axios.create({
    baseURL: 'https://api.exemplo.com'
  });
  
  // Adiciona token a todas as requisições
  api.interceptors.request.use(config => {
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  });
  
  // Funções de autenticação
  const login = async (email, senha) => {
    const resposta = await api.post('/login', { email, senha });
    const { token, usuario } = resposta.data;
    
    localStorage.setItem('authToken', token);
    setToken(token);
    setUsuario(usuario);
    
    return usuario;
  };
  
  const logout = () => {
    localStorage.removeItem('authToken');
    setToken(null);
    setUsuario(null);
  };
  
  // Carrega dados do usuário quando tiver token
  useEffect(() => {
    const carregarUsuario = async () => {
      if (token) {
        try {
          const resposta = await api.get('/usuario');
          setUsuario(resposta.data);
        } catch (erro) {
          console.error("Erro ao carregar usuário:", erro);
          logout();
        }
      }
    };
    
    carregarUsuario();
  }, [token]);
  
  return (
    <AuthContext.Provider value={{ 
      token, 
      usuario, 
      login, 
      logout,
      api, // Expõe a instância configurada do axios
      autenticado: !!usuario
    }}>
      {children}
    </AuthContext.Provider>
  );
}

// Hook para usar a autenticação
function useAuth() {
  return useContext(AuthContext);
}

// Componente que usa autenticação
function PerfilUsuario() {
  const { usuario, logout, api } = useAuth();
  const [dadosPerfil, setDadosPerfil] = useState(null);
  const [carregando, setCarregando] = useState(true);
  
  useEffect(() => {
    const buscarPerfil = async () => {
      try {
        const resposta = await api.get('/perfil');
        setDadosPerfil(resposta.data);
      } catch (erro) {
        console.error("Erro ao buscar perfil:", erro);
      } finally {
        setCarregando(false);
      }
    };
    
    buscarPerfil();
  }, [api]);
  
  if (!usuario) return <p>Você precisa estar logado</p>;
  if (carregando) return <p>Carregando perfil...</p>;
  
  return (
    <div>
      <h2>Bem-vindo, {usuario.nome}!</h2>
      {dadosPerfil && (
        <div>
          <p>Email: {dadosPerfil.email}</p>
          <p>Último acesso: {new Date(dadosPerfil.ultimoAcesso).toLocaleString()}</p>
        </div>
      )}
      <button onClick={logout}>Sair</button>
    </div>
  );
}
```

## Manipulando Diferentes Tipos de Requisição

### GET: Buscar Dados

```jsx
// Buscar uma lista de itens
const buscarProdutos = async () => {
  const resposta = await api.get('/produtos');
  return resposta.data;
};

// Buscar um item específico
const buscarProduto = async (id) => {
  const resposta = await api.get(`/produtos/${id}`);
  return resposta.data;
};

// Buscar com parâmetros de consulta
const buscarProdutosFiltrados = async (categoria, ordenacao) => {
  const resposta = await api.get('/produtos', {
    params: {
      categoria,
      ordenar_por: ordenacao
    }
  });
  return resposta.data;
};
```

### POST: Criar Dados

```jsx
// Criar um novo recurso
const criarProduto = async (dadosProduto) => {
  const resposta = await api.post('/produtos', dadosProduto);
  return resposta.data;
};

// Upload de arquivo
const uploadImagem = async (arquivo) => {
  const formData = new FormData();
  formData.append('imagem', arquivo);
  
  const resposta = await api.post('/upload', formData, {
    headers: {
      'Content-Type': 'multipart/form-data'
    }
  });
  
  return resposta.data.url;
};
```

### PUT/PATCH: Atualizar Dados

```jsx
// Atualização completa (PUT)
const atualizarProduto = async (id, dadosProduto) => {
  const resposta = await api.put(`/produtos/${id}`, dadosProduto);
  return resposta.data;
};

// Atualização parcial (PATCH)
const atualizarParcialmente = async (id, campos) => {
  const resposta = await api.patch(`/produtos/${id}`, campos);
  return resposta.data;
};
```

### DELETE: Remover Dados

```jsx
// Excluir um recurso
const excluirProduto = async (id) => {
  await api.delete(`/produtos/${id}`);
  return true;
};
```

## Lidando com Respostas da API

### ✅ Tratamento de Sucesso

```jsx
try {
  const dados = await api.get('/produtos');
  
  // Verificar e processar os dados
  if (Array.isArray(dados.data) && dados.data.length > 0) {
    setProdutos(dados.data);
  } else {
    // Lista vazia
    setProdutos([]);
    setMensagem('Nenhum produto encontrado');
  }
} catch (erro) {
  // Tratamento de erro...
}
```

### ❌ Tratamento de Erros

```jsx
try {
  await api.post('/usuarios', dadosUsuario);
} catch (erro) {
  if (erro.response) {
    // Resposta do servidor com código de erro
    const { status, data } = erro.response;
    
    switch (status) {
      case 400:
        setErro('Dados inválidos. Verifique os campos obrigatórios.');
        // Manipular erros de validação específicos
        if (data.errors) {
          setErrosForm(data.errors);
        }
        break;
      case 401:
        setErro('Não autorizado. Faça login novamente.');
        // Possivelmente redirecionar para login
        break;
      case 403:
        setErro('Sem permissão para realizar esta ação.');
        break;
      case 404:
        setErro('Recurso não encontrado.');
        break;
      case 409:
        setErro('Conflito: ' + (data.message || 'Recurso já existe'));
        break;
      case 500:
        setErro('Erro no servidor. Tente novamente mais tarde.');
        break;
      default:
        setErro(`Erro ${status}: ${data.message || 'Ocorreu um erro'}`);
    }
  } else if (erro.request) {
    // Requisição feita mas sem resposta
    setErro('Sem resposta do servidor. Verifique sua conexão.');
  } else {
    // Erro ao configurar a requisição
    setErro('Erro ao configurar requisição: ' + erro.message);
  }
}
```

## Estratégias de Manipulação de API

### Centralização de Serviços

Organize seu código isolando lógica de API em serviços:

```jsx
// src/services/api.js
import axios from 'axios';

const baseURL = process.env.REACT_APP_API_URL || 'https://api.exemplo.com';

const api = axios.create({ baseURL });

// Interceptor para adicionar token
api.interceptors.request.use(config => {
  const token = localStorage.getItem('authToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default api;

// src/services/produtosService.js
import api from './api';

export const ProdutosService = {
  listar: async () => {
    const resposta = await api.get('/produtos');
    return resposta.data;
  },
  
  buscarPorId: async (id) => {
    const resposta = await api.get(`/produtos/${id}`);
    return resposta.data;
  },
  
  criar: async (produto) => {
    const resposta = await api.post('/produtos', produto);
    return resposta.data;
  },
  
  atualizar: async (id, produto) => {
    const resposta = await api.put(`/produtos/${id}`, produto);
    return resposta.data;
  },
  
  excluir: async (id) => {
    await api.delete(`/produtos/${id}`);
    return true;
  }
};
```

### Uso de Hooks Personalizados

Encapsule a lógica de API em hooks personalizados para reuso:

```jsx
// src/hooks/useProdutos.js
import { useState, useEffect, useCallback } from 'react';
import { ProdutosService } from '../services/produtosService';

export function useProdutos() {
  const [produtos, setProdutos] = useState([]);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  const buscarProdutos = useCallback(async () => {
    try {
      setCarregando(true);
      const dados = await ProdutosService.listar();
      setProdutos(dados);
      setErro(null);
    } catch (error) {
      setErro('Falha ao carregar produtos: ' + error.message);
      setProdutos([]);
    } finally {
      setCarregando(false);
    }
  }, []);
  
  useEffect(() => {
    buscarProdutos();
  }, [buscarProdutos]);
  
  const adicionarProduto = async (novoProduto) => {
    try {
      const produtoCriado = await ProdutosService.criar(novoProduto);
      setProdutos(prev => [...prev, produtoCriado]);
      return produtoCriado;
    } catch (error) {
      setErro('Erro ao adicionar produto: ' + error.message);
      throw error;
    }
  };
  
  return { 
    produtos, 
    carregando, 
    erro, 
    buscarProdutos, 
    adicionarProduto 
  };
}
```

## Gerenciamento de Cache e Otimização

### Implementação de Cache Básico

```jsx
// Hook simples com cache
function useAPIComCache(url) {
  const [dados, setDados] = useState(null);
  const [carregando, setCarregando] = useState(true);
  const [erro, setErro] = useState(null);
  
  // Cache local
  const cacheRef = useRef({});
  
  useEffect(() => {
    let isMounted = true;
    
    const fetchDados = async () => {
      // Verifica se temos dados em cache
      if (cacheRef.current[url]) {
        setDados(cacheRef.current[url]);
        setCarregando(false);
        return;
      }
      
      setCarregando(true);
      
      try {
        const resposta = await fetch(url);
        
        if (!resposta.ok) {
          throw new Error(`Erro ${resposta.status}`);
        }
        
        const novosDados = await resposta.json();
        
        if (isMounted) {
          // Atualiza o estado e o cache
          setDados(novosDados);
          cacheRef.current[url] = novosDados;
          setErro(null);
        }
      } catch (error) {
        if (isMounted) {
          setErro(error.message);
        }
      } finally {
        if (isMounted) {
          setCarregando(false);
        }
      }
    };
    
    fetchDados();
    
    return () => {
      isMounted = false;
    };
  }, [url]);
  
  return { dados, carregando, erro };
}
```

### Bibliotecas de Gerenciamento de Cache

Para projetos maiores, considere bibliotecas especializadas:

```jsx
// Exemplo com React Query
import { useQuery, useMutation, useQueryClient } from 'react-query';
import { ProdutosService } from '../services/produtosService';

function ProdutosPage() {
  const queryClient = useQueryClient();
  
  // Consulta para buscar produtos
  const { 
    data: produtos, 
    isLoading, 
    error 
  } = useQuery('produtos', ProdutosService.listar);
  
  // Mutação para adicionar produto
  const { mutate: adicionarProduto } = useMutation(
    ProdutosService.criar,
    {
      // Atualiza o cache após sucesso
      onSuccess: (novoProduto) => {
        queryClient.setQueryData('produtos', (dados) => {
          return [...dados, novoProduto];
        });
      }
    }
  );
  
  if (isLoading) return <p>Carregando...</p>;
  if (error) return <p>Erro: {error.message}</p>;
  
  return (
    <div>
      <h1>Produtos</h1>
      <ul>
        {produtos.map(produto => (
          <li key={produto.id}>{produto.nome}</li>
        ))}
      </ul>
      <button onClick={() => adicionarProduto({ 
        nome: "Novo Produto", 
        preco: 99.99 
      })}>
        Adicionar Produto
      </button>
    </div>
  );
}
```

## Testando Código que Consome API

Usando Jest e Testing Library para testar componentes que consomem APIs:

```jsx
// __tests__/components/ListaProdutos.test.js
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import ListaProdutos from '../../components/ListaProdutos';
import { ProdutosService } from '../../services/produtosService';

// Mock do serviço
jest.mock('../../services/produtosService');

describe('ListaProdutos', () => {
  test('renderiza lista de produtos após carregamento', async () => {
    // Define o retorno do mock
    ProdutosService.listar.mockResolvedValue([
      { id: 1, nome: 'Produto 1' },
      { id: 2, nome: 'Produto 2' }
    ]);
    
    render(<ListaProdutos />);
    
    // Estado inicial de carregamento
    expect(screen.getByText('Carregando...')).toBeInTheDocument();
    
    // Esperar o conteúdo aparecer após carregamento
    await waitFor(() => {
      expect(screen.getByText('Produto 1')).toBeInTheDocument();
      expect(screen.getByText('Produto 2')).toBeInTheDocument();
    });
  });
  
  test('renderiza mensagem de erro quando falha', async () => {
    // Define o retorno do mock como rejeição
    ProdutosService.listar.mockRejectedValue(
      new Error('Falha ao carregar')
    );
    
    render(<ListaProdutos />);
    
    // Esperar a mensagem de erro aparecer
    await waitFor(() => {
      expect(screen.getByText(/falha ao carregar/i)).toBeInTheDocument();
    });
  });
});
```

## Dicas e Melhores Práticas

1. **Centralize a Lógica de API**: Use serviços ou hooks personalizados para isolar chamadas de API
2. **Implemente Tratamento de Erros Robusto**: Lide com diferentes cenários de erro com graciosidade
3. **Considere Estados da UI**: Sempre forneça feedback para estados de carregamento e erro
4. **Cancele Requisições Pendentes**: Use AbortController para cancelar requisições ao desmontar componentes
5. **Use Cache Quando Apropriado**: Evite requisições desnecessárias cacheando resultados
6. **Considere o Rate Limiting**: Implemente throttling ou debounce para evitar enviar muitas requisições
7. **Mantenha Dados Sensíveis Seguros**: Nunca exponha tokens ou credenciais no código do cliente
8. **Considere Bibliotecas Especializadas**: Para projetos maiores, avalie React Query, SWR ou Redux Toolkit Query

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>