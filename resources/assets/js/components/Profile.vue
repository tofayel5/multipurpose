<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-12 mt-5">
                <div class="card card-widget widget-user">
                    <!-- Add the bg color to the header using any of the bg-* classes -->
                    <div class="widget-user-header text-white" style="background-image:url('./img/user-cover.jpg')">
                        <h3 class="widget-user-username text-right">{{this.form.name}}</h3>
                        <h5 class="widget-user-desc text-right">{{this.form.type}}</h5>
                    </div>
                    <div class="widget-user-image">
                        <img class="img-circle" :src="getUserProfile()" alt="User Avatar">
                    </div>
                    <div class="card-footer">
                        <div class="row">
                            <div class="col-sm-4 border-right">
                                <div class="description-block">
                                    <h5 class="description-header">3,200</h5>
                                    <span class="description-text">SALES</span>
                                </div>
                                <!-- /.description-block -->
                            </div>
                            <!-- /.col -->
                            <div class="col-sm-4 border-right">
                                <div class="description-block">
                                    <h5 class="description-header">13,000</h5>
                                    <span class="description-text">FOLLOWERS</span>
                                </div>
                                <!-- /.description-block -->
                            </div>
                            <!-- /.col -->
                            <div class="col-sm-4">
                                <div class="description-block">
                                    <h5 class="description-header">35</h5>
                                    <span class="description-text">PRODUCTS</span>
                                </div>
                                <!-- /.description-block -->
                            </div>
                            <!-- /.col -->
                        </div>
                        <!-- /.row -->
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header p-2">
                        <ul class="nav nav-pills">
                            <li class="nav-item"><a class="nav-link" href="#activity"
                                                    data-toggle="tab">Activity</a>
                            </li>
                            <li class="nav-item"><a class="nav-link active" href="#settings"
                                                    data-toggle="tab">Settings</a>
                            </li>
                        </ul>
                    </div><!-- /.card-header -->
                    <div class="card-body">
                        <div class="tab-content">
                            <div class="tab-pane" id="activity">
                                <h3 class="text-center">Display User Activity</h3>
                            </div>

                            <div class=" active tab-pane" id="settings">
                                <form class="form-horizontal">
                                    <div class="form-group">
                                        <label for="name" class="col-sm-2 control-label">Name</label>

                                        <div class="col-sm-10">
                                            <input type="text" v-model="form.name" class="form-control" id="name"
                                                   placeholder="Name" :class="{ 'is-invalid': form.errors.has('name') }">
                                            <has-error :form="form" field="name"></has-error>
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label for="email" class="col-sm-2 control-label">Email</label>

                                        <div class="col-sm-10">
                                            <input type="email" v-model="form.email" class="form-control"
                                                   id="email" :class="{ 'is-invalid': form.errors.has('email') }"
                                                   placeholder="Email">
                                            <has-error :form="form" field="email"></has-error>
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label for="inputBio" class="col-sm-2 control-label">User Info</label>

                                        <div class="col-sm-10">
                                            <textarea class="form-control" v-model="form.bio" id="inputBio"
                                                      placeholder="User Bio" ></textarea>

                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label for="inputProfile" class="col-sm-2 control-label">Upload Photo</label>

                                        <div class="col-sm-10">
                                            <!--                                            <input type="file"   class="form-control-file" id="inputProfile"-->
                                            <!--                                                   placeholder="Profile Picture">-->

                                            <input type="file" name="photo" @change="updateProfilePicture"
                                                   class="form-input">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label for="password" class="col-sm-2 control-label">Password</label>

                                        <div class="col-sm-10">
                                            <input type="password" v-model="form.password" class="form-control" id="password"
                                                   placeholder="If you don't want to change password,just leave it"
                                                   :class="{ 'is-invalid': form.errors.has('password') }">
                                            <has-error :form="form" field="password"></has-error>
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <div class="col-sm-offset-2 col-sm-10">
                                            <button type="submit" @click.prevent="updateInfo" class="btn btn-primary">
                                                Update
                                            </button>
                                        </div>
                                    </div>
                                </form>
                            </div>
                            <!-- /.tab-pane -->
                        </div>
                        <!-- /.tab-content -->
                    </div><!-- /.card-body -->
                </div>
                <!-- /.nav-tabs-custom -->
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                form: new Form({
                    id: '',
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
                })
            }
        },
        mounted() {
            console.log('Component mounted.')
        },
        methods: {
            getUserProfile(){
                let getInstantPhoto = (this.form.photo.length > 200) ? this.form.photo : "img/profile/"+this.form.photo ;
                //return "img/profile/"+this.form.photo;
                return getInstantPhoto;
            },
            updateInfo() {
                this.form.put('api/profile/')
                    .then(() => {
                        Fire.$emit('AfterHttpRequest');
                        this.$Progress.start();
                    })
                    .catch(() => {
                        this.$Progress.fail();
                    })
            },
            updateProfilePicture(e) {
                //console.log('file updated.')
                let file = e.target.files[0];
                console.log(file);
                let reader = new FileReader();
                if (file['size']<2111775){
                    reader.onloadend = (file)=> {
                        //console.log('RESULT', reader.result)
                        this.form.photo = reader.result;
                    }
                    reader.readAsDataURL(file);
                } else {
                    swal.fire('Failed!', 'The file size must be less than 2MB', 'warning');
                }
            }
        },
        created() {
            axios.get('api/profile')
                .then(({data}) => (this.form.fill(data)));
        }
    }
</script>
<style>
    .widget-user-header {
        background-position: center center;
        background-size: cover;
        /*height: 250px;*/
    }
</style>
