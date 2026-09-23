# Claude Code session — 2026-09-21 (Week 0 setup + Flask intro)

Saved transcript so it can be reopened on this laptop or pasted into a new
Claude Code session for context. This file is not auto-committed to git —
commit it yourself if you want it in the repo's history.

---

## 1. WSL: `sudo apt install python3.14-venv` failing

**Problem:** `sudo` auth kept failing, and even after that, `python3.14-venv`
had no installation candidate in Ubuntu's apt repos.

**Diagnosis:**
- The `sudo` password was simply being entered wrong (or the WSL Ubuntu
  password was never set/remembered).
- Ubuntu's apt repos don't ship Python 3.14 packages, so `python3.14-venv`
  doesn't exist there — the `.venv` claiming to be 3.14 was built by some
  other Python, not apt's.

**Fix given:**
```bash
sudo apt update
sudo apt install python3-venv python3-pip
python3 --version

cd /mnt/c/Users/theev/Documents/aws-cloud-engineer-journey
rm -rf .venv
python3 -m venv .venv
source .venv/bin/activate
```
If 3.14 specifically was needed: deadsnakes PPA, or `uv venv --python 3.14`.

Noted that a `.venv` under `/mnt/c/...` is slow and cross-environment venvs
(Windows-made vs WSL-made) don't work interchangeably — pick one shell and
stick with it.

---

## 2. Decision: stay in PowerShell (not WSL)

User wants to stay in PowerShell. Diagnosed the environment:

```
python.exe   -> C:\Program Files\Python313\python.exe  (3.13.2)
python3.exe  -> Windows Store stub (0.0.0.0)            -- ignore this one
py.exe       -> 3.13 (default), 3.12 also installed
pip.exe      -> 3.13's pip
aws          -> NOT FOUND
.venv/       -> was actually a *Linux* venv (pyvenv.cfg pointed at
                 /usr/bin/python3.14, built by WSL) — unusable from PowerShell
```

**Fix given:**
```powershell
# AWS CLI
winget install Amazon.AWSCLI
# reopen terminal/VS Code so PATH refreshes
aws --version

# Rebuild venv as a real Windows venv
cd C:\Users\theev\Documents\aws-cloud-engineer-journey
Remove-Item -Recurse -Force .venv
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r PropertyLite\requirements.txt

aws configure
```
Plus: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` if activation is
blocked, and select `.venv\Scripts\python.exe` as the VS Code interpreter.

`winget install` and `aws configure` were left for the user to run
themselves (the latter needs their own access keys).

---

## 3. IDX Exchange AWS Cloud Engineer Intern Handbook (PDF)

User is an **unpaid** AWS Cloud Engineer intern for IDX Exchange this fall.
Shared a 12-week + Week 0 handbook built around one app, **PropertyLite**
(Flask + CSV, in `PropertyLite/`), taken through:

| Weeks | Theme |
|---|---|
| 0 | Env setup, get PropertyLite running locally |
| 1–2 | IAM & security foundations |
| 3 | EC2 compute |
| 4 | S3 / RDS / DynamoDB, real IDX data + PII cleanup |
| 5 | VPC networking, bastion host |
| 6 | ALB + Auto Scaling |
| 7 | Lambda / API Gateway / SQS (serverless) |
| 8 | Terraform (IaC) |
| 9 | Docker + ECS Fargate |
| 10 | CI/CD via GitHub Actions + OIDC |
| 11 | CloudWatch observability, cost |
| 12 | Capstone: consolidate everything into one Terraform-defined prod env |

**User's explicit stated goal:** understand the *root concepts*, not just
complete tasks with AI doing the work. Wants depth on: Flask/API/Python
fluency, JSON/YAML, Docker + Kubernetes, Terraform/IaC concepts, and basic
SQL (SELECT/WHERE/GROUP BY/ALTER TABLE, primary keys).

Also flagged: the handbook was likely AI-generated, so don't follow it too
literally — verify steps rather than trusting them blindly.

**Handbook issues already spotted** (for later correction/verification):
- Week 3 user-data script has a placeholder instead of real `app.py` content.
- Week 5's cleanup deletes the VPC that Week 6 then depends on.
- Week 6 uses `amazon-linux-extras`/`epel`, which don't exist on Amazon Linux 2023.
- Week 7 Lambda will throw on DynamoDB `Decimal` values in `json.dumps`.
- Week 9 Fargate service/task has no security group opening port 8080.
- Debug Lab 10.2's stated root cause is wrong — workflow-level `permissions:` *does* cascade to jobs.
- Debug Lab 11.3's stated root cause is wrong — `AWS/ECS` CPUUtilization is emitted without Container Insights.
- Week 4 raw `rets_property.sql` contains real PII (agent names/emails/phones) — never commit it; confirm data-license permission before handling it.
- Week 12 "restore from snapshot" actually creates a *new* RDS instance, not a repair of the old one.
- Cost care: NAT Gateway, ALB, and RDS are the real cost drivers on an unpaid-intern's own AWS bill — teardown discipline matters.

Memory saved (persists across sessions):
- `user-learning-goal.md` — wants root-concept understanding, not AI-driven task completion.
- `project-idx-aws-handbook.md` — the curriculum summary + the error list above.

---

## 4. Lesson 1: reading `PropertyLite/app.py` — Flask/API basics

Walked through the existing app:

```python
import csv
import os
from flask import Flask, jsonify, request

app = Flask(__name__)
DATA_PATH = os.environ.get("PROPERTY_DATA_PATH", "rets_property_sample.csv")

def load_properties():
    with open(DATA_PATH, newline="") as f:
        return list(csv.DictReader(f))

@app.route("/health")
def health():
    return jsonify(status="ok")

@app.route("/properties")
def list_properties():
    city = request.args.get("city")
    rows = load_properties()
    if city:
        rows = [r for r in rows if r.get("L_City", "").lower() == city.lower()]
    return jsonify(rows[:50])

@app.route("/properties/<listing_id>")
def get_property(listing_id):
    rows = load_properties()
    match = next((r for r in rows if r.get("L_ListingID") == listing_id), None)
    if not match:
        return jsonify(error="not found"), 404
    return jsonify(match)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8080)
```

**Concepts taught:**
- API = client sends a request, server runs code, sends back a response; JSON
  is the text format.
- `Flask(__name__)` creates the app; `@app.route(...)` is a decorator binding
  a URL path to the function below it.
- `jsonify(...)` turns a Python dict into a JSON HTTP response.
- `<listing_id>` in a route captures a URL segment as a function argument.
- `request.args.get("city")` reads a query string param (`?city=...`).
- `return jsonify(error="..."), 404` — a tuple of (body, status code).

**Planned learning order going forward:** Python/JSON/API/Flask → SQL (maps
cleanly onto Week 4) → YAML (picked up alongside Docker/Actions, not its own
unit) → Docker → Terraform → Kubernetes (extra, not in the handbook, done
last once Docker is solid).

**Predict-before-running questions posed to the user:**
1. Does `/properties?city=fresno` (lowercase) match the "Fresno" row? Which
   line decides that?
2. What does `rows[:50]` do, and why would a real API want it?
3. `load_properties()` re-reads the whole CSV on every request — what
   breaks at 100,000 rows? (Motivates Week 4's move to a real database.)

**Writing exercise assigned:** add a `/properties/count` route to
`app.py` that returns `{"count": N}`, using `/health` as a template and
`load_properties()` + `len()` — code intentionally not given, to be written
by the user and reviewed next.

**Note on running locally in PowerShell:** use `curl.exe`, not bare `curl`
(which is a PowerShell alias for `Invoke-WebRequest` and behaves
differently), e.g. `curl.exe http://localhost:8080/health`.

---

## Where things stand / next steps

- [ ] Confirm `aws --version` and `aws sts get-caller-identity` work (Week 0 CLI setup).
- [ ] User to answer the 3 prediction questions above.
- [ ] User to write and test the `/properties/count` route.
- [ ] Then: query parameters, a POST route, and error handling.
- [ ] Eventually start Week 1 (MFA, budget alert, IAM admin user, CLI config) once Week 0 is verified working.
