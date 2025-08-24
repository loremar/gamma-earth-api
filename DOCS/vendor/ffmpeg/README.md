**Using ffmpeg.wasm in GitHub Pages (local copy)**

This project uses @ffmpeg/ffmpeg to build timelapse videos in the browser.
Because GitHub Pages blocks some CDN/WASM fetches, we self-host the core files.

**Folder structure**

DOCS/
  S2DR3_timelapse.html
  vendor/
    ffmpeg/
      ffmpeg.min.js
      ffmpeg-core.js
      ffmpeg-core.wasm
      ffmpeg-core.worker.js
      README.md  ← (this file)

**Setup steps**

Install the npm packages locally (one time):

---
npm install @ffmpeg/ffmpeg@0.12.10 @ffmpeg/core@0.12.10
---

Copy the following files from node_modules into DOCS/vendor/ffmpeg/:

---
node_modules/@ffmpeg/ffmpeg/dist/ffmpeg.min.js
node_modules/@ffmpeg/core/dist/ffmpeg-core.js
node_modules/@ffmpeg/core/dist/ffmpeg-core.wasm
node_modules/@ffmpeg/core/dist/ffmpeg-core.worker.js
---

Example:

---
mkdir -p DOCS/vendor/ffmpeg
cp node_modules/@ffmpeg/ffmpeg/dist/ffmpeg.min.js DOCS/vendor/ffmpeg/
cp node_modules/@ffmpeg/core/dist/ffmpeg-core.* DOCS/vendor/ffmpeg/
---

In your HTML (S2DR3_timelapse.html), load the wrapper locally:

---
<script src="./vendor/ffmpeg/ffmpeg.min.js"></script>
---

In your JS, load the core from the same folder:

---
const FFmpeg = window.FFmpeg;
let ffmpeg = null;

async function getFF() {
  if (ffmpeg) return ffmpeg;
  ffmpeg = new FFmpeg();
  await ffmpeg.load({
    coreURL: "./vendor/ffmpeg/ffmpeg-core.js", // same folder as .wasm + worker
  });
  return ffmpeg;
}
---


**Verify**

After pushing to GitHub Pages:
Open DevTools → Network tab
Trigger timelapse export
You should see ffmpeg-core.js, ffmpeg-core.wasm, and ffmpeg-core.worker.js load from /DOCS/vendor/ffmpeg/
