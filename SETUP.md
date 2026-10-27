# Setup

One-time setup for running every exercise in MySQL and PostgreSQL.

| What | Version | Why |
|---|---|---|
| Docker Desktop | any recent | Runs both databases in containers |
| MySQL | 8.4 LTS (container) | The book uses MySQL 8.x |
| PostgreSQL | 17 (container) | The book requires 15 or later |
| MySQL Workbench | 8.0+ | The GUI the book uses for MySQL (ER diagrams, SQL editor) |
| pgAdmin (optional) | container | GUI for PostgreSQL, including an ERD tool |

---

## 1. Configure your environment

```bash
cp .env.example .env
```

If you already have MySQL or PostgreSQL installed locally on port `3306` or `5432`, change `MYSQL_PORT` / `POSTGRES_PORT` in `.env` (for example to `3307` / `5433`).

## 2. Clone the book's companion repo (needed from Chapter 3)

Chapter 3 onward uses the **ShopSphere** sample dataset from the book's GitHub repo. Clone it into `book-repo/`. That folder is git-ignored, and the containers mount it read-only at `/book`.

```bash
git clone https://github.com/PacktPublishing/Database-Design-and-Modeling-with-PostgreSQL-and-MySQL.git book-repo
```

If you start the containers first, Docker creates an empty `book-repo/` folder. That's fine: `git clone` into an empty folder still works, and the files appear inside the containers straight away.

## 3. Start the databases

```bash
docker compose up -d
```

Wait until both show `healthy`:

```bash
docker compose ps
```

## 4. Connect from the command line

MySQL:

```bash
docker exec -it practice-mysql mysql -uroot -p
```

PostgreSQL:

```bash
docker exec -it practice-postgres psql -U postgres
```

The default password for both is `practice`, unless you changed it in `.env`.

Inside both containers, this repo is mounted read-only at `/workspace`, so you can run a saved script directly:

```bash
docker exec -i practice-mysql mysql -uroot -ppractice < ch02-strong-foundation/mysql/01_example.sql
```

```bash
docker exec -i practice-postgres psql -U postgres -f /workspace/ch02-strong-foundation/postgres/01_example.sql
```

## 5. Connect MySQL Workbench

1. Open Workbench → **+** next to *MySQL Connections*.
2. Connection name: `practice-mysql`
3. Hostname: `127.0.0.1` · Port: `3306` (or your `MYSQL_PORT`) · Username: `root`
4. **Store in Keychain…** → password from `.env` → **Test Connection** → **OK**.

## 6. Connect a PostgreSQL GUI (optional)

**pgAdmin in Docker:**

```bash
docker compose --profile gui up -d
```

Open <http://localhost:5050>, then **Add New Server**:

- Name: `practice-postgres`
- Connection → Host: `postgres` (the service name inside Docker) · Port: `5432` · Username: `postgres` · Password: from `.env`

**Or a desktop client** (pgAdmin desktop, DBeaver, TablePlus): connect to `127.0.0.1`, port `5432` (or your `POSTGRES_PORT`).

## Everyday commands

| Task | Command |
|---|---|
| Start | `docker compose up -d` |
| Stop (keep data) | `docker compose stop` |
| View logs | `docker compose logs -f mysql` |
| Reset everything (deletes all data) | `docker compose down -v` |
