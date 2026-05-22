# 3-Tier Todo App

A full-stack todo application with a React/Vite frontend, FastAPI backend, and PostgreSQL database via SQLAlchemy.

```
.
├── docker-compose.yml   # PostgreSQL + pgAdmin services
├── backend/             # FastAPI + SQLAlchemy
│   ├── main.py
│   ├── models.py
│   ├── schemas.py
│   ├── database.py
│   ├── requirements.txt
│   └── .env
└── frontend/            # React + Vite
    ├── index.html
    ├── vite.config.js
    ├── package.json
    └── src/
        ├── main.jsx
        └── App.jsx
```

---

## Database Services (PostgreSQL + pgAdmin)

Before running the backend, start the database containers from the parent folder:

```bash
docker-compose up -d
```

This will pull and start:

- **PostgreSQL** → main application database
- **pgAdmin** → database management interface

You can access **pgAdmin** at:

```txt
http://localhost:5000
```

---

## Backend

### Requirements

- Python 3.10+

### Setup

```bash
cd backend

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Environment Variables

Create a `.env` file inside the `backend/` folder:

```env
DATABASE_URL=postgresql://<username>:<password>@localhost:5432/<database_name>
```

Example:

```env
DATABASE_URL=postgresql://postgres:password@localhost:5432/todos
```

### Run

```bash
uvicorn main:app --reload
```

The API will be available at `http://localhost:8000`

Interactive docs:

```txt
http://localhost:8000/docs
```

### Endpoints

| Method | Path          | Description    |
| ------ | ------------- | -------------- |
| GET    | `/todos`      | List all todos |
| POST   | `/todos`      | Create a todo  |
| PATCH  | `/todos/{id}` | Update a todo  |
| DELETE | `/todos/{id}` | Delete a todo  |

---

## Frontend

### Requirements

- Node.js 18+

### Setup

```bash
cd frontend
npm install
```

### Run

```bash
npm run dev
```

The app will be available at:

```txt
http://localhost:5173
```

> The backend must be running for the frontend to work.

### Build for production

```bash
npm run build   # output goes to frontend/dist/
npm run preview # preview production build locally
```

---

## Running the Full Stack

Open **three terminals**:

### Terminal 1 — Database

```bash
docker-compose up -d
```

### Terminal 2 — Backend

```bash
cd backend
source .venv/bin/activate
uvicorn main:app --reload
```

### Terminal 3 — Frontend

```bash
cd frontend
npm run dev
```

Then open:

- Frontend → `http://localhost:5173`
- Backend Docs → `http://localhost:8000/docs`
- pgAdmin → `http://localhost:5000`
