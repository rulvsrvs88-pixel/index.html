<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ERP de Inventario - Modo Alta Legibilidad</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Forzar letra negra en toda la tabla */
        table { color: #000000 !important; }
        .text-black-bold { font-weight: 800 !important; color: #000000 !important; }
    </style>
</head>
<body class="bg-gray-100 text-gray-900 font-sans">

    <header class="bg-white border-b border-gray-300 p-6 shadow-sm">
        <div class="max-w-7xl mx-auto flex justify-between items-center">
            <h1 class="text-2xl font-black text-black">Simulador de Inventario V6.0</h1>
            <div class="flex gap-2">
                <button onclick="switchView('store')" id="btnTabStore" class="px-4 py-2 font-bold bg-black text-white rounded">Tienda</button>
                <button onclick="switchView('admin')" id="btnTabAdmin" class="px-4 py-2 font-bold bg-gray-300 text-black rounded">Almacén</button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-6">
        
        <div id="viewStore" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <div id="storeGrid" class="lg:col-span-2 grid grid-cols-1 md:grid-cols-2 gap-4"></div>
            
            <div class="bg-white p-6 border border-gray-300 rounded shadow-sm">
                <h2 class="font-black text-lg mb-4 uppercase">Lista de Compra</h2>
                <div id="cartItems" class="space-y-2 mb-4"></div>
                <button onclick="checkoutCart()" class="w-full bg-blue-600 text-white font-black py-3 rounded">Confirmar Compra</button>
            </div>
        </div>

        <div id="viewAdmin" class="hidden bg-white p-6 border border-gray-300 rounded shadow-sm">
            <h2 class="font-black text-xl mb-6 uppercase">Control de Existencias</h2>
            <div class="overflow-x-auto">
                <table class="w-full text-left">
                    <thead>
                        <tr class="border-b-2 border-black">
                            <th class="p-3 font-black uppercase">Componente</th>
                            <th class="p-3 font-black uppercase">Stock</th>
                            <th class="p-3 font-black uppercase">Acción</th>
                        </tr>
                    </thead>
                    <tbody id="adminTableBody"></tbody>
                </table>
            </div>
        </div>
    </main>

    <script>
        let products = JSON.parse(localStorage.getItem('v6_products')) || [
            { id: "COMP-001", nombre: "MEMORIA RAM DDR4 8GB", stock: 10 },
            { id: "COMP-002", nombre: "TECLADO MECÁNICO LOGITECH", stock: 3 },
            { id: "COMP-003", nombre: "CABLE DE CORRIENTE 1.8M", stock: 47 }
        ];

        function switchView(view) {
            document.getElementById('viewStore').classList.toggle('hidden', view === 'admin');
            document.getElementById('viewAdmin').classList.toggle('hidden', view === 'store');
        }

        function render() {
            localStorage.setItem('v6_products', JSON.stringify(products));
            
            // Render Tienda
            const grid = document.getElementById('storeGrid');
            grid.innerHTML = products.map(p => `
                <div class="bg-white border-2 border-black p-4">
                    <p class="font-black text-lg">${p.nombre}</p>
                    <p class="font-bold">Existencias: ${p.stock}</p>
                    <button onclick="addToCart('${p.id}')" class="mt-2 w-full bg-black text-white font-black py-2">COMPRAR</button>
                </div>
            `).join('');

            // Render Tabla Admin
            const tbody = document.getElementById('adminTableBody');
            tbody.innerHTML = products.map(p => `
                <tr class="border-b border-gray-300">
                    <td class="p-4 font-black text-black">${p.nombre}</td>
                    <td class="p-4 font-black text-black">${p.stock}</td>
                    <td class="p-4">
                        <button onclick="restock('${p.id}')" class="bg-black text-white font-black px-4 py-1">SURTIR +</button>
                    </td>
                </tr>
            `).join('');
        }

        function addToCart(id) {
            let p = products.find(i => i.id === id);
            if(p.stock > 0) { p.stock--; render(); }
        }

        function restock(id) {
            let p = products.find(i => i.id === id);
            p.stock += 10; render();
        }

        window.onload = render;
    </script>
</body>
</html>
