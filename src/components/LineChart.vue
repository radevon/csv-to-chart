
<template>
  <svg :id="'svg-'+id" ref="svg" class="line-chart" :width="width" :viewBox="viewBox" @mousemove="movemouse"> 
    <g :transform="'translate('+margin[0]+','+ margin[1]+')'">
      <text :x="rangeX[1]/2-30" style="font-size:14px; fill:rgb(200, 125, 125)">{{dataName}}</text>
      <!-- axis X and Y -->
      <g class="gX" :transform="'translate(0,'+ rangeY[0]+')'"></g>
      <g class="gY"></g>

      <!-- Data line -->
      <path class="line-chart__line"  :d="line" />
     
     
    </g>
    <path class="tracert" :d="trace"/>
  </svg>
</template>

<script>
import * as d3 from 'd3';
import mitt from 'mitt';

const emmiter=mitt();

export default {
  name: 'LineChart',
  props: {
    source: {
      required: true,
      type: Array,
    },
    width: {
      default: 1500,
      type: Number,
    },
    height: {
      default: 400,
      type: Number,
    },
    dataName: {
      type:String,
      required:true
    },
    id:{
      type:Number,
      required:true
    }
  },
  data() {
    return {
      margin: [45, 20],
      traceX:0
    };
  },
  methods: {
    drawAxis(){
      let axisY= d3.axisLeft().scale(this.Y).ticks().tickSizeInner(-this.rangeX[1]).tickSizeOuter(0);
      let axisX= d3.axisBottom().scale(this.X).ticks().tickSizeInner(-this.rangeY[0]).tickSizeOuter(0);
      let svg=d3.select("#svg-"+this.id);
      svg.select('.gX').call(axisX);
      svg.select('.gY').call(axisY);
    },

    movemouse($event){
      this.traceX=event.clientX-this.$refs.svg.getBoundingClientRect().left;
      emmiter.emit('mousemoved',this.traceX);
    }
  },
  computed: {

    points() {
      return this.source.map(x=>+x[this.dataName]);
    },

    rangeX() {
      const width = this.width - 2*this.margin[0];
      return [0, width];
    },

    rangeY() {
      const height = this.height - 2*this.margin[1];
      return [height, 0];
    },

    X(){
      return d3.scaleLinear().range(this.rangeX).domain(d3.extent(this.points, (d, i) => i));
    },

    Y(){
      return d3.scaleLinear().range(this.rangeY).domain([0, d3.max(this.points)*1.1]);
    },

    path() {
       return d3.line().x((d, i) => this.X(i)).y(d => this.Y(d));
    },    

    line() {
      return this.path(this.points);
    },

    trace() {
      return "M"+this.traceX+",0V"+this.height;
    },
    
    viewBox() {
      return `0 0 ${this.width} ${this.height}`;
    }
  },

  mounted(){
    this.drawAxis();
    //отлавливаю перемещение мыши на других компонентах
    emmiter.on('mousemoved',newTraceX=>{
      this.traceX=newTraceX;
    });
  },

  updated(){
    this.drawAxis();
  }
};
</script>


<style lang="css">
    .line-chart {
      margin: 5px;
      border:1px solid #f2f2f2;
      background-color: rgba(255, 255, 255, 0.9);
    }

  .line-chart__line {
    fill: none;
    stroke:#69996e;
    stroke-width: 2.6px;
  }

  .gX > *, .gY > *{
      opacity: 0.4;
      stroke-width:0.6px;
    }
    
  .gX  text, .gY  text {
    font-size:15px;
  }

  .tracert {
    fill:none;
    stroke:#d13a3cb0;
    stroke-linejoin:round;
		stroke-linecap:round;
		stroke-width:1;
  }
</style>