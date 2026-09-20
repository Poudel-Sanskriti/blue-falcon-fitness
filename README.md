# Blue Falcon Fitness

## Overview

Blue Falcon Fitness is a full-stack fitness application developed as a Texas State Software Engineering course project.

Our team built it to address a limitation we saw in many fitness applications: users often receive generic plans that do not account for their actual goals, experience, equipment, injuries, or dietary needs. Blue Falcon Fitness collects this information during onboarding and uses it to create a more personalized experience.

The application includes account management, a fitness quiz, health calculations, workout generation, workout tracking, nutrition tools, reports, real-time chat, and optional local AI features.

This is an educational software project, not a medical device. Its recommendations are not a substitute for professional medical advice.

## My role and contributions

I worked as a full-stack developer across the backend and frontend. My main responsibility was building the onboarding and workout-planning flow that connects a user's information to a personalized plan.

### Onboarding and fitness quiz

I implemented the onboarding feature across the full application stack:

- database models for fitness goals;
- validation schemas;
- CRUD operations;
- FastAPI endpoints;
- the multi-step React quiz;
- height and weight unit conversions;
- input validation and review screens;
- equipment, allergy, limitation, and preference inputs.

I built this feature because the rest of the application needs reliable user information before it can generate meaningful workouts, nutrition plans, or reports.

### Health calculations

I implemented BMI, BMR, and TDEE calculations and connected the results to the user's saved fitness profile.

These calculations provide a consistent baseline for personalization and allow later features to use the same validated data instead of repeating health-related logic in multiple places.

### Workout-plan generation

I implemented the workout-plan models, schemas, CRUD operations, API endpoints, generation logic, and frontend presentation.

The generator considers the user's goal, activity level, available equipment, experience, and schedule. It supports optional local AI generation while retaining a rule-based fallback so the feature can still work when the AI service is unavailable.

### Exercise database integration

I integrated and normalized data from free-exercise-db. The project uses a catalog of 873 exercises that can be filtered by difficulty, equipment, target muscles, and injury-related restrictions.

I added this because a fixed list of hard-coded exercises would limit personalization and make future changes difficult.

### Workout tracking and user experience

I implemented or contributed to:

- set tracking;
- exercise swapping;
- weekly completion state;
- stale-plan detection after quiz updates;
- progress indicators;
- workout history;
- calendar views;
- rest timers;
- exercise animations.

These features turn a generated plan into something users can follow, update, and review over time.

The Git history preserves the complete contribution record for every team member.

## Team

| Contributor | Role | Primary areas |
|---|---|---|
| Shawn Mele | Full Stack | Architecture documentation, sprint planning, and muscle-map integration |
| Yuxi Luo | Backend and AI | WebSocket and Redis chat, AI features, and the original hosting integration |
| Sanskriti Poudel | Full Stack | Onboarding, health calculations, workout planning, tracking, history, timers, and animations |
| Abraham Calzado Estrada | Full Stack | Equipment selection, workout details, profiles, routing, and supplement features |
| Sergio Mendoza | Frontend | React foundation, landing page, design system, dashboard, workout, and nutrition interfaces |

## Core features

- User registration, login, profiles, and JWT authentication.
- Redis-backed token invalidation for logout.
- Personalized onboarding and health calculations.
- Workout generation from a normalized exercise catalog.
- Equipment and injury-aware exercise filtering.
- Rule-based workout generation when local AI is unavailable.
- Workout completion, set tracking, swaps, history, and calendar views.
- Rest timers and exercise animations.
- Nutrition plans and fitness reports.
- Redis-backed background processing for longer AI tasks.
- Real-time chat using WebSockets and Redis Pub/Sub.
- Muscle heat maps, supplement guidance, subscriptions, and administration tools.

## Architecture

The frontend is a React and Vite single-page application. It communicates with a FastAPI backend through HTTP and WebSocket endpoints.

The backend is the source of truth. Protected endpoints identify the current user from a verified JWT rather than trusting a user ID supplied by the frontend.

Application data is stored with SQLModel and SQLite. Redis supports token invalidation, real-time chat, and background task queues. A worker processes longer AI jobs and stores their results for the frontend to retrieve.

```text
User
  |
React and Vite frontend
  |
FastAPI backend
  |-- SQLModel and SQLite
  |-- Redis
       |-- authentication state
       |-- chat Pub/Sub
       `-- background task queues
              |
          worker process
              |
          Ollama model
```

## Technology stack

| Layer | Technologies |
|---|---|
| Frontend | React 19, Vite 7, React Router, Axios, Recharts, and Lottie |
| Backend | Python 3.11+, FastAPI, Pydantic, and SQLModel |
| Data | SQLite, aiosqlite, and Redis |
| Authentication | JWT, passlib, bcrypt, and Redis token state |
| Optional AI | Ollama with a configurable local model |
| Development tools | npm, Uvicorn, FastAPI OpenAPI, and SQLAdmin |

## Getting started

### Requirements

- Python 3.11 or newer
- Node.js 18 or newer
- Redis, installed locally or running in Docker
- Ollama only if local AI generation is needed

### Backend setup

```bash
git clone <repository-url>
cd blue-falcon-fitness

python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

cp .env.example .env
```

On Windows, activate the environment with:

```powershell
.venv\Scripts\activate
```

Replace the placeholder values in `.env`. `SECRET_KEY`, `ADMIN_PASSWORD`, and `PREMIUM_COUPON` do not have public fallback values.

Start Redis with Docker:

```bash
docker run --rm -p 6379:6379 redis:alpine
```

Start the backend:

```bash
uvicorn main:app --reload
```

The API runs at `http://localhost:8000`. Interactive API documentation is available at `http://localhost:8000/docs`.

### Frontend setup

In another terminal:

```bash
cd src/frontend
npm install
npm run dev
```

The frontend normally runs at `http://localhost:5173`.

To use a different backend URL, create `src/frontend/.env`:

```env
VITE_API_URL=http://localhost:8000
```

### Optional local AI

```bash
ollama pull llama3
ollama serve
```

Set `ENABLE_LLM_MODEL=true` in `.env` and restart the backend. Supported features use mock or rule-based behavior when local AI is disabled.

## Project structure

```text
.
|-- docs/                  Project documentation
|-- scripts/               Repository maintenance scripts
|-- src/
|   |-- api/               FastAPI routers
|   |-- core/              Authentication, configuration, calculations, AI, and exercise logic
|   |-- crud/              Database operations
|   |-- data/              Normalized exercise catalog
|   |-- frontend/          React and Vite application
|   |-- model.py           SQLModel database entities
|   |-- schemas.py         API request and response contracts
|   `-- tasks.py           Redis-backed worker
|-- .env.example           Safe configuration template
|-- main.py                Backend entry point
`-- requirements.txt       Python dependencies
```

## Documentation

- [System architecture](docs/architecture.md)
- [Requirements specification](docs/requirements-specification.md)
- [Project management plan](docs/project-management-plan.md)
- [Test plan](docs/test-plan.md)
- [Backend feature-development guide](docs/How_to_build_feature.md)
- [AI feature-development guide](docs/How_to_build_AI_feature.md)
- [Muscle-map guide](docs/muscle-heat-map.md)
- [Engineering onboarding guide](https://gray-clarie-96.tiiny.site/)

## Current limitations

- SQLite is not intended for heavy concurrent production workloads.
- Schema changes require manual SQL because Alembic migrations are not configured.
- Automated test coverage is not yet comprehensive.
- Password-reset email delivery is not implemented.
- The previous custom-domain deployment is currently unavailable.

## Attribution and license

Exercise data is derived from [free-exercise-db](https://github.com/yuhonas/free-exercise-db).

The application was developed collaboratively as a Texas State Software Engineering course project. The original Git history is preserved to retain each contributor's authorship.

No open-source license has been selected. All rights remain with the project contributors unless the team adds an explicit license.
