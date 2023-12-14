<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lista de Tareas</title>
    <style>
        .completed {
            text-decoration: line-through;
            color: gray;
        }

        .eliminar {
            display: none;
            background-color: #ff6961;
            color: white;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
        }

        .eliminar.activo {
            display: inline-block;
            background-color: #cc0000;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div>
    <h2>Lista de Tareas</h2>
    <input type="text" id="nuevaTarea" placeholder="Agregar nueva tarea" oninput="filtrarTareas()">
    <button onclick="agregarTarea()">Agregar</button>
    <ul id="listaTareas"></ul>

    <template id="tareaTemplate">
        <li>
            <span></span>
            <button class="eliminar" onclick="eliminarTarea(this)">x</button>
        </li>
    </template>

    <template id="buscarTemplate">
        <li>
            <span></span>
        </li>
    </template>

    <template id="eliminarTemplate">
        <li>
            <span></span>
            <button class="eliminar" onclick="eliminarTarea(this)">x</button>
        </li>
    </template>

    <template id="completadaTemplate">
        <li class="completed">
            <span></span>
            <button class="eliminar" onclick="eliminarTarea(this)">x</button>
        </li>
    </template>
</div>

<script>
    function agregarTarea() {
        var nuevaTarea = document.getElementById("nuevaTarea").value.trim();
        if (nuevaTarea !== "") {
            var lista = document.getElementById("listaTareas");
            var template = document.getElementById("tareaTemplate");
            var nuevaTareaElemento = document.importNode(template.content, true);

            nuevaTareaElemento.querySelector("span").textContent = nuevaTarea;

            nuevaTareaElemento.querySelector("li").onclick = function () {
                this.classList.toggle("completed");
                actualizarBotonEliminar();
                guardarTareas();
            };

            lista.appendChild(nuevaTareaElemento);

            document.getElementById("nuevaTarea").value = "";
            actualizarBotonEliminar();
            guardarTareas();
        }
    }

    function filtrarTareas() {
        var input, filter, ul, li, i, txtValue;
        input = document.getElementById("nuevaTarea");
        filter = input.value.toUpperCase();
        ul = document.getElementById("listaTareas");
        li = ul.getElementsByTagName("li");

        for (i = 0; i < li.length; i++) {
            txtValue = li[i].querySelector("span").textContent || li[i].querySelector("span").innerText;
            if (txtValue.toUpperCase().startsWith(filter)) {
                li[i].style.display = "";
            } else {
                li[i].style.display = "none";
            }
        }
    }

    function eliminarTarea(botonEliminar) {
        var tarea = botonEliminar.parentNode;
        tarea.remove();
        actualizarBotonEliminar();
        guardarTareas();
    }

    function actualizarBotonEliminar() {
        var lista = document.getElementById("listaTareas");
        var li = lista.getElementsByTagName("li");

        for (var i = 0; i < li.length; i++) {
            var botonEliminar = li[i].querySelector(".eliminar");
            if (li[i].classList.contains("completed")) {
                botonEliminar.classList.add("activo");
            } else {
                botonEliminar.classList.remove("activo");
            }
        }
    }

    function guardarTareas() {
        var lista = document.getElementById("listaTareas");
        var tareas = [];

        for (var i = 0; i < lista.children.length; i++) {
            var tarea = lista.children[i].querySelector("span").textContent;
            var completada = lista.children[i].classList.contains("completed");
            tareas.push({ tarea: tarea, completada: completada });
        }

        localStorage.setItem("tareas", JSON.stringify(tareas));
    }

    function cargarTareas() {
        var lista = document.getElementById("listaTareas");
        var tareas = localStorage.getItem("tareas");

        if (tareas) {
            tareas = JSON.parse(tareas);

            for (var i = 0; i < tareas.length; i++) {
                var template;
                if (tareas[i].completada) {
                    template = document.getElementById("completadaTemplate");
                } else {
                    template = document.getElementById("tareaTemplate");
                }

                var nuevaTareaElemento = document.importNode(template.content, true);
                nuevaTareaElemento.querySelector("span").textContent = tareas[i].tarea;

                nuevaTareaElemento.querySelector("li").onclick = function () {
                    this.classList.toggle("completed");
                    actualizarBotonEliminar();
                    guardarTareas();
                };

                lista.appendChild(nuevaTareaElemento);
            }
        }
    }

    window.onload = function () {
        cargarTareas();
        actualizarBotonEliminar();
    };
</script>

</body>
</html>
