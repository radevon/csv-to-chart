<template lang="html">
 <div class="container">
        <div class="row">
        
            <div class="input-field col s12" style="background-color:#fefefe">
                
                <select id="clm" multiple v-model="selectedData">
                    <option v-for="item in parsed.columns">{{item}}</option>
                </select>
                <label>Columns</label>
            </div>      
            
        </div>
        <div class="row">
            <div id="tableData" class="col s12">
                <table class="bordered striped responsive-table">
                <thead class="blue-grey lighten-3">
                <tr>
                    <th v-for="col in parsed.columns">{{col}}</th>            
                </tr>
                </thead>
                <tbody>
                <tr v-for="row in parsed">
                    <td v-for="(value,key) in row">
                        {{value}}
                    </td>
                </tr>
                
                </tbody>
            </table>
            </div>
        </div>
        <div class="row">
            <div class="col s12 right-align">
                {{parsed.length}} строк
            </div>
        </div>
    </div>
    <div style="width:85vw; max-width:2000px; margin:0 auto;">
        <div v-for="(line,index) in selectedData">
                <div class="col s12">
                    <LineChart :id="index" :source="parsed" :dataName="line"/>
                </div>
        </div>
   </div>
</template>

<script>

import * as d3 from 'd3';
import LineChart from './LineChart.vue';



export default {
    name:'FileParser',
    props:{
        text: {
          type: String,
          default:''
         }
    },
    data(){
        return {
            selectedData:[]
        }
    },

   
    computed:{
        parsed() { return d3.csvParse(this.text); },
    },
    watch:{
        text: function(newval,oldval){
            this.selectedData=[];
        }
    },
    updated(){
        let selects = document.querySelector('#clm');
        let opt=M.FormSelect.init(selects);
    },
    
    components:{
        LineChart
    }
}
</script>

<style lang="css" scoped>
    table {
        background-color: #fff;
    }
    td, th {
        padding: 4px 4px;
    }
</style>