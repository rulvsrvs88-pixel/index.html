<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ERP & E-Commerce - Control de Inventario Maestro</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans antialiased">

    <header class="bg-indigo-950 border-b border-indigo-800 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-400">Simulador Logístico y Comercial v4.0</h1>
                <p class="text-sm text-gray-300">Entorno Integrado: Almacén (EOQ/ROP) + Tienda de Componentes</p>
            </div>
            
            <div class="flex bg-gray-800 p-1 rounded-lg border border-indigo-700">
                <button onclick="switchView('admin')" id="btnTabAdmin" class="px-4 py-2 text-sm font-bold rounded-md bg-indigo-600 text-white transition-all">
                    ⚙️ Modo Almacén (Admin)
                </button>
                <button onclick="switchView('store')" id="btnTabStore" class="px-4 py-2 text-sm font-bold rounded-md text-gray-400 hover:text-white transition-all">
                    🛒 Modo Tienda (Cliente)
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <div id="viewAdmin" class="space-y-8">
            <section class="grid grid-cols-1 md:grid-cols-4 gap-4">
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase">Artículos en Catálogo</span>
                    <span id="kpiItems" class="text-2xl font-extrabold text-indigo-400 block mt-1">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase">Inversión Almacén</span>
                    <span id="kpiCapital" class="text-2xl font-extrabold text-emerald-400 block mt-1">$0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase">Alertas Críticas (ROP)</span>
                    <span id="kpiAlerts" class="text-2xl font-extrabold text-red-500 block mt-1">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-indigo-900 bg-indigo-950/20">
                    <span class="text-xs font-medium text-indigo-400 uppercase">Caja Registradora Tienda</span>
                    <span id="kpiSales" class="text-2xl font-extrabold text-amber-400 block mt-1">$0</span>
                </div>
            </section>

            <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
                <h3 class="text-lg font-bold text-emerald-400 mb-4 flex items-center gap-2">
                    <span>📦</span> Registrar Entrada por Reabastecimiento (Surtir Lote EOQ)
                </h3>
                <form id="restockForm" onsubmit="handleRestock(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
                    <div>
                        <label class="block text-xs text-gray-400 mb-1">Seleccionar Componente</label>
                        <select id="restockId" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required></select>
                    </div>
                    <div>
                        <label class="block text-xs text-gray-400 mb-1">Cantidad a Surtir</label>
                        <input type="number" id="restockQty" min="1" placeholder="Ej: 40" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white" required>
                    </div>
                    <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2 rounded text-sm transition-all shadow">
                        Ingresar Unidades a Almacén
                    </button>
                </form>
            </section>

            <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden shadow-lg">
                <div class="p-5 border-b border-gray-700 bg-gray-850 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2">
                    <div>
                        <h2 class="text-lg font-semibold text-gray-200">Matriz Operativa de Almacén (Control Interno)</h2>
                        <p class="text-xs text-gray-400">Monitoreo matemático de variables. Las compras de los clientes afectan esta tabla directamente.</p>
                    </div>
                    <button onclick="resetToDefaultData()" class="bg-gray-700 hover:bg-gray-600 border border-gray-600 text-gray-300 text-2xs px-3 py-1.5 rounded transition-all">
                        Reiniciar Datos Originales
                    </button>
                </div>
                
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse" id="inventoryTable">
                        <thead>
                            <tr class="bg-gray-750 text-gray-400 text-xs uppercase border-b border-gray-700">
                                <th class="py-4 px-4">ID</th>
                                <th class="py-4 px-4">Descripción Componente</th>
                                <th class="py-4 px-4">Categoría</th>
                                <th class="py-4 px-4 text-center">Stock Físico</th>
                                <th class="py-4 px-4 text-right">Costo U.</th>
                                <th class="py-4 px-4 text-right">Valor Total Stock</th>
                                <th class="py-4 px-4 text-center text-indigo-400">Lote (EOQ)</th>
                                <th class="py-4 px-4 text-center text-amber-400">Reorden (ROP)</th>
                                <th class="py-4 px-4 text-center">Estado Alerta</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-gray-700 text-sm text-gray-300"></tbody>
                    </table>
                </div>
            </section>
        </div>


        <div id="viewStore" class="hidden grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="lg:col-span-2 space-y-6">
                <h2 class="text-xl font-bold text-indigo-400 flex items-center gap-2">
                    <span>💻</span> Componentes Disponibles para Compra Inmediata
                </h2>
                <div id="storeGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-
