<template>
    <div class="container-fluid">
        <div class="row mt-5">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h3 class="card-title">All Users</h3>

                        <div class="card-tools">
                            <button type="button" class="btn btn-primary" @click="newModal">
                                <i class="fas fa-user-plus fa-fw"></i>
                                Add User
                            </button>

                        </div>
                    </div>
                    <!-- /.card-header -->
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover">
                            <thead>
                            <tr>
                                <th>ID</th>
                                <th>NAME</th>
                                <th>EMAIL</th>
                                <th>TYPE</th>
                                <th>CREATE AT</th>
                                <th>ACTION</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="(user,index) in users" :key="user.id">
                                <td>{{ index + 1}}</td>
                                <td>{{ user.name}}</td>
                                <td>{{ user.email}}</td>
                                <td>{{ user.type | upText}}</td>
                                <td>{{ user.created_at | myDate }}</td>
                                <td class="text-center">
                                    <button type="button" class="btn btn-primary btn-sm" @click="editUser(user)">
                                        <i class="fa fa-edit"></i>
                                    </button>

                                    <button  type="button" class="btn btn-primary btn-sm" @click="deleteUser(user.id)">
                                        <i class="fa fa-trash"></i>
                                    </button>
                                </td>
                            </tr>
                            </tbody>
                        </table>
                    </div>
                    <!-- /.card-body -->
                </div>
                <!-- /.card -->
            </div>
        </div>

        <div class="modal fade" id="addUser" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
             aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 v-show="editMode" class="modal-title" >Update User Info</h5>
                        <h5 v-show="!editMode" class="modal-title">Add New User</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <form @submit.prevent="editMode ? updateUser() : createUser()">
                        <div class="modal-body">
                            <div class="form-group">
                                <input v-model="form.name" type="text" name="name" placeholder="Enter Name"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                <has-error :form="form" field="name"></has-error>
                            </div>

                            <div class="form-group">
                                <input v-model="form.email" type="email" name="email" placeholder="Enter Email"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                <has-error :form="form" field="email"></has-error>
                            </div>

                            <div class="form-group">
                                <textarea v-model="form.bio" id="bio" name="bio" placeholder="Enter bio for user"
                                          class="form-control"
                                          :class="{ 'is-invalid': form.errors.has('bio') }"> </textarea>
                                <has-error :form="form" field="bio"></has-error>
                            </div>

                            <div class="form-group">
                                <select v-model="form.type" name="type" id="type"
                                        class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="">Select User Role</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">User</option>
                                    <option value="author">Author</option>
                                </select>
                                <has-error :form="form" field="type"></has-error>
                            </div>

                            <div class="form-group">
                                <input v-model="form.password" id="password" type="password" name="password"
                                       placeholder="Enter Password"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                <has-error :form="form" field="password"></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                            <button v-show="editMode" type="submit" class="btn btn-primary">Update</button>
                            <button v-show="!editMode" type="submit" class="btn btn-primary">Create</button>
                        </div>
                    </form>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                editMode: false,
                users: {},
                form: new Form({
                    id:'',
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
                })
            }
        },
        methods: {
            updateUser(id){
                this.$Progress.start();
               this.form.put('api/user/'+this.form.id)
               .then(()=>{
                   $("#addUser").modal('hide');
                   toast.fire({
                       type: 'success',
                       title: 'User Update  in successfully'
                   });
                   Fire.$emit('AfterHttpRequest');
               })
               .catch(()=>{
                   this.$Progress.fail();
                   swal.fire('Failed!', 'There are something Wronge', 'warning');
               })
            },
            newModal(){
                this.editMode = false;
                this.form.reset();
                $('#addUser').modal('show');
            },
            editUser(user){
                this.editMode = true;
                this.form.reset();
                $('#addUser').modal('show');
                this.form.fill(user);
            },
            deleteUser(id){
                swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    if (result.value) {
                        this.form.delete('api/user/' + id).then(() => {
                            Fire.$emit('AfterHttpRequest')
                            toast.fire({
                                type: 'success',
                                title: 'User Delete  in successfully'
                            })
                        }).catch(() => {
                            swal.fire('Failed!', 'There are something Wronge', 'warning');
                        })
                    }
                })

            },
            loadUser() {
                axios.get('api/user').then(({data}) => (this.users = data.data));
            },
            createUser() {
                this.$Progress.start();
                this.form.post('api/user')
                .then(() =>{
                    Fire.$emit('AfterHttpRequest');
                    toast.fire({
                        type: 'success',
                        title: 'User create  in successfully'
                    })
                    $("#addUser").modal('hide');
                    this.$Progress.finish();
                })
                .catch(() =>{

                })

            }
        },
        created() {
           this.loadUser();
           Fire.$on('AfterHttpRequest',() =>{
               this.loadUser();
           })
           //setInterval( () => this.loadUser(),3000);
        }
    }
</script>
