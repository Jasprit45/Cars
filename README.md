# Cars — Fullstack Demo (React + Vite front-end, Express + Sequelize back-end)

This repository contains a small fullstack project to manage 3D car models and metadata. It includes a React + Vite client that renders 3D assets (using Three / react-three-fiber) and an Express server using Sequelize (MySQL) to persist car data.

**Quick overview**
- **Front-end**: `client/` — React, Vite, Tailwind, Three.js + `@react-three/fiber`.
- **Back-end**: `server/` — Express, Sequelize, MySQL, route controllers, service & repository layers.
- **DB**: Sequelize migrations in `server/migrations/` and model `server/models/car.js`.
- **3D assets**: `server/porsche_911_turbo_996/` contains GLTF and textures used by the client.

**Prerequisites**
- Node.js (16+ recommended)
- npm (comes with Node) or Yarn
- MySQL server (or Dockerized MySQL)

**Project structure (important files)**
- `client/` — React app (entry: `client/src/main.jsx`, `client/package.json`)
- `server/` — Express API (entry: `server/index.js`, `server/package.json`)
- `server/models/car.js` — Sequelize Car model (fields documented below)
- `server/migrations/` — Sequelize migrations for database schema
- `server/seeders/` — seeders (empty scaffold)
- `server/config/config.json` — DB credentials (development/test/production)

Note: There is an existing `client/README.md` scaffold created by Vite; this README documents the full project root and how to run both sides together.

Environment & configuration
- The server loads environment vars from a `.env` file via `dotenv` (see `server/config/serverConfig.js`).
- Database config for environments is in `server/config/config.json` (current development values use `username: root`, `password: jasprit@123`, `database: CARS_DB_DEV`, `host: 127.0.0.1`).
- For safety, move credentials into `.env` and update `config/config.json` or use environment variables for production use.

Database setup (development)
1. Create the development database (example using MySQL CLI / PowerShell):

```powershell
# login to mysql and create DB
mysql -u root -p
-- then run inside mysql: CREATE DATABASE CARS_DB_DEV;
```

2. Run migrations from `server/` (uses `sequelize-cli`):

```powershell
cd server
npx sequelize-cli db:migrate --env development
```

3. (Optional) Run seeders if/when they are added:

```powershell
npx sequelize-cli db:seed:all
```

How to run (development)
- Start the API server (uses `nodemon`):

```powershell
cd server
npm install
npm run start
```

- Start the client dev server (Vite):

```powershell
cd client
npm install
npm run dev
```

Default CORS origin configured in `server/index.js` is `http://localhost:5173` (Vite default). Adjust if you run the client on a different port.

API Reference (base path: `/api/v1`)
- `POST /api/v1/car` — create a car (request body: car fields)
- `GET /api/v1/car` — fetch all cars
- `GET /api/v1/car/:id` — fetch a specific car by id
- `DELETE /api/v1/car/:id` — delete a car by id

Car model fields (see `server/models/car.js`)
- `model_name` (string, unique, required)
- `brand` (string, required)
- `year` (integer, required)
- `price` (decimal, required)
- `engine_type` (string, required)
- `horsepower` (integer, required)
- `image_url` (text, required)
- `model_3d_url` (text, required)
- `scale`, `posx`, `posy`, `posz`, `time`, `range` (numbers used for 3D placement/settings)

Client details
- The client uses `three` + `@react-three/fiber` and `@react-three/drei` to render 3D scenes.
- Key front-end files: `client/src/components/carmodel1.jsx`, `client/src/components/carcontext.jsx`, `client/src/components/page.jsx`.
- Client communicates with the server using `axios` (see `client/package.json`).

Dev tips & notes
- The server expects a MySQL database configured in `server/config/config.json`. For a cleaner setup, switch to environment variables and update `config/config.json` accordingly.
- If you get CORS errors, verify the `origin` in `server/index.js` or set `origin: true` temporarily for development.
- The `server/index.js` has commented-out DB sync logic. To sync models automatically (not recommended for production), you can enable `db.sequelize.sync()` guarded by an env var.

Suggested next steps
- Add a top-level `README.md` (this file) — done.
- Add `.env.example` and update server config to use env vars.
- Add basic API tests (Jest / Supertest) and CI (GitHub Actions).
- Consider Dockerizing the stack (`docker-compose` with MySQL, server, and client) for easy local setup.
