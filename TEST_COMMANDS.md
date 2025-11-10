# API Test Commands

## Deployed Service URL
https://aiml-engineer-assignment.onrender.com

---

## Quick Tests

### 1. Root Endpoint
```bash
curl https://aiml-engineer-assignment.onrender.com/
```

**Expected Response:**
```json
{
  "service": "Member Data QA Service",
  "endpoints": {
    "ask": "/ask?question=YOUR_QUESTION",
    "health": "/health",
    "stats": "/stats"
  }
}
```

---

### 2. Health Check
```bash
curl https://aiml-engineer-assignment.onrender.com/health
```

**Expected Response:**
```json
{
  "status": "healthy",
  "llm_provider": "groq",
  "groq_configured": true,
  "claude_configured": false,
  "openai_configured": false
}
```

---

### 3. Get Statistics
```bash
curl https://aiml-engineer-assignment.onrender.com/stats
```

**Expected Response:**
```json
{
  "total_messages": 3349,
  "unique_users": 10,
  "users": {
    "Sophia Al-Farsi": 346,
    "Fatima El-Tahir": 349,
    ...
  }
}
```

---

### 4. Ask Questions (GET Method)

**Question 1: How many users?**
```bash
curl "https://aiml-engineer-assignment.onrender.com/ask?question=How%20many%20users%20are%20in%20the%20dataset?"
```

**Question 2: Who are the members?**
```bash
curl "https://aiml-engineer-assignment.onrender.com/ask?question=Who%20are%20the%20members?"
```

**Question 3: Specific user info**
```bash
curl "https://aiml-engineer-assignment.onrender.com/ask?question=What%20is%20Vikram%20Desai's%20user%20ID?"
```

---

### 5. Ask Questions (POST Method)

```bash
curl -X POST https://aiml-engineer-assignment.onrender.com/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "List all user names in the dataset"}'
```

---

## Pretty Print JSON

Add `| python3 -m json.tool` or `| jq` to format output:

```bash
curl -s https://aiml-engineer-assignment.onrender.com/health | python3 -m json.tool
```

---

## Test from Browser

Simply paste these URLs in your browser:

- Root: https://aiml-engineer-assignment.onrender.com/
- Health: https://aiml-engineer-assignment.onrender.com/health
- Stats: https://aiml-engineer-assignment.onrender.com/stats
- Ask: https://aiml-engineer-assignment.onrender.com/ask?question=How%20many%20users%20are%20there?

---

## Current Status

✅ Service deployed successfully
✅ Root endpoint working
✅ Health endpoint working
✅ Stats endpoint working (fetches 3,349 messages)
⚠️ Ask endpoint needs valid Groq API key

---

## Repository
GitHub: https://github.com/ksaimanikanta4-arch/aiml-engineer-assignment
