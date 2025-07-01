<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Fluxo de Caixa</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
            animation: fadeIn 0.3s ease;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        #salesChart, #cashFlowChart {
            background: white;
            border-radius: 0.5rem;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <header class="mb-8 text-center">
            <img src="https://storage.googleapis.com/workspace-0f70711f-8b4e-4d94-86f1-2a93ccde5887/image/99f834d1-e90d-4ba6-8ac5-89e7533aa755.png" alt="Logotipo da empresa com fundo azul e ícone de gráfico de barras com cifrão" class="mx-auto h-40 w-auto mb-4 rounded-lg" />
            <h1 class="text-4xl font-bold text-blue-600">Controle de Fluxo de Caixa</h1>
            <p class="mt-2 text-lg text-gray-600">Gerencie seus produtos, vendas e finanças em um só lugar</p>
        </header>

        <div class="bg-white rounded-lg shadow-lg overflow-hidden mb-8">
            <div class="flex border-b">
                <button onclick="openTab('dashboard')" class="tab-btn flex-1 py-4 px-6 font-medium text-center border-b-2 border-blue-500 text-blue-600">Dashboard</button>
                <button onclick="openTab('products')" class="tab-btn flex-1 py-4 px-6 font-medium text-center border-b-2 border-transparent hover:border-gray-300">Produtos</button>
                <button onclick="openTab('sales')" class="tab-btn flex-1 py-4 px-6 font-medium text-center border-b-2 border-transparent hover:border-gray-300">Vendas</button>
                <button onclick="openTab('cashflow')" class="tab-btn flex-1 py-4 px-6 font-medium text-center border-b-2 border-transparent hover:border-gray-300">Fluxo</button>
            </div>

            <!-- Dashboard Tab -->
            <div id="dashboard" class="tab-content active p-6">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
                    <div class="bg-gradient-to-r from-blue-500 to-blue-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Total de Produtos</h3>
                        <p class="text-3xl font-bold" id="totalProducts">0</p>
                    </div>
                    <div class="bg-gradient-to-r from-green-500 to-green-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Vendas Hoje</h3>
                        <p class="text-3xl font-bold" id="todaySales">R$ 0,00</p>
                    </div>
                    <div class="bg-gradient-to-r from-purple-500 to-purple-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Saldo Atual</h3>
                        <p class="text-3xl font-bold" id="currentBalance">R$ 0,00</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
                    <div class="bg-white p-4 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-4 text-gray-800">Últimas Vendas</h3>
                        <div class="overflow-x-auto">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Data</th>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total</th>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Itens</th>
                                    </tr>
                                </thead>
                                <tbody id="recentSalesTable" class="bg-white divide-y divide-gray-200">
                                    <!-- Recent sales will be inserted here -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-4 text-gray-800">Produtos Mais Vendidos</h3>
                        <div class="overflow-x-auto">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Produto</th>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Vendas</th>
                                        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total</th>
                                    </tr>
                                </thead>
                                <tbody id="topProductsTable" class="bg-white divide-y divide-gray-200">
                                    <!-- Top products will be inserted here -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <canvas id="salesChart" width="400" height="200" class="w-full h-auto"></canvas>
            </div>

            <!-- Products Tab -->
            <div id="products" class="tab-content p-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Gestão de Produtos</h2>
                    <button onclick="openNewProductModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition duration-200">
                        + Adicionar Produto
                    </button>
                </div>

                <div class="bg-white overflow-hidden shadow rounded-lg mb-6">
                    <div class="px-4 py-5 sm:p-6">
                        <div class="flex flex-col md:flex-row md:items-center md:justify-between">
                            <div class="relative w-full md:w-64 mb-4 md:mb-0">
                                <input type="text" id="productSearch" placeholder="Buscar produto..." class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" />
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                    <svg class="h-5 w-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
                                    </svg>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Código</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Nome</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Preço</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Estoque</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                            </tr>
                        </thead>
                        <tbody id="productsTable" class="bg-white divide-y divide-gray-200">
                            <!-- Products will be inserted here -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Sales Tab -->
            <div id="sales" class="tab-content p-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Registro de Vendas</h2>
                    <button onclick="openNewSaleModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition duration-200">
                        + Nova Venda
                    </button>
                </div>

                <div class="mb-6 flex flex-col md:flex-row gap-4">
                    <div class="w-full md:w-1/2">
                        <label for="saleDateFilter" class="block text-sm font-medium text-gray-700 mb-1">Filtrar por data:</label>
                        <input type="date" id="saleDateFilter" class="w-full p-2 border border-gray-300 rounded-lg" />
                    </div>
                    <div class="w-full md:w-1/2">
                        <label class="block text-sm font-medium text-gray-700 mb-1">Ações</label>
                        <button onclick="filterSales()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition duration-200 mr-2">
                            Aplicar Filtro
                        </button>
                        <button onclick="clearSalesFilter()" class="bg-gray-200 hover:bg-gray-300 text-gray-800 px-4 py-2 rounded-lg transition duration-200">
                            Limpar
                        </button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Data</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Nº Venda</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Itens</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Pagamento</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                            </tr>
                        </thead>
                        <tbody id="salesTable" class="bg-white divide-y divide-gray-200">
                            <!-- Sales will be inserted here -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Cash Flow Tab -->
            <div id="cashflow" class="tab-content p-6">
                <div class="flex justify-between items-center mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Fluxo de Caixa</h2>
                    <div class="flex gap-2">
                        <select id="cashflowPeriod" class="border border-gray-300 rounded-lg p-2">
                            <option value="day">Hoje</option>
                            <option value="week">Esta Semana</option>
                            <option value="month">Este Mês</option>
                        </select>
                        <button onclick="updateCashFlow()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition duration-200">
                            Atualizar
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
                    <div class="bg-gradient-to-r from-blue-500 to-blue-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Entradas</h3>
                        <p class="text-3xl font-bold" id="inflows">R$ 0,00</p>
                    </div>
                    <div class="bg-gradient-to-r from-red-500 to-red-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Saídas</h3>
                        <p class="text-3xl font-bold" id="outflows">R$ 0,00</p>
                    </div>
                    <div class="bg-gradient-to-r from-purple-500 to-purple-600 text-white p-6 rounded-lg shadow">
                        <h3 class="text-xl font-semibold mb-2">Saldo</h3>
                        <p class="text-3xl font-bold" id="cashflowBalance">R$ 0,00</p>
                    </div>
                </div>

                <canvas id="cashFlowChart" width="400" height="200" class="w-full h-auto"></canvas>
            </div>
        </div>
    </div>

    <!-- Product Modal -->
    <div id="productModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-md">
            <div class="flex justify-between items-center border-b px-6 py-4">
                <h3 class="text-xl font-semibold text-gray-800" id="productModalTitle">Adicionar Produto</h3>
                <button onclick="closeModal('productModal')" class="text-gray-500 hover:text-gray-700">
                    <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                </button>
            </div>
            <form id="productForm" onsubmit="saveProduct(event)" class="p-6">
                <input type="hidden" id="productId">
                <div class="mb-4">
                    <label for="productName" class="block text-sm font-medium text-gray-700 mb-1">Nome do Produto</label>
                    <input type="text" id="productName" class="w-full p-2 border border-gray-300 rounded-lg" required />
                </div>
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label for="productPrice" class="block text-sm font-medium text-gray-700 mb-1">Preço (R$)</label>
                        <input type="number" id="productPrice" step="0.01" min="0" class="w-full p-2 border border-gray-300 rounded-lg" required />
                    </div>
                    <div>
                        <label for="productQuantity" class="block text-sm font-medium text-gray-700 mb-1">Estoque</label>
                        <input type="number" id="productQuantity" min="0" class="w-full p-2 border border-gray-300 rounded-lg" required />
                    </div>
                </div>
                <div class="flex justify-end space-x-3 pt-4">
                    <button type="button" onclick="closeModal('productModal')" class="px-4 py-2 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-50">Cancelar</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">Salvar</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Sale Modal -->
    <div id="saleModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-2xl">
            <div class="flex justify-between items-center border-b px-6 py-4">
                <h3 class="text-xl font-semibold text-gray-800">Nova Venda</h3>
                <button onclick="closeModal('saleModal')" class="text-gray-500 hover:text-gray-700">
                    <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                </button>
            </div>
            <form id="saleForm" onsubmit="saveSale(event)" class="p-6">
                <div class="mb-6">
                    <label for="saleDate" class="block text-sm font-medium text-gray-700 mb-1">Data da Venda</label>
                    <input type="datetime-local" id="saleDate" class="w-full p-2 border border-gray-300 rounded-lg" required />
                </div>
                
                <div class="mb-6">
                    <div class="flex items-center justify-between mb-3">
                        <h4 class="text-lg font-medium text-gray-800">Itens da Venda</h4>
                        <div class="flex items-center">
                            <select id="productSelector" class="border border-gray-300 rounded-l-lg p-2">
                                <!-- Products will be inserted here -->
                            </select>
                            <button type="button" onclick="addItemToSale()" class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-2 rounded-r-lg">
                                Adicionar
                            </button>
                        </div>
                    </div>
                    
                    <div class="overflow-x-auto">
                        <table class="min-w-full divide-y divide-gray-200">
                            <thead class="bg-gray-50">
                                <tr>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Produto</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Preço Unit.</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Qtd.</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                                </tr>
                            </thead>
                            <tbody id="saleItemsTable" class="bg-white divide-y divide-gray-200">
                                <!-- Sale items will be inserted here -->
                            </tbody>
                        </table>
                    </div>
                </div>
                
                <div class="flex flex-col md:flex-row justify-between items-center py-4 px-4 border-t">
                    <div class="mb-4 md:mb-0 w-full md:w-1/3">
                        <label class="block text-sm font-medium text-gray-700 mb-1">Forma de Pagamento</label>
                        <select id="paymentMethod" class="w-full p-2 border border-gray-300 rounded-lg" required>
                            <option value="dinheiro">Dinheiro</option>
                            <option value="pix">PIX</option>
                            <option value="credito">Cartão de Crédito</option>
                            <option value="debito">Cartão de Débito</option>
                        </select>
                    </div>
                    <div class="text-lg mr-6">
                        <span class="font-medium">Total:</span>
                        <span class="font-bold ml-2" id="saleTotal">R$ 0,00</span>
                    </div>
                    <button type="button" onclick="closeModal('saleModal')" class="px-4 py-2 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-50 mr-2">Cancelar</button>
                    <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">Finalizar Venda</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Sale Detail Modal -->
    <div id="saleDetailModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-md">
            <div class="flex justify-between items-center border-b px-6 py-4">
                <h3 class="text-xl font-semibold text-gray-800">Detalhes da Venda</h3>
                <button onclick="closeModal('saleDetailModal')" class="text-gray-500 hover:text-gray-700">
                    <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                </button>
            </div>
            <div class="p-6">
                <div class="mb-6">
                    <h4 class="text-sm font-medium text-gray-500">Nº Venda</h4>
                    <p class="text-lg font-semibold" id="detailSaleId"></p>
                </div>
                <div class="mb-6">
                    <h4 class="text-sm font-medium text-gray-500">Data</h4>
                    <p class="text-lg font-semibold" id="detailSaleDate"></p>
                </div>
                
                <div class="mb-6">
                    <h4 class="text-lg font-medium text-gray-800 border-b pb-2 mb-3">Itens</h4>
                    <div class="space-y-4">
                        <div id="detailSaleItems">
                            <!-- Sale items will be inserted here -->
                        </div>
                    </div>
                </div>
                
                <div class="flex justify-between items-center py-4 px-4 border-t">
                    <div>
                        <h4 class="text-sm font-medium text-gray-500">Forma de Pagamento</h4>
                        <p class="text-lg font-semibold" id="detailPaymentMethod"></p>
                    </div>
                    <div class="text-lg">
                        <span class="font-medium">Total:</span>
                        <span class="font-bold ml-2" id="detailSaleTotal"></span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        // Data storage using localStorage
        let products = JSON.parse(localStorage.getItem('products')) || [];
        let sales = JSON.parse(localStorage.getItem('sales')) || [];
        let cashFlow = JSON.parse(localStorage.getItem('cashFlow')) || { balance: 0 };
        
        // DOM Elements
        const productSearch = document.getElementById('productSearch');
        const productsTable = document.getElementById('productsTable');
        const salesTable = document.getElementById('salesTable');
        const totalProducts = document.getElementById('totalProducts');
        const todaySales = document.getElementById('todaySales');
        const currentBalance = document.getElementById('currentBalance');
        const recentSalesTable = document.getElementById('recentSalesTable');
        const topProductsTable = document.getElementById('topProductsTable');
        const inflows = document.getElementById('inflows');
        const outflows = document.getElementById('outflows');
        const cashflowBalance = document.getElementById('cashflowBalance');
        
        // Initialize charts
        let salesChart, cashFlowChart;
        
        // Initialize the application
        document.addEventListener('DOMContentLoaded', function() {
            loadProducts();
            loadSales();
            updateDashboard();
            updateCashFlow();
            initCharts();
            
            // Add event listeners
            productSearch.addEventListener('input', filterProducts);
        });
        
        // Tab navigation
        function openTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(tab => {
                tab.classList.remove('active');
            });
            
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('border-blue-500', 'text-blue-600');
                btn.classList.add('border-transparent');
            });
            
            document.getElementById(tabId).classList.add('active');
            event.currentTarget.classList.add('border-blue-500', 'text-blue-600');
            event.currentTarget.classList.remove('border-transparent');
        }
        
        // Modal functions
        function openNewProductModal() {
            document.getElementById('productModalTitle').textContent = 'Adicionar Produto';
            document.getElementById('productId').value = '';
            document.getElementById('productForm').reset();
            document.getElementById('productModal').classList.remove('hidden');
        }
        
        function openEditProductModal(id) {
            const product = products.find(p => p.id === id);
            if (product) {
                document.getElementById('productModalTitle').textContent = 'Editar Produto';
                document.getElementById('productId').value = product.id;
                document.getElementById('productName').value = product.name;
                document.getElementById('productPrice').value = product.price;
                document.getElementById('productQuantity').value = product.quantity;
                document.getElementById('productModal').classList.remove('hidden');
            }
        }
        
        function openNewSaleModal() {
            document.getElementById('saleForm').reset();
            document.getElementById('saleDate').value = new Date().toISOString().slice(0, 16);
            document.getElementById('saleItemsTable').innerHTML = '';
            document.getElementById('saleTotal').textContent = 'R$ 0,00';
            
            // Populate product selector
            const productSelector = document.getElementById('productSelector');
            productSelector.innerHTML = '<option value="">Selecione um produto</option>';
            products.forEach(product => {
                if (product.quantity > 0) {
                    const option = document.createElement('option');
                    option.value = product.id;
                    option.textContent = `${product.name} (R$ ${formatCurrency(product.price)} - Estoque: ${product.quantity})`;
                    productSelector.appendChild(option);
                }
            });
            
            document.getElementById('saleModal').classList.remove('hidden');
        }
        
        function getPaymentMethodName(method) {
            const methods = {
                'dinheiro': 'Dinheiro',
                'pix': 'PIX',
                'credito': 'Cartão Crédito',
                'debito': 'Cartão Débito'
            };
            return methods[method] || method;
        }

        function openSaleDetailModal(saleId) {
            const sale = sales.find(s => s.id === saleId);
            if (sale) {
                document.getElementById('detailSaleId').textContent = sale.id;
                document.getElementById('detailSaleDate').textContent = formatDateTime(sale.date);
                document.getElementById('detailSaleTotal').textContent = formatCurrency(sale.total);
                document.getElementById('detailPaymentMethod').textContent = getPaymentMethodName(sale.paymentMethod);
                
                // Populate sale items
                const detailSaleItems = document.getElementById('detailSaleItems');
                detailSaleItems.innerHTML = '';
                
                sale.items.forEach(item => {
                    const product = products.find(p => p.id === item.productId);
                    if (product) {
                        const itemDiv = document.createElement('div');
                        itemDiv.className = 'flex justify-between items-center border-b pb-2';
                        itemDiv.innerHTML = `
                            <div>
                                <p class="font-medium">${product.name}</p>
                                <p class="text-sm text-gray-500">${item.quantity} x R$ ${formatCurrency(item.price)}</p>
                            </div>
                            <p class="font-medium">R$ ${formatCurrency(item.quantity * item.price)}</p>
                        `;
                        detailSaleItems.appendChild(itemDiv);
                    }
                });
                
                document.getElementById('saleDetailModal').classList.remove('hidden');
            }
        }
        
        function closeModal(modalId) {
            document.getElementById(modalId).classList.add('hidden');
        }
        
        // Product functions
        function saveProduct(e) {
            e.preventDefault();
            
            const id = document.getElementById('productId').value;
            const name = document.getElementById('productName').value;
            const price = parseFloat(document.getElementById('productPrice').value);
            const quantity = parseInt(document.getElementById('productQuantity').value);
            
            if (id) {
                // Update existing product
                const index = products.findIndex(p => p.id === id);
                if (index !== -1) {
                    products[index] = { ...products[index], name, price, quantity };
                }
            } else {
                // Add new product
                const newProduct = {
                    id: generateId(),
                    name,
                    price,
                    quantity
                };
                products.push(newProduct);
            }
            
            saveProducts();
            loadProducts();
            closeModal('productModal');
            updateDashboard();
        }
        
        function deleteProduct(id) {
            if (confirm('Tem certeza que deseja excluir este produto? Esta ação não pode ser desfeita.')) {
                products = products.filter(product => product.id !== id);
                saveProducts();
                loadProducts();
                updateDashboard();
            }
        }
        
        function loadProducts() {
            productsTable.innerHTML = '';
            
            if (products.length === 0) {
                productsTable.innerHTML = '<tr><td colspan="5" class="px-6 py-4 text-center text-gray-500">Nenhum produto cadastrado</td></tr>';
                return;
            }
            
            products.forEach(product => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${product.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${product.name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(product.price)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${product.quantity}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                        <button onclick="openEditProductModal('${product.id}')" class="text-blue-600 hover:text-blue-900 mr-3">Editar</button>
                        <button onclick="deleteProduct('${product.id}')" class="text-red-600 hover:text-red-900">Excluir</button>
                    </td>
                `;
                productsTable.appendChild(tr);
            });
        }
        
        function filterProducts() {
            const searchTerm = productSearch.value.toLowerCase();
            const rows = productsTable.querySelectorAll('tr');
            
            rows.forEach(row => {
                const name = row.querySelector('td:nth-child(2)').textContent.toLowerCase();
                if (name.includes(searchTerm)) {
                    row.style.display = '';
                } else {
                    row.style.display = 'none';
                }
            });
        }
        
        // Sale functions
        function addItemToSale() {
            const productId = document.getElementById('productSelector').value;
            if (!productId) return;
            
            const product = products.find(p => p.id === productId);
            if (!product) return;
            
            // Check if product already in sale
            const existingRow = document.querySelector(`#saleItemsTable tr[data-product-id="${productId}"]`);
            if (existingRow) {
                // Increase quantity
                const qtyInput = existingRow.querySelector('input[type="number"]');
                if (parseInt(qtyInput.value) < product.quantity) {
                    qtyInput.value = parseInt(qtyInput.value) + 1;
                    updateSaleItemTotal(existingRow, product.price);
                } else {
                    alert('Quantidade em estoque insuficiente');
                }
                return;
            }
            
            // Add new row
            const tr = document.createElement('tr');
            tr.dataset.productId = productId;
            tr.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${product.name}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(product.price)}</td>
                <td class="px-6 py-4 whitespace-nowrap">
                    <input type="number" min="1" max="${product.quantity}" value="1" onchange="updateSaleItemQuantity(this, ${product.price})" class="w-16 p-1 border border-gray-300 rounded">
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 item-total">R$ ${formatCurrency(product.price)}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <button onclick="removeSaleItem(this)" class="text-red-600 hover:text-red-900">Remover</button>
                </td>
            `;
            document.getElementById('saleItemsTable').appendChild(tr);
            updateSaleTotal();
        }
        
        function updateSaleItemQuantity(input, price) {
            const max = parseInt(input.max);
            if (parseInt(input.value) > max) {
                input.value = max;
            } else if (parseInt(input.value) < 1) {
                input.value = 1;
            }
            
            const row = input.closest('tr');
            updateSaleItemTotal(row, price);
        }
        
        function updateSaleItemTotal(row, price) {
            const qty = row.querySelector('input[type="number"]').value;
            const totalCell = row.querySelector('.item-total');
            const total = parseFloat(price) * parseInt(qty);
            totalCell.textContent = `R$ ${formatCurrency(total)}`;
            updateSaleTotal();
        }
        
        function removeSaleItem(button) {
            const row = button.closest('tr');
            row.remove();
            updateSaleTotal();
        }
        
        function updateSaleTotal() {
            let total = 0;
            document.querySelectorAll('#saleItemsTable tr').forEach(row => {
                const totalText = row.querySelector('.item-total').textContent;
                const amount = parseFloat(totalText.replace('R$ ', '').replace(',', '.'));
                total += amount;
            });
            document.getElementById('saleTotal').textContent = `R$ ${formatCurrency(total)}`;
        }
        
        function saveSale(e) {
            e.preventDefault();
            
            const date = document.getElementById('saleDate').value;
            const items = [];
            
            document.querySelectorAll('#saleItemsTable tr').forEach(row => {
                const productId = row.dataset.productId;
                const qty = parseInt(row.querySelector('input[type="number"]').value);
                const price = parseFloat(row.querySelector('td:nth-child(2)').textContent.replace('R$ ', '').replace(',', '.'));
                
                items.push({
                    productId,
                    quantity: qty,
                    price
                });
            });
            
            if (items.length === 0) {
                alert('Adicione pelo menos um item à venda');
                return;
            }
            
            const totalText = document.getElementById('saleTotal').textContent;
            const total = parseFloat(totalText.replace('R$ ', '').replace(',', '.'));
            
            const paymentMethod = document.getElementById('paymentMethod').value;
            
            const newSale = {
                id: generateId(),
                date,
                items,
                total,
                paymentMethod
            };
            
            sales.push(newSale);
            
            // Update product quantities
            items.forEach(item => {
                const product = products.find(p => p.id === item.productId);
                if (product) {
                    product.quantity -= item.quantity;
                }
            });
            
            // Update cash flow
            cashFlow.balance += total;
            
            saveSales();
            saveProducts();
            saveCashFlow();
            loadSales();
            closeModal('saleModal');
            updateDashboard();
            updateCashFlow();
        }
        
        function loadSales() {
            salesTable.innerHTML = '';
            
            if (sales.length === 0) {
                salesTable.innerHTML = '<tr><td colspan="5" class="px-6 py-4 text-center text-gray-500">Nenhuma venda registrada</td></tr>';
                return;
            }
            
            // Sort by date (newest first)
            const sortedSales = [...sales].sort((a, b) => new Date(b.date) - new Date(a.date));
            
            sortedSales.forEach(sale => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${formatDate(sale.date)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${sale.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${sale.items.length}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(sale.total)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${getPaymentMethodName(sale.paymentMethod)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                        <button onclick="openSaleDetailModal('${sale.id}')" class="text-blue-600 hover:text-blue-900">Detalhes</button>
                    </td>
                `;
                salesTable.appendChild(tr);
            });
        }
        
        function filterSales() {
            const dateFilter = document.getElementById('saleDateFilter').value;
            if (!dateFilter) return;
            
            const filteredSales = sales.filter(sale => {
                const saleDate = new Date(sale.date);
                const filterDate = new Date(dateFilter);
                return (
                    saleDate.getDate() === filterDate.getDate() &&
                    saleDate.getMonth() === filterDate.getMonth() &&
                    saleDate.getFullYear() === filterDate.getFullYear()
                );
            });
            
            salesTable.innerHTML = '';
            
            if (filteredSales.length === 0) {
                salesTable.innerHTML = '<tr><td colspan="5" class="px-6 py-4 text-center text-gray-500">Nenhuma venda encontrada para esta data</td></tr>';
                return;
            }
            
            filteredSales.forEach(sale => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${formatDate(sale.date)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${sale.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${sale.items.length}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(sale.total)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                        <button onclick="openSaleDetailModal('${sale.id}')" class="text-blue-600 hover:text-blue-900">Detalhes</button>
                    </td>
                `;
                salesTable.appendChild(tr);
            });
        }
        
        function clearSalesFilter() {
            document.getElementById('saleDateFilter').value = '';
            loadSales();
        }
        
        // Dashboard functions
        function updateDashboard() {
            // Total products
            totalProducts.textContent = products.length;
            
            // Today's sales
            const today = new Date();
            today.setHours(0, 0, 0, 0);
            
            const todaySalesTotal = sales.reduce((total, sale) => {
                const saleDate = new Date(sale.date);
                if (saleDate >= today) {
                    return total + sale.total;
                }
                return total;
            }, 0);
            
            todaySales.textContent = `R$ ${formatCurrency(todaySalesTotal)}`;
            
            // Current balance
            currentBalance.textContent = `R$ ${formatCurrency(cashFlow.balance)}`;
            
            // Recent sales (last 5)
            recentSalesTable.innerHTML = '';
            const recentSales = [...sales]
                .sort((a, b) => new Date(b.date) - new Date(a.date))
                .slice(0, 5);
                
            if (recentSales.length === 0) {
                recentSalesTable.innerHTML = '<tr><td colspan="3" class="px-6 py-4 text-center text-gray-500">Nenhuma venda recente</td></tr>';
            } else {
                recentSales.forEach(sale => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${formatDateTime(sale.date)}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(sale.total)}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${sale.items.length}</td>
                    `;
                    recentSalesTable.appendChild(tr);
                });
            }
            
            // Top products (by sales volume)
            topProductsTable.innerHTML = '';
            const productSales = {};
            
            sales.forEach(sale => {
                sale.items.forEach(item => {
                    if (!productSales[item.productId]) {
                        productSales[item.productId] = { quantity: 0, total: 0 };
                    }
                    productSales[item.productId].quantity += item.quantity;
                    productSales[item.productId].total += item.quantity * item.price;
                });
            });
            
            const topProducts = Object.entries(productSales)
                .map(([productId, data]) => {
                    const product = products.find(p => p.id === productId);
                    return {
                        id: productId,
                        name: product ? product.name : 'Produto não encontrado',
                        quantity: data.quantity,
                        total: data.total
                    };
                })
                .sort((a, b) => b.quantity - a.quantity)
                .slice(0, 5);
                
            if (topProducts.length === 0) {
                topProductsTable.innerHTML = '<tr><td colspan="3" class="px-6 py-4 text-center text-gray-500">Nenhum produto vendido ainda</td></tr>';
            } else {
                topProducts.forEach(product => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${product.name}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${product.quantity}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">R$ ${formatCurrency(product.total)}</td>
                    `;
                    topProductsTable.appendChild(tr);
                });
            }
            
            // Update charts
            updateCharts();
        }
        
        // Cash Flow functions
        function updateCashFlow() {
            const period = document.getElementById('cashflowPeriod').value;
            let startDate, endDate = new Date();
            
            switch (period) {
                case 'day':
                    startDate = new Date();
                    startDate.setHours(0, 0, 0, 0);
                    break;
                case 'week':
                    startDate = new Date();
                    startDate.setDate(startDate.getDate() - startDate.getDay());
                    startDate.setHours(0, 0, 0, 0);
                    break;
                case 'month':
                    startDate = new Date();
                    startDate.setDate(1);
                    startDate.setHours(0, 0, 0, 0);
                    break;
            }
            
            const periodSales = sales.filter(sale => {
                const saleDate = new Date(sale.date);
                return saleDate >= startDate && saleDate <= endDate;
            });
            
            const totalInflows = periodSales.reduce((total, sale) => total + sale.total, 0);
            const totalOutflows = 0; // In a real app, this would come from expenses module
            
            inflows.textContent = `R$ ${formatCurrency(totalInflows)}`;
            outflows.textContent = `R$ ${formatCurrency(totalOutflows)}`;
            cashflowBalance.textContent = `R$ ${formatCurrency(totalInflows - totalOutflows)}`;
            
            updateCashFlowChart(periodSales, startDate, endDate);
        }
        
        // Chart functions
        function initCharts() {
            // Sales chart
            const salesCtx = document.getElementById('salesChart').getContext('2d');
            salesChart = new Chart(salesCtx, {
                type: 'bar',
                data: {
                    labels: [],
                    datasets: [{
                        label: 'Vendas (R$)',
                        data: [],
                        backgroundColor: 'rgba(59, 130, 246, 0.5)',
                        borderColor: 'rgba(59, 130, 246, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Vendas Diárias (Últimos 7 dias)'
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    }
                }
            });
            
            // Cash flow chart
            const cashFlowCtx = document.getElementById('cashFlowChart').getContext('2d');
            cashFlowChart = new Chart(cashFlowCtx, {
                type: 'line',
                data: {
                    labels: [],
                    datasets: [
                        {
                            label: 'Entradas (R$)',
                            data: [],
                            backgroundColor: 'rgba(16, 185, 129, 0.2)',
                            borderColor: 'rgba(16, 185, 129, 1)',
                            borderWidth: 2,
                            tension: 0.1
                        },
                        {
                            label: 'Saídas (R$)',
                            data: [],
                            backgroundColor: 'rgba(239, 68, 68, 0.2)',
                            borderColor: 'rgba(239, 68, 68, 1)',
                            borderWidth: 2,
                            tension: 0.1
                        }
                    ]
                },
                options: {
                    responsive: true,
                    plugins: {
                        title: {
                            display: true,
                            text: 'Fluxo de Caixa'
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    }
                }
            });
        }
        
        function updateCharts() {
            // Update sales chart (last 7 days)
            const today = new Date();
            today.setHours(0, 0, 0, 0);
            
            const last7Days = Array.from({ length: 7 }, (_, i) => {
                const date = new Date(today);
                date.setDate(date.getDate() - i);
                return date;
            }).reverse();
            
            const salesByDay = last7Days.map(date => {
                const salesTotal = sales.reduce((total, sale) => {
                    const saleDate = new Date(sale.date);
                    if (
                        saleDate.getDate() === date.getDate() &&
                        saleDate.getMonth() === date.getMonth() &&
                        saleDate.getFullYear() === date.getFullYear()
                    ) {
                        return total + sale.total;
                    }
                    return total;
                }, 0);
                
                return salesTotal;
            });
            
            salesChart.data.labels = last7Days.map(d => formatDate(d.toISOString()));
            salesChart.data.datasets[0].data = salesByDay;
            salesChart.update();
        }
        
        function updateCashFlowChart(periodSales, startDate, endDate) {
            // Group sales by day/week/month depending on period
            const period = document.getElementById('cashflowPeriod').value;
            let labels = [];
            let inflowsData = [];
            let outflowsData = [];
            
            if (period === 'day') {
                // By hour
                labels = Array.from({ length: 24 }, (_, i) => `${i}h`);
                inflowsData = Array(24).fill(0);
                
                periodSales.forEach(sale => {
                    const saleDate = new Date(sale.date);
                    const hour = saleDate.getHours();
                    inflowsData[hour] += sale.total;
                });
                
                cashFlowChart.options.plugins.title.text = 'Fluxo de Caixa (Hoje por Hora)';
            } else if (period === 'week') {
                // By day
                const days = ['Dom', 'Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sáb'];
                labels = days;
                inflowsData = Array(7).fill(0);
                
                periodSales.forEach(sale => {
                    const saleDate = new Date(sale.date);
                    const dayOfWeek = saleDate.getDay();
                    inflowsData[dayOfWeek] += sale.total;
                });
                
                cashFlowChart.options.plugins.title.text = 'Fluxo de Caixa (Esta Semana)';
            } else {
                // By day of month
                const daysInMonth = new Date(
                    endDate.getFullYear(),
                    endDate.getMonth() + 1,
                    0
                ).getDate();
                
                labels = Array.from({ length: daysInMonth }, (_, i) => (i + 1).toString());
                inflowsData = Array(daysInMonth).fill(0);
                
                periodSales.forEach(sale => {
                    const saleDate = new Date(sale.date);
                    const dayOfMonth = saleDate.getDate() - 1;
                    if (dayOfMonth >= 0 && dayOfMonth < daysInMonth) {
                        inflowsData[dayOfMonth] += sale.total;
                    }
                });
                
                cashFlowChart.options.plugins.title.text = 'Fluxo de Caixa (Este Mês)';
            }
            
            // Outflows would come from expenses in a real app
            outflowsData = Array(inflowsData.length).fill(0);
            
            cashFlowChart.data.labels = labels;
            cashFlowChart.data.datasets[0].data = inflowsData;
            cashFlowChart.data.datasets[1].data = outflowsData;
            cashFlowChart.update();
        }
        
        // Utility functions
        function generateId() {
            return Date.now().toString(36) + Math.random().toString(36).substr(2);
        }
        
        function formatCurrency(value) {
            return parseFloat(value).toFixed(2).replace('.', ',');
        }
        
        function formatDate(dateString) {
            const date = new Date(dateString);
            return date.toLocaleDateString('pt-BR');
        }
        
        function formatDateTime(dateTimeString) {
            const date = new Date(dateTimeString);
            return date.toLocaleString('pt-BR');
        }
        
        function saveProducts() {
            localStorage.setItem('products', JSON.stringify(products));
        }
        
        function saveSales() {
            localStorage.setItem('sales', JSON.stringify(sales));
        }
        
        function saveCashFlow() {
            localStorage.setItem('cashFlow', JSON.stringify(cashFlow));
        }
    </script>
</body>
</html>

