<template>
    <div class="app" >
        <h1 >Страница с постами</h1>
        <my-input 
            placeholder="Поиск..."
            v-model="searchQuery" />
        <div class="app__btns">
            <my-button
            style="margin: 0"
                @click="showDialog">
                Создать пост
            </my-button>
            <my-select 
                :options="sortOptions"
                v-model="selectedSort"/>
                
        </div>


        <!-- <my-button
            style="margin: 15px 0"
            @click="fenchPosts">
            Получить посты
        </my-button> -->
        
        
        <my-dialog v-model:show="dialogVisible">
            <post-form @create="createPost"/>
        </my-dialog>
        <post-list 
            :posts="sortedAndSearchPosts"
            v-if="!isPostsLoading"
            @remove="removePost"/>

        <div v-else>Идет загрузка...</div>

        <div ref="observer" class="observer"></div>


        <!-- pagination -->
        <!-- <div class="page__wrapper">
            <div 
                class="page"
                :class="{
                    'current-page': page === pageNumber
                }"
                v-for="pageNumber in totalPages" 
                :key="pageNumber"   
                @click="changePage(pageNumber)"                
                >
                    {{pageNumber}}
            </div>
        </div> -->

    </div>
</template>

<script>
import PostList from '@/components/PostList.vue';
import PostForm from '@/components/PostForm.vue';
import axios from 'axios';


export default {
    components: { 
        PostList,
        PostForm
    },
    data() {
        return {
            posts: [
                // {id: 1, title: 'JS', body: 'Описание'},
                // {id: 2, title: 'JS 2', body: 'Описание 2'},
                // {id: 3, title: 'JS 3', body: 'Описание 3'},
            ],
            dialogVisible: false,
            isPostsLoading: false,
            page: 1,
            limit: 10,
            totalPages: 0,
            selectedSort: '',
            searchQuery: '',
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По описанию'},
            ],
            
        }
    },
    methods: {
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter(p => p.id !== post.id);
        },
        showDialog() {
            this.dialogVisible = true
        },
        // <!-- pagination -->
        // changePage(pageNumber) {
        //     this.page = pageNumber;
        // },
        async fenchPosts() {
            try{
                this.isPostsLoading = true;
                const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }});
                this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit)
                this.posts = res.data;                
            }catch(e){
                throw new Error
            } finally {
                this.isPostsLoading = false;
            }
            
        },
        async loadMorePosts() {
            try{
                this.page += 1;
                const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
                    params: {
                        _page: this.page,
                        _limit: this.limit
                    }});
                this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit)
                this.posts = [...this.posts, ...res.data];                
            }catch(e){
                throw new Error
            } 
            
        }
    },
    mounted() {
        this.fenchPosts();

        // Intersection  Observer API
        const options = {
            rootMargin: '0px',
            threshold: 1.0
        }
        const callback = (entries, observer) => {
            if(entries[0].isIntersecting) {
                this.loadMorePosts();
            }
        };
        const observer = new IntersectionObserver(callback, options);
        observer.observe(this.$refs.observer)
    },
    computed: {
        sortedPosts() {
            return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
        },
        sortedAndSearchPosts() {
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        }

    },
    watch: {
        // <!-- pagination -->
        // page() {
        //     this.fenchPosts()
        // }
    }
    // watch: {
    //     selectedSort(newValue) {
    //         this.posts.sort((post1, post2) => {
    //             // return post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
    //             return post1[newValue]?.localeCompare(post2[newValue])
    //         })
    //     }
    // }
}
</script>

<style>

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    .app {
        width: 100%;
        max-width: 1000px;
        margin: 0 auto;
        padding: 20px;
    }
    .app__btns {
        display: flex;
        justify-content: space-between;
        margin: 15px 0;
    }

    .page__wrapper {
        display: flex;
        margin: 0 auto;
        margin-top: 15px;
        justify-content: center;

    }
    .page {
        padding: 10px;
        border: 1px solid black;
        margin: 0 5px;
        cursor: pointer;
    }
    .current-page {
        border: 2px solid teal;
    }
    .observer {
        height: 30px;
        background: green;
    }

    
</style>

