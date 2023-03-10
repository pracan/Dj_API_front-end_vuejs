<template>
    <div>
      <button id="start">start</button>
      <button id="reset">reset</button>
    </div>
    <div id="sigma-container"></div>
  </template>
  
  <script setup>
  import Graph from "graphology";
  import { circlepack } from "graphology-layout";
  import FA2Layout from "graphology-layout-forceatlas2/worker";
  import forceAtlas2 from "graphology-layout-forceatlas2";
  import Sigma from "sigma";
  import getNodeProgramImage from "sigma/rendering/webgl/programs/node.image";
  import dataJson from "../assets/data.json";
  import { onMounted, onUnmounted } from "vue";

  import axios from 'axios';
  
  let fa2Layout = null;
  onMounted(() => {
    const graph = new Graph({ multi: true });
    axios.get('http://127.0.0.1:8000/sellers/getAllPersons') //addresse à modifier avec l'adresse perso
        .then(response => {
          console.log(response.data);
					console.log(response.status);
          const graphData = transformData(response.data);
					// const graphData = faketransformData();
					// console.log(graphData);
					// graphConfig.graph = graphData;
          console.log(graphData);
          // const s = new sigma(graphConfig);
          const graph2 = new Graph();
          graph2.import(graphData);
          console.log(graph);


    const container = document.getElementById("sigma-container");
    
    graph.import(graphData);
    console.log(graph);
    graph.nodes().forEach((node, i) => {
      graph.setNodeAttribute(node, "size", 5);
      graph.setNodeAttribute(node, "type", "image");
      graph.setNodeAttribute(node, "image", "https://img95.699pic.com/photo/50034/0209.jpg_wh300.jpg");
    });
    graph.edges().forEach((edge, i) => {
      graph.setEdgeAttribute(edge, "type", "arrow");
    });
    circlepack.assign(graph);
    const settings = forceAtlas2.inferSettings(graph);
    fa2Layout = new FA2Layout(graph, { settings });
    console.log(graph._nodes);
    const render = new Sigma(graph, container, {
      allowInvalidContainer: true,
      renderEdgeLabels: true, // 显示线的label
      nodeProgramClasses: {
        image: getNodeProgramImage()
      }
    });
  
    const startBtn = document.getElementById("start");
    const resetBtn = document.getElementById("reset");
    startBtn.addEventListener("click", () => {
      fa2Layout.start();
    });
    resetBtn.addEventListener("click", () => {
      if (fa2Layout.isRunning) fa2Layout.stop();
      circlepack.assign(graph);
      render.refresh();
    });

        })
        .catch(error => console.error(error));
  });
  
  onUnmounted(() => {
    if (fa2Layout) {
      fa2Layout.kill();
    }
  });

  function transformData(data) {
  const nodes = [];
  const edges = [];
  let count = 0;

  // Créer les noeuds à partir des données de l'API
  for (const person of data) {
    const node = { key: count++, id: person.uid, label: person.name };
    nodes.push(node);
  }

  // Créer les arrêtes (liens) à partir des données de l'API
  edges.push({ key: count++, source: nodes[0].key, target: nodes[1].key });

  return { nodes, edges };
}
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  #sigma-container {
    position: relative;
    height: 90vh;
    border: 1px solid #e8e8e8;
    border-radius: 8px;
  }
  button + button {
    margin-left: 10px;
  }
  </style>
  