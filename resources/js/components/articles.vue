<template>
    <div>
        <h2>My Articles</h2>
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

        <div class="card card-body mb-2" v-for="(item, index) in articles" :key="index.id" :item="item">
            <h3>{{item.title}}</h3>
            <p>{{item.body}}</p>
        </div>
    </div>
</template>

<script>
    export default {
        name: "articles",
        data: function () {
            return{
                articles:[],
                article:{
                    id:'',
                    title:',',
                    body:','
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
            }

        }

    }
</script>

<style scoped>

</style>