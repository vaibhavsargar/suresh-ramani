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
                    <h4>Update Category</h4>
                </div>
                <div class="card-body">
                    <form @submit.prevent="update">
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
                                <button type="submit" class="btn btn-primary">Update</button>
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
    name:"update-category",
    data(){
        return {
            category:{
                title:"",
                description:"",
                _method:"patch"
            },
            errors:{}
        }
    },
    mounted(){
        this.showCategory()
    },
    methods:{
        async showCategory(){
            await this.axios.get(`/api/category/${this.$route.params.id}`).then(response=>{
                const { title, description } = response.data
                this.category.title = title
                this.category.description = description
            }).catch(error=>{
                console.log(error)
            })
        },
        async update(){
            await this.axios.post(`/api/category/${this.$route.params.id}`,this.category).then(response=>{
                console.log(response.data);
                this.$fire({
                    title: "Updated",
                    text: response.data.category,
                    type: "success",
                    timer: 3000
                }).then(r => {
                    console.log(r.value);
                });
                this.$router.push({name:"categoryList"})
            }).catch(error=>{
                if(error.response.status === 422){
                    console.log(error.response.data.errors);
                    this.errors = error.response.data.errors
                }
            })
        }
    }
}
</script>