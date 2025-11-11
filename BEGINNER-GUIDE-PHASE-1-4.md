# 🎓 Synthea - Complete Beginner's Guide
## From Student to Senior Developer in One Project

**Your Mentor**: A software engineer with 70+ years of collective industry experience  
**Your Goal**: Build a production-ready voice-to-code AI assistant  
**Your Path**: Follow these steps exactly, and you'll learn professional software development

---

## 📋 **PHASE 0: Prerequisites Setup (Day 1 - 2 hours)**

Before we write any code, let's set up your development environment properly. This is what professionals do first.

### Step 0.1: Install Required Software

#### **Windows Users:**

```powershell
# 1. Install Python 3.11
# Download from: https://www.python.org/downloads/
# ✅ CHECK: "Add Python to PATH" during installation

# Verify installation
python --version  # Should show Python 3.11.x

# 2. Install Git
# Download from: https://git-scm.com/download/win
# Use default settings during installation

# Verify
git --version  # Should show git version 2.x

# 3. Install Visual Studio Code (Your IDE)
# Download from: https://code.visualstudio.com/
# Install these extensions:
# - Python (Microsoft)
# - Pylance
# - Docker (Microsoft)
# - GitLens

# 4. Install Docker Desktop
# Download from: https://www.docker.com/products/docker-desktop/
# Requires WSL2 - installer will guide you
```

#### **Mac Users:**

```bash
# 1. Install Homebrew (package manager)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Install Python 3.11
brew install python@3.11

# Verify
python3 --version

# 3. Install Git
brew install git

# 4. Install Visual Studio Code
brew install --cask visual-studio-code

# 5. Install Docker Desktop
brew install --cask docker
```

#### **Linux Users:**

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install python3.11 python3.11-venv python3-pip git

# Install VS Code
sudo snap install code --classic

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

### Step 0.2: Verify Your Environment

```bash
# Run these commands - ALL should work:
python --version      # or python3 --version
git --version
docker --version
code --version       # VS Code

# If any fail, reinstall that tool
```

**✅ CHECKPOINT**: All commands work? → Proceed to Step 1  
**❌ ERROR?** → Google the error message, fix it before continuing

---

## 📁 **PHASE 1: Project Setup (Day 1 - 1 hour)**

Now let's create your project structure like a professional.

### Step 1.1: Create Project Directory

```bash
# Open Terminal (Mac/Linux) or PowerShell (Windows)

# Navigate to where you want your project
cd ~/Desktop  # or cd C:\Users\YourName\Desktop on Windows

# Create project folder
mkdir synthea-voice-assistant
cd synthea-voice-assistant

# Open in VS Code
code .
```

**📝 What just happened?**
- You created a dedicated folder for your project
- You opened it in VS Code (your coding workspace)

### Step 1.2: Initialize Git Repository

```bash
# In VS Code terminal (View → Terminal or Ctrl+`)
git init

# Create .gitignore file
# In VS Code: Click "New File" → name it ".gitignore"
# Paste this content:
```

**File: `.gitignore`**
```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
.venv

# Environment variables
.env
.env.local

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Logs
*.log

# Database
*.db
*.sqlite3

# Docker
*.pid
*.seed
*.pid.lock
```

**📝 Why?** This tells Git to ignore files we don't want to track (like passwords, temporary files)

### Step 1.3: Create Project Structure

```bash
# In VS Code terminal, create all folders at once:

# For Mac/Linux:
mkdir -p backend/{app/{api/v1/endpoints,core,services,models,db},tests,alembic/versions}
mkdir -p frontend/{src/{components,hooks,services,types},public}
mkdir -p docs
mkdir -p .github/workflows

# For Windows (PowerShell):
New-Item -ItemType Directory -Path backend\app\api\v1\endpoints -Force
New-Item -ItemType Directory -Path backend\app\core -Force
New-Item -ItemType Directory -Path backend\app\services -Force
New-Item -ItemType Directory -Path backend\app\models -Force
New-Item -ItemType Directory -Path backend\app\db -Force
New-Item -ItemType Directory -Path backend\tests -Force
New-Item -ItemType Directory -Path backend\alembic\versions -Force
New-Item -ItemType Directory -Path frontend\src\components -Force
New-Item -ItemType Directory -Path frontend\src\hooks -Force
New-Item -ItemType Directory -Path frontend\src\services -Force
New-Item -ItemType Directory -Path frontend\src\types -Force
New-Item -ItemType Directory -Path frontend\public -Force
New-Item -ItemType Directory -Path docs -Force
New-Item -ItemType Directory -Path .github\workflows -Force
```

**📝 What's this structure?**
```
synthea-voice-assistant/
├── backend/          ← Your Python API server
│   ├── app/          ← Main application code
│   │   ├── api/      ← API endpoints (routes)
│   │   ├── core/     ← Security, config
│   │   ├── services/ ← Business logic (AI, STT)
│   │   ├── models/   ← Database models
│   │   └── db/       ← Database connection
│   ├── tests/        ← Test files
│   └── alembic/      ← Database migrations
├── frontend/         ← React app (we'll build later)
├── docs/             ← Documentation
└── .github/          ← CI/CD automation
```

**✅ CHECKPOINT**: You see this structure in VS Code sidebar? → Continue  
**❌ ERROR?** → Re-run the mkdir commands

---

## 🔐 **PHASE 2: Get FREE API Keys (Day 1 - 15 minutes)**

Let's sign up for the free services. I'll guide you through each one.

### Step 2.1: Groq API Key (LLM for Code Generation)

1. **Go to**: https://console.groq.com
2. **Click**: "Sign Up" (top right)
3. **Sign up with**: Google/GitHub account (easiest)
4. **After login**: Click "API Keys" in left sidebar
5. **Click**: "Create API Key"
6. **Copy**: The key that starts with `gsk_...`
7. **Save it**: Open Notepad, paste it, label it "GROQ_API_KEY"

**📝 What's Groq?** It runs AI models (like GPT) super fast and gives you 14,400 free requests per day!

### Step 2.2: AssemblyAI API Key (Speech-to-Text)

1. **Go to**: https://www.assemblyai.com
2. **Click**: "Get started for free"
3. **Sign up**: With email
4. **After login**: You'll see your dashboard
5. **Find**: "API Key" section (usually visible on main page)
6. **Copy**: Your API key
7. **Save it**: In your Notepad as "ASSEMBLYAI_API_KEY"

**📝 What's AssemblyAI?** Converts your voice to text. Free $50 credits = ~500 hours of audio!

### Step 2.3: Neon PostgreSQL Database

1. **Go to**: https://neon.tech
2. **Click**: "Sign up"
3. **Sign up**: With GitHub (recommended)
4. **After login**: Click "Create Project"
5. **Name it**: "synthea"
6. **Copy**: The connection string (starts with `postgresql://`)
   - It looks like: `postgresql://user:pass@ep-xxx.us-east-2.aws.neon.tech/synthea`
7. **Save it**: In Notepad as "DATABASE_URL"

**📝 What's Neon?** Free PostgreSQL database (10GB) - stores your users, sessions, etc.

### Step 2.4: Upstash Redis Cache

1. **Go to**: https://upstash.com
2. **Click**: "Sign Up"
3. **Sign up**: With GitHub
4. **After login**: Click "Create Database"
5. **Select**: "Redis" → "Free" tier
6. **Name it**: "synthea-cache"
7. **Copy**: The "REST URL" (starts with `redis://`)
8. **Save it**: In Notepad as "REDIS_URL"

**📝 What's Redis?** Super-fast temporary storage (like RAM) - used for caching and sessions.

**✅ CHECKPOINT**: You have 4 API keys in Notepad? → Continue  
**❌ STUCK?** → Take a screenshot of where you're stuck, Google it, or ask for help

---

## 🐍 **PHASE 3: Backend Setup (Day 1-2 - 2 hours)**

Now let's create your Python backend!

### Step 3.1: Create Python Virtual Environment

```bash
# In VS Code terminal, navigate to backend folder
cd backend

# Create virtual environment
python -m venv venv

# Activate it
# On Mac/Linux:
source venv/bin/activate

# On Windows:
.\venv\Scripts\activate

# You should see (venv) in your terminal now
```

**📝 What's a virtual environment?**  
It's an isolated Python installation for this project only. Keeps dependencies separate from other projects.

**💡 PRO TIP**: Always activate venv before installing packages!

### Step 3.2: Create requirements.txt

Create file: `backend/requirements.txt`

```txt
# Web Framework
fastapi==0.115.0
uvicorn[standard]==0.32.0
python-multipart==0.0.12

# Database
sqlalchemy==2.0.36
alembic==1.14.0
psycopg2-binary==2.9.10
asyncpg==0.30.0

# Authentication & Security
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-dotenv==1.0.1

# API & Validation
pydantic==2.10.3
pydantic-settings==2.6.1

# HTTP Client
httpx==0.28.1

# FREE AI Services
groq==0.13.1
assemblyai==0.36.1

# Redis
redis==5.2.1

# Testing
pytest==8.3.4
pytest-asyncio==0.24.0

# Code Quality
black==24.10.0
```

**📝 What are these?**  
These are all the libraries (tools) we need. Like LEGO blocks for building our app.

### Step 3.3: Install Dependencies

```bash
# Make sure (venv) is visible in terminal
# If not, run: source venv/bin/activate (Mac) or .\venv\Scripts\activate (Windows)

# Install everything (takes 2-3 minutes)
pip install -r requirements.txt

# Verify installation
pip list  # Should show all installed packages
```

**⏳ WAIT**: This will take a few minutes. It's downloading and installing all tools.

**❌ ERROR "no module named pip"?**
```bash
python -m ensurepip --upgrade
pip install --upgrade pip
pip install -r requirements.txt
```

### Step 3.4: Create Environment Variables File

Create file: `backend/.env`

```bash
# Copy your API keys from Notepad to here:

# Application
DEBUG=True
SECRET_KEY=your-secret-key-change-this-later

# Groq LLM (paste your key)
GROQ_API_KEY=gsk_your_actual_key_here

# AssemblyAI Speech-to-Text (paste your key)
ASSEMBLYAI_API_KEY=your_actual_assemblyai_key_here

# Neon PostgreSQL (paste your connection string)
DATABASE_URL=postgresql://user:pass@ep-xxx.neon.tech/synthea?sslmode=require

# Upstash Redis (paste your URL)
REDIS_URL=redis://default:pass@xxx.upstash.io:6379

# CORS (keep as is for now)
ALLOWED_ORIGINS=http://localhost:3000,http://localhost:5173
```

**⚠️ IMPORTANT**: Replace `your_actual_key_here` with REAL keys from Step 2!

**📝 What's .env?**  
Stores secret keys. NEVER commit this to GitHub (that's why it's in .gitignore)

**✅ CHECKPOINT**: .env file has all 4 real API keys? → Continue

---

## 💻 **PHASE 4: Write Your First Code (Day 2 - 3 hours)**

Now the exciting part - writing code! I'll explain every line.

### Step 4.1: Configuration File

Create file: `backend/app/__init__.py` (empty file - just create it)

Create file: `backend/app/config.py`

```python
"""
Configuration management for Synthea
This file reads environment variables and makes them available to our app
"""

from pydantic_settings import BaseSettings
import os

class Settings(BaseSettings):
    """
    Application settings
    
    Think of this like a settings panel in a video game.
    All configurable values go here.
    """
    
    # Application Info
    APP_NAME: str = "Synthea Voice Assistant"
    APP_VERSION: str = "1.0.0"
    DEBUG: bool = True  # Turn off in production
    API_V1_PREFIX: str = "/api/v1"  # All API routes start with this
    
    # Security
    SECRET_KEY: str = os.getenv("SECRET_KEY", "CHANGE-THIS")
    ALGORITHM: str = "HS256"  # JWT encryption algorithm
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60  # Tokens expire after 1 hour
    
    # Database (reads from .env file)
    DATABASE_URL: str = os.getenv("DATABASE_URL", "")
    
    # Redis Cache
    REDIS_URL: str = os.getenv("REDIS_URL", "")
    
    # Groq LLM API
    GROQ_API_KEY: str = os.getenv("GROQ_API_KEY", "")
    GROQ_MODEL: str = "mixtral-8x7b-32768"  # Fast and smart model
    
    # AssemblyAI Speech-to-Text
    ASSEMBLYAI_API_KEY: str = os.getenv("ASSEMBLYAI_API_KEY", "")
    
    # CORS (which websites can access our API)
    ALLOWED_ORIGINS: list = [
        "http://localhost:3000",  # React dev server
        "http://localhost:5173",  # Vite dev server
    ]
    
    class Config:
        env_file = ".env"  # Load from .env file
        case_sensitive = True

# Create a global settings object
settings = Settings()
```

**📝 Line-by-line explanation:**
- `BaseSettings`: Pydantic's magic class that loads .env automatically
- `os.getenv()`: Gets value from .env file
- `settings = Settings()`: Creates one instance we'll use everywhere

**💡 WHY?** Keeps all settings in one place. Change here, affects everywhere.

### Step 4.2: Main Application File

Create file: `backend/app/main.py`

```python
"""
Main FastAPI Application
This is the entry point - where everything starts
"""

from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.config import settings

# Create the FastAPI app
# Think of this as creating your web server
app = FastAPI(
    title=settings.APP_NAME,
    version=settings.APP_VERSION,
    docs_url=f"{settings.API_V1_PREFIX}/docs",  # Interactive docs URL
    openapi_url=f"{settings.API_V1_PREFIX}/openapi.json"
)

# Add CORS middleware (allows browsers to access your API)
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,  # Which websites can call us
    allow_credentials=True,  # Allow cookies
    allow_methods=["*"],  # Allow all HTTP methods (GET, POST, etc.)
    allow_headers=["*"],  # Allow all headers
)

# Root endpoint - test if server is running
@app.get("/")
async def root():
    """
    Root endpoint
    Visit http://localhost:8000 to see this
    """
    return {
        "message": "🎤 Welcome to Synthea Voice-to-Code API!",
        "version": settings.APP_VERSION,
        "docs": f"{settings.API_V1_PREFIX}/docs",  # Where to find API docs
        "status": "running"
    }

# Health check endpoint (required for deployment platforms)
@app.get("/health")
async def health_check():
    """
    Health check - tells if server is alive
    Deployment platforms check this to know if app is working
    """
    return {
        "status": "healthy",
        "service": "synthea-api"
    }

# Startup event (runs when server starts)
@app.on_event("startup")
async def startup_event():
    """Runs once when server starts"""
    print("=" * 60)
    print(f"🚀 {settings.APP_NAME} v{settings.APP_VERSION}")
    print(f"📚 API Docs: http://localhost:8000{settings.API_V1_PREFIX}/docs")
    print(f"💰 Running on 100% FREE services!")
    print("=" * 60)

# Shutdown event (runs when server stops)
@app.on_event("shutdown")
async def shutdown_event():
    """Runs when server stops"""
    print("👋 Shutting down Synthea API...")


# This runs the server when you execute this file
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        "app.main:app",  # app.main = this file, :app = the FastAPI instance
        host="0.0.0.0",  # Listen on all network interfaces
        port=8000,  # Port number
        reload=True  # Auto-restart when code changes (dev only!)
    )
```

**📝 Key concepts:**
- `@app.get("/")`: Decorator - makes function handle GET requests to "/"
- `async def`: Asynchronous function - can handle multiple requests at once
- `return {...}`: Returns JSON response to caller

### Step 4.3: Test Your Server! 🎉

```bash
# In terminal (with venv activated), from backend folder:
python app/main.py

# You should see:
# ========================================
# 🚀 Synthea Voice Assistant v1.0.0
# 📚 API Docs: http://localhost:8000/api/v1/docs
# ========================================
# INFO:     Uvicorn running on http://0.0.0.0:8000
```

**🌐 Open browser:**
1. Go to: http://localhost:8000
   - You should see: `{"message": "🎤 Welcome to Synthea...`
   
2. Go to: http://localhost:8000/api/v1/docs
   - You should see: Interactive API documentation!

**🎉 CONGRATULATIONS!** Your server is running!

**To stop server**: Press `Ctrl+C` in terminal

**✅ CHECKPOINT**: Server runs and you see the welcome message? → Continue  
**❌ ERROR?** → Read the error message carefully, Google it, fix imports

---

## 🎓 **What You Learned So Far**

1. ✅ Set up professional development environment
2. ✅ Created proper project structure
3. ✅ Signed up for FREE services
4. ✅ Created Python virtual environment
5. ✅ Installed dependencies
6. ✅ Wrote configuration management
7. ✅ Created your first web server
8. ✅ Tested it successfully!

**🏆 Achievement Unlocked**: You're now running a professional web server!

---

## 📝 **Next Steps Preview**

In the next phase, we'll add:
- Authentication (login system)
- AI code generation (the cool part!)
- Voice transcription
- Database integration

**📖 Save your progress:**
```bash
git add .
git commit -m "Initial backend setup with FastAPI"
```

---

## 💡 **Pro Tips from Your Mentor**

1. **Always read error messages** - They tell you exactly what's wrong
2. **Google is your friend** - Even seniors Google errors constantly
3. **Test frequently** - Don't write 100 lines before testing
4. **Commit often** - Save progress after each working feature
5. **Ask questions** - No question is stupid in learning

**Ready for Phase 5?** Let me know, and I'll guide you through adding AI services!
