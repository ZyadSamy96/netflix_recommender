# 🎬 Netflix Recommender

A two-tier movie-recommendation project with:

* **Backend** — Python 3.11 service (`main.py`) that exposes REST endpoints for generating recommendations.  
* **Frontend** — Streamlit application (`app.py`) that lets users explore top-N recommendations in the browser.

Everything can be spun up locally with a single **Docker Compose** command.

---

## 🌟 Quick Demo

```bash
git clone https://github.com/NourhanNabil/netflix_recommender.git
cd netflix_recommender
docker compose up --build
```

* Visit **http://localhost:8080/docs** for interactive backend API.
* Visit **http://localhost:8501** for the Streamlit UI.

Press <kbd>Ctrl +C</kbd> to stop the stack.

---

## 🗂️ Project Layout

```
netflix_recommender/
│
├── backend/                    # FastAPI/Flask-like service
│   ├── requirements.txt
│   ├── main.py
│   └── ...                     # recommender logic, models, utils
│
├── frontend/                   # Streamlit UI
│   ├── requirements.txt
│   ├── app.py
│   └── ...                     # pages, components, assets
│
├── docker-compose.yml          # orchestrates both services
├── .env.example                # sample environment variables
└── README.md
```

---

## 🐳 Container Setup

### Backend Dockerfile
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 8080
CMD ["python", "main.py"]
```

### Frontend Dockerfile
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8501
CMD ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

### docker-compose.yml
```yaml
services:
  backend:
    build: ./backend
    ports:
      - "8080:8080"
    env_file:
      - .env             # optional runtime vars
  frontend:
    build: ./frontend
    ports:
      - "8501:8501"
    depends_on:
      - backend
```

---

## 🔧 Local Development (without Docker)

1. **Backend**

   ```bash
   cd backend
   python -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
   python main.py         # runs on localhost:8080
   ```

2. **Frontend**

   ```bash
   cd ../frontend
   python -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
   streamlit run app.py   # runs on localhost:8501
   ```

---

## 📡 API Reference (Backend)

| Verb | Endpoint           | Description                          |
|------|--------------------|--------------------------------------|
| GET  | `/health`          | Liveness probe                       |
| GET  | `/recommend/{uid}` | Return recommended titles for user id |
| GET  | `/item/{mid}`      | Return metadata for movie id         |

📖 **Full OpenAPI spec** available at `/docs`.

---

## 🖥️ UI Features (Frontend)

* Upload/enter a **user ID** to view personalised recommendations.  
* Browse **similar movies** for any selected item.  
* Real-time filtering by rating, year, or genre.

---

## 📝 Training & Models

The recommender currently uses:

| Technique           | File / Notebook              | Purpose                              |
|---------------------|------------------------------|--------------------------------------|
| Matrix Factorisation| `backend/models/mf.py`       | Baseline latent-factor CF            |
| AutoEncoder         | `notebooks/SDAE.ipynb`       | Deep embeddings for users & items    |
| Deep Embedded Clust.| `notebooks/IDEC.ipynb`       | Joint clustering + representation    |

Model artefacts are saved in `backend/models/checkpoints/`.  
Swap in your own `.pkl` or `.pt` files and reboot containers.

---

## ⚙️ Environment Variables

Create `.env` (or copy `.env.example`) at the repo root:

```
# example
DB_URL=sqlite:///recommender.db
TOP_N=20
```

docker-compose automatically injects these into the backend.

---

## ▶️ Running Tests

```bash
pytest -q  # inside backend virtual-env
```

---

## 🤝 Contributing

1. Fork the repo & create your feature branch (`git checkout -b feat/AmazingFeature`).
2. Commit your changes (`git commit -m 'feat: add AmazingFeature'`).
3. Push to the branch (`git push origin feat/AmazingFeature`).
4. Open a Pull Request.

---

## 📄 License

Distributed under the **MIT License**.  
See `LICENSE` for more information.

---

