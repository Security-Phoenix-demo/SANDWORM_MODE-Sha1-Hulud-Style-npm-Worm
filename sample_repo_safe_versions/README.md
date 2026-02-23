# Sample Safe Versions Repo

This folder includes monitored package names, but only non-compromised versions.

## Example Commands

```bash
python3 enhanced_npm_compromise_detector_phoenix.py sample_repo_safe_versions --output safe_report.txt
python3 enhanced_npm_compromise_detector_phoenix.py sample_repo_safe_versions --enable-phoenix --output safe_report.txt
python3 -m universal_vulnerability_scanner.main scan sample_repo_safe_versions --output safe_universal_report.txt
```
