# Rider Manager — Project Skill

This file defines how Claude should work on the Rider Manager project. Read this at the start of every session.

---

## Project Info

| Item | Value |
|------|-------|
| Repo | `redbearstudio-my/rider-manager` |
| Production URL | `https://gogo-rider-manager.netlify.app` |
| Staging URL | `https://staging--gogo-rider-manager.netlify.app` |
| Supabase project | `gqavlaetwrvyugrqarcz.supabase.co` |
| Main file | `index.html` (single HTML file — all CSS, JS, HTML in one file) |
| GitHub token | Stored in Claude memory |

---

## Session Start Checklist

1. **Always fetch the latest `index.html` from GitHub** (never assume local file is current):
```bash
curl -s -H "Authorization: Bearer TOKEN" \
  "https://api.github.com/repos/redbearstudio-my/rider-manager/contents/index.html?ref=staging" \
  | python3 -c "import sys,json,base64; d=json.load(sys.stdin); open('/home/claude/index.html','wb').write(base64.b64decode(d['content'])); print('SHA:', d['sha'])"
```
2. Note the file SHA — needed for every push.
3. Always fetch from `staging` branch as the base for new work.

---

## Branch Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `release/vX.X` | `release/v1.9` |
| Bug fix | `fix/description` | `fix/export-modal` |
| UI change | `ui/description` | `ui/settings-labels` |

---

## PR & Merge Flow

```
New branch (from staging)
        ↓
Push changes
        ↓
Open PR → staging
        ↓
Derred tests on staging--gogo-rider-manager.netlify.app
        ↓
"merge" → merge to staging
        ↓
"merge to main" or "merge" after staging confirmed
        ↓
Open PR staging → main, merge
        ↓
Production live in ~30s ✅
```

**Always target `staging` first, never push directly to `main`.**

---

## GitHub API Patterns

### Get branch HEAD SHA
```bash
curl -s -H "Authorization: Bearer TOKEN" \
  https://api.github.com/repos/redbearstudio-my/rider-manager/git/ref/heads/staging \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['object']['sha'])"
```

### Get file SHA (required before any push)
```bash
curl -s -H "Authorization: Bearer TOKEN" \
  "https://api.github.com/repos/redbearstudio-my/rider-manager/contents/index.html?ref=BRANCH" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['sha'])"
```

### Create branch
```bash
curl -s -X POST -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
  https://api.github.com/repos/redbearstudio-my/rider-manager/git/refs \
  -d "{\"ref\":\"refs/heads/BRANCH_NAME\",\"sha\":\"BRANCH_HEAD_SHA\"}"
```

### Push file (ALWAYS use JSON payload file — never pass base64 as shell arg)
```bash
python3 - <<EOF
import json, base64
with open('/home/claude/index.html', 'rb') as f:
    content = base64.b64encode(f.read()).decode()
payload = {
    "message": "commit message",
    "content": content,
    "sha": "FILE_SHA",
    "branch": "BRANCH_NAME"
}
with open('/home/claude/push_payload.json', 'w') as f:
    json.dump(payload, f)
EOF

curl -s -X PUT -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
  https://api.github.com/repos/redbearstudio-my/rider-manager/contents/index.html \
  -d @/home/claude/push_payload.json
```

### Open PR
```bash
curl -s -X POST -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
  https://api.github.com/repos/redbearstudio-my/rider-manager/pulls \
  -d '{"title":"PR title","body":"PR description","head":"FEATURE_BRANCH","base":"staging"}'
```

### Merge PR
```bash
curl -s -X PUT -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
  https://api.github.com/repos/redbearstudio-my/rider-manager/pulls/PR_NUMBER/merge \
  -d '{"commit_title":"commit message","merge_method":"squash"}'
```

### Handle merge conflict (staging → main)
If PR is not mergeable (`mergeable_state: dirty`), force push directly to main:
- Get file SHA on main branch
- Push `index.html` directly to `main` branch
- Close the stale PR

---

## Versioning

- Version format: `vMAJOR.MINOR` (e.g. `v1.9`)
- Bump version in `<title>` tag: `<title>Rider Manager vX.X</title>`
- Bump `<meta name="version" content="X.X">`
- Bump version display in Settings About section
- **Always update `release.md` after every merge** — push directly to main

### release.md push pattern
```bash
curl -s -H "Authorization: Bearer TOKEN" \
  "https://api.github.com/repos/redbearstudio-my/rider-manager/contents/release.md?ref=main" \
  | python3 -c "import sys,json,base64; d=json.load(sys.stdin); open('/home/claude/release.md','wb').write(base64.b64decode(d['content'])); print('SHA:', d['sha'])"
```
Then edit `/home/claude/release.md` and push directly to `main` using the same payload pattern above.

---

## Tech Stack

| Layer | Detail |
|-------|--------|
| Frontend | Single `index.html` — vanilla HTML/CSS/JS, no build step |
| Auth | Supabase Google OAuth |
| Database | Supabase (Postgres) |
| Hosting | Netlify (staging + production branches) |
| Excel | SheetJS (`xlsx@0.18.5` via CDN) |
| Maps | Waze deep links |
| Messaging | WhatsApp deep links (Malaysian +60 normalisation) |

---

## Key Supabase Tables

| Table | Dedup Key | Notes |
|-------|-----------|-------|
| `trips` | `trip_id` (unique constraint) | Upsert on `trip_id`; rows without `trip_id` use plain insert with `null` |
| `expenses` | none | Plain insert |
| `busy_times` | none | Plain insert |
| `profiles` | `id` (user id) | Upsert always |

---

## Important Rules

- **Never pass base64 content as a shell argument** — always write to `push_payload.json` and use `-d @file`
- **Always fetch current file SHA before pushing** — stale SHA causes 422 error
- **Always create feature branches from `staging`**, not `main`
- **Always test on staging before merging to main**
- **Always update `release.md` after every merge to main**
- Single HTML file — all features, styles, scripts are self-contained
