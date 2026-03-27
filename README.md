# AI Agent Analytics Platform

Product analytics designed for conversational AI and AI agents. Tracks where users struggle, drop off, or get confused during AI interactions — filling the gap that traditional analytics tools (built for button-based UIs) leave open.

## Current State

The platform provides a working FastAPI backend with user and item management, React frontend with Tailwind CSS, and SQLite persistence. The analytics-specific features (conversation tracking, drop-off analysis, frustration detection) are defined in the spec and the infrastructure is in place, but the domain-specific endpoints are not yet implemented beyond the scaffolding.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 19, Vite, Tailwind CSS, Shadcn/ui |
| Backend | FastAPI, Python 3.11+, Pydantic v2 |
| Database | SQLite |

## Prerequisites

- Python 3.11+
- Node.js 18+

## Setup

### Quick Start

```bash
git clone https://github.com/m2ai-portfolio/ai-agent-analytics-platform.git
cd ai-agent-analytics-platform
chmod +x init.sh && ./init.sh
```

### Manual Setup

**Backend:**

```bash
cd backend
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

**Frontend:**

```bash
cd frontend
npm install
npm run dev
```

## API Reference

All endpoints are prefixed with `/api`. Auto-generated docs available at `/docs`.

### Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/health` | Health check |

### Users

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/users` | List all users (limit 100) |
| `GET` | `/api/users/{id}` | Get user by ID |

### Items

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/items` | List all items (limit 100) |
| `POST` | `/api/items` | Create a new item |
| `GET` | `/api/items/{id}` | Get item by ID |
| `PUT` | `/api/items/{id}` | Update item (title, description, status) |
| `DELETE` | `/api/items/{id}` | Delete an item |

### Usage Examples

```bash
# Create an item
curl -X POST http://your-server:8000/api/items \
  -H "Content-Type: application/json" \
  -d '{"user_id": "user-1", "title": "Track conversation drop-offs", "status": "active"}'

# List items
curl http://your-server:8000/api/items

# Update item status
curl -X PUT http://your-server:8000/api/items/ITEM_ID \
  -H "Content-Type: application/json" \
  -d '{"status": "completed"}'
```

## Project Structure

```
ai-agent-analytics-platform/
├── backend/
│   ├── main.py           # FastAPI app, CORS, lifespan
│   ├── routes.py          # API endpoints (users + items)
│   ├── models.py          # Pydantic request/response models
│   ├── database.py        # SQLite setup, seeding
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/    # React components
│   │   ├── pages/         # Page views
│   │   └── App.jsx
│   └── package.json
├── app_spec.txt           # Full product specification
└── init.sh                # One-command setup
```

## Development

```bash
# Backend (auto-reload)
cd backend && source venv/bin/activate
uvicorn main:app --reload --port 8000

# Frontend (dev server)
cd frontend && npm run dev

# API docs
# Visit http://your-server:8000/docs
```
