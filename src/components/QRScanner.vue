<script setup lang="ts">
import {Html5QrcodeScanner} from "html5-qrcode";
import { onMounted } from 'vue';

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
  if (!textToCopy) return;

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

onMounted(() => {
  let html5QrcodeScanner = new Html5QrcodeScanner(
      "reader",
      { fps: 10, qrbox: {width: 250, height: 250} },
      /* verbose= */ false);
  html5QrcodeScanner.render(onScanSuccess, onScanFailure);

  resultDom = document.getElementById('text');
  copyButton = document.getElementById("CopyBtn");
});
</script>

<template>
  <div class="scanner-container">
    <div class="panel glass">
      <div id="reader" width="100%"></div>
      <div class="result-section">
        <div id="text" class="scan-result" placeholder="Awaiting scan..."></div>
        <button id="CopyBtn" @click="CopyResult" class="btn btn-primary">Copy</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.scanner-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.panel {
  max-width: 500px;
  width: 100%;
  padding: 2rem;
  border-radius: 16px;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.result-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.scan-result {
  min-height: 60px;
  padding: 1rem;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  word-break: break-all;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.scan-result:empty:before {
  content: attr(placeholder);
  color: #888;
}

.btn {
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
  font-family: inherit;
  display: inline-flex;
  justify-content: center;
  align-items: center;
}

.btn-primary {
  background: linear-gradient(135deg, #646cff, #8a2be2);
  color: white;
  width: 100%;
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(100, 108, 255, 0.3);
}
</style>