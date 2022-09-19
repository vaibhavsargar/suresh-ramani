<template>
    <div class="row" >
        <div >
            <div class="text-center"  v-if="loading">
                <b-spinner style="width: 3rem; height: 3rem;"></b-spinner>
            </div>
            <b-alert show>Default Alert</b-alert>
            <b-alert variant="success" show>Success Alert</b-alert>

            <b-alert v-model="showDismissibleAlert" variant="danger" dismissible>
                Dismissible Alert!
            </b-alert>
            <b-alert :show="dismissCountDown" dismissible variant="warning" @dismissed="dismissCountDown=0"
                @dismiss-count-down="countDownChanged">
                <p>This alert will dismiss after {{ dismissCountDown }} seconds...</p>
                <b-progress variant="warning" :max="dismissSecs" :value="dismissCountDown" height="4px"></b-progress>
            </b-alert>
            <b-button @click="showAlert" variant="info" class="m-1">
                Show alert with count-down timer
            </b-button>
            <b-button @click="showDismissibleAlert=true" variant="info" class="m-1">
                Show dismissible alert ({{ showDismissibleAlert ? 'visible' : 'hidden' }})
            </b-button>
        </div>
        <div class="col-12 mb-2 text-end">
            <router-link :to='{name:"categoryAdd"}' class="btn btn-primary">Create</router-link>
        </div>
        <div class="col-12">

            <div class="card">
                <div class="card-header">
                    <h4>Category</h4>
                </div>
                <div class="card-body">
                    <div class="table-responsive">
                        <table class="table table-bordered">
                            <thead>
                                <tr>
                                    <th>ID</th>
                                    <th>Title</th>
                                    <th>Description</th>
                                    <th>Actions</th>
                                </tr>
                            </thead>
                            <tbody v-if="categories.length > 0">
                                <tr v-for="(category,key) in categories" :key="key">
                                    <td>{{ category.id }}</td>
                                    <td>{{ category.title }}</td>
                                    <td>{{ category.description }}</td>
                                    <td>
                                        <router-link :to='{name:"categoryEdit",params:{id:category.id}}'
                                            class="btn btn-success">Edit</router-link>
                                        <button type="button" @click="deleteCategory(category.id)"
                                            class="btn btn-danger">Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                            <tbody v-else>
                                <tr>
                                    <td colspan="4" align="center">No Categories Found.</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "categories",
    data() {
        return {
            categories: [],
            dismissSecs: 10,
            dismissCountDown: 0,
            showDismissibleAlert: false,
            loading: true
        }
    },
    mounted() {
        this.getCategories()
    },
    methods: {
        countDownChanged(dismissCountDown) {
        this.dismissCountDown = dismissCountDown
      },
      showAlert() {
        this.dismissCountDown = this.dismissSecs
      },
        async getCategories() {
            await this.axios.get('/api/category').then(response => {
                this.categories = response.data,
                this.loading = false

            }).catch(error => {
                console.log(error)
                this.categories = []
            })
        },
        deleteCategory(id) {
            this.$confirm("Are you sure?").then(() => {
                this.axios.delete(`/api/category/${id}`).then(response => {
                    console.log(response.data);
                    this.getCategories()
                    this.$fire({
                        title: "Deleted",
                        text: response.data.message,
                        type: "success",
                        timer: 3000
                    }).then(r => {
                        console.log(r.value);
                    });
                }).catch(error => {
                    console.log(error)
                })
            });
        }
    }
}
</script>