<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List Tugas</h3>
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            <v-data-table :headers="headers" :items="todos" :search="search" :expanded.sync="expanded" item-key="note" show-expand  class="elevation-1" >
                
                <template v-slot:[`item.selected`]="{ item }">
                        <input v-model="selected" :value="item.task"
                         type="checkbox" enabled>
                </template>
                   
                <template v-slot:top>
                    <v-toolbar flat>
                            <v-spacer></v-spacer>
                            <v-switch
                                v-model="singleExpand"
                                label="Single expand"
                                class="mt-2"
                            ></v-switch>
                    </v-toolbar>
                </template>
                <template v-slot:expanded-item="{headers, item}">
                    <td align="start" :colspan="headers.length">
                        <h5> Note</h5>
                        <br> {{ item.note }} <br>
                    </td>
                </template>
                 
                 <template v-slot:[`item.priority`]="{ item }">
                        <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Tidak penting'" color="green" outlined>
                            {{ item.priority }}
                        </v-chip>
                        <v-chip v-else-if="item.priority == 'Biasa'" color="blue" outlined>
                            {{ item.priority }}
                        </v-chip>
                        
                 </template>
                 
                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="mr-2" @click="editItem(item)">
                        edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                        delete
                    </v-btn>
                </template>
            </v-data-table>
        </v-card>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-card-text>
                <div align="start"  v-if="selected.length > 0">
                    <br><br>
                    <h2>DELETE MULTIPLE : </h2>
                    <ul>
                        <li v-for="(item, index)
                         in selected" :key="index">
                            {{item}}
                        </li>
                    </ul>
                    <br>
                    <v-btn color="error" @click="deleteList">Delete</v-btn>
                </div>
        </v-card-text>
        <v-dialog v-model="dialogHapus" max-width="500px">
          <v-card>
            <v-card-title class="headline">Yakin ingin menghapus?</v-card-title>
            <v-card-actions>
              <v-btn color="error" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="green" text @click="deleteConfirm">OK</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
    </v-main>
</template>
<script>
export default {
        name: "List",
        data() {
            return {
                deleteList: true,
                search: null,
                dialog: false,
              dialogHapus : false,
                expanded: [],
                selected: [],
                singleExpand: false,
              editedIndex: -1,
                headers: [
                    {   
                        
                        text: "",
                        align: "start",
                        sortable: true,
                        value: 'data-table-select"',
                    },
                    { text: "Task" , value: 'task'},
                    { text: "Priority", value: "priority" },
                    
                    { text: "Actions", value: "actions" },
                    { text: "Choose", value: "selected" },
                ],
                todos: [
                    {
                        note: "huffttt",
                        task: "bernafas",
                        priority: "Penting",
                        
                       
                    },
                    {
                        note: "bersama tman2",
                        task: "nongkrong",
                        priority: "Tidak penting",
                        
                        
                    },
                    {
                        note: "masak air 500ml",
                        task: "masak",
                        priority: "Biasa",
                        
                        
                    },
                ],
                formTodo: {
                    task: null,
                    priority: null,
                    note: null,
                },
            };
        },
        methods: {
            save(){
                if (this.editedIndex > -1) {
                        Object.assign(this.todos[this.editedIndex], this.formTodo)
                        
                } else {
                        this.todos.push(this.formTodo)
                
                }
                this.resetForm();
                this.dialog = false;
                
            },
            cancel() {
                this.resetForm();
                this.dialog = false;
            },
            resetForm() {
                this.formTodo = {
                    task: null,
                    priority: null,
                    note: null,
                };
            },
            editItem (item) {
this.dialog = true;
                this.editedIndex = this.todos.indexOf(item);
                this.formTodo = Object.assign({}, item);
        
                },
            deleteItem (item) {
                this.editedIndex = this.todos.indexOf(item);
                this.formTodo= Object.assign({}, item);
                this.dialogHapus = true;
            },
            
            deleteConfirm () {
                this.todos.splice(this.editedIndex, 1)
                this.dialogHapus = false
                 this.resetForm();
            },
            closeDelete(){
                 this.dialogHapus = false
            },
            deleteList(){
                this.editedIndex = this.todos.indexOf(item);
                this.formTodo= Object.assign({}, item);
            },
            
            
        },
    };
</script>
