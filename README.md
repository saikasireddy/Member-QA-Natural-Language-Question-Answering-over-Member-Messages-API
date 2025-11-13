# Member Data Question-Answering Service

A simple question-answering system that answers natural-language questions about member data from a public API.

## 🚀 Live Deployment

**Service URL**: https://aiml-engineer-assignment.onrender.com

**Quick Test**:
```bash
# Check service health
curl https://aiml-engineer-assignment.onrender.com/health

# Get statistics
curl https://aiml-engineer-assignment.onrender.com/stats

# Ask a question
curl "https://aiml-engineer-assignment.onrender.com/ask?question=How%20many%20users%20are%20there?"
```

**GitHub Repository**: https://github.com/ksaimanikanta4-arch/aiml-engineer-assignment

## Features

- **Natural Language Processing**: Answers questions using Groq LLM (llama-3.3-70b-versatile - free tier), with fallback support for Claude 3.5 Sonnet or OpenAI GPT-3.5-turbo
- **RESTful API**: Simple `/ask` endpoint that accepts questions via GET or POST
- **Data Aggregation**: Automatically fetches and processes all 3,349 messages from the external API
- **Fallback Support**: Basic keyword-based search when LLM is unavailable
- **Multi-LLM Support**: Supports Groq (preferred), Claude, and OpenAI APIs with automatic failover

## API Endpoints

### GET/POST `/ask?question=YOUR_QUESTION`

Answer a question about member data.

**Example:**
```bash
curl "http://localhost:8000/ask?question=When is Layla planning her trip to London?"
```

**Response:**
```json
{
  "answer": "Based on the messages, Layla mentioned planning a trip to London in March 2024."
}
```

### GET `/health`

Health check endpoint. Returns LLM provider status.

### GET `/stats`

Get statistics about the messages (total messages, unique users, etc.).

## Project Structure

```
Assessment Project/
├── main.py                 # FastAPI application with /ask endpoint
├── requirements.txt        # Python dependencies
├── Dockerfile             # Container configuration
├── docker-compose.yml     # Docker compose configuration
├── Procfile              # Deployment configuration
├── README.md              # Main documentation
├── .gitignore             # Git ignore patterns
├── tests/                 # Test files
│   ├── test_api.py       # API integration tests
│   └── test_models.py    # Model/API tests
├── scripts/               # Utility scripts
│   └── analysis.py       # Data analysis script
└── docs/                  # Documentation
    ├── SETUP.md          # Setup guide
    └── TASK_EXPLANATION.md # Task explanation
```

## Setup

### Prerequisites

- Python 3.11+
- Groq API key (preferred - free tier) or Claude/OpenAI API key (for LLM-based answers)

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd "Assessment Project"
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Set up environment variables:
```bash
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY (preferred) or OPENAI_API_KEY
```

4. Run the service:
```bash
python main.py
```

Or using uvicorn directly:
```bash
uvicorn main:app --reload
```

The service will be available at `http://localhost:8000`

## Deployment

### Using Docker

1. Build the Docker image:
```bash
docker build -t member-qa-service .
```

2. Run the container:
```bash
docker run -p 8000:8000 -e ANTHROPIC_API_KEY=your_key_here member-qa-service
```

Or with OpenAI:
```bash
docker run -p 8000:8000 -e OPENAI_API_KEY=your_key_here member-qa-service
```

### Deployment Platforms

This service can be deployed to:
- **Railway**: Connect your GitHub repo and set environment variables
- **Render**: Use the Dockerfile or deploy directly
- **Fly.io**: Use `fly launch` with the Dockerfile
- **Heroku**: Use the Procfile (create one if needed)

## Design Notes

### Alternative Approaches Considered

#### 1. **Embedding-based Semantic Search** (Alternative 1)
   - **Approach**: Use OpenAI embeddings to create vector representations of messages, store in a vector database (e.g., Pinecone, Weaviate), and perform semantic search.
   - **Pros**: 
     - Faster query response times after initial indexing
     - Can handle large datasets efficiently
     - Better scalability
   - **Cons**: 
     - Requires additional infrastructure (vector database)
     - More complex setup
     - May need fine-tuning for domain-specific queries
   - **Why not chosen**: More complex setup, requires additional services, current dataset size doesn't justify the overhead

#### 2. **Rule-based Pattern Matching** (Alternative 2)
   - **Approach**: Use regex patterns and keyword extraction to identify entities (names, dates, locations) and answer questions through structured queries.
   - **Pros**: 
     - No external API dependencies
     - Predictable and deterministic
     - Fast execution
   - **Cons**: 
     - Limited to predefined patterns
     - Cannot handle complex or nuanced questions
     - Requires extensive pattern engineering
   - **Why not chosen**: Too rigid, cannot handle the variety of natural language questions effectively

#### 3. **Hybrid Approach: LLM + Caching** (Current Implementation)
   - **Approach**: Use LLM for question answering with message context, optionally cache frequently asked questions.
   - **Pros**: 
     - Flexible and handles diverse questions
     - Good balance between accuracy and simplicity
     - Can be enhanced with caching for performance
   - **Cons**: 
     - Requires API key and incurs costs
     - Slightly slower than vector search
     - Token limits may truncate very large datasets
   - **Why chosen**: Best balance of simplicity, accuracy, and flexibility for this use case

#### 4. **Fine-tuned Model** (Alternative 3)
   - **Approach**: Fine-tune a smaller model (e.g., GPT-3.5, LLaMA) on the message dataset for domain-specific knowledge.
   - **Pros**: 
     - Better domain-specific understanding
     - Potentially lower inference costs
     - Can run locally without API dependencies
   - **Cons**: 
     - Requires training data and computational resources
     - Model needs retraining when data updates
     - More complex deployment
   - **Why not chosen**: Overkill for this use case, data updates frequently, training overhead is significant

#### 5. **Multi-step Retrieval-Augmented Generation (RAG)** (Alternative 4)
   - **Approach**: First retrieve relevant messages using embeddings, then generate answer using LLM with only relevant context.
   - **Pros**: 
     - Efficient use of token limits
     - Better accuracy by focusing on relevant context
     - Scalable to large datasets
   - **Cons**: 
     - More complex architecture
     - Requires embedding generation and storage
     - Two-step process adds latency
   - **Why not chosen**: Current implementation is simpler and sufficient for the dataset size; can be upgraded later if needed

### Current Implementation Details

The current implementation uses:
- **FastAPI** for the REST API framework
- **Claude 3.5 Sonnet** (preferred) or **OpenAI GPT-3.5-turbo** for question answering
- **httpx** for async API calls to fetch messages
- **Simple context formatting** that includes user names, IDs, timestamps, and messages

The system:
1. Fetches all messages from the external API (with pagination)
2. Formats messages into a context string (up to 100K chars for Claude, 8K for OpenAI)
3. Sends the question and context to Claude API (or OpenAI as fallback)
4. Returns the generated answer

**LLM Preference**: Claude API is preferred when available, with OpenAI as a fallback. If neither is configured, the system falls back to a simple keyword-based search.

## Data Insights

### Analysis of Member Data

After analyzing the dataset from the external API using the `scripts/analysis.py` script, here are the actual findings:

#### 1. **Dataset Overview**
   - **Total messages analyzed**: 1,400 (limited by API errors; total available: 3,349)
   - **Unique users**: 10
   - **Message ID format**: UUID (e.g., `b1e9bb83-18be-4b90-bbb8-83b7428e8e21`)
   - **Average message length**: 67 characters (range: 25-105 characters)

#### 2. **Data Quality Findings**

   **✅ Positive Findings:**
   - **No duplicate message IDs**: Each message has a unique identifier
   - **Consistent timestamps**: All messages use ISO 8601 format with timezone (e.g., `2025-05-05T07:47:20.159073+00:00`)
   - **No missing fields**: All required fields (`id`, `user_id`, `user_name`, `timestamp`, `message`) are present
   - **Consistent user mapping**: Each `user_id` maps to exactly one `user_name` with no conflicts
   - **No empty messages**: All messages contain actual content

   **⚠️ Issues Identified:**

   **A. External API Reliability**
   - The API returns random HTTP errors during pagination: 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 405 (Method Not Allowed)
   - These errors occur inconsistently - same requests sometimes succeed, sometimes fail
   - Impact: Only ~42% of total messages (1,400/3,349) could be fetched successfully
   - **Mitigation implemented**: Retry logic with exponential backoff and batch skipping

   **B. User Distribution**
   - Messages are relatively evenly distributed across users (118-152 messages per user)
   - Top contributors: Amina Van Den Berg (152), Lily O'Sullivan (151), Layla Kawaguchi (146)

#### 3. **Recommendations**

1. **API Reliability**: Contact API provider about the random HTTP errors during pagination
2. **Caching Strategy**: Cache successfully fetched messages to avoid repeated API calls
3. **Error Monitoring**: Log API failures for pattern analysis
4. **Retry Logic**: Already implemented with exponential backoff and failure tolerance
5. **Data Validation**: Current implementation handles the clean data structure well

### Future Improvements

1. **Caching**: Implement Redis caching for frequently asked questions
2. **Rate Limiting**: Add rate limiting to prevent API abuse
3. **Analytics**: Track question patterns and improve answer quality
4. **Multi-language Support**: Support questions in multiple languages
5. **Confidence Scores**: Add confidence scores to answers
6. **Citation**: Include message sources/citations in answers

## Testing

Test the service with example questions using either format:

### Simple Format (with spaces in quotes)
```bash
# When is Layla planning her trip to London?
curl "http://localhost:8000/ask?question=When is Layla planning her trip to London?"

# How many cars does Vikram Desai have?
curl "http://localhost:8000/ask?question=How many cars does Vikram Desai have?"

# What are Amira's favorite restaurants?
curl "http://localhost:8000/ask?question=What are Amira's favorite restaurants?"

# How many users are in the dataset?
curl "http://localhost:8000/ask?question=How many users are in the dataset?"

# List all user names
curl "http://localhost:8000/ask?question=List all user names"
```

### URL-Encoded Format (with %20 for spaces)
```bash
# When is Layla planning her trip to London?
curl "http://localhost:8000/ask?question=When%20is%20Layla%20planning%20her%20trip%20to%20London?"

# How many cars does Vikram Desai have?
curl "http://localhost:8000/ask?question=How%20many%20cars%20does%20Vikram%20Desai%20have?"

# What are Amira's favorite restaurants?
curl "http://localhost:8000/ask?question=What%20are%20Amira's%20favorite%20restaurants?"
```

### POST Method
```bash
# Using POST for longer questions
curl -X POST http://localhost:8000/ask \
  -H "Content-Type: application/json" \
  -d '{"question": "What are the most common topics discussed in the messages?"}'
```

## License

MIT

## Author

Assessment Project - Member Data QA Service

