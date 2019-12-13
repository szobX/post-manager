<template>
  <div id="app">
    <div class="wrapper">
        <div v-if="!isLoaded" >LOADED</div>

      <h2 class="appTitle">Post Manager</h2>
        <div class="pagination">

        <button @click="prevPage()" class="pagination__item pagination__item--btn"   :disabled="currentPage === 0"> &lt; Prev </button>
            <div class="items">
                <button @click="currentPage = n-1"  class="pagination__item" :class="{active:currentPage+1 === n}"  v-for="n in parseInt(posts.length / jump)" :key="n">{{n}}</button>

            </div>
        <button @click="nextPage()" class="pagination__item  pagination__item--btn">  Next &gt; </button>
    </div>
      <div class="postWrapper">
          <PostItem :loadLess="loadLess"  @remove-post="removePost($event)"  v-for="post in pageContent" :post="post" :key="post.id"  />
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
      pageContent(){
          const start = this.jump * this.currentPage;
          return this.posts.slice(start,(this.jump+start));
      }
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
              /*  this.posts = ([...resPosts.data.map(post=> ({
                    ...post,
                    ...resUsers.data.find(({id})=> id===post.userId)

                }))]);*/
            } catch(err){
                this.error = err;
            }
        },
        loadLess(content){
            return`${content.slice(0,40)}...`;
        },
        nextPage(){
          if((this.posts.length/this.jump)-2< this.currentPage) return;
            this.currentPage++;
        },
        prevPage(){
            if(this.currentPage ===0){
                return ;
            }
            this.currentPage--;
        }
    }

}
</script>

<style lang="scss">

    @import url('https://fonts.googleapis.com/css?family=Montserrat:300,400,600&display=swap');

  body{
    margin:0;
    padding:0;
    width:100%;
    background:$primary; /*#EDF2F7;*/
    color:#000000;
    font-family: 'Montserrat',sans-serif;
  }
  *{
    box-sizing: border-box;
  }
  .appTitle{
    opacity: .8;
  }
.wrapper{
    width:100%;
    max-width: 1440px;
    /*max-width: 1140px;*/
    margin: 0 auto;
  text-align: center;
    display: flex;
    flex-direction: column;
    padding:10px 0;
    @media(min-width:1024px){

    }
}

  .postWrapper{
      width:100%;

      padding: 2%;
      text-align: left;
        display: flex;
        flex-direction: column;


    }

  .pagination{
      justify-content: center;
      display: flex;
      flex-direction: column;
      border-radius: 5px;
      padding: 10px 20px;
      /*flex-wrap: wrap;*/
      align-self: center;
      background: #ffffff;
      @media(min-width: 450px){
          flex-direction: row;
      }
      .items{
          display:flex;
      }
      &__item{
          border:none;
          background: transparent;
          display: flex;
        justify-content: center;
        align-items: center;
          padding: 7px 8px;
          &--btn{


              @media(max-width: 450px){
                    &:first-child{
                        border-bottom: 1px solid #d6d6d6;
                    }
                  &:last-child{
                      border-top: 1px solid #d6d6d6;
                  }

              }
           font-weight: 700;
          }

          &.active{
              background: #6899ff;
              border-radius: 20%;
              color:#fff;
              transition: .4s linear;
          }
      }

  }
</style>
