# BrainScan AI — AI-Assisted Brain Tumor Detection & Clinical Workflow Platform

## Abstract

Brain tumor diagnosis conventionally depends on radiologists and neurologists manually inspecting MRI scans — a process that is accurate but slow, and one where subtle early-stage tumors can be missed due to fatigue or case volume. **BrainScan AI** is a full-stack Flask web application that combines a deep-learning MRI classifier with a complete clinical workflow, so that AI-assisted screening is embedded directly into the process of booking an appointment, taking a scan, and receiving a diagnosis — rather than existing as a standalone model. At its core is an **EfficientNetB0-based convolutional neural network**, fine-tuned to classify brain MRI images into four categories: **Glioma, Meningioma, Pituitary tumor, and No Tumor**. Every prediction is paired with a **Grad-CAM heatmap** that visually highlights the regions of the MRI the model relied on, giving clinicians an interpretable basis for trusting — or questioning — the AI's output, and predictions below a 70% confidence threshold are automatically flagged for closer review. Around this model, the platform implements a realistic hospital workflow with four distinct roles — **Patient, Doctor, Lab Staff, and Admin** — covering appointment booking and scheduling, MRI request routing to a lab technician, scan upload and instant AI analysis, doctor review with clinical notes and confirmed diagnosis, prescription issuance, downloadable PDF reports, doctor ratings, and an admin dashboard for user management and system-wide monitoring. The result is a working prototype of how an AI diagnostic aid can be integrated into an end-to-end care pathway rather than used in isolation.

## Key Features

### 🧠 AI Diagnosis Engine
- **EfficientNetB0-based CNN** fine-tuned on MRI images to classify scans into 4 classes: **Glioma**, **Meningioma**, **Pituitary**, **No Tumor**
- **Grad-CAM visual explanations** — a heatmap overlay showing which regions of the MRI most influenced the prediction, generated per scan
- **Confidence scoring** for every prediction, with automatic flagging of low-confidence (<70%) scans for extra clinical scrutiny
- **Downloadable PDF diagnostic reports** (via ReportLab) combining the scan, Grad-CAM overlay, AI prediction, and doctor's confirmed diagnosis

### 👤 Patient Portal
- Register / login
- Book appointments with a doctor (date, time slot, reason, priority) and reschedule/cancel them
- Request-driven MRI flow: upload an MRI scan for AI analysis and get an instant prediction + Grad-CAM
- View a detailed scan report and download it as a PDF
- Rate and leave feedback for the consulting doctor
- View prescriptions issued by doctors

### 🩺 Doctor Portal
- Confirm or cancel patient appointments and manage personal availability
- Conduct consultations and request an MRI scan (routed to the lab)
- Review AI predictions and Grad-CAM overlays, add clinical notes, and confirm the final diagnosis
- Automatic alerts for low-confidence AI predictions needing manual attention
- Issue prescriptions (diagnosis, medicines, instructions, follow-up date)

### 🧪 Lab Portal
- View and manage incoming MRI scan requests from doctors
- Schedule a scan date/time for the patient
- Upload the MRI image on the patient's behalf, triggering the same AI classification + Grad-CAM pipeline, with results routed back to the requesting doctor

### 🛡️ Admin Portal
- User management — activate/deactivate patient, doctor, and lab accounts
- View all appointments and all scans across the system
- Prediction-distribution dashboard (class breakdown across all scans)
- Low-confidence scan monitoring for quality control

### 🔔 Cross-cutting features
- Role-based access control and session-based authentication
- In-app notification system (new scan uploaded, appointment confirmed, rating received, etc.)
- Slot-based appointment scheduling with doctor unavailability calendar

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python, Flask |
| ORM / Database | Flask-SQLAlchemy, SQLite (`brain_tumour.db`) |
| Deep Learning | TensorFlow / Keras, EfficientNetB0 (transfer learning, ImageNet weights) |
| Explainability | Grad-CAM (OpenCV for heatmap overlay) |
| PDF Reports | ReportLab |
| Frontend | Jinja2 templates, HTML/CSS |
| Auth | Werkzeug password hashing, Flask sessions |

## Model Details

The classifier (`models/brain_model.h5`) is built and trained in `CODE/models/Brain_Tumor_MRI_Training (1).ipynb` / `.py`:

- **Backbone:** `EfficientNetB0` pretrained on ImageNet, `include_top=False`, base layers frozen (transfer learning)
- **Head:** `GlobalAveragePooling2D → Dropout(0.3) → Dense(num_classes, softmax)`
- **Input size:** 224×224×3, preprocessed with `efficientnet.preprocess_input`
- **Classes:** `Glioma`, `Meningioma`, `No Tumor`, `Pituitary` (inferred from the training data's directory structure)
- **Training setup:** Adam optimizer, sparse categorical cross-entropy loss, batch size 32, trained for a small number of epochs (8) directly on top of the frozen backbone
- **Dataset split:** `train/`, `val/`, `test/` folders, one subfolder per class
- **Explainability:** Grad-CAM is computed on the backbone's last convolutional feature maps and overlaid on the original MRI using OpenCV's `COLORMAP_JET`

## Project Structure

```
BrainScan-AI-main/
├── CODE/
│   ├── app.py                          # Main Flask app — all routes & business logic
│   ├── database.py                     # SQLAlchemy models (User, Appointment, ScanResult,
│   │                                    #   Notification, MRIRequest, Prescription, etc.)
│   ├── model_utils.py                  # Image loading/preprocessing helpers
│   ├── grad_cam.py                     # Grad-CAM heatmap generation & overlay
│   ├── requirements.txt
│   ├── Brain_Tumor_MRI_Training.ipynb  # Model training notebook
│   ├── models/
│   │   ├── brain_model.h5              # Trained EfficientNetB0 classifier
│   │   └── Brain_Tumor_MRI_Training (1).ipynb / .py
│   ├── static/
│   │   ├── css/style.css
│   │   ├── uploads/                    # Uploaded MRI scans + Grad-CAM overlays
│   │   └── reports/                    # Generated PDF reports
│   └── templates/
│       ├── base.html                   # Sidebar layout
│       ├── landing.html                # Public landing page
│       ├── auth/                       # Login & register
│       ├── patient/                    # Patient portal views
│       ├── doctor/                     # Doctor portal views
│       ├── lab/                        # Lab staff portal views
│       └── admin/                      # Admin dashboard views
└── DOC/
    ├── ER diagram.png                  # Entity-relationship diagram
    └── intro (2).pdf                   # Project introduction/report
```

## Getting Started

### Prerequisites
- Python 3.8+
- pip

### Installation

```bash
# Clone the repository
git clone <repo-url>
cd BrainScan-AI-main/CODE

# (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Model file
Make sure `brain_model.h5` is present inside `CODE/models/`. If missing, retrain it using `Brain_Tumor_MRI_Training.ipynb` against your own brain MRI dataset (organized into `train/`, `val/`, `test/` folders, one subfolder per class), or supply a pretrained `.h5` file with the same 4-class output.

### Run the app

```bash
python app.py
```

Visit **http://localhost:5000** in your browser. The SQLite database (`brain_tumour.db`) and its tables are created automatically on first run.

## Demo Accounts

| Role | Email | Password |
|---|---|---|
| Admin | admin@brain.com | admin123 |
| Doctor | doctor@brain.com | doctor123 |
| Patient | patient@brain.com | patient123 |

> Lab-staff accounts are not seeded by default — create one via registration (or the admin panel) and assign it the `lab` role.

## Typical Workflow

1. **Patient** registers, books an appointment with a doctor, and optionally uploads an MRI directly for AI analysis.
2. **Doctor** reviews the appointment, consults with the patient, and — if a scan hasn't been uploaded yet — can request an MRI, which is routed to the **Lab**.
3. **Lab staff** schedules the scan and uploads the MRI image once captured; the AI pipeline runs automatically and returns a prediction, confidence score, and Grad-CAM heatmap.
4. **Doctor** reviews the AI's prediction and Grad-CAM overlay, adds clinical notes, confirms (or overrides) the diagnosis, and issues a prescription if needed.
5. **Patient** views the full report, downloads it as a PDF, and rates the doctor.
6. **Admin** oversees all users, appointments, and scans, with visibility into prediction distribution and low-confidence cases across the platform.

## Disclaimer

This is a research/educational prototype. The model's predictions are **not a substitute for professional medical diagnosis**. Any real-world clinical use would require a validated, regulatory-approved model, a properly sourced and de-identified dataset, and clinical oversight at every step.

## License

Specify a license (e.g. MIT) if you intend to share this project publicly.
