# Database Design & Modeling — Hands-On Practice

My hands-on work while reading **_Database Design and Modeling with PostgreSQL and MySQL_** (Alkin Tezuysal & Ibrar Ahmed, Packt, 2024).

Each chapter folder follows what the authors implement in that chapter. I run every exercise in both **MySQL** and **PostgreSQL**, both in Docker.

- Book companion repo (code + ShopSphere dataset): <https://github.com/PacktPublishing/Database-Design-and-Modeling-with-PostgreSQL-and-MySQL>

---

## Progress

| Chapter | Topic | Guide | MySQL | PostgreSQL | Notes |
|---|---|:---:|:---:|:---:|:---:|
| 1 | SQL and NoSQL databases (theory only) | — | — | — | — |
| 2 | [Building a Strong Foundation for Database Design](ch02-strong-foundation/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 3 | [Getting Your Hands Dirty with PostgreSQL and MySQL](ch03-hands-on-shopsphere/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 4 | [Mastering the Building Blocks of Database Design and Modeling](ch04-building-blocks/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 5 | [Advanced Techniques for Advanced Databases](ch05-advanced-techniques/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 6 | [Understanding Database Scalability](ch06-scalability/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 7 | [Best Practices for Building and Maintaining Your Database](ch07-best-practices/) | ⬜ | ⬜ | ⬜ | ⬜ |
| 8 | [The Future of Databases and Their Designs](ch08-future-of-databases/) | ⬜ | ⬜ | ⬜ | ⬜ |

Legend: ⬜ not started · 🟨 in progress · ✅ done

---

## Repository layout

```
.
├── README.md              ← you are here (progress tracker)
├── SETUP.md               ← one-time environment setup
├── docker-compose.yml     ← MySQL + PostgreSQL (+ optional pgAdmin)
├── .env.example           ← copy to .env and adjust ports/passwords
├── book-repo/             ← (git-ignored) clone of the book's companion repo
└── chXX-<topic>/
    ├── GUIDE.md           ← step-by-step guide (added when I start the chapter)
    ├── NOTES.md           ← my observations, errors hit, answers
    ├── mysql/             ← my MySQL scripts, numbered in run order
    └── postgres/          ← my PostgreSQL scripts, numbered in run order
```

## Quick start

```bash
cp .env.example .env
```

```bash
docker compose up -d
```

```bash
docker compose ps
```

Full instructions, including connecting MySQL Workbench and pgAdmin, are in [SETUP.md](SETUP.md).

## Workflow per chapter

1. Read the chapter in the book.
2. Add the chapter's `GUIDE.md` and work through it step by step.
3. Save each step's SQL in the file the guide names, e.g. `ch02-strong-foundation/mysql/01_unnormalized.sql`.
4. Commit at each checkpoint the guide suggests.
5. Write down what surprised me, errors I hit, and how I fixed them in `NOTES.md`.
6. Update the progress table above.

## Commit message convention

```
chNN: <what I did> (<engine>)
```

Examples:

- `ch02: create unnormalized StudentCourses table (mysql)`
- `ch02: decompose into Student, Course, StudentCourses (postgres)`
- `ch02: notes on 2NF partial dependencies`
