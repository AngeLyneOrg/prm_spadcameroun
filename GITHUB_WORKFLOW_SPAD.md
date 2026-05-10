# 🚀 GUIDE GITHUB PROFESSIONNEL — PRM SPAD CAMEROUN
### Gestion de Projet avec GitHub : Organisation, Workflow, Branches, Issues, PR & CI/CD

---

## 📋 TABLE DES MATIÈRES

1. [Configuration de l'Organisation GitHub](#1-configuration-de-lorganisation-github)
2. [Structure du Repository](#2-structure-du-repository)
3. [Stratégie de Branches (Git Flow)](#3-stratégie-de-branches-git-flow)
4. [Labels GitHub](#4-labels-github)
5. [Milestones (Jalons)](#5-milestones-jalons)
6. [Issues — Création & Gestion](#6-issues--création--gestion)
7. [Branches par Issue](#7-branches-par-issue)
8. [Convention de Messages de Commit](#8-convention-de-messages-de-commit)
9. [Pull Requests (PR)](#9-pull-requests-pr)
10. [GitHub Projects (Kanban)](#10-github-projects-kanban)
11. [GitHub Actions (CI/CD)](#11-github-actions-cicd)
12. [Rôles & Permissions dans l'Org](#12-rôles--permissions-dans-lorg)
13. [Workflow Complet Pas à Pas](#13-workflow-complet-pas-à-pas)

---

## 1. Configuration de l'Organisation GitHub

### 1.1 — Créer le Repository dans l'Org

```
GitHub → Votre Organisation → Repositories → New repository
```

| Paramètre | Valeur |
|-----------|--------|
| **Repository name** | `prm-spad-cameroun` |
| **Description** | `PRM de Suivi Médical Personnalisé - SPAD Cameroun (Django + MongoDB)` |
| **Visibility** | `Private` (projet étudiant médical) |
| **Initialize with README** | ✅ Oui |
| **Add .gitignore** | `Python` |
| **Choose a license** | `MIT` |

### 1.2 — Configurer la Team

```
Organisation → Teams → [Votre Team] → Repositories → Add repository
```

Donner les permissions : **Write** (vous et votre partenaire pouvez push et créer des PR, mais pas supprimer le repo).

### 1.3 — Protéger les branches importantes

```
Repository → Settings → Branches → Add branch ruleset
```

**Règles pour `main` :**
- ✅ Require a pull request before merging
- ✅ Require approvals : **1 reviewer minimum**
- ✅ Dismiss stale pull request approvals when new commits are pushed
- ✅ Require status checks to pass before merging
- ✅ Do not allow bypassing the above settings

**Règles pour `develop` :**
- ✅ Require a pull request before merging
- ✅ Require approvals : **1 reviewer minimum**

---

## 2. Structure du Repository

### 2.1 — Initialisation locale

```bash
# Cloner le repo de l'organisation
git clone https://github.com/VOTRE-ORG/prm-spad-cameroun.git
cd prm-spad-cameroun

# Configurer son identité Git (à faire une seule fois)
git config user.name "VotrePrenom VotreNom"
git config user.email "votre.email@exemple.com"
```

### 2.2 — Fichiers à créer à la racine

```
prm-spad-cameroun/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── user_story.md
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── workflows/
│       └── django_ci.yml
├── .gitignore
├── README.md
└── ... (code Django)
```

### 2.3 — `.gitignore` Python/Django

```gitignore
# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.Python
env/
venv/
*.egg-info/

# Django
*.log
local_settings.py
db.sqlite3
db.sqlite3-journal
media/

# Variables d'environnement (CRITIQUE : ne jamais versionner)
.env
.env.local
.env.production

# IDE
.vscode/
.idea/
*.swp

# Collectstatic
staticfiles/

# MongoDB
*.dump
```

---

## 3. Stratégie de Branches (Git Flow)

### Schéma des branches

```
main ──────────────────────────────────────────────► Production stable
  │
  └──► develop ────────────────────────────────────► Intégration des features
          │
          ├──► feature/1-authentification-utilisateurs
          ├──► feature/2-dashboard-admin
          ├──► feature/5-gestion-patients-crud
          ├──► feature/8-module-visites
          │
          └──► fix/12-correction-login-redirect
                hotfix/15-securite-csrf-token   (→ merge aussi dans main)
```

### Règle des 4 types de branches

| Type | Format | Rôle | Merge vers |
|------|--------|------|-----------|
| `main` | `main` | Code en production, stable, taggé | — |
| `develop` | `develop` | Version stable en développement | `main` (via release) |
| `feature/` | `feature/[N°issue]-[description]` | Nouvelle fonctionnalité | `develop` |
| `fix/` | `fix/[N°issue]-[description]` | Correction de bug non critique | `develop` |
| `hotfix/` | `hotfix/[N°issue]-[description]` | Correction urgente en prod | `main` + `develop` |
| `release/` | `release/v1.0.0` | Préparation d'une version | `main` + `develop` |

### Créer les branches permanentes

```bash
# Sur GitHub, créer manuellement develop depuis main
# Repository → main → Branch: develop (Create branch)

# En local, récupérer develop
git fetch origin
git checkout develop

# Vérifier les branches
git branch -a
```

---

## 4. Labels GitHub

Les labels permettent de catégoriser visuellement les issues et PR.

**Créer les labels :**
```
Repository → Issues → Labels → New label
```

### Labels à créer

#### 🔷 Type de travail
| Label | Couleur (hex) | Description |
|-------|--------------|-------------|
| `type: feature` | `#0075ca` | Nouvelle fonctionnalité |
| `type: bug` | `#d73a4a` | Quelque chose ne fonctionne pas |
| `type: enhancement` | `#a2eeef` | Amélioration d'une fonctionnalité existante |
| `type: documentation` | `#0075ca` | Améliorations ou ajouts à la documentation |
| `type: refactor` | `#e4e669` | Refactoring du code sans changement fonctionnel |
| `type: test` | `#bfd4f2` | Ajout ou correction de tests |
| `type: chore` | `#e4e669` | Tâches de maintenance (config, dépendances) |

#### 🔶 Priorité
| Label | Couleur (hex) | Description |
|-------|--------------|-------------|
| `priority: critical` | `#b60205` | Bloque le projet, traitement immédiat |
| `priority: high` | `#e11d48` | Important, à traiter dans le sprint actuel |
| `priority: medium` | `#f97316` | Normal, à planifier |
| `priority: low` | `#84cc16` | Peut attendre |

#### 🟢 Statut
| Label | Couleur (hex) | Description |
|-------|--------------|-------------|
| `status: in progress` | `#0052cc` | En cours de développement |
| `status: blocked` | `#e11d48` | Bloqué par une dépendance |
| `status: needs review` | `#fbca04` | En attente de revue de code |
| `status: needs more info` | `#d876e3` | Informations supplémentaires requises |

#### 🟣 Module (pour votre projet SPAD)
| Label | Couleur (hex) | Description |
|-------|--------------|-------------|
| `module: accounts` | `#7c3aed` | App authentification & utilisateurs |
| `module: patients` | `#0891b2` | App gestion des patients |
| `module: visites` | `#059669` | App planification des visites |
| `module: prescriptions` | `#d97706` | App ordonnances & traitements |
| `module: alertes` | `#dc2626` | App notifications & alertes |
| `module: rapports` | `#4f46e5` | App génération de rapports |
| `module: facturation` | `#065f46` | App facturation & paiements |

#### ⚪ Autres
| Label | Couleur (hex) | Description |
|-------|--------------|-------------|
| `good first issue` | `#7057ff` | Bon pour les débutants |
| `help wanted` | `#008672` | Aide externe bienvenue |
| `wontfix` | `#ffffff` | Ne sera pas traité |
| `duplicate` | `#cfd3d7` | Issue déjà signalée |

---

## 5. Milestones (Jalons)

Les milestones regroupent les issues par version ou phase du projet.

**Créer les milestones :**
```
Repository → Issues → Milestones → New milestone
```

### Milestones du projet SPAD

#### 📌 Milestone 1 — Setup & Infrastructure
- **Titre :** `v0.1 — Setup & Infrastructure`
- **Due date :** `[+2 semaines depuis le début]`
- **Description :**
  ```
  Configuration initiale du projet : structure Django, connexion BDD,
  authentification de base, déploiement du template de base.
  ```
- **Issues à inclure :** Setup projet Django, .gitignore, settings, modèle User custom, page login

---

#### 📌 Milestone 2 — Module Patients
- **Titre :** `v0.2 — Module Patients`
- **Due date :** `[+4 semaines]`
- **Description :**
  ```
  CRUD complet pour la gestion des patients : liste, création,
  édition, suppression, dossier médical, recherche.
  ```

---

#### 📌 Milestone 3 — Dashboards & Navigation
- **Titre :** `v0.3 — Dashboards & Navigation`
- **Due date :** `[+6 semaines]`
- **Description :**
  ```
  Interface complète : layout base.html, sidebar, header, footer,
  dashboard Admin et dashboard Médecin avec statistiques.
  ```

---

#### 📌 Milestone 4 — Modules Visites & Prescriptions
- **Titre :** `v0.4 — Visites & Prescriptions`
- **Due date :** `[+9 semaines]`

---

#### 📌 Milestone 5 — Alertes, Rapports & Facturation
- **Titre :** `v0.5 — Alertes, Rapports & Facturation`
- **Due date :** `[+12 semaines]`

---

#### 📌 Milestone 6 — Migration MongoDB & Tests
- **Titre :** `v1.0 — MongoDB + Tests + Déploiement`
- **Due date :** `[+15 semaines]`
- **Description :**
  ```
  Migration complète vers MongoDB, suite de tests, CI/CD GitHub Actions,
  documentation finale, soutenance.
  ```

---

## 6. Issues — Création & Gestion

### 6.1 — Templates d'Issues

Créer `.github/ISSUE_TEMPLATE/feature_request.md` :

```markdown
---
name: ✨ Feature Request (User Story)
about: Proposer une nouvelle fonctionnalité
title: '[FEATURE] '
labels: 'type: feature'
assignees: ''
---

## 📖 User Story

**En tant que** [rôle : Admin / Médecin / Infirmier / Patient]
**Je veux** [action à réaliser]
**Afin de** [bénéfice attendu]

---

## ✅ Critères d'Acceptation

- [ ] Critère 1 : ...
- [ ] Critère 2 : ...
- [ ] Critère 3 : ...

---

## 🎨 Maquette / Wireframe (si applicable)

<!-- Glisser une image ou décrire l'interface attendue -->

---

## 🔗 Dépendances

<!-- Issues qui doivent être terminées avant celle-ci -->
- Dépend de : #[numéro]

---

## 📊 Estimation

- **Complexité :** Faible / Moyenne / Haute
- **Temps estimé :** X heures / X jours
```

---

Créer `.github/ISSUE_TEMPLATE/bug_report.md` :

```markdown
---
name: 🐛 Bug Report
about: Signaler un problème
title: '[BUG] '
labels: 'type: bug, priority: high'
assignees: ''
---

## 🐛 Description du Bug

Description claire et concise du problème.

---

## 🔄 Étapes pour Reproduire

1. Aller sur '...'
2. Cliquer sur '...'
3. Voir l'erreur

---

## ✅ Comportement Attendu

Ce qui devrait se passer.

---

## ❌ Comportement Actuel

Ce qui se passe réellement.

---

## 📸 Screenshots / Logs

```
[Coller les logs d'erreur ici]
```

---

## 🖥️ Environnement

- **OS :** Windows / Linux / macOS
- **Python version :** 3.x
- **Django version :** 4.x
- **Navigateur :** Chrome / Firefox
```

---

### 6.2 — Exemple d'Issues à créer pour le projet SPAD

Voici les **premières issues** à créer immédiatement, dans l'ordre :

```
Issue #1  — [SETUP]  Initialiser le projet Django & configuration settings.py
Issue #2  — [SETUP]  Créer le modèle Utilisateur personnalisé (AbstractBaseUser)
Issue #3  — [SETUP]  Configurer les templates base.html, sidebar, header, footer
Issue #4  — [FEATURE] Implémenter la page de connexion (Login)
Issue #5  — [FEATURE] Créer le modèle Patient & les migrations
Issue #6  — [FEATURE] Implémenter la liste des patients (ListView)
Issue #7  — [FEATURE] Implémenter le formulaire de création patient
Issue #8  — [FEATURE] Dashboard Administrateur avec statistiques
Issue #9  — [FEATURE] Dashboard Médecin avec planning du jour
Issue #10 — [FEATURE] Créer le modèle Visite & planification
Issue #11 — [FEATURE] Module Prescriptions — modèle & CRUD
Issue #12 — [FEATURE] Système d'alertes automatiques
Issue #13 — [CHORE]  Configurer GitHub Actions (CI Django tests)
Issue #14 — [CHORE]  Migrer la base de données vers MongoDB (Djongo)
Issue #15 — [DOCS]   Rédiger la documentation technique finale
```

---

## 7. Branches par Issue

### Convention de nommage

```
[type]/[N°issue]-[description-en-kebab-case]
```

**Exemples concrets pour SPAD :**

```bash
feature/1-setup-django-settings
feature/2-custom-user-model
feature/3-base-template-layout
feature/4-page-connexion-login
feature/5-patient-model-migrations
feature/6-patient-list-view
feature/7-patient-create-form
feature/8-admin-dashboard-stats
feature/9-medecin-dashboard
feature/10-visite-model-planning
fix/4-correction-login-redirect-url
hotfix/13-csrf-token-security
```

### Créer une branche depuis une issue

**Méthode 1 — Via GitHub (recommandée) :**
```
Issue #5 → Development → Create a branch
→ Branch name : feature/5-patient-model-migrations
→ Based on : develop   ← TOUJOURS depuis develop, jamais main
```

**Méthode 2 — En ligne de commande :**
```bash
# S'assurer d'être sur develop et à jour
git checkout develop
git pull origin develop

# Créer et basculer sur la nouvelle branche
git checkout -b feature/5-patient-model-migrations

# Vérifier
git branch
# * feature/5-patient-model-migrations
#   develop
#   main
```

---

## 8. Convention de Messages de Commit

### Format Conventional Commits

```
<type>(<scope>): <description courte en impératif>

[corps optionnel : explication détaillée]

[footer optionnel : références issues, breaking changes]
```

### Types de commits

| Type | Usage | Exemple |
|------|-------|---------|
| `feat` | Nouvelle fonctionnalité | `feat(patients): ajouter le formulaire de création patient` |
| `fix` | Correction de bug | `fix(auth): corriger la redirection après login` |
| `docs` | Documentation | `docs(readme): mettre à jour le guide d'installation` |
| `style` | Formatage CSS/HTML | `style(dashboard): ajuster l'espacement des cartes stats` |
| `refactor` | Refactoring | `refactor(models): renommer le champ statut_patient` |
| `test` | Tests | `test(patients): ajouter tests unitaires PatientModel` |
| `chore` | Maintenance | `chore(deps): mettre à jour Django vers 4.2.8` |
| `ci` | CI/CD | `ci(github-actions): ajouter workflow test Django` |
| `perf` | Performance | `perf(queries): optimiser la requête liste patients` |
| `revert` | Revert commit | `revert: revenir feat(auth) commit abc123` |

### Exemples concrets pour SPAD

```bash
# Commits de setup
git commit -m "chore(setup): initialiser le projet Django prm_spadcameroun"
git commit -m "chore(settings): configurer INSTALLED_APPS et DATABASES SQLite"
git commit -m "chore(gitignore): ajouter règles Python/Django/env"

# Commits de feature
git commit -m "feat(accounts): créer le modèle Utilisateur avec champ role"
git commit -m "feat(accounts): implémenter la vue ConnexionView avec redirection"
git commit -m "feat(templates): ajouter base.html avec layout sidebar/header/footer"
git commit -m "feat(patients): créer le modèle Patient avec champs médicaux"
git commit -m "feat(patients): implémenter PatientListView avec pagination"
git commit -m "feat(dashboard): ajouter les 4 cartes statistiques admin"

# Commits de fix
git commit -m "fix(patients): corriger l'affichage date_naissance format français"
git commit -m "fix(sidebar): corriger le lien actif sur la page patients"

# Commits de migration BD
git commit -m "feat(models): ajouter migration 0001_initial Patient"
git commit -m "chore(db): configurer Djongo pour connexion MongoDB"

# Commits de docs
git commit -m "docs(readme): ajouter section installation MongoDB"
```

### Règles d'or pour les commits

```
✅ Un commit = une seule chose logique
✅ Description en français ou anglais, mais COHÉRENT dans tout le projet
✅ Verbe à l'infinitif : "ajouter", "corriger", "configurer"
✅ Maximum 72 caractères pour la première ligne
✅ Référencer l'issue : "fix(auth): corriger login redirect - closes #4"
❌ Ne jamais commiter : .env, db.sqlite3, __pycache__, media/
❌ Éviter : "fix bug", "update", "wip", "corrections diverses"
```

---

## 9. Pull Requests (PR)

### 9.1 — Template de PR

Créer `.github/PULL_REQUEST_TEMPLATE.md` :

```markdown
## 📋 Description

Résumé clair de ce que cette PR apporte.

Closes #[numéro de l'issue]

---

## 🔄 Type de changement

- [ ] 🐛 Bug fix (correction non-breaking)
- [ ] ✨ Nouvelle feature (ajout non-breaking)
- [ ] 💥 Breaking change (changement qui casse une fonctionnalité existante)
- [ ] 📝 Documentation
- [ ] 🔧 Refactoring / Maintenance

---

## ✅ Checklist avant merge

- [ ] Mon code suit les conventions du projet (PEP 8, Conventional Commits)
- [ ] J'ai relu mon propre code avant de demander la review
- [ ] Les migrations Django sont incluses et fonctionnelles
- [ ] Les URLs sont correctement configurées
- [ ] Les templates s'affichent correctement
- [ ] Aucun fichier sensible (.env, db.sqlite3) n'est commité
- [ ] La branche est à jour avec `develop`

---

## 📸 Screenshots (si modification d'interface)

| Avant | Après |
|-------|-------|
| screenshot | screenshot |

---

## 🧪 Comment tester

1. `git checkout feature/X-nom-branche`
2. `pip install -r requirements.txt`
3. `python manage.py migrate`
4. `python manage.py runserver`
5. Naviguer vers [URL concernée]
6. Vérifier [comportement attendu]
```

### 9.2 — Créer une PR

```
feature/5-patient-model-migrations → develop

GitHub → Pull Requests → New pull request
Base: develop  ←  Compare: feature/5-patient-model-migrations

Titre : feat(patients): créer le modèle Patient et les migrations #5
```

**Règles pour les PR :**
- **Base branch toujours `develop`** (sauf hotfix → `main`)
- **Titre = format Conventional Commit**
- **Assigner un reviewer** (votre partenaire)
- **Lier l'issue** dans la description : `Closes #5`
- **Attendre l'approbation** avant de merger
- **Merger avec "Squash and merge"** pour garder un historique propre

### 9.3 — Processus de Review

```
1. Développeur ouvre la PR → Assigne le partenaire comme Reviewer
2. Reviewer lit le code → Laisse des commentaires sur les lignes
3. Développeur répond et/ou corrige → Re-push sur la même branche
4. Reviewer approuve ✅ → Le développeur ORIGINAL merge
5. Branche feature supprimée automatiquement après merge
```

---

## 10. GitHub Projects (Kanban)

### Créer le tableau Kanban

```
Organisation → Projects → New project → Board (Kanban)
Nom : "PRM SPAD Cameroun — Sprint Board"
```

### Colonnes à créer

| Colonne | Description | Auto-ajout |
|---------|-------------|-----------|
| 📋 **Backlog** | Toutes les issues non planifiées | Issues ouvertes |
| 🎯 **Sprint Actuel** | Issues du sprint en cours | — |
| 🔄 **En cours** | Issues en développement actif | Issues avec label `status: in progress` |
| 👀 **En Review** | PR ouverte, en attente de review | PR ouvertes |
| ✅ **Terminé** | Issues fermées / PR mergées | Issues fermées |

### Lier les Issues au Project

```
Issue → Projects → Sélectionner "PRM SPAD Cameroun — Sprint Board"
```

### Utiliser les Iterations (Sprints)

```
Project → Settings → Iterations → Add iteration
Sprint 1 : [Date début] → [Date fin, 2 semaines]
Sprint 2 : ...
```

---

## 11. GitHub Actions (CI/CD)

### Workflow Django CI

Créer `.github/workflows/django_ci.yml` :

```yaml
name: 🧪 Django CI

on:
  push:
    branches: [ develop, main ]
  pull_request:
    branches: [ develop, main ]

jobs:
  test:
    runs-on: ubuntu-latest
    name: Tests Django

    steps:
    - name: 📥 Checkout du code
      uses: actions/checkout@v4

    - name: 🐍 Configurer Python 3.11
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'

    - name: 📦 Installer les dépendances
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: 🔍 Vérifier le style de code (flake8)
      run: |
        pip install flake8
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

    - name: 🗄️ Appliquer les migrations
      run: |
        python manage.py migrate --settings=prm_spadcameroun.settings
      env:
        SECRET_KEY: ${{ secrets.DJANGO_SECRET_KEY }}
        DEBUG: 'True'

    - name: ✅ Lancer les tests
      run: |
        python manage.py test --settings=prm_spadcameroun.settings
      env:
        SECRET_KEY: ${{ secrets.DJANGO_SECRET_KEY }}
        DEBUG: 'True'
```

### Configurer les Secrets GitHub

```
Repository → Settings → Secrets and variables → Actions → New secret

Secrets à créer :
- DJANGO_SECRET_KEY : [votre clé secrète Django]
- MONGODB_URI : [URI connexion MongoDB]
```

---

## 12. Rôles & Permissions dans l'Org

### Configuration de la Team

```
Organisation → Teams → [Votre Team] → Members
```

| Membre | Rôle dans la Team | Permissions |
|--------|------------------|-------------|
| Vous (chef de projet) | **Maintainer** | Peut gérer la team, approuver les PR, merger dans main |
| Partenaire | **Member** | Peut push sur les branches feature, ouvrir des PR |

### Assignation des responsabilités

Dès le début, définir qui s'occupe de quoi :

| Module | Responsable Principal | Responsable Secondaire (Reviewer) |
|--------|----------------------|----------------------------------|
| `accounts` + Auth | Vous | Partenaire |
| `patients` | Partenaire | Vous |
| `visites` | Vous | Partenaire |
| `prescriptions` | Partenaire | Vous |
| `alertes` | Vous | Partenaire |
| `rapports` | Partenaire | Vous |
| `facturation` | Partenaire | Vous |
| CI/CD + MongoDB | Vous | — |
| Templates/CSS | Partenaire | Vous |

**Règle :** Le responsable principal développe → Le secondaire review la PR.

---

## 13. Workflow Complet Pas à Pas

### Scénario : Implémenter la feature "Liste des Patients" (Issue #6)

```bash
# ═══ ÉTAPE 1 : Créer l'issue sur GitHub ═══════════════════════════════════
# Sur GitHub → Issues → New issue
# Titre : [FEATURE] Implémenter la liste des patients (ListView)
# Labels : type: feature, module: patients, priority: high
# Milestone : v0.2 — Module Patients
# Assignee : [Responsable]
# → L'issue reçoit le numéro #6

# ═══ ÉTAPE 2 : Créer la branche depuis l'issue ════════════════════════════
# Sur GitHub : Issue #6 → Development → Create a branch
# Nom : feature/6-patient-list-view
# Base : develop

# ═══ ÉTAPE 3 : Récupérer la branche en local ══════════════════════════════
git checkout develop
git pull origin develop
git checkout -b feature/6-patient-list-view
# ou : git fetch origin && git checkout feature/6-patient-list-view

# ═══ ÉTAPE 4 : Développer ════════════════════════════════════════════════
# Travailler sur : apps/patients/views.py, urls.py, templates/patients/...

# ═══ ÉTAPE 5 : Commiter progressivement ══════════════════════════════════
git add apps/patients/models.py
git commit -m "feat(patients): créer le modèle Patient avec champs médicaux"

git add apps/patients/views.py
git commit -m "feat(patients): implémenter PatientListView avec pagination"

git add apps/patients/urls.py
git commit -m "feat(patients): configurer les URLs de l'app patients"

git add templates/patients/
git commit -m "feat(patients): ajouter template liste_patients.html avec tableau"

git add templates/patients/detail_patient.html
git commit -m "feat(patients): ajouter template detail_patient.html"

# ═══ ÉTAPE 6 : Pousser la branche sur GitHub ══════════════════════════════
git push origin feature/6-patient-list-view

# ═══ ÉTAPE 7 : Ouvrir la Pull Request ════════════════════════════════════
# Sur GitHub → Pull Requests → New PR
# Base: develop ← Compare: feature/6-patient-list-view
# Titre : feat(patients): implémenter la liste des patients - closes #6
# Reviewer : [Partenaire]
# Labels : type: feature, status: needs review, module: patients

# ═══ ÉTAPE 8 : Review par le partenaire ══════════════════════════════════
# Partenaire examine le code → Approve ou Request changes
# Si corrections : recommencer depuis Étape 4

# ═══ ÉTAPE 9 : Merger la PR ══════════════════════════════════════════════
# PR approuvée → "Squash and merge" → Confirmer
# develop reçoit le code → Issue #6 se ferme automatiquement
# Supprimer la branche feature/6-patient-list-view (bouton sur la PR)

# ═══ ÉTAPE 10 : Nettoyer en local ════════════════════════════════════════
git checkout develop
git pull origin develop
git branch -d feature/6-patient-list-view
```

---

### Release : Créer la version v0.2

```bash
# Quand toutes les issues du Milestone v0.2 sont fermées
git checkout develop
git pull origin develop

# Créer la branche de release
git checkout -b release/v0.2.0

# Mettre à jour la version dans le code si nécessaire
# git commit -m "chore(release): préparer la version v0.2.0"

# Merger dans main
git checkout main
git merge release/v0.2.0 --no-ff
git tag -a v0.2.0 -m "Release v0.2.0 — Module Patients complet"
git push origin main --tags

# Merger aussi dans develop (pour les éventuels fix de la release)
git checkout develop
git merge release/v0.2.0 --no-ff
git push origin develop

# Supprimer la branche de release
git branch -d release/v0.2.0
git push origin --delete release/v0.2.0

# Sur GitHub : créer une Release
# Releases → Create a new release
# Tag : v0.2.0
# Title : v0.2.0 — Module Patients
# Description : [liste des fonctionnalités ajoutées]
```

---

## 📌 Récapitulatif des Commandes Git Essentielles

```bash
# État du projet
git status                          # Voir les fichiers modifiés
git log --oneline --graph           # Historique des commits (visuel)
git branch -a                       # Voir toutes les branches

# Synchronisation
git fetch origin                    # Récupérer sans merger
git pull origin develop             # Récupérer et merger develop

# Branches
git checkout -b feature/X-nom       # Créer + basculer
git checkout develop                # Basculer sur develop
git branch -d feature/X-nom         # Supprimer branche locale

# Commits
git add fichier.py                  # Stager un fichier spécifique
git add .                           # Stager tous les fichiers
git commit -m "feat(scope): message" # Commiter
git push origin feature/X-nom       # Pousser la branche

# Annuler
git restore fichier.py              # Annuler modifications non stagées
git restore --staged fichier.py     # Déstaguer un fichier
git revert HEAD                     # Créer un commit d'annulation (safe)
```

---

> **💡 Conseil final :** Commencez par créer les 3 premiers éléments dans l'ordre :
> 1. **Repository** dans l'Org avec les bonnes protections de branches
> 2. **Tous les labels** (10 minutes, une fois pour toutes)
> 3. **Les 3 premiers milestones** et les **15 premières issues**
>
> Puis adoptez le workflow : `Issue → Branche → Commits → PR → Review → Merge → Repeat`
