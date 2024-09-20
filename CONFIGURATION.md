# Configuration Guide

This document explains how to configure Orion Schema for your environment.

## API Keys Setup

Orion Schema requires three types of API keys:

### 1. Gemini API Key

1. Visit [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Create a new API key
3. Replace `YOUR_GEMINI_API_KEY_HERE` in the following files:
   - `piwot/backend.py`
   - `piwot/minimal_backend.py`
   - `piwot/pages/Evals Settings.py`

### 2. Groq API Key

1. Visit [Groq Console](https://console.groq.com/)
2. Create an account and generate an API key
3. Replace `YOUR_GROQ_API_KEY_HERE` in:
   - `piwot/minimal_backend.py`
   - `piwot/pages/Choose Model.py`
   - `piwot/pages/Evals Settings.py`

### 3. Hugging Face Token

1. Visit [Hugging Face Settings](https://huggingface.co/settings/tokens)
2. Create a new token with read access
3. Replace `YOUR_HUGGINGFACE_TOKEN_HERE` in:
   - `piwot/backend.py`

## Modal Setup

For cloud deployment:

1. Install Modal CLI:
   ```bash
   pip install modal
   ```

2. Authenticate:
   ```bash
   modal token new
   ```

3. Deploy the backend:
   ```bash
   modal deploy piwot/backend.py
   ```

4. Update the API endpoint in `piwot/minimal_frontend.py` with your Modal endpoint

## Environment Variables (Recommended)

Instead of hardcoding API keys, use environment variables:

Create a `.env` file:

```
GEMINI_API_KEY=your_gemini_key
GROQ_API_KEY=your_groq_key
HF_TOKEN=your_huggingface_token
```

Then modify the Python files to read from environment:

```python
import os
from dotenv import load_dotenv

load_dotenv()
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")
```

Install python-dotenv:
```bash
pip install python-dotenv
```

## Testing Configuration

Run this test to verify your setup:

```python
import google.generativeai as genai
from groq import Groq
from huggingface_hub import list_models

# Test Gemini
genai.configure(api_key="YOUR_GEMINI_API_KEY")
model = genai.GenerativeModel("gemini-pro")
response = model.generate_content("Hello")
print("Gemini:", response.text)

# Test Groq
client = Groq(api_key="YOUR_GROQ_API_KEY")
completion = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[{"role": "user", "content": "Hello"}]
)
print("Groq:", completion.choices[0].message.content)

# Test Hugging Face
models = list_models(limit=1)
print("HF:", list(models)[0].modelId)
```

## Security Best Practices

1. **Never commit API keys** to version control
2. **Use .env files** and add them to `.gitignore`
3. **Rotate keys regularly**
4. **Set usage limits** on your API keys
5. **Use different keys** for development and production

## Troubleshooting

### Import Errors
```bash
pip install -r requirements.txt
```

### API Rate Limits
- Implement exponential backoff
- Use the built-in retry logic in the code
- Monitor your API usage

### Modal Deployment Issues
- Ensure you're authenticated: `modal token new`
- Check your Modal quota
- Review Modal logs: `modal logs <app-name>`
