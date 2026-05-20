<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ERP Logístico - Control de Inventario Maestro</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans antialiased">

    <header class="bg-indigo-950 border-b border-indigo-800 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-400">Sistema Logístico de Almacén v3.0</h1>
                <p class="text-sm text-gray-300">Simulador Empresarial - Manipulación Directa de Stock, EOQ y ROP</p>
            </div>
            <div class="w-full md:w-96">
                <input type="text" id="searchBar" onkeyup="filterInventory()" placeholder="Buscar por ID, componente o categoría..." 
                       class="w-full px-4 py-2 bg-gray-800 border border-indigo-700 rounded-lg focus:outline-none focus:border-indigo-400 text-gray-200">
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <section class="grid grid-cols-1 md:grid-cols-4 gap-4">
            <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                <span class="text-xs font-medium text-gray-400 uppercase">Artículos en Catálogo</span>
                <span id="kpiItems" class="text-2xl font-extrabold text-indigo-400 block mt-1">0</span>
            </div>
            <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                <span class="text-xs font-medium text-gray-400 uppercase">Inversión Total Stock</span>
                <span id="kpiCapital" class="text-2xl font-extrabold text-emerald-400 block mt-1">$0 USD</span>
            </div>
            <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                <span class="text-xs font-medium text-gray-400 uppercase">Alertas Críticas (ROP)</span>
                <span id="kpiAlerts" class="text-2xl font-extrabold text-red-500 block mt-1">0</span>
            </div>
            <div class="bg-gray-800 p-5 rounded-xl border border-indigo-900 bg-indigo-950/20">
                <span class="text-xs font-medium text-indigo-400 uppercase">Operador Activo</span>
                <span class="text-lg font-bold text-gray-200 block mt-1">Raúl Villegas Silva</span>
            </div>
        </section>

        <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
            <h3 class="text-lg font-bold text-emerald-400 mb-4 flex items-center gap-2">
                <span>➕</span> Añadir Nuevo Artículo al Catálogo
            </h3>
            <form id="newProductForm" onsubmit="handleNewProduct(event)" class="grid grid-cols-1 md:grid-cols-4 gap-4 items-end">
                <div>
                    <label class="block text-xs text-gray-400 mb-1">ID Código (Único)</label>
                    <input type="text" id="newId" placeholder="COMP-095" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <div class="md:col-span-2">
                    <label class="block text-xs text-gray-400 mb-1">Descripción / Modelo del Componente</label>
                    <input type="text" id="newName" placeholder="Ej: Memoria RAM DDR5 Kingston 32GB" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <div>
                    <label class="block text-xs text-gray-400 mb-1">Categoría</label>
                    <input type="text" id="newCat" placeholder="Memorias" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <div>
                    <label class="block text-xs text-gray-400 mb-1">Stock Inicial</label>
                    <input type="number" id="newStock" min="0" value="10" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <div>
                    <label class="block text-xs text-gray-400 mb-1">Costo Unitario ($)</label>
                    <input type="number" id="newCosto" min="1" value="120" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <div>
                    <label class="block text-xs text-gray-400 mb-1">Demanda Anual (D)</label>
                    <input type="number" id="newD" min="1" value="400" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required>
                </div>
                <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2 rounded text-sm transition-all shadow h-9">
                    Insertar al Catálogo
                </button>
            </form>
        </section>

        <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden shadow-lg">
            <div class="p-5 border-b border-gray-700 bg-gray-850 flex justify-between items-center">
                <div>
                    <h2 class="text-lg font-semibold text-gray-200">Panel de Control General de Inventario</h2>
                    <p class="text-xs text-gray-400">Manipula el Stock Físico usando los botones <span class="bg-gray-700 px-1 rounded text-white font-mono"> - </span> y <span class="bg-indigo-600 px-1 rounded text-white font-mono"> + </span> dentro de la tabla.</p>
                </div>
                <button onclick="resetToDefaultData()" class="bg-gray-700 hover:bg-gray-600 border border-gray-600 text-gray-300 text-xs px-3 py-1.5 rounded transition-all">
                    Reiniciar Tabla Original
                </button>
            </div>
            
            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse" id="inventoryTable">
                    <thead>
                        <tr class="bg-gray-750 text-gray-400 text-xs uppercase border-b border-gray-700">
                            <th class="py-4 px-4">ID</th>
                            <th class="py-4 px-4">Descripción Componente</th>
                            <th class="py-4 px-4">Categoría</th>
                            <th class="py-4 px-4 text-center w-40">Stock Físico</th>
                            <th class="py-4 px-4 text-right">Costo U.</th>
                            <th class="py-4 px-4 text-right">Inversión Total</th>
                            <th class="py-4 px-4 text-center text-indigo-400">Lote (EOQ)</th>
                            <th class="py-4 px-4 text-center text-amber-400">Reorden (ROP)</th>
                            <th class="py-4 px-4 text-center">Estado</th>
                            <th class="py-4 px-4 text-center">Acción</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-700 text-sm text-gray-300"></tbody>
                </table>
            </div>
        </section>

        <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
            <h3 class="text-lg font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">
                📋 Bitácora del Sistema (Historial Automatizado de Clicks)
            </h3>
            <div id="logContainer" class="space-y-2 max-h-48 overflow-y-auto pr-2"></div>
        </section>
    </main>

    <script>
        // Mapeo automático de tu Excel
        const defaultProducts = [
            { id: "COMP-001", nombre: "RAM DDR4 8GB Kingston Mod-45", cat: "Memorias", stock: 10, costo: 1101, d: 364, s: 288, h: 143 },
            { id: "COMP-002", nombre: "Teclado Mecánico Logitech Mod-85", cat: "Periféricos", stock: 3, costo: 296, d: 532, s: 123, h: 41 },
            { id: "COMP-003", nombre: "Cable de Corriente Trébol 1.8m Mod-13", cat: "Accesorios", stock: 47, costo: 367, d: 703, s: 266, h: 69 },
            { id: "COMP-004", nombre: "Gabinete ATX Corsair 4000D Mod-67", cat: "Chasis", stock: 53, costo: 381, d: 784, s: 101, h: 74 },
            { id: "COMP-005", nombre: "Disco Duro Enterprise 4TB Mod-64", cat: "Almacenamiento", stock: 11, costo: 1296, d: 392, s: 155, h: 279 },
            { id: "COMP-006", nombre: "Disipador de Aire Master Mod-21", cat: "Enfriamiento", stock: 24, costo: 274, d: 599, s: 188, h: 49 },
            { id: "COMP-083", nombre: "Switch Cisco Catalyst 24P Mod-55", cat: "Redes", stock: 26, costo: 3946, d: 292, s: 207, h: 685 },
            { id: "COMP-087", nombre: "Intel Core i5 Mod-92", cat: "Procesadores", stock: 57, costo: 5011, d: 134, s: 218, h: 692 },
            { id: "COMP-088", nombre: "Fuente 850W ASUS ROG Gold Mod-51", cat: "Fuentes", stock: 22, costo: 1033, d: 482, s: 186, h: 186 },
            { id: "COMP-089", nombre: "Gabinete Micro-ATX NZXT H5 Mod-63", cat: "Chasis", stock: 7, costo: 209, d: 1354, s: 220, h: 25 }
        ];

        let products = JSON.parse(localStorage.getItem('erp_products_v3')) || defaultProducts;
        let logs = JSON.parse(localStorage.getItem('erp_logs_v3')) || [{ time: "2026-05-19 20:00", text: "Sistema iniciado correctamente por Raúl Villegas." }];

        function
