# Repository Scanning & GitHub Credentials Guide

This guide covers GitHub scanning workflows for SANDWORM_MODE using credentials, plus common scanning patterns.

## Credentials Setup

You can provide GitHub credentials in either of these ways:

1. **Environment variable**

```bash
export GITHUB_TOKEN="your_github_token_here"
```

1. **`.phoenix.config`**

Add a token in the `[phoenix]` section:

```ini
[phoenix]
client_id = your_client_id_here
client_secret = your_client_secret_here
api_base_url = https://api.securityphoenix.cloud
github_token = your_github_token_here
```

Notes:

- Public repos can be scanned without a token, but rate limits apply.
- Private repos require a valid token with `repo` scope.

---

## GitHub Scanning Methods

### 1. Light scan a single repo (no clone)

```bash
python3 enhanced_npm_compromise_detector_phoenix.py \
  --light-scan \
  --repo-url https://github.com/ORG/REPO \
  --output repo-light-scan.txt
```

### 2. Light scan a list of repos

```bash
cat > github_repos.txt << EOF
https://github.com/facebook/react
https://github.com/vuejs/vue
https://github.com/angular/angular
EOF

python3 enhanced_npm_compromise_detector_phoenix.py \
  --light-scan \
  --repo-list github_repos.txt \
  --organize-folders \
  --output multi-repo-light-scan.txt
```

### 3. Full clone + scan

```bash
cat > repos.txt << EOF
https://github.com/facebook/react
EOF

python3 enhanced_npm_compromise_detector_phoenix.py \
  --repo-list repos.txt \
  --organize-folders \
  --output repo-clone-scan.txt
```

### 4. Full tree analysis on remote repos

```bash
python3 enhanced_npm_compromise_detector_phoenix.py \
  --repo-list repos.txt \
  --full-tree \
  --organize-folders \
  --output remote-full-tree-scan.txt
```

---

## Phoenix Upload (Optional)

Add `--enable-phoenix` to any command to upload findings:

```bash
python3 enhanced_npm_compromise_detector_phoenix.py \
  --light-scan \
  --repo-url https://github.com/ORG/REPO \
  --enable-phoenix \
  --output repo-light-scan.txt
```

---

## Troubleshooting

### 401 or GitHub API failures

- Confirm `GITHUB_TOKEN` is valid.
- For private repos, ensure the token has `repo` scope.
- If using `.phoenix.config`, verify `github_token` is set and not a placeholder.

### No NPM files found

- Ensure the repo contains `package.json` or lock files.
- For light scan mode, the scanner only fetches NPM files.
