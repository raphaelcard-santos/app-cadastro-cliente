# app-cadastro-cliente
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Controle de Clientes e Pagamentos</title>
    <meta name="description" content="Sistema para controle de clientes, compras e pagamentos">
    <meta name="theme-color" content="#4F46E5">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="manifest" href="data:application/json;base64,ewogICJuYW1lIjogIlNpc3RlbWEgZGUgQ29udHJvbGUgZGUgQ2xpZW50ZXMiLAogICJzaG9ydF9uYW1lIjogIkNsaWVudGVzIiwKICAic3RhcnRfdXJsIjogIi4vIiwKICAiZGlzcGxheSI6ICJzdGFuZGFsb25lIiwKICAidGhlbWVfY29sb3IiOiAiIzRGNDZFNSIsCiAgImJhY2tncm91bmRfY29sb3IiOiAiI2ZmZmZmZiIKfQ==">
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.1.1/css/all.min.css">
    <style>
        .hidden {
            display: none;
        }
        .tab-active {
            border-bottom: 2px solid #4F46E5;
            color: #4F46E5;
        }
        .cliente-item:hover {
            background-color: #f3f4f6;
        }
        .compra-item:hover {
            background-color: #f3f4f6;
        }
        .pagamento-item:hover {
            background-color: #f3f4f6;
        }
        .paga {
            text-decoration: line-through;
            color: #6b7280;
        }
        /* Ajustes para PWA */
        @media (display-mode: standalone) {
            body {
                padding-top: env(safe-area-inset-top);
                padding-bottom: env(safe-area-inset-bottom);
            }
        }
        /* Animações */
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        .animate-fade-in {
            animation: fadeIn 0.3s ease-in-out;
        }
        .install-banner {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background-color: #4F46E5;
            color: white;
            padding: 1rem;
            z-index: 50;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <div id="install-banner" class="hidden install-banner">
        <div class="flex justify-between items-center">
            <div>
                <p class="font-bold">Instale este aplicativo no seu celular</p>
                <p class="text-sm">Adicione à tela inicial para acesso rápido</p>
            </div>
            <div class="flex">
                <button id="install-btn" class="bg-white text-indigo-600 px-3 py-1 rounded-md mr-2">Instalar</button>
                <button id="close-install-banner" class="text-white"><i class="fas fa-times"></i></button>
            </div>
        </div>
    </div>

    <div class="container mx-auto p-4">
        <div class="bg-white shadow-md rounded-lg p-5 mb-4">
            <h1 class="text-2xl md:text-3xl font-bold text-center text-indigo-600 mb-4">Sistema de Controle de Clientes e Pagamentos</h1>
            
            <!-- Tabs -->
            <div class="flex border-b mb-4 overflow-x-auto whitespace-nowrap">
                <button id="tab-clientes" class="px-4 py-2 tab-active" onclick="changeTab('clientes')">Clientes</button>
                <button id="tab-compras" class="px-4 py-2" onclick="changeTab('compras')">Compras</button>
                <button id="tab-pagamentos" class="px-4 py-2" onclick="changeTab('pagamentos')">Pagamentos</button>
                <button id="tab-relatorio" class="px-4 py-2" onclick="changeTab('relatorio')">Relatório</button>
            </div>
            
            <!-- Tab Clientes -->
            <div id="content-clientes" class="tab-content animate-fade-in">
                <div class="flex flex-col md:flex-row gap-4">
                    <!-- Formulário de Cadastro -->
                    <div class="w-full md:w-1/3 bg-gray-50 p-4 rounded-md">
                        <h2 class="text-lg font-semibold mb-3">Cadastro de Cliente</h2>
                        <form id="form-cliente">
                            <input type="hidden" id="cliente-id">
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="nome">Nome</label>
                                <input type="text" id="nome" placeholder="Nome completo" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="telefone">Telefone</label>
                                <input type="tel" id="telefone" placeholder="(00) 00000-0000" class="w-full px-3 py-2 border rounded-md">
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="endereco">Endereço</label>
                                <textarea id="endereco" placeholder="Endereço completo" class="w-full px-3 py-2 border rounded-md" rows="2"></textarea>
                            </div>
                            <div class="flex justify-between">
                                <button type="submit" class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700">Salvar</button>
                                <button type="button" id="btn-limpar" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md hover:bg-gray-400">Limpar</button>
                            </div>
                        </form>
                    </div>
                    
                    <!-- Lista de Clientes -->
                    <div class="w-full md:w-2/3 bg-gray-50 p-4 rounded-md">
                        <div class="flex justify-between items-center mb-3">
                            <h2 class="text-lg font-semibold">Clientes Cadastrados</h2>
                            <div class="relative">
                                <input type="text" id="busca-cliente" placeholder="Buscar cliente..." class="px-3 py-2 border rounded-md pr-8">
                                <span class="absolute right-2 top-2 text-gray-400"><i class="fas fa-search"></i></span>
                            </div>
                        </div>
                        <div class="overflow-auto max-h-80">
                            <ul id="lista-clientes" class="divide-y divide-gray-200">
                                <!-- Clientes serão inseridos aqui via JavaScript -->
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Tab Compras -->
            <div id="content-compras" class="tab-content hidden animate-fade-in">
                <!-- Cliente selecionado para compras -->
                <div class="bg-gray-100 p-3 rounded-md mb-4">
                    <h2 class="text-lg font-semibold mb-2">Cliente Selecionado</h2>
                    <div id="cliente-selecionado" class="flex flex-col md:flex-row justify-between items-start md:items-center">
                        <div>
                            <p class="text-gray-500">Nenhum cliente selecionado</p>
                        </div>
                        <button id="btn-selecionar-cliente" class="mt-2 md:mt-0 bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                            Selecionar Cliente
                        </button>
                    </div>
                </div>
                
                <div class="flex flex-col md:flex-row gap-4">
                    <!-- Formulário de Registro de Compra -->
                    <div class="w-full md:w-1/3 bg-gray-50 p-4 rounded-md">
                        <h2 class="text-lg font-semibold mb-3">Registrar Compra</h2>
                        <form id="form-compra">
                            <input type="hidden" id="compra-id">
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="data">Data</label>
                                <input type="date" id="data" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="descricao">Descrição</label>
                                <input type="text" id="descricao" placeholder="Descrição da compra" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="valor">Valor (R$)</label>
                                <input type="number" id="valor" placeholder="0.00" step="0.01" min="0" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="flex justify-between">
                                <button type="submit" class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700" id="btn-registrar-compra">Registrar</button>
                                <button type="button" id="btn-limpar-compra" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md hover:bg-gray-400">Limpar</button>
                            </div>
                        </form>
                    </div>
                    
                    <!-- Lista de Compras -->
                    <div class="w-full md:w-2/3 bg-gray-50 p-4 rounded-md">
                        <div class="flex justify-between items-center mb-3">
                            <h2 class="text-lg font-semibold">Compras do Cliente</h2>
                            <div class="text-right">
                                <div class="text-sm">Total: <span id="total-compras" class="font-bold">R$ 0,00</span></div>
                                <div class="text-sm">Total Pago: <span id="total-pago" class="font-bold text-green-600">R$ 0,00</span></div>
                                <div class="text-sm">Pendente: <span id="total-pendente" class="font-bold text-red-500">R$ 0,00</span></div>
                            </div>
                        </div>
                        <div class="overflow-auto max-h-80">
                            <ul id="lista-compras" class="divide-y divide-gray-200">
                                <!-- Compras serão inseridas aqui via JavaScript -->
                            </ul>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Tab Pagamentos -->
            <div id="content-pagamentos" class="tab-content hidden animate-fade-in">
                <!-- Cliente selecionado para pagamentos -->
                <div class="bg-gray-100 p-3 rounded-md mb-4">
                    <h2 class="text-lg font-semibold mb-2">Cliente Selecionado</h2>
                    <div id="cliente-selecionado-pagamento" class="flex flex-col md:flex-row justify-between items-start md:items-center">
                        <div>
                            <p class="text-gray-500">Nenhum cliente selecionado</p>
                        </div>
                        <button id="btn-selecionar-cliente-pagamento" class="mt-2 md:mt-0 bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                            Selecionar Cliente
                        </button>
                    </div>
                </div>
                
                <div class="flex flex-col md:flex-row gap-4">
                    <!-- Formulário de Registro de Pagamento -->
                    <div class="w-full md:w-1/3 bg-gray-50 p-4 rounded-md">
                        <h2 class="text-lg font-semibold mb-3">Registrar Pagamento</h2>
                        <form id="form-pagamento">
                            <input type="hidden" id="pagamento-id">
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="data-pagamento">Data do Pagamento</label>
                                <input type="date" id="data-pagamento" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="valor-pagamento">Valor Pago (R$)</label>
                                <input type="number" id="valor-pagamento" placeholder="0.00" step="0.01" min="0" class="w-full px-3 py-2 border rounded-md" required>
                            </div>
                            <div class="mb-3">
                                <label class="block text-sm font-medium text-gray-700 mb-1" for="obs-pagamento">Observação</label>
                                <textarea id="obs-pagamento" placeholder="Forma de pagamento, parcela, etc." class="w-full px-3 py-2 border rounded-md" rows="2"></textarea>
                            </div>
                            <div class="bg-yellow-50 p-3 mb-3 rounded-md">
                                <span class="text-sm font-medium">Valor Pendente: <span id="valor-pendente-pagamento" class="text-red-500 font-bold">R$ 0,00</span></span>
                            </div>
                            <div class="flex justify-between">
                                <button type="submit" class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700" id="btn-registrar-pagamento" disabled>Registrar</button>
                                <button type="button" id="btn-limpar-pagamento" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md hover:bg-gray-400">Limpar</button>
                            </div>
                        </form>
                    </div>
                    
                    <!-- Histórico de Pagamentos -->
                    <div class="w-full md:w-2/3">
                        <div class="bg-gray-50 p-4 rounded-md mb-4">
                            <h2 class="text-lg font-semibold mb-3">Resumo Financeiro</h2>
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                                <div class="bg-white p-3 rounded-md shadow-sm">
                                    <div class="text-sm text-gray-500">Total em Compras</div>
                                    <div class="text-xl font-bold" id="resumo-total-compras">R$ 0,00</div>
                                </div>
                                <div class="bg-white p-3 rounded-md shadow-sm">
                                    <div class="text-sm text-gray-500">Total Pago</div>
                                    <div class="text-xl font-bold text-green-600" id="resumo-total-pago">R$ 0,00</div>
                                </div>
                                <div class="bg-white p-3 rounded-md shadow-sm">
                                    <div class="text-sm text-gray-500">Saldo Devedor</div>
                                    <div class="text-xl font-bold text-red-500" id="resumo-saldo-devedor">R$ 0,00</div>
                                </div>
                            </div>
                        </div>

                        <div class="bg-gray-50 p-4 rounded-md">
                            <div class="flex justify-between items-center mb-3">
                                <h2 class="text-lg font-semibold">Histórico de Pagamentos</h2>
                            </div>
                            <div class="overflow-auto max-h-64">
                                <ul id="lista-pagamentos" class="divide-y divide-gray-200">
                                    <!-- Pagamentos serão inseridos aqui via JavaScript -->
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Tab Relatório -->
            <div id="content-relatorio" class="tab-content hidden animate-fade-in">
                <div class="bg-gray-50 p-4 rounded-md">
                    <h2 class="text-lg font-semibold mb-3">Relatório de Clientes e Finanças</h2>
                    
                    <div class="flex flex-wrap gap-4 mb-4">
                        <div class="bg-white p-4 rounded-md shadow-sm flex-1">
                            <h3 class="text-md font-medium text-gray-700">Total de Clientes</h3>
                            <p class="text-2xl font-bold text-indigo-600" id="total-clientes">0</p>
                        </div>
                        <div class="bg-white p-4 rounded-md shadow-sm flex-1">
                            <h3 class="text-md font-medium text-gray-700">Total de Vendas</h3>
                            <p class="text-2xl font-bold text-indigo-600" id="total-vendas">R$ 0,00</p>
                        </div>
                        <div class="bg-white p-4 rounded-md shadow-sm flex-1">
                            <h3 class="text-md font-medium text-gray-700">Total Recebido</h3>
                            <p class="text-2xl font-bold text-green-600" id="total-recebido">R$ 0,00</p>
                        </div>
                        <div class="bg-white p-4 rounded-md shadow-sm flex-1">
                            <h3 class="text-md font-medium text-gray-700">Total a Receber</h3>
                            <p class="text-2xl font-bold text-red-500" id="total-a-receber">R$ 0,00</p>
                        </div>
                    </div>
                    
                    <div class="bg-white p-4 rounded-md shadow-sm mb-4">
                        <h3 class="text-md font-medium text-gray-700 mb-2">Clientes com Pendências</h3>
                        <div class="overflow-auto max-h-60">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead>
                                    <tr>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Cliente</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Telefone</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Valor Pendente</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                                    </tr>
                                </thead>
                                <tbody id="tabela-pendencias" class="bg-white divide-y divide-gray-200">
                                    <!-- Clientes com pendências serão inseridos aqui -->
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <div class="bg-white p-4 rounded-md shadow-sm mb-4">
                        <h3 class="text-md font-medium text-gray-700 mb-2">Pagamentos Recentes (últimos 30 dias)</h3>
                        <div class="overflow-auto max-h-60">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead>
                                    <tr>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Data</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Cliente</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Valor</th>
                                        <th class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Observação</th>
                                    </tr>
                                </thead>
                                <tbody id="tabela-pagamentos-recentes" class="bg-white divide-y divide-gray-200">
                                    <!-- Pagamentos recentes serão inseridos aqui -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                    
                    <div class="bg-white p-4 rounded-md shadow-sm">
                        <h3 class="text-md font-medium text-gray-700 mb-2">Opções</h3>
                        <div class="space-y-2">
                            <button class="bg-red-500 text-white px-4 py-2 rounded-md hover:bg-red-600 mr-2" id="btn-backup">
                                <i class="fas fa-download mr-1"></i> Exportar Dados
                            </button>
                            <button class="bg-green-500 text-white px-4 py-2 rounded-md hover:bg-green-600" id="btn-importar">
                                <i class="fas fa-upload mr-1"></i> Importar Dados
                            </button>
                            <input type="file" id="input-importar" class="hidden" accept=".json">
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Instruções para instalação no celular -->
        <div class="bg-white shadow-md rounded-lg p-5 mb-4">
            <h2 class="text-xl font-bold text-indigo-600 mb-3">Como instalar no celular</h2>
            <div class="bg-gray-50 p-4 rounded-md">
                <h3 class="text-lg font-semibold mb-2">Para iPhone (Safari)</h3>
                <ol class="list-decimal pl-5 space-y-2">
                    <li>Abra este sistema no Safari</li>
                    <li>Toque no botão de compartilhar <i class="fas fa-share-alt"></i></li>
                    <li>Role para baixo e toque em "Adicionar à Tela de Início" <i class="fas fa-plus-square"></i></li>
                    <li>Confirme tocando em "Adicionar"</li>
                </ol>
            </div>
            <div class="bg-gray-50 p-4 rounded-md mt-3">
                <h3 class="text-lg font-semibold mb-2">Para Android (Chrome)</h3>
                <ol class="list-decimal pl-5 space-y-2">
                    <li>Abra este sistema no Chrome</li>
                    <li>Toque no menu <i class="fas fa-ellipsis-v"></i> (três pontos)</li>
                    <li>Selecione "Adicionar à tela inicial" ou "Instalar aplicativo"</li>
                    <li>Confirme tocando em "Adicionar" ou "Instalar"</li>
                </ol>
            </div>
            <p class="mt-3 text-sm text-gray-600">Após instalar, você poderá acessar este sistema diretamente do seu celular como um aplicativo, sem precisar abrir o navegador.</p>
        </div>
    </div>
    
    <!-- Modal para seleção de cliente -->
    <div id="modal-selecionar-cliente" class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center hidden z-50">
        <div class="bg-white p-6 rounded-md shadow-lg max-w-md w-full">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-lg font-semibold">Selecionar Cliente</h2>
                <button id="modal-close" class="text-gray-500 hover:text-gray-700">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div class="mb-4">
                <input type="text" id="modal-busca-cliente" placeholder="Buscar cliente..." class="w-full px-3 py-2 border rounded-md">
            </div>
            <div class="overflow-auto max-h-60">
                <ul id="modal-lista-clientes" class="divide-y divide-gray-200">
                    <!-- Clientes serão inseridos aqui via JavaScript -->
                </ul>
            </div>
        </div>
    </div>
    
    <script>
        // Modelo de dados
        let clientes = [];
        let clienteSelecionado = null;
        let tipoSelecao = 'compra'; // 'compra' ou 'pagamento'
        let deferredPrompt = null;
        
        // Elementos DOM
        const formCliente = document.getElementById('form-cliente');
        const formCompra = document.getElementById('form-compra');
        const formPagamento = document.getElementById('form-pagamento');
        const listaClientes = document.getElementById('lista-clientes');
        const listaCompras = document.getElementById('lista-compras');
        const listaPagamentos = document.getElementById('lista-pagamentos');
        const clienteSelecionadoInfo = document.getElementById('cliente-selecionado');
        const clienteSelecionadoPagamento = document.getElementById('cliente-selecionado-pagamento');
        const totalCompras = document.getElementById('total-compras');
        const totalPago = document.getElementById('total-pago');
        const totalPendente = document.getElementById('total-pendente');
        const resumoTotalCompras = document.getElementById('resumo-total-compras');
        const resumoTotalPago = document.getElementById('resumo-total-pago');
        const resumoSaldoDevedor = document.getElementById('resumo-saldo-devedor');
        const valorPendentePagamento = document.getElementById('valor-pendente-pagamento');
        
        // Carregar dados do localStorage
        function carregarDados() {
            const dadosSalvos = localStorage.getItem('sistema-clientes');
            if (dadosSalvos) {
                clientes = JSON.parse(dadosSalvos);
                // Migrar dados antigos para o novo formato se necessário
                clientes.forEach(cliente => {
                    if (!cliente.pagamentos) {
                        cliente.pagamentos = [];
                    }
                });
                atualizarListaClientes();
                atualizarRelatorio();
            }
        }
        
        // Salvar dados no localStorage
        function salvarDados() {
            localStorage.setItem('sistema-clientes', JSON.stringify(clientes));
            atualizarRelatorio();
        }
        
        // Atualizar lista de clientes
        function atualizarListaClientes() {
            listaClientes.innerHTML = '';
            const busca = document.getElementById('busca-cliente').value.toLowerCase();
            
            const clientesFiltrados = clientes.filter(cliente => 
                cliente.nome.toLowerCase().includes(busca) ||
                cliente.telefone.includes(busca)
            );
            
            if (clientesFiltrados.length === 0) {
                listaClientes.innerHTML = '<li class="py-3 px-2">Nenhum cliente encontrado.</li>';
                return;
            }
            
            clientesFiltrados.forEach((cliente, index) => {
                const li = document.createElement('li');
                li.className = 'cliente-item py-3 px-2 flex justify-between items-center';
                
                const pendente = calcularSaldoDevedor(cliente);
                const pendenteBadge = pendente > 0 ? 
                    `<span class="text-red-500 font-medium">Pendente: R$ ${pendente.toFixed(2)}</span>` : 
                    '<span class="text-green-500">Sem pendências</span>';
                
                li.innerHTML = `
                    <div>
                        <h3 class="font-semibold">${cliente.nome}</h3>
                        <p class="text-sm text-gray-600">${cliente.telefone}</p>
                        ${pendenteBadge}
                    </div>
                    <div>
                        <button class="text-indigo-600 hover:text-indigo-800 mr-2" onclick="editarCliente(${index})">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="text-red-600 hover:text-red-800" onclick="removerCliente(${index})">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                `;
                listaClientes.appendChild(li);
            });
        }
        
        // Adicionar Cliente
        formCliente.addEventListener('submit', function(e) {
            e.preventDefault();
            const id = document.getElementById('cliente-id').value;
            const nome = document.getElementById('nome').value;
            const telefone = document.getElementById('telefone').value;
            const endereco = document.getElementById('endereco').value;
            
            if (id) {
                // Editar cliente existente
                const index = parseInt(id);
                clientes[index].nome = nome;
                clientes[index].telefone = telefone;
                clientes[index].endereco = endereco;
            } else {
                // Adicionar novo cliente
                const novoCliente = {
                    nome,
                    telefone,
                    endereco,
                    compras: [],
                    pagamentos: []
                };
                clientes.push(novoCliente);
            }
            
            salvarDados();
            atualizarListaClientes();
            limparFormularioCliente();
        });
        
        // Limpar formulário de cliente
        function limparFormularioCliente() {
            document.getElementById('cliente-id').value = '';
            document.getElementById('nome').value = '';
            document.getElementById('telefone').value = '';
            document.getElementById('endereco').value = '';
        }
        
        // Editar cliente
        function editarCliente(index) {
            const cliente = clientes[index];
            document.getElementById('cliente-id').value = index;
            document.getElementById('nome').value = cliente.nome;
            document.getElementById('telefone').value = cliente.telefone;
            document.getElementById('endereco').value = cliente.endereco;
            
            // Rolagem para o formulário em dispositivos móveis
            if (window.innerWidth <= 768) {
                document.getElementById('form-cliente').scrollIntoView({ behavior: 'smooth' });
            }
        }
        
        // Remover cliente
        function removerCliente(index) {
            if (confirm("Tem certeza que deseja remover este cliente?")) {
                clientes.splice(index, 1);
                salvarDados();
                atualizarListaClientes();
                
                // Se o cliente removido for o selecionado, limpar seleção
                if (clienteSelecionado && clienteSelecionado.index === index) {
                    clienteSelecionado = null;
                    atualizarClienteSelecionado();
                    atualizarListaCompras();
                    atualizarListaPagamentos();
                }
            }
        }
        
        // Buscar cliente
        document.getElementById('busca-cliente').addEventListener('input', function() {
            atualizarListaClientes();
        });
        
        // Botão limpar
        document.getElementById('btn-limpar').addEventListener('click', limparFormularioCliente);
        
        // Tabs de navegação
        function changeTab(tab) {
            const tabs = ['clientes', 'compras', 'pagamentos', 'relatorio'];
            tabs.forEach(t => {
                document.getElementById(`tab-${t}`).classList.remove('tab-active');
                document.getElementById(`content-${t}`).classList.add('hidden');
            });
            document.getElementById(`tab-${tab}`).classList.add('tab-active');
            document.getElementById(`content-${tab}`).classList.remove('hidden');
            
            if (tab === 'relatorio') {
                atualizarRelatorio();
            }
        }
        
        // Botão selecionar cliente
        document.getElementById('btn-selecionar-cliente').addEventListener('click', function() {
            tipoSelecao = 'compra';
            document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
            atualizarModalListaClientes();
        });

        // Botão selecionar cliente para pagamento
        document.getElementById('btn-selecionar-cliente-pagamento').addEventListener('click', function() {
            tipoSelecao = 'pagamento';
            document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
            atualizarModalListaClientes();
        });
        
        // Fechar modal
        document.getElementById('modal-close').addEventListener('click', function() {
            document.getElementById('modal-selecionar-cliente').classList.add('hidden');
        });
        
        // Buscar cliente no modal
        document.getElementById('modal-busca-cliente').addEventListener('input', function() {
            atualizarModalListaClientes();
        });
        
        // Atualizar lista de clientes no modal
        function atualizarModalListaClientes() {
            const listaModal = document.getElementById('modal-lista-clientes');
            listaModal.innerHTML = '';
            const busca = document.getElementById('modal-busca-cliente').value.toLowerCase();
            
            const clientesFiltrados = clientes.filter(cliente => 
                cliente.nome.toLowerCase().includes(busca) || 
                cliente.telefone.includes(busca)
            );
            
            if (clientesFiltrados.length === 0) {
                listaModal.innerHTML = '<li class="py-3 px-2">Nenhum cliente encontrado.</li>';
                return;
            }
            
            clientesFiltrados.forEach((cliente, index) => {
                const pendente = calcularSaldoDevedor(cliente);
                const pendenteBadge = pendente > 0 ? 
                    `<span class="text-red-500 font-medium">Pendente: R$ ${pendente.toFixed(2)}</span>` : 
                    '<span class="text-green-500">Sem pendências</span>';

                const li = document.createElement('li');
                li.className = 'py-3 px-2 hover:bg-gray-100 cursor-pointer';
                li.innerHTML = `
                    <div>
                        <h3 class="font-semibold">${cliente.nome}</h3>
                        <p class="text-sm text-gray-600">${cliente.telefone}</p>
                        ${pendenteBadge}
                    </div>
                `;
                li.addEventListener('click', function() {
                    selecionarCliente(index);
                    document.getElementById('modal-selecionar-cliente').classList.add('hidden');
                });
                listaModal.appendChild(li);
            });
        }
        
        // Selecionar cliente para compras ou pagamentos
        function selecionarCliente(index) {
            clienteSelecionado = {
                index,
                cliente: clientes[index]
            };
            
            if (tipoSelecao === 'compra') {
                atualizarClienteSelecionado();
                atualizarListaCompras();
            } else {
                atualizarClienteSelecionadoPagamento();
                atualizarListaPagamentos();
                atualizarValorPendentePagamento();
            }
        }
        
        // Atualizar informação do cliente selecionado para compras
        function atualizarClienteSelecionado() {
            if (clienteSelecionado) {
                const cliente = clienteSelecionado.cliente;
                clienteSelecionadoInfo.innerHTML = `
                    <div>
                        <h3 class="font-semibold">${cliente.nome}</h3>
                        <p class="text-sm text-gray-600">${cliente.telefone}</p>
                    </div>
                    <button id="btn-alterar-cliente" class="bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                        Alterar Cliente
                    </button>
                `;
                document.getElementById('btn-alterar-cliente').addEventListener('click', function() {
                    tipoSelecao = 'compra';
                    document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
                    atualizarModalListaClientes();
                });
                document.getElementById('btn-registrar-compra').disabled = false;
                document.getElementById('btn-registrar-compra').classList.remove('opacity-50', 'cursor-not-allowed');
            } else {
                clienteSelecionadoInfo.innerHTML = `
                    <div>
                        <p class="text-gray-500">Nenhum cliente selecionado</p>
                    </div>
                    <button id="btn-selecionar-cliente" class="bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                        Selecionar Cliente
                    </button>
                `;
                document.getElementById('btn-selecionar-cliente').addEventListener('click', function() {
                    tipoSelecao = 'compra';
                    document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
                    atualizarModalListaClientes();
                });
                document.getElementById('btn-registrar-compra').disabled = true;
                document.getElementById('btn-registrar-compra').classList.add('opacity-50', 'cursor-not-allowed');
            }
        }

        // Atualizar informação do cliente selecionado para pagamentos
        function atualizarClienteSelecionadoPagamento() {
            if (clienteSelecionado) {
                const cliente = clienteSelecionado.cliente;
                const saldoDevedor = calcularSaldoDevedor(cliente);
                
                clienteSelecionadoPagamento.innerHTML = `
                    <div>
                        <h3 class="font-semibold">${cliente.nome}</h3>
                        <p class="text-sm text-gray-600">${cliente.telefone}</p>
                        <p class="text-sm ${saldoDevedor > 0 ? 'text-red-500' : 'text-green-500'}">
                            ${saldoDevedor > 0 ? `Pendente: R$ ${saldoDevedor.toFixed(2)}` : 'Sem pendências'}
                        </p>
                    </div>
                    <button id="btn-alterar-cliente-pagamento" class="bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                        Alterar Cliente
                    </button>
                `;
                document.getElementById('btn-alterar-cliente-pagamento').addEventListener('click', function() {
                    tipoSelecao = 'pagamento';
                    document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
                    atualizarModalListaClientes();
                });
                
                document.getElementById('btn-registrar-pagamento').disabled = saldoDevedor <= 0;
                if (saldoDevedor <= 0) {
                    document.getElementById('btn-registrar-pagamento').classList.add('opacity-50', 'cursor-not-allowed');
                } else {
                    document.getElementById('btn-registrar-pagamento').classList.remove('opacity-50', 'cursor-not-allowed');
                }
            } else {
                clienteSelecionadoPagamento.innerHTML = `
                    <div>
                        <p class="text-gray-500">Nenhum cliente selecionado</p>
                    </div>
                    <button id="btn-selecionar-cliente-pagamento" class="bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700 text-sm">
                        Selecionar Cliente
                    </button>
                `;
                document.getElementById('btn-selecionar-cliente-pagamento').addEventListener('click', function() {
                    tipoSelecao = 'pagamento';
                    document.getElementById('modal-selecionar-cliente').classList.remove('hidden');
                    atualizarModalListaClientes();
                });
                document.getElementById('btn-registrar-pagamento').disabled = true;
                document.getElementById('btn-registrar-pagamento').classList.add('opacity-50', 'cursor-not-allowed');
            }
        }

        // Atualizar valor pendente no formulário de pagamento
        function atualizarValorPendentePagamento() {
            if (clienteSelecionado) {
                const saldoDevedor = calcularSaldoDevedor(clienteSelecionado.cliente);
                valorPendentePagamento.textContent = `R$ ${saldoDevedor.toFixed(2)}`;
                
                // Preencher o valor do pagamento com o valor total pendente
                document.getElementById('valor-pagamento').value = saldoDevedor.toFixed(2);
            } else {
                valorPendentePagamento.textContent = 'R$ 0,00';
                document.getElementById('valor-pagamento').value = '';
            }
        }
        
        // Adicionar compra
        formCompra.addEventListener('submit', function(e) {
            e.preventDefault();
            if (!clienteSelecionado) {
                alert('Selecione um cliente primeiro!');
                return;
            }
            
            const compraId = document.getElementById('compra-id').value;
            const data = document.getElementById('data').value;
            const descricao = document.getElementById('descricao').value;
            const valor = parseFloat(document.getElementById('valor').value);
            
            if (compraId) {
                // Editar compra existente
                const index = parseInt(compraId);
                clienteSelecionado.cliente.compras[index].data = data;
                clienteSelecionado.cliente.compras[index].descricao = descricao;
                clienteSelecionado.cliente.compras[index].valor = valor;
            } else {
                // Adicionar nova compra
                const novaCompra = {
                    data,
                    descricao,
                    valor,
                    paga: false
                };
                clienteSelecionado.cliente.compras.push(novaCompra);
            }
            
            salvarDados();
            atualizarListaCompras();
            limparFormularioCompra();
        });

        // Adicionar pagamento
        formPagamento.addEventListener('submit', function(e) {
            e.preventDefault();
            if (!clienteSelecionado) {
                alert('Selecione um cliente primeiro!');
                return;
            }
            
            const pagamentoId = document.getElementById('pagamento-id').value;
            const data = document.getElementById('data-pagamento').value;
            const valor = parseFloat(document.getElementById('valor-pagamento').value);
            const observacao = document.getElementById('obs-pagamento').value;
            
            // Verificar se o valor do pagamento é maior que o saldo devedor
            const saldoDevedor = calcularSaldoDevedor(clienteSelecionado.cliente);
            if (valor > saldoDevedor) {
                alert('O valor do pagamento não pode ser maior que o saldo devedor!');
                return;
            }
            
            if (pagamentoId) {
                // Editar pagamento existente
                const index = parseInt(pagamentoId);
                clienteSelecionado.cliente.pagamentos[index].data = data;
                clienteSelecionado.cliente.pagamentos[index].valor = valor;
                clienteSelecionado.cliente.pagamentos[index].observacao = observacao;
            } else {
                // Adicionar novo pagamento
                const novoPagamento = {
                    data,
                    valor,
                    observacao
                };
                if (!clienteSelecionado.cliente.pagamentos) {
                    clienteSelecionado.cliente.pagamentos = [];
                }
                clienteSelecionado.cliente.pagamentos.push(novoPagamento);
                
                // Marcar compras como pagas automaticamente
                const comprasNaoPagas = clienteSelecionado.cliente.compras.filter(c => !c.paga);
                let valorRestante = valor;
                
                for (let compra of comprasNaoPagas) {
                    if (valorRestante >= compra.valor) {
                        compra.paga = true;
                        valorRestante -= compra.valor;
                    } else if (valorRestante > 0) {
                        // No caso de pagamento parcial, não marcamos como paga
                        valorRestante = 0;
                    }
                    
                    if (valorRestante <= 0) break;
                }
            }
            
            salvarDados();
            atualizarListaPagamentos();
            atualizarListaCompras();
            atualizarValorPendentePagamento();
            limparFormularioPagamento();
        });
        
        // Limpar formulário de compra
        function limparFormularioCompra() {
            document.getElementById('compra-id').value = '';
            document.getElementById('data').value = new Date().toISOString().substring(0, 10);
            document.getElementById('descricao').value = '';
            document.getElementById('valor').value = '';
        }

        // Limpar formulário de pagamento
        function limparFormularioPagamento() {
            document.getElementById('pagamento-id').value = '';
            document.getElementById('data-pagamento').value = new Date().toISOString().substring(0, 10);
            document.getElementById('valor-pagamento').value = '';
            document.getElementById('obs-pagamento').value = '';
            atualizarValorPendentePagamento();
        }
        
        // Atualizar lista de compras
        function atualizarListaCompras() {
            listaCompras.innerHTML = '';
            
            if (!clienteSelecionado || !clienteSelecionado.cliente.compras.length) {
                listaCompras.innerHTML = '<li class="py-3 px-2">Nenhuma compra registrada.</li>';
                totalCompras.textContent = 'R$ 0,00';
                totalPago.textContent = 'R$ 0,00';
                totalPendente.textContent = 'R$ 0,00';
                return;
            }
            
            let total = 0;
            let pago = 0;
            let pendente = 0;
            
            // Ordenar compras por data (mais recente primeiro)
            const comprasOrdenadas = [...clienteSelecionado.cliente.compras].sort((a, b) => 
                new Date(b.data) - new Date(a.data)
            );
            
            comprasOrdenadas.forEach((compra, index) => {
                const realIndex = clienteSelecionado.cliente.compras.indexOf(compra);
                total += compra.valor;
                if (compra.paga) {
                    pago += compra.valor;
                } else {
                    pendente += compra.valor;
                }
                
                const li = document.createElement('li');
                li.className = `compra-item py-3 px-2 flex justify-between items-center ${compra.paga ? 'paga' : ''}`;
                
                const dataFormatada = new Date(compra.data).toLocaleDateString('pt-BR');
                
                li.innerHTML = `
                    <div>
                        <div class="flex items-center">
                            <input type="checkbox" ${compra.paga ? 'checked' : ''} class="mr-2 h-5 w-5" 
                                onchange="marcarComoPaga(${realIndex})">
                            <div>
                                <h3 class="font-semibold">${compra.descricao}</h3>
                                <p class="text-sm text-gray-600">${dataFormatada}</p>
                            </div>
                        </div>
                    </div>
                    <div class="flex items-center">
                        <span class="font-medium mr-3">R$ ${compra.valor.toFixed(2)}</span>
                        <button class="text-indigo-600 hover:text-indigo-800 mr-2" onclick="editarCompra(${realIndex})">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="text-red-600 hover:text-red-800" onclick="removerCompra(${realIndex})">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                `;
                listaCompras.appendChild(li);
            });
            
            totalCompras.textContent = `R$ ${total.toFixed(2)}`;
            totalPago.textContent = `R$ ${pago.toFixed(2)}`;
            totalPendente.textContent = `R$ ${pendente.toFixed(2)}`;
        }

        // Atualizar lista de pagamentos
        function atualizarListaPagamentos() {
            listaPagamentos.innerHTML = '';
            
            if (!clienteSelecionado || !clienteSelecionado.cliente.pagamentos || !clienteSelecionado.cliente.pagamentos.length) {
                listaPagamentos.innerHTML = '<li class="py-3 px-2">Nenhum pagamento registrado.</li>';
                resumoTotalCompras.textContent = 'R$ 0,00';
                resumoTotalPago.textContent = 'R$ 0,00';
                resumoSaldoDevedor.textContent = 'R$ 0,00';
                return;
            }
            
            const totalComprasValor = clienteSelecionado.cliente.compras.reduce((total, compra) => total + compra.valor, 0);
            const totalPagoValor = clienteSelecionado.cliente.pagamentos.reduce((total, pagamento) => total + pagamento.valor, 0);
            const saldoDevedor = totalComprasValor - totalPagoValor;
            
            resumoTotalCompras.textContent = `R$ ${totalComprasValor.toFixed(2)}`;
            resumoTotalPago.textContent = `R$ ${totalPagoValor.toFixed(2)}`;
            resumoSaldoDevedor.textContent = `R$ ${saldoDevedor.toFixed(2)}`;
            
            // Ordenar pagamentos por data (mais recente primeiro)
            const pagamentosOrdenados = [...clienteSelecionado.cliente.pagamentos].sort((a, b) => 
                new Date(b.data) - new Date(a.data)
            );
            
            pagamentosOrdenados.forEach((pagamento, index) => {
                const realIndex = clienteSelecionado.cliente.pagamentos.indexOf(pagamento);
                const li = document.createElement('li');
                li.className = 'pagamento-item py-3 px-2 flex justify-between items-center';
                
                const dataFormatada = new Date(pagamento.data).toLocaleDateString('pt-BR');
                
                li.innerHTML = `
                    <div>
                        <h3 class="font-semibold">Pagamento de R$ ${pagamento.valor.toFixed(2)}</h3>
                        <p class="text-sm text-gray-600">${dataFormatada}</p>
                        ${pagamento.observacao ? `<p class="text-sm text-gray-500">${pagamento.observacao}</p>` : ''}
                    </div>
                    <div class="flex items-center">
                        <button class="text-indigo-600 hover:text-indigo-800 mr-2" onclick="editarPagamento(${realIndex})">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="text-red-600 hover:text-red-800" onclick="removerPagamento(${realIndex})">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                `;
                listaPagamentos.appendChild(li);
            });
        }
        
        // Marcar compra como paga
        function marcarComoPaga(index) {
            if (clienteSelecionado) {
                const compra = clienteSelecionado.cliente.compras[index];
                const estavaPaga = compra.paga;
                compra.paga = !estavaPaga;
                
                // Se marcar como paga, registrar um pagamento automático
                if (compra.paga && !estavaPaga) {
                    if (!clienteSelecionado.cliente.pagamentos) {
                        clienteSelecionado.cliente.pagamentos = [];
                    }
                    
                    const novoPagamento = {
                        data: new Date().toISOString().substring(0, 10),
                        valor: compra.valor,
                        observacao: `Pagamento automático da compra: ${compra.descricao}`
                    };
                    clienteSelecionado.cliente.pagamentos.push(novoPagamento);
                }
                // Se desmarcar como paga, remover o pagamento correspondente
                else if (!compra.paga && estavaPaga) {
                    const pagamentoIndex = clienteSelecionado.cliente.pagamentos.findIndex(
                        p => p.observacao === `Pagamento automático da compra: ${compra.descricao}`
                    );
                    if (pagamentoIndex !== -1) {
                        clienteSelecionado.cliente.pagamentos.splice(pagamentoIndex, 1);
                    }
                }
                
                salvarDados();
                atualizarListaCompras();
                atualizarListaPagamentos();
                atualizarValorPendentePagamento();
            }
        }
        
        // Editar compra
        function editarCompra(index) {
            if (clienteSelecionado) {
                const compra = clienteSelecionado.cliente.compras[index];
                document.getElementById('compra-id').value = index;
                document.getElementById('data').value = compra.data;
                document.getElementById('descricao').value = compra.descricao;
                document.getElementById('valor').value = compra.valor;
                
                // Rolagem para o formulário em dispositivos móveis
                if (window.innerWidth <= 768) {
                    document.getElementById('form-compra').scrollIntoView({ behavior: 'smooth' });
                }
            }
        }

        // Editar pagamento
        function editarPagamento(index) {
            if (clienteSelecionado) {
                const pagamento = clienteSelecionado.cliente.pagamentos[index];
                document.getElementById('pagamento-id').value = index;
                document.getElementById('data-pagamento').value = pagamento.data;
                document.getElementById('valor-pagamento').value = pagamento.valor;
                document.getElementById('obs-pagamento').value = pagamento.observacao || '';
                
                // Rolagem para o formulário em dispositivos móveis
                if (window.innerWidth <= 768) {
                    document.getElementById('form-pagamento').scrollIntoView({ behavior: 'smooth' });
                }
            }
        }
        
        // Remover compra
        function removerCompra(index) {
            if (clienteSelecionado && confirm("Tem certeza que deseja remover esta compra?")) {
                clienteSelecionado.cliente.compras.splice(index, 1);
                salvarDados();
                atualizarListaCompras();
                atualizarListaPagamentos();
                atualizarValorPendentePagamento();
            }
        }

        // Remover pagamento
        function removerPagamento(index) {
            if (clienteSelecionado && confirm("Tem certeza que deseja remover este pagamento?")) {
                clienteSelecionado.cliente.pagamentos.splice(index, 1);
                
                // Recalcular quais compras devem estar marcadas como pagas
                const totalPago = clienteSelecionado.cliente.pagamentos.reduce(
                    (total, pagamento) => total + pagamento.valor, 0
                );
                
                let valorAlocado = 0;
                // Ordenar compras por data (mais antigas primeiro)
                const comprasOrdenadas = [...clienteSelecionado.cliente.compras].sort(
                    (a, b) => new Date(a.data) - new Date(b.data)
                );
                
                // Marcar compras como pagas começando pelas mais antigas
                for (let compra of comprasOrdenadas) {
                    if (valorAlocado + compra.valor <= totalPago) {
                        compra.paga = true;
                        valorAlocado += compra.valor;
                    } else {
                        compra.paga = false;
                    }
                }
                
                salvarDados();
                atualizarListaPagamentos();
                atualizarListaCompras();
                atualizarValorPendentePagamento();
            }
        }
        
        // Limpar formulário de compra
        document.getElementById('btn-limpar-compra').addEventListener('click', limparFormularioCompra);

        // Limpar formulário de pagamento
        document.getElementById('btn-limpar-pagamento').addEventListener('click', limparFormularioPagamento);
        
        // Calcular valor pendente de um cliente
        function calcularSaldoDevedor(cliente) {
            if (!cliente) return 0;
            
            const totalCompras = cliente.compras ? cliente.compras.reduce(
                (total, compra) => total + compra.valor, 0
            ) : 0;
            
            const totalPago = cliente.pagamentos ? cliente.pagamentos.reduce(
                (total, pagamento) => total + pagamento.valor, 0
            ) : 0;
            
            return Math.max(0, totalCompras - totalPago);
        }
        
        // Atualizar relatório
        function atualizarRelatorio() {
            const totalClientes = document.getElementById('total-clientes');
            const totalVendas = document.getElementById('total-vendas');
            const totalRecebido = document.getElementById('total-recebido');
            const totalAReceber = document.getElementById('total-a-receber');
            const tabelaPendencias = document.getElementById('tabela-pendencias');
            const tabelaPagamentosRecentes = document.getElementById('tabela-pagamentos-recentes');
            
            let totalVendasValor = 0;
            let totalRecebidoValor = 0;
            let totalAReceberValor = 0;
            let pagamentosRecentes = [];
            
            clientes.forEach(cliente => {
                if (cliente.compras) {
                    cliente.compras.forEach(compra => {
                        totalVendasValor += compra.valor;
                    });
                }
                
                if (cliente.pagamentos) {
                    cliente.pagamentos.forEach(pagamento => {
                        totalRecebidoValor += pagamento.valor;
                        
                        // Coletar pagamentos dos últimos 30 dias
                        const dataLimite = new Date();
                        dataLimite.setDate(dataLimite.getDate() - 30);
                        const dataPagamento = new Date(pagamento.data);
                        
                        if (dataPagamento >= dataLimite) {
                            pagamentosRecentes.push({
                                data: pagamento.data,
                                cliente: cliente.nome,
                                valor: pagamento.valor,
                                observacao: pagamento.observacao || '-'
                            });
                        }
                    });
                }
                
                totalAReceberValor += calcularSaldoDevedor(cliente);
            });
            
            totalClientes.textContent = clientes.length;
            totalVendas.textContent = `R$ ${totalVendasValor.toFixed(2)}`;
            totalRecebido.textContent = `R$ ${totalRecebidoValor.toFixed(2)}`;
            totalAReceber.textContent = `R$ ${totalAReceberValor.toFixed(2)}`;
            
            // Atualizar tabela de pendências
            tabelaPendencias.innerHTML = '';
            
            const clientesComPendencia = clientes.filter(cliente => calcularSaldoDevedor(cliente) > 0)
                .sort((a, b) => calcularSaldoDevedor(b) - calcularSaldoDevedor(a));
            
            if (clientesComPendencia.length === 0) {
                tabelaPendencias.innerHTML = `
                    <tr>
                        <td colspan="4" class="px-6 py-4 text-center">Não há clientes com pendências</td>
                    </tr>
                `;
            } else {
                clientesComPendencia.forEach((cliente, index) => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="px-6 py-4">${cliente.nome}</td>
                        <td class="px-6 py-4">${cliente.telefone}</td>
                        <td class="px-6 py-4 font-medium text-red-500">R$ ${calcularSaldoDevedor(cliente).toFixed(2)}</td>
                        <td class="px-6 py-4">
                            <button class="bg-green-500 text-white px-2 py-1 rounded text-xs" onclick="registrarPagamentoRapido(${index})">
                                Registrar Pagamento
                            </button>
                        </td>
                    `;
                    tabelaPendencias.appendChild(tr);
                });
            }
            
            // Atualizar tabela de pagamentos recentes
            tabelaPagamentosRecentes.innerHTML = '';
            
            if (pagamentosRecentes.length === 0) {
                tabelaPagamentosRecentes.innerHTML = `
                    <tr>
                        <td colspan="4" class="px-6 py-4 text-center">Nenhum pagamento nos últimos 30 dias</td>
                    </tr>
                `;
            } else {
                // Ordenar pagamentos por data (mais recentes primeiro)
                pagamentosRecentes.sort((a, b) => new Date(b.data) - new Date(a.data));
                
                pagamentosRecentes.forEach(pagamento => {
                    const tr = document.createElement('tr');
                    const dataFormatada = new Date(pagamento.data).toLocaleDateString('pt-BR');
                    
                    tr.innerHTML = `
                        <td class="px-6 py-4">${dataFormatada}</td>
                        <td class="px-6 py-4">${pagamento.cliente}</td>
                        <td class="px-6 py-4 font-medium text-green-600">R$ ${pagamento.valor.toFixed(2)}</td>
                        <td class="px-6 py-4">${pagamento.observacao}</td>
                    `;
                    tabelaPagamentosRecentes.appendChild(tr);
                });
            }
        }

        // Registrar pagamento rápido
        function registrarPagamentoRapido(index) {
            selecionarCliente(index);
            changeTab('pagamentos');
            atualizarClienteSelecionadoPagamento();
            atualizarListaPagamentos();
            atualizarValorPendentePagamento();
        }
        
        // Exportar dados (backup)
        document.getElementById('btn-backup').addEventListener('click', function() {
            const dados = JSON.stringify(clientes);
            const blob = new Blob([dados], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            
            const a = document.createElement('a');
            a.href = url;
            a.download = `backup-clientes-${new Date().toISOString().slice(0, 10)}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
        });
        
        // Importar dados
        document.getElementById('btn-importar').addEventListener('click', function() {
            document.getElementById('input-importar').click();
        });
        
        document.getElementById('input-importar').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const dadosImportados = JSON.parse(e.target.result);
                    
                    if (confirm("Isso substituirá todos os dados atuais. Deseja continuar?")) {
                        clientes = dadosImportados;
                        
                        // Migrar dados antigos para o novo formato se necessário
                        clientes.forEach(cliente => {
                            if (!cliente.pagamentos) {
                                cliente.pagamentos = [];
                            }
                        });
                        
                        salvarDados();
                        atualizarListaClientes();
                        atualizarRelatorio();
                        alert("Dados importados com sucesso!");
                    }
                } catch (error) {
                    alert("Erro ao importar dados: arquivo inválido");
                }
            };
            reader.readAsText(file);
        });

        // Progressive Web App (PWA)
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            deferredPrompt = e;
            
            // Mostrar banner de instalação
            document.getElementById('install-banner').classList.remove('hidden');
        });
        
        document.getElementById('install-btn').addEventListener('click', () => {
            if (deferredPrompt) {
                deferredPrompt.prompt();
                
                deferredPrompt.userChoice.then((choiceResult) => {
                    if (choiceResult.outcome === 'accepted') {
                        console.log('Usuário aceitou a instalação');
                        document.getElementById('install-banner').classList.add('hidden');
                    }
                    deferredPrompt = null;
                });
            }
        });
        
        document.getElementById('close-install-banner').addEventListener('click', () => {
            document.getElementById('install-banner').classList.add('hidden');
        });
        
        // Detectar se já está instalado como PWA
        if (window.matchMedia('(display-mode: standalone)').matches) {
            document.getElementById('install-banner').classList.add('hidden');
        }
        
        // Inicialização
        document.addEventListener('DOMContentLoaded', function() {
            carregarDados();
            limparFormularioCliente();
            limparFormularioCompra();
            limparFormularioPagamento();
            document.getElementById('data').value = new Date().toISOString().substring(0, 10);
            document.getElementById('data-pagamento').value = new Date().toISOString().substring(0, 10);
            atualizarClienteSelecionado();
            atualizarClienteSelecionadoPagamento();
        });
    </script>
</body>
</html>
