# Finsure - AI-Powered Financial Assistant

A sophisticated AI-powered financial assistant with conversational interface, built with FastAPI, LangChain, and ChromaDB.

## 🚀 Features

- **Conversational Financial Planning**: Guided conversation flow for comprehensive financial assessment
- **Hybrid Retrieval System**: Combines semantic search (embeddings) + keyword search (BM25) using ChromaDB
- **Financial Domain Restriction**: Only responds to finance-related queries with helpful error messages
- **Personalized Recommendations**: Analyzes user profile and provides tailored financial advice
- **Multi-source Data Ingestion**: Supports CSV, JSON, and text file uploads
- **Session Management**: Maintains conversation state across multiple interactions
- **Real-time Analysis**: Provides instant feedback and recommendations based on user inputs

## 📋 Prerequisites

- Python 3.8+
- pip or conda
- Google Gemini API Key (or OpenAI API Key)

## 🛠️ Installation

1. **Clone the repository** (if applicable) or navigate to the project directory

2. **Install dependencies**:
```bash
pip install -r requirements.txt
```

3. **Set up environment variables**:
   - Copy `.env.example` to `.env`
   - Add your Google Gemini API key:
   ```bash
   GOOGLE_API_KEY=your-actual-gemini-api-key-here
   ```

## 🚀 Quick Start

1. **Start the server**:
```bash
python main.py
```

2. **Access the API documentation**:
   - Open your browser and go to: http://localhost:8000/docs
   - Interactive Swagger UI for testing endpoints

3. **Test the chatbot**:
   - Use the `/chat` endpoint with a finance-related message
   - The bot will guide you through a financial assessment conversation

## 📡 API Endpoints

### 1. Health Check
```http
GET /
```
Returns system health status and timestamp.

### 2. Chat Endpoint
```http
POST /chat
Content-Type: application/json

{
  "message": "What is your monthly income?",
  "user_id": "optional-user-id",
  "session_id": "optional-session-id",
  "language": "en"
}
```

**Response**:
```json
{
  "response": "Thank you! I've noted your monthly income as ₹50,000. What are your monthly expenses (in rupees)?",
  "next_question": "What are your monthly expenses (in rupees)?",
  "question_type": "expenses",
  "user_profile": {
    "income": 50000
  },
  "session_id": "generated-session-id",
  "is_finance_related": true
}
```

### 3. Data Ingestion
```http
POST /ingest
Content-Type: multipart/form-data

Form Data:
- file: (upload file)
- source_type: "csv" | "json" | "text"
- content: (alternative to file upload)
- metadata: (JSON string with metadata)
```

### 4. Session Management
```http
GET /sessions/{session_id}
```
Returns conversation state for a specific session.

## 💬 Conversation Flow

The chatbot follows a structured financial assessment process:

1. **Income Assessment**: "What is your monthly income (in rupees)?"
2. **Expense Analysis**: "What are your monthly expenses (in rupees)?"
3. **Savings Evaluation**: "How much do you currently have in savings (in rupees)?"
4. **Loan Assessment**: "Do you have any existing loans or EMIs? If yes, how much is remaining?"
5. **Risk Profile**: "What is your risk tolerance? (low/medium/high)"
6. **Investment Horizon**: "How long do you plan to invest? (short-term/medium-term/long-term)"
7. **Personalized Analysis**: Comprehensive financial recommendations based on profile

## 🔍 Financial Domain Filtering

The chatbot only responds to finance-related queries. If a non-finance question is asked, it responds with:
```
"Please ask only finance-related questions. I can help you with topics like investments, savings, loans, insurance, and financial planning."
```

**Finance keywords detected**:
- money, finance, investment, bank, loan, savings, mutual fund
- stock, bond, insurance, tax, retirement, pension
- income, expense, budget, wealth, financial planning
- portfolio, interest, deposit, credit, debt, emi, sip
- fd, ppf, scheme, government, nse, bse, rbi, market, trading
- crypto, bitcoin, economy, inflation, gold, silver

## 🎯 Recommendation Engine

Based on user profile analysis, the system provides:

### For Low Income Users (< ₹20,000/month):
- Government welfare schemes (PM Kisan Samman Nidhi)
- Post Office Savings Account
- Sukanya Samriddhi Yojana

### For Medium Income Users (₹20,000 - ₹50,000/month):
- Bank Fixed Deposits
- Public Provident Fund (PPF)
- Low-risk mutual fund options

### For High Income Users (> ₹50,000/month):
- Diversified portfolio (equity + debt)
- Tax planning instruments
- Higher return options based on risk tolerance

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   FastAPI App   │────│   LangChain     │────│   ChromaDB      │
│                 │    │   Pipeline      │    │   Vector Store  │
│ • /chat endpoint│    │ • Embeddings    │    │ • Semantic      │
│ • /ingest endpoint│  │ • Text Splitter │    │ • Keyword       │
│ • /health endpoint│ │ • Retrievers    │    │ • Hybrid Search │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   Gemini AI     │
                    │   Integration   │
                    │                 │
                    │ • Conversation  │
                    │ • Analysis      │
                    │ • Recommendations│
                    └─────────────────┘
```

## 📊 Data Storage & Retrieval

### ChromaDB Configuration:
- **Collection**: `financial_data`
- **Embedding Model**: `sentence-transformers/all-MiniLM-L6-v2`
- **Hybrid Search**: 70% semantic + 30% keyword weighting
- **Chunk Size**: 1000 characters with 200 character overlap

### Initial Financial Data:
- Bank Fixed Deposits (FD) information
- Government schemes (PPF, Sukanya Samriddhi)
- Mutual Fund details
- Insurance products (PM Jeevan Jyoti Bima Yojana)

## 🔧 Configuration

### Environment Variables (.env):
```bash
# Required
GOOGLE_API_KEY=your-gemini-api-key

# Optional
OPENAI_API_KEY=your-openai-api-key
LOG_LEVEL=INFO
CHROMA_DB_PATH=./data/chroma_db
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
```

## 🧪 Testing the API

### Using cURL:

1. **Test Health Check**:
```bash
curl http://localhost:8000/
```

2. **Test Chat (Start Conversation)**:
```bash
curl -X POST "http://localhost:8000/chat" \
  -H "Content-Type: application/json" \
  -d '{"message": "I want to start financial planning"}'
```

3. **Continue Conversation**:
```bash
curl -X POST "http://localhost:8000/chat" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "My monthly income is 45000",
    "session_id": "previous-session-id"
  }'
```

### Using Python requests:
```python
import requests

# Start conversation
response = requests.post("http://localhost:8000/chat", json={
    "message": "I want to start financial planning"
})

print(response.json())
```

## 📈 Performance Optimization

- **Caching**: Conversation states are cached in memory
- **Efficient Search**: Hybrid retrieval reduces latency
- **Chunked Processing**: Large documents are split for better retrieval
- **Session Management**: State is maintained per user session

## 🚨 Error Handling

The API includes comprehensive error handling:
- Invalid input validation
- Session not found errors
- File upload errors
- API key configuration issues
- Database connection problems

## 🔮 Future Enhancements

- **Multi-language Support**: Hindi, Marathi, regional languages
- **Advanced Analytics**: Detailed financial ratio analysis
- **Integration with Banks**: Real-time interest rate data
- **Portfolio Tracking**: Monitor user's investment performance
- **Educational Content**: Financial literacy modules
- **Voice Interface**: Speech-to-text integration

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Ensure all tests pass
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

## 📞 Support

For issues and questions:
- Check the API documentation at `/docs`
- Review error messages for guidance
- Ensure all environment variables are properly configured

---

**Built for democratizing financial literacy and empowering users with personalized financial guidance!** 💰📈
