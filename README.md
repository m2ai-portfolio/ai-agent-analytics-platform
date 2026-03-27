# AI Agent Analytics Platform

Full-stack analytics platform for tracking and managing AI agent work items. Provides a dashboard with real-time status tracking, CRUD operations with optimistic UI updates, and a RESTful API backed by SQLite. Built for monitoring where users struggle and drop off in AI agent interactions.

## Features

- **Dashboard with Live Stats** -- Summary cards showing total, active, and completed item counts with automatic data refresh
- **Item Management** -- Full CRUD (create, read, update, delete) with modal forms, status badges, and user assignment
- **Optimistic UI Updates** -- Immediate visual feedback on create/update/delete with automatic rollback on API failure
- **User-Item Association** -- Items are assigned to users with foreign key integrity and user details merged into item views
- **Toast Notifications** -- Success/error feedback on all mutating operations
- **API Health Indicator** -- Real-time backend connection status displayed in the header
- **Auto-Seeded Dev Data** -- Database populates with sample users and items on first run for immediate exploration

## Prerequisites

- Python 3.12+
- Node.js 18+
- npm

## Quick Start

```bash
git clone https://github.com/m2ai-portfolio/ai-agent-analytics-platform.git
cd ai-agent-analytics-platform
chmod +x init.sh
./init.sh
```

This installs all dependencies and starts both servers:

- **Frontend UI**: `http://your-server:3001`
- **Backend API**: `http://your-server:8000`
- **Swagger Docs**: `http://your-server:8000/docs`

Press `Ctrl+C` to stop all services.

## Manual Setup

### Backend

```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python3 main.py
```

The backend runs on port 8000 with auto-reload enabled.

### Frontend

```bash
cd frontend
npm install --include=dev
npm run dev
```

The Vite dev server runs on port 3001 and proxies all `/api` requests to the backend at `http://localhost:8000`.

## API Reference

All endpoints are prefixed with `/api`.

### Users

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/users` | List all users (limit 100) |
| `GET` | `/api/users/{user_id}` | Get user by ID |

### Items

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/items` | List all items (limit 100) |
| `POST` | `/api/items` | Create a new item |
| `GET` | `/api/items/{item_id}` | Get item by ID |
| `PUT` | `/api/items/{item_id}` | Update an item (title, description, status) |
| `DELETE` | `/api/items/{item_id}` | Delete an item |

### Utility

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/` | App info and version |
| `GET` | `/api/health` | Health check |

## Usage Examples

### Create an Item

```bash
curl -X POST http://your-server:8000/api/items \
  -H "Content-Type: application/json" \
  --data-raw '{
    "user_id": "user-1",
    "title": "Implement Conversation Analytics",
    "description": "Add drop-off rate tracking per conversation turn",
    "status": "active"
  }'
```

Response:

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "user_id": "user-1",
  "title": "Implement Conversation Analytics",
  "description": "Add drop-off rate tracking per conversation turn",
  "status": "active",
  "created_at": "2026-03-26T15:30:00"
}
```

### Update an Item

```bash
curl -X PUT http://your-server:8000/api/items/item-1 \
  -H "Content-Type: application/json" \
  --data-raw '{
    "status": "completed",
    "title": "Complete Project Setup"
  }'
```

Only `title`, `description`, and `status` are updatable fields. Other fields are rejected with a 400 error.

### List All Items

```bash
curl http://your-server:8000/api/items
```

### Delete an Item

```bash
curl -X DELETE http://your-server:8000/api/items/item-1
```

### List Users

```bash
curl http://your-server:8000/api/users
```

### Health Check

```bash
curl http://your-server:8000/api/health
```

## Item Statuses

Items support three status values:

| Status | Description |
|--------|-------------|
| `active` | Currently in progress (default) |
| `pending` | Queued, not yet started |
| `completed` | Successfully finished |

## Project Structure

```
ai-agent-analytics-platform/
├── backend/
│   ├── main.py              # FastAPI app, CORS config, lifespan (DB init + seed)
│   ├── routes.py            # All API endpoints (users, items CRUD)
│   ├── models.py            # Pydantic request/response schemas
│   ├── database.py          # SQLite connection manager, schema init, seed data
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Card.tsx           # Reusable card wrapper
│   │   │   ├── ErrorMessage.tsx   # Error display with retry button
│   │   │   ├── ItemCard.tsx       # Item summary card for grid view
│   │   │   ├── ItemForm.tsx       # Create/edit form with user dropdown
│   │   │   ├── LoadingSpinner.tsx # Loading state indicator
│   │   │   ├── Modal.tsx          # Overlay modal container
│   │   │   ├── StatusBadge.tsx    # Color-coded status label
│   │   │   └── Toast.tsx          # Success/error toast notifications
│   │   ├── lib/
│   │   │   └── api.ts             # Typed API client with timeout + error handling
│   │   ├── pages/
│   │   │   ├── Dashboard.tsx      # Main view: stats, item grid, create modal
│   │   │   └── ItemDetail.tsx     # Detail view: edit, delete, status tracking
│   │   ├── App.tsx                # App shell with nav and page routing
│   │   └── main.tsx
│   ├── package.json
│   ├── vite.config.ts             # Dev server on port 3001, API proxy to 8000
│   └── tsconfig.json
├── init.sh                        # One-command setup + start
└── package.json                   # Root (Playwright + Puppeteer for E2E)
```

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI 0.115, Python 3.12+, Pydantic 2.9, uvicorn |
| Frontend | React 19, TypeScript 5.9, Vite 7, Tailwind CSS 4 |
| Database | SQLite (auto-created at `backend/ai_agent_analytics.db`) |
| Linting | ESLint 9, typescript-eslint |
| E2E Testing | Playwright, Puppeteer |

## Seed Data

On first startup, the database is populated with:

**Users:**
- Alice Johnson (`user-1`)
- Bob Smith (`user-2`)
- Charlie Davis (`user-3`)

**Items:** 6 sample items across all three status types, assigned to the seed users.

## Development

### Frontend Commands

```bash
cd frontend
npm run dev       # Start dev server with hot reload
npm run build     # TypeScript check + production build
npm run lint      # Run ESLint
npm run preview   # Preview production build
```

### Backend

The backend auto-reloads on file changes when started with `python3 main.py`. The SQLite database file is created automatically in the `backend/` directory.

## License

MIT
