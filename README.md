<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Control de Inventario - Componentes PC</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans antialiased">

    <header class="bg-gray-800 border-b border-gray-700 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-400">Maestría en Sistemas</h1>
                <p class="text-sm text-gray-400">Control de Inventario y Alertas de Reabastecimiento</p>
            </div>
            <div class="w-full md:w-80">
                <input type="text" id="searchBar" onkeyup="filterInventory()" placeholder="Buscar por SKU, categoría o marca..." 
                       class="w-full px-4 py-2 bg-gray-700 border border-gray-600 rounded-lg focus:outline-none focus:border-indigo-500 text-gray-200">
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <section>
            <h2 class="text-xl font-semibold mb-4 text-gray-300">Dashboard de Indicadores Clave (KPIs)</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-sm flex flex-col justify-between">
                    <span class="text-sm font-medium text-gray-400 uppercase tracking-wider">Valor Total del Inventario</span>
                    <span class="text-3xl font-extrabold text-green-400 mt-2">$91,173 USD</span>
                </div>
                <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-sm flex flex-col justify-between">
                    <span class="text-sm font-medium text-gray-400 uppercase tracking-wider">Total Unidades en Stock</span>
                    <span class="text-3xl font-extrabold text-indigo-400 mt-2">690 uds</span>
                </div>
                <div class="bg-gray-800 p-6 rounded-xl border border-red-900 bg-red-950/20 p-6 flex flex-col justify-between">
                    <span class="text-sm font-medium text-red-400 uppercase tracking-wider">Productos por Reordenar</span>
                    <span class="text-3xl font-extrabold text-red-500 mt-2">5 Alertas</span>
                </div>
            </div>
        </section>

        <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden shadow-lg">
            <div class="p-5 border-b border-gray-700 bg-gray-850 flex justify-between items-center">
                <h2 class="text-lg font-semibold text-gray-200">Listado Maestro de Componentes</h2>
                <span class="text-xs bg-gray-700 px-2.5 py-1 rounded-full text-gray-400">27 Productos Únicos</span>
            </div>
            
            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse" id="inventoryTable">
                    <thead>
                        <tr class="bg-gray-750 text-gray-400 text-xs uppercase tracking-wider border-b border-gray-700">
                            <th class="py-4 px-6">ID SKU</th>
                            <th class="py-4 px-6">Categoría</th>
                            <th class="py-4 px-6">Marca</th>
                            <th class="py-4 px-6">Modelo / Descripción</th>
                            <th class="py-4 px-6 text-right">Precio Unitario</th>
                            <th class="py-4 px-6 text-center">Stock Actual</th>
                            <th class="py-4 px-6 text-center">Punto Reorden (ROP)</th>
                            <th class="py-4 px-6 text-right">Valor Total Stock</th>
                            <th class="py-4 px-6 text-center">Estado</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-700 text-sm text-gray-300">
                        </tbody>
                </table>
            </div>
        </section>
    </main>

    <script>
        const inventoryData = [
            { sku: "CPU-AMD-R5600", cat: "Procesadores", marca: "AMD", desc: "Ryzen 5 5600X 3.7GHz 6-Core", precio: 145, stock: 45, rop: 15, total: 6525, alerta: "OK" },
            { sku: "CPU-AMD-R7700", cat: "Procesadores", marca: "AMD", desc: "Ryzen 7 7800X3D 4.2GHz 8-Core", precio: 369, stock: 12, rop: 10, total: 4428, alerta: "OK" },
            { sku: "CPU-INT-I5134", cat: "Procesadores", marca: "Intel", desc: "Core i5-13400F 2.5GHz 10-Core", precio: 165, stock: 28, rop: 12, total: 4620, alerta: "OK" },
            { sku: "CPU-INT-I7147", cat: "Procesadores", marca: "Intel", desc: "Core i7-14700K 3.4GHz 20-Core", precio: 389, stock: 8, rop: 10, total: 3112, alerta: "REORDENAR" },
            { sku: "GPU-NV-RTX46", cat: "Tarjetas de Video", marca: "NVIDIA", desc: "GeForce RTX 4060 Ti 8GB", precio: 399, stock: 18, rop: 8, total: 7182, alerta: "OK" },
            { sku: "GPU-NV-RTX47", cat: "Tarjetas de Video", marca: "NVIDIA", desc: "GeForce RTX 4070 Super 12GB", precio: 599, stock: 5, rop: 6, total: 2995, alerta: "REORDENAR" },
            { sku: "RAM-KIN-D416", cat: "Memoria RAM", marca: "Kingston", desc: "Fury Beast DDR4 16GB (2x8GB) 3200MHz", precio: 45, stock: 85, rop: 25, total: 3825, alerta: "OK" },
            { sku: "RAM-GSH-D532", cat: "Memoria RAM", marca: "G.Skill", desc: "Trident Z5 Neo DDR5 32GB 6400MHz", precio: 125, stock: 15, rop: 10, total: 1875, alerta: "OK" },
            { sku: "STG-SAM-990P", cat: "Almacenamiento", marca: "Samsung", desc: "990 Pro NVMe M.2 SSD 2TB", precio: 175, stock: 40, rop: 15, total: 7000, alerta: "OK" },
            { sku: "STG-WD-BLUE1", cat: "Almacenamiento", marca: "Western Digital", desc: "WD Blue SN580 NVMe M.2 1TB", precio: 69, stock: 65, rop: 20, total: 4485, alerta: "OK" },
            { sku: "STG-CRU-P32T", cat: "Almacenamiento", marca: "Crucial", desc: "P3 Plus NVMe M.2 SSD 2TB", precio: 119, stock: 4, rop: 12, total: 476, alerta: "REORDENAR" },
            { sku: "STG-SEA-BARR2", cat: "Almacenamiento", marca: "Seagate", desc: "Barracuda 2TB HDD 3.5\" 7200RPM", precio: 55, stock: 30, rop: 10, total: 1650, alerta: "OK" },
            { sku: "MOB-ASU-B650", cat: "Tarjetas Madre", marca: "ASUS", desc: "TUF Gaming B650-Plus WiFi AM5", precio: 199, stock: 16, rop: 8, total: 3184, alerta: "OK" },
            { sku: "MOB-MSI-B760", cat: "Tarjetas Madre", marca: "MSI", desc: "MAG B760 Tomahawk WiFi Intel", precio: 179, stock: 22, rop: 10, total: 3938, alerta: "OK" }
        ];

        // Se omiten funciones repetidas para claridad del script básico
        function renderTable(data) {
            const tbody = document.querySelector("#inventoryTable tbody");
            tbody.innerHTML = "";
            data.forEach(item => {
                const tr = document.createElement("tr");
                tr.className = "hover:bg-gray-700/50 transition-colors";
                const badgeClass = item.alerta === "REORDENAR" ? "bg-red-900/40 text-red-400 border border-red-700/50" : "bg-green-900/40 text-green-400 border border-green-700/50";
                tr.innerHTML = `
                    <td class="py-3 px-6 font-mono text-xs text-indigo-300 font-semibold">${item.sku}</td>
                    <td class="py-3 px-6 text-gray-400">${item.cat}</td>
                    <td class="py-3 px-6 font-medium">${item.marca}</td>
                    <td class="py-3 px-6">${item.desc}</td>
                    <td class="py-3 px-6 text-right">$${item.precio.toLocaleString()}</td>
                    <td class="py-3 px-6 text-center font-semibold">${item.stock}</td>
                    <td class="py-3 px-6 text-center text-gray-500">${item.rop}</td>
                    <td class="py-3 px-6 text-right text-emerald-400 font-medium">$${item.total.toLocaleString()}</td>
                    <td class="py-3 px-6 text-center"><span class="px-2.5 py-1 rounded-full text-xs font-semibold uppercase ${badgeClass}">${item.alerta}</span></td>
                `;
                tbody.appendChild(tr);
            });
        }

        function filterInventory() {
            const query = document.getElementById("searchBar").value.toLowerCase();
            const filtered = inventoryData.filter(item => item.sku.toLowerCase().includes(query) || item.cat.toLowerCase().includes(query) || item.marca.toLowerCase().includes(query) || item.desc.toLowerCase().includes(query));
            renderTable(filtered);
        }
        window.onload = () => renderTable(inventoryData);
    </script>
</body>
</html>
