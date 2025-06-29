<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hortifruti - Sistema de Cadastro</title>
    <style>
        :root {
            --primary-color: #2ecc71;
            --secondary-color: #3498db;
            --dark-color: #2c3e50;
            --light-color: #ecf0f1;
            --danger-color: #e74c3c;
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f5f5f5;
            color: var(--dark-color);
            line-height: 1.6;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background-color: var(--primary-color);
            color: white;
            padding: 20px 0;
            text-align: center;
            border-radius: 8px 8px 0 0;
            margin-bottom: 30px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        
        h1 {
            font-size: 2.2rem;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
        }
        
        input, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 1rem;
        }
        
        button {
            background-color: var(--secondary-color);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1rem;
            transition: background-color 0.3s;
        }
        
        button:hover {
            background-color: #2980b9;
        }
        
        .product-list {
            margin-top: 30px;
        }
        
        .product-item {
            background-color: white;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 4px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .product-info {
            flex: 1;
        }
        
        .product-name {
            font-weight: 600;
            margin-bottom: 5px;
        }
        
        .product-details {
            display: flex;
            gap: 8px;
            font-size: 0.9rem;
            align-items: center;
        }
        
        .product-separator {
            color: #ddd;
        }
        
        .product-category {
            color: #7f8c8d;
        }
        
        .product-price {
            font-weight: bold;
            color: var(--primary-color);
        }
        
        .product-actions button {
            margin-left: 10px;
        }
        
        .completed {
            text-decoration: line-through;
            opacity: 0.7;
        }
        
        .btn-danger {
            background-color: var(--danger-color);
        }
        
        .btn-danger:hover {
            background-color: #c0392b;
        }
        
        .btn-edit {
            background-color: #95a5a6;
        }
        
        .btn-edit:hover {
            background-color: #7f8c8d;
        }
        
        .empty-state {
            text-align: center;
            padding: 40px 0;
            color: #7f8c8d;
        }
        
        .store-image {
            width: 100%;
            height: 200px;
            object-fit: cover;
            border-radius: 8px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Hortifruti</h1>
            <p>Sistema de Cadastro de Produtos</p>
        </header>
        
        <form id="productForm">
            <div class="form-group">
                <label for="productName">Nome do Produto</label>
                <input type="text" id="productName" required>
            </div>
            
            <div class="form-group">
                <label for="productCategory">Categoria</label>
                <select id="productCategory" required>
                    <option value="">Selecione uma categoria</option>
                    <option value="Frutas">Frutas</option>
                    <option value="Hortaliças">Hortaliças</option>
                    <option value="Temperos">Temperos</option>
                </select>
            </div>

            <div class="form-group">
                <label for="productPrice">Preço (R$)</label>
                <input type="number" id="productPrice" step="0.01" required>
            </div>
            
            <button type="submit">Cadastrar Produto</button>
        </form>
        
        <div class="product-list" id="productList">
            <h2>Produtos Cadastrados</h2>
            <div id="productsContainer">
                <p class="empty-state">Nenhum produto cadastrado ainda.</p>
            </div>
        </div>
    </div>
    
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const productForm = document.getElementById('productForm');
            const productsContainer = document.getElementById('productsContainer');
            
            // Array para armazenar os produtos
            let products = JSON.parse(localStorage.getItem('products')) || [];
            
            // Função para renderizar os produtos
            function renderProducts() {
                console.log('Renderizando produtos... Nº de produtos:', products.length);
                productsContainer.innerHTML = '';
                console.log('Produtos atuais:', products);
                
                if (products.length === 0) {
                    productsContainer.innerHTML = '<p class="empty-state">Nenhum produto cadastrado ainda.</p>';
                    return;
                }
                
                products.forEach((product, index) => {
                    const productItem = document.createElement('div');
                    productItem.className = `product-item ${product.completed ? 'completed' : ''}`;
                    
                    productItem.innerHTML = `
                        <div class="product-info">
                            <div class="product-name">${product.name}</div>
                            <div class="product-details">
                                <span class="product-category">${product.category}</span>
                                <span class="product-separator">|</span>
                                <span class="product-price">R$ ${product.price.toFixed(2).replace('.', ',')}</span>
                            </div>
                        </div>
                        <button class="btn-danger" onclick="removeProduct(${index})">Remover</button>
                    `;
                    
                    productsContainer.appendChild(productItem);
                });
            }
            
            // Função para adicionar um novo produto
            productForm.addEventListener('submit', function(e) {
                e.preventDefault();
                console.log('Formulário enviado');
                
                const productName = document.getElementById('productName').value.trim();
                const productCategory = document.getElementById('productCategory').value;
                const productPrice = document.getElementById('productPrice').value;

                console.log('Valores:', {productName, productCategory, productPrice});
                
                // Validation
                if (!productName) {
                    alert('Por favor, informe o nome do produto!');
                    return;
                }
                if (productCategory === "") {
                    alert('Por favor, selecione uma categoria!');
                    return;
                }
                if (!productPrice || parseFloat(productPrice) <= 0) {
                    alert('Por favor, informe um preço válido!');
                    return;
                }

                // Verificar se produto com mesmo nome (case-insensitive e trim) já existe
                const trimmedProductName = productName.trim();
                if (!trimmedProductName) {
                    alert('Por favor, informe um nome de produto válido!');
                    return;
                }
                
                const productExists = products.some(
                    p => p.name.toLowerCase().trim() === trimmedProductName.toLowerCase()
                );

                if (productExists) {
                    alert('Este produto já está cadastrado no sistema!\nNão é permitido cadastrar o mesmo produto novamente.');
                    return;
                }

                const newProduct = {
                    name: trimmedProductName,
                    category: productCategory,
                    price: parseFloat(productPrice),
                    completed: false
                };
                
                // Adiciona o novo produto ao array
                products.push(newProduct);
                localStorage.setItem('products', JSON.stringify(products));
                console.log('Produto cadastrado:', newProduct);
                console.log('Todos produtos:', products);
                
                // Limpa o formulário e renderiza a lista de produtos
                productForm.reset();
                renderProducts();
                // Show brief success message
                setTimeout(() => {
                    const successMsg = document.createElement('div');
                    successMsg.textContent = 'Produto cadastrado com sucesso!';
                    successMsg.style.color = 'green';
                    successMsg.style.marginTop = '10px';
                    productForm.appendChild(successMsg);
                    
                    setTimeout(() => successMsg.remove(), 3000);
                }, 0);
            });

            // Funções globais para manipulação dos produtos
            window.editProduct = function(index) {
                const originalProduct = products[index];
                document.getElementById('productName').value = originalProduct.name;
                document.getElementById('productCategory').value = originalProduct.category;
                document.getElementById('productPrice').value = originalProduct.price;
                
                // Remove the product being edited
                products.splice(index, 1);
                localStorage.setItem('products', JSON.stringify(products));
                renderProducts();
            };

            window.removeProduct = function(index) {
                if (confirm('Tem certeza que deseja remover este produto?')) {
                    products.splice(index, 1);
                    localStorage.setItem('products', JSON.stringify(products));
                    renderProducts();
                }
            };

            // Renderizar produtos ao carregar a página
            renderProducts();
        });
    </script>
</body>
</html>
