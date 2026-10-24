# RemoodleChat: Development of an Intelligent Chatbot for Student Information Support Based on Language Models

**Institution:** Astana IT University (AITU)  
**Department:** Software Engineering  
**Developer:** Baurzhanova Ayauzhan (3rd Year Student — Solo Diploma Project)  
**Project Supervisor:** Sadirmekova Zhanna  
**Current Milestone / Git Tag:** `prob-period-1` (October 24, 2026)  
**YouTrack Agile Board:** https://ayaub.youtrack.cloud/agiles/204-1/current  
**GitHub Repository:** https://github.com/Ayauzhan-Baurzhanova/Diploma-Chatbot-with-Mascot.git  

---

## 1. Project Overview

**RemoodleChat** is a student-centric, gamified intelligent mobile application built for Astana IT University (AITU) undergraduate students. It features the unofficial, student-designed mascot **Remoodle-chan** and is powered by a Retrieval-Augmented Generation (RAG) architecture paired with a local relational SQLite database. The application is engineered to rapidly deliver source-verified answers regarding university regulations, course syllabuses, and hypothetical academic grade scenarios while fostering student engagement through optional, non-intrusive gamification.

### 1.1 Problem Statement
Undergraduate students at AITU currently spend between 5 and 20 minutes per inquiry searching for academic regulations, FX/retake policies, GPA/scholarship formulas, and syllabus details scattered across unstructured PDF documents on Moodle and the official university website. Existing search utilities rely on basic keyword matching without semantic or contextual understanding. Consequently, students experience bureaucratic friction and frequently rely on informal Telegram group chats—risking misinformation—while administrative staff and academic advisors face a high volume of repetitive inquiries.

### 1.2 Target Users
* **Primary Target Users:** Current undergraduate students at Astana IT University (1st, 2nd, and 3rd-year students across all faculties).
* **Secondary Beneficiaries:** University administrative staff, academic advisors, and course instructors (via reduced repetitive inquiry workload).

---

## 2. Aim, Objectives, and Expected Outcomes

**Aim:** To develop RemoodleChat, a student-centric, gamified intelligent mobile application featuring the mascot Remoodle-chan, powered by a RAG architecture and a local SQLite relational database to deliver source-verified information regarding university regulations in under 8 seconds.

1. **Objective 1 (Multi-Source Knowledge Base Aggregation):** Parse, clean, chunk, and vectorize the university's institutional PDF knowledge base (AITU Academic Policy, course syllabuses, bureaucratic templates) with section-level metadata.
   * *Expected Outcome:* A structured ChromaDB vector database containing normalized text chunks (`chunk_size=1000`, `overlap=150`) paired with exact source document titles, page numbers, authority scores, and reference links.
2. **Objective 2 (Grounded RAG & Deterministic Calculation Backend):** Integrate a Large Language Model via the OpenRouter API (DeepSeek V3 / 3.2) orchestrated by FastAPI and LangChain, incorporating semantic similarity thresholding tau = 0.72, deterministic grade/GPA calculation functions, and dynamic persona prompt injection.
   * *Expected Outcome:* An asynchronous REST API backend capable of answering academic queries and calculating hypothetical grade scenarios with `>85%` factual extraction accuracy, `<8.0s` mean latency, `0%` arithmetic error, and strict inline source citations.
3. **Objective 3 (Gamified Unity Client & Relational Store Economy):** Design and implement an interactive Android mobile client in Unity (C#) featuring the mascot Remoodle-chan, an assignment/grade tracker, a virtual store for unlocking sprite modifications (hairstyles and outfits), and dynamic personality/speech adaptation based on user profile (name, gender, year, faculty) and equipped outfits (with an optional toggle in Settings).
   * *Expected Outcome:* A deployed mobile client backed by a local SQLite relational database that persists chat history, user profile attributes, manually logged assignments, point balances (`P`), store inventory, and mascot Animator state transitions.
4. **Objective 4 (Empirical Verification & Evaluation):** Execute accuracy, latency, and hallucination-abstention testing against a 70-query benchmark dataset (50 in-scope complex queries + 20 out-of-scope adversarial queries) derived from the AITU Academic Policy.
   * *Expected Outcome:* Empirical validation using the RAGAS framework demonstrating `>85%` factual accuracy, `>=95%` out-of-scope abstention reliability, and `<8.0` seconds average response time compared to a non-RAG baseline.

---

## 3. Project Scope

### In Scope
* Automated offline ETL pipeline for AITU PDF documents (Academic Policy, syllabuses) and vector indexing in ChromaDB.
* FastAPI + LangChain middleware supporting semantic retrieval, inline citation packaging, deterministic grade/scholarship calculations, and dynamic system prompt modulation (adapting tone to user gender and equipped mascot outfit persona).
* Unity (C#) Android mobile application featuring Onboarding/Profiling, Chat UI with collapsible mascot viewport, Chat History, Assignment/Grade Point Logger (1:1 grade-to-point conversion), Virtual Store for purchasing/equipping Remoodle-chan outfits, and Settings (including a toggle to disable outfit-based personality shifts).
* Local relational database (SQLite) for managing structured user state, assignment logs, store inventory, and chat logs.

### Out of Scope
* Direct API integration with AITU's official Moodle LMS (AITU Moodle lacks an open student-facing API; assignment grades are logged manually by the student within the application).
* Voice-to-text audio synthesis (user interaction is strictly natural language text and 2D sprite/Animator state transitions).

---

## 4. Selected Technology Stack

| Subsystem | Selected Technology | Alternatives Considered | Justification & Requirement Alignment |
| :--- | :--- | :--- | :--- |
| **1. Mobile Frontend & Animation** | **Unity Engine (C#)** | Flutter (Dart), React Native (TS), Native Android SDK (Kotlin) | Fulfills FR-5, FR-6, NFR-3. Native Animator state machine and 2D Sprite rendering manage layered outfit swaps and `emotion_id` transitions without heavy third-party bridges. |
| **2. Backend API & Orchestration** | **FastAPI (Python) + LangChain** | Django REST Framework, Flask, Node.js (Express) | Fulfills FR-1, FR-2, NFR-2. Native ASGI `async/await` concurrency, automatic Pydantic JSON validation, and Python PDF/ML library support keep latency `<8s`. |
| **3. Vector Database** | **ChromaDB** | Pinecone, FAISS, Qdrant | Fulfills FR-1, FR-3, NFR-1. Open-source persistent local storage with built-in CRUD and rich metadata filtering (section, page, authority weight) at zero cloud subscription cost. |
| **4. Client Relational Database** | **SQLite (`sqlite-net-pcl`)** | Unity PlayerPrefs, Remote PostgreSQL, Firebase Firestore | Fulfills FR-4, FR-5, FR-7. Enforces local relational integrity across `Users`, `Assignments`, `StoreItems`, `UserInventory`, and `ChatMessages` with zero network latency. |
| **5. LLM Inference Provider** | **OpenRouter API (DeepSeek V3 / 3.2)** | Official OpenAI API (GPT-4o), Direct DeepSeek API, Local Ollama (Llama-3 8B) | Fulfills FR-2, FR-6, NFR-2. Unified endpoint with high throughput, low cost, strong instruction/persona adherence, and hot-swappable fallback routing. |

---

## 5. System Requirements Summary

### Functional Requirements (FR)
* **FR-1 (Must) — Multi-Source RAG Processing & Conflict Resolution:** Retrieve top-$k$ ($k=5$) chunks from ChromaDB with cosine similarity $\ge 0.72$, prioritizing official Academic Policy over syllabus drafts in 100% of conflicting cases.
* **FR-2 (Must) — Deterministic Academic Scenario Calculation:** Invoke a deterministic Python calculator function (`calculate_required_final()`) for hypothetical RegMid/RegEnd/Final grade and scholarship queries with 0% arithmetic error.
* **FR-3 (Must) — Inline Source Traceability:** Provide a reference link on 100% of non-fallback factual responses.
* **FR-4 (Must) — User Onboarding & Gender/Profile Personalization:** Capture Name, Gender, Study Year (`1` / `2` / `3`), and Faculty during onboarding, persist in SQLite, and inject into the prompt payload.
* **FR-5 (Must) — Assignment Grade Logger & Store Economy:** Convert manually logged assignment grades $X \in [0, 100]$ at a 1:1 ratio into in-app points (`+X P`) in SQLite to purchase and equip hairstyles and outfits.
* **FR-6 (Should) — Dynamic Mascot Emotion & Outfit-Driven Persona:** Trigger Unity Animator transitions via `emotion_id` in `<100 ms` and shift conversational persona based on equipped outfit unless `persona_shift_enabled == false` in Settings.
* **FR-7 (Should) — Persistent Chat History Management:** Save and load past conversation sessions locally from the SQLite `ChatMessages` table.

### Non-Functional Requirements (NFR)
* **NFR-1 (Must) — Hallucination Abstention & Grounding:** Achieve `>85%` factual accuracy on the 50 in-scope benchmark queries and `>=95%` fallback warning activation on the 20 out-of-scope queries when max chunk similarity $< 0.72$.
* **NFR-2 (Must) — Low End-to-End Response Latency:** Maintain mean end-to-end response time `< 8.0 seconds` (95th percentile `< 10.0 seconds`).
* **NFR-3 (Must) — Unobtrusive Gamified UI:** Collapsing the mascot viewport via the Enlarge icon expands the text chat to full screen in `< 200 ms`.
* **NFR-4 (Must) — Configuration Security & Secret Isolation:** Isolate all API keys inside server-side `.env` variables excluded via `.gitignore`; zero secrets in the Unity client binary or Git history.

---

## 6. Repository Structure

```text
Diploma-Chatbot-with-Mascot/
├── README.md                        # Project overview, scope, stack, and traceability links
├── .gitignore                       # Excludes .env secrets, Unity Library/Temp, and Python __pycache__
├── docs/                            # Project documentation and pre-defense deliverables
│   ├── DiplomaProjectProposal_Ayauzhan_ver1.docx
│   ├── Presentation_RemoodleChat.pdf
│   └── diagrams/                    # Editable Mermaid (.mmd) source files and PNG/SVG exports
│       ├── system-context.mmd
│       ├── use-case.mmd
│       └── component-diagram.mmd
├── prototype/                       # UI/UX mockup screens (Splash, Onboarding, Chat, Store)
├── client-unity/                    # (Phase 3) Unity C# Android project, Animator, and SQLite DAL
├── server-fastapi/                  # (Phase 2) FastAPI REST middleware, LangChain RAG, Grade Calculator
├── data-pipeline/                   # (Phase 1) Offline PDF ETL scripts (PyMuPDF/pdfplumber) & ChromaDB
└── tests/                           # (Phase 4) 70-query benchmark dataset and RAGAS evaluation scripts
3. Install dependencies: `pip install -r requirements.txt` (Available in future commits).
4. Set up the `.env` file with your OpenRouter API keys.
5. Run the FastAPI server: `uvicorn main:app --reload`
