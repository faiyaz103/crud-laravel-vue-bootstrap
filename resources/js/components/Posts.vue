<template>
    <!-- Form to create post -->
    <section class="bg-dark text-light">
        <div class="container p-5">
            <h1 class="text-center">{{ editMode ? 'Update' : 'Create' }} Post</h1>
            <form @submit.prevent="savePost">
                <div class="mb-3">
                    <input v-model="form.title" type="text" name="title" id="title" class="form-control" placeholder="Title">
                </div>
                <div class="mb-3">
                    <textarea v-model="form.content" name="content" id="content" class="form-control" placeholder="Content"></textarea>
                </div>
                <label for="image" class="form-label">Image</label>
                <input @change="handleFileUpload" ref="imagefile" class="form-control" type="file" id="formFile">

                <label for="pdf" class="form-label">PDF</label>
                <input @change="handlePdfUpload" ref="pdffile" class="form-control" type="file" id="formPdf">

                <button type="submit" class="btn btn-primary mt-3">{{ editMode ? 'Update' : 'Create' }}</button>
            </form>
        </div>
    </section>

    <!-- Data List -->
     <section class="bg-info">
        <div class="container p-5">
            <h1 class="text-center">All Posts</h1>
            <ul class="list-group">
                <li v-for="post in posts.data" :key="post.id" class="list-group-item mb-3">
                    <div class="row">
                        <div class="col-2">
                            <img v-if="post.image" :src="'/storage/' + post.image" class="img-fluid rounded float-start me-3" :style="{ width: '100px', height: 'auto' }" alt="img">
                        </div>
                        <div class="col-10">
                            <h3>{{ post.title }}</h3>
                            <p>{{ post.content }}</p>
                            <a v-if="post.pdf" :href="'/storage/' + post.pdf" target="_blank" class="btn btn-sm btn-outline-secondary mt-2">View PDF</a>

                            <button @click="editPost(post)" type="button" class="btn btn-warning mx-2">Edit</button>
                            <button @click="deletePost(post.id)" type="button" class="btn btn-danger mx-2">Delete</button>
                        </div>
                    </div>
                    
                </li>
            </ul>
        </div>
     </section>

    <section class="bg-secondary p-2">
        <div v-if="posts.links" class="d-flex justify-content-center align-items-center gap-2">
        <button
            v-for="(link, index) in posts.links"
            :key="index"
            @click="fetchPost(link.url)"
            :disabled="!link.url"
            class="btn"
            :class="[
            link.active ? 'btn-primary' : (!link.url ? 'btn-secondary disabled' : 'btn-light')
            ]"
            v-html="link.label"
        >
        </button>
    </div>
    </section>

</template>

<script>
import axios from 'axios';


export default{
    data(){
        return{
            posts: {},
            form: {
                title: '',
                content: '',
                image: null,
                pdf: null
            },
            editMode: false,
            editId: null
        }
    },
    methods:{
        handleFileUpload(event){
            this.form.image = event.target.files[0];
        },

        handlePdfUpload(event) {
            this.form.pdf = event.target.files[0];
        },

        async fetchPost(url="/api/posts"){
            const {data} = await axios.get(url);
            this.posts=data;
        },

        async savePost(){

            const formData = new FormData();
            formData.append('title', this.form.title);
            formData.append('content', this.form.content);
            if(this.form.image){
                formData.append('image', this.form.image);
            }
            if (this.form.pdf) {
                formData.append('pdf', this.form.pdf);
            }


            if(this.editMode){           
                formData.append('_method', 'PUT');     
                await axios.post(`/api/posts/${this.editId}`, formData, 
                    {
                        headers:{
                            "Content-Type": "multipart/form-data"
                        }
                    }
                );
                this.editMode=false;
            }
            else{
                await axios.post('/api/posts', formData, 
                    {
                        headers:{
                            "Content-Type": "multipart/form-data"
                        }
                    }
                );
            }
            
            this.form.title = '';
            this.form.content = '';
            this.form.image = null;
            this.$refs.imagefile.value=null;
            this.form.pdf = null;
            this.$refs.pdffile.value = null;

            this.fetchPost();
            
        },

        editPost(post){
            this.form.title=post.title;
            this.form.content=post.content;
            this.editId=post.id;

            this.editMode=true;
        },

        async deletePost(id){
            await axios.delete(`/api/posts/${id}`);
            this.fetchPost();
        }
    },
    mounted(){
        this.fetchPost();
    }
}

</script>