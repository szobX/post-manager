<template>
  <div id="app">
    <div class="wrapper">
        <div v-if="!isLoaded" >LOADED</div>
        <div class="pagination"></div>
      <h2 class="appTitle">Post Manager</h2>
        <div class="pagination">
        <button @click="prevPage()" :disabled="currentPage === 0">PREV</button>
            <div class="items">
                <div class="pagination__item" :class="{active:currentPage+1 === n}"  v-for="n in parseInt(posts.length / jump)" :key="n">{{n}}</div>
            </div>
        <button @click="nextPage()"  >NEXT</button>
    </div>
      <div class="postWrapper">
          <PostItem :loadLess="loadLess"  v-for="post in pageContent" :post="post" :key="post.id"  />
      </div>

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

  @import url('https://fonts.googleapis.com/css?family=Open+Sans:300,400,700&display=swap');
  body{
    margin:0;
    padding:0;
    width:100%;
    background: #c9dfff;
    color:#000000;
    font-family: 'Open Sans',sans-serif;
  }
  *{
    box-sizing: border-box;
  }
  .appTitle{
    opacity: .8;
  }
  .title{
      font-size: 15px;
      font-weight: bold;
      font-style: italic;
      opacity: 0.6;
  }
  .name{
  }
.wrapper{
    width:100%;
    margin: 0 auto;
  text-align: center;
    display: flex;
    flex-direction: column;
    padding:10px 0;
}

  .postWrapper{
      width:100%;
      padding: 3%;
      text-align: left;
        display: flex;
        flex-direction: column;

      &__item{
          flex:1;
          border-radius: 10px;
          margin:20px 0px;
          background: aliceblue;
          padding: 5px 15px;
          position: relative;
          box-shadow:
                  0 12.5px 10px rgba(0, 0, 0, 0.035),
                  0 100px 80px rgba(0, 0, 0, 0.07)
      ;

          .delete{
              position: absolute;
              top:15px;
              right:15px;
              width: 30px;
              height: 30px;

              &:after,&:before{
                  content:'';
                  height:2px;
                  width:80%;
                  position: absolute;
                  top:50%;
                  background: darkred;
              }
              &:after{
                  transform: rotate(-45deg);
              }
              &:before{
                  transform: rotate(45deg);
              }
          }

      }
  }
    .more{
        background: transparent;
        border:none;
        font-weight: 600;
        position: relative;
        padding:15px 10px;
        display: block;
        text-align: center;
        &:hover{
            &:after{
                width:100%;
            }
        }
        &:after{
            content:'';
            position:absolute;
            bottom:8px;
            left:0;
            transition: width .5s ease-out;
            height:2px;
            width:0;
            background: #6899ff;
        }
    }

  .pagination{
      width: 100%;
      justify-content: center;
      display: flex;

      &__item{
    padding:2px 3px;
          &.active{
              background:#6899ff;
              border-radius: 5px;
              color:#fff;
              transition: .4s linear;
          }
      }
    .items{
        background: aliceblue;
        border-radius: 5px;
        padding: 5px 10px;
        margin:0 10px;
        display: flex;
    }
  }
</style>
