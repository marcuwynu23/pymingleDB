<div align="center">
  <h1>pymingledb</h1>
</div>

<p align="center">
  <img src="https://img.shields.io/github/stars/mingledb/pymingledb.svg" alt="Stars Badge"/>
  <img src="https://img.shields.io/github/forks/mingledb/pymingledb.svg" alt="Forks Badge"/>
  <img src="https://img.shields.io/github/issues/mingledb/pymingledb.svg" alt="Issues Badge"/>
  <img src="https://img.shields.io/github/license/mingledb/pymingledb.svg" alt="License Badge"/>
</p>

Python implementation of MingleDB: a lightweight file-based NoSQL engine with schema validation, query operators, auth helpers, and single-file `.mgdb` persistence.

## Overview

`pymingledb` is intended for Python applications that need embedded/local-first storage without running a separate DB service.

## Install (uv)

```bash
cd pymingledb
uv sync
```

Or add to your project:

```bash
uv add pymingledb
```

## Features

- User authentication helpers (register/login/logout/session checks)
- Schema rules (`required`, `type`, `unique`)
- Query operators (`$gt`, `$gte`, `$lt`, `$lte`, `$eq`, `$ne`, `$in`, `$nin`, `$regex`)
- Single `.mgdb` database file for all collections

## Usage

```python
from pymingledb import MingleDB, ValidationError, UsernameExistsError, AuthFailedError

db = MingleDB("./mydb")  # directory -> ./mydb/database.mgdb
# db = MingleDB("./mydb/app.mgdb")  # explicit single-file path

# Schema (optional)
db.define_schema("users", {
    "name": {"type": "string", "required": True},
    "email": {"type": "string", "required": True, "unique": True},
    "age": {"type": "number"},
})

# CRUD
db.insert_one("users", {"name": "Alice", "email": "alice@example.com", "age": 30})
db.insert_one("users", {"name": "Bob", "email": "bob@example.com", "age": 17})

db.find_all("users")
db.find_one("users", {"email": "alice@example.com"})
db.find("users", {"age": {"$gte": 18, "$lt": 60}})
db.find("users", {"name": {"$regex": "ali", "$options": "i"}})
db.find("users", {"email": {"$in": ["alice@example.com", "bob@example.com"]}})

db.update_one("users", {"name": "Alice"}, {"age": 31})
db.delete_one("users", {"email": "bob@example.com"})

# Auth (uses internal _auth collection)
db.register_user("admin", "secure123")
db.login("admin", "secure123")
db.is_authenticated("admin")  # True
db.logout("admin")

# Reset (wipe database file and schemas)
db.reset()
```

## Exceptions

- `MingleDBError` — base
- `UsernameExistsError` — register with existing username
- `AuthFailedError` — login failed
- `ValidationError` — schema validation (required, type, unique)

## File format

All collections are stored in one `.mgdb` database file, compatible with mingleDB (JS) and gomingleDB (Go). Internal file layout details are intentionally abstracted from user-facing docs.

## Tests

```bash
uv sync --extra dev
uv run pytest tests/ -v
```

## Community Standards

- Contribution guide: [`CONTRIBUTING.md`](./CONTRIBUTING.md)
- Code of conduct: [`CODE_OF_CONDUCT.md`](./CODE_OF_CONDUCT.md)
- Changelog: [`CHANGELOG.md`](./CHANGELOG.md)
- License: [`LICENSE`](./LICENSE)
- Funding: [`.github/FUNDING.yml`](./.github/FUNDING.yml)
- Bug reports and feature requests: [Issue templates](./.github/ISSUE_TEMPLATE/)
- Pull requests: [PR template](./.github/pull_request_template.md)

## License

Use under the same terms as mingleDB / gomingleDB.
