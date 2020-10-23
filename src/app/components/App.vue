<template>
    <div>
        <nav class="navbar navbar-light bg-light">
            <div class="container">
                <a href="/" class="navbar-brand">MEVN Stack</a>
            </div>
        </nav>
        <div class="container">
            <div class="row pt-5">
                <div class="col-md-5">
                    <div class="card">
                        <div class="card-body">
                            <form @submit.prevent="enviarTarea">
                                <div class="form-group">
                                    <input class="form-control" type="text" v-model="tarea.title" placeholder="Inserte una nueva Tarea">
                                </div>
                                <div class="form-group">
                                    <textarea class="form-control" v-model="tarea.description" placeholder="Ingrese una Descripcion" name="" id="" cols="30" rows="10"></textarea>
                                </div>
                                <template v-if="editar === false">
                                    <button class="btn btn-primary btn-block">Enviar Datos</button>
                                </template>
                                <template v-else>
                                    <button class="btn btn-primary btn-block">Actualizar Datos</button>
                                </template>
                            </form>
                        </div>
                    </div>
                </div>
                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Titulo</th>
                                <th>Descripcion</th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="tarea of tareas">
                                <td>{{tarea.title}}</td>
                                <td>{{tarea.description}}</td>
                                <td>
                                    <button class="btn btn-primary" @click="editarTarea(tarea._id)">Editar</button>
                                    <button class="btn btn-danger" @click="eliminarTarea(tarea._id)">Eliminar</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    class Tarea {
        constructor(title, description) {
            this.title = title;
            this.description = description;
        }
    }

    export default {
        data() {
            return {
                tarea: new Tarea(),
                tareas: [],
                editar: false,
                tareaEditar: ""
            }
        },
        created() {
            this.obtenerTareas()
        },
        methods: {
            obtenerTareas() {
                fetch("/api/tareas")
                    .then(res => res.json())
                    .then(data => {
                        this.tareas = data;
                    })
            },
            enviarTarea() {
                if(this.editar === false){
                    fetch("/api/tareas", {
                        method: "POST",
                        body: JSON.stringify(this.tarea),
                        headers: {
                            "Accept": "application/json",
                            "Content-type": "application/json",
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.obtenerTareas()
                    })
                    this.tarea = new Tarea();
                } else {
                    fetch("/api/tareas/" + this.tareaEditar, {
                        method: "PUT",
                        body: JSON.stringify(this.tarea),
                        headers: {
                            "Accept": "application/json",
                            "Content-type": "application/json",
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.obtenerTareas()
                        this.editar = false;
                        this.tareaEditar = "";
                        this.tarea = new Tarea();
                    })
                }
            },
            eliminarTarea(id){
                fetch("/api/tareas/" + id, {
                    method: "DELETE",
                    headers: {
                        "Accept": "application/json",
                        "Content-type": "application/json",
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.obtenerTareas()
                })
            },
            editarTarea(id){
                fetch("/api/tareas/"+id)
                    .then(res => res.json())
                    .then(data => {
                        this.tarea = new Tarea(data.title, data.description);
                        this.tareaEditar = data._id;
                        this.editar = true;
                    })
            }
        }
    }
</script>