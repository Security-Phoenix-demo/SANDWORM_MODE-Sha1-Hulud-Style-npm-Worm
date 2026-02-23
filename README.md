# SANDWORM_MODE NPM Compromise Detector

Detects compromised NPM packages associated with the SANDWORM_MODE supply-chain campaign and optionally uploads findings to Phoenix Security.

## What It Detects

The detector flags only these compromised packages and versions:

- claud-code@0.2.1
- cloude-code@0.2.1
- cloude@0.3.0
- crypto-locale@1.0.0
- crypto-reader-info@1.0.0
- detect-cache@1.0.0
- format-defaults@1.0.0
- hardhta@1.0.0
- locale-loader-pro@1.0.0
- naniod@1.0.0
- node-native-bridge@1.0.0
- opencraw@2026.2.17
- parse-compat@1.0.0
- rimarf@1.0.0
- scan-store@1.0.0
- secp256@1.0.0
- suport-color@1.0.1
- veim@2.46.2
- yarsg@18.0.1

Only packages listed in `compromised_packages_2025.json` are treated as vulnerable.

## Quick Start

1. Install dependencies:

```bash
python3 -m pip install -r requirements.txt
```

1. Configure Phoenix (optional):

- Create a template with `--create-config`, then copy `.phoenix.config.template` to `.phoenix.config`, or
- Set environment variables: `PHOENIX_CLIENT_ID`, `PHOENIX_CLIENT_SECRET`, `PHOENIX_API_URL`

1. Run a scan:

```bash
python3 enhanced_npm_compromise_detector_phoenix.py . --output report.txt
```

1. Run with Phoenix upload:

```bash
python3 enhanced_npm_compromise_detector_phoenix.py . --enable-phoenix --output report.txt
```

## Universal Scanner (Modular CLI)

The universal scanner is now adapted to use the same `compromised_packages_2025.json` reference database by default.

```bash
# Scan a directory (uses compromised_packages_2025.json by default)
python3 -m universal_vulnerability_scanner.main scan .

# JSON output
python3 -m universal_vulnerability_scanner.main scan . --json --output scan_results.json

# Upload findings to Phoenix
python3 -m universal_vulnerability_scanner.main scan . --upload-phoenix

# Create Phoenix config template
python3 -m universal_vulnerability_scanner.main create-config
```

## Campaign Report

See `SANDWORM_MODE_CAMPAIGN_REPORT.md` for the full structured report, IOCs, and mitigation guidance.

## Repository Scanning Guide

See `REPOSITORY_SCANNING_GUIDE.md` for GitHub scanning workflows and credential setup.

## Notes

- `compromised_packages_2025.json` is the authoritative package list.
- The Phoenix upload uses the `/v1/import/assets` API endpoint and supports both config-file and environment-based auth.
