# Sprint 08 – Sprint Review Presentation (5–7 Slides)
## Continuous Integration using Jenkins
**Project**: Chennai Street-Level Flood Prediction System  
**Course**: ISWE406P – Agile Development Process and DevOps Lab  
**Student**: Hema Priyadharshni G | **Faculty**: Dr. Kaja Mohideen A  

---

### Slide 1: Title Slide
- **Title**: Sprint 08: Continuous Integration using Jenkins
- **Subtitle**: Automated Build, Test, and Containerization Workflow for Chennai Flood Prediction
- **Course**: ISWE406P – Agile Development Process & DevOps Lab
- **Presenter**: Hema Priyadharshni G (M.Tech Software Engineering)
- **Faculty Guide**: Dr. Kaja Mohideen A
- **Class / Slots**: CH2026270100722 | L11+L12

---

### Slide 2: Customer Requirement & Sprint Objectives
- **Customer Requirement**:
  > *"The customer wants the application to be automatically built and validated whenever new changes are pushed to the Git repository."*
- **Key Objectives**:
  - Transition from manual EC2 deployments to automated CI pipelines.
  - Setup and configure Jenkins controller with necessary plugins (Git, Pipeline).
  - Integrate Jenkins with GitHub repository via SCM triggers.
  - Validate application with 13 automated `pytest` test suites.
  - Build and tag Docker images dynamically (`chennai-flood-backend:build-<N>`).
  - Demonstrate end-to-end automated pipeline trigger on code push.

---

### Slide 3: Existing Workflow vs. Continuous Integration
- **Pre-Sprint 8 Workflow (Sprints 1–7)**:
  - Developer Workstation $\rightarrow$ Git Push to GitHub.
  - Manual SSH login to AWS EC2 instance.
  - Manual execution of `git pull origin main`.
  - Manual Docker image building and container restart.
  - **Bottlenecks**: No pre-build validation, risk of human error, deployment downtime.
- **Sprint 8 Evolution (Continuous Integration)**:
  - Developer pushes code $\rightarrow$ GitHub notifies Jenkins.
  - Automated Checkout $\rightarrow$ Isolated Dependency Build.
  - Automated Pytest Suite Execution (13 Unit Tests).
  - Automated Docker Image Creation & Tagging.

---

### Slide 4: Jenkins Pipeline Architecture
```
  [Developer Git Push]
           │
           ▼
    [GitHub Repo]
           │ (Webhook / SCM Poll)
           ▼
   [Jenkins Controller] (localhost:8080)
   ┌─────────────────────────────────────────────────────────────┐
   │  Stage 1: Checkout (Pull source code from GitHub)           │
   │  Stage 2: Build (Python 3.11 Virtualenv & Dependencies)     │
   │  Stage 3: Test / Validate (13 Pytest Tests - 100% Passed)   │
   │  Stage 4: Docker Build (Tagged Backend Container Created)   │
   │  Stage 5: Post Actions (Workspace Cleanup & Status Report)  │
   └─────────────────────────────────────────────────────────────┘
           │
           ▼
[Artifact: chennai-flood-backend:build-N & latest]
```

---

### Slide 5: Jenkinsfile & Pipeline Stages
- **Declarative Pipeline Design**:
  - `agent any`: Runs on Jenkins executor with isolated workspace.
  - `Stage('Checkout')`: Clones `origin/main` branch using SCM credentials.
  - `Stage('Build')`: Installs `backend/requirements.txt` inside `venv`.
  - `Stage('Test / Validate')`: Runs `pytest backend/tests/ -v`.
    - Health checks (`/health` and root `/`).
    - Flood risk classification thresholds (Low, Moderate, High, Critical).
    - ML feature column order verification against metadata.
  - `Stage('Docker Build')`: Builds `chennai-flood-backend:build-${BUILD_NUMBER}`.
  - `post { always { cleanWs() } }`: Keeps agent environment clean.

---

### Slide 6: Successful CI Execution & Evidence
- **Build #1 (Initial Pipeline Run)**:
  - Execution Result: **SUCCESS** (Green).
  - Test Suite: 13 passed in 8.14 seconds.
  - Docker Image Created: `chennai-flood-backend:build-1`.
- **Build #2 (CI Demonstration Run)**:
  - Triggered by Commit `39bdcec` (`docs: add CI documentation`).
  - Automatically detected and processed.
  - Result: **SUCCESS** (Green).
  - Docker Image Created: `chennai-flood-backend:build-2` & `latest`.
- **Key Metric**: Total pipeline turnaround time < 45 seconds.

---

### Slide 7: Outcome, Challenges & Future CI/CD Extension
- **Sprint 08 Outcomes**:
  - 100% automated build and validation on code push.
  - Zero manual intervention required for testing and image creation.
  - High repeatability across development and deployment environments.
- **Challenges Overcome**:
  - Python version compatibility resolved by explicitly locking to Python 3.11 virtualenv.
  - Jenkins pipeline integration with local Docker socket and CLI permissions.
- **DevOps Roadmap (Future Sprint - CD)**:
  - Push validated Docker images to AWS Elastic Container Registry (ECR).
  - Add Automated Continuous Deployment (CD) step to update live AWS EC2 containers.
