<script setup lang="ts">
import {Html5QrcodeScanner} from "html5-qrcode";

let copyButton:HTMLElement | null;
let resultDom:HTMLElement | null;

function onScanSuccess(decodedText:string) {
  if(resultDom){
    resultDom.innerText = decodedText;
  }
}

function onScanFailure(error:any) {
  console.warn(`Code scan error = ${error}`);
}

const CopyResult = () => {
  let textToCopy = resultDom?.innerText?resultDom.innerText:"";

  navigator.clipboard.writeText(textToCopy).then(()=>{
    if(copyButton===null) return;

    if ("innerText" in copyButton) {
      copyButton.innerText = "Copied ✔️";
    }
    setTimeout(()=>{
      if(copyButton===null) return;
      if ("innerText" in copyButton) {
        copyButton.innerText = "Copy";
      }
    },2000);
  })
}

document.addEventListener("DOMContentLoaded",function (){
  let html5QrcodeScanner = new Html5QrcodeScanner(
      "reader",
      { fps: 10, qrbox: {width: 250, height: 250} },
      /* verbose= */ false);
  html5QrcodeScanner.render(onScanSuccess, onScanFailure);

  resultDom =document.getElementById('text');
  copyButton = document.getElementById("CopyBtn");
})
</script>

<template>
<div id="reader" width="600px"></div>
  <div id="text"></div>
  <button id="CopyBtn" @click="CopyResult">Copy</button>
</template>

<style scoped>
#text{
  overflow: hidden;
}
</style>