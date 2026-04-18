# MediScan AI — AI-Powered Radiology Web Application

A production-grade AI radiology diagnostic system built with **Flask** (Python 3.12+).

---

## Features

- **YOLOv8 Model Integration** — Brain Tumor (MRI), Kidney Stone (CT), Bone Fracture (X-ray)
- **JWT Authentication** — Register/Login with role-based access (Admin / Doctor / User)
- **AI Chatbot** — Context-aware assistant (rule-based + optional OpenAI GPT)
- **Dashboard** — Total scans, disease distribution charts, recent predictions
- **PDF Report Generation** — Professional reports with patient info and scan images
- **Grad-CAM Heatmaps** — Visual explainability for AI predictions
- **Demo Mode** — Works without model files (shows simulated results)

---

## System Requirements

- Python **3.12.7**
- pip (comes with Python)

---

## Installation & Setup

### 1. Navigate to the project folder

```bash
cd radiology_app
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

### 3. Activate the virtual environment

**Windows:**
```cmd
venv\Scripts\activate
```

**macOS / Linux:**
```bash
source venv/bin/activate
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

> **Note:** `torch` and `ultralytics` are large packages (~2 GB). Installation may take several minutes.
> If you face issues with `torch`, install it separately first:
> ```bash
> pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
> pip install ultralytics
> pip install -r requirements.txt
> ```

### 5. Set up environment variables (optional)

Copy the example env file:
```bash
cp .env.example .env
```

Edit `.env` and set:
- `SECRET_KEY` — A long random string (for Flask session security)
- `JWT_SECRET_KEY` — A long random string (for JWT tokens)
- `OPENAI_API_KEY` — Your OpenAI API key (optional — app works without it)

### 6. Add YOLOv8 Model Files (optional)

Place your model files in the `models/` folder:
```
models/
  braintumorbest.pt   ← Brain Tumor model
  kidneybest.pt       ← Kidney Stone model
  bone.pt             ← Bone Fracture model
```

> **Without model files:** The app runs in **Demo Mode** — predictions are simulated with realistic bounding boxes. This is useful for testing without GPU/model files.

### 7. Run the application

```bash
python run.py
```

Open your browser at: **http://localhost:5000**

---

## Default Login Credentials

| Role   | Username | Password    |
|--------|----------|-------------|
| Admin  | admin    | Admin@123   |
| Doctor | doctor   | Doctor@123  |

You can also register a new account from the Sign In page.

---

## Project Structure

```
radiology_app/
├── app.py                  # Flask app factory
├── run.py                  # Entry point
├── config.py               # Configuration
├── models.py               # SQLAlchemy models (User, Prediction, Report)
├── requirements.txt        # Python dependencies
├── .env.example            # Environment variable template
│
├── routes/
│   ├── auth.py             # Register, Login, /me endpoints
│   ├── predict.py          # Image upload, YOLOv8, stats
│   ├── reports.py          # PDF report generation & download
│   └── chat.py             # AI Chatbot (rule-based + OpenAI)
│
├── utils/
│   ├── model_loader.py     # Loads YOLOv8 models at startup
│   ├── predictor.py        # Runs prediction (real or demo)
│   ├── gradcam.py          # Generates Grad-CAM heatmaps
│   └── pdf_report.py       # Creates PDF reports (ReportLab)
│
├── models/                 # Place .pt model files here
│   ├── braintumorbest.pt
│   ├── kidneybest.pt
│   └── bone.pt
│
├── static/
│   ├── css/style.css       # Full application CSS
│   ├── js/app.js           # Full frontend JavaScript
│   ├── uploads/            # Uploaded & annotated images (auto-created)
│   └── reports/            # Generated PDF reports (auto-created)
│
└── templates/
    └── index.html          # Single-page application template
```

---

## API Endpoints

### Authentication
| Method | Endpoint              | Description              |
|--------|-----------------------|--------------------------|
| POST   | `/api/auth/register`  | Create new account       |
| POST   | `/api/auth/login`     | Login, get JWT token     |
| GET    | `/api/auth/me`        | Get current user info    |

### Prediction
| Method | Endpoint                | Description              |
|--------|-------------------------|--------------------------|
| POST   | `/api/predict/upload`   | Upload image & predict   |
| GET    | `/api/predict/history`  | List scan history        |
| GET    | `/api/predict/<id>`     | Get single prediction    |
| GET    | `/api/predict/stats`    | Dashboard stats          |

### Reports
| Method | Endpoint                      | Description              |
|--------|-------------------------------|--------------------------|
| POST   | `/api/reports/generate/<id>`  | Generate PDF report      |
| GET    | `/api/reports/`               | List all reports         |
| GET    | `/api/reports/download/<id>`  | Download PDF             |

### Chatbot
| Method | Endpoint    | Description              |
|--------|-------------|--------------------------|
| POST   | `/api/chat/` | Send message to AI chat |

---

## Production Deployment

### Using Gunicorn (Linux/macOS)
```bash
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 "app:create_app()"
```

### Using Waitress (Windows)
```bash
pip install waitress
waitress-serve --host=0.0.0.0 --port=5000 "app:create_app()"
```

---

## Troubleshooting

**Q: `torch` installation fails**
```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
```

**Q: `cv2` (OpenCV) import error**
```bash
pip install opencv-python-headless
```

**Q: Port 5000 already in use**
```bash
python run.py --port 8080
# or edit run.py and change port=5000 to port=8080
```

**Q: Models not loading**
> The app will automatically use Demo Mode. No action needed.

---

## License

MIT License — Free to use and modify.
