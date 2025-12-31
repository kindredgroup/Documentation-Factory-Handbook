# Slice 0.4: Secrets & Environment Management

**Phase:** 0 – Foundation  
**Status:** Ready  
**Estimated Time:** 45–60 minutes  
**Primary Owner:** Platform / Security (Dev Lead executes)

---

## 1. Purpose
Establish secure patterns for managing credentials, API keys, and environment-specific configuration:
- `.env` and `.env.example` pattern for local dev
- Environment configuration files (non-secret) with a config loader
- Pre-commit secret scanning guardrails
- Azure Key Vault strategy captured as ADR + runbook references

This prevents:
- Secrets committed to Git
- Hardcoded keys
- Credential sprawl
- Compliance/security incidents

---

## 2. Architectural Context
- **Depends on:** Slice 0.3 (Documentation framework complete)
- **Related ADRs:** ADR-002 (Key Vault secrets strategy) and any existing ADRs covering repo standards
- **Enables:** Infrastructure slices that require secrets, CI/CD pipelines, multi-env deployments

---

## 3. Prerequisites

### Completed Slices
- [x] Slice 0.1: Development Environment Setup
- [x] Slice 0.2: GitHub Organization & Repository Setup
- [x] Slice 0.3: Documentation Framework

### Access Required
- [ ] Local repo access
- [ ] Ability to create PRs
- [ ] Python environment (for pre-commit tooling)

---

## 4. What You Will Build
| Component | Description |
|---|---|
| `.env.example` | Template for required environment variables (committed) |
| `.gitignore` updates | Ensure `.env*` is ignored |
| `config/*.json` | Base config + per-environment overrides (non-secret) |
| `src/utils/config_loader.py` | Loads base + env overlay and supports `${ENV_VAR}` |
| `.pre-commit-config.yaml` | Automated checks including secret detection |
| `.secrets.baseline` | detect-secrets baseline file |

---

## 5. Step-by-Step Procedure

### Step 5.1 — Add `.env` pattern and template
**Objective:** Local secrets are stored outside Git; repo contains only a template.

**Commands / Actions:**
```bash
# Verify .env is ignored
grep -n "^\.env$" .gitignore || true

# Ensure ignore patterns exist (idempotent approach recommended)
printf "\n.env\n.env.local\n.env.*.local\n" >> .gitignore
````

Create `/.env.example` in repo root:

```dotenv
# =============================================================================
# Documentation Factory - Environment Variables Template
# =============================================================================
# SETUP:
# 1) cp .env.example .env
# 2) Fill values locally
# 3) NEVER commit .env
# =============================================================================

# --- Azure Authentication ---
AZURE_SUBSCRIPTION_ID=
AZURE_TENANT_ID=
AZURE_REGION=uksouth

AZURE_CLIENT_ID=
AZURE_CLIENT_SECRET=

# --- Azure Resources ---
RESOURCE_GROUP_NAME=

AZURE_OPENAI_ENDPOINT=
AZURE_OPENAI_API_KEY=
AZURE_OPENAI_DEPLOYMENT_GPT4=
AZURE_OPENAI_DEPLOYMENT_GPT4O=

DOCUMENT_INTELLIGENCE_ENDPOINT=
DOCUMENT_INTELLIGENCE_API_KEY=
DOCUMENT_INTELLIGENCE_MODEL_ID=

AZURE_SEARCH_ENDPOINT=
AZURE_SEARCH_API_KEY=
AZURE_SEARCH_INDEX_NAME=

APPLICATIONINSIGHTS_CONNECTION_STRING=

AZURE_KEY_VAULT_NAME=
AZURE_KEY_VAULT_URI=

AZURE_STORAGE_ACCOUNT_NAME=
AZURE_STORAGE_CONNECTION_STRING=

COSMOS_DB_ENDPOINT=
COSMOS_DB_KEY=

# --- Confluence Integration ---
CONFLUENCE_BASE_URL=
CONFLUENCE_API_TOKEN=
CONFLUENCE_USERNAME=
CONFLUENCE_SPACE_KEY=

# --- Microsoft 365 / SharePoint / Dataverse ---
SHAREPOINT_SITE_URL=
SHAREPOINT_SITE_ID=
SHAREPOINT_DRIVE_ID=

DATAVERSE_ENVIRONMENT_URL=
DATAVERSE_CLIENT_ID=
DATAVERSE_CLIENT_SECRET=

# --- Development Settings ---
ENVIRONMENT=dev
LOG_LEVEL=INFO
USE_LOCAL_MODELS=false
ENABLE_TELEMETRY=true

# --- GitHub / CI ---
GITHUB_TOKEN=
GITHUB_REPOSITORY=

# --- Feature Flags ---
FEATURE_ENABLE_GENERATOR_AGENT=false
FEATURE_ENABLE_AUTO_SYNC=false
FEATURE_ENABLE_GROUNDEDNESS_CHECK=true
```

Create local `.env` (DO NOT COMMIT):

```bash
cp .env.example .env
git status .env
# Expected: file does not appear (ignored)
```

**Expected Outcome:**

* `.env` ignored
* `.env.example` committed
* Team can bootstrap local config safely

---

### Step 5.2 — Add environment-specific configuration files

**Objective:** Non-secret config lives in version control; secrets are injected via environment variables.

Create config directory:

```bash
mkdir -p config src/utils
```

Create config files (no secrets):

* `config/base.json`
* `config/dev.json`
* `config/test.json`
* `config/stage.json`
* `config/prod.json`

Create `src/utils/config_loader.py`:

```python
"""Configuration loader with environment variable support."""
import json
import os
from pathlib import Path
from typing import Any, Dict


class ConfigLoader:
    """Load and merge configuration from JSON files and environment variables."""

    def __init__(self, config_dir: str = "config"):
        self.config_dir = Path(config_dir)
        self._config: Dict[str, Any] = {}

    def load(self, environment: str | None = None) -> Dict[str, Any]:
        if environment is None:
            environment = os.getenv("ENVIRONMENT", "dev")

        base_config = self._load_json_file("base.json")
        env_config = self._load_json_file(f"{environment}.json")

        merged = self._deep_merge(base_config, env_config)
        merged = self._replace_env_vars(merged)

        self._config = merged
        return merged

    def get(self, key_path: str, default: Any = None) -> Any:
        keys = key_path.split(".")
        value: Any = self._config
        for key in keys:
            if isinstance(value, dict) and key in value:
                value = value[key]
            else:
                return default
        return value

    def _load_json_file(self, filename: str) -> Dict[str, Any]:
        filepath = self.config_dir / filename
        if not filepath.exists():
            raise FileNotFoundError(f"Config file not found: {filepath}")
        with open(filepath, "r", encoding="utf-8") as f:
            return json.load(f)

    def _deep_merge(self, base: Dict[str, Any], override: Dict[str, Any]) -> Dict[str, Any]:
        result = base.copy()
        for key, value in override.items():
            if key == "extends":
                continue
            if key in result and isinstance(result[key], dict) and isinstance(value, dict):
                result[key] = self._deep_merge(result[key], value)
            else:
                result[key] = value
        return result

    def _replace_env_vars(self, config: Any) -> Any:
        if isinstance(config, dict):
            return {k: self._replace_env_vars(v) for k, v in config.items()}
        if isinstance(config, list):
            return [self._replace_env_vars(item) for item in config]
        if isinstance(config, str) and config.startswith("${") and config.endswith("}"):
            env_var = config[2:-1]
            return os.getenv(env_var, config)
        return config


config = ConfigLoader()
```

**Expected Outcome:**

* `config/` exists with base + env overlays
* loader resolves `${ENV_VAR}` at runtime

---

### Step 5.3 — Install pre-commit hooks to prevent secret leaks

**Objective:** Block accidental commits of keys/tokens.

Add packages:

```bash
printf "\npre-commit>=3.5.0\ndetect-secrets>=1.4.0\n" >> requirements.txt
pip install -r requirements.txt
```

Create baseline and install hooks:

```bash
detect-secrets scan > .secrets.baseline
pre-commit install
pre-commit run --all-files
```

Test secret blocking:

```bash
echo "API_KEY=sk-1234567890abcdef" > test-secret.txt
git add test-secret.txt
git commit -m "test: trying to commit a secret"
# Expected: blocked by detect-secrets
git reset HEAD test-secret.txt
rm test-secret.txt
```

**Expected Outcome:**

* Commits are blocked if secrets detected
* Hooks run automatically on commit

---

## 6. Validation Checklist

* [ ] `.env` does not appear in `git status`
* [ ] `.env.example` exists and contains required variables
* [ ] `config/` includes base + dev/test/stage/prod
* [ ] `config_loader.py` loads and merges config successfully
* [ ] `pre-commit run --all-files` succeeds
* [ ] detect-secrets blocks committing test secret

---

## 7. Artifacts Produced

* `.env.example`
* `.pre-commit-config.yaml`
* `.secrets.baseline`
* `config/*`
* `src/utils/config_loader.py`
* `config/README.md`

---

## 8. Rollback / Undo

If hooks break workflows temporarily:

```bash
pre-commit uninstall
```

If baseline is wrong:

```bash
rm .secrets.baseline
detect-secrets scan > .secrets.baseline
```

---

## 9. Notes & Gotchas

* `.env` must never be committed.
* JSON configs must never contain secrets.
* `${ENV_VAR}` placeholders are the supported pattern.

```

✅ Reply with only: **“have it”** or **“missing”** (and if missing, tell me what you *do* have: the file path or different name).
```
