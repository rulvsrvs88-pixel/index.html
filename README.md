<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ERP & Tienda Virtual - Modo Morado</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-purple-200 font-sans antialiased">

    <header class="bg-indigo-950 border-b border-indigo-800 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-purple-400">Simulador de Almacén y Ventas v5.2</h1>
                <p class="text-sm text-purple-300">Interfaz con Personalización Morada</p>
            </div>
            
            <div class="flex bg-gray-800 p-1 rounded-lg border border-indigo-700">
                <button onclick="switchView('store')" id="btnTabStore" class="px-4 py-2 text-sm font-bold rounded-md bg-indigo-600 text-purple-100 transition-all">
                    🛒 Modo Tienda
                </button>
                <button onclick="switchView('admin')" id="btnTabAdmin" class="px-4 py-2 text-sm font-bold rounded-md text-purple-300 hover:text-purple-100 transition-all">
                    ⚙️ Modo Almacén
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <div id="viewStore" class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <div class="lg:col-span-2 space-y-6">
                <h2 class="text-xl font-bold text-purple-400 flex items-center gap-2"><span>💻</span> Catálogo General</h2>
                <div id="storeGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-4"></div>
            </div>

            <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 h-fit space-y-4 shadow-xl sticky top-28">
                <h3 class="text-lg font-bold text-amber-400 border-b border-gray-700 pb-2"><span>🛒</span> Lista de Compra</h3>
                <div id="cartItems" class="space-y-3 max-h-64 overflow-y-auto pr-1"></div>
                <button onclick="checkoutCart()" class="w-full bg-amber-500 hover:bg-amber-600 text-gray-950 font-extrabold py-3 rounded-lg text-sm uppercase mt-4">
                    Confirmar Entrega
                </button>
            </div>
        </div>

        <div id="viewAdmin" class="hidden space-y-8">
            <section class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-purple-300 uppercase">Total Productos</span>
                    <span id="kpiItems" class="text-2xl font-extrabold text-purple-400 block">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-purple-300 uppercase">Alertas ROP</span>
                    <span id="kpiAlerts" class="text-2xl font-extrabold text-red-500 block">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-indigo-900">
                    <span class="text-xs font-medium text-purple-400 uppercase">Encargado</span>
                    <span class="text-lg font-bold text-purple-100 block">Raúl Villegas</span>
                </div>
            </section>

            <section class="bg-gray-800 p-6 rounded-xl border border-gray-700">
                <h3 class="text-lg font-bold text-emerald-400 mb-4"><span>📦</span> Surtir Almacén</h3>
                <form id="restockForm" onsubmit="handleRestock(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
                    <div class="md:col-span-2">
                        <label class="block text-xs text-purple-300 mb-1">Componente</label>
                        <select id="restockNameSelect" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-purple-100" required></select>
                    </div>
                    <input type="number" id="restockQty" placeholder="Cantidad" class="bg-gray-700 border border-gray-600 rounded p-2 text-sm text-purple-100" required>
                    <button type="submit" class="w-full md:col-span-3 bg-emerald-600 text-white font-bold py-2 rounded text-sm">Confirmar</button>
                </form>
            </section>

            <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden">
                <div class="p-5 border-b border-gray-700"><h2 class="text-lg font-semibold text-purple-100">Matriz de Control</h2></div>
                <table class="w-full text-left" id="inventoryTable">
                    <thead><tr class="text-purple-300 text-xs uppercase border-b border-gray-700"><th class="p-4">Componente</th><th class="p-4">Stock</th><th class="p-4">Estado</th></tr></thead>
                    <tbody class="text-sm text-purple-200"></tbody>
                </table>
            </section>
        </div>
    </main>

    <script>
        const defaultProducts = [
            { id: "COMP-001", nombre: "Memoria RAM DDR4 8GB Kingston", cat: "Memorias", stock: 10, d: 364, s: 288, h: 143 },
            { id: "COMP-002", nombre: "Teclado Mecánico Logitech", cat: "Periféricos", stock: 3, d: 532, s: 123, h: 41 },
            { id: "COMP-0088", nombre: "Fuente 850W ASUS ROG Gold", cat: "Fuentes", stock: 22, d: 482, s: 186, h: 186 }
        ];

        let products = JSON.parse(localStorage.getItem('st_products_v52')) || defaultProducts;
        let cart = [];

        function switchView(view) {
            document.getElementById("viewAdmin").classList.toggle("hidden", view === 'store');
            document.getElementById("viewStore").classList.toggle("hidden", view === 'admin');
        }

        function renderStoreGrid() {
            const grid = document.getElementById("storeGrid");
            grid.innerHTML = products.map(p => `
                <div class="bg-gray-800 p-4 rounded-xl border border-gray-700">
                    <h4 class="text-sm font-bold text-purple-100">${p.nombre}</h4>
                    <p class="text-xs text-purple-300 mt-2">Disponibles: <strong class="text-purple-400">${p.stock}</strong></p>
                    <button onclick="addToCart('${p.id}')" class="mt-3 w-full bg-indigo-600 text-purple-100 text-xs py-2 rounded font-bold">🛒 AGREGAR</button>
                </div>
            `).join('');
        }

        function renderAdminTable() {
            const tbody = document.querySelector("#inventoryTable tbody");
            const select = document.getElementById("restockNameSelect");
            tbody.innerHTML = products.map(p => `
                <tr class="border-b border-gray-700">
                    <td class="p-4 font-bold text-purple-100">${p.nombre}</td>
                    <td class="p-4 text-purple-200">${p.stock}</td>
                    <td class="p-4 text-purple-400">✅ OK</td>
                </tr>
            `).join('');
            select.innerHTML = products.map(p => `<option value="${p.id}">${p.nombre}</option>`).join('');
        }

        function addToCart(id) {
            let p = products.find(x => x.id === id);
            if(p.stock > 0) { p.stock--; renderStoreGrid(); }
        }

        function handleRestock(e) {
            e.preventDefault();
            let id = document.getElementById("restockNameSelect").value;
            let qty = parseInt(document.getElementById("restockQty").value);
            products.find(x => x.id === id).stock += qty;
            renderAdminTable();
        }

        window.onload = () => { renderStoreGrid(); renderAdminTable(); };
    </script>
</body>
</html>
