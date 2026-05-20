<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ERP & Tienda Virtual de Componentes</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans antialiased">

    <header class="bg-indigo-950 border-b border-indigo-800 p-5 sticky top-0 z-50 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-indigo-400">Simulador de Almacén y Ventas v5.0</h1>
                <p class="text-sm text-gray-300">Catálogo Completo Visible — Control por Nombre de Componente</p>
            </div>
            
            <div class="flex bg-gray-800 p-1 rounded-lg border border-indigo-700">
                <button onclick="switchView('store')" id="btnTabStore" class="px-4 py-2 text-sm font-bold rounded-md bg-indigo-600 text-white transition-all">
                    🛒 Modo Tienda (Cliente)
                </button>
                <button onclick="switchView('admin')" id="btnTabAdmin" class="px-4 py-2 text-sm font-bold rounded-md text-gray-400 hover:text-white transition-all">
                    ⚙️ Modo Almacén (Admin)
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto p-4 md:p-6 space-y-8">

        <div id="viewStore" class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="lg:col-span-2 space-y-6">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2">
                    <h2 class="text-xl font-bold text-indigo-400 flex items-center gap-2">
                        <span>💻</span> Catálogo General de Componentes
                    </h2>
                    <p class="text-xs text-gray-400">Visualiza en tiempo real qué componentes están disponibles y cuáles están agotados.</p>
                </div>
                
                <div id="storeGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    </div>
            </div>

            <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 h-fit space-y-4 shadow-xl sticky top-28">
                <h3 class="text-lg font-bold text-amber-400 border-b border-gray-700 pb-2 flex items-center gap-2">
                    <span>🛒</span> Lista de Compra del Cliente
                </h3>
                <div id="cartItems" class="space-y-3 max-h-64 overflow-y-auto pr-1">
                    </div>
                <button onclick="checkoutCart()" class="w-full bg-amber-500 hover:bg-amber-600 text-gray-950 font-extrabold py-3 rounded-lg text-sm transition-all shadow uppercase tracking-wider mt-4">
                    Simular Entrega y Descontar de Almacén
                </button>
            </div>
        </div>


        <div id="viewAdmin" class="hidden space-y-8">
            
            <section class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase">Total Productos en Catálogo</span>
                    <span id="kpiItems" class="text-2xl font-extrabold text-indigo-400 block mt-1">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-gray-700">
                    <span class="text-xs font-medium text-gray-400 uppercase">Alertas Críticas (Bajo ROP)</span>
                    <span id="kpiAlerts" class="text-2xl font-extrabold text-red-500 block mt-1">0</span>
                </div>
                <div class="bg-gray-800 p-5 rounded-xl border border-indigo-900 bg-indigo-950/20">
                    <span class="text-xs font-medium text-indigo-400 uppercase">Encargado de Inventario</span>
                    <span class="text-lg font-bold text-gray-200 block mt-1">Raúl Villegas Silva</span>
                </div>
            </section>

            <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
                <h3 class="text-lg font-bold text-emerald-400 mb-4 flex items-center gap-2">
                    <span>📦</span> Surtir Almacén (Ingresar pedido de Proveedor)
                </h3>
                <form id="restockForm" onsubmit="handleRestock(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
                    <div class="md:col-span-2">
                        <label class="block text-xs text-gray-400 mb-1">Selecciona el Componente por su Nombre</label>
                        <select id="restockNameSelect" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white focus:border-emerald-500" required></select>
                    </div>
                    <div>
                        <label class="block text-xs text-gray-400 mb-1">Cantidad de piezas que entran</label>
                        <input type="number" id="restockQty" min="1" placeholder="Ej: 25" class="w-full bg-gray-700 border border-gray-600 rounded p-2 text-sm text-white" required>
                    </div>
                    <button type="submit" class="w-full md:col-span-3 bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2 rounded text-sm transition-all shadow">
                        Confirmar Entrada de Mercancía
                    </button>
                </form>
            </section>

            <section class="bg-gray-800 rounded-xl border border-gray-700 overflow-hidden shadow-lg">
                <div class="p-5 border-b border-gray-700 bg-gray-850 flex justify-between items-center">
                    <div>
                        <h2 class="text-lg font-semibold text-gray-200">Matriz de Control Técnico (Variables EOQ/ROP)</h2>
                    </div>
                    <button onclick="resetToDefaultData()" class="bg-gray-700 hover:bg-gray-600 border border-gray-600 text-gray-300 text-2xs px-3 py-1.5 rounded transition-all">
                        Restablecer Valores Iniciales
                    </button>
                </div>
                
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse" id="inventoryTable">
                        <thead>
                            <tr class="bg-gray-750 text-gray-400 text-xs uppercase border-b border-gray-700">
                                <th class="py-4 px-4">Componente</th>
                                <th class="py-4 px-4">Categoría</th>
                                <th class="py-4 px-4 text-center">Stock Disponible</th>
                                <th class="py-4 px-4 text-center text-indigo-400">Lote Sugerido (EOQ)</th>
                                <th class="py-4 px-4 text-center text-amber-400">Límite Mínimo (ROP)</th>
                                <th class="py-4 px-4 text-center">Estado Logístico</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-gray-700 text-sm text-gray-300"></tbody>
                    </table>
                </div>
            </section>
        </div>

        <section class="bg-gray-800 p-6 rounded-xl border border-gray-700 shadow-lg">
            <h3 class="text-sm font-semibold text-gray-200 mb-2 border-b border-gray-700 pb-2">
                📋 Registro Automatizado de Movimientos (Salidas por Tienda y Entradas por Almacén)
            </h3>
            <div id="logContainer" class="space-y-2 max-h-36 overflow-y-auto pr-2"></div>
        </section>
    </main>

    <script>
        // Datos base de los componentes
        const defaultProducts = [
            { id: "COMP-001", nombre: "Memoria RAM DDR4 8GB Kingston HyperX", cat: "Memorias", stock: 12, d: 364, s: 288, h: 143 },
            { id: "COMP-002", nombre: "Teclado Mecánico RGB Logitech G Pro", cat: "Periféricos", stock: 3, d: 532, s: 123, h: 41 },
            { id: "COMP-003", nombre: "Cable de Corriente Trébol Uso Rudo 1.8m", cat: "Accesorios", stock: 45, d: 703, s: 266, h: 69 },
            { id: "COMP-004", nombre: "Gabinete ATX Corsair 4000D Airflow Black", cat: "Chasis", stock: 6, d: 784, s: 101, h: 74 },
            { id: "COMP-005", nombre: "Disco Duro Interno Enterprise 4TB Seagate", cat: "Almacenamiento", stock: 10, d: 392, s: 155, h: 279 },
            { id: "COMP-006", nombre: "Disipador de Aire CPU Cooler Master Hyper", cat: "Enfriamiento", stock: 15, d: 599, s: 188, h: 49 },
            { id: "COMP-083", nombre: "Switch Cisco Catalyst 24 Puertos L3", cat: "Redes", stock: 4, d: 292, s: 207, h: 685 },
            { id: "COMP-087", nombre: "Procesador Intel Core i5-12400F", cat: "Procesadores", stock: 8, d: 134, s: 218, h: 692 },
            { id: "COMP-088", nombre: "Fuente de Poder 850W ASUS ROG Strix Gold", cat: "Fuentes", stock: 5, d: 482, s: 186, h: 186 }
        ];

        let products = JSON.parse(localStorage.getItem('st_products')) || defaultProducts;
        let logs = JSON.parse(localStorage.getItem('st_logs')) || [{ time: "2026-05-19 20:00", text: "Sistema comercial e inventario listo. Raúl Villegas activo." }];
        let cart = [];

        function calculateEOQ(d, s, h) { return Math.round(Math.sqrt((2 * d * s) / h)) || 1; }
        function calculateROP(d) { return Math.round((d / 365) * 12) || 1; }

        function addLog(text) {
            const now = new Date();
            const timeStr = now.toISOString().replace('T', ' ').substring(0, 16);
            logs.unshift({ time: timeStr, text: text });
        }

        // PESTAÑAS INTERACTIVAS
        function switchView(view) {
            const adminTab = document.getElementById("viewAdmin");
            const storeTab = document.getElementById("viewStore");
            const btnAdmin = document.getElementById("btnTabAdmin");
            const btnStore = document.getElementById("btnTabStore");

            if (view === 'admin') {
                adminTab.classList.remove("hidden");
                storeTab.classList.add("hidden");
                btnAdmin.className = "px-4 py-2 text-sm font-bold rounded-md bg-indigo-600 text-white transition-all";
                btnStore.className = "px-4 py-2 text-sm font-bold rounded-md text-gray-400 hover:text-white transition-all";
                renderAdminTable();
            } else {
                adminTab.classList.add("hidden");
                storeTab.classList.remove("hidden");
                btnAdmin.className = "px-4 py-2 text-sm font-bold rounded-md text-gray-400 hover:text-white transition-all";
                btnStore.className = "px-4 py-2 text-sm font-bold rounded-md bg-indigo-600 text-white transition-all";
                renderStoreGrid();
            }
        }

        // AÑADIR COMPONENTE AL CARRITO
        function addToCart(id) {
            const item = products.find(p => p.id === id);
            if (!item) return;

            const cartItem = cart.find(c => c.id === id);
            const currentQty = cartItem ? cartItem.qty : 0;

            if (currentQty >= item.stock) {
                alert(`⚠️ No puedes agregar más piezas. Almacén reporta que el stock físico llegó a su límite.`);
                return;
            }

            if (cartItem) {
                cartItem.qty++;
            } else {
                cart.push({ id: item.id, nombre: item.nombre, qty: 1 });
            }

            renderCartItems();
        }

        function changeCartQty(id, change) {
            const cartItem = cart.find(c => c.id === id);
            const p = products.find(prod => prod.id === id);
            if (!cartItem) return;

            if (change > 0 && cartItem.qty >= p.stock) {
                alert("⚠️ Límite de existencias alcanzado.");
                return;
            }

            cartItem.qty += change;
            if (cartItem.qty <= 0) cart.push;
            cart = cart.filter(c => c.id !== id);
            renderCartItems();
        }

        // CONFIRMAR TRANSACCIÓN DE VENTA
        function checkoutCart() {
            if (cart.length === 0) {
                alert("🛒 Por favor, añade algún componente del catálogo a la lista antes de procesar.");
                return;
            }

            cart.forEach(cartItem => {
                const product = products.find(p => p.id === cartItem.id);
                if (product) {
                    product.stock -= cartItem.qty;
                    addLog(`🛒 TIENDA: Cliente adquirió ${cartItem.qty} pieza(s) de "${cartItem.nombre}".`);
                    
                    // Alerta ROP inmediata
                    if (product.stock <= calculateROP(product.stock)) {
                        addLog(`🚨 ALERTA: "${product.nombre}" bajó a las ${product.stock} unidades. Nivel crítico.`);
                    }
                }
            });

            alert("✅ ¡Venta simulada con éxito! Las piezas se han rebajado de las existencias reales.");
            cart = [];
            renderCartItems();
            renderStoreGrid();
        }

        // REABASTECIMIENTO DE PIEZAS DESDE MODO ADMIN
        function handleRestock(e) {
            e.preventDefault();
            const id = document.getElementById("restockNameSelect").value;
            const qty = parseInt(document.getElementById("restockQty").value);
            const p = products.find(prod => prod.id === id);

            if (!p) return;

            p.stock += qty;
            addLog(`📦 ALMACÉN: Se ingresaron ${qty} unidades de "${p.nombre}" para reabastecimiento.`);
            document.getElementById("restockForm").reset();
            renderAdminTable();
            alert(`Listo Raúl, se sumaron las piezas de forma correcta.`);
        }

        // RENDER DE LA TIENDA VIRTUAL (CATÁLOGO GENERAL)
        function renderStoreGrid() {
            localStorage.setItem('st_products', JSON.stringify(products));
            localStorage.setItem('st_logs', JSON.stringify(logs));

            const grid = document.getElementById("storeGrid");
            grid.innerHTML = "";

            products.forEach(p => {
                const agotado = p.stock <= 0;
                const div = document.createElement("div");
                div.className = `bg-gray-800 p-4 rounded-xl border border-gray-700 shadow flex flex-col justify-between h-36 transition-all ${agotado ? 'border-red-900/50 bg-gray-850/70' : ''}`;
                
                div.innerHTML = `
                    <div>
                        <div class="flex justify-between items-center text-2xs font-mono text-gray-500 mb-1">
                            <span class="bg-gray-700 px-2 py-0.5 rounded text-gray-300 font-sans font-semibold">${p.cat}</span>
                        </div>
                        <h4 class="text-sm font-bold ${agotado ? 'text-gray-500 line-through' : 'text-gray-100'}">${p.nombre}</h4>
                    </div>
                    
                    <div class="flex justify-between items-center pt-2 border-t border-gray-750">
                        <span class="text-xs text-gray-400">Disponibles: <strong class="${agotado ? 'text-red-500' : 'text-white'}">${p.stock} piezas</strong></span>
                        <button onclick="addToCart('${p.id}')" ${agotado ? 'disabled' : ''} 
                                class="px-4 py-2 rounded text-xs font-bold transition-all ${agotado ? 'bg-red-950/30 text-red-400 border border-red-900/40 cursor-not-allowed' : 'bg-indigo-600 hover:bg-indigo-500 text-white shadow'}">
                            ${agotado ? '🚨 AGOTADO EN ALMACÉN' : '🛒 Añadir a Lista'}
                        </button>
                    </div>
                `;
                grid.appendChild(div);
            });
            renderLogs();
        }

        // RENDER DEL CARRITO SIN DETALLES EXTRA DE COSTOS
        function renderCartItems() {
            const container = document.getElementById("cartItems");
            container.innerHTML = "";

            if (cart.length === 0) {
                container.innerHTML = `<p class="text-xs text-gray-500 text-center py-8 italic">No hay artículos listados para la compra.</p>`;
                return;
            }

            cart.forEach(c => {
                const div = document.createElement("div");
                div.className = "p-3 bg-gray-750 rounded-lg border border-gray-700 text-xs flex justify-between items-center";
                div.innerHTML = `
                    <span class="font-medium text-gray-200 max-w-[75%] truncate">${c.nombre}</span>
                    <span class="bg-amber-500/10 border border-amber-500/30 text-amber-400 px-2 py-0.5 rounded font-bold font-mono">${c.qty} pza(s)</span>
                `;
                container.appendChild(div);
            });
        }

        // RENDER DE LA MATRIZ LOGÍSTICA DE ADMINISTRACIÓN
        function renderAdminTable() {
            localStorage.setItem('st_products', JSON.stringify(products));
            localStorage.setItem('st_logs', JSON.stringify(logs));

            const tbody = document.querySelector("#inventoryTable tbody");
            const selectRestock = document.getElementById("restockNameSelect");
            
            tbody.innerHTML = "";
            selectRestock.innerHTML = "";

            let alertaContador = 0;

            products.forEach(item => {
                const eoq = calculateEOQ(item.d, item.s, item.h);
                const rop = calculateROP(item.d);
                const bajoMinimo = item.stock <= rop;
                if (bajoMinimo) alertaContador++;

                const tr = document.createElement("tr");
                tr.className = `border-b border-gray-700 text-xs ${bajoMinimo ? 'bg-red-950/10' : ''}`;
                
                tr.innerHTML = `
                    <td class="py-3 px-4 font-bold text-white">${item.nombre}</td>
                    <td class="py-3 px-4 text-gray-400">${item.cat}</td>
                    <td class="py-3 px-4 text-center font-bold ${bajoMinimo ? 'text-red-400' : 'text-gray-200'}">${item.stock} pzas</td>
                    <td class="py-3 px-4 text-center text-indigo-400 font-bold">${eoq}</td>
                    <td class="py-3 px-4 text-center text-amber-500 font-bold">${rop}</td>
                    <td class="py-3 px-4 text-center">
                        <span class="px-2 py-0.5 rounded text-2xs font-extrabold ${bajoMinimo ? 'bg-red-900/40 text-red-300 border border-red-700' : 'bg-green-900/30 text-green-400 border border-green-700'}">
                            ${bajoMinimo ? '🚨 ABASTECER ROP' : '✅ STOCK CORRECTO'}
                        </span>
                    </td>
                `;
                tbody.appendChild(tr);

                // Llenar selector del formulario usando únicamente el nombre del componente
                const opt = document.createElement("option");
                opt.value = item.id;
                opt.textContent = item.nombre;
                selectRestock.appendChild(opt);
            });

            document.getElementById("kpiItems").textContent = products.length;
            document.getElementById("kpiAlerts").textContent = alertaContador;
            renderLogs();
        }

        function renderLogs() {
            const logContainer = document.getElementById("logContainer");
            logContainer.innerHTML = "";
            logs.forEach(l => {
                const d = document.createElement("div");
                d.className = "text-2xs bg-gray-750 p-2 rounded border border-gray-700 text-gray-300 font-mono";
                d.innerHTML = `<span class="text-gray-500">${l.time}</span> | ${l.text}`;
                logContainer.appendChild(d);
            });
        }

        function resetToDefaultData() {
            if (confirm("¿Limpiar la simulación actual y regresar al inventario original?")) {
                localStorage.removeItem('st_products');
                localStorage.removeItem('st_logs');
                products = [...defaultProducts];
                logs = [{ time: "2026-05-19 20:00", text: "Inventario reiniciado correctamente por Raúl Villegas." }];
                cart = [];
                renderStoreGrid();
                if(!document.getElementById("viewAdmin").classList.contains("hidden")) renderAdminTable();
            }
        }

        // Carga inicial
        window.onload = () => {
            renderStoreGrid();
            renderCartItems();
        };
    </script>
</body>
</html>
