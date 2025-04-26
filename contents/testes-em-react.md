<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 🔧 Testes em React 🧪

Testar uma aplicação React é fundamental para garantir qualidade, confiabilidade e facilitar a manutenção do código. Esta seção explora diferentes tipos de testes, ferramentas e estratégias para testar componentes e aplicações React. 🚀

## 🤔 Por que Testar?

Testar suas aplicações React traz diversos benefícios:

- 🐛 **Prevenção de bugs**: Detecte problemas antes que cheguem à produção
- 🔄 **Refatoração segura**: Modifique código com confiança, sabendo que os testes alertarão sobre regressões
- 📝 **Documentação viva**: Testes revelam como o código deve funcionar
- ⚡ **Processo de desenvolvimento mais ágil**: Evite ciclos longos de debugging manual
- 🧠 **Design de código melhor**: Código testável geralmente tem melhor arquitetura

## 🧪 Tipos de Testes

### 🔍 Testes Unitários

Testam componentes ou funções individuais de forma isolada:

```jsx
// Componente a ser testado
function Saudacao({ nome }) {
  return <h1>Olá, {nome}!</h1>;
}

// Teste unitário
import { render, screen } from '@testing-library/react';
import Saudacao from './Saudacao';

test('renderiza a saudação com o nome correto', () => {
  render(<Saudacao nome="Maria" />);
  const elementoSaudacao = screen.getByText(/Olá, Maria!/i);
  expect(elementoSaudacao).toBeInTheDocument();
});
```

### 🧩 Testes de Integração

Verificam a interação entre múltiplos componentes ou módulos:

```jsx
// Componentes
function Contador() {
  const [contador, setContador] = useState(0);
  
  return (
    <div>
      <p data-testid="valor">{contador}</p>
      <button onClick={() => setContador(contador + 1)}>
        Incrementar
      </button>
    </div>
  );
}

// Teste de integração
import { render, screen, fireEvent } from '@testing-library/react';
import Contador from './Contador';

test('incrementa o contador quando o botão é clicado', () => {
  render(<Contador />);
  
  // Estado inicial
  expect(screen.getByTestId('valor')).toHaveTextContent('0');
  
  // Simula clique no botão
  fireEvent.click(screen.getByText('Incrementar'));
  
  // Verifica se o estado foi atualizado
  expect(screen.getByTestId('valor')).toHaveTextContent('1');
});
```

### 🖥️ Testes End-to-End (E2E)

Simulam o uso real da aplicação pelo usuário:

```javascript
// Usando Cypress
describe('App E2E', () => {
  it('permite ao usuário fazer login', () => {
    cy.visit('/');
    
    cy.get('[data-testid="email-input"]').type('usuario@exemplo.com');
    cy.get('[data-testid="senha-input"]').type('senha123');
    cy.get('[data-testid="login-button"]').click();
    
    // Verifica se o usuário foi redirecionado para o dashboard
    cy.url().should('include', '/dashboard');
    cy.get('[data-testid="boas-vindas"]').should('contain', 'Bem-vindo');
  });
});
```

## 🛠️ Ferramentas de Teste

### 🧰 Jest

Jest é um framework de teste desenvolvido pelo Facebook, amplamente utilizado em projetos React:

- 🔄 **Test runner**: Executa testes e relata resultados
- ✅ **Assertions**: Verificações como `expect(value).toBe(expected)`
- 🧩 **Mocks, spies e stubs**: Ferramentas para simular dependências
- 📊 **Cobertura de código**: Medição de quanto código é testado

```jsx
// Exemplo de teste com Jest
test('soma dois números', () => {
  expect(1 + 2).toBe(3);
});

// Testando funcionalidade assíncrona
test('busca dados do usuário', async () => {
  const dados = await fetchUsuario(1);
  expect(dados.nome).toBe('João');
});

// Usando mocks
jest.mock('../api');
import { fetchUsuario } from '../api';

test('manipula erro na API', async () => {
  fetchUsuario.mockRejectedValue(new Error('Falha na rede'));
  
  await expect(buscarPerfil(1)).rejects.toThrow('Falha na rede');
});
```

### 🧪 React Testing Library

Biblioteca que incentiva boas práticas de teste, focando na experiência do usuário:

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Formulario from './Formulario';

test('submete o formulário com os valores corretos', () => {
  // Mock da função de submissão
  const handleSubmit = jest.fn();
  
  render(<Formulario onSubmit={handleSubmit} />);
  
  // Preenche os campos
  fireEvent.change(screen.getByLabelText(/nome/i), {
    target: { value: 'Maria Silva' }
  });
  
  fireEvent.change(screen.getByLabelText(/email/i), {
    target: { value: 'maria@exemplo.com' }
  });
  
  // Clica no botão de enviar
  fireEvent.click(screen.getByText(/enviar/i));
  
  // Verifica se a função foi chamada com os valores corretos
  expect(handleSubmit).toHaveBeenCalledWith({
    nome: 'Maria Silva',
    email: 'maria@exemplo.com'
  });
});
```

### 🎭 Enzyme

Uma alternativa à Testing Library que permite testar a implementação interna:

```jsx
import { shallow, mount } from 'enzyme';
import Contador from './Contador';

test('renderiza com o valor inicial correto', () => {
  const wrapper = shallow(<Contador inicial={5} />);
  expect(wrapper.find('.valor').text()).toBe('5');
});

test('incrementa quando o botão é clicado', () => {
  const wrapper = mount(<Contador />);
  wrapper.find('button.incrementar').simulate('click');
  expect(wrapper.find('.valor').text()).toBe('1');
});
```

### 🌐 Cypress

Ferramenta para testes end-to-end que permite testar a aplicação completa:

```javascript
// cypress/integration/login.spec.js
describe('Login', () => {
  beforeEach(() => {
    cy.visit('/login');
  });
  
  it('exibe mensagem de erro com credenciais inválidas', () => {
    cy.get('input[name="email"]').type('usuario@exemplo.com');
    cy.get('input[name="senha"]').type('senhaerrada');
    cy.get('button[type="submit"]').click();
    
    cy.get('.mensagem-erro').should('be.visible')
      .and('contain', 'Credenciais inválidas');
  });
  
  it('redireciona para dashboard após login bem-sucedido', () => {
    cy.get('input[name="email"]').type('usuario@exemplo.com');
    cy.get('input[name="senha"]').type('senha123');
    cy.get('button[type="submit"]').click();
    
    cy.url().should('include', '/dashboard');
  });
});
```

## 📋 Estratégias de Teste

### 🧩 Testando Componentes de UI

Para componentes de apresentação:

```jsx
import { render, screen } from '@testing-library/react';
import BotaoPrimario from './BotaoPrimario';

test('renderiza botão com o texto correto', () => {
  render(<BotaoPrimario>Clique Aqui</BotaoPrimario>);
  expect(screen.getByRole('button')).toHaveTextContent('Clique Aqui');
});

test('aplica classe de desabilitado quando a prop é passada', () => {
  render(<BotaoPrimario disabled>Clique Aqui</BotaoPrimario>);
  expect(screen.getByRole('button')).toBeDisabled();
  expect(screen.getByRole('button')).toHaveClass('desabilitado');
});

test('chama a função onClick quando clicado', () => {
  const handleClick = jest.fn();
  render(<BotaoPrimario onClick={handleClick}>Clique Aqui</BotaoPrimario>);
  
  screen.getByRole('button').click();
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

### 🧠 Testando Componentes com Estado

Para componentes que gerenciam estado interno:

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Acordeao from './Acordeao';

const conteudoMock = [
  { titulo: 'Seção 1', conteudo: 'Conteúdo da seção 1' },
  { titulo: 'Seção 2', conteudo: 'Conteúdo da seção 2' }
];

test('renderiza todos os títulos das seções', () => {
  render(<Acordeao itens={conteudoMock} />);
  
  expect(screen.getByText('Seção 1')).toBeInTheDocument();
  expect(screen.getByText('Seção 2')).toBeInTheDocument();
});

test('conteúdo está inicialmente oculto', () => {
  render(<Acordeao itens={conteudoMock} />);
  
  expect(screen.queryByText('Conteúdo da seção 1')).not.toBeVisible();
  expect(screen.queryByText('Conteúdo da seção 2')).not.toBeVisible();
});

test('exibe conteúdo quando o título é clicado', () => {
  render(<Acordeao itens={conteudoMock} />);
  
  fireEvent.click(screen.getByText('Seção 1'));
  expect(screen.getByText('Conteúdo da seção 1')).toBeVisible();
  expect(screen.queryByText('Conteúdo da seção 2')).not.toBeVisible();
});
```

### 🔌 Testando Hooks Personalizados

Para hooks customizados:

```jsx
import { renderHook, act } from '@testing-library/react-hooks';
import useFormulario from './useFormulario';

test('retorna os valores iniciais', () => {
  const { result } = renderHook(() => 
    useFormulario({ nome: '', email: '' })
  );
  
  expect(result.current.valores).toEqual({ nome: '', email: '' });
  expect(result.current.erros).toEqual({});
});

test('atualiza valor quando handleChange é chamado', () => {
  const { result } = renderHook(() => 
    useFormulario({ nome: '', email: '' })
  );
  
  act(() => {
    result.current.handleChange({
      target: { name: 'nome', value: 'Maria' }
    });
  });
  
  expect(result.current.valores.nome).toBe('Maria');
});

test('valida o campo nome como requerido', () => {
  const validacoes = {
    nome: (valor) => !valor ? 'Nome é obrigatório' : null
  };
  
  const { result } = renderHook(() => 
    useFormulario({ nome: '' }, validacoes)
  );
  
  act(() => {
    result.current.validar();
  });
  
  expect(result.current.erros.nome).toBe('Nome é obrigatório');
});
```

### 🌐 Testando Componentes com Context

Para componentes que usam React Context:

```jsx
import { render, screen } from '@testing-library/react';
import { TemaContext } from './TemaContext';
import BotaoTema from './BotaoTema';

test('renderiza com a cor do tema', () => {
  render(
    <TemaContext.Provider value={{ cor: 'azul', modo: 'claro' }}>
      <BotaoTema>Clique Aqui</BotaoTema>
    </TemaContext.Provider>
  );
  
  const botao = screen.getByRole('button');
  expect(botao).toHaveClass('tema-azul');
  expect(botao).toHaveClass('modo-claro');
});
```

### 🔄 Testando Requisições API

Para componentes que fazem chamadas à API:

```jsx
import { render, screen, waitFor } from '@testing-library/react';
import axios from 'axios';
import ListaUsuarios from './ListaUsuarios';

// Mock da biblioteca axios
jest.mock('axios');

test('renderiza lista de usuários da API', async () => {
  // Configuração do mock
  axios.get.mockResolvedValue({
    data: [
      { id: 1, nome: 'João Silva' },
      { id: 2, nome: 'Maria Oliveira' }
    ]
  });
  
  render(<ListaUsuarios />);
  
  // Inicialmente mostra estado de carregamento
  expect(screen.getByText(/carregando/i)).toBeInTheDocument();
  
  // Espera os dados serem carregados
  await waitFor(() => {
    expect(screen.getByText('João Silva')).toBeInTheDocument();
    expect(screen.getByText('Maria Oliveira')).toBeInTheDocument();
  });
  
  // Verifica se a chamada foi feita corretamente
  expect(axios.get).toHaveBeenCalledWith('/api/usuarios');
});

test('manipula erro da API', async () => {
  // Mock que simula erro
  axios.get.mockRejectedValue(new Error('Falha ao carregar dados'));
  
  render(<ListaUsuarios />);
  
  // Espera a mensagem de erro aparecer
  await waitFor(() => {
    expect(screen.getByText(/falha ao carregar/i)).toBeInTheDocument();
  });
});
```

### 🖥️ Testando Rotas e Navegação

Para testar navegação com React Router:

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import { BrowserRouter, MemoryRouter, Routes, Route } from 'react-router-dom';
import App from './App';
import Home from './Home';
import Perfil from './Perfil';

// Teste de um componente que usa o Link do React Router
test('navega para outra página quando o link é clicado', () => {
  render(
    <BrowserRouter>
      <Home />
    </BrowserRouter>
  );
  
  fireEvent.click(screen.getByText(/ir para perfil/i));
  
  // Verificar se a URL mudou
  expect(window.location.pathname).toBe('/perfil');
});

// Teste mais controlado com MemoryRouter
test('renderiza componentes de acordo com a rota', () => {
  render(
    <MemoryRouter initialEntries={['/perfil']}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/perfil" element={<Perfil />} />
      </Routes>
    </MemoryRouter>
  );
  
  // Verifica se o componente correto foi renderizado
  expect(screen.getByText(/página de perfil/i)).toBeInTheDocument();
});
```

### 🧠 Testando Redux

Para componentes conectados ao Redux:

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import contadorReducer from './contadorSlice';
import ContadorComponente from './ContadorComponente';

// Cria uma store para testes
function renderComStore(
  ui,
  { preloadedState, store = configureStore({ 
      reducer: { contador: contadorReducer },
      preloadedState
    }), 
    ...renderOptions 
  } = {}
) {
  function Wrapper({ children }) {
    return <Provider store={store}>{children}</Provider>;
  }
  
  return {
    ...render(ui, { wrapper: Wrapper, ...renderOptions }),
    store
  };
}

test('renderiza o contador com o valor inicial do estado', () => {
  renderComStore(<ContadorComponente />, {
    preloadedState: {
      contador: { valor: 5 }
    }
  });
  
  expect(screen.getByText('5')).toBeInTheDocument();
});

test('incrementa o contador quando o botão é clicado', () => {
  renderComStore(<ContadorComponente />, {
    preloadedState: {
      contador: { valor: 0 }
    }
  });
  
  fireEvent.click(screen.getByText('Incrementar'));
  expect(screen.getByText('1')).toBeInTheDocument();
});
```

## 📝 Boas Práticas de Teste

### 🔍 O que Testar?

1. **Comportamento, não implementação**: Teste como o componente se comporta, não como é implementado
2. **Principais caminhos do usuário**: Foque nos fluxos mais comuns e críticos
3. **Edge cases**: Teste cenários extremos e condições de erro
4. **Lógica de renderização condicional**: Verifique se o componente renderiza corretamente em diferentes estados

### 🏆 Como Escrever Bons Testes

1. **Mantenha testes independentes**: Cada teste deve funcionar isoladamente
2. **Organize em conjuntos lógicos**: Agrupe testes relacionados
3. **Use nomes descritivos**: Descreva claramente o que cada teste verifica
4. **Siga o padrão AAA**: Arrange (preparar), Act (agir), Assert (verificar)
5. **Use Test-Driven Development (TDD)**: Escreva o teste antes da implementação quando possível
6. **Evite testes acoplados**: Não crie dependências entre testes

```jsx
// Exemplo de bons nomes e organização
describe('Componente Formulário', () => {
  describe('Validação', () => {
    test('exibe erro quando o email é inválido', () => {
      // Teste aqui
    });
    
    test('exibe erro quando a senha é muito curta', () => {
      // Teste aqui
    });
  });
  
  describe('Submissão', () => {
    test('chama onSubmit com dados válidos quando enviado', () => {
      // Teste aqui
    });
    
    test('desabilita o botão durante submissão', () => {
      // Teste aqui
    });
  });
});
```

### 🧩 Mocks, Stubs e Spies

Técnicas para isolar o código sendo testado:

```jsx
// Mock de módulo
jest.mock('./api');
import { fetchUsuarios } from './api';

// Mock de função
const mockCallback = jest.fn();
elementoAlvo.addEventListener('click', mockCallback);
elementoAlvo.click();
expect(mockCallback).toHaveBeenCalled();

// Spy (monitoramento de função existente)
jest.spyOn(console, 'error').mockImplementation(() => {});
expect(console.error).toHaveBeenCalledWith('mensagem de erro');

// Stub (implementação temporária)
fetchUsuarios.mockResolvedValue([{ id: 1, nome: 'João' }]);
```

### 🔄 Configuração e Limpeza

Configure o ambiente antes dos testes e limpe depois:

```jsx
// Setup e teardown globais
beforeAll(() => {
  // Executa uma vez antes de todos os testes
  localStorage.setItem('token', 'fake-token');
});

afterAll(() => {
  // Executa uma vez depois de todos os testes
  localStorage.clear();
});

// Setup e teardown por teste
beforeEach(() => {
  // Executa antes de cada teste
  jest.resetAllMocks();
});

afterEach(() => {
  // Executa depois de cada teste
  document.body.innerHTML = '';
});
```

## 🎯 Métricas e Cobertura de Testes

Ferramentas como Jest fornecem relatórios de cobertura:

```bash
# Executando testes com cobertura
npm test -- --coverage
```

O relatório inclui:

- **Statements**: Percentual de declarações executadas
- **Branches**: Percentual de caminhos condicionais testados
- **Functions**: Percentual de funções chamadas
- **Lines**: Percentual de linhas executadas

### 📊 Interpretando a Cobertura

- 💯 **100% não é sempre necessário**: Foque na qualidade, não na quantidade
- 🎯 **Áreas críticas**: Priorize partes importantes da aplicação
- 🧠 **Cobertura não significa qualidade**: Testes mal escritos podem ter alta cobertura

## 🔧 Configurando o Ambiente de Teste

### 🛠️ Create React App

Aplicações criadas com Create React App já vêm configuradas para testes:

```bash
# Executar testes
npm test

# Executar testes uma vez (CI)
npm test -- --watchAll=false
```

### ⚙️ Configuração Manual

Para projetos personalizados:

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/setupTests.js'],
  moduleNameMapper: {
    // Mock para arquivos estáticos
    '\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2)$': 
      '<rootDir>/__mocks__/fileMock.js',
    // Mock para arquivos de estilo
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  transform: {
    '^.+\\.(js|jsx|ts|tsx)$': 'babel-jest'
  },
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};

// setupTests.js
import '@testing-library/jest-dom';

// Configuração global para testes
window.matchMedia = window.matchMedia || function() {
  return {
    matches: false,
    addListener: function() {},
    removeListener: function() {}
  };
};
```

## 🏭 Testes em Ambientes de CI/CD

Integre testes em pipelines de integração contínua:

```yaml
# Exemplo de configuração GitHub Actions
name: React Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'
        cache: 'npm'
    - name: Install dependencies
      run: npm ci
    - name: Run tests
      run: npm test -- --watchAll=false
    - name: Run tests with coverage
      run: npm test -- --coverage --watchAll=false
    - name: Upload coverage report
      uses: codecov/codecov-action@v2
```

## 🌟 Dicas e Melhores Práticas

1. **Priorize testes que agregam valor**: Foque em funcionalidades críticas
2. **Mantenha testes rápidos**: Testes lentos desaceleram o desenvolvimento
3. **Use seletores acessíveis**: Prefira `getByRole` e `getByLabelText` a seletores de CSS
4. **Evite testar bibliotecas externas**: Foque no seu código, não no comportamento de libs
5. **Combine tipos de teste**: Use unitários para lógica, integração para fluxos, E2E para jornadas críticas
6. **Teste para regressões**: Quando encontrar um bug, escreva um teste para ele
7. **Use Snapshot Testing com moderação**: Útil para UI estável, mas pode gerar falsos positivos
8. **Simule o comportamento do usuário**: Teste como um usuário interage, não como desenvolvedores

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/> 