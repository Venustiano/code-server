# code-server

A Docker Compose setup for running [linuxserver/code-server](https://docs.linuxserver.io/images/docker-code-server/) with a shared config directory and easy access to sibling project folders.

---

## Directory layout

```
./                          ← repository root (this repo)
├── .gitignore
├── README.md
├── config/                 ← code-server config, auto-populated at first run
├── elasticstacks/          ← example project (sibling folder)
├── fotos-ops-api/          ← example project (sibling folder)
└── code-server/            ← Docker Compose lives here
    ├── docker-compose.yaml
    └── .env                ← environment variables (edit before first run)
```

Every folder that sits next to `code-server/` (i.e. one level up from the
compose file) is mounted inside the container under `/workspaces/`, so you can
open any of them as a workspace without changing the compose file.

| Host path            | Container path                   |
|----------------------|----------------------------------|
| `../config`          | `/config`  (settings, extensions)|
| `..` (root)          | `/workspaces`                    |
| `../elasticstacks`   | `/workspaces/elasticstacks`      |
| `../fotos-ops-api`   | `/workspaces/fotos-ops-api`      |

---

## Quick start

### 1. Clone this repository

```bash
git clone https://github.com/Venustiano/code-server.git
cd code-server
```

### 2. Configure environment variables

Open `code-server/.env` and set at minimum:

| Variable | Description |
|----------|-------------|
| `PUID` / `PGID` | Host user/group IDs (`id` on Linux/macOS) |
| `TZ` | Your timezone (e.g. `America/New_York`) |
| `PASSWORD` | Plain-text password, **or** |
| `HASHED_PASSWORD` | bcrypt hash (safer, takes precedence) |
| `PORT` | Host port to expose (default `8443`) |
| `DEFAULT_WORKSPACE` | Folder opened on start (default `/workspaces`) |

### 3. Start the container

```bash
cd code-server
docker compose up -d
```

Then open **http://localhost:8443** (or the port you configured) in your browser.

### 4. Add more project folders

Just create (or clone) new directories at the repository root – they will
automatically appear under `/workspaces` inside the container without any
compose file changes:

```bash
# from the repository root
git clone https://github.com/your-org/new-project.git
# → accessible at /workspaces/new-project inside code-server
```

### 5. Stop the container

```bash
cd code-server
docker compose down
```

---

## Security notes

* **Do not commit real passwords.** The `.env` file in this repo is a template
  with all values empty. Fill in your secrets locally; they are not pushed to
  Git.
* Prefer `HASHED_PASSWORD` / `SUDO_PASSWORD_HASH` over plain-text values.
* If you expose code-server publicly, set `PROXY_DOMAIN` and put it behind a
  TLS-terminating reverse proxy (e.g. nginx, Caddy, Traefik).

---

## References

* [linuxserver/code-server image docs](https://docs.linuxserver.io/images/docker-code-server/)
* [Docker Compose reference](https://docs.docker.com/compose/)