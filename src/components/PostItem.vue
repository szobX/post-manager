<template>

    <div class="postItem">
    <div  @click="removePost(post.id)" class="delete"></div>
    <p class="postItem__title">{{post.title}}</p>
        <h4 class="postItem__name">{{post.name}}</h4>
        <p v-if="!isOpen" class="postItem__body">
            {{loadLess(post.body)}}
        </p>

            <p v-if="isOpen" class="postItem__body">
                {{post.body}}
            </p>


    <button @click="isOpen = !isOpen" class="postItem__btn"> Read {{isOpen ? "less " : "more "}}</button>
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
            removePost(id){
                this.$emit('remove-post',id);
            }
        }
    }
</script>




<style lang="scss" scoped>


    @import "../styles/post-item";




</style>
