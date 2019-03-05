<template>
  <div class="container">
      <div class="row justify-content-center">
          <div class="col-md-8">
            <!-- previene que la pagina se refresque -->
              <form v-on:submit.prevent="">
                  <div class="form-group">
                    <label for="nombre"><h3>Comparte tu Opinión</h3></label>
                    <input type="text" class="form-control" id="nombre" placeholder="Ingresa tu Nombre" title="Nombre" required>
                    <small id="nombreHelp" class="form-text text-muted">Nombre visible para la comunidad.</small>
                  </div>
                  <div class="form-group">
                      <label for="txtArea"><h4>Ingresa tu Comentario</h4></label>
                      <textarea class="form-control" v-model="message" id="txtArea" rows="3" title="Comentarios" v-on:keydown="countdown()" required></textarea>
                      <small id="comHelp" class="form-text text-muted" v-bind:class="{'text-danger': hasError }">{{remainingCount}} |:V*| Escribe tu opinión en menos de {{maxCount}} carácteres.</small>
                  </div>
                  <div class="form-group" style= "visibility: hidden; color:red" id="textAdvertencia">
                      <label>Excediste el número de caracteres</label>                      
                  </div>
                  <button type="submit" v-on:click="postear();obtenerPosts()" class="btn btn-primary" id="botonSubmit">Comentar</button>
                </form>
          </div>
      </div>
      <!-- <div class="row justify-content-center">
        <div class="col-md-8">
          <h2 class="page-header">Comentarios</h2>
            <section class="comment-list" v-if="arregloPosts && arregloPosts.length" v-model="arregloPosts">
              <article class="row" v-for="post of arregloPosts">
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
                          <p>{{post.comentario}}</p>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </article>
            </section>
        </div>
      </div> -->
      <div>
        <pagination :list-data="arregloPosts"/>
      </div>
    </div>
</template>

<script>
import pagination from './pagination.vue';
import axios from 'axios';///importar la dependencia de axios
export default {
  name: 'HelloWorld',
  components: {
    pagination
  },
  data(){
    return{
      arregloPosts: [],
      maxCount: 300,
      remainingCount: 300,
      message: '',
      hasError: false,
      nombre: '',
      //groserias a ser evaluadas
      groserias: ["puta","puto","marica","pirobo","gonorrea",
      "hijo de puta","malparido","gonorreo","hpta","piro","piroo","hp","loca","jijueputa",
      "piroba","piroa","ñunfla","huevon","guevon","weon","marico","maricon","cabron","chupa","pijas"
      ,"chupa pijas","sifilis","hijueputa","mierda","caca","excremento"],
      groseria: false
    }
  },
  created() {
      this.obtenerPosts();
  },
  methods:{
    //////////////////
    regularExpression: function(){
      // [dominio] indica un dominio que puede ir en la cadena de texto
      // /expresion/ indica la expresión regular sirviendo como un formato o verificando contenido en el texto
      // new RegExp() es un metodo para comprobar la expresión regular
      // /expresion/i al final de la expresion lo hace case insentitive sin la i es case sensitive 
      // ? indica que la presencia de un elemento es opcional, el caracter o grupo anterior a este signo
      // () para agrupar una parte del patron
      // test evalua si alguna parte del texto contiene la expresion evaluada, si la cadena tiene alguna palabra 
      // que coincida con el formato va a devolver true
      // /^ expresion $/ se usan tipicamente al inicio y final del patron y van por dentro del delimitador
      // buscan coincidencias al inicio y al final de la cadena
      // \w indica el patron para usar [a-zA-z0-9_] una abrevación para esto
      //  \d es equivalente al patrón [0-9] /^([VE]-)?\d{1,8}$/i
      const cedula    = 'v-11122233'// --- cadena a probar
      const ced = 'xxV-111111'
      const regExp    = new RegExp( /[VE]-[0-9]{1,8}/) // --- sin comillas
      const regExp2 = new RegExp(/([VE]-)?[0-9]{1,8}/i) // --- no es obligatorio el uso de VE con el guion
      const regExp3 = new RegExp(/^([VE]-)?[0-9]{1,8}$/i) // se asegura que la cadena completa coincide

      const resultado = regExp.test( cedula )
      const resultado2 = regExp2.test(cedula)
      const resultado3 = regExp3.test(ced)
      //--
      console.log ( resultado ) // --- true = coincide, false = no-coincide
      console.log(resultado2)
      console.log(resultado3)
    },
    //comprobar si los posts tienen una groseria con expresiones regulares
    filtro: function(){
      this.message = document.getElementById("txtArea").value;
      // console.log(text);
      for(var i = 0; i < this.groserias.length;i++){
          var regex = new RegExp("(^|\\s)"+this.groserias[i]+"($|(?=\\s))","gi");
          // console.log(regex)
          var evaluacion = regex.test(this.message);
          if(evaluacion == true){
            console.log("groseria");
            this.groseria = true;
          }          
      }
    },
    //funcion para calcular cuantos caracteres tiene por escribir la persona
    countdown: function() {
      // console.log(this.message);
      this.remainingCount = this.maxCount - this.message.length;
      this.hasError = this.remainingCount < 0;
      if(this.remainingCount<=0){
        document.getElementById("textAdvertencia").style.visibility = "visible";
        document.getElementById("botonSubmit").disabled = true;
      }else{
        document.getElementById("textAdvertencia").style.visibility = "hidden";
        document.getElementById("botonSubmit").disabled = false;
      }
    },
    obtenerPosts: function(){
      var inst = this;
      const mod = "cap1"; //se agrega esta constante por que son los comentarios sobre el capitulo1
      //para otros comentarios se cambiarará esta constante
      //obtiene solo los posts del capitulo especificado
        // this.arreglo = [];
        axios.get('https://cors-anywhere.herokuapp.com/geoapps.esri.co/TheService/api/mod/?'+'modulo='+mod)
        // axios.get('data/prueba.json/') //data de prueba
        .then(function(response){
            var arreglo = response.data;
            console.log(arreglo);
            inst.arregloPosts = arreglo.reverse(); //para ver los posts mas recientes
            console.log(inst.arregloPosts); // ex.: { user: 'Your User'}
            console.log(response.status); // ex.: 200
      });
    },
    limpiarCampos: function(){
      document.getElementById("nombre").value = "";
      document.getElementById("txtArea").value = "";
    },
    postear: function(){
      try {
        var inst = this;
        if(inst.remainingCount >= 0){ ///comporbar si tiene mas de los 300 carateres escritos
        this.filtro();
          if(!this.groseria){ ///filtra si tiene groserias
            var headers = {
            "Content-Type": "application/json",
            "Allow": "*",
            };
            var usuario = document.getElementById("nombre").value;
            var comentario = document.getElementById("txtArea").value;
            var fecha = new Date();
            var f = fecha.getDate()+"/"+(fecha.getMonth()+1)+"/"+fecha.getFullYear();
            var h = fecha.getHours()+":"+fecha.getMinutes();
            var fechaCom = f+" "+h;
            // var idd = inst.arregloPosts.length+1;

            // console.log(inst.message);
            // console.log(inst.nombre);
            var data = {
                      // id: idd,
                      nombre: usuario,
                      comentario: comentario,
                      fechaHora: fechaCom,
                      modulo: "cap1"
                  };
            var test= JSON.stringify(data);
            // console.log(test);
            axios.post('https://cors-anywhere.herokuapp.com/geoapps.esri.co/TheService/api/mongo/',  test, {headers: headers})
                  // axios.post('geoapps.esri.co:81/api/reporte/',  test, {headers: headers})
                  .then(function (response){
                    inst.obtenerPosts();
                    console.log("Reporte agregado");
                      // alert("Reporte agregado");
                  })
                  .catch(function (error){
                      console.log(error);
                      console.log("No se pudo agregar");
                  });
          }else{
            alert("Apreciamos tu opinión y tiempo para comentar, sin embargo este comentario no esta permitido debído al lenguaje usado en él");
          }
        }else{
          alert("Pasó el limite de caracteres permitidos");
        }
      } catch (error) {
        console.log(error);
        console.log("error al registrar");
      }
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
</style>
