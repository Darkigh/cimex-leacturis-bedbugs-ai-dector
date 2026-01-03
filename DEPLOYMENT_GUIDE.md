# Bedbug Detector - Complete Deployment Guide

This guide covers the complete setup and deployment of the Bedbug Detection System, including both frontend and backend components.

## Project Structure

```
bedbug_detector/
├── client/                 # React + TypeScript frontend
│   ├── src/
│   │   ├── pages/         # Page components (Home, Info, Detector)
│   │   ├── components/    # Reusable components (Navigation)
│   │   ├── App.tsx        # Main router
│   │   └── index.css      # Global styles with clinical precision palette
│   ├── public/
│   │   └── images/        # Hero images
│   └── index.html
├── backend/               # FastAPI backend
│   ├── app.py            # Main FastAPI application
│   ├── Database.py       # SQLAlchemy models
│   ├── testing.py        # Smart prediction logic
│   ├── requirements.txt  # Python dependencies
│   ├── README.md         # Backend documentation
│   └── model/            # Place your trained model here
├── package.json          # Frontend dependencies
└── DEPLOYMENT_GUIDE.md   # This file
```

## Frontend Setup

### Prerequisites
- Node.js 18+ and pnpm

### Installation

1. Navigate to project directory:
```bash
cd bedbug_detector
```

2. Install dependencies:
```bash
pnpm install
```

3. Development server:
```bash
pnpm dev
```

The frontend will be available at `http://localhost:3000`

### Building for Production

```bash
pnpm build
```

This creates an optimized build in the `dist/` directory.

### Environment Variables

Create a `.env.local` file in the project root:

```env
VITE_API_URL=http://localhost:8000
```

For production, update to your backend URL:
```env
VITE_API_URL=https://api.yourdomain.com
```

## Backend Setup

### Prerequisites
- Python 3.8+
- Trained model file: `cimex_binary_RGB_V4.keras`

### Installation

1. Navigate to backend directory:
```bash
cd backend
```

2. Create virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Place your model file:
```bash
mkdir -p model
# Copy your cimex_binary_RGB_V4.keras to model/ directory
```

### Running Locally

```bash
python app.py
```

Or with auto-reload:
```bash
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```

API documentation available at: `http://localhost:8000/docs`

## Full Stack Deployment

### Option 1: Local Development

1. **Terminal 1 - Backend:**
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python app.py
```

2. **Terminal 2 - Frontend:**
```bash
pnpm install
pnpm dev
```

Access the application at `http://localhost:3000`

### Option 2: Docker Deployment

#### Backend Docker Setup

Create `backend/Dockerfile`:
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

Build and run:
```bash
cd backend
docker build -t bedbug-detector-api .
docker run -p 8000:8000 -v $(pwd)/model:/app/model bedbug-detector-api
```

#### Frontend Docker Setup

Create `Dockerfile` in project root:
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm && pnpm install

COPY . .

RUN pnpm build

EXPOSE 3000

CMD ["pnpm", "preview"]
```

Build and run:
```bash
docker build -t bedbug-detector-web .
docker run -p 3000:3000 -e VITE_API_URL=http://localhost:8000 bedbug-detector-web
```

### Option 3: Cloud Deployment

#### Deploy Frontend to Vercel

1. Push code to GitHub
2. Connect repository to Vercel
3. Set environment variable: `VITE_API_URL=https://your-api-domain.com`
4. Deploy

#### Deploy Backend to Railway/Render

1. Create account on Railway or Render
2. Connect GitHub repository
3. Set environment variables:
   - `MODEL_PATH`: Path to model in container
   - `DATABASE_URL`: PostgreSQL or SQLite URL
4. Deploy

#### Using Manus Hosting

This project is already set up for Manus hosting:

1. Click "Publish" button in the Manus UI
2. Configure custom domain if needed
3. Backend can be deployed separately and connected via `VITE_API_URL`

## Configuration

### API Endpoint Configuration

Update the API URL based on your deployment:

**Development:**
```env
VITE_API_URL=http://localhost:8000
```

**Production (same domain):**
```env
VITE_API_URL=/api
```

**Production (different domain):**
```env
VITE_API_URL=https://api.yourdomain.com
```

### CORS Configuration

The backend allows all origins by default. For production, update `backend/app.py`:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://yourdomain.com"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Database Configuration

**SQLite (Default):**
```env
DATABASE_URL=sqlite:///./predictions.db
```

**PostgreSQL:**
```env
DATABASE_URL=postgresql://user:password@localhost/bedbug_db
```

## Performance Optimization

### Frontend Optimization

1. **Image Optimization:**
   - Hero images are already optimized
   - Use WebP format for better compression
   - Implement lazy loading for images

2. **Code Splitting:**
   - Already implemented via Vite
   - Routes are automatically code-split

3. **Caching:**
   - Static assets cached with content hash
   - API responses cached in browser

### Backend Optimization

1. **Model Optimization:**
   - Use TensorFlow Lite for faster inference
   - Implement model quantization
   - Consider GPU acceleration

2. **Database Optimization:**
   - Add indexes on frequently queried columns
   - Implement query caching
   - Clean up old predictions periodically

3. **API Optimization:**
   - Implement request rate limiting
   - Add response compression
   - Use async operations

## Monitoring and Maintenance

### Health Checks

Frontend:
```bash
curl http://localhost:3000
```

Backend:
```bash
curl http://localhost:8000/health
```

### Logs

**Frontend:**
```bash
pnpm dev  # Logs in console
```

**Backend:**
```bash
# Logs printed to console
# For persistent logs, redirect to file:
python app.py > logs/api.log 2>&1
```

### Database Maintenance

Clean up old predictions:
```python
from Database import SessionLocal, Prediction
from datetime import datetime, timedelta

db = SessionLocal()
cutoff_date = datetime.utcnow() - timedelta(days=30)
db.query(Prediction).filter(Prediction.created_at < cutoff_date).delete()
db.commit()
db.close()
```

## Troubleshooting

### Frontend Issues

**Port 3000 already in use:**
```bash
pnpm dev -- --port 3001
```

**API connection errors:**
- Check backend is running
- Verify `VITE_API_URL` is correct
- Check CORS configuration

### Backend Issues

**Model not found:**
```bash
export MODEL_PATH=/path/to/model/cimex_binary_RGB_V4.keras
python app.py
```

**Database locked:**
- Ensure only one instance is running
- Delete `predictions.db` and restart (data will be lost)

**Port 8000 already in use:**
```bash
uvicorn app:app --port 8001
```

## Security Considerations

1. **CORS:** Restrict to known domains in production
2. **Input Validation:** Already implemented for image uploads
3. **Rate Limiting:** Add in production
4. **HTTPS:** Use in production
5. **Database:** Use strong credentials for PostgreSQL
6. **Secrets:** Never commit API keys or credentials

## Scaling

### Horizontal Scaling

1. **Load Balancer:** Use Nginx or cloud provider's load balancer
2. **Multiple Backend Instances:** Run multiple API servers
3. **Shared Database:** Use PostgreSQL instead of SQLite
4. **Cache Layer:** Add Redis for caching

### Vertical Scaling

1. **GPU Acceleration:** Use CUDA for faster inference
2. **Model Optimization:** Quantize or prune model
3. **Database Indexing:** Optimize queries

## Backup and Recovery

### Database Backup

```bash
# SQLite
cp predictions.db predictions.db.backup

# PostgreSQL
pg_dump bedbug_db > backup.sql
```

### Model Backup

```bash
cp model/cimex_binary_RGB_V4.keras model/cimex_binary_RGB_V4.keras.backup
```

## Support and Documentation

- Frontend: See `client/src/pages/` for component documentation
- Backend: See `backend/README.md` for API documentation
- API Docs: `http://localhost:8000/docs` (Swagger UI)

## Version History

- **v1.0.0** (Current): Initial release with React frontend and FastAPI backend

## License

This project is part of the Bedbug Detector system.
