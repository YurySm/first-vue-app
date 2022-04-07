<template>
  <form @submit.prevent>
        <h4 class=form-title>Создание поста</h4>
        <my-input 
            v-model="post.title"
            placeholder="Название"/>

        <my-input 
            v-model="post.body" 
            placeholder="Описание" />

        <my-button 
            @click="createPost">Создать пост</my-button>
    </form>
</template>

<script>
export default {
    data() {
        return {
            post: {
                title: '',
                body: '',
            }
        }
    },
    methods: {
        createPost() {
            this.post.id = Date.now();
            this.$emit('create', this.post);
            this.post = {
                title: '',
                body: ''
            }
        }
    },
    // watch: {
    //     post: {
    //         handler(newValue){
    //             // console.log(newValue)
    //         },
    //         deep:true
    //     }
    // }
}
</script>

<style scoped>
    .form-title {
        text-align: center;
    }
</style>