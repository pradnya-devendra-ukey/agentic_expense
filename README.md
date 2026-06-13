---
title: PennyWise AI
emoji: 💰
colorFrom: green
colorTo: indigo
sdk: docker
pinned: false
license: mit
---

# 💰 PennyWise AI — Agentic Expense Manager

PennyWise AI is an LLM-powered agentic expense tracking and querying application. You can log expenses using natural language, upload receipts for OCR scanning, ask voice-based queries, and visualize your spending habits.

It leverages:
*   **FastAPI** for a fast, modern API backend that also serves a responsive web dashboard.
*   **LangGraph & OpenAI** for an agentic reasoning engine that parses natural language logs, extracts structured details, and dynamically queries your history.
*   **PaddleOCR & OpenCV** for extracting text from uploaded receipts.
*   **Deepgram API** for robust, real-time voice command transcription.
*   **SQLite** (local development) and **PostgreSQL** (production deployment) backend support.

---

## 🚀 Getting Started (Local Development)

### 📋 Prerequisites

Ensure you have the following installed on your system:
*   **Python 3.11** (recommended version)
*   **Git**

#### System-level OCR & OpenCV Dependencies
PaddleOCR and OpenCV require system-level dependencies.
*   **Debian/Ubuntu:**
    ```bash
    sudo apt-get update && sudo apt-get install -y \
        build-essential swig libgl1 libglib2.0-0 libsm6 \
        libxrender1 libxext6 libgomp1 wget
    ```
*   **macOS:**
    ```bash
    brew install swig
    ```
*   **Windows:**
    *   Ensure you have [Microsoft Visual C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) installed if you face compilation errors during Paddle installation.

---

### 🔧 Installation & Setup

1.  **Clone or navigate to the repository:**
    ```bash
    cd Agentic_expense_manager
    ```

2.  **Create and activate a virtual environment:**
    *   **Windows:**
        ```powershell
        python -m venv .venv
        .venv\Scripts\activate
        ```
    *   **macOS / Linux:**
        ```bash
        python3 -m venv .venv
        source .venv/bin/activate
        ```

3.  **Install the dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Set up environment variables:**
    *   Copy `.env.example` to `.env`:
        ```bash
        cp .env.example .env
        ```
    *   Open the newly created `.env` file and fill in your variables:
        *   `OPENAI_API_KEY`: Get your API key from the [OpenAI Platform](https://platform.openai.com/).
        *   `DEEPGRAM_API_KEY`: (Optional) Required for voice commands.
        *   `JWT_SECRET`: (Optional) Change to a long random secret key.

---

### 🖥️ Running the Application

There are two ways to run and interact with PennyWise AI locally:

#### 1. Run the Web App & API Dashboard (Recommended)
This starts the FastAPI server which serves both the API endpoints and the front-end dashboard UI:
```bash
uvicorn app:app --reload --port 8000
```
Open [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser to sign up, log in, view charts, upload receipts, and chat with your agent.

#### 2. Run the Interactive CLI REPL
You can interact directly with the agent through the terminal:
```bash
python main.py
```
This starts a command-line interface where you can talk to the agent directly (e.g., `"spent 500 on dinner using UPI"`, or `"how much did I spend this week?"`). Note that the local CLI runs on a default local user context and does not require registration.

---

## 🐳 Docker (Local & Production)

If you prefer to run the application using Docker, a configuration is ready out of the box.

1.  **Build the Docker image:**
    ```bash
    docker build -t pennywise-ai .
    ```

2.  **Run the container:**
    ```bash
    docker run -d -p 7860:7860 --env-file .env pennywise-ai
    ```
    Access the application at [http://localhost:7860](http://localhost:7860).

---

## ☁️ Deployment

PennyWise AI is ready for containerized or server-based deployments.

### 🌐 1. Deploying to Hugging Face Spaces (Docker SDK)
This project is already pre-configured to deploy seamlessly to Hugging Face Spaces:
1.  Create a new Space on [Hugging Face](https://huggingface.co/spaces).
2.  Choose **Docker** as the SDK.
3.  Choose the **Blank** template.
4.  Commit and push the project files to the Hugging Face Space repository.
5.  In the Hugging Face Space **Settings**, add your **Repository Secrets / Environment Variables**:
    *   `OPENAI_API_KEY` (Required)
    *   `JWT_SECRET` (Required for authentication)
    *   `DATABASE_URL` (Highly recommended for persistent database, see below)
    *   `DEEPGRAM_API_KEY` (Optional)

### 🛢️ Database Persistence (PostgreSQL / Neon)
*   **Local Dev Default:** By default, if `DATABASE_URL` is empty, PennyWise AI uses a local SQLite database (`expenses.db`).
*   **Production Deployment:** Docker containers and cloud environments (like Hugging Face Spaces or Render) have ephemeral disk storage. Restarting the app will erase SQLite database changes.
*   **How to persist:** Set the `DATABASE_URL` environment variable to point to a PostgreSQL instance (e.g., a free tier database on [Neon Database](https://neon.tech/)). The application will automatically detect this and switch to PostgreSQL, creating the tables and running migrations on startup.

### 🚀 2. Deploying to Render, Railway, or Fly.io
You can deploy PennyWise AI to any cloud platform supporting Docker or Python:
1.  **Using Dockerfile:** Point the platform to the `Dockerfile`. Ensure you map port `7860` or let the platform override it via its setup.
2.  **Using Python directly:** Run the start command:
    ```bash
    uvicorn app:app --host 0.0.0.0 --port $PORT
    ```
3.  Provide the environment variables listed in `.env.example` in the platform's dashboard.
