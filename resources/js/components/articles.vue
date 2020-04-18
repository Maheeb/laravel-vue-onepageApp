<template>
    <div>
        <h2>My Articles</h2>
        <form @submit.prevent="addArticle" class="mb-3">
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Title" v-model="article.title">
            </div>
            <div class="form-group">
                <textarea  class="form-control" placeholder="Body" v-model="article.body"> </textarea>
            </div>
            <button type="submit" class="btn btn-light btn-block">Submit</button>
        </form>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li class="page-item" v-bind:class="[{disabled: !pagination.previous_page_url }]">
                    <a  @click="fetchArticles(pagination.previous_page_url)"  class="page-link" href="#">Previous</a></li>

                <li class="page-item disabled"><a class="page-link text-darkk" href="#">
                Page {{ pagination.current_page}} of   {{ pagination.last_page}}

                </a></li>



                <li  class="page-item" v-bind:class="[{disabled: !pagination.next_page_url}]">

                    <a  @click="fetchArticles(pagination.next_page_url)" class="page-link" href="#">Next</a></li>
            </ul>
        </nav>

        <div class="card card-body mb-2" v-for="(item,index) in articles"  v-bind:key="item.id"   v-bind:index="index" v-bind:item="item">
            <h3>{{item.title}}</h3>
            <p>{{item.body}}</p>
            <hr>
            <button @click="editArticle(item)" class="btn btn-warning mb-2">Edit</button>

            <button @click="deleteArticle(item.id)" class="btn btn-danger ">Delete</button>

           <!--Id is: {{item.id}}-->
        </div>
    </div>
</template>

<script>
    // import {AxiosInstance as axios} from "axios";
    import Vue from 'vue'
    import axios from 'axios'
    import VueAxios from 'vue-axios'

    export default {

        name: "articles",

        data: function () {
            return{
                articles:[],
                article:{
                    id:'',
                    title:'',
                    body:''
                },
                article_id: '',
                pagination:{},
                edit: false
            }
        },

        created(){
          this.fetchArticles();

        },
        methods:{
            fetchArticles: function(page_url){
                let vm = this;
                page_url = page_url || '/api/articles';
                fetch(page_url)
                    .then(res => res.json())
                    .then(res => {
                       this.articles = res.data;

                        vm.makePagination(res.meta, res.links);
                    })
                    .catch( err => console.log(err));
            },
            makePagination : function(meta, links){
                let pagination = {
                    current_page : meta.current_page,
                    last_page: meta.last_page,
                    next_page_url: links.next,
                    previous_page_url: links.prev
                }
                this.pagination = pagination
            },

            deleteArticle: function (id) {
                if (confirm('are you sure?')){
                    fetch('/api/articles/'+ id,{
                        method: 'delete'
                    })
                        .then(res => res.json())
                        .then(data =>{
                            alert('Article removed');
                            this.fetchArticles();
                        })
                        .catch(err => console.log(err));
                }
            },
            addArticle: function () {
                if(this.edit === false){
                    fetch('api/articles',{
                       method:'post',
                       body:  JSON.stringify(this.article),
                        headers:{
                           'content-type' : 'application/json'
                        }
                    })
                        .then(res=> res.json())
                        .then(data=>{
                            this.article.title = '';
                            this.article.body = '';
                            alert('article added');
                            this.fetchArticles();
                        })
                        .catch(err=> console.log(err))
                }else{

                    fetch('api/articles',{
                        method:'put',
                        body:  JSON.stringify(this.article),
                        headers:{
                            'content-type' : 'application/json'
                        }
                    })
                        .then(res=> res.json())
                        .then(data=>{
                            this.article.title = '';
                            this.article.body = '';
                            alert('article Updated');
                            this.fetchArticles();
                        })
                        .catch(err=> console.log(err))
                }
            },

            editArticle:function (article) {
                this.edit = true;
                this.article.id = article.id;
                this.article.article_id = article.id;
                this.article.title= article.title;
                this.article.body= article.body;
            }


        }

    }
</script>

<style scoped>

</style>