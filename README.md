<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Dinámico de Inventario y Compras</title>
    <style>
        :root {
            --primary-color: #0056b3;
            --bg-color: #f4f7f6;
            --text-color: #333;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
        }
        h1, h2 {
            color: var(--primary-color);
        }
        .container {
            max-width: 1200px;
            margin: auto;
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .panels {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
        }
        .panel {
            flex: 1;
            padding: 15px;
            background: #e9ecef;
            border-radius: 8px;
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            margin: 5px 0 15px 0;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
        }
        button:hover {
            background-color: #004494;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background-color: var(--primary-color);
            color: white;
        }
        .alert {
            color: #dc3545;
            font-weight: bold;
        }
        .ok {
            color: #28a745;
            font-weight: bold;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Panel de Control de Inventario</h1>
    
    <div class="panels">
        <div class="panel">
            <h2>Agregar Componente</h2>
            <input type="text" id="newId" placeholder="ID (ej. COMP-008)" required>
            <input type="text" id="newName" placeholder="Nombre del Componente" required>
            <input type="number" id="newStock" placeholder="Stock Inicial" required>
            <input type="number" id="newROP" placeholder="Punto de Reorden (ROP)" required>
            <button onclick="addComponent()">Registrar en Inventario</button>
        </div>

        <div class="panel">
            <h2>Sistema de Compras / Movimientos</h2>
            <select id="moveId">
                </select>
            <select id="moveType">
                <option value="compra">Entrada (Compra de Lote)</option>
                <option value="salida">Salida (Consumo/Uso)</option>
            </select>
            <input type="number" id="moveQty" placeholder="Cantidad" required>
            <button onclick="processMovement()">Procesar Movimiento</button>
        </div>
    </div>

    <h2>Inventario Maestro</h2>
    <table id="inventoryTable">
        <thead>
            <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Stock Actual</th>
                <th>Punto Reorden (ROP)</th>
                <th>Estado</th>
            </tr>
        </thead>
        <tbody>
            </tbody>
    </table>
</div>

<script>
    // Base de datos inicial simulada
    let inventory = [
        { id: 'COMP-001', name: 'RAM DDR4 8GB Kingston Mod-45', stock: 10, rop: 15 },
        { id: 'COMP-002', name: 'Teclado Mecánico Logitech Mod-85', stock: 3, rop: 5 },
        { id: 'COMP-003', name: 'Cable de Corriente Trébol 1.8m', stock: 47, rop: 20 },
        { id: 'COMP-005', name: 'Disco Duro Enterprise 4TB', stock: 11, rop: 10 }
    ];

    function renderTable() {
        const tbody = document.querySelector('#inventoryTable tbody');
        const selectBox = document.getElementById('moveId');
        
        tbody.innerHTML = '';
        selectBox.innerHTML = '';

        inventory.forEach(item => {
            // Determinar estado basado en ROP
            let status = item.stock <= item.rop 
                ? '<span class="alert">⚠️ Requiere Pedido</span>' 
                : '<span class="ok">✅ Óptimo</span>';

            // Llenar tabla
            tbody.innerHTML += `
                <tr>
                    <td>${item.id}</td>
                    <td>${item.name}</td>
                    <td><strong>${item.stock}</strong></td>
                    <td>${item.rop}</td>
                    <td>${status}</td>
                </tr>
            `;

            // Llenar select del sistema de compras
            selectBox.innerHTML += `<option value="${item.id}">${item.id} - ${item.name}</option>`;
        });
    }

    function addComponent() {
        const id = document.getElementById('newId').value;
        const name = document.getElementById('newName').value;
        const stock = parseInt(document.getElementById('newStock').value);
        const rop = parseInt(document.getElementById('newROP').value);

        if(!id || !name || isNaN(stock) || isNaN(rop)) {
            alert("Por favor, completa todos los campos correctamente.");
            return;
        }

        // Verificar que el ID no exista
        if(inventory.find(i => i.id === id)) {
            alert("Ese ID ya existe en el inventario.");
            return;
        }

        inventory.push({ id, name, stock, rop });
        
        // Limpiar campos
        document.getElementById('newId').value = '';
        document.getElementById('newName').value = '';
        document.getElementById('newStock').value = '';
        document.getElementById('newROP').value = '';

        renderTable();
        alert("Componente agregado exitosamente.");
    }

    function processMovement() {
        const id = document.getElementById('moveId').value;
        const type = document.getElementById('moveType').value;
        const qty = parseInt(document.getElementById('moveQty').value);

        if(isNaN(qty) || qty <= 0) {
            alert("Ingresa una cantidad válida mayor a cero.");
            return;
        }

        let itemIndex = inventory.findIndex(i => i.id === id);
        
        if (type === 'compra') {
            inventory[itemIndex].stock += qty;
            alert(`Compra registrada. Se sumaron ${qty} unidades a ${id}.`);
        } else if (type === 'salida') {
            if (inventory[itemIndex].stock < qty) {
                alert("Stock insuficiente para realizar esta salida.");
                return;
            }
            inventory[itemIndex].stock -= qty;
            alert(`Salida registrada. Se restaron ${qty} unidades a ${id}.`);
        }

        document.getElementById('moveQty').value = '';
        renderTable();
    }

    // Inicializar la tabla al cargar la página
    window.onload = renderTable;
</script>

</body>
</html>
