# Salesforce DX + GitHub — Command Reference
> Day 1 · pavan-enterprisearch/rca-learning

---

## Node.js & CLI Verification

```bash
node --version              # verify Node.js is installed
npm --version               # verify npm is installed
sf --version                # verify Salesforce CLI is installed
sf --help                   # list all available SF CLI commands
git --version               # verify Git is installed
```

---

## Project Setup

```bash
mkdir ~/salesforce-projects                     # create projects folder
cd ~/salesforce-projects                        # navigate into it
sf project generate --name rca-learning         # create SFDX project
cd rca-learning                                 # navigate into project
code .                                          # open project in VS Code
```

---

## Git — Local Setup

```bash
git init                                        # initialise local repository
git add .                                       # stage all files
git add [file]                                  # stage one specific file
git commit -m "initial project setup"           # commit with message
git status                                      # check staged/unstaged/untracked files
git diff                                        # see unstaged changes
git diff --staged                               # see staged changes
git log --oneline                               # see commit history
```

---

## GitHub — Remote Setup (one-time per project)

```bash
git remote add origin https://github.com/pavan-enterprisearch/rca-learning.git
git branch -M main                              # rename branch to main
git push -u origin main                         # first push + set upstream
git push                                        # every push after the first
git pull                                        # get latest changes from GitHub
```

---

## Salesforce Org Connection

```bash
sf org login web --alias rca-dev                # connect org via browser login
sf config set target-org rca-dev                # set default org for this project
sf org display --target-org rca-dev             # verify org connection details
sf org open --target-org rca-dev                # open org in browser
```

---

## Metadata — Pull & Push

```bash
# Pull from org to local
sf project retrieve start --metadata ApexClass

# Push from local to org
sf project deploy start --source-dir force-app/main/default/classes

# Generate a new Apex class locally
sf apex generate class --name BrokerUtils --output-dir force-app/main/default/classes
```

---

## Daily Workflow (run every single day)

```bash
git status                                      # check what changed
git add .                                       # stage everything
git commit -m "description of change"           # commit with message
git push                                        # push to GitHub
```

---

## Cheat Code — Help for Any Command

```bash
git commit --help                               # help for any git command
sf project retrieve --help                      # help for any sf command
sf org login --help
sf apex run --help
```

---

## Key Concepts

### The Three Git Zones
```
WORKING DIRECTORY  →  STAGING AREA  →  REPOSITORY
(files you edit)      (git add)         (git commit)
```

### The Three-Way Loop
```
VS Code  →  sf deploy  →  Salesforce Org
VS Code  →  git push   →  GitHub
```

### SF CLI Command Pattern
```
sf  [topic]   [action]   [flags]
sf  org       login      --alias rca-dev
sf  project   retrieve   --metadata ApexClass
sf  project   deploy     --source-dir force-app/
sf  apex      generate   --name MyClass
```

### `-` vs `--` Flags
| Style | Example | When to use |
|---|---|---|
| `-m` | `git commit -m "msg"` | Single letter shorthand |
| `--message` | `git commit --message "msg"` | Full word, more readable |

> One dash = one letter. Two dashes = full word. Same outcome.

---

## Project Structure

```
rca-learning/
├── force-app/
│   └── main/
│       └── default/
│           ├── classes/      ← Apex classes
│           ├── lwc/          ← Lightning Web Components
│           └── ...
├── .sfdx/                    ← CLI cache (never commit this)
├── sfdx-project.json         ← Project config
├── .forceignore              ← Like .gitignore for Salesforce
└── COMMANDS.md               ← This file
```

---

*Last updated: Day 1 — GitHub + Salesforce DX setup*
