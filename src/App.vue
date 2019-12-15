<template>
  <div id="app">
      <div v-if="error">{{error}}</div>
    <div v-if="!error" class="wrapper">
        <div v-if="!isLoaded" >LOADING</div>

      <h2 class="appTitle">Post Manager</h2>

        <div v-if="posts.length>0  && isLoaded" class="pagination">

            <button @click="changePage(currentPage-1)" class="pagination__item pagination__item--btn"   :disabled="currentPage === 0"> &lt; Prev </button>
            <div class="items">
                <button @click="changePage(page-1)"  class="pagination__item" :class="{active:currentPage+1 === page}"  v-for="page in pageCounter" :key="page">{{page}}</button>

            </div>
            <button @click="changePage(currentPage+1)" class="pagination__item  pagination__item--btn" :disabled="currentPage === pageCounter -1">  Next &gt; </button>
        </div>

      <div v-if="posts.length > 0 " class="postWrapper">
              <PostItem :loadLess="loadLess"  @remove-post="removePost($event)"  v-for="post in pageContent" :post="post" :key="post.id"  />
      </div>
        <div v-if="posts.length === 0 && isLoaded" class="appTitle">
            No data
        </div>
        <footer>
            <p>Patryk Szober 2019</p>
        </footer>
    </div>

  </div>
</template>

<script>
import axios from 'axios';
import PostItem from "./components/PostItem";
export default {
  name: 'app',
  components: {
      PostItem,
  },
  data(){
    return{
      currentPage:0,
      jump:10,
      isLoaded:false,
      error:'',
      posts:[],
      postsAPI:'https://jsonplaceholder.typicode.com/posts',
      usersAPI:'https://jsonplaceholder.typicode.com/users',
    }
  },
    computed:{
      pageCounter(){
          return Math.ceil(this.posts.length / this.jump)
      },
      pageContent(){

          const start = this.jump * this.currentPage;
          return this.posts.slice(start,(this.jump+start));
      },
    },
    created() {
      this.isLoaded = false
    },
    async mounted() {
        const res = await Promise.all([this.fetchPosts(), this.fetchUsers()]);

        this.posts = ([...res[0].data.map(post=> ({
            ...post,
            ...this.takeDataFromUser(res[1].data.find(({id})=> id===post.userId)),
        }))]);

        this.isLoaded = !this.isLoaded;

    },
    methods:{
      removePost(remId){
          const index = this.posts.findIndex(({id})=> id===remId);
          this.posts.splice(index,1);
      }
      ,
        takeDataFromUser(obj){
          const {name, } = obj;
          return {name};
      },
      async fetchPosts(){
          try{
             return axios.get(this.postsAPI)
          } catch(err){
              this.error = err;
          }
      },
      async  fetchUsers(){
            try{
               return axios.get(this.usersAPI)
            } catch(err){
                this.error = err;
            }
        },
        loadLess(content){
            return`${content.slice(0,40)}...`;
        }
        ,
        changePage(site){
            this.currentPage = site;

            if((this.posts.length/this.jump)-2< this.currentPage) return;
            if(this.currentPage===0) return;

        }
    }

}
</script>


<style lang="scss">

    @import "styles/variables";
    @import "styles/main";
    @import "styles/pagination";




</style>
