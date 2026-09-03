# 🔐 Integrating Automated Security Scanning into DevOps Pipelines with SonarQube and Trivy

## 📌 Overview

This project demonstrates a Jenkins CI/CD pipeline for a sample Flask application, with automated static code analysis (SonarQube) and container/dependency vulnerability scanning (Trivy) enforced as quality and security gates before deployment.

The goal was to replace manual code and security review with automated, pipeline-triggered checks — reducing review time while catching issues earlier in the development cycle.

---

## 🛠️ Tech Stack

- **Flask** (sample app)
- **Jenkins** (CI/CD orchestration)
- **SonarQube** (static code analysis)
- **Trivy** (container & dependency vulnerability scanning)
- **Docker** (containerization)
- **Kubernetes / Minikube** (local deployment)
- **pytest** (unit testing + coverage)

---

## 🔄 Pipeline Stages

The Jenkins pipeline is triggered automatically via a GitHub webhook on every push to `main`. Stages run in order:

1. **Build** — Install dependencies, package the app
2. **Test** — Run unit tests with `pytest` and generate coverage reports
3. **SonarQube Scan** — Static code analysis for bugs, code smells, security hotspots, duplication, and coverage
4. **Docker Build** — Build the container image
5. **Trivy Scan** — Scan the image, dependencies (`requirements.txt`), and Kubernetes manifests for vulnerabilities and misconfigurations
6. **Deploy** — Apply `deployment.yaml` to a local Kubernetes cluster (Minikube)

---

## 🔍 SonarQube Quality Gate

A quality gate is configured to **fail the pipeline** if:
- Test coverage falls below **80%**
- Any **blocker-level** issues are present

### Sample scan results (demo app)
| Metric | Result |
|---|---|
| Bugs | 2 |
| Code Smells | 14 |
| Security Hotspots | 1 |
| Test Coverage | 82% |

---

## 🛡️ Trivy Vulnerability Scanning

Trivy scans three targets:
- Docker image (base + application layers)
- Python dependencies (`requirements.txt`)
- Kubernetes manifests (misconfiguration checks)

**Blocking behavior:** High/critical vulnerabilities fail the pipeline. Medium/low vulnerabilities are reported but do not block the build.

### Sample scan results
| Run | High | Medium | Total |
|---|---|---|---|
| Initial (standard base image) | 3 | 3 | 6 |
| After switching to a slim base image | 0 | 1 | 1 |

Switching to a slimmer base image alone removed all high-severity findings and cut total vulnerabilities by ~83%.

---

## 🚀 Deployment

`deployment.yaml` defines replica count, container image, and service exposure for the app. The pipeline deploys the built and scanned image to a local Kubernetes cluster via **Minikube**. Cloud deployment (EKS/GKE) is a planned next step, not yet implemented.

---

## 📊 Impact: Manual Review Time

Manual code and container security review previously took approximately **25 minutes per cycle**. With SonarQube and Trivy automated into the pipeline, this dropped to approximately **15 minutes per cycle** — a **~40% reduction**, measured across repeated pipeline runs.

---

## 📂 Project Structure

```
Devops/
├── Dockerfile
├── Jenkinsfile
├── SETUP.md
├── app.py
├── deployment.yaml
├── docker-compose.yml
└── requirements.txt
```

---

## 🚀 How to Run Locally

1. Clone the repository
   ```
   git clone https://github.com/sahana-a25/Devops.git
   cd Devops
   ```
2. Install dependencies
   ```
   pip install -r requirements.txt
   ```
3. Run the app locally
   ```
   python app.py
   ```
4. Build and run with Docker
   ```
   docker-compose up --build
   ```
5. See `SETUP.md` for full Jenkins, SonarQube, and Minikube configuration steps.

---

## 📈 Future Enhancements

- Cloud deployment (EKS/GKE) instead of local Minikube only
- Slack/email notifications on pipeline failure
- Dependency-update automation (Dependabot/Renovate)

---

## 👩‍💻 Author

**Sahana A**
