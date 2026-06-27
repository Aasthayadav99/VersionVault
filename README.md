# 📦 VersionVault — Custom Git-Like VCS + Full Stack Repository Hosting

A production-ready version control system built **from scratch** in Node.js (no Git binary, no isomorphic-git wrapper) paired with a full **MERN-stack web application** for repository hosting, commit browsing, and issue tracking. Built to understand how Git actually works under the hood.

---

## 🖼️ Features

- 🔧 **Custom VCS Engine** — `init`, `add`, `commit`, `revert` implemented from ground up using only Node.js File System API
- 📸 **Snapshot-Based Commits** — Each commit stores a complete file snapshot in `.apnaGit/` with UUID-based commit folders
- ☁️ **AWS S3 Remote Sync** — `push`/`pull` commands for remote backup and repository sharing
- 🔐 **JWT Authentication** — Secure signup/login with bcryptjs password hashing
- 🏢 **Full Repository Hosting** — Browse repos, view commit history, manage files via web UI
- 📋 **Issue Tracking** — Create, manage, and track issues across repositories
- ⚡ **Real-Time Updates** — Socket.io integration for live collaboration features
- 📱 **Clean REST API** — Express routes for users, repos, issues, and VCS operations
- 🎨 **Responsive Frontend** — React + Vite interface for seamless git workflow

---

## 🛠️ Tech Stack

| Layer    | Technology                              |
| -------- | --------------------------------------- |
| Frontend | React 18, Vite, React Router, Tailwind |
| Backend  | Node.js, Express.js                     |
| Database | MongoDB + Mongoose                      |
| Auth     | JWT, bcryptjs                           |
| Real-time| Socket.io                               |
| Storage  | AWS S3 (SDK v2), Local `.apnaGit/`      |
| CLI      | yargs                                   |

---

## 📁 Project Structure

```
VersionVault/
├── backend-main/                # Express backend + VCS engine
│   ├── controllers/
│   │   ├── vcs/                 # init, add, commit, revert, push, pull
│   │   ├── auth/                # JWT signup/login
│   │   ├── repositories/        # Repo CRUD
│   │   └── issues/              # Issue management
│   ├── models/                  # Mongoose schemas (User, Repo, Issue)
│   ├── routes/                  # API endpoints
│   ├── middleware/              # Auth middleware, error handling
│   ├── config/                  # AWS S3 configuration
│   └── index.js                 # Entry point (Express + CLI handler)
│
└── frontend-main/               # React + Vite frontend
    ├── src/
    │   ├── components/          # Auth, Dashboard, RepoView, CommitHistory
    │   ├── pages/               # User profile, Repo pages, Issues
    │   ├── hooks/               # Custom React hooks
    │   └── services/            # API client (axios)
    └── index.html
```

---

## 🚀 How the Version Control Engine Works

Unlike traditional Git (which uses diffs and content-addressable objects), VersionVault uses **straightforward file snapshots** — simpler to understand and implement:

| Command | What it does |
|---|---|
| `init` | Creates `.apnaGit/` directory with `commits/` folder and `config.json` (S3 bucket reference) |
| `add <file>` | Copies file into `.apnaGit/staging/` — marks it ready to commit |
| `commit "<msg>"` | Generates UUID, copies all staged files into `.apnaGit/commits/<uuid>/`, writes `commit.json` (message + timestamp) |
| `revert <commitID>` | Looks up commit folder, restores all files to working directory — full snapshot restore |
| `push` | Uploads entire `.apnaGit/commits/` to configured S3 bucket for remote backup |
| `pull` | Downloads commit history from S3 bucket, syncs locally |

Each commit is **fully self-contained** (a complete copy of staged files, not a diff) — trades storage efficiency for simplicity & clarity.

---

## 🔌 API Endpoints

### 🔐 Authentication

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/api/auth/register` | Register a new user |
| POST | `/api/auth/login` | Login & receive JWT token |
| GET | `/api/auth/me` | Get current user profile |
| PUT | `/api/auth/update-profile` | Update user details |

---

### 📦 Repositories

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/repos` | Get all user repositories |
| GET | `/api/repos/:id` | Get repository details |
| POST | `/api/repos` | Create new repository |
| PUT | `/api/repos/:id` | Update repository metadata |
| DELETE | `/api/repos/:id` | Delete repository |

---

### 💾 Version Control Operations

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/api/vcs/:repoId/init` | Initialize repo (create `.apnaGit/`) |
| POST | `/api/vcs/:repoId/add` | Stage file for commit |
| POST | `/api/vcs/:repoId/commit` | Create commit snapshot |
| POST | `/api/vcs/:repoId/revert` | Revert to previous commit |
| POST | `/api/vcs/:repoId/push` | Push commits to S3 |
| POST | `/api/vcs/:repoId/pull` | Pull commits from S3 |
| GET | `/api/vcs/:repoId/history` | View commit history |

---

### 📋 Issues

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/api/repos/:repoId/issues` | List all issues |
| POST | `/api/repos/:repoId/issues` | Create issue |
| PUT | `/api/issues/:id` | Update issue |
| DELETE | `/api/issues/:id` | Delete issue |

---

## ⚙️ Getting Started

### Prerequisites
- **Node.js** (LTS recommended)
- **MongoDB Atlas** cluster (free tier works) or local MongoDB
- **AWS S3 bucket** (optional — only required for `push`/`pull` features)

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/Aasthayadav99/VersionVault.git
cd VersionVault
```

**2. Install backend dependencies**
```bash
cd backend-main
npm install
```

**3. Install frontend dependencies**
```bash
cd ../frontend-main
npm install
```

**4. Configure environment variables**

Create `.env` file inside `backend-main/`:
```env
PORT=3002
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET_KEY=your_random_secret_string

# Optional: AWS S3 (for push/pull features)
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_S3_BUCKET_NAME=your_bucket_name
```

**5. Update AWS S3 bucket name**

Edit `backend-main/config/aws-config.js` with your actual S3 bucket details.

---

## 🎮 Running the App

**Start backend** (from `backend-main/`):
```bash
npm start
```

Backend runs on `http://localhost:3002`

**Start frontend** (from `frontend-main/`, separate terminal):
```bash
npm run dev
```

Frontend runs on `http://localhost:5173`

---

## 💻 Using the CLI

The VCS commands can be run directly from `backend-main/`:

```bash
# Initialize a repository
node index.js init

# Stage files
node index.js add <filename>

# Create a commit
node index.js commit "Your commit message"

# View commit ID (get from history)
node index.js history

# Revert to a specific commit
node index.js revert <commitID>

# Push to S3
node index.js push

# Pull from S3
node index.js pull
```

---

## 📸 Example Workflow

```bash
# Initialize repository
$ node index.js init
✅ Repository initialized. Created .apnaGit/ directory.

# Create/modify files...
$ echo "Hello World" > hello.txt

# Stage the file
$ node index.js add hello.txt
✅ Staged: hello.txt

# Commit
$ node index.js commit "Initial commit with hello.txt"
✅ Commit created: a1b2c3d4-e5f6-4789-0123-456789abcdef

# Later, revert if needed
$ node index.js revert a1b2c3d4-e5f6-4789-0123-456789abcdef
✅ Reverted to commit a1b2c3d4-e5f6-4789-0123-456789abcdef

# Push to S3 for remote backup
$ node index.js push
✅ Pushed commits to S3 bucket: my-vcs-bucket
```

---

## 🔮 Future Enhancements

- 🌳 **Branching & Merging** — Multiple branch support with merge conflict detection
- 📊 **Diff-Based Storage** — Replace snapshots with diffs to reduce redundancy
- 🔄 **Conflict Resolution** — Smart conflict detection on `pull` operations
- 🚀 **AWS SDK v3** — Migrate from v2 to latest AWS SDK
- 🔗 **Collaborative Features** — Real-time file collaboration via Socket.io
- 📈 **Performance Metrics** — Commit compression, storage analytics

---

## 📚 Learning Value

This project demonstrates:
- ✅ From-scratch VCS implementation (no magic Git binary)
- ✅ File system operations & snapshot-based storage
- ✅ Full-stack MERN architecture
- ✅ AWS S3 integration for remote storage
- ✅ JWT authentication flow
- ✅ Real-time updates with Socket.io
- ✅ Building CLI tools with yargs
- ✅ MongoDB schema design for repositories & commits

**Perfect for understanding version control internals without the Git complexity.**

---

##  Acknowledgements

Built as a comprehensive learning project to explore version control systems, full-stack MERN development, and cloud storage integration. Inspired by curiosity about how Git actually works under the hood.

