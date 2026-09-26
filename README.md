# Sigma Detection Rules

MITRE ATT&CK-aligned Sigma detection rules for Splunk SIEM. Detection-as-Code approach: versioned in Git with CI/CD validation pipeline and automated deployment.

## Structure

Rules are organized into folders by MITRE ATT&CK tactic:

| Folder | ATT&CK Tactic | ID |
|---|---|---|
| `initial_access/` | Initial Access | TA0001 |
| `execution/` | Execution | TA0002 |
| `persistence/` | Persistence | TA0003 |
| `defense_evasion/` | Defense Evasion | TA0005 |
| `credential_access/` | Credential Access | TA0006 |
| `discovery/` | Discovery | TA0007 |
| `lateral_movement/` | Lateral Movement | TA0008 |
| `command_and_control/` | Command and Control | TA0011 |
| `exfiltration/` | Exfiltration | TA0010 |
| `impact/` | Impact | TA0040 |

Each rule is a single Sigma `.yml` file placed in the folder of its primary tactic. Additional techniques are captured in the rule's `tags` (e.g. `attack.t1059.001`).

`.github/workflows/` contains the CI/CD pipeline definitions.

## CI/CD Pipeline

The GitHub Actions workflow (`.github/workflows/validate-sigma.yml`) runs on pushes to `main`/`develop` and on pull requests to `main`:

1. **Syntax validation** – every `.yml` file is parsed to catch YAML errors.
2. **Rule expansion** – Sigma rules are converted to Splunk SPL using pySigma and the Splunk backend (`pysigma-plugin-splunk`).
3. **Linting** – rules are checked against the Sigma specification (required fields, valid tags, status, level, etc.).
4. **Splunk deployment** – on `main` only, converted searches are deployed to Splunk via the API using the `HEC_URL` and `HEC_TOKEN` repository secrets.

## Adding Rules

1. Create a branch from `develop` (e.g. `rule/powershell-encoded-command`).
2. Add your rule as `<tactic_folder>/<descriptive_name>.yml` following the [Sigma specification](https://github.com/SigmaHQ/sigma-specification). Include `title`, `id` (UUID), `status`, `description`, `author`, `date`, `tags` (ATT&CK tactic and technique), `logsource`, `detection`, `falsepositives`, and `level`.
3. Validate locally:
   ```bash
   pip install pySigma pysigma-plugin-splunk pyyaml
   sigma convert -t splunk -p splunk_windows <tactic_folder>/<rule>.yml
   ```
4. Open a pull request. CI must pass before merge.
5. Once merged to `main`, the rule is deployed to Splunk automatically.

## References

- [Sigma Specification](https://github.com/SigmaHQ/sigma-specification)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Splunk Sigma App / pySigma Splunk Backend](https://github.com/SigmaHQ/pySigma-backend-splunk)
