# HAR File JavaScript Extractor

## Prerequisites
- Google Chrome or any Chromium-based browser
- Python 3.x installed
- Basic knowledge of browser DevTools

---

## Step 1: Open DevTools Network Tab

1. Open **Google Chrome**
2. Navigate to your target website (e.g., `filename.com`)
3. Press `F12` or `Ctrl + Shift + I` (Windows/Linux) or `Cmd + Option + I` (Mac)
4. Click the **"Network"** tab

---

## Step 2: Enable Preserve Log (Optional but Recommended)

> **Why?** Ensures requests are not cleared during page redirects or reloads.

- Locate the **"Preserve log"** checkbox at the top of the Network panel
- ✅ Check it to retain all requests across navigations

---

## Step 3: Reload the Page

- Press `Ctrl + R` (Windows/Linux) or `Cmd + R` (Mac)
- Wait for the page to **fully load**
- Watch the Network tab populate with all requests

---

## Step 4: Download as HAR

1. Right-click anywhere inside the **network requests list**
2. Select **"Save all as HAR with content"**

   **OR**

   Click the **⬇ Export HAR** icon in the Network panel toolbar

3. Save the file as `filename.com.har`
4. Place it in the **same folder** as your Python script

---

## Step 5: Run the following python script

```python
import json
import os
import base64

# Load HAR file
with open("filename.har", "r", encoding="utf-8") as f:
    har = json.load(f)

output_dir = "extracted_js"
os.makedirs(output_dir, exist_ok=True)

entries = har["log"]["entries"]

for entry in entries:
    url = entry["request"]["url"]
    mime = entry["response"]["content"].get("mimeType", "")
    
    # Filter only JS files
    if "javascript" in mime or url.endswith(".js"):
        content = entry["response"]["content"].get("text", "")
        encoding = entry["response"]["content"].get("encoding", "")
        
        if encoding == "base64":
            content = base64.b64decode(content).decode("utf-8", errors="ignore")
        
        # Get filename from URL
        filename = url.split("/")[-1].split("?")[0]
        if not filename.endswith(".js"):
            filename += ".js"
        
        filepath = os.path.join(output_dir, filename)
        
        with open(filepath, "w", encoding="utf-8") as f:
            f.write(content)
            print(f"Saved: {filename}")

print(f"\nDone! Files saved to: {output_dir}/")



