---
name: jonnify
description: "Transforms an image into Jonny's cartoon drawing style using Gemini image generation. Use when the user wants to stylize, jonnify, or redraw an image in Jonny's style."
allowed-tools: Bash, Read
---

# Jonify

Input image: `$ARGUMENTS`
Style samples: `${CLAUDE_SKILL_DIR}/reference/style-samples/`

Run this script with `python3 -c` to perform the style transfer. Do not modify it.

```python
import sys, os, json, base64, urllib.request
from pathlib import Path

input_image = sys.argv[1]
style_dir = sys.argv[2]
input_stem = Path(input_image).stem
output_path = f"output/{input_stem}-jonnified.png"
Path("output").mkdir(exist_ok=True)

api_key = os.environ["GEMINI_API_KEY"]

def enc(path):
    mime = "image/png" if str(path).endswith(".png") else "image/jpeg"
    return {"inline_data": {"mime_type": mime, "data": base64.b64encode(Path(path).read_bytes()).decode()}}

style_images = sorted(p for p in Path(style_dir).iterdir() if p.suffix.lower() in (".png", ".jpg", ".jpeg", ".webp"))
if not style_images:
    print(f"No style samples in {style_dir}", file=sys.stderr); sys.exit(1)

prompt = """Here are examples of a specific hand-drawn whiteboard illustration style. Study these examples carefully and then redraw the last image in this exact same style.

Key style characteristics to match:
- Simple stick figures with round heads and minimal body detail
- Black ink as primary color, with only purple/lavender, orange, and blue as accents
- Clean white background with occasional soft lavender/purple blobs as highlights
- Wobbly, imperfect hand-drawn lines — like a sketch on a whiteboard
- Icons and concepts in rounded rectangles with colored borders (orange or purple)
- Handwritten-style text labels connected to elements with curved lines
- Small blue dashes or lines as emphasis marks
- Conceptual and diagrammatic — simplify the subject into its core idea, don't make it detailed or realistic """

parts = [{"text": prompt}]
for img in style_images:
    parts.append(enc(str(img)))
parts.append(enc(input_image))

req = urllib.request.Request(
    f"https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-image-preview:generateContent?key={api_key}",
    data=json.dumps({"contents": [{"parts": parts}], "generationConfig": {"responseModalities": ["TEXT", "IMAGE"], "imageConfig": {"imageSize": "2K"}}}).encode(),
    headers={"Content-Type": "application/json"})

resp = json.loads(urllib.request.urlopen(req).read())
for part in resp["candidates"][0]["content"]["parts"]:
    if "inlineData" in part:
        Path(output_path).write_bytes(base64.b64decode(part["inlineData"]["data"]))
        print(f"Saved to {output_path}"); sys.exit(0)
print("No image in response", file=sys.stderr); sys.exit(1)
```

Pass the arguments:
```
python3 -c '<script above>' "$ARGUMENTS" "${CLAUDE_SKILL_DIR}/reference/style-samples/"
```

Do NOT read, echo, log, or search for the GEMINI_API_KEY value. Do NOT look for .env files.

If the script fails with `KeyError: 'GEMINI_API_KEY'`, tell the user to set the environment variable and stop. Do not attempt to find or source the key yourself.

After the script succeeds, tell the user where the output was saved.
