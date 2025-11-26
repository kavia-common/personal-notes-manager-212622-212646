# notes_db (MySQL)

This container provisions a MySQL database for the Notes app and applies schema migrations on startup. It is safe to re-run (idempotent).

Environment defaults:
- DB: myapp
- App user: appuser / dbuser123
- Port: 5000

Schema summary:
- users
  - id BIGINT PK, auto-increment
  - email VARCHAR(255) UNIQUE NOT NULL
  - password_hash VARCHAR(255) NOT NULL
  - display_name VARCHAR(100) NULL
  - created_at, updated_at timestamps
- notes
  - id BIGINT PK, auto-increment
  - user_id BIGINT FK -> users(id), cascade on delete/update
  - title VARCHAR(255) NOT NULL
  - content TEXT NOT NULL
  - is_archived TINYINT(1) DEFAULT 0
  - created_at, updated_at timestamps

Files:
- migrations/001_init.sql: initial schema migration (idempotent; uses CREATE TABLE IF NOT EXISTS)
- schema.sql: snapshot of current schema (for tooling/inspection)
- startup.sh: starts MySQL, ensures DB and users, then applies all SQL files in migrations/ in order

Usage:
1) Run startup.sh from this directory to initialize MySQL and apply migrations.
2) Connection string is saved to db_connection.txt
3) To view data with the bundled viewer:
   - source db_visualizer/mysql.env
   - from db_visualizer/, run: npm start

Notes:
- Migrations are applied in filename order. Ensure new migrations follow a zero-padded numeric prefix (e.g., 002_add_index.sql).
- Each migration must be idempotent (use IF EXISTS/IF NOT EXISTS or guards) to support repeated runs.
