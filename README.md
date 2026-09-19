# 🩺 HealthBot — AI-Powered Patient Education Assistant

HealthBot is an AI-powered **patient education assistant** that helps users learn about health topics through a retrieval-augmented workflow.

Instead of asking the language model to answer from memory alone, HealthBot first retrieves relevant information using **Tavily Search**, then passes the retrieved context to **Google Gemini** to generate a simple, patient-friendly educational summary.

After the explanation, HealthBot generates a comprehension quiz and evaluates the user's answer using the information presented in the summary.

> **⚠️ Medical Disclaimer:** HealthBot is an educational project and is **not a medical diagnostic or treatment system**. It does not replace a qualified healthcare professional. Users should seek professional medical advice for diagnosis, treatment, emergencies, or personal health decisions.

---

## ✨ Key Features

- 🔎 **Web-based health information retrieval** using Tavily
- 🤖 **AI-generated patient-friendly summaries** using Google Gemini
- 🧠 **Retrieval-Augmented Generation (RAG-style) workflow**
- 🔗 **LangGraph orchestration** for the multi-step pipeline
- 📝 **AI-generated comprehension quiz**
- 📊 **Answer grading and feedback** based only on the generated summary
- 🔄 **Multi-topic interactive sessions**
- 🛡️ Prompt constraints designed to reduce unsupported medical claims
- 🐍 Python-based implementation using a virtual environment

---

## 🏗️ System Architecture

```text
                    ┌─────────────────────┐
                    │      User Input     │
                    │   Health Question   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Tavily Search     │
                    │ Retrieve Relevant   │
                    │ Health Information  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Context Builder   │
                    │ Search Results →    │
                    │ LLM Context         │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Google Gemini    │
                    │ Patient-Friendly    │
                    │ Health Summary      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Quiz Generator    │
                    │ 1 Question + 4      │
                    │ Multiple Choices    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    User Answer      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Gemini Grading    │
                    │ Grade + Explanation │
                    │ + Summary Citation  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Continue / End      │
                    └─────────────────────┘
```

---

## 🔄 LangGraph Workflow

The complete HealthBot workflow is orchestrated using **LangGraph**:

```text
START
  │
  ▼
ask_topic
  │
  ▼
search
  │
  ▼
generate
  │
  ▼
present_summary
  │
  ▼
ready_for_quiz
  │
  ▼
quiz
  │
  ▼
collect_answer
  │
  ▼
grade
  │
  ▼
present_feedback
  │
  ▼
ask_continue
  │
  ├──────── yes ───────► ask_topic
  │
  └──────── no ────────► END
```

The workflow maintains a shared `HealthBotState` containing the user's query, retrieved search results, generated context, response, quiz, answer, score/feedback, and session state.

---

## 🧠 How the AI Pipeline Works

### 1. User asks a health-related question

Example:

```text
What are the common symptoms of dehydration?
```

### 2. Information retrieval

HealthBot sends the query to **Tavily** and retrieves relevant search results.

The search query is expanded with terms such as:

```text
symptoms
causes
treatment
patient information
```

### 3. Context construction

The retrieved results are converted into a context containing:

- Source title
- Source URL
- Retrieved content

### 4. Gemini generates the explanation

Google Gemini receives the retrieved context and is instructed to:

- Use only the retrieved information
- Avoid unsupported medical claims
- Use simple language
- Avoid diagnosing the user
- Avoid personalized treatment advice
- Mention warning signs only when supported by the retrieved information

### 5. Comprehension quiz

After the explanation, Gemini generates:

- One question
- Four options: A, B, C, D
- One correct answer

The question is required to be answerable from the generated summary.

### 6. Answer evaluation

The user submits an answer.

Gemini evaluates it using the summary and returns:

```text
GRADE: A/B/C/D/F

FEEDBACK:
Explanation of the result

CITATION FROM SUMMARY:
Relevant information supporting the evaluation
```

This keeps the evaluation grounded in the information that was actually presented to the user.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python 3.11+** | Core programming language |
| **Google Gemini** | Health summary, quiz generation and answer evaluation |
| **Tavily** | Web search and information retrieval |
| **LangGraph** | Workflow and state orchestration |
| **LangChain Community** | Tavily integration |
| **python-dotenv** | Environment configuration |
| **Jupyter Notebook** | Development and experimentation |
| **uv** | Python project and dependency management |

---

## 📁 Project Structure

```text
HealthBot/
│
├── src/
│   └── healthbot/
│       └── __init__.py
│
├── healthBot_Trial.ipynb
├── requirements.txt
├── pyproject.toml
├── uv.lock
├── .python-version
├── .gitignore
├── config.env
└── README.md
```

> The local `.venv/` virtual environment and Python cache files are excluded from Git using `.gitignore`.

---

## ⚙️ Setup

### 1. Clone the repository

```bash
git clone https://github.com/Mallikarjun-Timasni/HealthBot.git
cd HealthBot
```

### 2. Create and activate a virtual environment

Using Python:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

Or, if you use `uv`:

```bash
uv sync
```

### 4. Configure API keys

Create a local `config.env` file:

```env
GEMINI_API_KEY=your_gemini_api_key
GEMINI_MODEL=your_gemini_model
TAVILY_API_KEY=your_tavily_api_key
```

**Never commit real API keys, tokens, passwords, or other secrets to GitHub.**

### 5. Start Jupyter

```bash
jupyter notebook
```

Open:

```text
healthBot_Trial.ipynb
```

Run the notebook cells and start a HealthBot session.

---

## 🧪 Example Interaction

```text
======================================================================
WELCOME TO HEALTHBOT
Learn about a health topic and test your understanding.
======================================================================

What health topic or medical condition would you like to learn about?
> What are the common symptoms of dehydration?
```

HealthBot retrieves information, generates an educational summary, and then presents a comprehension question.

```text
======================================================================
COMPREHENSION CHECK
======================================================================

A. ...
B. ...
C. ...
D. ...

Your answer:
```

The system then provides feedback based on the information contained in the generated summary.

---

## 🔐 Safety & Grounding Approach

Because this project deals with health information, the prompting strategy intentionally restricts the model.

HealthBot instructs the model to:

- Ground the summary in retrieved search results
- Avoid diagnosing users
- Avoid personalized treatment recommendations
- Avoid introducing unsupported medical facts
- Generate quiz questions from the presented summary
- Grade answers using the summary rather than unrelated medical knowledge

These measures are intended to improve grounding, but they **do not guarantee medical accuracy**.

---

## ⚠️ Current Limitations

This is an educational prototype and has several limitations:

- It relies on external web search results for retrieved information.
- Search-result quality can vary.
- The system does not independently verify medical sources.
- It does not maintain a clinical patient record.
- It does not provide medical diagnosis.
- It does not replace professional medical advice.
- The current implementation is primarily notebook/terminal based rather than a production web application.
- LLM-generated content can still contain errors.

---

## 🚀 Future Improvements

Potential next steps include:

- [ ] Add authoritative medical-source filtering
- [ ] Add source ranking and credibility checks
- [ ] Add a web UI using Streamlit
- [ ] Add persistent conversation history
- [ ] Add structured citations to retrieved sources
- [ ] Add multilingual health education
- [ ] Add evaluation metrics for retrieval and generation quality
- [ ] Add automated hallucination/grounding tests
- [ ] Add Docker support
- [ ] Add automated CI/CD
- [ ] Deploy the application to a cloud platform
- [ ] Add authentication and secure session management

---

## 🎯 Project Objective

The goal of HealthBot is to explore how **LLMs, information retrieval, and agent/workflow orchestration** can be combined to build an educational assistant while explicitly constraining the model to retrieved information.

The project demonstrates practical use of:

**Retrieval → Context → Generation → Education → Assessment**

---

## 👨‍💻 Author

**Mallikarjun Timasani**

GitHub: [Mallikarjun-Timasni](https://github.com/Mallikarjun-Timasni)

---

## 📜 License

This project is intended for educational and portfolio purposes.
