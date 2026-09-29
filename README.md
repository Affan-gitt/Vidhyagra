# Vidhyagra

**Learn where you are. Grow from where you stand.**

Vidhyagra is an **AI-powered, offline-first education and opportunity platform** built for students across Madhya Pradesh and India. Powered by **Narmada AI**, it connects personalized learning, AI tutoring, skills, scholarships, higher-education pathways, documentation support, and career opportunities in one student-first system.

> **Core principle:** The student should not have to understand the education system in order to use it. The system should understand the student.

## 30-second explanation

Rural and underserved students do not face a single education problem. Connectivity, device limitations, language, teacher availability, financial constraints, lack of awareness, documentation barriers, and weak career guidance compound each other.

**Vidhyagra treats these as one connected journey.**

The platform understands a student's level, goals, constraints, and interests; builds a personalized path; teaches and assesses through **Narmada AI**; works offline and syncs when connectivity returns; and then connects learning to scholarships, higher education, skills, credentials, and employment opportunities.

## The problem

A student may have access to a phone but still lack:
- reliable internet or affordable data
- consistent access to teachers
- personalized academic support
- awareness of scholarships and opportunities
- confidence navigating higher education
- documentation and digital-service support
- career and job-readiness guidance
- learning resources in a comfortable language or modality

Most digital education products address only the classroom. Vidhyagra connects the **learning system around the student**.

## The solution

Vidhyagra is organized around five layers:

| Layer | What it does |
|---|---|
| **Understand** | Builds a student profile from goals, level, constraints, interests, and progress |
| **Learn** | Delivers adaptive lessons, practice, assessment, revision, and mastery |
| **Guide** | Uses Narmada AI for tutoring, doubts, planning, and next-step guidance |
| **Connect** | Surfaces scholarships, higher-education pathways, skills, and career opportunities |
| **Enable** | Supports documentation, digital literacy, low-connectivity access, and delayed synchronization |

## Why Vidhyagra is different

| Conventional approach | Vidhyagra |
|---|---|
| Content-first | Student-first |
| Requires reliable connectivity | Offline-first with delayed sync |
| Student must search for resources | Conversational entry point discovers needs |
| Learning separated from opportunities | Learning connected to scholarships, pathways and jobs |
| Generic recommendations | Student model + adaptive learning |
| Teacher required for every interaction | AI handles day-to-day support; teachers retain oversight and intervention |
| Single-language interaction | Regional-language and voice-ready architecture |
| Education as an app | Education + skills + opportunities + enablement ecosystem |

## Core user journey

```text
Student
  ↓
Conversational entry point
  ↓
Understand student: level + goals + constraints + interests
  ↓
Personal learning & opportunity path
  ↓
Teach → Practice → Assess
  ↓
Measure progress
  ↓
Update student model
  ↓
Recommend next action
  ↓
Mastery → Skills → Credentials
  ↓
Higher education / Scholarships / Careers
```

The loop is continuous rather than a one-time course recommendation.

## Narmada AI

Narmada AI is the platform's AI orchestration layer. It is designed to be provider-independent rather than coupling the product directly to one model.

```text
Flutter App
    ↓
Supabase Edge Function
    ↓
Narmada AI Orchestrator
    ├── Student Context
    ├── Conversation / Intent
    ├── Retrieval
    ├── Safety / Validation
    └── Model Adapter
            ↓
      Gemini 2.5 Flash
      Gemini 2.5 Flash-Lite
            ↓
      Response + next action
```

### Retrieval-augmented generation

Relevant platform knowledge is embedded using **Gemini Embedding 2** and stored in **Supabase PostgreSQL + pgvector**. Retrieval provides grounded context to the model before generation.

This architecture supports future model providers without redesigning the Flutter application.

## Offline-first architecture

The local application uses **Drift + SQLite** as the source of truth while offline.

```text
UI
 ↓
Riverpod / Application Logic
 ↓
Drift + SQLite
 ↓
Sync Queue
 ↓
Connectivity Detection
 ↓
Supabase
 ↓
PostgreSQL
```

When connectivity returns, queued changes are synchronized. The design aims to minimize data usage and tolerate intermittent connectivity rather than assuming always-online access.

See:
- [System architecture](assets/system-architecture.svg)
- [Offline-first architecture](assets/offline-first.svg)
- [AI architecture](assets/ai-architecture.svg)

## Technology stack

| Area | Technology |
|---|---|
| Mobile | Flutter 3.x + Dart |
| State | Riverpod |
| Navigation | go_router |
| Local database | Drift + SQLite |
| Sync | Custom sync engine + Workmanager |
| Backend | Supabase |
| Database | PostgreSQL |
| Vector search | pgvector |
| Authentication | Supabase Auth |
| Storage | Supabase Storage |
| Server logic | Supabase Edge Functions |
| Primary AI | Gemini 2.5 Flash |
| High-volume AI | Gemini 2.5 Flash-Lite |
| Embeddings | Gemini Embedding 2 |
| Speech | Google Cloud Speech-to-Text V2 |
| Voice output | Google Cloud Text-to-Speech |
| Notifications | Firebase Cloud Messaging |
| Analytics | Firebase Analytics |
| Stability | Firebase Crashlytics |
| App protection | Firebase App Check |
| UI design | Google Stitch |
| Development | Google Antigravity + ChatGPT + Claude |
| Source control | GitHub + GitHub Actions |
| Testing | Google Play internal testing |

## Repository structure

```text
Vidhyagra/
├── app/
│   ├── lib/
│   ├── test/
│   └── pubspec.yaml
├── supabase/
│   └── migrations/
├── assets/
│   ├── system-architecture.svg
│   ├── offline-first.svg
│   └── ai-architecture.svg
├── docs/
│   ├── system-architecture.md
│   ├── offline-first.md
│   └── ai-architecture.md
├── .github/
│   └── workflows/
├── .env.example
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Prototype / demo

**Repository:** https://github.com/Affan-gitt/Vidhyagra

Prototype and presentation links will be added as the corresponding builds are finalized.

## Getting started

### Prerequisites

- Flutter 3.x
- Dart
- Android Studio / Android SDK
- A Supabase project for backend integration
- Firebase project for notification/analytics services
- Gemini API access for AI features

### Run

```bash
git clone https://github.com/Affan-gitt/Vidhyagra.git
cd Vidhyagra/app
flutter pub get
flutter run
```

Environment variables belong in a local `.env` file. **Never commit production credentials.** Use `.env.example` as the configuration template.

## Database

The initial Supabase migration is included under:

```text
supabase/migrations/0001_initial_schema.sql
```

The schema is intended to evolve with the prototype as student profiles, learning content, progress, opportunities, synchronization metadata, and AI retrieval requirements are implemented.

## Security

The prototype follows a least-privilege approach:
- secrets are kept outside source control
- backend operations are isolated behind Supabase services / Edge Functions
- client-side code does not contain privileged database credentials
- authentication and authorization are enforced at the backend
- AI requests are routed through an orchestration layer rather than exposing provider credentials in the app

## Roadmap

### Phase 1 — Prototype
- Core Flutter shell
- Student onboarding
- Local-first data layer
- Initial learning flow
- Narmada AI tutoring
- Supabase integration

### Phase 2 — Personalization
- Student model
- Adaptive assessment
- Mastery tracking
- RAG knowledge layer
- Regional-language and voice interaction

### Phase 3 — Opportunity layer
- Scholarship discovery
- Higher-education pathways
- Skills and credentials
- Career and employment guidance
- Documentation assistance

### Phase 4 — Scale
- Community / institution deployment
- Better offline content distribution
- Multi-state expansion
- Additional languages and dialects
- Provider-independent AI scaling

## Project status

**Active prototype / hackathon build.**

The architecture is deliberately designed so the prototype can evolve into a production platform without replacing the core offline-first, student-model, AI-orchestration, and opportunity-discovery foundations.

## Team

**Team Vidhyagra**

Built for the **Digital Inclusion for Rural Higher Education** problem statement.

## License

MIT License. See [LICENSE](LICENSE).

---

> **Vidhyagra is not another content library. It is an attempt to make the education system navigable, personalized, and resilient for the student.**
