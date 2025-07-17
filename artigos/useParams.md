# Dominando o Hook `useParams()` no React Router DOM

No desenvolvimento web, muitas vezes você precisa exibir detalhes de um item específico (um produto, um usuário, um post de blog) com base em um ID ou slug na URL. É aí que o hook `useParams()` entra em ação!

## O Que É e Para Que Serve `useParams()`?

O `useParams()` é um hook do React Router DOM que permite que você **acesse os parâmetros dinâmicos da URL** em seus componentes funcionais.

Imagine que você tem uma lista de produtos. Ao clicar em um produto, a URL pode mudar de `/produtos` para `/produtos/123`, onde `123` é o ID do produto. O `useParams()` vai te ajudar a "extrair" esse `123` diretamente no componente que exibe os detalhes do produto.

### Sintaxe Básica

1. **Defina a rota com um parâmetro** usando dois pontos (`:`) seguido do nome do parâmetro.
   Ex: `/produtos/:id`, `/usuarios/:nomeDoUsuario`, `/blog/:ano/:mes/:slug`.

2. **Importe** o `useParams` do `react-router-dom`:

```
import { useParams } from 'react-router-dom';
```

3. Chame o hook dentro do seu componente funcional:

```
const params = useParams(); // params será um objeto { id: '123' }
// Ou desestruture diretamente:
const { id, nomeDoUsuario, ano, mes, slug } = useParams();
```

## Exemplo Prático: Exibindo Detalhes de um Produto

Vamos criar uma lista de produtos simples e uma página de detalhes de produto que usa o `useParams()` para exibir informações baseadas no ID do produto na URL.

### Passo 1: Configurar as Rotas no `App.jsx`

Precisaremos de uma rota para listar os produtos (`/produtos`) e uma rota dinâmica para os detalhes de cada produto (`/produtos/:id`).

```
// src/App.jsx (exemplo simplificado)
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';
import ProductList from './pages/ProductList';     // Vamos criar este
import ProductDetail from './pages/ProductDetail'; // E este também

function App() {
  return (
    <BrowserRouter>
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/produtos">Produtos</Link></li>
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<h1>Bem-vindo à Loja!</h1>} />
        <Route path="/produtos" element={<ProductList />} />
        {/* Rota com parâmetro dinâmico ":id" */}
        <Route path="/produtos/:id" element={<ProductDetail />} />
        {/* Rota para 404 */}
        <Route path="*" element={<h2>Página Não Encontrada!</h2>} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### Passo 2: Criar o Componente `ProductList.jsx`

Este componente listará alguns produtos e usará `<Link>` para navegar para a página de detalhes de cada um, passando o ID na URL.

```
// src/pages/ProductList.jsx
import React from 'react';
import { Link } from 'react-router-dom';

// Dados simulados de produtos
const products = [
  { id: '1', name: 'Notebook Gamer', price: 5000 },
  { id: '2', name: 'Monitor Ultrawide', price: 1500 },
  { id: '3', name: 'Teclado Mecânico', price: 300 },
  { id: '4', name: 'Mouse RGB', price: 150 },
];

function ProductList() {
  return (
    <div>
      <h1>Nossos Produtos</h1>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            {/* O Link constrói a URL dinâmica */}
            <Link to={`/produtos/${product.id}`}>
              {product.name} - R$ {product.price.toFixed(2)}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default ProductList;
```

### Passo 3: Criar o Componente `ProductDetail.jsx` (Onde `useParams` Brilha!)

Este é o componente que vai **usar** o `useParams()` para pegar o ID da URL e exibir os detalhes do produto correspondente.

```
// src/pages/ProductDetail.jsx
import React from 'react';
import { useParams, Link } from 'react-router-dom'; // Importe useParams e Link

// Dados simulados de produtos (repetido aqui para simplificar o exemplo)
const products = [
  { id: '1', name: 'Notebook Gamer', price: 5000, description: 'Notebook de alta performance para jogos.' },
  { id: '2', name: 'Monitor Ultrawide', price: 1500, description: 'Monitor com tela curva para imersão total.' },
  { id: '3', name: 'Teclado Mecânico', price: 300, description: 'Teclado com switches de alta durabilidade.' },
  { id: '4', name: 'Mouse RGB', price: 150, description: 'Mouse ergonômico com iluminação personalizável.' },
];

function ProductDetail() {
  // 1. Usa o hook useParams para extrair os parâmetros da URL
  //    No nosso caso, a rota é "/produtos/:id", então teremos um objeto { id: "valor" }
  const { id } = useParams();

  // 2. Encontra o produto correspondente ao ID
  const product = products.find((p) => p.id === id);

  // 3. Exibe o conteúdo ou uma mensagem de "não encontrado"
  if (!product) {
    return (
      <div>
        <h2>Produto Não Encontrado</h2>
        <p>O produto com ID "{id}" não existe.</p>
        <Link to="/produtos">Voltar para a lista de produtos</Link>
      </div>
    );
  }

  return (
    <div>
      <h1>Detalhes do Produto: {product.name}</h1>
      <p><strong>ID:</strong> {product.id}</p>
      <p><strong>Preço:</strong> R$ {product.price.toFixed(2)}</p>
      <p><strong>Descrição:</strong> {product.description}</p>
      <Link to="/produtos">Voltar para a lista de produtos</Link>
    </div>
  );
}

export default ProductDetail;
```

### Passo 4: Testar o `useParams()`

1. Inicie seu servidor de desenvolvimento:

```
npm run dev
```

2. Acesse `http://localhost:5173/produtos` no seu navegador. Você verá a lista de produtos.

3. Clique em qualquer link de produto.

- Observe como a URL muda (ex: para `http://localhost:5173/produtos/1`).

- O componente `ProductDetail` será renderizado, e ele usará o `useParams()` para pegar o `id` da URL (`1` neste exemplo) e exibir os detalhes do "Notebook Gamer".

### Cenários Avançados com `useParams()`

- **Múltiplos Parâmetros**: Se sua rota for `/blog/:ano/:mes/:slug`, o `useParams()` retornará `{ ano: '2023', mes: '07', slug: 'meu-primeiro-post' }`.

- **IDs Numéricos**: Lembre-se que os parâmetros extraídos por `useParams()` são sempre strings. Se você precisar usá-los como números (para cálculos ou busca em arrays/bancos de dados), converta-os (ex: `Number(id)` ou `parseInt(id, 10)`).

O `useParams()` é a espinha dorsal para criar páginas dinâmicas e reutilizáveis, onde o conteúdo exibido depende diretamente de um identificador na URL.

### [Voltar ao README Principal](../README.md)
