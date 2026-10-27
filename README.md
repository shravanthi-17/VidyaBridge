# VidyaBridge AI

**Offline-first, vernacular, adaptive vocational skilling for rural youth.**
Theme: Education and Skill Enhancement · Team: The Solutionist (HSC|TN|02711) · State: Tamil Nadu

VidyaBridge AI delivers short vocational skill modules in Tamil and English. Content is packaged into
offline bundles, learner progress is stored locally, and an adaptive engine chooses the next lesson
from quiz results. Data is synced in batches only when a connection is available.

## Repository layout

```
.
├── src/vidyabridge/        # Core Python package
│   ├── content.py          # Load and validate offline content bundles
│   ├── adaptive.py         # Adaptive lesson selection engine
│   ├── storage.py          # Local SQLite progress store
│   ├── sync.py             # Batched upload of pending progress (when online)
│   ├── certificate.py      # Offline certificate with verifiable code
│   └── cli.py              # Command-line entry point
├── data/sample/            # Sample content bundle and learner data
├── tests/                  # pytest test suite
├── docs/                   # Architecture and technical design
├── .env.example            # Environment variable template
├── requirements.txt
└── LICENSE (MIT)
```

## Prerequisites

- Python 3.10 or newer
- Git
- (Optional) SQLite CLI for inspecting the local database

## Setup

```bash
git clone https://github.com/shravanthi-17/VidyaBridge.git
cd VidyaBridge

python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt   # installs the package in editable mode

cp .env.example .env               # then edit the values as needed
export VB_CERT_SECRET=$(python -c "import secrets;print(secrets.token_hex(32))")  # or set it in .env
```

## Environment variables

| Variable | Required | Default | Purpose |
|---|---|---|---|
| `VB_DB_PATH` | No | `./data/local/progress.db` | Path to the local SQLite progress database |
| `VB_CONTENT_DIR` | No | `./data/sample` | Folder containing content bundles |
| `VB_DEFAULT_LANG` | No | `ta` | Default language (`ta` Tamil, `en` English) |
| `VB_SYNC_URL` | No | empty | Central sync endpoint. Leave empty to stay fully offline |
| `VB_SYNC_TOKEN` | Only if `VB_SYNC_URL` is set | empty | Bearer token for the sync endpoint |
| `VB_CERT_SECRET` | Yes, for certificates | empty | Secret used to sign certificate codes. Use a long random value |

Sync is optional. If `VB_SYNC_URL` is empty the app runs entirely offline.

## Usage

```bash
# Validate a content bundle
python -m vidyabridge.cli validate data/sample/bundle_ta_electrical.json

# Start a learner session (quiz-driven lesson selection)
python -m vidyabridge.cli learn --learner L001 --bundle data/sample/bundle_ta_electrical.json

# Show a learner's progress
python -m vidyabridge.cli progress --learner L001

# Sync pending progress (requires VB_SYNC_URL)
python -m vidyabridge.cli sync

# Issue a completion certificate
python -m vidyabridge.cli certify --learner L001 --bundle data/sample/bundle_ta_electrical.json
```

## Running tests

```bash
pytest -v
```

Tests cover content validation, adaptive selection, local storage, sync batching and
certificate signing. They run offline and need no environment variables except `VB_CERT_SECRET`,
which the test suite sets itself.

## Documentation

- [Technical Design Document](docs/TECHNICAL_DESIGN.md), including architecture diagrams
- [Sample data description](data/sample/README.md)

## Scope of the current prototype

Implemented in this repository: content bundle handling, adaptive engine, offline storage,
batched sync, certificates, CLI and tests.

Planned for the next stage: the Android client (Flutter), on-device speech input, and
dialect-specific content review. These are described in the design document.

## License

Released under the MIT License. See [LICENSE](LICENSE).
