# ✦ Resume Studio — AI-Powered Resume Optimizer

> Upload your resume, paste a job description, get an ATS-optimized resume + personalized cover letter in under 15 seconds.

![Resume Studio](https://img.shields.io/badge/AI-Powered-6c63ff?style=flat-square) ![React](https://img.shields.io/badge/React-18-61dafb?style=flat-square&logo=react) ![Node.js](https://img.shields.io/badge/Node.js-Express-339933?style=flat-square&logo=node.js) ![Groq](https://img.shields.io/badge/Groq-openai-f55036?style=flat-square) ![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)


## 📌 What It Does

Most job seekers send the same resume to every job. Companies use **ATS (Applicant Tracking Systems)** that automatically reject resumes missing specific keywords — before a human ever reads them.

Resume Studio solves this by:

1. **Reading your existing resume** — it never invents experience you don't have
2. **Analyzing the job description** — extracting required skills, keywords, and tone
3. **Rewriting your resume** — reordering bullets, strengthening language, injecting ATS keywords naturally
4. **Writing a cover letter** — personalized to the role, in your voice, not generic templates
5. **Scoring the match** — shows % job fit, ATS score, strengths, gaps, and missing keywords



## Video

[screen-capture (14).webm](https://github.com/user-attachments/assets/47c24d9c-6c69-4322-9d49-2e52e7c2aeec)


---

## 🧠 How the AI Works

This project uses **3 parallel AI calls** via the Groq API (openai/gpt-oss-20b):

```
User Resume + Job Description
          │
    ┌─────┴──────┐
    │            │            │
    ▼            ▼            ▼
Rewrite      Write        Score &
Resume      Cover        Analyze
(Prompt 1)  Letter       Match
            (Prompt 2)   (Prompt 3)
    │            │            │
    └─────┬──────┘
          ▼
   Results Screen
   (resume + cover letter + score)
```

All 3 calls run with `Promise.all()` — in parallel — so the total wait time is ~10–15 seconds instead of 30–45 seconds if run sequentially.

### Prompt Engineering Strategy

| Prompt | Role | Key technique |
|--------|------|---------------|
| Resume rewrite | Expert resume writer | Explicit rules: never invent, use STAR method, ATS keywords |
| Cover letter | Career coach | Anti-cliché rules, 3-paragraph structure, tone matching |
| Match analysis | Hiring manager | JSON-only output, structured scoring rubric |

---

## 🛠 Tech Stack

### Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| React | 18.3 | UI framework |
| Vite | 5.3 | Build tool & dev server |
| react-markdown | 9.0 | Render AI markdown output |
| lucide-react | 0.383 | Icons |
| Inter (Google Fonts) | — | Typography |
| Vanilla CSS | — | Styling (no CSS framework) |

### Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | 20+ | Runtime |
| Express | 4.19 | HTTP server & routing |
| groq-sdk | 0.3 | Groq AI API client |
| multer | 1.4 | PDF file upload handling |
| pdf-parse | 1.1 | Extract text from PDF buffer |
| dotenv | 16.4 | Environment variable management |
| cors | 2.8 | Cross-origin request handling |

### AI / External Services
| Service | Model | Purpose |
|---------|-------|---------|
| Groq | openai/gpt-oss-20b | LLM inference (fast, free tier) |
| Google Fonts | Inter | Typography CDN |

---

## 📁 Project Structure

```
resume-studio/
│
├── frontend/                        # React app (Vite)
│   ├── index.html                   # Entry HTML — mounts React root
│   ├── vite.config.js               # Vite config + API proxy to backend
│   ├── package.json
│   └── src/
│       ├── main.jsx                 # React entry point
│       ├── App.jsx                  # Root component — manages all state & step flow
│       ├── index.css                # Global dark theme CSS variables
│       └── components/
│           ├── Header.jsx           # Sticky nav with step progress indicator
│           ├── UploadStep.jsx       # Step 1 — PDF drag & drop upload
│           ├── JobStep.jsx          # Step 2 — Job description + name input
│           ├── LoadingScreen.jsx    # Step 3 — Animated loading with rotating messages
│           ├── ResultsScreen.jsx    # Step 4 — Scores, resume, cover letter, download
│           └── Toast.jsx            # Global toast notification system
│
└── backend/                         # Node.js + Express API
    ├── server.js                    # Express app entry — routes, CORS, health check
    ├── package.json
    ├── .env.example                 # Environment variable template
    └── routes/
        ├── upload.js                # POST /api/upload — PDF → text extraction
        └── analyze.js               # POST /api/analyze — 3 parallel AI calls
```

---

## ⚙️ Architecture & Key Decisions

### Why Groq instead of OpenAI?
Groq provides a **free tier** with 14,400 requests/day and extremely fast inference (~500 tokens/sec vs OpenAI's ~50). For a portfolio project this means zero cost and a better user experience with faster responses.

### Why 3 separate prompts instead of 1?
Each task (resume rewrite, cover letter, scoring) has different instructions, output format, and quality requirements. Combining them into one prompt reduces quality significantly — the model context gets confused. Separate prompts + `Promise.all()` gives better results at the same speed.

### Why store PDF text in React state instead of a database?
This is a stateless, session-based app — no user accounts, no persistence needed. The PDF text lives in `useState()` and is sent with each API request. This keeps the architecture simple and avoids GDPR/data privacy concerns since nothing is stored server-side.

### Why Vite proxy instead of direct backend URL?
In development, the Vite proxy forwards `/api/*` to `localhost:4000`, eliminating CORS issues. In production, the frontend uses the live backend URL via an environment variable. This pattern means zero CORS configuration changes between dev and production.

---

## 🔌 API Reference

### `POST /api/upload`
Accepts a PDF file and returns extracted text.

**Request:** `multipart/form-data`
| Field | Type | Description |
|-------|------|-------------|
| resume | File | PDF file, max 5MB |

**Response:**
```json
{
  "text": "John Doe\nSoftware Engineer...",
  "pages": 2,
  "filename": "my-resume.pdf"
}
```

---

### `POST /api/analyze`
Sends resume + job description to AI and returns all outputs.

**Request:** `application/json`
```json
{
  "resumeText": "extracted resume text...",
  "jobDescription": "We are looking for a Senior...",
  "userName": "John Doe"
}
```

**Response:**
```json
{
  "optimizedResume": "rewritten resume text...",
  "coverLetter": "personalized cover letter...",
  "matchScore": 78,
  "atsScore": 82,
  "verdict": "Strong match — React and Node.js experience aligns well",
  "strengths": ["3 years React experience", "REST API work matches", "Agile team experience"],
  "gaps": ["No TypeScript listed", "No CI/CD experience"],
  "missingKeywords": ["TypeScript", "CI/CD", "AWS"]
}
```

---

## 🏃 Running Locally

### Prerequisites
- Node.js 20+
- Free Groq API key from [console.groq.com](https://console.groq.com)

### Backend
```bash
cd backend
npm install
cp .env.example .env
# Paste your GROQ_API_KEY into .env
npm run dev
# Running on http://localhost:4000
```

### Frontend
```bash
cd frontend
npm install
npm run dev
# Running on http://localhost:5175
```

### Test the API is working
Open: `http://localhost:4000/api/test-groq`  
You should see a JSON response with `"success": true` and an AI-generated message.

## 🗓 How It Was Built — 6-Day Learning Journey

This project was built as a **learning project** to understand Generative AI and LLM integration from scratch.

| Day | Focus | What was learned |
|-----|-------|-----------------|
| 1 | Backend setup + first AI call | How LLM APIs work, tokens, system prompts, Groq SDK |
| 2 | PDF upload & extraction | File handling with multer, binary-to-text with pdf-parse |
| 3 | AI analysis route | Prompt engineering, parallel API calls, JSON parsing from LLM output |
| 4 | React frontend | 4-step UI flow, state management, fetch with FormData |
| 5 | Polish & UX | Toast notifications, download feature, keyword highlighting, mobile responsive |
| 6 | Deploy & README | Vercel, Render, environment variables, documentation |

**Starting point:** No prior experience with Generative AI or LLMs  
**Ending point:** Full-stack AI-powered product, deployed and live

---

## 💡 What I Learned About LLMs

- **Tokens** are not words — they're chunks of ~4 characters. `max_tokens` controls output length.
- **System prompts** are the "job description" for the AI — they define its role and rules.
- **Temperature** controls randomness — lower = more consistent, higher = more creative.
- **Prompt engineering** matters enormously — the same task with different instructions gives wildly different quality outputs.
- **Parallel AI calls** with `Promise.all()` is a common pattern to reduce latency when tasks are independent.
- **JSON output from LLMs** requires defensive parsing — models sometimes add markdown around JSON that needs stripping.
- **Context window** = the maximum text (input + output) the model can handle in one call.

---

## 🔮 Future Improvements

- [ ] Export resume as formatted PDF (not just plain text)
- [ ] Multiple resume versions saved in localStorage
- [ ] LinkedIn profile URL as input (scrape + analyze)
- [ ] Interview question generator based on the job description
- [ ] Side-by-side diff view of original vs optimized resume
- [ ] Multi-language support (French, German, Spanish)
- [ ] User accounts + history with Supabase

---

## 📄 License

MIT — free to use, modify, and distribute.

---

## 🤝 Contact

Built by **[Muhammad Zain Tariq]**  
[LinkedIn](https://www.linkedin.com/in/muhammad-zain-tariq) · [GitHub](https://github.com/mzaintariqdev)

---

*Built in 6 days as a portfolio project to learn Generative AI and LLM integration. Stack: React + Node.js + Groq (openai/gpt-oss-20b) + Express.*
