<template>
    <div>
        <nav class="navbar navbar-light bg-ght">
            <a href="/" class="navbar-brand">MEVN Stack</a>
        </nav>

        <div class="container">
            <div class="row pt-5">
                <div class="col-md-5">
                    <div class="card">
                        <div class="card-body">
                            <form @submit.prevent="sendTask">
                                <div class="form-group">
                                    <input type="text" v-model="task.title" placeholder="Insert a Task">
                                </div>

                                <div class="form-group">
                                    <textarea class="form-control" cols="30" rows="10" v-model="task.description" placeholder="Insert a Description"></textarea>
                                </div>

                                <template v-if="edit === false">
                                    <button class="btn btn-primary btn-block">Send</button>
                                </template>

                                <template v-else>
                                    <button class="btn btn-primary btn-block">Update</button>
                                </template>

                            </form>
                        </div>
                    </div>
                </div>

                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Task</th>
                                <th>Description</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task of tasks">
                                <td>{{task.title}}</td>
                                <td>{{task.description}}</td>
                                <!-- <td>{{task._id}}</td> -->
                                <td>
                                    <button @click="deleteTask(task._id)" class="btn btn-danger">Delete</button>
                                    <button @click="editTask(task._id)" class="btn btn-secondary">Update</button>
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

class Task{
    constructor(title, description){
        this.title = title;
        this.description = description;
    }
}

export default {
    data() { 
        return {
            task: new Task(),
            tasks: [],
            edit: false,
            taskToEdit: ''
        }
    },
    created(){
        this.getTasks();
    },
    methods: {
        sendTask() {
            if(this.edit === false) {
                fetch('/api/tasks', {
                    method: "POST",
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTasks();
                })
                this.task = new Task();
            } else {
                fetch('/api/tasks/' + this.taskToEdit, {
                    method: 'PUT',
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTasks();
                    this.edit = false;
                })
                this.task = new Task();
            }
        },
        getTasks() {
            fetch('/api/tasks')
            .then(res => res.json())
            .then(data => {
                this.tasks = data;
                console.log(this.tasks);
            });
        },
        deleteTask(ide) {
            fetch('/api/tasks/' + ide, {
                method: 'DELETE',
                headers: {
                    'Accept': 'application/json',
                    'Content-type': 'application/json'
                }
            })
            .then(res => res.json())
            .then(data => {
                this.getTasks();
            });
         },
        //  editTask(ide) {
        //      fetch('/api/tasks/' + ide)
        //      .then(res => res.json())
        //      .then(data => {
        //          this.task = new Task(data.title, data.description);
        //          this.taskToEdit = data._ide;
        //          this.edit = true;
        //      });
        //  }
         editTask(taskId) {
           fetch('/api/tasks/' + taskId)
           .then(res => res.json())
           .then(data => {
               const { _id, title, description } = data;
               this.task = new Task(title, description);
               this.taskToEdit = _id;
               this.edit = true;
           });
        }
    }
}
</script>