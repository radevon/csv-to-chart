<template>
<div class="container">
  <div class="row">
    <div class="col s12">
      <form action="#">
        <div class="file-field input-field">
          <div class="btn blue-grey darken-1">
            <span>Data file .csv</span>
            <input type="file" accept=".csv" @change="loadFile">
          </div>
          <div class="file-path-wrapper">
            <input class="file-path validate" type="text">
          </div>
        </div>
      </form>

    
    </div>
  </div> 
  </div> 
  
  <file-parser :text="text"/>
</template>

<script>

import FileParser from './components/FileParser.vue';

export default {
  components: { FileParser },
  name: 'App',
  data: function(){
    return {
      text:''
    }
  },
  methods: {
    loadFile(event){
      if(event.target.files.length==0) {  alert('There is no choosen file!');   this.text='';  return; }
      
      let f=event.target.files[0];
      if(f.name.indexOf('.csv',)!=f.name.length-4)
       {   alert('This is not .csv file!');   event.target.value = '';    this.text='';  return;}

      let reader=new FileReader();
      
      reader.onload = ()=> this.text=reader.result;

      reader.onerror = () => { alert('Error while read .csv file!');  this.text='';    };

      reader.readAsText(f);
      
    }
  },
  components:{ FileParser }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
