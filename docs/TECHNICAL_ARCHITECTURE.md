# Vidhyagra MP — Technical Architecture

**AI-powered, offline-first education and opportunity platform**  
*Learn where you are. Grow from where you stand.*

**Problem Statement 4:** Digital Inclusion for Rural Higher Education  
**Event:** MPOnline Idea & Innovation Hackathon 2026  
**Team:** Team Crusaders  
**Domain:** EdTech | AI | Inclusive Education

## Document Overview

Vidhyagra is designed as an Android-first, offline-first platform that brings learning, assessment, guidance and opportunity discovery into one continuous system.

The technical architecture is built around intermittent connectivity, shared or low-end devices, regional-language requirements, uneven academic foundations and limited ability to navigate multiple education and government systems independently.

The architecture therefore has three priorities:

1. Keep the learner moving when connectivity is unreliable.
2. Use AI where individual guidance is difficult to provide at scale.
3. Keep the system modular enough to expand from a pilot in Madhya Pradesh to wider deployment.

## Technical Approach

Vidhyagra separates the system into a mobile application, local storage and synchronisation layer, central backend, AI layer and supporting external services.

The Flutter mobile application is the learner's primary interface. It handles onboarding, learning, assessments, progress, opportunity discovery and interaction with Narmada AI. Local storage allows important learner information and downloaded content to remain available when the device is temporarily disconnected.

The Supabase backend provides authentication, PostgreSQL database services, file storage and server-side functions. It acts as the controlled connection between the application, the platform's data and external services.

Narmada AI sits above this foundation as the intelligence layer. It uses learner information, assessment results, previous progress and retrieved platform information to provide explanations, tutoring and next-step guidance.

The system also uses Google Cloud Speech services for voice interaction and Firebase for notifications, monitoring and application protection.

The architecture deliberately avoids putting external service credentials or sensitive AI operations directly inside the mobile application. Server-side functions provide a controlled point through which these services can be accessed.

The result is a system in which the learner-facing application, backend, AI services and supporting infrastructure have separate responsibilities and can be developed or changed without rebuilding the entire platform.

## How Vidhyagra Works

### 1. Onboarding Assessment

The student provides basic information such as:

- What they want to achieve
- What they are currently studying
- What they already know
- Preferred language
- Relevant constraints

This gives the system an initial understanding of the learner.

### 2. Find the Starting Point

A short diagnostic assessment checks what the student already understands.

The purpose is to identify whether the student is ready for the intended material or whether prerequisite concepts need to be addressed first.

### 3. Get the Next Thing to Learn

Instead of requiring the student to search through a large course library, Vidhyagra recommends an appropriate lesson, explanation or practice activity based on the learner's current state.

### 4. Learn in a Way That Works

The learner can use different forms of content:

**Read · Listen · Watch · Ask**

Where supported, the student can ask questions, request another explanation or change the language of the explanation.

Downloaded learning material can continue to be used without a live connection.

The cycle then continues. New progress changes the learner's state, which changes what the system recommends next.

### 5. Prove That the Concept Was Understood

Learning is followed by practice and assessment.

If the student is struggling, the system can identify the underlying gap and return to it.

If the student demonstrates understanding, the system can move forward.

This prevents progress from being based only on whether a lesson was opened or completed.

### 6. Keep Track of What Changed

The platform records learning progress, assessment results and areas of weakness.

The learner's state therefore changes as they learn. That updated state affects what Vidhyagra recommends next.

### 7. Find Where Learning Can Lead

Once the learner has a clearer academic or skills profile, the platform can connect that information to relevant pathways such as:

- Higher education
- Scholarships
- Skills
- Documentation
- Jobs and other opportunities

### 8. Take the Next Step

The system moves from information to action.

Depending on the student's situation, that action may be to:

**Apply / Prepare / Learn / Revise / Build a skill / Find an opportunity**

## Technology Architecture

### 1. Client: Mobile Application

The learner-facing application is built using **Flutter 3.x and Dart**.

- **Flutter** — main application layer
- **Riverpod** — application state and dependency management
- **go_router** — structured navigation
- **Drift + SQLite** — local persistence for learner data, progress and cached content
- **Workmanager** — background processing and queued synchronisation when connectivity returns

The mobile application therefore handles both normal connected operation and the parts of the learning experience that need to continue locally.

The application is designed Android-first because Android devices are the primary target for the intended deployment environment.

### 2. Backend: Supabase

Supabase provides the central backend and PostgreSQL database.

It is responsible for:

- User authentication
- Learner profiles
- Course and content information
- Assessment records
- Learning progress
- Opportunity information
- File storage
- Server-side functions
- Vector retrieval

The main database is **PostgreSQL**.

**Supabase Storage** handles course packs, audio and other supporting resources.

**Supabase Edge Functions** provide server-side logic and act as a controlled layer between the application and external APIs or AI services.

**pgvector** is used for semantic retrieval of relevant educational and opportunity information.

### 3. AI Layer: Narmada AI

Narmada AI is the AI layer of Vidhyagra.

The planned model configuration consists of:

#### Gemini 2.5 Flash

Used for:

- Tutoring
- Reasoning
- Personalised guidance
- More complex learner interactions

#### Gemini 2.5 Flash-Lite

Used for:

- High-volume tasks
- Lower-cost routine processing
- Tasks that do not require the full reasoning capability of the primary model

#### Gemini Embedding 2

Used for:

- Creating semantic representations of information
- Retrieval from the Vidhyagra knowledge base
- Supporting the RAG pipeline

The AI layer is separated from the mobile application so that model configuration can change without requiring the learner-facing application to be redesigned.

### 4. External AI Services: Google Cloud

**Google Cloud Speech-to-Text V2** provides speech recognition.

**Google Cloud Text-to-Speech** provides voice output.

Voice pathway:

Student voice → Speech-to-Text → Narmada AI → Text response → Text-to-Speech → Student

Voice is particularly relevant for learners who may find typing less convenient or who prefer interacting in spoken language.

### 5. Firebase Services

Firebase supports the operational requirements around the application.

- **Firebase Cloud Messaging** — notifications and reminders
- **Firebase Analytics** — application usage information
- **Firebase Crashlytics** — crash monitoring and debugging
- **Firebase App Check** — additional protection against unauthorised application requests

These services support the application but do not form the core learning engine.

### 6. Development and Deployment

The development workflow uses:

- **GitHub** — version control and collaboration
- **GitHub Actions** — automated testing and CI/CD
- **Google Play** — Android testing and deployment
- **Vercel** — web-facing deployment where required
- **Google Stitch** — interface design
- **Antigravity, ChatGPT and Claude** — development and reasoning tools

The development tools are replaceable. The learner-facing architecture does not depend on any particular AI-assisted development workflow.

## Technical Risk Mitigation

The architecture has been designed around the technical conditions most likely to affect Vidhyagra in actual use.

### Connectivity

Intermittent internet access is addressed through local storage, downloaded learning material and delayed synchronisation rather than assuming a permanent connection.

### Device Limitations

Low storage and lower-end hardware are addressed through selective caching, lightweight interfaces and content that does not depend entirely on large video files.

### AI Reliability

Narmada AI is not treated as an unquestionable source of information. Retrieval can ground responses in Vidhyagra's own information, while controlled workflows and human escalation can be used where an incorrect answer would have greater consequences.

### Regional-Language Accuracy

Automated translation and re-explanation can introduce errors, particularly in specialised material. Important localised content therefore requires review rather than assuming that generated output is automatically correct.

### P2P Compatibility

Local device-to-device distribution can be useful in low-connectivity environments, but Android device restrictions mean it must be tested across supported hardware rather than assumed to work identically everywhere.

### Service Dependence

Cloud services may become temporarily unavailable. Locally available learning functions are therefore separated from operations that require live backend access.

### API and Data Security

Authentication, server-side credentials, access controls and application protection reduce exposure of learner data and external service credentials.

### AI Cost

Model selection by task, retrieval and appropriate caching provide mechanisms for controlling the cost of high-volume AI usage.

These are actual considerations, not claims that the risks have already been eliminated. The purpose of the design is to ensure that each major failure condition has a corresponding technical response.

## Offline-First Design

Vidhyagra is designed to keep learning functional even when the internet is unavailable.

Courses, essential learning resources and the learner's current progress can be stored locally on the device, allowing students to continue studying, practise and complete supported assessments without maintaining a live connection to the backend.

Actions performed offline are recorded locally and placed in a synchronisation queue. When connectivity becomes available, these changes are sent back to the central system and the learner's progress is updated.

This also allows larger learning resources to be downloaded once and reused instead of requiring repeated mobile data.

Where community nodes are available, course packs can be distributed locally from one connected device to nearby learners through device-to-device transfer.

The result is that connectivity becomes a point of synchronisation rather than a requirement for every learning session.

### Data Flow

**Connected:** App → Backend → AI / Data → App

**Offline:** App → Local content + learner state

**When connection returns:** Local changes → Sync → Backend

## Conclusion

Vidhyagra's architecture separates the learner experience, local operation, central data, AI and supporting services while allowing them to function as one system.

Flutter provides the application layer. Local storage keeps relevant learning and learner state available when connectivity disappears. Supabase provides the central backend. Narmada AI provides tutoring and next-step guidance.

Retrieval connects that intelligence to Vidhyagra's own information, while voice and Firebase services extend the platform where required.

The architecture is deliberately modular. New content can be added without rebuilding the application, AI services can evolve without changing the learner interface, and the same underlying structure can support additional districts, languages and states.

Most importantly, the technical complexity stays behind the interface.

The student should only have to figure out what they need to do next. Vidhyagra's architecture is built to handle the rest.
