# 🎓 Synthea - Beginner's Guide PHASE 5-7
## Adding AI Services & Building Core Features

**Mentor Note**: You've successfully set up your server! Now we add the "brain" - AI services that generate code.

---

## 🤖 **PHASE 5: Add AI Code Generation (Day 3 - 2 hours)**

### Step 5.1: Create Pydantic Schemas (Data Models)

**📝 What are schemas?**  
They're like forms - they define what data looks like and validate it automatically.

Create file: `backend/app/models/__init__.py` (empty)

Create file: `backend/app/models/schemas.py`

```python
"""
Pydantic Schemas - Data validation models
These define the structure of requests and responses
"""

from pydantic import BaseModel, Field
from typing import Optional
from datetime import datetime
from enum import Enum


# ===== ENUMS (Predefined choices) =====

class ProgrammingLanguage(str, Enum):
    """Supported programming languages"""
    PYTHON = "python"
    C = "c"
    JAVASCRIPT = "javascript"
    JAVA = "java"


class IntentType(str, Enum):
    """Types of user intents"""
    GENERATE = "generate"  # Create new code
    EXPLAIN = "explain"    # Explain existing code
    REFACTOR = "refactor"  # Improve code
    DEBUG = "debug"        # Fix code
    EXECUTE = "execute"    # Run code


# ===== REQUEST/RESPONSE MODELS =====

class CodeGenerationRequest(BaseModel):
    """
    Request model for code generation
    What user sends to our API
    """
    prompt: str = Field(..., description="What code to generate", min_length=5)
    language: ProgrammingLanguage = Field(..., description="Target language")
    context: Optional[str] = Field(None, description="Previous conversation")
    
    class Config:
        # Example for API docs
        json_schema_extra = {
            "example": {
                "prompt": "create a function to calculate fibonacci numbers",
                "language": "python",
                "context": None
            }
        }


class CodeGenerationResponse(BaseModel):
    """
    Response model for code generation
    What our API returns to user
    """
    code: str = Field(..., description="Generated code")
    language: ProgrammingLanguage
    explanation: str = Field(..., description="What the code does")
    tokens_used: int = Field(..., description="AI tokens consumed")
    generation_time: float = Field(..., description="Time taken (seconds)")
    
    class Config:
        json_schema_extra = {
            "example": {
                "code": "def fibonacci(n):\n    if n <= 1:\n        return n\n    return fibonacci(n-1) + fibonacci(n-2)",
                "language": "python",
                "explanation": "This function calculates fibonacci numbers recursively",
                "tokens_used": 150,
                "generation_time": 0.5
            }
        }
```

**📝 Line-by-line:**
- `BaseModel`: Pydantic's base class for validation
- `Field(...)`: Adds validation rules and documentation
- `Enum`: Restricts values to predefined choices (no typos!)
- `Optional[str]`: Can be string or None (null)

**💡 WHY?** Automatic validation! If someone sends wrong data, FastAPI rejects it automatically.

### Step 5.2: Create LLM Service (Groq Integration)

Create file: `backend/app/services/__init__.py` (empty)

Create file: `backend/app/services/llm_service.py`

```python
"""
LLM Service - Groq API Integration
This is the "brain" that generates code
"""

from groq import Groq
import time
from typing import Optional
from app.config import settings


class LLMService:
    """
    Service for AI code generation using Groq
    
    Think of this as your AI assistant that writes code
    """
    
    def __init__(self):
        """Initialize Groq client"""
        # Create Groq client with your API key from .env
        self.client = Groq(api_key=settings.GROQ_API_KEY)
        self.model = settings.GROQ_MODEL  # mixtral-8x7b-32768
        
    async def generate_code(
        self, 
        prompt: str, 
        language: str, 
        context: Optional[str] = None
    ) -> dict:
        """
        Generate code using Groq's LLM
        
        Args:
            prompt: User's request ("create a function to...")
            language: Target programming language
            context: Previous conversation (optional)
            
        Returns:
            dict with generated code and metadata
            
        Example:
            result = await llm.generate_code(
                prompt="create a function to check if number is prime",
                language="python"
            )
            print(result["code"])  # Prints generated code
        """
        start_time = time.time()  # Track how long it takes
        
        # Build system prompt (tells AI how to behave)
        system_prompt = f"""You are an expert {language} programmer.
Generate clean, well-documented, production-ready code.
Follow best practices and include error handling.
Add clear comments explaining the logic.
Only return code, no extra text."""

        # Build user message
        user_message = f"Generate {language} code for: {prompt}"
        
        # Add context if provided (for multi-turn conversations)
        if context:
            user_message += f"\n\nPrevious context:\n{context}"
        
        try:
            # Call Groq API (the magic happens here!)
            response = self.client.chat.completions.create(
                model=self.model,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_message}
                ],
                temperature=0.3,  # Low = more consistent code
                max_tokens=2048,  # Maximum length of response
                top_p=1,
                stream=False  # Get complete response at once
            )
            
            # Extract generated code from response
            generated_code = response.choices[0].message.content
            tokens_used = response.usage.total_tokens
            generation_time = time.time() - start_time
            
            # Return structured result
            return {
                "code": generated_code,
                "tokens_used": tokens_used,
                "generation_time": generation_time,
                "model": self.model
            }
            
        except Exception as e:
            # If something goes wrong, print error and re-raise
            print(f"❌ Groq API Error: {e}")
            raise  # Pass error up to caller
    
    async def explain_code(self, code: str, language: str) -> str:
        """
        Generate explanation of existing code
        
        Args:
            code: Source code to explain
            language: Programming language
            
        Returns:
            Natural language explanation
        """
        system_prompt = """You are a programming teacher.
Explain code clearly and concisely for beginners.
Break down complex logic into simple terms."""

        user_message = f"Explain this {language} code:\n\n```{language}\n{code}\n```"
        
        try:
            response = self.client.chat.completions.create(
                model=self.model,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_message}
                ],
                temperature=0.7,  # Higher = more creative explanations
                max_tokens=1024
            )
            
            return response.choices[0].message.content
            
        except Exception as e:
            print(f"❌ Groq API Error: {e}")
            raise


# Create singleton instance (one instance for entire app)
llm_service = LLMService()
```

**📝 Key concepts:**
- `async def`: Asynchronous function - doesn't block other requests
- `try/except`: Error handling - catches problems gracefully
- `self.client`: Instance variable - belongs to this object
- `time.time()`: Tracks execution time

**💡 WHY singleton?** We create LLM client once and reuse it (efficient!)

### Step 5.3: Create API Endpoint for Code Generation

Create file: `backend/app/api/__init__.py` (empty)  
Create file: `backend/app/api/v1/__init__.py` (empty)  
Create file: `backend/app/api/v1/endpoints/__init__.py` (empty)

Create file: `backend/app/api/v1/endpoints/code.py`

```python
"""
Code Generation API Endpoints
These are the routes users call to generate code
"""

from fastapi import APIRouter, HTTPException
from app.models.schemas import (
    CodeGenerationRequest,
    CodeGenerationResponse
)
from app.services.llm_service import llm_service

# Create router (collection of related endpoints)
router = APIRouter()


@router.post("/generate", response_model=CodeGenerationResponse)
async def generate_code(request: CodeGenerationRequest):
    """
    Generate code from natural language prompt
    
    This endpoint receives a request like:
    {
        "prompt": "create a fibonacci function",
        "language": "python"
    }
    
    And returns generated code
    """
    try:
        # Call LLM service to generate code
        code_result = await llm_service.generate_code(
            prompt=request.prompt,
            language=request.language.value,  # .value gets string from enum
            context=request.context
        )
        
        # Generate explanation of the code
        explanation = await llm_service.explain_code(
            code=code_result["code"],
            language=request.language.value
        )
        
        # Return structured response (FastAPI auto-converts to JSON)
        return CodeGenerationResponse(
            code=code_result["code"],
            language=request.language,
            explanation=explanation,
            tokens_used=code_result["tokens_used"],
            generation_time=code_result["generation_time"]
        )
    
    except Exception as e:
        # If anything goes wrong, return 500 error with details
        raise HTTPException(
            status_code=500, 
            detail=f"Code generation failed: {str(e)}"
        )


@router.post("/explain")
async def explain_code(code: str, language: str):
    """
    Explain existing code in natural language
    
    Usage:
    POST /api/v1/code/explain
    {
        "code": "def fib(n): return n if n <= 1 else fib(n-1) + fib(n-2)",
        "language": "python"
    }
    """
    try:
        explanation = await llm_service.explain_code(code, language)
        return {
            "explanation": explanation,
            "language": language,
            "code": code
        }
    
    except Exception as e:
        raise HTTPException(
            status_code=500,
            detail=f"Code explanation failed: {str(e)}"
        )
```

**📝 What's happening:**
1. `@router.post("/generate")`: Handles POST requests to `/generate`
2. `request: CodeGenerationRequest`: FastAPI auto-validates input
3. `await llm_service.generate_code()`: Calls our AI service
4. `return CodeGenerationResponse(...)`: FastAPI auto-converts to JSON

**💡 Magic!** FastAPI automatically:
- Validates input against schema
- Rejects invalid requests
- Converts Python objects to JSON
- Generates API documentation

### Step 5.4: Connect Router to Main App

Update file: `backend/app/main.py`

Add these imports at the top:
```python
from app.api.v1.endpoints import code
```

Add this BEFORE the `if __name__ == "__main__"` block:
```python
# Include code generation router
app.include_router(
    code.router, 
    prefix=f"{settings.API_V1_PREFIX}/code",  # /api/v1/code
    tags=["Code Generation"]  # Groups in API docs
)
```

**Your complete main.py should now look like this:**

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.config import settings
from app.api.v1.endpoints import code  # NEW

app = FastAPI(
    title=settings.APP_NAME,
    version=settings.APP_VERSION,
    docs_url=f"{settings.API_V1_PREFIX}/docs",
    openapi_url=f"{settings.API_V1_PREFIX}/openapi.json"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include routers (NEW)
app.include_router(
    code.router, 
    prefix=f"{settings.API_V1_PREFIX}/code",
    tags=["Code Generation"]
)

@app.get("/")
async def root():
    return {
        "message": "🎤 Welcome to Synthea Voice-to-Code API!",
        "version": settings.APP_VERSION,
        "docs": f"{settings.API_V1_PREFIX}/docs",
        "status": "running"
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy", "service": "synthea-api"}

@app.on_event("startup")
async def startup_event():
    print("=" * 60)
    print(f"🚀 {settings.APP_NAME} v{settings.APP_VERSION}")
    print(f"📚 API Docs: http://localhost:8000{settings.API_V1_PREFIX}/docs")
    print(f"💰 Running on 100% FREE services!")
    print("=" * 60)

@app.on_event("shutdown")
async def shutdown_event():
    print("👋 Shutting down Synthea API...")

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        "app.main:app",
        host="0.0.0.0",
        port=8000,
        reload=True
    )
```

### Step 5.5: Test AI Code Generation! 🎉

```bash
# Make sure .env has your GROQ_API_KEY
# Start server (from backend folder with venv activated)
python app/main.py
```

**🌐 Open browser:**

1. Go to: http://localhost:8000/api/v1/docs
2. Find "Code Generation" section
3. Click "POST /api/v1/code/generate"
4. Click "Try it out"
5. Modify the request body:

```json
{
  "prompt": "create a function to check if a number is prime",
  "language": "python",
  "context": null
}
```

6. Click "Execute"

**You should see generated code! 🎉**

**🐛 Troubleshooting:**

**Error: "GROQ_API_KEY not found"**
```bash
# Check .env file has real key
cat .env | grep GROQ_API_KEY

# If missing, add it:
echo "GROQ_API_KEY=gsk_your_key_here" >> .env
```

**Error: "Module not found"**
```bash
# Make sure you're in backend folder
pwd  # Should show .../synthea-voice-assistant/backend

# Make sure venv is activated
# You should see (venv) in terminal

# Reinstall if needed
pip install -r requirements.txt
```

**Error: "Connection refused"**
- Your Groq API key might be invalid
- Check internet connection
- Verify key at: https://console.groq.com

**✅ CHECKPOINT**: Generated code successfully? → Continue!

---

## 🎓 **What You Just Built**

1. ✅ Data validation with Pydantic
2. ✅ AI service integration (Groq)
3. ✅ RESTful API endpoint
4. ✅ Error handling
5. ✅ Automatic API documentation

**🏆 Achievement**: You're using AI to generate code with your own API!

---

## 🧪 **PHASE 6: Test Your API (Day 3 - 30 min)**

### Test with curl (Command Line)

```bash
# In a NEW terminal (keep server running in other terminal)

# Test code generation
curl -X POST "http://localhost:8000/api/v1/code/generate" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "create a function to reverse a string",
    "language": "python"
  }'

# You should see JSON response with generated code!
```

### Test with Python Script

Create file: `backend/test_api.py`

```python
"""
Simple script to test our API
Run this while server is running
"""

import requests
import json

# Base URL of our API
BASE_URL = "http://localhost:8000/api/v1"

def test_code_generation():
    """Test code generation endpoint"""
    print("🧪 Testing Code Generation...")
    
    # Prepare request
    url = f"{BASE_URL}/code/generate"
    payload = {
        "prompt": "create a function to calculate factorial",
        "language": "python"
    }
    
    # Send request
    response = requests.post(url, json=payload)
    
    # Check response
    if response.status_code == 200:
        result = response.json()
        print("✅ Success!")
        print(f"\n📝 Generated Code:\n{result['code']}")
        print(f"\n💡 Explanation:\n{result['explanation']}")
        print(f"\n📊 Tokens used: {result['tokens_used']}")
        print(f"⏱️  Time: {result['generation_time']:.2f}s")
    else:
        print(f"❌ Failed: {response.status_code}")
        print(response.text)

def test_code_explanation():
    """Test code explanation endpoint"""
    print("\n🧪 Testing Code Explanation...")
    
    url = f"{BASE_URL}/code/explain"
    params = {
        "code": "def factorial(n):\n    return 1 if n <= 1 else n * factorial(n-1)",
        "language": "python"
    }
    
    response = requests.post(url, params=params)
    
    if response.status_code == 200:
        result = response.json()
        print("✅ Success!")
        print(f"\n💡 Explanation:\n{result['explanation']}")
    else:
        print(f"❌ Failed: {response.status_code}")
        print(response.text)

if __name__ == "__main__":
    print("=" * 60)
    print("🚀 Testing Synthea API")
    print("=" * 60)
    
    # Run tests
    test_code_generation()
    test_code_explanation()
    
    print("\n" + "=" * 60)
    print("✅ All tests completed!")
    print("=" * 60)
```

**Run it:**
```bash
# Install requests if needed
pip install requests

# Run test
python test_api.py
```

**✅ CHECKPOINT**: Both tests pass? → You're crushing it! 🎉

---

## 💾 **Save Your Progress**

```bash
# In terminal (from project root)
git add .
git commit -m "Add AI code generation with Groq integration"
git log --oneline  # See your commits
```

---

## 📊 **Your Progress**

**Completed:**
- ✅ Phase 0: Environment setup
- ✅ Phase 1: Project structure
- ✅ Phase 2: API keys setup
- ✅ Phase 3: Backend setup
- ✅ Phase 4: First server
- ✅ Phase 5: AI integration
- ✅ Phase 6: Testing

**Next:**
- ⏳ Phase 7: Voice transcription
- ⏳ Phase 8: Database integration
- ⏳ Phase 9: Frontend
- ⏳ Phase 10: Deployment

---

## 🎓 **Learning Checkpoints**

**You now understand:**
1. ✅ How REST APIs work
2. ✅ Request/Response models
3. ✅ Asynchronous programming
4. ✅ AI API integration
5. ✅ Error handling
6. ✅ API testing

**Professional skills gained:**
- Writing clean, documented code
- Structuring a real application
- Using industry-standard tools
- Testing your work

---

**Ready for Phase 7 (Voice Transcription)?** Say "continue" and I'll guide you through adding speech-to-text! 🎤
