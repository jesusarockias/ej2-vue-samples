<template>
  <div class="control-section">
    <div class="row linear-parent">
        <div class="col-lg-12 col-md-12" style="margin-top:1%">
           <div class="col-lg-12 col-md-12 progressbar-label" >Determinate</div>
            <div class="linear-progress">
            <div id="lineardeterminate">
            <ejs-progressbar
            ref="determinate"
            id="lineardeterminate"
            type='Linear'
            height='60'
            width='90%'
            :value='value1'
            :animation="animation"
            :load='load'
            >
            </ejs-progressbar>
          </div>
        </div>
        </div>
        <div class="col-lg-12 col-md-12" style="margin-top:2.5%">
        <div class="col-lg-12 col-md-12 progressbar-label">Indeterminate</div>
        <div class="linear-progress">
        <div id="linearindeterminate">
          <ejs-progressbar
            ref="indeterminate"
            id="successcontainer"
            type='Linear'
            height='60'
            width='90%'
            isIndeterminate=true
            :value='value2'
            :load='load'
            >
            </ejs-progressbar>
            </div>
          </div>
        </div>
         <div class="col-lg-12 col-md-12" style="margin-top:2.5%">
           <div class="col-lg-12 col-md-12 progressbar-label" >Segment</div>
            <div  class="linear-progress">
            <div id="linearsegment">
           <ejs-progressbar
            ref="segment"
            id="errorcontainer" 
            type='Linear'
            height='60'
            width='90%'
            :segmentCount='count'
            :value='value3'
            :animation="animation"
            :load='load'
            >
            </ejs-progressbar> 
            </div>
          </div>
        </div>
        <div class="col-lg-12 col-md-12" style="margin-top:2.5%">
           <div class="col-lg-12 col-md-12 progressbar-label" >
              <div class="row">
                <div class="col-lg-8 col-md-8">Buffer</div>
             </div>
            </div>
          <div class="linear-progress">
          <div id="linearbuffer">
          <ejs-progressbar
            ref="buffer"
            id="warningscontainer"                           
            type='Linear'
            height='60'
            width='90%'
            :value='value4'
            :secondaryProgress='secvalue'
            :animation="animation"
            :load='load'
            >
            </ejs-progressbar>
            </div>
         </div>
        </div>
         <div class="col-lg-12 col-md-12" style="margin-top:2.5%">
           <div class="col-lg-12 col-md-12 progressbar-label" >
              <div class="row">
                <div class="col-lg-8 col-md-8">Active</div>
             </div>
            </div>
          <div class="linear-progress">
          <div id="linearactive">
          <ejs-progressbar
            ref="active"
            id="activecontainer"                           
            type='Linear'
            height='60'
            width='90%'
            :value='value3'
            isActive=true
            :load='load'
            >
            </ejs-progressbar>
            </div>
         </div>
        </div>
    </div>
    <div id="replay-progressbar" style="margin-top:2%;margin-left:45.5%">
      <button id="reLoad" class="e-control e-btn e-lib e-outline e-primary" @click="onclick">Reload</button>
    </div>
  <div id="action-description">
      <p>This sample illustrates a linear progress bar with determinate and indeterminate states, segments and buffer value.</p>
    </div>
    <div id="description">
      <p> In this example, you can see how to render and configure the linear progress bar. Progress bar is used to visualize the progression of an extended operation. The sample shows the determinate and indeterminate states, buffer and segments of linear progress bar.</p>
    </div>
  </div>
</template>
<style scoped>
  .e-progressbar, #lineardeterminate, #linearindeterminate, #linearbuffer, #linearsegment {
            display: block;
        }
        .actual-txt{
            font-size: 14px;
        }
         #control-container {
            padding: 0px !important;
        }
        .replay-progressbar {
            right: 100px;
        }

        .linear-parent {
            text-align: center;
        }

        .progressbar-label {
            text-align: left;
            font-family: Roboto-Regular;
            font-size: 14px;
            color: #3D3E3C;
            letter-spacing: 0;
            margin-top: -2%;
            top: 20px;
            margin-left: 13.5%;
        }

        .linear-progress {
            width: 80%;
            margin: auto;
        }

        .lineartxt {
            color: black;
            top: 20px;
            left: 13.5%;
        }

        #successtext {
            color: black;
            top: 20px;
            right: 245px;
        }

        #warningtext {
            color: black;
            top: 20px;
            right: 268px;
        }

        #errortext {
            color: black;
            top: 20px;
            right: 260px;
        }


        .reload-btn {
            text-align: center;
        }

        #reLoad {
            border-radius: 4px;
            text-transform: capitalize;
        }

</style>
<script>
import Vue from "vue";
import { Browser } from "@syncfusion/ej2-base";
import {
  ProgressBarPlugin,
  ProgressAnnotation
} from "@syncfusion/ej2-vue-progressbar";

let div = document.getElementsByClassName('progressbar-label');
 
function annotationElementContent(color, controlID) {
        let content;
        let annotation='';
        switch (controlID) {
         case 'rounded-container':
                content = '60%';
                annotation='<div id="point1" style="font-size:20px;font-weight:bold;color: ' + color + ' "><span>' + content + '</span></div>';
                break;
        }
        return annotation;
    }
 let annotationColors = ['#e91e63', '#0078D6', '#317ab9', '#007bff', '#FFD939'];

Vue.use(ProgressBarPlugin);

export default Vue.extend({
  data: function() {
    return {
      value1: 100,
      value2: 20,
      value3: 100,
      value4: 40,
      secvalue:60,
      count:8,
      animation: {
        enable: true,
        duration: 2000,
        delay: 0
      }
    };
  },
  provide: {},
  methods: {
    onclick: function() {
      this.$refs.determinate.ej2Instances.refresh();
      this.$refs.indeterminate.ej2Instances.refresh();
      this.$refs.buffer.ej2Instances.refresh();
      this.$refs.segment.ej2Instances.refresh();
    },
    load: function(args) {
        let selectedTheme = location.hash.split('/')[1];
        selectedTheme = selectedTheme ? selectedTheme : 'Material';
        args.progressBar.theme = (selectedTheme.charAt(0).toUpperCase() +
        selectedTheme.slice(1)).replace(/-dark/i, 'Dark').replace(/contrast/i, 'Contrast');
        if(selectedTheme === 'highcontrast') {
            for (let i = 0; i < div.length; i++) {
                div[i].setAttribute('style', 'color:white');
            }
         }
  }
  }
});
</script>
