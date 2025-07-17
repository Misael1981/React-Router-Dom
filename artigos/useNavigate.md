# Dominando o Hook useNavigate() no React Router DOM

No React Router DOM, o componente `<Link>` é excelente para navegação baseada em cliques de usuários. No entanto, há muitas situações onde você precisa navegar para uma nova rota **programaticamente**, ou seja, através de código, sem a interação direta do usuário com um link. É aí que o hook `useNavigate()` entra em cena!

## O Que É e Para Que Serve `useNavigate()`?

O `useNavigate()` é um hook (gancho) que fornece uma função para navegar programaticamente. Ele permite que você redirecione o usuário para uma nova URL em resposta a algum evento, como:

- O envio bem-sucedido de um formulário.

- Um login ou logout de usuário.

- Um redirecionamento após uma ação específica (ex: excluir um item, criar um novo recurso).

- Um timer que expira e leva o usuário a outra página.

- Lógica condicional que decide para onde o usuário deve ir.

### Sintaxe Básica

1. #### Importe o `useNavigate` do `react-router-dom`:

```
import { useNavigate } from 'react-router-dom';
```

2. #### Chame o hook dentro do seu componente funcional:

```
const navigate = useNavigate();
```

3. #### Use a função `Maps` para redirecionar:

```
navigate('/nova-rota'); // Navega para uma rota específica
navigate(-1);           // Volta uma página no histórico do navegador
navigate('../');        // Volta um nível na rota atual (navegação relativa)
```

### Exemplo Prático: Redirecionamento Após Envio de Formulário

Vamos criar um formulário de login simples que redireciona o usuário para uma página de `Dashboard` após o envio bem-sucedido.

### Passo 1: Configurar as Rotas no `App.jsx`

Vamos usar a mesma estrutura de roteamento do tutorial do `Outlet` ou a base inicial do projeto. Certifique-se de ter as rotas para `/login` e `/dashboard`.

```
// src/App.jsx (exemplo simplificado)
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Login from './pages/Login'; // Vamos criar este componente
import Dashboard from './pages/Dashboard'; // Já devemos ter este do tutorial do Outlet

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/dashboard" element={<Dashboard />} />
        {/* Outras rotas, se houver */}
        <Route path="/" element={<h1>Bem-vindo! Vá para /login</h1>} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### Passo 2: Criar o Componente `Login.jsx`

Este componente terá um formulário simples. Ao "submeter" o formulário, usaremos `useNavigate` para ir para a página de Dashboard.

```
// src/pages/Login.jsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom'; // Importe o hook useNavigate

function Login() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const navigate = useNavigate(); // Inicializa o hook

  const handleSubmit = (event) => {
    event.preventDefault(); // Impede o recarregamento padrão da página

    // Lógica de autenticação simulada:
    if (username === 'usuario' && password === 'senha123') {
      alert('Login bem-sucedido!');
      // Redireciona para o Dashboard após o login
      navigate('/dashboard');
      // Ou, se quiser substituir a entrada atual no histórico para que o botão "voltar" não volte para o login:
      // navigate('/dashboard', { replace: true });
    } else {
      alert('Credenciais inválidas. Tente novamente!');
    }
  };

  return (
    <div>
      <h1>Página de Login</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="username">Usuário:</label>
          <input
            type="text"
            id="username"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
            required
          />
        </div>
        <div>
          <label htmlFor="password">Senha:</label>
          <input
            type="password"
            id="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            required
          />
        </div>
        <button type="submit">Entrar</button>
      </form>
    </div>
  );
}

export default Login;
```

### Passo 3: Criar o Componente `Dashboard.jsx`

Este é o componente para onde o usuário será redirecionado.

```
// src/pages/Dashboard.jsx
import React from 'react';
import { useNavigate } from 'react-router-dom'; // Para o botão de logout

function Dashboard() {
  const navigate = useNavigate();

  const handleLogout = () => {
    alert('Saindo da conta...');
    // Redireciona para a página de login
    navigate('/login');
    // Ou, para voltar para a página anterior (se houver), ou para a home se não houver:
    // navigate(-1);
  };

  return (
    <div>
      <h1>Bem-vindo ao seu Dashboard!</h1>
      <p>Aqui estão suas informações secretas.</p>
      <button onClick={handleLogout}>Sair</button>
    </div>
  );
}

export default Dashboard;
```

### Passo 4: Testar a Navegação Programática

1. Inicie seu servidor de desenvolvimento:

```
npm run dev
```

2. Acesse `http://localhost:5173/login` no seu navegador.

3. Preencha com `usuario` e `senha123` e clique em "Entrar". Você deve ser redirecionado para o `Dashboard`.

4. No `Dashboard`, clique em "Sair" para ser redirecionado de volta para a página de Login.

## Opções da Função `Maps`

A função `Maps` não serve apenas para ir para uma rota específica. Ela aceita um segundo argumento, um objeto de opções:

- `replace: true`: Se `true`, a entrada atual na pilha do histórico será substituída pela nova rota. Isso significa que, se o usuário clicar no botão "voltar" do navegador, ele não voltará para a página anterior, mas sim para a página antes da que foi substituída. Útil para redirecionamentos pós-login/logout.

```
navigate('/dashboard', { replace: true });
```

- `state`: Um objeto que pode conter dados que serão passados para a nova rota. Esses dados estarão disponíveis no componente de destino através do hook `useLocation()`. Útil para passar pequenas informações sem colocá-las na URL.

```
// Na origem
navigate('/produto/123', { state: { from: 'homepage', category: 'eletronicos' } });

// No destino (Produto.jsx)
import { useLocation } from 'react-router-dom';
function Produto() {
  const location = useLocation();
  const { from, category } = location.state || {}; // Sempre verifique se state existe
  console.log('Vindo de:', from, 'Categoria:', category);
  // ...
}
```

### Navegação Relativa

Você também pode usar `Maps` para navegação relativa, útil em layouts aninhados:

- `Maps('../')`: Volta um nível na URL (ex: de `/users/1` para `/users`).

- `Maps('./edit')`: Vai para `/users/1/edit` se a URL atual for `/users/1`.

O `useNavigate` é uma ferramenta super flexível e essencial para controlar o fluxo do usuário na sua aplicação de forma dinâmica e reativa a eventos.

### [Voltar ao README Principal](../README.md)
