<div align="center">

# ✂️ JS Image Cropper

**Browser-side image cropper with drag selection, aspect ratio lock, live preview, and Blob output — no libraries, no canvas hacks, no server round-trip.**

[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=flat-square&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Canvas API](https://img.shields.io/badge/Canvas-API-orange?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
[![Zero deps](https://img.shields.io/badge/dependencies-zero-brightgreen?style=flat-square)](.)
[![License](https://img.shields.io/badge/license-MIT-a855f7?style=flat-square)](LICENSE)

</div>

---

## The Problem

Profile photo upload, product image editing, avatar cropping — every user-facing app needs it. Cropper.js is 200KB and requires jQuery. This is a 3KB vanilla implementation: drag to draw a crop box, lock aspect ratio, zoom the preview, and get a `Blob` ready to `FormData` upload. No npm. No jQuery. Nothing.

---

## ✨ Features

- 🖱️ **Drag crop box** — click and drag directly on the image to draw your crop region
- 🔒 **Aspect ratio lock** — configurable fixed ratio (1:1 for avatars, 16:9 for banners, free-form)
- 🔎 **Live preview pane** — real-time preview of the cropped region as you drag
- 🔭 **Zoom slider** — zoom the source image in/out before cropping
- ↕️ **Resize handles** — 8-handle resize on the crop box corners and edges
- 📤 **Blob / base64 output** — `getCroppedBlob()` for FormData upload, `getCroppedDataURL()` for inline use
- 📱 **Touch support** — pinch to zoom and touch drag on mobile devices
- 🎨 **Styleable overlay** — dark scrim outside the crop region, dashed crop border, all overridable

---

## 🔧 How It Works

```
User selects image file
        ↓
FileReader reads file → drawImage() to <canvas>
        ↓
Overlay layer drawn on top: semi-transparent scrim + crop box
        ↓
MouseMove / TouchMove events update crop region coordinates
        ↓
Live preview: drawImage() with source crop rect → small canvas
        ↓
getCroppedBlob(): draw crop rect to offscreen canvas → canvas.toBlob()
        ↓
Blob passed to FormData / uploaded directly
```

The core trick: `canvas.drawImage(img, sx, sy, sw, sh, 0, 0, dw, dh)` — the source rectangle is the crop region, the destination is your output size.

---

## 🚀 Quick Start

### 1 — Add the files

```html
<link rel="stylesheet" href="cropper.css">
<script src="cropper.js" defer></script>
```

### 2 — Add the markup

```html
<!-- File input -->
<input type="file" id="imageInput" accept="image/*">

<!-- Cropper stage (source image renders here) -->
<div id="cropperStage"></div>

<!-- Preview -->
<canvas id="previewCanvas" width="200" height="200"></canvas>

<!-- Output -->
<button id="cropBtn">Crop & Upload</button>
```

### 3 — Initialise

```javascript
const cropper = new ImageCropper('#cropperStage', {
  aspectRatio: 1,          // 1 = square; null = free-form; 16/9 = widescreen
  outputWidth:  400,       // Output canvas pixel width
  outputHeight: 400,       // Output canvas pixel height
  previewCanvas: '#previewCanvas',
});

document.getElementById('imageInput').addEventListener('change', (e) => {
  cropper.loadFile(e.target.files[0]);
});

document.getElementById('cropBtn').addEventListener('click', async () => {
  const blob = await cropper.getCroppedBlob('image/jpeg', 0.92);
  const formData = new FormData();
  formData.append('avatar', blob, 'avatar.jpg');
  await fetch('/upload', { method: 'POST', body: formData });
});
```

---

## 🧑‍💻 API Reference

| Method | Returns | Description |
|---|---|---|
| `new ImageCropper(selector, options)` | `ImageCropper` | Initialises the cropper on a container element |
| `cropper.loadFile(file)` | `void` | Load a `File` object (from `<input type="file">`) |
| `cropper.loadUrl(url)` | `void` | Load an image from a URL (same-origin or CORS-enabled) |
| `cropper.getCroppedBlob(type, quality)` | `Promise<Blob>` | Returns cropped image as a Blob |
| `cropper.getCroppedDataURL(type, quality)` | `string` | Returns cropped image as a base64 data URL |
| `cropper.reset()` | `void` | Resets crop box to full image |
| `cropper.setAspectRatio(ratio)` | `void` | Change aspect ratio dynamically |
| `cropper.destroy()` | `void` | Removes all event listeners and DOM elements |

---

## ⚙️ Options

| Option | Default | Description |
|---|---|---|
| `aspectRatio` | `null` | Lock to a ratio (e.g. `1`, `16/9`, `4/3`) or `null` for free-form |
| `outputWidth` | `400` | Width of the output Blob/DataURL in pixels |
| `outputHeight` | `400` | Height of the output Blob/DataURL in pixels |
| `minCropSize` | `50` | Minimum crop box dimension in px |
| `overlayColor` | `rgba(0,0,0,0.5)` | Scrim color outside the crop box |
| `previewCanvas` | `null` | CSS selector for a live preview `<canvas>` |
| `zoomable` | `true` | Show zoom slider |

---

## 📁 Project Structure

```
js-image-cropper/
├── cropper.js          # Core — ImageCropper class (~3KB minified)
├── cropper.css         # Overlay, handles, preview styles
├── demo/
│   ├── index.html      # Working demo: file upload, crop, download
│   └── avatar-demo.html   # Profile photo crop (1:1 locked)
└── README.md
```

---

<details>
<summary><strong>Common issues and fixes</strong></summary>

| Issue | Fix |
|---|---|
| Preview is blank | Ensure `previewCanvas` selector matches an existing `<canvas>` element |
| CORS error loading URL | The image server must send `Access-Control-Allow-Origin: *`; or use `loadFile()` instead |
| Crop box doesn't maintain ratio | Pass `aspectRatio` as a number, not a string (`1` not `"1:1"`) |
| Output is blurry | Increase `outputWidth` / `outputHeight` to match display size × device pixel ratio |
| Touch drag not working | Ensure `touch-action: none` is set on the cropper stage container |

</details>

---

<div align="center">

Built by [Harish K](https://github.com/harryatwork)

</div>