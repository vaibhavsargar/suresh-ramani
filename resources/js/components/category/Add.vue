<template>
    <div class="row">
        <div class="col-12">
            <div v-if="errors">
                <div v-for="(v, k) in errors" :key="k" class="bg-danger text-white rounded font-bold mb-4 shadow-lg py-2 px-4 pr-0">
                    <p v-for="error in v" :key="error" class="text-sm">
                        {{error}}
                    </p>
                </div>
            </div>
            
            <div class="card">
                <div class="card-header">
                    <h4>Add Category</h4>
                </div>
                <div class="card-body">
                    <form @submit.prevent="create">
                        <div class="row">
                            <div class="col-12 mb-2">
                                <div class="form-group">
                                    <label>Title</label>
                                    <input type="text" class="form-control" v-model="category.title">
                                </div>
                            </div>
                            <div class="col-12 mb-2">
                                <div class="form-group">
                                    <label>Description</label>
                                    <input type="text" class="form-control" v-model="category.description">
                                </div>
                            </div>
                            <div class="col-12">
                                <button type="submit" class="btn btn-primary">Save</button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "add-category",
    data() {
        return {
            category: {
                title: "",
                description: ""
            },
            errors: []
        }
    },
    methods: {
        async create() {
            await this.axios.post('/api/category', this.category).then(response => {
                console.log(response.data);
                this.$fire({
                    title: "Added",
                    text: response.data.category,
                    type: "success",
                    timer: 3000
                }).then(r => {
                    console.log(r.value);
                });
                this.$router.push({ name: "categoryList" })
            }).catch(e => {
                console.log(e);
                if(e.response.status === 422){
                    console.log(e.response.data.errors);
                    this.errors = e.response.data.errors
                }
            })
        }
    }
}
</script>