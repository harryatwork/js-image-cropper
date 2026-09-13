# ✂️ JS Image Cropper

A JavaScript image cropping module — drag to select a crop area, resize handles, preview the cropped result, and export as a base64 or blob.

## Usage
```html
<img id="target" src="photo.jpg">
<script src="cropper.js"></script>
<script>
  const cropper = new ImageCropper('#target');
  cropper.getCroppedImage(); // returns base64
</script>
```