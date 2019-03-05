<template>
  <div class="container">
      <div>
        <!-- <ul>
          <li v-for="p in paginatedData">
            {{p.nombre}} 
            {{p.fechaHora}}  
            {{p.comentario}}
          </li>
        </ul> -->
        <div class="row justify-content-center">
        <div class="col-md-8">
          <h2 class="page-header">Comentarios</h2>
            <section class="comment-list">
              <article class="row" v-for="post of paginatedData">
                <div>
                  <div class="col-md-2 col-sm-2 col-lg-2 hidden-xs">
                    <figure class="thumbnail">
                      <img class="img-responsive" src="http://www.tangoflooring.ca/wp-content/uploads/2015/07/user-avatar-placeholder.png" />
                      <figcaption class="text-center">{{post.nombre}}</figcaption>
                    </figure>
                  </div>
                  <div class="col-md-10 col-sm-10 col-lg-10">
                    <div class="panel panel-default arrow left">
                      <div class="panel-body">
                        <header class="text-left">
                          <div class="comment-user"><i class="fa fa-user"></i>{{post.nombre}}</div>
                          <time class="comment-date" datetime="16-12-2014 01:05"><i class="fa fa-clock-o"></i>{{post.fechaHora}}</time>
                        </header>
                        <div class="comment-post">
                          <p class="overflow">{{post.comentario}}</p>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </article>
            </section>
            </div>
        </div>
        <button class="btn btn-outline-success" :disabled="pageNumber === 0" @click="prevPage">
          << 10 Ant.
        </button>
        <button class="btn btn-outline-success" :disabled="pageNumber >= pageCount -1" @click="nextPage">
          10 Sig. >>
        </button>
      </div>
    </div>
</template>

<script>
import axios from 'axios';///importar la dependencia de axios
export default {
  name: 'pagination',
  data(){
    return{
      //paginacion
      pageNumber: 0,  // default to page 0,
    }
  },
  created(){
  },
  ///propiedades para crear la paginacion
  props:{
    listData:{
      type:Array,
      required:true
    },
    size:{
      type:Number,
      required:false,
      default: 10
    }
  },
  ///para paginacion de posts
  computed:{
    pageCount: function(){
      let l = this.listData.length,
          s = this.size;
      return Math.ceil(l/s);
    },
    paginatedData: function(){
    const start = this.pageNumber * this.size,
          end = start + this.size;
     return this.listData.slice(start, end);
    } 
  },
  methods:{
    ///para la paginacion
    nextPage: function(){
        this.pageNumber++;
    },
    prevPage: function(){
      this.pageNumber--;
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/*font Awesome http://fontawesome.io*/
@import url(//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css);
/*Comment List styles*/
.comment-list .row {
  margin-bottom: 0px;
}
.comment-list .panel .panel-heading {
  padding: 4px 15px;
  position: absolute;
  border:none;
  /*Panel-heading border radius*/
  border-top-right-radius:0px;
  top: 1px;
}
.comment-list .panel .panel-heading.right {
  border-right-width: 0px;
  /*Panel-heading border radius*/
  border-top-left-radius:0px;
  right: 16px;
}
.comment-list .panel .panel-heading .panel-body {
  padding-top: 6px;
}
.comment-list figcaption {
  /*For wrapping text in thumbnail*/
  word-wrap: break-word;
}
/* Portrait tablets and medium desktops */
@media (min-width: 768px) {
  .comment-list .arrow:after, .comment-list .arrow:before {
    content: "";
    position: absolute;
    width: 0;
    height: 0;
    border-style: solid;
    border-color: transparent;
  }
  .comment-list .panel.arrow.left:after, .comment-list .panel.arrow.left:before {
    border-left: 0;
  }
  /*****Left Arrow*****/
  /*Outline effect style*/
  .comment-list .panel.arrow.left:before {
    left: 0px;
    top: 30px;
    /*Use boarder color of panel*/
    border-right-color: inherit;
    border-width: 16px;
  }
  /*Background color effect*/
  .comment-list .panel.arrow.left:after {
    left: 1px;
    top: 31px;
    /*Change for different outline color*/
    border-right-color: #FFFFFF;
    border-width: 15px;
  }
  /*****Right Arrow*****/
  /*Outline effect style*/
  .comment-list .panel.arrow.right:before {
    right: -16px;
    top: 30px;
    /*Use boarder color of panel*/
    border-left-color: inherit;
    border-width: 16px;
  }
  /*Background color effect*/
  .comment-list .panel.arrow.right:after {
    right: -14px;
    top: 31px;
    /*Change for different outline color*/
    border-left-color: #FFFFFF;
    border-width: 15px;
  }
}
.comment-list .comment-post {
  margin-top: 6px;
}

textarea {
  overflow: auto;
  resize: none;
}

.text-danger {
  color: red;
}

/* // para la paginacion  */

ul{
  padding: 4px 4px;
  border: 1px solid black;
  
}
li{
  list-style-type:none;
  padding:4px 4px;
}
li:hover{
  background-color:#eee;
}
li:nth-child(2n){
  background-color:#ddd;
}
li:nth-child(2n):hover{
  background-color:#ccc;
}

button{
  width:100px;
  height:40px;
  background-color:#eef;
}

button:hover{
  cursor:pointer;
}
button:hover:disabled{
  cursor:not-allowed;
}

.overflow{
  white-space: normal;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>