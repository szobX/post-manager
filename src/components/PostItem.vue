<template>
    <div class="postItem">
    <div  @click="removePost(post.id)" class="delete"></div>{{post.id}}
    <h4 class="name">{{post.name}}</h4>
    <p class="title">{{post.title}}</p>
    <p v-if="!isOpen">
        {{loadLess(post.body)}}
    </p>
        <p v-if="isOpen">
                            {{post.body}}
        </p>
    <button @click="isOpen = !isOpen" class="more">Read more</button>
    </div>

</template>

<script>
    export default {
        name: "PostItem",
        data(){
            return{
                isOpen:false,

            }
        },
        props:{
            post:{
                type:Object,
                required:true,
            },
            loadLess:{
                type:Function,
                required:true,
            }

        },
        methods:{
            handleClick(body){
                this.$emit('hover-content',body);
            }
        }
    }
</script>

<style lang="scss" scoped>
    .postItem{
        flex:1;
        border-radius: 10px;
        margin:20px 0px;
        background: aliceblue;
        padding: 5px 15px;
        position: relative;
        box-shadow:
                0 12.5px 10px rgba(0, 0, 0, 0.035),
                0 100px 80px rgba(0, 0, 0, 0.07);
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

</style>
