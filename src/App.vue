<template>
    <div class = "hello">
        <input type="text" placeholder="Votre login" v-on:keyup.enter="setLogin">
        <p>Votre login est : {{pseudo}}</p>
    
        Charger une image pour un test
        <div class="large-12 medium-12 small-12 cell">
            <label>File
                <input type="file" id="file" ref="file" v-on:change="uploadFile()"/>
            </label>
            <button v-on:click="submitFile()">Submit</button>
        </div>

        Resultat : {{res_predict}}

        <canvas id="cnv" @mousemove="draw"></canvas>
        <h2>Position {{x}}/{{y}}</h2>
    </div>
</template>

<style>
  canvas{
    width:200px; height: 200px;
    border: 1px solid #000;
  }
</style>



<script>
import axios from "axios";
export default {
    name:'App',
    data: function(){
        return {
            pseudo: "Lapinou",
            file:'',
            res_predict: 0,
            x:0,
            y:0,
        }
    },
    methods:{
        setLogin:function(e){
            this.pseudo = e.target.value;
            axios.get('http://localhost:8000/update_login/',{params:{pseudo:e.target.value}});
            this.loadLogin();
        },
        loadLogin:function(){
            axios.get('http://localhost:8000/get_login/').then(response => {this.pseudo = response.data.result})
        },
        uploadFile(){
          this.file = this.$refs.file.files[0];
        },
        submitFile(){
            let formData = new FormData();
            formData.append('file',this.file);
            axios.post('http://localhost:8000/upload/',
                formData,
                {
                  headers:{
                    'Content-Type':'multipart/form-data'
                  }
                }
            ).then(function(){
                console.log('SUCCESS!!');
            })
            .catch(function(error){
                console.log(error);
                console.log('FAILURE!!');
            });
            this.getPrediction();
        },
        getPrediction(){
            axios.get('http://localhost:8000/get_prediction/')
                .then(response => {
                    this.res_predict = response.data.result
                })
        },
        draw:function(e){
          this.x = e.offsetX;
          this.y = e.offsetY;
          var canvas = document.getElementById("cnv");
          var ctx = canvas.getContext('2d');
          ctx.fillRect(e.offsetX,e.offsetY,5,5);
        }
    }
}
</script>
