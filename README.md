<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Control de Inventario Maestro - EOQ/ROP</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans antialiased">

    <header class="bg-indigo-950 border-b border-indigo-800 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-400">Control de Inventario Maestro</h1>
                <p class="text-sm text-gray-300">Optimización de Compra mediante Metodología EOQ & ROP</p>
            </div>
            <div class="w-full md:w-96">
                <input type="text" id="searchBar" onkeyup="filterInventory()" placeholder="Buscar componente, categoría o ID..." 
                       class="w-full px-4 py-2 bg-gray-800 border border-indigo-700 rounded-lg focus:outline-none focus:border-indigo-400 text-gray-200">
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <section>
            <h2 class="text-xl font-semibold mb-4 text-gray-300">Resumen Logístico Gerencial</h2>
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase tracking-wider">Catálogo Total</span>
                    <span class="text-2xl font-extrabold text-indigo-400 block mt-1">100 Componentes</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase tracking-wider">Último Movimiento</span>
                    <span class="text-sm font-semibold text-emerald-400 block mt-1">OC-503 Surtido (EOQ)</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase tracking-wider">Responsable Activo</span>
                    <span class="text-base font-bold text-gray-200 block mt-1">Raúl Villegas</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-red-900 bg-red-950/10">
                    <span class="text-xs font-medium text-red-400 uppercase tracking-wider">Estatus de Alerta</span>
                    <span class="text-2xl font-extrabold text-red-500 block mt-1">Monitoreo ROP Activo</span>
                </div>
            </div>
        </section>

        <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden shadow-lg">
            <div class="p-5 border-b border-gray-700 bg-gray-850 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2">
                <div>
                    <h2 class="text-lg font-semibold text-gray-200">Matriz Logística de Almacén</h2>
                    <p class="text-xs text-gray-400">Modelado matemático para cálculo de lote óptimo y punto de reorden diario</p>
                </div>
                <span class="text-xs bg-indigo-900/50 text-indigo-300 border border-indigo-700/50 px-3 py-1 rounded-full font-medium">Filtro Inteligente</span>
            </div>
            
            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse" id="inventoryTable">
                    <thead>
                        <tr class="bg-gray-750 text-gray-400 text-xs uppercase tracking-wider border-b border-gray-700">
                            <th class="py-4 px-4">ID</th>
                            <th class="py-4 px-4">Nombre del Componente</th>
                            <th class="py-4 px-4">Categoría</th>
                            <th class="py-4 px-4 text-center">Stock Actual</th>
                            <th class="py-4 px-4 text-right">Costo Unitario</th>
                            <th class="py-4 px-4 text-center">Demanda Anual (D)</th>
                            <th class="py-4 px-4 text-center">Costo Pedido (S)</th>
                            <th class="py-4 px-4 text-center">Mantenimiento (H)</th>
                            <th class="py-4 px-4 text-center text-indigo-400">Lote (EOQ)</th>
                            <th class="py-4 px-4 text-center text-amber-400">Reorden (ROP)</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-700 text-sm text-gray-300">
                        </tbody>
                </table>
            </div>
        </section>

        <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
            <h3 class="text-lg font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Bitácora Diaria de Entradas y Salidas</h3>
            <div class="space-y-3 max-h-60 overflow-y-auto pr-2">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center p-3 bg-gray-750 rounded-lg border border-gray-700 gap-2">
                    <div>
                        <span class="text-xs font-mono text-gray-400">2026-05-19 10:00</span> - 
                        <span class="font-semibold text-indigo-400">COMP-005</span> 
                        <span class="text-xs bg-emerald-900/40 text-emerald-400 border border-emerald-700/50 px-2 py-0.5 rounded ml-2">ENTRADA (COMPRA)</span>
                    </div>
                    <div class="text-right text-xs">
                        <p class="text-gray-300">Cantidad: <span class="font-bold">45</span> | Responsable: <span class="text-gray-400">SISTEMA AUTOMÁTICO</span></p>
                        <p class="text-gray-500 italic">"Lote EOQ surtido por proveedor, OC-501"</p>
                    </div>
                </div>
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center p-3 bg-gray-750 rounded-lg border border-gray-700 gap-2">
                    <div>
                        <span class="text-xs font-mono text-gray-400">2026-05-18 16:45</span> - 
                        <span class="font-semibold text-indigo-400">COMP-088</span> 
                        <span class="text-xs bg-red-900/40 text-red-400 border border-red-700/50 px-2 py-0.5 rounded ml-2">SALIDA</span>
                    </div>
                    <div class="text-right text-xs">
                        <p class="text-gray-300">Cantidad: <span class="font-bold">5</span> | Responsable: <span class="text-gray-400">Martínez E.</span></p>
                        <p class="text-gray-500 italic">"Laboratorio de Switches Cisco de alumnos"</p>
                    </div>
                </div>
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center p-3 bg-gray-750 rounded-lg border border-gray-700 gap-2">
                    <div>
                        <span class="text-xs font-mono text-gray-400">2026-05-17 14:00</span> - 
                        <span class="font-semibold text-indigo-400">COMP-015</span> 
                        <span class="text-xs bg-red-900/40 text-red-400 border border-red-700/50 px-2 py-0.5 rounded ml-2">SALIDA</span>
                    </div>
                    <div class="text-right text-xs">
                        <p class="text-gray-300">Cantidad: <span class="font-bold">2</span> | Responsable: <span class="text-gray-400">Raúl Villegas</span></p>
                        <p class="text-gray-500 italic">"Sustitución de disco dañado en servidor"</p>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <script>
        const inventoryData = [
            { id: "COMP-001", nombre: "RAM DDR4 8GB Kingston Mod-45", cat: "Memorias", stock: 10, costo: 1101, d: 364, s: 288, h: 143, eoq: 38, rop: 12 },
            { id: "COMP-002", nombre: "Teclado Mecánico Logitech Mod-85", cat: "Periféricos y Accesorios", stock: 3, costo: 296, d: 532, s: 123, h: 41, eoq: 56, rop: 18 },
            { id: "COMP-003", nombre: "Cable de Corriente Trébol 1.8m Mod-13", cat: "Periféricos y Accesorios", stock: 47, costo: 367, d: 703, s: 266, h: 69, eoq: 74, rop: 23 },
            { id: "COMP-004", nombre: "Gabinete ATX Corsair 4000D Mod-67", cat: "Gabinete y Chasis", stock: 53, costo: 381, d: 784, s: 101, h: 74, eoq: 46, rop: 26 },
            { id: "COMP-005", nombre: "Disco Duro Enterprise 4TB Mod-64", cat: "Almacenamiento", stock: 11, costo: 1296, d: 392, s: 155, h: 279, eoq: 21, rop: 13 },
            { id: "COMP-006", nombre: "Disipador de Aire Cooler Master Mod-21", cat: "Enfriamiento", stock: 24, costo: 274, d: 599, s: 188, h: 49, eoq: 68, rop: 20 },
            { id: "COMP-083", nombre: "Switch Cisco Catalyst 24 Puertos Mod-55", cat: "Redes e Infraestructura", stock: 26, costo: 3946, d: 292, s: 207, h: 685, eoq: 13, rop: 10 },
            { id: "COMP-084", nombre: "Cable de Corriente Trébol 1.8m Mod-38", cat: "Periféricos y Accesorios", stock: 19, costo: 329, d: 724, s: 211, h: 55, eoq: 74, rop: 24 },
            { id: "COMP-085", nombre: "Gabinete Micro-ATX NZXT H5 Mod-95", cat: "Gabinete y Chasis", stock: 27, costo: 427, d: 1317, s: 285, h: 58, eoq: 114, rop: 44 },
            { id: "COMP-086", nombre: "Access Point Cisco Catalyst Mod-89", cat: "Redes e Infraestructura", stock: 60, costo: 5875, d: 106, s: 200, h: 1052, eoq: 6, rop: 4 },
            { id: "COMP-087", nombre: "Intel Core i5 Mod-92", cat: "Procesadores", stock: 57, costo: 5011, d: 134, s: 218, h: 692, eoq: 9, rop: 5 },
            { id: "COMP-088", nombre: "Fuente 850W ASUS ROG Gold Mod-51", cat: "Fuentes de Poder", stock: 22, costo: 1033, d: 482, s: 186, h: 186, eoq: 31, rop: 16 },
            { id: "COMP-089", nombre: "Gabinete Micro-ATX NZXT H5 Mod-63", cat: "Gabinete y Chasis", stock: 7, costo: 209, d: 1354, s: 220, h: 25, eoq: 154, rop: 45 }
        ];

        function renderTable(data) {
            const tbody = document.querySelector("#inventoryTable tbody");
            tbody.innerHTML = "";
            
            data.forEach(item => {
                const tr = document.createElement("tr");
                tr.className = "hover:bg-gray-700/40 transition-colors border-b border-gray-700";
                
                // Resaltado visual si está debajo del punto de reorden (ROP)
                const alertStyle = item.stock <= item.rop ? "text-amber-400 font-bold bg-amber-950/20" : "";

                tr.innerHTML = `
                    <td class="py-3 px-4 font-mono text-xs text-indigo-300 font-semibold">${item.id}</td>
                    <td class="py-3 px-4 font-medium">${item.nombre}</td>
                    <td class="py-3 px-4 text-gray-400">${item.cat}</td>
                    <td class="py-3 px-4 text-center ${alertStyle}">${item.stock}</td>
                    <td class="py-3 px-4 text-right">$${item.costo.toLocaleString()}</td>
                    <td class="py-3 px-4 text-center text-gray-400">${item.d}</td>
                    <td class="py-3 px-4 text-center text-gray-500">${item.s}</td>
                    <td class="py-3 px-4 text-center text-gray-500">${item.h}</td>
                    <td class="py-3 px-4 text-center font-bold text-indigo-400">${item.eoq}</td>
                    <td class="py-3 px-4 text-center font-bold text-amber-500">${item.rop}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterInventory() {
            const query = document.getElementById("searchBar").value.toLowerCase();
            const filtered = inventoryData.filter(item => 
                item.id.toLowerCase().includes(query) ||
                item.nombre.toLowerCase().includes(query) ||
                item.cat.toLowerCase().includes(query)
            );
            renderTable(filtered);
        }

        window.onload = () => renderTable(inventoryData);
    </script>
</body>
</html>
