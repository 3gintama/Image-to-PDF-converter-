# Image-to-PDF-converter-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image to PDF Converter</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #ff6b6b, #ee5a24);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            font-weight: 700;
        }

        .header p {
            font-size: 1.1rem;
            opacity: 0.9;
        }

        .content {
            padding: 40px;
        }

        .upload-area {
            border: 3px dashed #ddd;
            border-radius: 15px;
            padding: 50px 20px;
            text-align: center;
            margin-bottom: 30px;
            background: #fafafa;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .upload-area:hover {
            border-color: #667eea;
            background: #f0f4ff;
        }

        .upload-area.dragover {
            border-color: #667eea;
            background: #f0f4ff;
            transform: scale(1.02);
        }

        .upload-icon {
            font-size: 4rem;
            color: #667eea;
            margin-bottom: 20px;
        }

        .upload-text {
            font-size: 1.2rem;
            color: #666;
            margin-bottom: 10px;
        }

        .upload-subtext {
            color: #999;
            font-size: 0.9rem;
        }

        .file-input {
            display: none;
        }

        .btn {
            background: linear-gradient(135deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 15px 30px;
            border-radius: 25px;
            font-size: 1.1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
        }

        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .image-preview {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 20px;
            margin: 30px 0;
        }

        .preview-item {
            position: relative;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
        }

        .preview-item:hover {
            transform: translateY(-5px);
        }

        .preview-image {
            width: 100%;
            height: 120px;
            object-fit: cover;
        }

        .remove-btn {
            position: absolute;
            top: 5px;
            right: 5px;
            background: rgba(255, 107, 107, 0.9);
            color: white;
            border: none;
            border-radius: 50%;
            width: 25px;
            height: 25px;
            cursor: pointer;
            font-size: 0.8rem;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .controls {
            display: flex;
            gap: 20px;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 30px;
        }

        .progress {
            display: none;
            margin-top: 20px;
        }

        .progress-bar {
            width: 100%;
            height: 10px;
            background: #f0f0f0;
            border-radius: 5px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(135deg, #667eea, #764ba2);
            width: 0%;
            transition: width 0.3s ease;
        }

        .settings {
            background: #f8f9fa;
            border-radius: 15px;
            padding: 25px;
            margin: 20px 0;
        }

        .settings h3 {
            margin-bottom: 15px;
            color: #333;
        }

        .setting-group {
            display: flex;
            align-items: center;
            margin-bottom: 15px;
            gap: 15px;
        }

        .setting-group label {
            min-width: 120px;
            font-weight: 500;
        }

        .setting-group select,
        .setting-group input {
            padding: 8px 12px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 1rem;
        }

        @media (max-width: 600px) {
            .header h1 {
                font-size: 2rem;
            }
            
            .content {
                padding: 20px;
            }
            
            .controls {
                flex-direction: column;
                align-items: center;
            }
            
            .setting-group {
                flex-direction: column;
                align-items: flex-start;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🖼️ Image to PDF</h1>
            <p>Convert your images to PDF in seconds</p>
        </div>
        
        <div class="content">
            <div class="upload-area" id="uploadArea">
                <div class="upload-icon">📁</div>
                <div class="upload-text">Drag & drop images here</div>
                <div class="upload-subtext">or click to browse (JPG, PNG, GIF, WebP)</div>
                <input type="file" id="fileInput" class="file-input" multiple accept="image/*">
            </div>

            <div class="settings">
                <h3>⚙️ PDF Settings</h3>
                <div class="setting-group">
                    <label>Page Size:</label>
                    <select id="pageSize">
                        <option value="a4">A4</option>
                        <option value="letter">Letter</option>
                        <option value="legal">Legal</option>
                        <option value="a3">A3</option>
                    </select>
                </div>
                <div class="setting-group">
                    <label>Orientation:</label>
                    <select id="orientation">
                        <option value="portrait">Portrait</option>
                        <option value="landscape">Landscape</option>
                    </select>
                </div>
                <div class="setting-group">
                    <label>Image Fit:</label>
                    <select id="imageFit">
                        <option value="fit">Fit to page</option>
                        <option value="fill">Fill page</option>
                        <option value="stretch">Stretch to fit</option>
                    </select>
                </div>
                <div class="setting-group">
                    <label>Quality:</label>
                    <select id="quality">
                        <option value="0.8">High</option>
                        <option value="0.6" selected>Medium</option>
                        <option value="0.4">Low</option>
                    </select>
                </div>
            </div>

            <div class="image-preview" id="imagePreview"></div>

            <div class="controls">
                <button class="btn" id="convertBtn" disabled>
                    🔄 Convert to PDF
                </button>
                <button class="btn" id="clearBtn" style="background: linear-gradient(135deg, #ff6b6b, #ee5a24);">
                    🗑️ Clear All
                </button>
            </div>

            <div class="progress" id="progress">
                <div class="progress-bar">
                    <div class="progress-fill" id="progressFill"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        class ImageToPDFConverter {
            constructor() {
                this.images = [];
                this.initializeElements();
                this.bindEvents();
            }

            initializeElements() {
                this.uploadArea = document.getElementById('uploadArea');
                this.fileInput = document.getElementById('fileInput');
                this.imagePreview = document.getElementById('imagePreview');
                this.convertBtn = document.getElementById('convertBtn');
                this.clearBtn = document.getElementById('clearBtn');
                this.progress = document.getElementById('progress');
                this.progressFill = document.getElementById('progressFill');
                
                // Settings
                this.pageSize = document.getElementById('pageSize');
                this.orientation = document.getElementById('orientation');
                this.imageFit = document.getElementById('imageFit');
                this.quality = document.getElementById('quality');
            }

            bindEvents() {
                // Upload area events
                this.uploadArea.addEventListener('click', () => this.fileInput.click());
                this.uploadArea.addEventListener('dragover', this.handleDragOver.bind(this));
                this.uploadArea.addEventListener('dragleave', this.handleDragLeave.bind(this));
                this.uploadArea.addEventListener('drop', this.handleDrop.bind(this));
                
                // File input change
                this.fileInput.addEventListener('change', this.handleFileSelect.bind(this));
                
                // Button events
                this.convertBtn.addEventListener('click', this.convertToPDF.bind(this));
                this.clearBtn.addEventListener('click', this.clearImages.bind(this));
            }

            handleDragOver(e) {
                e.preventDefault();
                this.uploadArea.classList.add('dragover');
            }

            handleDragLeave(e) {
                e.preventDefault();
                this.uploadArea.classList.remove('dragover');
            }

            handleDrop(e) {
                e.preventDefault();
                this.uploadArea.classList.remove('dragover');
                const files = Array.from(e.dataTransfer.files).filter(file => file.type.startsWith('image/'));
                this.processFiles(files);
            }

            handleFileSelect(e) {
                const files = Array.from(e.target.files);
                this.processFiles(files);
            }

            async processFiles(files) {
                for (const file of files) {
                    try {
                        const imageData = await this.fileToDataURL(file);
                        const imageObj = {
                            file: file,
                            dataURL: imageData,
                            name: file.name
                        };
                        this.images.push(imageObj);
                    } catch (error) {
                        console.error('Error processing file:', error);
                    }
                }
                this.updatePreview();
                this.updateConvertButton();
            }

            fileToDataURL(file) {
                return new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.onload = e => resolve(e.target.result);
                    reader.onerror = reject;
                    reader.readAsDataURL(file);
                });
            }

            updatePreview() {
                this.imagePreview.innerHTML = '';
                this.images.forEach((image, index) => {
                    const previewItem = document.createElement('div');
                    previewItem.className = 'preview-item';
                    previewItem.innerHTML = `
                        <img src="${image.dataURL}" alt="${image.name}" class="preview-image">
                        <button class="remove-btn" onclick="converter.removeImage(${index})">×</button>
                    `;
                    this.imagePreview.appendChild(previewItem);
                });
            }

            removeImage(index) {
                this.images.splice(index, 1);
                this.updatePreview();
                this.updateConvertButton();
            }

            updateConvertButton() {
                this.convertBtn.disabled = this.images.length === 0;
            }

            clearImages() {
                this.images = [];
                this.updatePreview();
                this.updateConvertButton();
                this.fileInput.value = '';
            }

            async convertToPDF() {
                if (this.images.length === 0) return;

                this.showProgress();
                this.convertBtn.disabled = true;

                try {
                    const { jsPDF } = window.jspdf;
                    const pageSize = this.pageSize.value;
                    const orientation = this.orientation.value;
                    const imageFit = this.imageFit.value;
                    const quality = parseFloat(this.quality.value);

                    const pdf = new jsPDF({
                        orientation: orientation,
                        unit: 'mm',
                        format: pageSize
                    });

                    const pageWidth = pdf.internal.pageSize.getWidth();
                    const pageHeight = pdf.internal.pageSize.getHeight();

                    for (let i = 0; i < this.images.length; i++) {
                        if (i > 0) {
                            pdf.addPage();
                        }

                        const image = this.images[i];
                        const img = new Image();
                        
                        await new Promise((resolve) => {
                            img.onload = () => {
                                const { width, height, x, y } = this.calculateImageDimensions(
                                    img.width, img.height, pageWidth, pageHeight, imageFit
                                );

                                pdf.addImage(
                                    image.dataURL,
                                    'JPEG',
                                    x, y, width, height,
                                    undefined,
                                    'MEDIUM'
                                );
                                resolve();
                            };
                            img.src = image.dataURL;
                        });

                        // Update progress
                        const progress = ((i + 1) / this.images.length) * 100;
                        this.updateProgress(progress);
                    }

                    // Generate filename with timestamp
                    const timestamp = new Date().toISOString().slice(0, 19).replace(/:/g, '-');
                    const filename = `images-to-pdf-${timestamp}.pdf`;

                    pdf.save(filename);
                    
                    setTimeout(() => {
                        this.hideProgress();
                        this.convertBtn.disabled = false;
                    }, 500);

                } catch (error) {
                    console.error('Error converting to PDF:', error);
                    alert('Error converting images to PDF. Please try again.');
                    this.hideProgress();
                    this.convertBtn.disabled = false;
                }
            }

            calculateImageDimensions(imgWidth, imgHeight, pageWidth, pageHeight, fitMode) {
                const margin = 10; // 10mm margin
                const availableWidth = pageWidth - (margin * 2);
                const availableHeight = pageHeight - (margin * 2);

                let width, height, x, y;

                switch (fitMode) {
                    case 'fill':
                        // Fill the page, may crop image
                        const scaleToFill = Math.max(
                            availableWidth / imgWidth,
                            availableHeight / imgHeight
                        );
                        width = imgWidth * scaleToFill;
                        height = imgHeight * scaleToFill;
                        x = margin + (availableWidth - width) / 2;
                        y = margin + (availableHeight - height) / 2;
                        break;

                    case 'stretch':
                        // Stretch to fill entire page
                        width = availableWidth;
                        height = availableHeight;
                        x = margin;
                        y = margin;
                        break;

                    default: // 'fit'
                        // Fit entire image within page
                        const scaleToFit = Math.min(
                            availableWidth / imgWidth,
                            availableHeight / imgHeight
                        );
                        width = imgWidth * scaleToFit;
                        height = imgHeight * scaleToFit;
                        x = margin + (availableWidth - width) / 2;
                        y = margin + (availableHeight - height) / 2;
                }

                return { width, height, x, y };
            }

            showProgress() {
                this.progress.style.display = 'block';
                this.updateProgress(0);
            }

            updateProgress(percent) {
                this.progressFill.style.width = percent + '%';
            }

            hideProgress() {
                this.progress.style.display = 'none';
            }
        }

        // Initialize the converter
        const converter = new ImageToPDFConverter();
    </script>
</body>
</html>