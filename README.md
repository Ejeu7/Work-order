<!DOCTYPE html>
<html>
<head>
    <title>Mi Capturador de Datos</title>
    <style>
        body { font-family: Arial; margin: 20px; }
        input { padding: 8px; }
        button { padding: 8px; cursor: pointer; background: #28a745; color: white; border: none; }
    </style>
</head>
<body>

    <h2>Captura de Productos</h2>
    <input type="text" id="entradaProducto" placeholder="Escribe un producto...">
    <button onclick="agregarALista()">Agregar a la Lista</button>

    <h3>Productos Guardados:</h3>
    <ul id="listaVisual"></ul>

    <script>
        // 1. Cargar datos guardados al abrir la página
        window.onload = mostrarProductos;

        function agregarALista() {
            let producto = document.getElementById("entradaProducto").value;
            if (producto === "") return;

            // 2. Obtener lo que ya existe o crear un arreglo vacío
            let productosExistentes = JSON.parse(localStorage.getItem("misProductos") || "[]");
            
            // 3. Guardar el nuevo dato
            productosExistentes.push(producto);
            localStorage.setItem("misProductos", JSON.stringify(productosExistentes));

            document.getElementById("entradaProducto").value = ""; // Limpiar cuadro
            mostrarProductos(); // Actualizar vista
        }

        function mostrarProductos() {
            let lista = document.getElementById("listaVisual");
            let datos = JSON.parse(localStorage.getItem("misProductos") || "[]");
            
            lista.innerHTML = ""; // Limpiar lista visual
            datos.forEach(item => {
                let li = document.createElement("li");
                li.textContent = item;
                lista.appendChild(li);
            });
        }
    </script>
</body>
</html>

