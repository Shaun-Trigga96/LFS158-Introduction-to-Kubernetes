# Kubernetes Study Plan (LFS158, LFD259, CKA, GCP DevOps)

## 📋 Overview
This repository organizes study materials for the Linux Foundation’s **Introduction to Kubernetes (LFS158)** and **Kubernetes for Developers (LFD259)** courses, preparing you for the **Certified Kubernetes Administrator (CKA)** exam by August 3, 2025, with a transition plan for the **Google Cloud Professional DevOps Engineer** exam. Designed for a Windows machine (using Git Bash or WSL2), the study plan leverages your unemployment for 6–8 hours of daily study from July 4 to August 3, 2025, building on your **Introduction to Linux (LFS101)** knowledge.

- **Duration**: July 4–August 3, 2025 (4 weeks + 2 days)
- **Study Time**: 6–8 hours/day (theory, labs, review)
- **Objectives**:
  - Complete LFS158 (Weeks 1–2)
  - Complete LFD259 (Weeks 3–4)
  - Pass CKA exam (August 3)
  - Prepare for GCP DevOps exam (post-August 3)

## 📁 Directory Structure
Materials are organized in two directories on your Desktop, created by `create_lfs158_folders.sh` and `create_lfd259_folders.sh`:

- **`LFS158-Introduction-to-Kubernetes/`**:
  - `Chapter-01/` to `Chapter-16/`: README.md, notes.md, exercises.md for each chapter
  - `Labs-and-Exercises/`: Lab guides (e.g., pod-deployment.md)
  - `Cheat-Sheets/`: kubectl-commands.md, yaml-examples.md
  - `Practice-Tests/`: cka-simulator.md, mock-exam.md
  - `Resources/`: useful-links.md, reference-materials.md
  - `Daily-Progress/`: week-1-progress.md, week-2-progress.md, study-schedule.md
  - `README.md`, `PROGRESS.md`, `settings.json`
- **`LFD259-Kubernetes-for-Developers/`**:
  - `Chapter-01/` to `Chapter-11/`: Same structure as LFS158
  - Similar subdirectories for labs, cheat sheets, etc.

## 🗓️ Study Schedule
### Week 1 (July 4–10, 2025): LFS158 Foundations
- **Goal**: Set up Kubernetes environment, cover basics.
- **Daily**: 6–8 hours (3–4 hours theory, 2–4 hours labs)
- **Tasks**:
  - **Day 1 (July 4)**: Set up Git Bash/WSL2, Minikube, kubectl, Docker Desktop. Study Chapter 1 (Introduction), Chapter 2 (Basics). Practice: `kubectl run nginx --image=nginx`. Update `notes.md`.
  - **Day 2–3**: Chapters 3–5 (Installation, Architecture, APIs). Practice: Configure cluster, `kubectl get pods`.
  - **Day 4–5**: Chapters 6–7 (API Objects, Deployments). Practice: Create deployment YAML.
  - **Day 6–7**: Review Chapters 1–7, labs (e.g., multi-container pod), update `notes.md`.
- **Resources**: [LFS158 on edX](https://www.edx.org/course/introduction-to-kubernetes), [Kubernetes Docs](https://kubernetes.io)

### Week 2 (July 11–17, 2025): LFS158 Advanced Topics
- **Goal**: Master advanced concepts, start CKA prep.
- **Daily**: 6–8 hours (3 hours theory, 3–5 hours labs)
- **Tasks**:
  - **Day 8–9**: Chapters 8–10 (Volumes, Services, Ingress). Practice: Set up PV/PVC, expose service.
  - **Day 10–11**: Chapters 11–13 (Scheduling, Logging, CRDs). Practice: Apply taints, debug with `kubectl logs`.
  - **Day 12–13**: Chapters 14–16 (Helm, Security, Advanced). Practice: Install Helm chart, configure RBAC.
  - **Day 14**: Review Chapters 8–16, practice CKA tasks (e.g., kubeadm setup).
- **Resources**: [Killer.sh](https://killer.sh), “Kubernetes in Action” by Marko Luksa

### Week 3 (July 18–24, 2025): LFD259 Core Concepts
- **Goal**: Learn developer-focused Kubernetes skills.
- **Daily**: 6–8 hours (3–4 hours theory, 2–4 hours labs)
- **Tasks**:
  - **Day 15–16**: Chapters 1–3 (Introduction, Architecture, Build). Practice: Build Docker image, deploy to Kubernetes.
  - **Day 17–18**: Chapters 4–6 (Deploy, Expose, Observability). Practice: Scale deployment, set up Prometheus.
  - **Day 19–20**: Chapters 7–9 (Environment, Jobs, Multi-container Pods). Practice: Create CronJob.
  - **Day 21**: Review Chapters 1–9, deploy app stack, update `notes.md`.
- **Resources**: [LFD259 Training](https://training.linuxfoundation.org), [KodeKloud](https://kodekloud.com)

### Week 4 (July 25–31, 2025): LFD259 Advanced & CKA Prep
- **Goal**: Complete LFD259, intensify CKA practice.
- **Daily**: 7–9 hours (2–3 hours theory, 4–6 hours practice)
- **Tasks**:
  - **Day 22–23**: Chapters 10–11 (Storage, Troubleshooting). Practice: Configure StorageClass, debug deployment.
  - **Day 24–25**: Killer.sh CKA simulator. Focus: Cluster setup (13%), Workloads (25%), Networking (20%), Storage (10%), Troubleshooting (30%).
  - **Day 26–27**: Timed CKA practice (3-hour sessions). Review: RBAC, upgrades.
  - **Day 28**: Final review, update cheat sheets, test exam environment.
- **Resources**: [Killer.sh](https://killer.sh), [CKA Curriculum](https://www.cncf.io/certification/cka)

### Final Days (August 1–3, 2025): CKA Exam
- **Goal**: Finalize prep, take CKA exam.
- **Daily**: 6–8 hours (2 hours review, 4–6 hours practice)
- **Tasks**:
  - **Day 29 (August 1)**: Full CKA simulator, review `notes.md`.
  - **Day 30 (August 2)**: Light review, test exam setup (internet, terminal).
  - **Day 31 (August 3)**: CKA exam (2 hours, 15–20 tasks).
- **Post-Exam**: Plan GCP DevOps study (GKE, Cloud Build).

## 🚀 Setup Instructions
1. **Environment**:
   - Use Git Bash or WSL2 (Ubuntu) on Windows.
   - Install: Minikube, kubectl, Docker Desktop.
   - Test: `minikube start`, `kubectl get nodes`.
2. **Folder Structure**:
   - Verify folders: `ls -la ~/Desktop`.
   - If missing, re-run scripts:
     ```bash
     cd ~/Desktop
     chmod +x create_lfs158_folders.sh create_lfd259_folders.sh
     ./create_lfs158_folders.sh
     ./create_lfd259_folders.sh
     ```
3. **VS Code**:
   - Open: `cd ~/Desktop/LFS158-Introduction-to-Kubernetes; code .`
   - Extensions:
     - Markdown All in One
     - Markdown Preview Enhanced
     - Bash IDE
     - GitLens
     - Code Spell Checker
     - Todo Tree
   - Use `settings.json` in course directories for markdown/todo-tree configs.
4. **Snippets**:
   - Add to VS Code `snippets.json`:
     ```json
     {
         "Daily Study Template": {
             "prefix": "daily-study",
             "body": [
                 "# Study Session - ${CURRENT_DATE}",
                 "",
                 "## Today's Focus",
                 "**Chapter(s)**: $1",
                 "**Study Duration**: $2",
                 "",
                 "## Key Learnings",
                 "1. $3",
                 "2. $4",
                 "3. $5",
                 "",
                 "## Commands Practiced",
                 "```bash",
                 "# Commands here",
                 "```"
             ]
         }
     }
     ```

## 📚 Resources
- **LFS158**:
  - [Introduction to Kubernetes (edX)](https://www.edx.org/course/introduction-to-kubernetes)
  - [Kubernetes Documentation](https://kubernetes.io)
  - [Minikube Tutorials](https://minikube.sigs.k8s.io)
- **LFD259**:
  - [Kubernetes for Developers (Linux Foundation)](https://training.linuxfoundation.org)
  - [KodeKloud Labs](https://kodekloud.com)
  - “Kubernetes for Developers” by John Arundel
- **CKA**:
  - [Killer.sh CKA Simulator](https://killer.sh)
  - [CKA Curriculum](https://www.cncf.io/certification/cka)
  - “Kubernetes in Action” by Marko Luksa
- **GCP DevOps**:
  - [Google Cloud Skills Boost](https://cloud.google.com/training)
  - [Coursera GCP DevOps](https://www.coursera.org/professional-certificates/google-cloud-devops-engineer)
  - [Google Cloud Icon Library](https://cloud.google.com/icons)

## ⚡ Success Strategies
- **Time Management**:
  - **Pomodoro**: 25-min study, 5-min breaks.
  - **Morning**: Theory (chapters).
  - **Afternoon**: Labs (Minikube/kind).
  - **Evening**: Review, update `notes.md`.
- **Retention**:
  - **Active Recall**: Test `kubectl` commands (e.g., `kubectl describe pod`).
  - **Spaced Repetition**: Review previous chapters daily.
  - **Documentation**: Summarize in `notes.md`, create `kubectl-commands.md`.
  - **Teaching**: Explain concepts aloud or in Kubernetes Slack.
- **Practical Application**:
  - Deploy sample apps (nginx, WordPress).
  - Join [Kubernetes Slack](https://slack.k8s.io) or r/kubernetes.
  - Share progress on GitHub/LinkedIn.
- **Exam Day**:
  - Practice timed CKA tasks (2-hour sessions).
  - Bookmark [kubernetes.io](https://kubernetes.io), [github.com/kubernetes](https://github.com/kubernetes).
  - Test internet, terminal, and exam interface.

## 🎯 Milestones
- [ ] **July 10**: LFS158 Chapters 1–7
- [ ] **July 17**: LFS158 complete, start LFD259
- [ ] **July 24**: LFD259 Chapters 1–9
- [ ] **July 31**: LFD259 complete, CKA practice
- [ ] **August 3**: CKA exam

## 🚨 Risk Mitigation
- **If Behind**:
  - Prioritize CKA tasks: Troubleshooting (30%), Workloads (25%).
  - Skip non-essential LFD259 labs (e.g., advanced Helm).
  - Extend CKA prep to August 4 if needed.
- **Burnout**:
  - Take Sundays off.
  - Sleep 7–8 hours.
  - Exercise 30 min daily.
- **Technical Issues**:
  - Test Minikube early: `minikube start`.
  - Use backup internet (hotspot).
  - Contact [CNCF Support](https://www.cncf.io/certification/cka) for exam issues.

## 📝 Next Steps (July 4, 2025)
- **Verify Folders**:
  ```bash
  ls -la ~/Desktop
  ```
  If missing, re-run scripts (see Setup Instructions).
- **Set Up Environment**:
  - Install Minikube, kubectl, Docker Desktop.
  - Test: `minikube start`, `kubectl get nodes`.
- **Study**:
  - LFS158 Chapter 1 (Introduction), Chapter 2 (Basics).
  - Practice: `kubectl run nginx --image=nginx`.
  - Update: `Chapter-01/notes.md`, `Chapter-02/notes.md`.
- **GCP DevOps Prep**:
  - Post-CKA, use LFD259’s GCP labs for GKE.
  - Start [Google Cloud Skills Boost](https://cloud.google.com/training) (free tier).

## 💡 Final Notes
This study plan builds on your LFS101 knowledge, ensuring hands-on practice with Minikube and alignment with CKA’s practical tasks. Use VS Code for organized note-taking and track progress in `Daily-Progress/`. Share your journey on LinkedIn for job opportunities. Good luck on your CKA and GCP DevOps journey! 🚀

<!-- Tags: #kubernetes #cka #lfs158 #lfd259 #gcp #devops -->