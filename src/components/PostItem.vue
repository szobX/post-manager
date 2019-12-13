<template>
    <div class="postItem">
    <div  @click="removePost(post.id)" class="delete"></div>
    <p class="postItem__title">{{post.title}}</p>
        <h4 class="postItem__name">{{post.name}}</h4>
        <p v-if="!isOpen" class="postItem__body">
            {{loadLess(post.body)}}
        </p>
        <Transition name="slide-fade " >
            <p v-if="isOpen" class="postItem__body">
                {{post.body}}
            </p>
        </Transition>



    <button @click="isOpen = !isOpen" class="postItem__btn">Read more</button>
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
    /* Enter and leave animations can use different */
    /* durations and timing functions.              */
    .slide-fade-enter-active {
        transition: all .3s ease;
    }
    .slide-fade-leave-active {
        transform: translateX(-10px);
        opacity: 0;
        transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
    }
    .slide-fade-enter, .slide-fade-leave-to
        /* .slide-fade-leave-active below version 2.1.8 */ {
        transform: translateX(10px);
        opacity: 0;
    }


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




    .postItem{
        text-align: left;
        transition: .5s ease-in;
        border-radius: 2px;
        margin:20px 0px;
        background: #ffffff;
        padding: 5px 15px;
        border: 1px solid #d6d6d6;
        position: relative;
     /*   box-shadow:
                0 12.3px 10px -95px rgba(0, 0, 0, 0.056),
                0 98px 80px -95px rgba(0, 0, 0, 0.2)
    ;*/

        &__title{
        font-size: 21px;
        font-weight: 500;
            width:calc(100% - 50px);

    }
        &__name{
            color:#000;
            opacity: 0.8;
            padding:20px 0;
            font-style: italic;
            border-bottom: 1px solid #d6d6d6;
        }
        &__body{
            font-size: 14px;

            font-weight: 300;

        }
    &__btn {
        background: transparent;
        border: none;
        font-weight: 600;
        position: relative;
        padding: 15px 10px;
        display: block;
        text-align: center;
        font-weight: 400;
        letter-spacing: 1.1px;
        &:hover {
            &:after {
                width: 100%;
            }
        }

        &:after {
            content: '';
            position: absolute;
            bottom: 8px;
            left: 0;
            transition: width .5s ease-out;
            height: 2px;
            width: 0;
            background: #6899ff;
        }
    }
    }

</style>
