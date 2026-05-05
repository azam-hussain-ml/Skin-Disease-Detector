# 🩺 AI Skin Disease Detector

> A deep learning-powered web application that detects skin diseases from photos using state-of-the-art computer vision models. Built with Python, Hugging Face Transformers, and Gradio.

---

## 📌 Project Overview

Skin diseases affect millions of people worldwide, yet access to dermatologists is limited in many regions. This project uses **Artificial Intelligence** to analyze skin photos and identify potential conditions — providing instant results with confidence scores, severity ratings, and medical advice.

This tool is built for **educational and research purposes** and demonstrates how deep learning can be applied to solve real-world medical problems.

---

## 🎯 Problem Statement

- Over **3 billion** people lack access to basic dermatology care globally
- Skin cancer (melanoma) is the **most common cancer** in the world
- Early detection increases survival rates by over **95%**
- Most people cannot distinguish dangerous skin conditions from harmless ones

**Our Solution:** An AI-powered detector that analyzes any skin photo in seconds and provides an instant preliminary assessment.

---

## ✨ Features

- 📷 **Upload any skin photo** — works with camera photos or saved images
- 🤖 **AI-powered detection** — uses a Vision Transformer (ViT) deep learning model
- 📊 **Confidence scores** — shows how confident the AI is in each prediction
- 🔴🟡🟢 **Severity rating** — categorizes results as High / Medium / Low risk
- 💊 **Medical advice** — gives personalized guidance for each detected condition
- 🏆 **Top 3 predictions** — shows the most likely conditions ranked by probability
- 🌐 **Web interface** — clean, interactive Gradio UI accessible from any browser
- 🔗 **Public shareable link** — demo can be shared with anyone instantly
- ✅ **100% free** — no GPU required, runs on free Google Colab CPU

---

## 🧠 Detectable Conditions

| Condition | Category | Severity |
|-----------|----------|----------|
| Melanoma | Skin Cancer | 🔴 High |
| Basal Cell Carcinoma | Skin Cancer | 🔴 High |
| Squamous Cell Carcinoma | Skin Cancer | 🔴 High |
| Actinic Keratosis | Pre-cancerous | 🟡 Medium |
| Vascular Lesion | Vascular | 🟡 Medium |
| Benign Keratosis | Benign | 🟢 Low |
| Dermatofibroma | Benign | 🟢 Low |
| Melanocytic Nevi (Moles) | Benign | 🟢 Low |

---

## 🛠️ Tech Stack

| Technology | Purpose |
|------------|---------|
| Python 3.x | Core programming language |
| Hugging Face Transformers | Pre-trained deep learning models |
| Google ViT (Vision Transformer) | Image classification backbone |
| Gradio | Interactive web UI |
| PyTorch | Deep learning framework |
| Pillow (PIL) | Image preprocessing |

---

## 🚀 How to Run (Google Colab)

### Step 1 — Open Google Colab
Go to [colab.research.google.com](https://colab.research.google.com) and create a **New Notebook**.

### Step 2 — Install Libraries (Cell 1)

```python
!pip install gradio transformers Pillow torch torchvision -q
print("✅ All libraries installed successfully!")
```

### Step 3 — Run the Full App (Cell 2)

```python
from transformers import pipeline
from PIL import Image
import gradio as gr

print("⏳ Loading model... please wait 1-2 minutes...")

classifier = pipeline(
    "image-classification",
    model="google/vit-base-patch16-224",
    top_k=3
)

print("✅ Model loaded! Launching app now...")

DISEASE_INFO = {
    "melanoma": {
        "severity": "🔴 HIGH RISK",
        "description": "A serious form of skin cancer from pigment-producing cells.",
        "advice": "URGENT: See a dermatologist immediately. Early detection is critical."
    },
    "basal cell carcinoma": {
        "severity": "🔴 HIGH RISK",
        "description": "Most common skin cancer. Grows slowly but needs treatment.",
        "advice": "See a doctor soon. Usually very treatable if caught early."
    },
    "squamous cell carcinoma": {
        "severity": "🔴 HIGH RISK",
        "description": "Second most common skin cancer. Can spread if untreated.",
        "advice": "Consult a dermatologist as soon as possible."
    },
    "actinic keratosis": {
        "severity": "🟡 MEDIUM RISK",
        "description": "Rough scaly patch from sun damage. Pre-cancerous condition.",
        "advice": "See a dermatologist for treatment options."
    },
    "benign keratosis": {
        "severity": "🟢 LOW RISK",
        "description": "Non-cancerous skin growth. Very common and harmless.",
        "advice": "No treatment needed generally. Monitor for changes."
    },
    "dermatofibroma": {
        "severity": "🟢 LOW RISK",
        "description": "Harmless fibrous nodule, usually on legs.",
        "advice": "No treatment required unless it causes discomfort."
    },
    "melanocytic nevi": {
        "severity": "🟢 LOW RISK",
        "description": "Common mole — usually completely harmless.",
        "advice": "Monitor for changes in size, shape, or color (ABCDE rule)."
    },
    "vascular lesion": {
        "severity": "🟡 MEDIUM RISK",
        "description": "Abnormality of blood vessels in the skin.",
        "advice": "Consult a doctor for proper diagnosis and treatment."
    }
}

def get_disease_info(label):
    label_clean = label.lower().replace("_", " ").replace("-", " ")
    for key in DISEASE_INFO:
        if key in label_clean or label_clean in key:
            return DISEASE_INFO[key]
    return {
        "severity": "🟡 MEDIUM RISK",
        "description": "A skin condition identified by the AI model.",
        "advice": "Please consult a qualified dermatologist for professional diagnosis."
    }

def format_label(label):
    return label.replace("_", " ").replace("-", " ").title()

def analyze_skin(image):
    if image is None:
        return "⚠️ Please upload a skin image first."
    results = classifier(image)
    top = results[0]
    top_label = format_label(top['label'])
    top_score = round(top['score'] * 100, 1)
    info = get_disease_info(top['label'])

    report = f"""
╔══════════════════════════════════════════╗
        SKIN ANALYSIS REPORT
╚══════════════════════════════════════════╝

🏆  PRIMARY DETECTION:
    {top_label}
    Confidence : {top_score}%

⚠️   SEVERITY LEVEL:
    {info['severity']}

📋  DESCRIPTION:
    {info['description']}

💊  MEDICAL ADVICE:
    {info['advice']}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊  OTHER POSSIBILITIES:
"""
    for i, r in enumerate(results[1:], 2):
        lbl = format_label(r['label'])
        score = round(r['score'] * 100, 1)
        report += f"    #{i}  {lbl}  ({score}%)\n"

    report += """
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚕️  DISCLAIMER:
    AI tool for educational purposes only.
    Always consult a certified dermatologist
    for real medical diagnosis & treatment.
"""
    return report

with gr.Blocks(title="Skin Disease Detector", theme=gr.themes.Soft()) as demo:
    gr.Markdown("""
    # 🩺 AI Skin Disease Detector
    ### Deep Learning Medical AI | Real-World Problem Solver
    Upload a close-up photo of any skin condition — AI analyzes it instantly.
    > ⚕️ *For educational use only — always consult a real doctor.*
    """)
    with gr.Row():
        with gr.Column():
            image_input = gr.Image(type="pil", label="📷 Upload Skin Photo", height=300)
            analyze_btn = gr.Button("🔍 Analyze Skin Condition", variant="primary", size="lg")
        with gr.Column():
            output_text = gr.Textbox(label="📊 AI Diagnosis Report", lines=22, interactive=False)
    analyze_btn.click(fn=analyze_skin, inputs=image_input, outputs=output_text)

demo.launch(share=True)
```

### Step 4 — Use the App
After running Cell 2, a **public URL** will appear in the output. Open it in any browser to use the app!

---

## 🔄 How It Works
User uploads skin photo
↓
Image preprocessed (resized + normalized)
↓
Vision Transformer (ViT) analyzes pixel patterns
↓
Model outputs top 3 condition predictions
↓
Disease database maps result → severity + advice
↓
Formatted report displayed to user
