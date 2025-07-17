# Iniciando um Projeto React com `react-router-dom`

Este guia passo a passo irá te mostrar como configurar um novo projeto React e integrar o `react-router-dom` para gerenciar as rotas da sua aplicação, utilizando `npm` para todas as instalações.

### Pré-requisitos

- Node.js e npm instalados na sua máquina.

- Conhecimento básico de React.

## Passo 1: Criar um Novo Projeto React

Vamos começar criando um novo projeto React. Usaremos o Vite, que é uma ferramenta moderna e rápida para iniciar projetos React (e outros frameworks).

1. **Abra seu terminal** (ou linha de comando).

2. **Navegue até a pasta** onde você quer criar seu projeto.

3. Execute o seguinte comando para criar um novo projeto Vite com React:

```
npm create vite@latest meu-projeto-com-router -- --template react
```

- `npm create vite@latest`: Usa o `npx` (que o `npm create` aciona por baixo dos panos) para executar a última versão do `create-vite`.

- `meu-projeto-com-router`: É o nome da pasta do seu novo projeto. Você pode substituir por qualquer nome que desejar.

- `-- --template react`: Especifica que queremos um template React. (Use `--template react-ts` se quiser TypeScript).

4. Após a criação, **entre na pasta do projeto** e instale as dependências iniciais:

```
cd meu-projeto-com-router
npm install
```

5. Você pode rodar o projeto para verificar se tudo está funcionando:

```
npm run dev
```

Abra o navegador na URL indicada (geralmente `http://localhost:5173/`) para ver a tela inicial do Vite/React.

## Passo 2: Instalar o `react-router-dom`

Agora que seu projeto base está pronto, vamos adicionar a biblioteca de roteamento.

1. Com o terminal ainda dentro da pasta `meu-projeto-com-router`, execute o seguinte comando:

```
npm install react-router-dom
```

Este comando vai baixar a biblioteca e adicioná-la como uma dependência no seu arquivo `package.json`.

## Passo 3: Configurar a Estrutura Básica de Roteamento

Chegou a hora de integrar o `react-router-dom` ao seu aplicativo.

1. **Abra o arquivo** `src/App.jsx `(ou `src/App.tsx` se estiver usando TypeScript).

2. **Importe os componentes essenciais** do `react-router-dom`: `BrowserRouter`, `Routes` e `Route`.

3. **Crie alguns componentes de página** simples para testar as rotas. Por exemplo, crie os arquivos `src/pages/Home.jsx` e `src/pages/About.jsx`.

`src/pages/Home.jsx`:

```
import React from 'react';

function Home() {
  return (
    <div>
      <h1>Bem-vindo à Página Inicial!</h1>
      <p>Esta é a página principal da sua aplicação.</p>
    </div>
  );
}

export default Home;
```

`src/pages/About.jsx`:

```
import React from 'react';

function About() {
  return (
    <div>
      <h1>Sobre Nós</h1>
      <p>Aqui você encontra informações sobre a nossa empresa.</p>
    </div>
  );
}

export default About;
```

4. **Modifique** o src/App.jsx para configurar as rotas:

```
// src/App.jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom'; // Importe Link também
import Home from './pages/Home';
import About from './pages/About';
import './App.css'; // Mantenha ou remova o CSS padrão do Vite, como preferir

function App() {
  return (
    // 1. Envolva toda a sua aplicação com BrowserRouter
    <BrowserRouter>
      {/* O Header ou navegação deve ficar fora das <Routes> se for comum a todas as páginas */}
      <nav>
        <ul>
          <li>
            {/* 4. Use o componente <Link> para navegação */}
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
        </ul>
      </nav>

      {/* 2. <Routes> é o contêiner para todas as suas definições de rota */}
      <Routes>
        {/* 3. <Route> define uma rota específica */}
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        {/* Opcional: Rota para páginas não encontradas (404) */}
        <Route path="*" element={<h2>Página Não Encontrada!</h2>} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

## Passo 4: Testar a Navegação

1. Certifique-se de que seu servidor de desenvolvimento está rodando:

```
npm run dev
```

2. Abra seu navegador e acesse a URL (`http://localhost:5173/` ou a que for indicada).

3. Clique nos links "Home" e "About" que você criou. Você verá o conteúdo de cada página ser carregado sem que a página inteira seja recarregada. Observe também como a URL no navegador muda.

### Entendendo os Componentes Chave:

- `<BrowserRouter>`: É o componente principal que habilita o roteamento na sua aplicação. Ele usa a History API do navegador para manter a UI em sincronia com a URL.

- `<Routes>`: É o contêiner onde você define todas as suas rotas. Ele examina a URL atual e renderiza apenas a primeira `<Route>` que corresponder.

- `<Route>`: Define um caminho específico (`path`) e o componente (element) que deve ser renderizado quando esse caminho for correspondido na URL.

- `<Link>`: É o componente preferencial para navegação entre rotas no React Router DOM. Ele impede o recarregamento completo da página e manipula a URL de forma "SPA-friendly".

### [Voltar ao README Principal](../README.md)
