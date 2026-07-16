# BrainScan AI — Full Stack Flask App

A complete brain tumor detection web application with appointment booking, doctor review system, patient portal, and admin dashboard.

## 🚀 Setup Instructions

### 1. Copy your model file
Place your `brain_model.h5` inside the `models/` folder.

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the app
```bash
python app.py
```
Visit: http://localhost:5000

---

## 🔐 Demo Accounts (auto-created on first run)

| Role    | Email                | Password    |
|---------|----------------------|-------------|
| Admin   | admin@brain.com      | admin123    |
| Doctor  | doctor@brain.com     | doctor123   |
| Patient | patient@brain.com    | patient123  |

---

## 📁 Project Structure

```
brain_tumour/
├── app.py              # Main Flask application & all routes
├── database.py         # SQLAlchemy database models
├── model_utils.py      # Image preprocessing utilities
├── grad_cam.py         # Grad-CAM heatmap generation
├── requirements.txt
├── models/
│   └── brain_model.h5  # ← Place your model here
├── static/
│   ├── css/style.css
│   └── uploads/        # Uploaded scans stored here
└── templates/
    ├── base.html        # Sidebar layout base
    ├── landing.html     # Public landing page
    ├── auth/            # Login & Register
    ├── patient/         # Patient portal templates
    ├── doctor/          # Doctor dashboard templates
    └── admin/           # Admin dashboard templates
```

---

## ✨ Features

### Patient
- Register & login
- Book appointments with doctors (date, time slot, priority)
- Cancel appointments
- Upload MRI scan → get instant AI prediction + Grad-CAM
- View detailed scan report

### Doctor
- Confirm / cancel appointments
- Review AI predictions with Grad-CAM
- Add clinical notes & confirmed diagnosis
- Flagged low-confidence scan alerts

### Admin
- User management (activate / deactivate accounts)
- View all appointments & scans
- Prediction distribution dashboard
- Low-confidence scan monitoring

---

## 🏥 Tumor Classes
- **Glioma** — Most common malignant brain tumor
- **Meningioma** — Tumor on brain membranes
- **Pituitary** — Pituitary gland tumor
- **No Tumor** — Normal MRI scan
