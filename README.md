<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>PDF to JPEG Converter</title>
  <style>
    :root {
      --accent:#d32f2f;
      --btn:#000;
      --input-bg:#fff6f6;
      --text:#111;
      --muted:#777;
      --sheet:#ffffff;
    }
    * { box-sizing:border-box; }
    body {
      margin:0;
      font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;
      background:#fafafa;
      color:var(--text);
      padding-bottom:60px;
      font-size:14px;
    }
    .header {
      background:var(--accent);
      color:#fff;
      padding:10px 16px;
      font-weight:700;
      font-size:16px;
      text-align:left;
    }
    .card {
      max-width:720px;
      margin:18px auto;
      background:var(--sheet);
      border:1px solid #e6e6e6;
      border-radius:4px;
      overflow:hidden;
      box-shadow:0 1px 4px rgba(0,0,0,0.04);
    }
    .card-body { padding:14px; }
    .row { margin-bottom:12px; }
    label {
      display:block;
      font-size:12px;
      margin-bottom:6px;
      font-weight:600;
      color:var(--muted);
    }
    input[type="file"] {
      width:100%;
      padding:8px 10px;
      font-size:13px;
      border:1px solid #ccc;
      border-radius:1px;
      background:var(--input-bg);
      outline:none;
    }
    input[type="file"]:focus {
      border-color:var(--accent);
      box-shadow:0 0 0 3px rgba(211,47,47,0.06);
    }
    .btn {
      display:block;
      width:100%;
      padding:9px 12px;
      font-size:14px;
      font-weight:700;
      color:#fff;
      background:var(--btn);
      border:none;
      border-radius:1px;
      cursor:pointer;
      margin-top:8px;
    }
    .btn:active { transform:translateY(1px); }
    .result {
      margin-top:10px;
      min-height:100px;
      border:1px solid #cfcfcf;
      border-radius:1px;
      background:#fff;
      padding:10px;
      font-weight:600;
      color:var(--text);
      white-space:pre-wrap;
      font-size:13px;
    }
    .meta { font-size:12px; color:var(--muted); margin-top:6px; }
    .footer {
      position:fixed;
      bottom:0;
      left:0;
      width:100%;
      text-align:center;
      padding:8px 5px;
      background:#f5f5f5;
      border-top:1px solid #ddd;
      font-size:12px;
      color:#555;
    }
    .footer span { font-weight:600; }
    canvas { display:block; margin-bottom:10px; border:1px solid #ccc; }
    a.download-link { display:block; margin-bottom:4px; color:var(--accent); text-decoration:none; font-weight:600; font-size:13px; }

    .h4-style {
      background-color: #FF0000;
      padding: 5px 8px;
      border-radius: 2px;
      color: #ffffff;
      margin-top: 50px;
      font-size:16px;
    }
    .justify-p {
      text-align: justify;
      margin-bottom: 10px;
      text-justify: inter-word;
      line-height: 1.5;
      word-spacing: normal;
      hyphens: auto;
      font-size:13px;
    }
  </style>
</head>
<body>
  <div class="header">PDF to JPEG Converter</div>

  <div class="card">
    <div class="card-body">
      <div class="row">
        <label for="pdfFile">Upload PDF</label>
        <input type="file" id="pdfFile" accept="application/pdf">
      </div>
      <button id="convertBtn" class="btn">Convert to JPEG</button>
      <div id="result" class="result">Upload a PDF and click Convert.</div>
      <div class="meta">
        Tip: Each page of the PDF will be converted to a separate JPEG image with download buttons.
      </div>

      <h4 class="h4-style">About This Tool</h4>
      <p class="justify-p">
        This PDF to JPEG converter allows you to quickly convert all pages of a PDF document into high-quality JPEG images, right from your browser.
      </p>
      <p class="justify-p">
        The conversion happens locally on your device, ensuring that your PDF content remains private and secure without being uploaded to any server.
      </p>
      <p class="justify-p">
        Each page of the PDF is rendered as a separate image, and download links are provided for easy saving. You can reset and convert another PDF anytime.
      </p>
    </div>
  </div>

  <div class="footer">
    &copy; <span>Skill2job</span> | All Rights Reserved
  </div>

  <!-- Local PDF.js library -->
  <script src="pdf.min.js"></script> <!-- Place pdf.min.js in the same folder -->
  <script>
    const pdfFileInput = document.getElementById('pdfFile');
    const convertBtn = document.getElementById('convertBtn');
    const resultDiv = document.getElementById('result');

    async function convertPDFtoJPEG(file) {
      if (!file) return;
      resultDiv.innerHTML = "Processing PDF...";

      const reader = new FileReader();
      reader.onload = async function() {
        const typedarray = new Uint8Array(this.result);
        const pdf = await pdfjsLib.getDocument(typedarray).promise;
        resultDiv.innerHTML = '';

        for (let pageNum = 1; pageNum <= pdf.numPages; pageNum++) {
          const page = await pdf.getPage(pageNum);
          const viewport = page.getViewport({ scale: 2 });
          const canvas = document.createElement('canvas');
          const context = canvas.getContext('2d');
          canvas.width = viewport.width;
          canvas.height = viewport.height;

          await page.render({ canvasContext: context, viewport: viewport }).promise;

          const jpegData = canvas.toDataURL('image/jpeg', 1.0);

          const link = document.createElement('a');
          link.href = jpegData;
          link.download = `page-${pageNum}.jpeg`;
          link.textContent = `Download Page ${pageNum}`;
          link.className = 'download-link';

          resultDiv.appendChild(canvas);
          resultDiv.appendChild(link);
        }
      };
      reader.readAsArrayBuffer(file);
    }

    convertBtn.addEventListener('click', () => {
      const file = pdfFileInput.files[0];
      if (!file) {
        alert("Please select a PDF file first.");
        return;
      }
      convertPDFtoJPEG(file);
    });
  </script>
</body>
</html>
