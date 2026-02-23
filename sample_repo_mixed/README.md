# Sample Mixed Repo

This folder contains a sample `package.json` and `package-lock.json` with a mix of:

- Compromised packages (SANDWORM_MODE list)
- Clean packages (common libraries)
- A monitored package version that is not compromised

## Example Commands

```bash
# Scan the sample folder
python3 enhanced_npm_compromise_detector_phoenix.py sample_repo_mixed --output sample_report.txt

# Upload findings to Phoenix (requires .phoenix.config)
python3 enhanced_npm_compromise_detector_phoenix.py sample_repo_mixed --enable-phoenix --output sample_report.txt

# Universal scanner (modular CLI)
python3 -m universal_vulnerability_scanner.main scan sample_repo_mixed --output sample_universal_report.txt
```
