# Eskandarani 

**An AI-powered travel and city companion for Alexandria, Egypt.**
*Travel Home.*

Eskandarani is a mobile application (Android first) with a backend and an admin dashboard. It helps tourists and residents discover Alexandria through landmark recognition, grounded AI question answering, offline heritage packs, trip planning, transport guidance, and emergency help.

> Graduation project — Faculty of Computer and Data Science, Alexandria University
> Supervised by **Dr. Yasser Fakhry**

---

## Table of Contents

- [Motivation](#motivation)
- [Key Features](#key-features)
- [How It Compares](#how-it-compares)
- [Project Roadmap (MVPs)](#project-roadmap-mvps)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Documentation](#documentation)
- [Project Management](#project-management)
- [Team](#team)
- [License](#license)

---

## Motivation

We surveyed **115 respondents** (95.7% aged 18–24, 85.2% living in Alexandria) and analysed community discussions on social media. Key findings:

| Finding | Result |
|---|---|
| Most common way to find places | Social media (38%), family & friends (33%), Google Maps (20%) |
| Hardest problem (residents) | Traffic congestion and long travel time (45%) |
| Hardest problem (visitors) | Finding suitable transport (35%) and reaching places (29%) |
| Most common other problem | Moving between places (53.9%), unclear prices (35.7%), unreliable info (33%) |
| Finding accurate information is easy | Only 24% |
| Usual transport | Public transport (79%) |
| Would use a dedicated app | 50% (definitely + probably) |
| Most requested features | Transport info & cost (62.6%), suggestions by budget/interests (59.1%), offline access (53.9%) |

No reviewed product combines **landmark recognition, sourced answers, offline heritage packs, practical visit support, and local Alexandria depth**. Eskandarani aims to fill that gap.

---

## Key Features

### MVP1 — Scan and Ask (current priority)

- **Register / log in** (guest mode supported)
- **Scan signs and text (OCR)** with translation
- **Landmark storytelling** with audio narration
- **AI chatbot (RAG)** — answers grounded in a reviewed knowledge base, with visible sources; declines when no relevant source is found
- **Personalised plans** based on interests, time, and budget
- **Search places** — hotels, restaurants, cafés, landmarks
- **Medical services and SOS** — hospitals, pharmacies, hotlines, first aid and CPR guides
- **Transportation guidance** — several ways to reach a place, not only ride-hailing
- **Alerts and notifications**
- **Offline mode** — downloadable offline packs
- **Admin dashboard** — manage places database, alerts, and content

### MVP2 — Smart Visit Companion
Cultural companion features, community, Alexandria maps, accuracy and sources, AR content.

### MVP3 — Community and Immersive
Community and partner finding, avatar guide, smart-glasses mode, community moderation.

---

## How It Compares

| Feature | Tripadvisor | Google Maps | Wanderlog | Visit Egypt | Time-Lens | AI Travel Comp. | **Eskandarani** |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Landmark recognition by camera | ✗ | ✗ | ✗ | ✗ | ✓ | ✗ | ✓ |
| OCR and translation | ✗ | ✗ | ✗ | ✗ | ✗ | ◐ | ✓ |
| Grounded AI Q&A (RAG) | ◐ | ✗ | ✗ | ✗ | ✓ | ◐ | ✓ |
| Works offline | ✗ | ✓ | ◐ | ✗ | ✗ | ✓ | ✓ |
| Itinerary planning | ✓ | ◐ | ✓ | ◐ | ✗ | ✗ | ✓ |
| Maps and nearby places | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| Transport guidance | ✗ | ✓ | ◐ | ◐ | ✗ | ✗ | ✓ |
| Augmented reality | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| Voice narration | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |
| Alexandria heritage depth | ✗ | ✗ | ✗ | ◐ | ✗ | ✗ | ✓ |
| User community | ◐ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |
| Medical and emergency help | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |

✓ yes · ◐ partial · ✗ no

---

## Project Roadmap (MVPs)

| Phase | Duration | Scope |
|---|---|---|
| **MVP1 – Sprint 1** (Month 1) | Nov 2026 | Planning and UI/UX design, Flutter development, data collection and chatbot, database and backend, testing |
| **MVP1 – Sprint 2** (Month 2) | Dec 2026 | Recommendation, OCR, offline mode, RAG integration, storytelling, continuous Flutter and backend work, testing |
| **MVP1 – Sprint 3** (Month 3) | Jan 2027 | Transportation and global search, continuous development, testing, publishing |
| **MVP2** | ~1.5 months | Detailed tasks planned after the MVP1 review |
| **MVP3** | ~1.5 months | Detailed tasks planned after the MVP1 review |

---

## System Architecture

Eskandarani follows a client–backend architecture with external AI and map services.

```
┌──────────────┐     ┌──────────────┐     ┌────────────────────────┐
│  Flutter App │ ──► │  Backend API │ ──► │  Vector DB (semantic   │
│  (Tourist)   │ ◄── │              │     │  search over chunks)   │
└──────┬───────┘     └──────┬───────┘     └────────────────────────┘
       │                    │
       │ offline            ├──► LLM / AI service (prompt + context)
       ▼                    └──► Maps service (origin, destination → routes)
┌──────────────┐
│ Offline pack │
└──────────────┘
```

### RAG chatbot flow

1. The tourist asks a question in the app.
2. **Online:** the backend preprocesses the query, runs a semantic search in the vector index, builds a prompt from the top chunks, calls the LLM, and returns a cited answer.
3. **Offline:** the app searches the local pack on the device and builds the answer from stored content.
4. Because the LLM only sees the retrieved chunks, answers stay grounded in the reviewed knowledge base.

### Data flow (DFD) overview

**External entities:** Tourist, Admin, LLM / AI service, Maps service

**Data stores:**

| ID | Store | Contents |
|---|---|---|
| D1 | User data | Profiles, preferences, saved trip plans |
| D2 | Places and services DB | Places, stories, medical services, transport stops and stations |
| D3 | Vector index | Embeddings of place and service content (RAG) |
| D4 | Offline cache | Downloaded data packs |
| D5 | Alerts | Active alerts and announcements |

**Level 1 processes:**

| ID | Process |
|---|---|
| 1.0 | Manage account and preferences |
| 2.0 | Search places, medical services, transportation and recommend plans |
| 3.0 | Offline sync |
| 4.0 | Scan text (OCR) and storytelling |
| 5.0 | AI chatbot (RAG) |
| 6.0 | Send alerts |
| 7.0 | Manage content |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Mobile app | Flutter (Android first) |
| Backend | Python, FastAPI |
| Databases | MongoDB, PostGIS, Redis |
| Knowledge graph | Neo4j |
| Vector search | FAISS / Qdrant |
| Offline storage | SQLite |
| Background jobs | Celery |
| AI core chain | OCR → Arabic NLP → Knowledge Graph → RAG |
| Maps | OpenStreetMap |
| Containerisation | Docker |
| Project tracking | Jira |

> Adjust this table to match the final implemented stack.

---

## Getting Started

> ⚠️ The project is under active development. The commands below are a template — update them to match the actual repository layout.

### Prerequisites

- Flutter SDK
- Python 3.10+
- Docker and Docker Compose
- Git

### Clone the repository

```bash
git clone https://github.com/<your-org>/eskandarani.git
cd eskandarani
```

### Run the backend

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env            # add your configuration
uvicorn app.main:app --reload
```

### Run the databases (Docker)

```bash
docker compose up -d
```

### Run the mobile app

```bash
cd mobile
flutter pub get
flutter run
```

### Run the admin dashboard

```bash
cd admin
# add your dashboard start commands here
```

---

## Project Structure

```
eskandarani/
├── mobile/          # Flutter app
├── backend/         # FastAPI backend (API, RAG pipeline, OCR service)
├── admin/           # Admin dashboard
├── data/            # Knowledge base, place data, offline packs
├── docs/            # Project documentation, diagrams, wireframes
├── docker-compose.yml
└── README.md
```

> Update this tree to match the real repository.

---

## Documentation

The full project report covers:

1. Introduction and problem statement
2. Survey analysis and use case diagrams (21 use cases grouped by MVP)
3. Activity, sequence, and data flow diagrams (Levels 0, 1, 2)
4. Software Requirements Specification (SRS) and user stories
5. Project plan (Gantt chart) and Jira tracking
6. Wireframes and prototype screens (camera/OCR, home, welcome, onboarding, storytelling, SOS and first aid, search and filter, profile and notifications, AI assistant)
7. Technical implementation *(in progress)*

---

## Project Management

- **Tracker:** Jira — "Product MVPs" space
- **Board columns:** To Do → In Progress → Testing → Done
- **Process:** Each task (for example System Analysis, UI/UX, ChatBot, Deep Search) is a ticket with an owner, a sprint, and an MVP tag. The board is reviewed in each sprint review with the supervisor.

---

## Team

| Name |
|---|
| Menna Elsayed Abdelal Elsayed Elshami |
| Manar Samir Ali Mohamed Diab |
| Mina Kirillos Mansour Botros Mansour |
| Tahiea Mohamed Abdelhady Khamis Ali |
| Ghada Ragab Mohamed Abdelaty |
| Ahmad Yasser Ahmad Ibrahim |
| Ahmad Mohamed Fathy |
| Hasnaa Mohamed Mahmoud |

**Supervisor:** Dr. Yasser Fakhry
**Institution:** Faculty of Computer and Data Science, Alexandria University

---

## License

This project is developed as an academic graduation project. Add a license here (for example MIT) if you plan to open-source it.
