<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import QRCode from 'qrcode';

const textInput = ref('');
const logoInput = ref<File | null>(null);
const logoUrl = ref<string | null>(null);
const colorDark = ref('#000000');
const colorLight = ref('#ffffff');
const canvasRef = ref<HTMLCanvasElement | null>(null);

const handleLogoUpload = (event: Event) => {
  const target = event.target as HTMLInputElement;
  if (target.files) {
    const file = target.files[0];
    if (file) {
      logoInput.value = file;
      logoUrl.value = URL.createObjectURL(file);
      generateQR();
    }
  }
};

const removeLogo = () => {
  if (logoUrl.value) {
    URL.revokeObjectURL(logoUrl.value);
  }
  logoInput.value = null;
  logoUrl.value = null;
  generateQR();
};

const generateQR = async () => {
  if (!canvasRef.value) return;

  const canvas = canvasRef.value;
  const ctx = canvas.getContext('2d');
  
  if (!textInput.value) {
    // Clear canvas if no text
    if (ctx) {
       ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
    return;
  }

  try {
    await QRCode.toCanvas(canvas, textInput.value, {
      width: 300,
      margin: 2,
      color: {
        dark: colorDark.value,
        light: colorLight.value,
      },
      errorCorrectionLevel: 'H' // High error correction to allow for logo covering
    });

    if (logoUrl.value && ctx) {
      const img = new Image();
      img.onload = () => {
        // Calculate logo size (25% of the canvas)
        const logoSize = canvas.width * 0.25;
        const x = (canvas.width - logoSize) / 2;
        const y = (canvas.height - logoSize) / 2;

        // Draw background for logo to make it stand out
        ctx.fillStyle = colorLight.value;
        ctx.fillRect(x - 5, y - 5, logoSize + 10, logoSize + 10);

        ctx.drawImage(img, x, y, logoSize, logoSize);
      };
      img.src = logoUrl.value;
    }
  } catch (err) {
    console.error('Error generating QR code', err);
  }
};

watch([textInput, colorDark, colorLight], () => {
  generateQR();
});

const downloadQR = () => {
  if (!canvasRef.value || !textInput.value) return;
  const link = document.createElement('a');
  link.download = 'qrcode.png';
  link.href = canvasRef.value.toDataURL();
  link.click();
};

onMounted(() => {
  generateQR();
});
</script>

<template>
  <div class="generator-container">
    <div class="controls panel glass">
      <div class="input-group">
        <label for="qr-text">Text or URL</label>
        <textarea id="qr-text" v-model="textInput" placeholder="Enter text or URL to generate QR..."></textarea>
      </div>
      
      <div class="color-controls">
        <div class="input-group">
          <label for="color-dark">Dots Color</label>
          <div class="color-picker-wrapper">
            <input type="color" id="color-dark" v-model="colorDark" />
            <span>{{ colorDark }}</span>
          </div>
        </div>
        <div class="input-group">
          <label for="color-light">Background Color</label>
          <div class="color-picker-wrapper">
            <input type="color" id="color-light" v-model="colorLight" />
            <span>{{ colorLight }}</span>
          </div>
        </div>
      </div>
      
      <div class="input-group">
        <label>Logo (Optional)</label>
        <div class="logo-upload-wrapper">
           <input type="file" id="logo-upload" accept="image/*" @change="handleLogoUpload" class="file-input-hidden" />
           <label for="logo-upload" class="btn btn-secondary">
             {{ logoInput ? 'Change Logo' : 'Upload Logo' }}
           </label>
           <button v-if="logoInput" @click="removeLogo" class="btn btn-danger-outline">Remove</button>
        </div>
      </div>
      
    </div>

    <div class="preview panel glass">
      <div class="canvas-wrapper">
        <canvas ref="canvasRef"></canvas>
        <div v-if="!textInput" class="placeholder-text">Enter text to generate QR code</div>
      </div>
      <button class="btn btn-primary" @click="downloadQR" :disabled="!textInput">Download QR</button>
    </div>
  </div>
</template>

<style scoped>
.generator-container {
  display: flex;
  gap: 2rem;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
}

.panel {
  flex: 1;
  min-width: 300px;
  max-width: 450px;
  padding: 2rem;
  border-radius: 16px;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  text-align: left;
}

.input-group label {
  font-weight: 500;
  font-size: 0.9rem;
  opacity: 0.9;
}

textarea {
  width: 100%;
  min-height: 100px;
  padding: 1rem;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  background: rgba(0, 0, 0, 0.2);
  color: inherit;
  font-family: inherit;
  resize: vertical;
  transition: border-color 0.3s, box-shadow 0.3s;
}

textarea:focus {
  outline: none;
  border-color: #646cff;
  box-shadow: 0 0 0 2px rgba(100, 108, 255, 0.2);
}

.color-controls {
  display: flex;
  gap: 1rem;
}

.color-controls .input-group {
  flex: 1;
}

.color-picker-wrapper {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.2);
  padding: 0.25rem 0.75rem 0.25rem 0.25rem;
  border-radius: 8px;
}

.color-picker-wrapper input[type="color"] {
  appearance: none;
  -webkit-appearance: none;
  border: none;
  width: 32px;
  height: 32px;
  border-radius: 4px;
  cursor: pointer;
  padding: 0;
  background: none;
}

.color-picker-wrapper input[type="color"]::-webkit-color-swatch-wrapper {
  padding: 0;
}

.color-picker-wrapper input[type="color"]::-webkit-color-swatch {
  border: none;
  border-radius: 4px;
}

.file-input-hidden {
  display: none;
}

.logo-upload-wrapper {
  display: flex;
  gap: 0.5rem;
}

.preview {
  align-items: center;
  justify-content: center;
}

.canvas-wrapper {
  position: relative;
  background: white;
  padding: 1rem;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  min-height: 332px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.placeholder-text {
  position: absolute;
  color: #888;
  text-align: center;
  font-weight: 500;
  padding: 2rem;
}

canvas {
  display: block;
  max-width: 100%;
  height: auto;
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

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  background: linear-gradient(135deg, #646cff, #8a2be2);
  color: white;
  width: 100%;
}

.btn-primary:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(100, 108, 255, 0.3);
}

.btn-secondary {
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.btn-secondary:hover {
  background: rgba(255, 255, 255, 0.2);
}

.btn-danger-outline {
  background: transparent;
  color: #ff4d4f;
  border: 1px solid #ff4d4f;
}

.btn-danger-outline:hover {
  background: rgba(255, 77, 79, 0.1);
}
</style>
