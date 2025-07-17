# Dominando o Hook useLocation() no React Router DOM

Enquanto o `useParams()` te dá informações sobre os parâmetros da URL, e o `useNavigate()` te permite mudar de rota, o `useLocation()` te entrega um objeto que representa a localização atual da sua aplicação. Pense nele como uma fotografia do estado da URL naquele momento.

## O Que É e Para Que Serve `useLocation()`?

O `useLocation()` é um hook que retorna o objeto `location` do React Router DOM. Este objeto contém informações detalhadas sobre a URL atual, como:

- `pathname`: O caminho da URL (ex: `/produtos/123`, /sobre).

- `search`: A string de consulta (query string) da URL (ex: `?categoria=eletronicos&ordenar=preco`).

- `hash`: O fragmento de hash da URL (ex: `#secao-contato`).

- `state`: Qualquer dado de estado que foi passado para a rota usando `Maps(path, { state: someData })`.

- `key`: Uma string única para a localização, útil para identificar entradas no histórico.

### Ele é útil para:

- **Acessar Query Parameters**: Extrair valores de `?chave=valor` na URL.

- **Passar Dados Não-URL**: Receber dados passados via `Maps` que não aparecem na URL.

- **Efeitos Colaterais ao Mudar de Rota**: Disparar ações (como scrollar para o topo) sempre que a rota muda.

- **Análises/Logs**: Registrar a rota atual para fins de análise.

### Sintaxe Básica

1. #### Importe o `useLocation` do `react-router-dom`:

```
import { useLocation } from 'react-router-dom';
```

2. #### Chame o hook dentro do seu componente funcional:

```
const location = useLocation();
console.log(location.pathname); // Ex: /meu-caminho
console.log(location.search);   // Ex: ?param=valor
console.log(location.state);    // Ex: { deOndeVeio: 'home' }
```

## Exemplo Prático: Filtragem por Query Parameters e Dados de Estado

Vamos aprimorar o exemplo da lista de produtos para incluir filtros via query parameters e passar uma mensagem de "compra bem-sucedida" via `state`.

### Passo 1: Configurar as Rotas no `App.jsx`

Vamos manter as rotas de produtos e adicionar uma rota para "confirmação de compra".

```
// src/App.jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';
import ProductList from './pages/ProductList';
import ProductDetail from './pages/ProductDetail';
import ConfirmationPage from './pages/ConfirmationPage'; // Novo componente

function App() {
  return (
    <BrowserRouter>
      <nav style={{ padding: '10px', background: '#f0f0f0' }}>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/produtos">Todos os Produtos</Link></li>
          <li><Link to="/produtos?category=eletronicos">Eletrônicos</Link></li> {/* Novo link com query param */}
          <li><Link to="/produtos?category=livros">Livros</Link></li>           {/* Novo link com query param */}
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<h1>Bem-vindo à Loja (Home)!</h1>} />
        <Route path="/produtos" element={<ProductList />} />
        <Route path="/produtos/:id" element={<ProductDetail />} />
        <Route path="/confirmacao" element={<ConfirmationPage />} /> {/* Nova rota */}
        <Route path="*" element={<h2>Página Não Encontrada!</h2>} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### Passo 2: Aprimorar `ProductList.jsx` para usar `useLocation()` (para Query Parameters)

Agora, `ProductList` vai ler o `category` do `search` da URL.

```
// src/pages/ProductList.jsx
import React, { useState, useEffect } from 'react';
import { Link, useLocation } from 'react-router-dom'; // Importe useLocation

// Dados simulados de produtos
const allProducts = [
  { id: '1', name: 'Notebook Gamer', price: 5000, category: 'eletronicos' },
  { id: '2', name: 'Monitor Ultrawide', price: 1500, category: 'eletronicos' },
  { id: '3', name: 'Teclado Mecânico', price: 300, category: 'eletronicos' },
  { id: '4', name: 'Mouse RGB', price: 150, category: 'eletronicos' },
  { id: '5', name: 'Livro: O Hobbit', price: 50, category: 'livros' },
  { id: '6', name: 'Livro: O Senhor dos Anéis', price: 80, category: 'livros' },
];

function ProductList() {
  const location = useLocation(); // 1. Obtenha o objeto location
  const [filteredProducts, setFilteredProducts] = useState([]);

  useEffect(() => {
    // 2. Crie um objeto URLSearchParams para parsear a query string
    const queryParams = new URLSearchParams(location.search);
    const categoryFilter = queryParams.get('category'); // 3. Obtenha o valor do parâmetro 'category'

    if (categoryFilter) {
      const filtered = allProducts.filter(
        (p) => p.category === categoryFilter
      );
      setFilteredProducts(filtered);
    } else {
      setFilteredProducts(allProducts); // Se não houver filtro, mostra todos
    }
  }, [location.search]); // 4. Re-execute o efeito sempre que a query string mudar

  return (
    <div>
      <h1>
        Produtos
        {filteredProducts.length < allProducts.length &&
          ` (${filteredProducts[0]?.category || 'Categoria Indefinida'})`}
      </h1>
      {filteredProducts.length === 0 ? (
        <p>Nenhum produto encontrado nesta categoria.</p>
      ) : (
        <ul>
          {filteredProducts.map((product) => (
            <li key={product.id}>
              <Link to={`/produtos/${product.id}`}>
                {product.name} - R$ {product.price.toFixed(2)}
              </Link>
            </li>
          ))}
        </ul>
      )}
      <Link to="/produtos">Ver todos os produtos</Link>
    </div>
  );
}

export default ProductList;
```

### Passo 3: Aprimorar `ProductDetail.jsx` para "Comprar" e Usar `useNavigate` com `state`

Vamos adicionar um botão de compra que, ao ser clicado, redireciona para uma página de confirmação, passando dados de estado que não aparecem na URL.

```
// src/pages/ProductDetail.jsx
import React from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom'; // Importe useNavigate

// Dados simulados de produtos
const products = [
  { id: '1', name: 'Notebook Gamer', price: 5000, description: 'Notebook de alta performance para jogos.' },
  { id: '2', name: 'Monitor Ultrawide', price: 1500, description: 'Monitor com tela curva para imersão total.' },
  { id: '3', name: 'Teclado Mecânico', price: 300, description: 'Teclado com switches de alta durabilidade.' },
  { id: '4', name: 'Mouse RGB', price: 150, description: 'Mouse ergonômico com iluminação personalizável.' },
  { id: '5', name: 'Livro: O Hobbit', price: 50, category: 'livros' },
  { id: '6', name: 'Livro: O Senhor dos Anéis', price: 80, category: 'livros' },
];

function ProductDetail() {
  const { id } = useParams();
  const navigate = useNavigate(); // Inicializa useNavigate

  const product = products.find((p) => p.id === id);

  if (!product) {
    return (
      <div>
        <h2>Produto Não Encontrado</h2>
        <p>O produto com ID "{id}" não existe.</p>
        <Link to="/produtos">Voltar para a lista de produtos</Link>
      </div>
    );
  }

  const handleBuy = () => {
    // Simula uma compra e redireciona, passando dados no state
    alert(`Comprando ${product.name}!`);
    navigate('/confirmacao', {
      state: {
        productName: product.name,
        totalPrice: product.price,
        orderId: Math.floor(Math.random() * 1000000) // ID de pedido aleatório
      }
    });
  };

  return (
    <div>
      <h1>Detalhes do Produto: {product.name}</h1>
      <p><strong>ID:</strong> {product.id}</p>
      <p><strong>Preço:</strong> R$ {product.price.toFixed(2)}</p>
      <p><strong>Descrição:</strong> {product.description}</p>
      <button onClick={handleBuy}>Comprar Agora</button>
      <p><Link to="/produtos">Voltar para a lista de produtos</Link></p>
    </div>
  );
}

export default ProductDetail;
```

### Passo 4: Criar o Componente `ConfirmationPage.jsx` (Onde `useLocation` Recebe `state`)

Este componente vai usar o `useLocation()` para acessar os dados passados pelo `Maps` no `state`.

```
// src/pages/ConfirmationPage.jsx
import React from 'react';
import { useLocation, Link } from 'react-router-dom'; // Importe useLocation e Link

function ConfirmationPage() {
  const location = useLocation(); // Obtenha o objeto location
  const { productName, totalPrice, orderId } = location.state || {}; // Desestruture o state, com fallback vazio

  if (!productName || !totalPrice || !orderId) {
    // Se a página for acessada diretamente ou sem os dados de state
    return (
      <div>
        <h1>Confirmação de Compra</h1>
        <p>Parece que você acessou esta página diretamente ou sem os detalhes da compra.</p>
        <p><Link to="/produtos">Voltar para a loja</Link></p>
      </div>
    );
  }

  return (
    <div>
      <h1>Compra Confirmada!</h1>
      <p>Seu pedido **#{orderId}** foi realizado com sucesso.</p>
      <p>Item: **{productName}**</p>
      <p>Valor Total: **R$ {totalPrice.toFixed(2)}**</p>
      <p>Agradecemos a sua compra!</p>
      <p><Link to="/produtos">Continuar Comprando</Link></p>
    </div>
  );
}

export default ConfirmationPage;
```

### Passo 5: Testar o `useLocation()`

1. Inicie seu servidor de desenvolvimento:

```
npm run dev
```

2. Acesse `http://localhost:5173/` ou `http://localhost:5173/produtos`.

3. #### Teste os Query Parameters:

- Clique em "Eletrônicos" e veja a URL mudar para `/produtos?category=eletronicos` e a lista ser filtrada.

- Clique em "Livros" e veja a URL mudar para `/produtos?category=livros` e a lista ser filtrada.

- Clique em "Todos os Produtos" para ver a lista completa novamente.

4. #### Teste o state:

- Vá para `http://localhost:5173/produtos` e clique em um produto (ex: "Notebook Gamer").

- Clique no botão "Comprar Agora".

- Você será redirecionado para `/confirmacao`. Observe que a URL não tem os dados da compra, mas a `ConfirmationPage` os exibe porque foram passados no `state` do `useNavigate` e lidos pelo `useLocation`.

- Tente acessar `http://localhost:5173/confirmacao` diretamente e veja a mensagem de erro (pois não há `state`).

### Quando usar o useLocation()?

- **Leitura de Query Parameters**: Para filtros, paginação, ordenação, etc., que são passados na URL após o `?`.

- **Acesso a Dados de `state`**: Para passar informações entre rotas sem expô-las na URL (útil para mensagens de sucesso, detalhes de transação temporários, etc.).

- **Scroll para o Topo**: Um `useEffect` que detecta mudanças em `location.pathname` pode ser usado para rolar a janela para o topo em cada navegação.

- **Análises**: Enviar eventos para ferramentas de análise sempre que a rota muda.

O `useLocation()` te dá uma visibilidade completa sobre o "onde" da sua aplicação no momento atual, sendo uma ferramenta poderosa para criar UIs reativas à URL e para passar dados de forma mais flexível.

### [Voltar ao README Principal](../README.md)
