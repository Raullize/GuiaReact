<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=header"/>

# 📝 Formulários e Inputs no React 📋

Formulários são componentes essenciais em aplicações web interativas. Esta seção explora como o React lida com inputs e formulários, desde os conceitos básicos até técnicas avançadas e boas práticas. 🚀

## 🔄 Componentes Controlados vs. Não-Controlados

No React, existem duas abordagens principais para trabalhar com formulários:

### 🎮 Componentes Controlados

Em componentes controlados, o React controla o estado do formulário. O valor do elemento é definido pelo estado e atualizado através de manipuladores de eventos:

```jsx
function FormularioControlado() {
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

#### 🌟 Vantagens dos Componentes Controlados:
- ✅ Acesso imediato ao valor do input (sem necessidade de refs)
- ✅ Validação instantânea
- ✅ Capacidade de controlar o que está sendo digitado
- ✅ Fácil manipulação e transformação de dados

### 🔓 Componentes Não-Controlados

Componentes não-controlados deixam o DOM gerenciar o estado do formulário. Usamos refs para acessar os valores dos campos quando necessário:

```jsx
function FormularioNaoControlado() {
  const inputRef = useRef(null);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Nome enviado: ${inputRef.current.value}`);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <label>
        Nome:
        <input type="text" ref={inputRef} defaultValue="" />
      </label>
      <button type="submit">Enviar</button>
    </form>
  );
}
```

#### 🌟 Vantagens dos Componentes Não-Controlados:
- ✅ Código mais simples para formulários básicos
- ✅ Menos re-renderizações (melhor desempenho)
- ✅ Integração mais fácil com bibliotecas DOM não-React

## 📝 Trabalhando com Diferentes Tipos de Input

### 🔤 Campos de Texto

```jsx
function CamposTexto() {
  const [valores, setValores] = useState({
    nome: '',
    email: '',
    mensagem: ''
  });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({ ...valores, [name]: value });
  };
  
  return (
    <form>
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
    </form>
  );
}
```

### ✅ Checkbox

```jsx
function Preferencias() {
  const [preferencias, setPreferencias] = useState({
    receberEmails: true,
    temaEscuro: false,
    notificacoes: true
  });
  
  const handleCheckboxChange = (e) => {
    const { name, checked } = e.target;
    setPreferencias({ ...preferencias, [name]: checked });
  };
  
  return (
    <form>
      <div>
        <label>
          <input
            type="checkbox"
            name="receberEmails"
            checked={preferencias.receberEmails}
            onChange={handleCheckboxChange}
          />
          Receber e-mails promocionais
        </label>
      </div>
      
      <div>
        <label>
          <input
            type="checkbox"
            name="temaEscuro"
            checked={preferencias.temaEscuro}
            onChange={handleCheckboxChange}
          />
          Usar tema escuro
        </label>
      </div>
      
      <div>
        <label>
          <input
            type="checkbox"
            name="notificacoes"
            checked={preferencias.notificacoes}
            onChange={handleCheckboxChange}
          />
          Ativar notificações
        </label>
      </div>
    </form>
  );
}
```

### 🔘 Radio Buttons

```jsx
function OpcoesDePagamento() {
  const [metodoPagamento, setMetodoPagamento] = useState('cartao');
  
  const handleRadioChange = (e) => {
    setMetodoPagamento(e.target.value);
  };
  
  return (
    <form>
      <div>
        <h3>Método de Pagamento:</h3>
        
        <div>
          <label>
            <input
              type="radio"
              name="pagamento"
              value="cartao"
              checked={metodoPagamento === 'cartao'}
              onChange={handleRadioChange}
            />
            Cartão de Crédito
          </label>
        </div>
        
        <div>
          <label>
            <input
              type="radio"
              name="pagamento"
              value="boleto"
              checked={metodoPagamento === 'boleto'}
              onChange={handleRadioChange}
            />
            Boleto Bancário
          </label>
        </div>
        
        <div>
          <label>
            <input
              type="radio"
              name="pagamento"
              value="pix"
              checked={metodoPagamento === 'pix'}
              onChange={handleRadioChange}
            />
            PIX
          </label>
        </div>
      </div>
    </form>
  );
}
```

### 📋 Select e Multi-select

```jsx
function Selecoes() {
  const [pais, setPais] = useState('');
  const [linguagens, setLinguagens] = useState([]);
  
  const handlePaisChange = (e) => {
    setPais(e.target.value);
  };
  
  const handleLinguagensChange = (e) => {
    const opcoes = Array.from(e.target.selectedOptions, (option) => option.value);
    setLinguagens(opcoes);
  };
  
  return (
    <form>
      <div>
        <label htmlFor="pais">País:</label>
        <select
          id="pais"
          value={pais}
          onChange={handlePaisChange}
        >
          <option value="">Selecione um país</option>
          <option value="br">Brasil</option>
          <option value="us">Estados Unidos</option>
          <option value="pt">Portugal</option>
          <option value="es">Espanha</option>
        </select>
      </div>
      
      <div>
        <label htmlFor="linguagens">Linguagens de Programação:</label>
        <select
          id="linguagens"
          multiple
          value={linguagens}
          onChange={handleLinguagensChange}
        >
          <option value="js">JavaScript</option>
          <option value="ts">TypeScript</option>
          <option value="py">Python</option>
          <option value="java">Java</option>
          <option value="csharp">C#</option>
        </select>
        <p>Segure Ctrl (ou Cmd) para selecionar múltiplas opções</p>
      </div>
    </form>
  );
}
```

### 📅 Inputs Numéricos e de Data

```jsx
function DadosNumericos() {
  const [valores, setValores] = useState({
    idade: 25,
    altura: 1.75,
    nascimento: '2000-01-01'
  });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({ ...valores, [name]: value });
  };
  
  return (
    <form>
      <div>
        <label htmlFor="idade">Idade:</label>
        <input
          type="number"
          id="idade"
          name="idade"
          min="0"
          max="120"
          value={valores.idade}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="altura">Altura (m):</label>
        <input
          type="number"
          id="altura"
          name="altura"
          step="0.01"
          min="0"
          max="3"
          value={valores.altura}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="nascimento">Data de Nascimento:</label>
        <input
          type="date"
          id="nascimento"
          name="nascimento"
          value={valores.nascimento}
          onChange={handleChange}
        />
      </div>
    </form>
  );
}
``` 

## 🛡️ Validação de Formulários

A validação é essencial para garantir que os dados fornecidos pelos usuários sejam corretos e seguros.

### ✅ Validação Nativa do HTML5

```jsx
function FormularioValidacaoNativa() {
  const handleSubmit = (e) => {
    e.preventDefault();
    // Os inputs só chegarão aqui se passarem na validação nativa
    console.log('Formulário válido!');
  };
  
  return (
    <form onSubmit={handleSubmit} noValidate>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          required
          pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$"
        />
      </div>
      
      <div>
        <label htmlFor="senha">Senha:</label>
        <input
          type="password"
          id="senha"
          required
          minLength={8}
        />
      </div>
      
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### 🚦 Validação Personalizada com Estado

```jsx
function FormularioValidacaoPersonalizada() {
  const [valores, setValores] = useState({
    email: '',
    senha: ''
  });
  
  const [erros, setErros] = useState({});
  const [enviado, setEnviado] = useState(false);
  
  const validarEmail = (email) => {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return regex.test(email);
  };
  
  const validarSenha = (senha) => {
    return senha.length >= 8;
  };
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValores({ ...valores, [name]: value });
    
    // Limpar erro quando o usuário começa a digitar novamente
    if (erros[name]) {
      setErros({
        ...erros,
        [name]: null
      });
    }
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // Validar todos os campos
    const novosErros = {};
    
    if (!valores.email) {
      novosErros.email = 'Email é obrigatório';
    } else if (!validarEmail(valores.email)) {
      novosErros.email = 'Email inválido';
    }
    
    if (!valores.senha) {
      novosErros.senha = 'Senha é obrigatória';
    } else if (!validarSenha(valores.senha)) {
      novosErros.senha = 'A senha deve ter pelo menos 8 caracteres';
    }
    
    // Se houver erros, atualizar o estado e não enviar
    if (Object.keys(novosErros).length > 0) {
      setErros(novosErros);
      return;
    }
    
    // Enviar o formulário se estiver tudo correto
    console.log('Dados válidos:', valores);
    setEnviado(true);
  };
  
  if (enviado) {
    return <div>Formulário enviado com sucesso!</div>;
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={valores.email}
          onChange={handleChange}
          className={erros.email ? 'input-erro' : ''}
        />
        {erros.email && <p className="mensagem-erro">{erros.email}</p>}
      </div>
      
      <div>
        <label htmlFor="senha">Senha:</label>
        <input
          type="password"
          id="senha"
          name="senha"
          value={valores.senha}
          onChange={handleChange}
          className={erros.senha ? 'input-erro' : ''}
        />
        {erros.senha && <p className="mensagem-erro">{erros.senha}</p>}
      </div>
      
      <button type="submit">Entrar</button>
    </form>
  );
}
```

## 🏗️ Estrutura Recomendada para Formulários

Organizar bem seus formulários pode facilitar a manutenção e melhorar a experiência do usuário:

```jsx
// Componente para campo de formulário reutilizável
function CampoFormulario({ 
  id, 
  label, 
  error, 
  children 
}) {
  return (
    <div className="campo-formulario">
      {label && <label htmlFor={id}>{label}</label>}
      {children}
      {error && <p className="erro-campo">{error}</p>}
    </div>
  );
}

// Componente de input reutilizável
function Input({
  id,
  name,
  type = 'text',
  value,
  onChange,
  error,
  label,
  ...props
}) {
  return (
    <CampoFormulario id={id} label={label} error={error}>
      <input
        id={id}
        name={name || id}
        type={type}
        value={value}
        onChange={onChange}
        className={error ? 'input-com-erro' : ''}
        {...props}
      />
    </CampoFormulario>
  );
}

// Exemplo de uso
function FormularioCadastro() {
  const [dados, setDados] = useState({
    nome: '',
    email: '',
    senha: ''
  });
  
  const [erros, setErros] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setDados({ ...dados, [name]: value });
  };
  
  const validarFormulario = () => {
    const novosErros = {};
    
    if (!dados.nome) novosErros.nome = 'Nome é obrigatório';
    if (!dados.email) novosErros.email = 'Email é obrigatório';
    if (!dados.senha) novosErros.senha = 'Senha é obrigatória';
    else if (dados.senha.length < 8) novosErros.senha = 'Senha muito curta';
    
    setErros(novosErros);
    return Object.keys(novosErros).length === 0;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
    if (validarFormulario()) {
      console.log('Enviando:', dados);
      // Lógica para enviar dados para o servidor
    }
  };
  
  return (
    <form onSubmit={handleSubmit} className="formulario-cadastro">
      <h2>Cadastro de Usuário</h2>
      
      <Input
        id="nome"
        label="Nome Completo"
        value={dados.nome}
        onChange={handleChange}
        error={erros.nome}
        required
      />
      
      <Input
        id="email"
        type="email"
        label="Email"
        value={dados.email}
        onChange={handleChange}
        error={erros.email}
        required
      />
      
      <Input
        id="senha"
        type="password"
        label="Senha"
        value={dados.senha}
        onChange={handleChange}
        error={erros.senha}
        required
        minLength={8}
      />
      
      <div className="acoes-formulario">
        <button type="reset">Limpar</button>
        <button type="submit" className="botao-primario">Cadastrar</button>
      </div>
    </form>
  );
}
``` 

## 📚 Bibliotecas Populares para Formulários

Além da implementação manual, você pode utilizar bibliotecas especializadas para simplificar o gerenciamento de formulários complexos:

### 🧩 Formik

Formik é uma das bibliotecas mais populares para formulários em React:

```jsx
import { Formik, Form, Field, ErrorMessage } from 'formik';
import * as Yup from 'yup'; // Para validação

// Schema de validação
const SignupSchema = Yup.object().shape({
  nome: Yup.string()
    .min(2, 'Nome muito curto!')
    .max(50, 'Nome muito longo!')
    .required('Nome é obrigatório'),
  email: Yup.string()
    .email('Email inválido')
    .required('Email é obrigatório'),
  senha: Yup.string()
    .min(8, 'Senha deve ter pelo menos 8 caracteres')
    .required('Senha é obrigatória')
});

function FormularioFormik() {
  return (
    <div>
      <h1>Cadastro</h1>
      <Formik
        initialValues={{ nome: '', email: '', senha: '' }}
        validationSchema={SignupSchema}
        onSubmit={(values, { setSubmitting }) => {
          setTimeout(() => {
            alert(JSON.stringify(values, null, 2));
            setSubmitting(false);
          }, 400);
        }}
      >
        {({ isSubmitting }) => (
          <Form>
            <div>
              <label htmlFor="nome">Nome</label>
              <Field type="text" name="nome" />
              <ErrorMessage name="nome" component="div" className="erro" />
            </div>
            
            <div>
              <label htmlFor="email">Email</label>
              <Field type="email" name="email" />
              <ErrorMessage name="email" component="div" className="erro" />
            </div>
            
            <div>
              <label htmlFor="senha">Senha</label>
              <Field type="password" name="senha" />
              <ErrorMessage name="senha" component="div" className="erro" />
            </div>
            
            <button type="submit" disabled={isSubmitting}>
              {isSubmitting ? 'Enviando...' : 'Enviar'}
            </button>
          </Form>
        )}
      </Formik>
    </div>
  );
}
```

### 🪝 React Hook Form

Uma alternativa mais leve e focada em performance:

```jsx
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

const schema = yup.object({
  nome: yup.string().required('Nome é obrigatório'),
  email: yup.string().email('Email inválido').required('Email é obrigatório'),
  senha: yup.string().min(8, 'Mínimo de 8 caracteres').required('Senha é obrigatória')
}).required();

function FormularioHookForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(schema)
  });
  
  const onSubmit = data => console.log(data);
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="nome">Nome</label>
        <input id="nome" {...register('nome')} />
        {errors.nome && <p className="erro">{errors.nome.message}</p>}
      </div>
      
      <div>
        <label htmlFor="email">Email</label>
        <input id="email" type="email" {...register('email')} />
        {errors.email && <p className="erro">{errors.email.message}</p>}
      </div>
      
      <div>
        <label htmlFor="senha">Senha</label>
        <input id="senha" type="password" {...register('senha')} />
        {errors.senha && <p className="erro">{errors.senha.message}</p>}
      </div>
      
      <button type="submit">Enviar</button>
    </form>
  );
}
```

## 💡 Boas Práticas para Formulários React

### 📋 Estrutura e Organização

1. **Componentização**: Divida formulários grandes em componentes menores e reutilizáveis
2. **Separação de Responsabilidades**: Mantenha lógica de validação separada da renderização
3. **Hierarquia Clara**: Organize campos relacionados em grupos lógicos

**Exemplo de Boa Estrutura**:
```jsx
function FormularioCompra() {
  return (
    <form>
      <fieldset>
        <legend>Informações Pessoais</legend>
        <DadosPessoais />
      </fieldset>
      
      <fieldset>
        <legend>Endereço de Entrega</legend>
        <EnderecoEntrega />
      </fieldset>
      
      <fieldset>
        <legend>Pagamento</legend>
        <DadosPagamento />
      </fieldset>
      
      <BotoesAcao />
    </form>
  );
}
```

### 🎯 Performance

1. **Evite Re-renderizações Desnecessárias**: Use `useMemo`, `useCallback` ou bibliotecas como React Hook Form para minimizar re-renderizações
2. **Validação Eficiente**: Faça validação no evento `onBlur` em vez de `onChange` para campos que requerem validação complexa
3. **Debounce em Tempo Real**: Utilize debounce para validações em tempo real que são custosas

### 👨‍👩‍👧‍👦 Acessibilidade

1. **Use Labels Corretamente**: Sempre associe labels com inputs usando o atributo `htmlFor`
2. **Mensagens de Erro Claras**: Associe mensagens de erro aos campos usando `aria-describedby`
3. **Ordem de Tabulação**: Garanta uma ordem lógica de tabulação com `tabIndex`
4. **Marcadores ARIA**: Utilize `aria-invalid`, `aria-required` e outros atributos ARIA

```jsx
function CampoAcessivel({ id, label, error, ...props }) {
  const errorId = `${id}-error`;
  
  return (
    <div className="form-group">
      <label htmlFor={id}>{label}</label>
      <input
        id={id}
        aria-invalid={!!error}
        aria-describedby={error ? errorId : undefined}
        {...props}
      />
      {error && <p id={errorId} className="error-message">{error}</p>}
    </div>
  );
}
```

### 🔒 Segurança

1. **Validação no Servidor**: Sempre valide dados no servidor, além do cliente
2. **Sanitização de Dados**: Limpe e sanitize os dados antes de processá-los
3. **Proteção CSRF**: Use tokens CSRF para formulários submetidos a APIs próprias
4. **Rate Limiting**: Implemente limitação de taxa para evitar ataques de força bruta

### 📱 Responsividade

1. **Inputs de Tamanho Adequado**: Use unidades relativas (%, em, rem) em vez de pixels fixos
2. **Labels Responsivos**: Em telas pequenas, coloque labels acima dos inputs
3. **Grupos de Campos Adaptáveis**: Use flexbox ou grid para reorganizar grupos de campos em diferentes tamanhos de tela

```css
.form-row {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.form-field {
  flex: 1 1 250px;
}

@media (max-width: 600px) {
  .form-field label {
    display: block;
    margin-bottom: 0.5rem;
  }
}
```

## 🚀 Dicas Avançadas

1. **Formulários Dinâmicos**: Crie campos que aparecem/desaparecem com base em respostas anteriores
2. **Auto-save**: Implemente salvamento automático para formulários longos
3. **Multi-step Forms**: Divida formulários complexos em várias etapas
4. **Form Arrays**: Use técnicas como `fieldArray` do Formik para lidar com grupos de campos repetíveis

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=61DAFB&height=120&section=footer"/>

