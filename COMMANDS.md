Salesforce DX + GitHub — Command Reference
pavan-enterprisearch/rca-learning
⸻
DAY 1 — GitHub + Salesforce DX Setup

Node.js & CLI Verification
node --version              # verify Node.js is installed
npm --version               # verify npm is installed
sf --version                # verify Salesforce CLI is installed
sf --help                   # list all available SF CLI commands
git --version               # verify Git is installed


Project Setup
mkdir ~/salesforce-projects                     # create projects folder
cd ~/salesforce-projects                        # navigate into it
sf project generate --name rca-learning         # create SFDX project
cd rca-learning                                 # navigate into project
code .                                          # open project in VS Code


Git — Local Setup
git init                                        # initialise local repository
git add .                                       # stage all files
git add [file]                                  # stage one specific file
git commit -m "initial project setup"           # commit with message
git status                                      # check staged/unstaged/untracked files
git diff                                        # see unstaged changes
git diff --staged                               # see staged changes
git log --oneline                               # see commit history


GitHub — Remote Setup (one-time per project)
git remote add origin https://github.com/pavan-enterprisearch/rca-learning.git
git branch -M main                              # rename branch to main
git push -u origin main                         # first push + set upstream
git push                                        # every push after the first
git pull                                        # get latest changes from GitHub


Salesforce Org Connection
sf org login web --alias rca-dev                # connect org via browser login
sf config set target-org rca-dev                # set default org for this project
sf org display --target-org rca-dev             # verify org connection details
sf org open --target-org rca-dev                # open org in browser
sf org list                                     # list all connected orgs


Metadata — Pull & Push
# Pull from org to local
sf project retrieve start --metadata ApexClass

# Preview what changed in org (no pull)
sf project retrieve preview

# Push from local to org
sf project deploy start --source-dir force-app/main/default/classes

# Generate a new Apex class locally
sf apex generate class --name BrokerUtils --output-dir force-app/main/default/classes


Daily Workflow
git status                                      # check what changed
git add .                                       # stage everything
git commit -m "description of change"           # commit with message
git push                                        # push to GitHub


Cheat Code — Help for Any Command
git commit --help                               # help for any git command
sf project retrieve --help                      # help for any sf command

⸻
DAY 2 — Scratch Orgs + Source Tracking + CI/CD Pipeline

Dev Hub Setup
# Authorise Developer Edition as Dev Hub
sf org login web --alias rca-devhub --set-default-dev-hub


Scratch Org — Create & Manage
# Create scratch org from config file
sf org create scratch \
  --definition-file config/project-scratch-def.json \
  --alias rca-scratch \
  --duration-days 30 \
  --set-default

sf org list                                        # list all orgs including scratch orgs
sf org open --target-org rca-scratch               # open scratch org in browser
sf org display --target-org rca-scratch --verbose  # show details + auth token
sf org generate password --target-org rca-scratch  # set a password for manual login


Source Tracking (scratch orgs only)
# Preview what changed in org — no pull, just shows changes
sf project retrieve preview

# Pull tracked changes from org to local
sf project retrieve start

# Push local changes to org
sf project deploy start --source-dir force-app/main/default/classes


GitHub Actions — CI/CD Pipeline Setup
# Create workflows directory
mkdir -p .github/workflows


Workflow file location: .github/workflows/deploy.yml

Get SFDX Auth URL for GitHub Secret:
sf org display --target-org rca-devhub --verbose
# Copy the "Sfdx Auth Url" value
# GitHub repo → Settings → Secrets and variables → Actions → New repository secret
# Name: SFDX_AUTH_URL | Value: the auth URL you copied

⸻
Key Concepts

The Three Git Zones
WORKING DIRECTORY  →  STAGING AREA  →  REPOSITORY
(files you edit)      (git add)         (git commit)


The Three-Way Loop
VS Code  →  sf deploy  →  Salesforce Org
VS Code  →  git push   →  GitHub


Two Flows — Know the Difference
Manual dev flow:
VS Code → sf project deploy → Scratch Org (daily development)

CI/CD flow:
VS Code → git push → GitHub Actions → Developer Edition / QA Sandbox (automated)


Scratch Org vs Developer Edition
Feature	Developer Edition	Scratch Org
Source tracking	❌ No	✅ Yes
Lifespan	Permanent	1–30 days
Purpose	Stable reference org	Isolated dev environment
Created via	Signup	SF CLI + Dev Hub
Login	Username + password	sf org open

SF CLI Command Pattern
sf  [topic]   [action]   [flags]
sf  org       login      --alias rca-dev
sf  org       open       --target-org rca-scratch
sf  project   retrieve   --metadata ApexClass
sf  project   deploy     --source-dir force-app/
sf  apex      generate   --name MyClass


- vs -- Flags
Style	Example	When to use
-m	git commit -m "msg"	Single letter shorthand
--message	git commit --message "msg"	Full word, more readable

One dash = one letter. Two dashes = full word. Same outcome.

Why Profiles Break Cross-Org Deploys
Profiles contain permissions for every org feature. Different org types have 
different feature sets — a permission valid in one org may not exist in another. 
Solution: deploy classes only via CI/CD. Use Permission Sets instead of Profiles 
— they are portable across org types.
⸻
Project Structure
rca-learning/
├── .github/
│   └── workflows/
│       └── deploy.yml            ← CI/CD pipeline
├── config/
│   └── project-scratch-def.json  ← scratch org blueprint
├── force-app/
│   └── main/
│       └── default/
│           ├── classes/          ← Apex classes
│           ├── lwc/              ← Lightning Web Components
│           └── ...
├── .sfdx/                        ← CLI cache (never commit this)
├── sfdx-project.json             ← Project config
├── .forceignore                  ← Like .gitignore for Salesforce
└── COMMANDS.md                   ← This file

⸻
GitHub Actions Pipeline — How It Works
git push to main
      ↓
GitHub spins up Ubuntu machine
      ↓
Installs Salesforce CLI
      ↓
Authenticates via SFDX_AUTH_URL secret
      ↓
Deploys force-app/main/default/classes to org
      ↓
Reports ✓ success or ✗ failure

⸻
Team of 5 Developers — The Full Pipeline
Individual scratch orgs       Feature branches       main branch
(isolated development)   →   (git push)         →   (pull request + review)
                                                           ↓
                                                     QA Sandbox (auto deploy via CI/CD)
                                                           ↓
                                                     Production (manual release)

⸻
Day 1 — GitHub + Salesforce DX setup 
Day 2 — Scratch Orgs + Source Tracking + CI/CD Pipeline