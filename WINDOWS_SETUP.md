# Bedbug Detector - Windows Setup Guide

This guide walks you through setting up and running the Bedbug Detection System on Windows.

## Prerequisites

Before starting, ensure you have:

1. **Python 3.8+** - Download from https://www.python.org/
   - ✓ Check "Add Python to PATH" during installation
   - Verify: Open Command Prompt and type `python --version`

2. **Node.js 18+** - Download from https://nodejs.org/
   - Verify: Open Command Prompt and type `node --version`

3. **Your trained model file** - `cimex_binary_RGB_V4.keras`
   - This should be placed in: `backend\model\cimex_binary_RGB_V4.keras`

## Quick Start (Automated)

### Step 1: Run Complete Setup

1. Navigate to the project directory
2. Double-click `setup-all.bat`
3. Wait for installation to complete
4. The script will show you next steps

This will:
- ✓ Install all frontend dependencies (Node.js packages)
- ✓ Install all backend dependencies (Python packages)
- ✓ Create Python virtual environment
- ✓ Verify all requirements

### Step 2: Add Your Model File

1. Create folder: `backend\model\`
2. Copy your `cimex_binary_RGB_V4.keras` file into this folder

### Step 3: Start the Servers

**Option A: Run Both Servers (Recommended)**

Open two Command Prompt windows:

**Window 1 - Backend:**
```
run-backend.bat
```

**Window 2 - Frontend:**
```
run-frontend.bat
```

**Option B: Run Individually**

Just run whichever you need:
```
run-frontend.bat    # Frontend only
run-backend.bat     # Backend only
```

### Step 4: Access the Application

Open your browser and go to:
```
http://localhost:3000
```

## Manual Setup (If Automated Script Fails)

### Frontend Setup

1. Open Command Prompt
2. Navigate to project directory:
   ```
   cd path\to\bedbug_detector
   ```

3. Install dependencies:
   ```
   pnpm install
   ```

4. Start development server:
   ```
   pnpm dev
   ```

5. Open browser: `http://localhost:3000`

### Backend Setup

1. Open Command Prompt
2. Navigate to backend directory:
   ```
   cd path\to\bedbug_detector\backend
   ```

3. Create virtual environment:
   ```
   python -m venv venv
   ```

4. Activate virtual environment:
   ```
   venv\Scripts\activate.bat
   ```

5. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

6. Create model directory:
   ```
   mkdir model
   ```

7. Copy your model file to: `backend\model\cimex_binary_RGB_V4.keras`

8. Run the server:
   ```
   python app.py
   ```

9. API available at: `http://localhost:8000`

## Batch Scripts Explained

### `setup-all.bat`
Installs all dependencies for both frontend and backend in one go.

### `install-frontend.bat`
Installs only frontend dependencies (Node.js packages via pnpm).

### `install-backend.bat`
Installs only backend dependencies (Python packages in virtual environment).

### `run-frontend.bat`
Starts the React development server on port 3000.

### `run-backend.bat`
Starts the FastAPI backend server on port 8000.

## Troubleshooting

### "Python is not installed"
- Download Python from https://www.python.org/
- **Important**: Check "Add Python to PATH" during installation
- Restart Command Prompt after installation

### "Node.js is not installed"
- Download Node.js from https://nodejs.org/
- Restart Command Prompt after installation

### "pnpm is not installed"
- The script will try to install it automatically
- If it fails, run manually:
  ```
  npm install -g pnpm
  ```

### Port 3000 already in use
- Another application is using port 3000
- Either close that application or modify `run-frontend.bat`:
  ```
  pnpm dev -- --port 3001
  ```

### Port 8000 already in use
- Another application is using port 8000
- Modify `run-backend.bat` to use a different port:
  ```
  uvicorn app:app --host 0.0.0.0 --port 8001
  ```

### Model file not found
- Create folder: `backend\model\`
- Copy your model file: `cimex_binary_RGB_V4.keras`
- Restart the backend server

### "Failed to install dependencies"
- Try running Command Prompt as Administrator
- Check your internet connection
- Try installing manually (see Manual Setup section)

### Virtual environment won't activate
- Delete the `backend\venv` folder
- Run `install-backend.bat` again

## Project Structure

```
bedbug_detector/
├── client/                    # React frontend
│   ├── src/
│   │   ├── pages/            # Home, Info, Detector pages
│   │   ├── components/       # Navigation component
│   │   ├── App.tsx           # Main router
│   │   └── index.css         # Global styles
│   ├── public/
│   │   └── images/           # Hero images
│   └── index.html
├── backend/                   # FastAPI backend
│   ├── app.py                # Main API
│   ├── Database.py           # Database models
│   ├── testing.py            # Prediction logic
│   ├── requirements.txt      # Python dependencies
│   ├── model/                # Place your model here
│   │   └── cimex_binary_RGB_V4.keras
│   └── venv/                 # Virtual environment (created by script)
├── package.json              # Frontend dependencies
├── setup-all.bat             # Complete setup script
├── install-frontend.bat      # Frontend install script
├── install-backend.bat       # Backend install script
├── run-frontend.bat          # Frontend run script
├── run-backend.bat           # Backend run script
└── DEPLOYMENT_GUIDE.md       # Detailed deployment guide
```

## Accessing the Application

### Frontend
- **URL**: http://localhost:3000
- **Pages**:
  - Home: Main landing page
  - About Bedbugs: Information and prevention tips
  - Detector: Image upload and prediction

### Backend API
- **URL**: http://localhost:8000
- **API Docs**: http://localhost:8000/docs (Interactive Swagger UI)
- **Health Check**: http://localhost:8000/health

## Environment Variables (Optional)

Create a file `backend\.env` to customize settings:

```
MODEL_PATH=model/cimex_binary_RGB_V4.keras
DATABASE_URL=sqlite:///./predictions.db
API_HOST=0.0.0.0
API_PORT=8000
```

## Stopping the Servers

- Press `Ctrl+C` in the Command Prompt window
- Or close the Command Prompt window

## Next Steps

1. ✓ Run `setup-all.bat`
2. ✓ Copy your model file to `backend\model\`
3. ✓ Run `run-backend.bat` in one window
4. ✓ Run `run-frontend.bat` in another window
5. ✓ Open http://localhost:3000 in your browser
6. ✓ Test the detector with sample images

## Support

For more detailed information, see:
- `DEPLOYMENT_GUIDE.md` - Complete deployment guide
- `backend/README.md` - Backend documentation
- Frontend code comments in `client/src/`

## Common Tasks

### Change Frontend Port
Edit `run-frontend.bat`:
```
pnpm dev -- --port 3001
```

### Change Backend Port
Edit `run-backend.bat`:
```
python -m uvicorn app:app --host 0.0.0.0 --port 8001
```

### Reinstall Dependencies
```
# Frontend
rmdir /s /q node_modules
pnpm install

# Backend
rmdir /s /q backend\venv
install-backend.bat
```

### View Backend Logs
Logs are printed to the console where you run `run-backend.bat`

### Clear Database
Delete `backend\predictions.db` and restart the backend server

## Performance Tips

- Use GPU acceleration if available (install `tensorflow-gpu`)
- Close unnecessary applications to free up memory
- Use high-quality images for better predictions
- Keep model file in the specified location

---

**Ready to start?** Run `setup-all.bat` now!
