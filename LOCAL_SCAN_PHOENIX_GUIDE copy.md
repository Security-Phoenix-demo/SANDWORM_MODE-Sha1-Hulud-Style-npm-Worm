# Local Scan + Phoenix Upload (Quick Guide)

This guide shows how to scan local packages and upload results to Phoenix.

## 1. Install dependencies

```bash
python3 -m pip install -r requirements.txt
```

## 2. Configure Phoenix

Create a template and fill in your credentials:

```bash
python3 enhanced_npm_compromise_detector_phoenix.py --create-config
cp .phoenix.config.template .phoenix.config
```

Edit `.phoenix.config` and set:

- `client_id`
- `client_secret`
- `api_base_url`

## 3. Scan a local folder and upload to Phoenix

```bash
python3 enhanced_npm_compromise_detector_phoenix.py /path/to/local/repo \
  --enable-phoenix \
  --output local-scan-report.txt
```

## 4. Scan multiple local folders

```bash
python3 enhanced_npm_compromise_detector_phoenix.py \
  --folders /path/to/repo1 /path/to/repo2 \
  --enable-phoenix \
  --output multi-local-scan.txt
```

## 5. Scan a list of local folders

Create a list file:

```bash
cat > local_folders.txt << EOF
/path/to/repo1
/path/to/repo2
EOF
```

Then scan:

```bash
python3 enhanced_npm_compromise_detector_phoenix.py \
  --folder-list local_folders.txt \
  --enable-phoenix \
  --output folder-list-scan.txt
```

---

## Notes

- Phoenix uploads are enabled with `--enable-phoenix`.
- Reports are saved to the path passed via `--output`.
- The scan only treats packages in `compromised_packages_2025.json` as vulnerable.
