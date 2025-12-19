            
            lista.innerHTML = ""; // Limpiar lista visual
            <!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Producción Pro - WO</title>
    <style>
        :root { --primary: #007bff; --success: #28a745; --danger: #dc3545; --warning: #ffc107; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #f0f2f5; padding: 20px; color: #333; margin: 0; }
        .container { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); max-width: 1200px; margin: auto; }
        h2 { text-align: center; color: var(--primary); margin-top: 0; }
        
        .grid-form { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; }
        .form-group { display: flex; flex-direction: column; }
        .full-width { grid-column: 1 / -1; }
        label { font-weight: 600; margin-bottom: 5px; font-size: 0.85em; color: #555; }
        input, select, textarea { padding: 10px; border: 1px solid #ccc; border-radius: 6px; outline: none; background: #fff; font-family: inherit; }
        input:focus, textarea:focus { border-color: var(--primary); }
        input[readonly] { background-color: #e9ecef; font-weight: bold; color: var(--danger); }
        
        .section-title { grid-column: 1 / -1; margin-top: 20px; border-bottom: 2px solid var(--primary); color: var(--primary); font-size: 1em; padding-bottom: 5px; font-weight: bold; display: flex; justify-content: space-between; align-items: center; }

        .buttons { grid-column: 1 / -1; display: flex; gap: 10px; margin-top: 20px; flex-wrap: wrap; }
        button { padding: 12px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; transition: 0.3s; flex: 1; min-width: 140px; }
        .btn-save { background-color: var(--success); color: white; }
        .btn-update { background-color: var(--warning); color: #333; display: none; }
        .btn-download { background-color: var(--primary); color: white; }
        .btn-clear { background-color: #6c757d; color: white; }
        .btn-manage { background-color: #17a2b8; color: white; padding: 5px 10px; font-size: 0.8em; }

        .search-container { margin: 30px 0 15px 0; }
        .search-input { width: 100%; padding: 12px; border: 2px solid var(--primary); border-radius: 8px; font-size: 1em; outline: none; }
        mark { background-color: #ffeb3b; color: black; padding: 0; }

        .table-container { overflow-x: auto; background: white; border-radius: 8px; border: 1px solid #eee; margin-top: 10px; }
        table { width: 100%; border-collapse: collapse; font-size: 0.85em; min-width: 1000px; }
        th, td { padding: 12px; border-bottom: 1px solid #eee; text-align: left; vertical-align: top; }
        th { background-color: var(--primary); color: white; position: sticky; top: 0; }
        tr:hover { background-color: #f1f7ff; }
        
        .actions { display: flex; gap: 8px; }
        .btn-edit { background: var(--warning); border: none; padding: 6px 10px; border-radius: 4px; cursor: pointer; font-weight: bold; }
        .btn-del { background: var(--danger); color: white; border: none; padding: 6px 10px; border-radius: 4px; cursor: pointer; }
        
        .config-box { background: #f8f9fa; border: 1px dashed var(--primary); padding: 15px; border-radius: 8px; margin-bottom: 20px; display: none; }
    </style>
</head>
<body>

<div class="container">
    <h2>🚀 Registro de Producción y WO</h2>

    <div id="configBox" class="config-box">
        <label>Añadir Nuevo Empleado:</label>
        <div style="display: flex; gap: 10px; margin-top: 5px;">
            <input type="text" id="newVendeurName" style="flex: 1;" placeholder="Nombre completo">
            <button onclick="addVendeur()" class="btn-save" style="flex: 0; min-width: 100px;">+ Añadir</button>
        </div>
    </div>
    
    <form id="dataForm">
        <div class="grid-form">
            <div class="section-title">
                Datos del Proyecto
                <button type="button" class="btn-manage" onclick="toggleConfig()">⚙️ Gestionar Empleados</button>
            </div>
            <div class="form-group">
                <label>WO #:</label>
                <input type="text" id="wo" required>
            </div>
            <div class="form-group">
                <label>Vendeur:</label>
                <select id="vendeur"></select>
            </div>
            <div class="form-group">
                <label>Client:</label>
                <input type="text" id="client" required>
            </div>
            <div class="form-group full-width">
                <label>Materiales (Puedes listar varios):</label>
                <textarea id="material" rows="2"></textarea>
            </div>

            <div class="section-title">Parámetros de Cálculo</div>
            <div class="form-group">
                <label>Pulgadas Lineales:</label>
                <input type="number" id="pulgadas" step="any" oninput="calcularTiempo()" value="0">
            </div>
            <div class="form-group">
                <label>Intervenciones (5 min):</label>
                <input type="number" id="intervenciones" oninput="calcularTiempo()" value="0">
            </div>
            <div class="form-group">
                <label>Lecturas (1.5 min):</label>
                <input type="number" id="lecturas" oninput="calcularTiempo()" value="0">
            </div>
            <div class="form-group">
                <label>T. Corte (Horas):</label>
                <input type="text" id="tiempo_total_display" readonly value="0h 0m">
                <input type="hidden" id="tiempo_total_minutos" value="0">
            </div>
            <div class="form-group">
                <label>Fecha Entrega:</label>
                <input type="date" id="delivery_date">
            </div>

            <div class="section-title">Observaciones</div>
            <div class="form-group full-width">
                <label>Nota:</label>
                <textarea id="nota" rows="2"></textarea>
            </div>

            <div class="buttons">
                <button type="button" id="btnSave" class="btn-save" onclick="saveData()">Guardar Registro</button>
                <button type="button" id="btnUpdate" class="btn-update" onclick="updateData()">Actualizar Cambios</button>
                <button type="button" class="btn-download" onclick="downloadCSV()">Exportar Excel</button>
                <button type="button" class="btn-clear" onclick="clearHistory()">Borrar Historial</button>
            </div>
        </div>
    </form>

    <div class="search-container">
        <input type="text" id="searchInput" class="search-input" onkeyup="renderTable()" placeholder="🔍 Buscar por WO, Cliente, Vendedor o Nota...">
    </div>

    <div class="table-container">
        <table id="dataTable">
            <thead>
                <tr>
                    <th>WO</th>
                    <th>Vendedor</th>
                    <th>Cliente</th>
                    <th>Materiales</th>
                    <th>T. Corte</th>
                    <th>Entrega</th>
                    <th>Notas</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>
</div>

<script>
    let editIndex = null;
    const defaultVendeurs = ["Anna L", "Claudia A", "Silvana P", "Chaza G"];

    window.onload = () => { loadVendeurs(); renderTable(); };

    function toggleConfig() {
        const box = document.getElementById('configBox');
        box.style.display = box.style.display === 'none' ? 'block' : 'none';
    }

    function loadVendeurs() {
        let list = JSON.parse(localStorage.getItem('vendeurs')) || defaultVendeurs;
        const sel = document.getElementById('vendeur');
        sel.innerHTML = list.map(v => `<option value="${v}">${v}</option>`).join('');
    }

    function addVendeur() {
        const name = document.getElementById('newVendeurName').value.trim();
        if(!name) return;
        let list = JSON.parse(localStorage.getItem('vendeurs')) || defaultVendeurs;
        if(!list.includes(name)) {
            list.push(name);
            localStorage.setItem('vendeurs', JSON.stringify(list));
            loadVendeurs();
            document.getElementById('newVendeurName').value = "";
        }
    }

    function formatTime(totalMinutes) {
        const minutes = parseFloat(totalMinutes);
        if (isNaN(minutes) || minutes === 0) return "0h 0m";
        const h = Math.floor(minutes / 60);
        const m = Math.round(minutes % 60);
        return `${h}h ${m}m`;
    }

    function calcularTiempo() {
        const p = parseFloat(document.getElementById('pulgadas').value) || 0;
        const i = parseInt(document.getElementById('intervenciones').value) || 0;
        const l = parseInt(document.getElementById('lecturas').value) || 0;
        const totalMinutos = (p / 39.37) + (i * 5) + (l * 1.5);
        document.getElementById('tiempo_total_minutos').value = totalMinutos.toFixed(2);
        document.getElementById('tiempo_total_display').value = formatTime(totalMinutos);
    }

    function getFormData() {
        return {
            wo: document.getElementById('wo').value,
            vendeur: document.getElementById('vendeur').value,
            client: document.getElementById('client').value,
            material: document.getElementById('material').value,
            pulgadas: document.getElementById('pulgadas').value,
            intervenciones: document.getElementById('intervenciones').value,
            lecturas: document.getElementById('lecturas').value,
            tiempo_total: document.getElementById('tiempo_total_minutos').value,
            delivery_date: document.getElementById('delivery_date').value,
            nota: document.getElementById('nota').value
        };
    }

    function saveData() {
        let data = getFormData();
        if(!data.wo) return alert("El campo WO es obligatorio");
        let history = JSON.parse(localStorage.getItem('registros')) || [];
        history.push(data);
        localStorage.setItem('registros', JSON.stringify(history));
        resetForm();
        renderTable();
    }

    function resetForm() {
        document.getElementById('dataForm').reset();
        document.getElementById('tiempo_total_minutos').value = "0";
        document.getElementById('tiempo_total_display').value = "0h 0m";
        document.getElementById('btnSave').style.display = 'block';
        document.getElementById('btnUpdate').style.display = 'none';
        editIndex = null;
    }

    // FUNCIÓN CORREGIDA: Ahora carga todas las cantidades al editar
    function editRow(index) {
        let history = JSON.parse(localStorage.getItem('registros')) || [];
        let item = history[index];
        editIndex = index;

        // Cargar datos básicos
        document.getElementById('wo').value = item.wo;
        document.getElementById('vendeur').value = item.vendeur;
        document.getElementById('client').value = item.client;
        document.getElementById('material').value = item.material;
        
        // Cargar Cantidades (Pulgadas, Intervenciones, Lecturas)
        document.getElementById('pulgadas').value = item.pulgadas || 0;
        document.getElementById('intervenciones').value = item.intervenciones || 0;
        document.getElementById('lecturas').value = item.lecturas || 0;
        
        // Cargar Tiempos y Fechas
        document.getElementById('tiempo_total_minutos').value = item.tiempo_total;
        document.getElementById('tiempo_total_display').value = formatTime(item.tiempo_total);
        document.getElementById('delivery_date').value = item.delivery_date;
        document.getElementById('nota').value = item.nota;

        // Cambiar botones
        document.getElementById('btnSave').style.display = 'none';
        document.getElementById('btnUpdate').style.display = 'block';
        
        // Subir al formulario suavemente
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function updateData() {
        let history = JSON.parse(localStorage.getItem('registros')) || [];
        history[editIndex] = getFormData();
        localStorage.setItem('registros', JSON.stringify(history));
        resetForm();
        renderTable();
    }

    function deleteRow(index) {
        if(confirm("¿Eliminar este registro permanentemente?")) {
            let history = JSON.parse(localStorage.getItem('registros')) || [];
            history.splice(index, 1);
            localStorage.setItem('registros', JSON.stringify(history));
            renderTable();
        }
    }

    function renderTable() {
        const query = document.getElementById('searchInput').value.toLowerCase();
        let history = JSON.parse(localStorage.getItem('registros')) || [];
        const tbody = document.querySelector('#dataTable tbody');
        tbody.innerHTML = "";

        history.forEach((row, index) => {
            const rowString = `${row.wo} ${row.client} ${row.vendeur} ${row.nota}`.toLowerCase();
            if(rowString.includes(query)) {
                let tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${highlight(row.wo, query)}</td>
                    <td>${highlight(row.vendeur, query)}</td>
                    <td>${highlight(row.client, query)}</td>
                    <td style="max-width:200px;">${row.material}</td>
                    <td><b>${formatTime(row.tiempo_total)}</b></td>
                    <td>${row.delivery_date}</td>
                    <td style="max-width:200px;">${highlight(row.nota, query)}</td>
                    <td class="actions">
                        <button class="btn-edit" onclick="editRow(${index})">✎</button>
                        <button class="btn-del" onclick="deleteRow(${index})">X</button>
                    </td>
                `;
                tbody.appendChild(tr);
            }
        });
    }

    function highlight(text, query) {
        if(!query) return text;
        const re = new RegExp(`(${query})`, 'gi');
        return text.toString().replace(re, '<mark>$1</mark>');
    }

    function downloadCSV() {
        let history = JSON.parse(localStorage.getItem('registros')) || [];
        let csv = "data:text/csv;charset=utf-8,WO,Vendedor,Cliente,Material,Pulgadas,Intervenciones,Lecturas,Tiempo (Minutos),Tiempo (Formato),Entrega,Nota\n";
        history.forEach(r => {
            csv += `"${r.wo}","${r.vendeur}","${r.client}","${r.material.replace(/\n/g,' ')}",${r.pulgadas},${r.intervenciones},${r.lecturas},${r.tiempo_total},"${formatTime(r.tiempo_total)}","${r.delivery_date}","${r.nota.replace(/\n/g,' ')}"\n`;
        });
        const link = document.createElement("a");
        link.setAttribute("href", encodeURI(csv));
        link.setAttribute("download", "reporte_produccion.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }

    function clearHistory() {
        if(confirm("¿BORRAR TODO EL HISTORIAL?")) {
            localStorage.removeItem('registros');
            renderTable();
        }
    }
</script>


</body>
</html>
