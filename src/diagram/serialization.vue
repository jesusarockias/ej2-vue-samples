<template>
<div class="control-section">
<div>
    <ejs-toolbar id='toolbar' style="width:100%;height: 10%;margin-top: 10px;"
                :clicked='toolbarclicked'
                :items='toolbaritems'>
    </ejs-toolbar>
</div>
<div class="control-section">
    <div style="width:100%;height: 80%">
        <div id="palette-space" class="sb-mobile-palette">
            <ejs-symbolpalette id="symbolpalette" :expandMode='paletteexpandMode'
                              :palettes='palettes'
                              :getNodeDefaults='palettegetNodeDefaults'
                              :symbolMargin='palettesymbolMargin'
                              :getSymbolInfo='palettegetSymbolInfo'
                              :width='palettewidth'
                              :height='paletteheight'
                              :symbolHeight='palettesymbolHeight'
                              :symbolWidth='palettesymbolWidth'/>
        </div>

        <div id="diagram-space" class="sb-mobile-diagram">
            <ejs-diagram style='display:block' ref="diagramObj" id="diagram"       
            :width='width'
            :height='height'
            :nodes='nodes'
            :connectors='connectors'
            :snapSettings='snapSettings'
            :getConnectorDefaults='getConnectorDefaults'/>
        </div>
          <div id="upload-container">
          <ejs-uploader id="fileupload" name="UploadFiles"       
                      :asyncSettings='fileuploadasyncSettings'
                      :success='fileuploadsuccess'
                      :showFileList ='showFile'/>
           </div>
        
    </div>
</div>
<div id="action-description">
    <p>
        This sample visualizes building diagrams interactively and editing the saved diagrams. Symbol Palette is used to easily build diagrams. 
    </p>
</div>
<div id="description">
    <p>
        This example shows how to drag-and-drop shapes and connectors from symbol palette to build diagrams. You can save the diagram as text files and edit the pre-saved diagrams. The <code>saveDiagram</code> method can be used to save the diagram as string. The <code>loadDiagram</code> method can be used to load the diagram from a string. In this example, context menu and undo/redo features are enabled.
    </p>

    <p style="font-weight: 500">Injecting Module</p>
    <p>
        The diagram component’s features are segregated into individual feature-wise modules. To enable undo/redo support, inject <code>UndoRedo</code> module using <code>provide: { diagram: [UndoRedo] }</code> method. To enable  context menu, inject <code>DiagramContextMenu</code> module using <code>provide: { diagram: [DiagramContextMenu] }</code> method.
    </p>
    <br>
</div>
</div>
</template>

<style scoped>
  .e-file-select-wrap {
    display: none;
  }
  #upload-container  {
    display: none;
  }
  .control-section {
    padding-top: 0px;
    padding-right: 0px;
  }

  @font-face {
    font-family: "e-ddb-icons";
    src: url(data:application/x-font-ttf;charset=utf-8;base64,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);
    font-weight: normal;
    font-style: normal;
  }

  @font-face {
    font-family: 'e-ddb-icons2';
    src: url(data:application/x-font-ttf;charset=utf-8;base64,AAEAAAAKAIAAAwAgT1MvMj1tSfIAAAEoAAAAVmNtYXDnEOdVAAABiAAAADZnbHlmdC1P4gAAAcgAAAAwaGVhZBJhohMAAADQAAAANmhoZWEIVQQDAAAArAAAACRobXR4CAAAAAAAAYAAAAAIbG9jYQAYAAAAAAHAAAAABm1heHABDgAUAAABCAAAACBuYW1lm+wy9gAAAfgAAAK1cG9zdLnsYngAAASwAAAAMAABAAAEAAAAAFwEAAAAAAAD+AABAAAAAAAAAAAAAAAAAAAAAgABAAAAAQAAgNcenF8PPPUACwQAAAAAANelrs4AAAAA16WuzgAAAAAD+AN6AAAACAACAAAAAAAAAAEAAAACAAgAAgAAAAAAAgAAAAoACgAAAP8AAAAAAAAAAQQAAZAABQAAAokCzAAAAI8CiQLMAAAB6wAyAQgAAAIABQMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAUGZFZABA5wDnAAQAAAAAXAQAAAAAAAABAAAAAAAABAAAAAQAAAAAAAACAAAAAwAAABQAAwABAAAAFAAEACIAAAAEAAQAAQAA5wD//wAA5wD//wAAAAEABAAAAAEAAAAAAAAAGAAAAAIAAAAAA/gDegACAAcAACUhCQEhATUhAQQC9P6G/YoBMQFF/YqGAjf+hgH0QwAAAAAAEgDeAAEAAAAAAAAAAQAAAAEAAAAAAAEAEwABAAEAAAAAAAIABwAUAAEAAAAAAAMAEwAbAAEAAAAAAAQAEwAuAAEAAAAAAAUACwBBAAEAAAAAAAYAEwBMAAEAAAAAAAoALABfAAEAAAAAAAsAEgCLAAMAAQQJAAAAAgCdAAMAAQQJAAEAJgCfAAMAAQQJAAIADgDFAAMAAQQJAAMAJgDTAAMAAQQJAAQAJgD5AAMAAQQJAAUAFgEfAAMAAQQJAAYAJgE1AAMAAQQJAAoAWAFbAAMAAQQJAAsAJAGzIERpYWdyYW1fU2hhcGVzX0ZPTlRSZWd1bGFyRGlhZ3JhbV9TaGFwZXNfRk9OVERpYWdyYW1fU2hhcGVzX0ZPTlRWZXJzaW9uIDEuMERpYWdyYW1fU2hhcGVzX0ZPTlRGb250IGdlbmVyYXRlZCB1c2luZyBTeW5jZnVzaW9uIE1ldHJvIFN0dWRpb3d3dy5zeW5jZnVzaW9uLmNvbQAgAEQAaQBhAGcAcgBhAG0AXwBTAGgAYQBwAGUAcwBfAEYATwBOAFQAUgBlAGcAdQBsAGEAcgBEAGkAYQBnAHIAYQBtAF8AUwBoAGEAcABlAHMAXwBGAE8ATgBUAEQAaQBhAGcAcgBhAG0AXwBTAGgAYQBwAGUAcwBfAEYATwBOAFQAVgBlAHIAcwBpAG8AbgAgADEALgAwAEQAaQBhAGcAcgBhAG0AXwBTAGgAYQBwAGUAcwBfAEYATwBOAFQARgBvAG4AdAAgAGcAZQBuAGUAcgBhAHQAZQBkACAAdQBzAGkAbgBnACAAUwB5AG4AYwBmAHUAcwBpAG8AbgAgAE0AZQB0AHIAbwAgAFMAdAB1AGQAaQBvAHcAdwB3AC4AcwB5AG4AYwBmAHUAcwBpAG8AbgAuAGMAbwBtAAAAAAIAAAAAAAAACgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAgECAQMABlNoYXBlcwAA) format('truetype');
    font-weight: normal;
    font-style: normal;
  }

  .e-ddb-icons {
    font-family: "e-ddb-icons";
    speak: none;
    font-size: 55px;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    line-height: 1;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .e-new::before {
    content: "\e70f";
  }

  .e-save::before {
    content: "\e710";
  }

  .e-open::before {
    content: "\e713";
  }

  .material .e-new::before {
    content: "\e704";
  }

  .material .e-save::before {
    content: "\e705";
  }

  .material .e-open::before {
    content: "\e708";
  }

  .bootstrap .e-new::before {
    content: "\e71c";
  }

  .bootstrap .e-save::before {
    content: "\e71d";
  }

  .bootstrap .e-open::before {
    content: "\e720";
  }

  .e-toolbar
    .e-toolbar-items
    .e-toolbar-item
    .e-tbar-btn.e-btn.e-tbtn-txt
    .e-icons.e-btn-icon {
    padding: 3px;
  }
  .sb-mobile-palette {
    width:240px;
    height:100%;
    float:left;
  }

  .sb-mobile-palette-bar {
    display: none;
  }

  .sb-mobile-diagram {
    width:calc(100% - 242px);
    height: 100%;
    float: left;
  }

  @media (max-width: 550px) {

    .sb-mobile-palette {
      z-index: 19;
      position: absolute;
      display: none;
      transition: transform 300ms linear, visibility 0s linear 300ms;
      width:39%;
      height:100%;
    }
    
    .sb-mobile-palette-bar {
      display: block;
      width: 100%;
      background:#fafafa;
      padding: 10px 10px;
      border:0.5px solid #e0e0e0;
      min-height: 40px;
    }
    
    .sb-mobile-diagram {
      width: 100%;
      height: 100%;
      float: left;
      left: 0px;
    }

    #palette-icon {
      font-size: 20px; 
    }
  }
    
  .sb-mobile-palette-open {
    position: absolute;
    display: block;
    right: 15px;
  }

  .e-ddb-icons2 {
    font-family: 'e-ddb-icons2';
    speak: none;
    font-size: 16px;
    font-style: normal;
    font-weight: normal;
    font-variant: normal;
    text-transform: none;
    line-height: 1;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .e-toggle-palette::before {
    content: "\e700"
  }

  #palette-icon {
    display: none;
  }

  @media (max-width: 550px) {
    #palette-icon {
      display: inline-flex;
    }
  }
</style>

<script>
import Vue from "vue";
import {
  DiagramPlugin,
  Diagram,
  NodeModel,
  UndoRedo,
  ConnectorModel,
  PointPortModel,
  Node,
  SymbolPalette,
  SymbolInfo,
  DiagramContextMenu,
  GridlinesModel
} from "@syncfusion/ej2-vue-diagrams";
import { Uploader, UploaderPlugin } from "@syncfusion/ej2-vue-inputs";
Diagram.Inject(UndoRedo, DiagramContextMenu);
import {
  ToolbarPlugin,
  Toolbar,
  ClickEventArgs
} from "@syncfusion/ej2-vue-navigations";

import { isNullOrUndefined } from "@syncfusion/ej2-base";

Vue.use(DiagramPlugin);
Vue.use(ToolbarPlugin);
Vue.use(UploaderPlugin);

let diagramInstance;

//Initializes the nodes for the diagram
let nodes = [
  {
    id: "Start",
    height: 50,
    width: 100,
    offsetX: 50,
    offsetY: 60,
    shape: { type: "Flow", shape: "Terminator" },
    annotations: [
      {
        content: "Start"
      }
    ],
    style: { fill: "#d0f0f1", strokeColor: "#797979" }
  },
  {
    id: "Alarm",
    height: 50,
    width: 100,
    offsetX: 50,
    offsetY: 160,
    shape: { type: "Flow", shape: "Process" },
    annotations: [
      {
        content: "Alarm Rings"
      }
    ],
    style: { fill: "#fbfdc5", strokeColor: "#797979" }
  },
  {
    id: "Ready",
    height: 80,
    width: 100,
    offsetX: 50,
    offsetY: 260,
    shape: { type: "Flow", shape: "Decision" },
    annotations: [
      {
        content: "Ready to Get Up?",
      }
    ],
    style: { fill: "#c5efaf", strokeColor: "#797979" }
  },
  {
    id: "Climb",
    height: 50,
    width: 100,
    offsetX: 50,
    offsetY: 370,
    shape: { type: "Flow", shape: "Process" },
    annotations: [
      {
        content: "Climb Out of Bed"
      }
    ],
    style: { fill: "#fbfdc5", strokeColor: "#797979" }
  },
  {
    id: "End",
    height: 50,
    width: 100,
    offsetX: 50,
    offsetY: 460,
    shape: { type: "Flow", shape: "Terminator" },
    annotations: [
      {
        content: "End"
      }
    ],
    style: { fill: "#d0f0f1", strokeColor: "#797979" }
  },
  {
    id: "Relay",
    height: 50,
    width: 100,
    offsetX: 250,
    offsetY: 160,
    shape: { type: "Flow", shape: "Delay" },
    annotations: [
      {
        content: "Relay"
      }
    ],
    style: { fill: "#f8eee5", strokeColor: "#797979" }
  },
  {
    id: "Hit",
    height: 50,
    width: 100,
    offsetX: 250,
    offsetY: 260,
    shape: { type: "Flow", shape: "Process" },
    annotations: [
      {
        content: "Hit Snooze Button",
        margin: { top: 10, left: 10, right: 10, bottom: 10 }
      }
    ],
    style: { fill: "#fbfdc5", strokeColor: "#797979" }
  }
];
//Initializes the connector for the diagram
let connectors = [
  {
    id: "connector1",
    sourceID: "Start",
    targetID: "Alarm"
  },
  { id: "connector2", sourceID: "Alarm", targetID: "Ready" },
  {
    id: "connector3",
    sourceID: "Ready",
    targetID: "Climb",
    annotations: [{ content: "Yes", style: { fill: "white" } }]
  },
  { id: "connector4", sourceID: "Climb", targetID: "End" },
  {
    id: "connector5",
    sourceID: "Ready",
    targetID: "Hit",
    annotations: [{ content: "No", style: { fill: "white" } }]
  },
  { id: "connector6", sourceID: "Hit", targetID: "Relay" },
  { id: "connector7", sourceID: "Relay", targetID: "Alarm" }
];
let interval;
interval = [
  1,
  9,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75,
  0.25,
  9.75
];
let gridlines = {
  lineColor: "#e0e0e0",
  lineIntervals: interval
};

export default Vue.extend({
  data: function() {
    return {
      width: "100%",
      height: "700px",
      nodes: nodes,
      connectors: connectors,
      snapSettings: {
        horizontalGridlines: gridlines,
        verticalGridlines: gridlines
      },
      getConnectorDefaults: (args, diagram) => {
        if (args.targetDecorator) {
          args.targetDecorator.height = 5;
          args.targetDecorator.width = 5;
          args.targetDecorator.style = {
            fill: "#797979",
            strokeColor: "#797979"
          };
        }
        if (args.style) args.style.strokeColor = "#797979";
        return args;
      },
      paletteexpandMode: "Multiple",
      palettes: [
        {
          id: "flow",
          expanded: true,
          symbols: flowshapes,
          iconCss: "shapes",
          title: "Flow Shapes"
        },
        {
          id: "connectors",
          expanded: true,
          symbols: connectorSymbols,
          iconCss: "shapes",
          title: "Connectors"
        }
      ],
      //set default value for Node.
      palettegetNodeDefaults: (symbol) => {
        if (symbol.id === "Terminator" || symbol.id === "Process") {
          symbol.width = 80;
          symbol.height = 40;
        } else if (
          symbol.id === "Decision" ||
          symbol.id === "Document" ||
          symbol.id === "PreDefinedProcess" ||
          symbol.id === "PaperTap" ||
          symbol.id === "DirectData" ||
          symbol.id === "MultiDocument" ||
          symbol.id === "Data"
        ) {
          symbol.width = 50;
          symbol.height = 40;
        }
        if (symbol.style) symbol.style.strokeWidth = 2;
      },
      palettesymbolMargin: { left: 15, right: 15, top: 15, bottom: 15 },
      palettegetSymbolInfo: (symbol) => {
        return { fit: true };
      },
      palettewidth: "100%",
      paletteheight: "700px",
      palettesymbolHeight: 60,
      palettesymbolWidth: 60,

      toolbarclicked: (args) => {
        if (args.item.text === "New") {
          diagramInstance.clear();
        } else if (args.item.text === "Load") {
          let element = document.getElementsByClassName(
            "e-file-select-wrap"
          );
          let htmlButtonElement = element[0].querySelector(
            "button"
          );
          htmlButtonElement.click();
        } else if (args.item.id === 'palette-icon') {
          openPalette();
        } else {
          download(diagramInstance.saveDiagram());
        }
      },
      toolbaritems: [
        { text: "New", tooltipText: "New", },
        {
          type: "Separator"
        },
        {
          text: "Save",
          tooltipText: "Save",
        },
        {
          type: "Separator"
        },
        {
          text: "Load",
          tooltipText: "Load"
        }
      ],
      fileuploadasyncSettings: {
        saveUrl: "https://aspnetmvc.syncfusion.com/services/api/uploadbox/Save",
        removeUrl:
          "https://aspnetmvc.syncfusion.com/services/api/uploadbox/Remove"
      },
      fileuploadsuccess: onUploadSuccess,
      showFile: true
    };
  },
  provide: {
    diagram: [UndoRedo, DiagramContextMenu]
  },
  mounted: function() {
    diagramInstance = this.$refs.diagramObj.ej2Instances;
    diagramInstance.fitToPage();
  }
});

//save the diagram object in json data.
function download(data) {
  let dataStr =
    "data:text/json;charset=utf-8," + encodeURIComponent(data);
  let a = document.createElement("a");
  a.href = dataStr;
  a.download = "Diagram.json";
  document.body.appendChild(a);
  a.click();
}

function onUploadSuccess(args) {
  let file1  = args.file;
  let file= file1.rawFile;
  let reader = new FileReader();
  reader.readAsText(file);
  reader.onloadend = loadDiagram;
}

//Load the diagraming object.
function loadDiagram(event) {
  diagramInstance.loadDiagram((event.target).result);
}

//Initialize the flowshapes for the symbol palatte
let flowshapes= [
  { id: "Terminator", shape: { type: "Flow", shape: "Terminator" } },
  { id: "Process", shape: { type: "Flow", shape: "Process" } },
  { id: "Decision", shape: { type: "Flow", shape: "Decision" } },
  { id: "Document", shape: { type: "Flow", shape: "Document" } },
  {
    id: "PreDefinedProcess",
    shape: { type: "Flow", shape: "PreDefinedProcess" }
  },
  { id: "PaperTap", shape: { type: "Flow", shape: "PaperTap" } },
  { id: "DirectData", shape: { type: "Flow", shape: "DirectData" } },
  { id: "SequentialData", shape: { type: "Flow", shape: "SequentialData" } },
  { id: "Sort", shape: { type: "Flow", shape: "Sort" } },
  { id: "MultiDocument", shape: { type: "Flow", shape: "MultiDocument" } },
  { id: "Collate", shape: { type: "Flow", shape: "Collate" } },
  { id: "SummingJunction", shape: { type: "Flow", shape: "SummingJunction" } },
  { id: "Or", shape: { type: "Flow", shape: "Or" } },
  { id: "InternalStorage", shape: { type: "Flow", shape: "InternalStorage" } },
  { id: "Extract", shape: { type: "Flow", shape: "Extract" } },
  { id: "ManualOperation", shape: { type: "Flow", shape: "ManualOperation" } },
  { id: "Merge", shape: { type: "Flow", shape: "Merge" } },
  {
    id: "OffPageReference",
    shape: { type: "Flow", shape: "OffPageReference" }
  },
  {
    id: "SequentialAccessStorage",
    shape: { type: "Flow", shape: "SequentialAccessStorage" }
  },
  { id: "Annotation", shape: { type: "Flow", shape: "Annotation" } },
  { id: "Annotation2", shape: { type: "Flow", shape: "Annotation2" } },
  { id: "data", shape: { type: "Flow", shape: "Data" } },
  { id: "Card", shape: { type: "Flow", shape: "Card" } },
  { id: "Delay", shape: { type: "Flow", shape: "Delay" } }
];
//Initializes connector symbols for the symbol palette
let connectorSymbols = [
  {
    id: "Link1",
    type: "Orthogonal",
    sourcePoint: { x: 0, y: 0 },
    targetPoint: { x: 40, y: 40 },
    targetDecorator: { shape: "Arrow" },
    style: { strokeWidth: 2 }
  },
  {
    id: "link2",
    type: "Orthogonal",
    sourcePoint: { x: 0, y: 0 },
    targetPoint: { x: 40, y: 40 },
    style: { strokeWidth: 2 },
    targetDecorator: { shape: "None" }
  },
  {
    id: "Link3",
    type: "Straight",
    sourcePoint: { x: 0, y: 0 },
    targetPoint: { x: 40, y: 40 },
    targetDecorator: { shape: "Arrow" },
    style: { strokeWidth: 2 }
  },
  {
    id: "link4",
    type: "Straight",
    sourcePoint: { x: 0, y: 0 },
    targetPoint: { x: 40, y: 40 },
    style: { strokeWidth: 2 },
    targetDecorator: { shape: "None" }
  },
  {
    id: "link5",
    type: "Bezier",
    sourcePoint: { x: 0, y: 0 },
    targetPoint: { x: 40, y: 40 },
    style: { strokeWidth: 2 },
    targetDecorator: { shape: "None" }
  }
];

function openPalette() {
  let paletteSpace = document.getElementById('palette-space') ;
  let isMobile = window.matchMedia('(max-width:550px)').matches;
  if (isMobile) {
    if (!paletteSpace.classList.contains('sb-mobile-palette-open')) {
      paletteSpace.classList.add('sb-mobile-palette-open');
    } else {
      paletteSpace.classList.remove('sb-mobile-palette-open');
    }
  }
}
</script>
