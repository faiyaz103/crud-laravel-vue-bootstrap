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
                
                <button type="submit" class="btn btn-primary">{{ editMode ? 'Update' : 'Create' }}</button>
            </form>
        </div>
    </section>

    <!-- Data List -->
     <section class="bg-info">
        <div class="container p-5">
            <h1 class="text-center">All Posts</h1>
            <ul class="list-group">
                <li v-for="post in posts.data" :key="post.id" class="list-group-item mb-3">
                    <h3>{{ post.title }}</h3>
                    <p>{{ post.content }}</p>
                    <button @click="editPost(post)" type="button" class="btn btn-warning mx-2">Edit</button>
                    <button @click="deletePost(post.id)" type="button" class="btn btn-danger mx-2">Delete</button>
                </li>
            </ul>
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
                content: ''
            },
            editMode: false,
            editId: null
        }
    },
    methods:{
        async fetchPost(url="/api/posts"){
            const {data} = await axios.get(url);
            this.posts=data;
        },
        async savePost(){
            if(this.editMode){
                await axios.put(`/api/posts/${this.editId}`, this.form);
                this.editMode=false;
            }
            else{
                await axios.post('/api/posts', this.form);
            }
            
            this.fetchPost();
            this.form.title = '';
            this.form.content = '';
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