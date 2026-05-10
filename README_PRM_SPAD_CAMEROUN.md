# 🏥 PRM DE SUIVI MÉDICAL PERSONNALISÉ — SPAD CAMEROUN
### Projet Django Étudiant | Système de Gestion de Soins à Domicile

---

## 📋 TABLE DES MATIÈRES

1. [Présentation du Projet](#-présentation-du-projet)
2. [Analyse UML](#-analyse-uml)
   - [Diagramme de Cas d'Utilisation](#diagramme-de-cas-dutilisation)
   - [Diagramme de Classes](#diagramme-de-classes)
3. [Architecture du Projet Django](#-architecture-du-projet-django)
4. [Installation & Configuration](#-installation--configuration)
   - [Étape 1 : Environnement virtuel & dépendances](#étape-1--environnement-virtuel--dépendances)
   - [Étape 2 : Configuration SQLite (démarrage)](#étape-2--configuration-sqlite-démarrage)
   - [Étape 3 : Migration vers MongoDB](#étape-3--migration-vers-mongodb)
5. [Applications Django (startapp)](#-applications-django-startapp)
6. [Structure des Fichiers du Projet](#-structure-des-fichiers-du-projet)
7. [Templates & Layout](#-templates--layout)
   - [base.html](#bashtml)
   - [Sidebar & Header](#sidebar--header)
   - [Footer](#footer)
8. [Dashboards & Vues à Implémenter](#-dashboards--vues-à-implémenter)
   - [Dashboard Administrateur](#dashboard-administrateur)
   - [Dashboard Médecin/Infirmier](#dashboard-médecininfirmier)
9. [Configuration des URLs](#-configuration-des-urls)
10. [Commandes Récapitulatives](#-commandes-récapitulatives)

---

## 🎯 Présentation du Projet

**SPAD Cameroun** (Service de Prestation et d'Assistance à Domicile) est une structure de soins à domicile basée au Cameroun. Ce projet est un **PRM (Portail de Relation et Management)** de suivi médical personnalisé conçu pour :

- Gérer les patients pris en charge à domicile
- Suivre les prescriptions et traitements médicaux
- Planifier et enregistrer les visites des soignants
- Générer des rapports de suivi médical
- Alerter sur les cas critiques

> **Contexte Académique** : Ce projet est réalisé dans un cadre étudiant, visant à illustrer la mise en place d'une application web médicale avec Django et MongoDB.

---

## 📊 Analyse UML

### Diagramme de Cas d'Utilisation

```
+----------------------------------------------------------+
|               SYSTÈME PRM SPAD CAMEROUN                  |
|                                                          |
|  +------------------+      +------------------------+   |
|  |  <<acteur>>      |      |  <<acteur>>            |   |
|  |  Administrateur  |      |  Médecin / Infirmier   |   |
|  +--------+---------+      +-----------+------------+   |
|           |                            |                 |
|     +-----+------+             +-------+-------+         |
|     |            |             |               |         |
|  [Gérer       [Gérer       [Consulter    [Enregistrer    |
|  utilisateurs] rapports]   dossier       visite]        |
|                             patient]                     |
|  [Gérer                [Prescrire    [Mettre à jour     |
|  paramètres]           traitement]   état patient]      |
|                                                          |
|  +------------------+      +------------------------+   |
|  |  <<acteur>>      |      |  <<acteur>>            |   |
|  |  Patient /       |      |  Secrétaire /          |   |
|  |  Famille         |      |  Réceptionniste        |   |
|  +--------+---------+      +-----------+------------+   |
|           |                            |                 |
|  [Consulter       ]            [Enregistrer             |
|  résultats propres]            patient]                 |
|  [Voir planning   ]            [Planifier               |
|  visites         ]             rendez-vous]             |
|                                [Gérer facturation]      |
+----------------------------------------------------------+

ACTEURS SECONDAIRES :
  - Système d'Alerte   : envoie notifications automatiques
  - Système de Rapport : génère exports PDF/Excel
```

#### Cas d'utilisation détaillés

| Acteur | Cas d'Utilisation | Description |
|--------|------------------|-------------|
| Administrateur | Gérer Utilisateurs | CRUD complet sur comptes médecins, infirmiers, secrétaires |
| Administrateur | Gérer Paramètres | Configuration globale de l'application |
| Administrateur | Voir Rapports Globaux | Statistiques agrégées sur tous les patients |
| Médecin | Créer Dossier Patient | Enregistrer un nouveau patient avec antécédents |
| Médecin | Prescrire Traitement | Ajouter ordonnance liée à un patient |
| Médecin | Planifier Visite | Créer un rendez-vous de suivi à domicile |
| Infirmier | Enregistrer Visite | Documenter l'exécution d'une visite planifiée |
| Infirmier | Mettre à Jour État | Modifier les constantes vitales d'un patient |
| Secrétaire | Enregistrer Patient | Saisie initiale des informations administratives |
| Secrétaire | Gérer Facturation | Suivi des paiements et factures |
| Patient/Famille | Consulter Résultats | Accès restreint au dossier médical personnel |

---

### Diagramme de Classes

```
+-------------------+          +----------------------+
|      Utilisateur  |          |       Patient        |
+-------------------+          +----------------------+
| - id              |          | - id                 |
| - nom             |          | - numero_dossier     |
| - prenom          |          | - nom                |
| - email           |          | - prenom             |
| - mot_de_passe    |          | - date_naissance      |
| - role            |          | - sexe               |
| - telephone       |          | - adresse            |
| - date_creation   |          | - telephone          |
+-------------------+          | - groupe_sanguin     |
| + se_connecter()  |          | - allergies          |
| + se_deconnecter()|          | - antecedents        |
| + modifier_profil()|         | - statut             |  ← actif/inactif/décédé
+-------------------+          | - medecin_referent   |
         △                     | - date_admission     |
         |                     +----------------------+
    +----+----+                | + get_dossier()      |
    |         |                | + get_visites()      |
 Medecin  Infirmier            | + get_prescriptions()|
    |         |                +----------+-----------+
    |         |                           |
    |    +----+------+           +--------+--------+
    |    | ZoneSoin  |           |   DossierMedical |
    |    +-----------+           +-----------------+
    |    | - id      |           | - id            |
    |    | - nom     |           | - patient       |◄--+
    |    | - region  |           | - date_creation |o   |
    |    +-----------+           | - observations  |   |
    |                            | - documents     |   |
    |                            +-----------------+   |
    |                                                   |
    +----+---------------------+                        |
         |                     |                        |
   +-----+------+    +---------+------+                 |
   |  Visite    |    |  Prescription  |                 |
   +------------+    +----------------+                 |
   | - id       |    | - id           |                 |
   | - patient  |◄---| - patient      |◄----------------+
   | - medecin  |    | - medecin      |
   | - infirmier|    | - medicaments  |
   | - date_prev|    | - posologie    |
   | - date_eff |    | - date_debut   |
   | - statut   |    | - date_fin     |
   | - rapport  |    | - renouvellable|
   | - constantes    | - statut       |
   +------------+    +----------------+
         |
   +-----+-------+
   |  Alerte     |
   +-------------+
   | - id        |
   | - patient   |
   | - type      |  ← critique/urgence/rappel
   | - message   |
   | - date      |
   | - lue       |
   +-------------+

+-------------------+
|   Facturation     |
+-------------------+
| - id              |
| - patient         |
| - visite          |
| - montant         |
| - statut_paiement |
| - date_facture    |
| - mode_paiement   |
+-------------------+
```

---

## 🏗️ Architecture du Projet Django

```
prm_spadcameroun/                   ← Dossier racine du projet
│
├── prm_spadcameroun/               ← Package de configuration principal
│   ├── __init__.py
│   ├── settings.py                 ← Paramètres (SQLite puis MongoDB)
│   ├── urls.py                     ← URLs racines
│   ├── wsgi.py
│   └── asgi.py
│
├── apps/                           ← Toutes les applications Django
│   ├── accounts/                   ← Gestion utilisateurs & authentification
│   ├── patients/                   ← Gestion des patients
│   ├── visites/                    ← Planification et suivi des visites
│   ├── prescriptions/              ← Ordonnances et traitements
│   ├── alertes/                    ← Système de notifications
│   ├── rapports/                   ← Génération de rapports
│   └── facturation/                ← Gestion des factures
│
├── templates/                      ← Templates HTML globaux
│   ├── base.html
│   ├── partials/
│   │   ├── _header.html
│   │   ├── _sidebar.html
│   │   └── _footer.html
│   ├── accounts/
│   ├── patients/
│   ├── visites/
│   └── ...
│
├── static/                         ← Fichiers statiques
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── main.js
│   └── img/
│       └── logo_spad.png
│
├── media/                          ← Fichiers uploadés (documents patients)
├── requirements.txt
├── .env                            ← Variables d'environnement (ne pas versionner)
└── manage.py
```

---

## ⚙️ Installation & Configuration

### Étape 1 : Environnement virtuel & dépendances

```bash
# 1. Créer et activer l'environnement virtuel
python -m venv env

# Sur Windows :
env\Scripts\activate

# Sur Linux/macOS :
source env/bin/activate

# 2. Installer les dépendances de base
pip install django
pip install python-dotenv
pip install pillow          # pour les images/documents
pip install django-crispy-forms  # formulaires stylisés

# 3. Générer le fichier requirements.txt
pip freeze > requirements.txt
```

---

### Étape 2 : Configuration SQLite (démarrage)

SQLite est la base de données par défaut de Django. Elle ne nécessite aucune installation supplémentaire — parfaite pour commencer le développement.

**Dans `prm_spadcameroun/settings.py` :**

```python
# ─── BASE DE DONNÉES : SQLite (configuration par défaut) ───────────────────
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

**Effectuer les migrations initiales :**

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser   # Créer l'administrateur
python manage.py runserver         # Lancer le serveur de développement
```

> ✅ À ce stade, l'application tourne avec SQLite. Développez et testez vos fonctionnalités librement avant de migrer.

---

### Étape 3 : Migration vers MongoDB

MongoDB est une base de données NoSQL orientée documents. Pour l'utiliser avec Django, nous utilisons **Djongo**, un ORM qui traduit les requêtes Django ORM en requêtes MongoDB, permettant de garder la syntaxe Django habituelle (`models.py`, migrations, etc.).

#### 3.1 — Installer MongoDB

**Sur Windows :**
1. Télécharger l'installeur sur [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
2. Installer en activant l'option "Run MongoDB as a service"
3. MongoDB tournera sur `localhost:27017` par défaut

**Sur Ubuntu/Debian :**
```bash
sudo apt update
sudo apt install -y mongodb
sudo systemctl start mongodb
sudo systemctl enable mongodb
```

**Sur macOS (via Homebrew) :**
```bash
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

#### 3.2 — Installer Djongo et les dépendances

```bash
pip install djongo
pip install pymongo
pip install dnspython   # si utilisation de MongoDB Atlas (cloud)

# Mettre à jour requirements.txt
pip freeze > requirements.txt
```

#### 3.3 — Modifier `settings.py` pour MongoDB

```python
# ─── BASE DE DONNÉES : MongoDB via Djongo ──────────────────────────────────
DATABASES = {
    'default': {
        'ENGINE': 'djongo',
        'NAME': 'prm_spad_db',       # Nom de votre base de données MongoDB
        'ENFORCE_SCHEMA': False,      # Flexibilité du schéma NoSQL
        'CLIENT': {
            'host': 'localhost',
            'port': 27017,
            # Si MongoDB nécessite une authentification :
            # 'username': 'admin',
            # 'password': 'votre_mot_de_passe',
            # 'authSource': 'admin',
        }
    }
}
```

> **Pour MongoDB Atlas (cloud)**, remplacez `host` par votre URI de connexion :
> ```python
> 'host': 'mongodb+srv://<user>:<password>@cluster0.mongodb.net/prm_spad_db'
> ```

#### 3.4 — Exporter les données SQLite et migrer

```bash
# 1. Sauvegarder les données existantes (si vous en avez)
python manage.py dumpdata > backup_sqlite.json

# 2. Supprimer les anciennes migrations (recommandé pour repartir proprement)
#    Dans chaque app, supprimer le contenu du dossier migrations/
#    sauf __init__.py

# 3. Recréer les migrations pour MongoDB
python manage.py makemigrations

# 4. Appliquer les migrations sur MongoDB
python manage.py migrate

# 5. Recharger les données sauvegardées (optionnel)
python manage.py loaddata backup_sqlite.json
```

#### 3.5 — Vérifier la connexion

```bash
# Ouvrir le shell Django
python manage.py shell

# Dans le shell, tester la connexion :
from django.db import connection
connection.ensure_connection()
print("✅ Connexion MongoDB réussie !")
```

> **Note importante :** Djongo supporte la syntaxe standard des `models.py` Django. Vos modèles définis avec `models.Model` fonctionneront directement, mais certaines fonctionnalités avancées de Django (comme les requêtes complexes avec `annotate`/`aggregate`) peuvent avoir des limitations avec Djongo. Pour des projets avancés, `mongoengine` est une alternative directe.

---

## 📦 Applications Django (startapp)

Voici les applications à créer et leur rôle dans le projet.

### Créer toutes les applications

```bash
# Depuis la racine du projet
mkdir apps
cd apps

python ../manage.py startapp accounts
python ../manage.py startapp patients
python ../manage.py startapp visites
python ../manage.py startapp prescriptions
python ../manage.py startapp alertes
python ../manage.py startapp rapports
python ../manage.py startapp facturation
```

### Enregistrer les apps dans `settings.py`

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Applications tierces
    'crispy_forms',

    # Applications du projet
    'apps.accounts',
    'apps.patients',
    'apps.visites',
    'apps.prescriptions',
    'apps.alertes',
    'apps.rapports',
    'apps.facturation',
]
```

> **Important :** Mettre à jour le fichier `apps.py` de chaque application pour inclure le bon `name` :
> ```python
> # apps/patients/apps.py
> from django.apps import AppConfig
> class PatientsConfig(AppConfig):
>     default_auto_field = 'django.db.models.BigAutoField'
>     name = 'apps.patients'
> ```

### Description de chaque application

| Application | Rôle | Modèles principaux |
|-------------|------|--------------------|
| `accounts` | Authentification, gestion des rôles (Admin, Médecin, Infirmier, Secrétaire) | `Utilisateur`, `Profil` |
| `patients` | CRUD patients, dossiers médicaux, constantes vitales | `Patient`, `DossierMedical`, `Antecedent` |
| `visites` | Planification et enregistrement des visites à domicile | `Visite`, `RapportVisite`, `Constantes` |
| `prescriptions` | Gestion des ordonnances et traitements | `Prescription`, `Medicament`, `Posologie` |
| `alertes` | Notifications automatiques pour cas critiques | `Alerte`, `NotificationConfig` |
| `rapports` | Génération de rapports PDF/Excel | (Utilise les données des autres apps) |
| `facturation` | Suivi des paiements et facturation des soins | `Facture`, `Paiement` |

---

## 🗂️ Structure des Fichiers du Projet

### Configuration de `settings.py` (paramètres essentiels à ajouter)

```python
import os
from pathlib import Path
from dotenv import load_dotenv

load_dotenv()

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.getenv('SECRET_KEY', 'changez-moi-en-production')
DEBUG = True
ALLOWED_HOSTS = ['*']

# ─── Répertoires de templates ──────────────────────────────────────────────
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # ← Dossier templates global
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

# ─── Fichiers statiques ────────────────────────────────────────────────────
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']
STATIC_ROOT = BASE_DIR / 'staticfiles'

# ─── Fichiers média (documents, images patients) ───────────────────────────
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# ─── Modèle utilisateur personnalisé ──────────────────────────────────────
AUTH_USER_MODEL = 'accounts.Utilisateur'

# ─── Redirection après connexion/déconnexion ──────────────────────────────
LOGIN_URL = '/accounts/connexion/'
LOGIN_REDIRECT_URL = '/dashboard/'
LOGOUT_REDIRECT_URL = '/accounts/connexion/'

# ─── Crispy Forms ─────────────────────────────────────────────────────────
CRISPY_TEMPLATE_PACK = 'bootstrap4'
```

---

## 🎨 Templates & Layout

### Arborescence des templates

```
templates/
├── base.html                    ← Template mère (layout complet)
├── partials/
│   ├── _header.html             ← Barre de navigation supérieure
│   ├── _sidebar.html            ← Menu latéral (adapté par rôle)
│   ├── _footer.html             ← Pied de page
│   └── _alerts.html             ← Messages flash Django
├── accounts/
│   ├── connexion.html           ← Page de login
│   └── profil.html              ← Page profil utilisateur
├── dashboard/
│   ├── admin_dashboard.html     ← Dashboard Administrateur
│   └── medecin_dashboard.html   ← Dashboard Médecin/Infirmier
├── patients/
│   ├── liste_patients.html
│   ├── detail_patient.html
│   └── formulaire_patient.html
└── visites/
    ├── liste_visites.html
    └── detail_visite.html
```

---

### base.html

Créer le fichier `templates/base.html` :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}PRM — SPAD Cameroun{% endblock %}</title>

    <!-- Bootstrap 5 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css" rel="stylesheet">
    <!-- CSS personnalisé -->
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">

    {% block extra_css %}{% endblock %}
</head>
<body class="bg-light">

    {% if user.is_authenticated %}
        <!-- Layout avec sidebar (utilisateurs connectés) -->
        <div class="d-flex" id="wrapper">

            <!-- ═══ SIDEBAR ═══ -->
            {% include 'partials/_sidebar.html' %}

            <!-- ═══ CONTENU PRINCIPAL ═══ -->
            <div id="page-content-wrapper" class="flex-grow-1">

                <!-- ═══ HEADER ═══ -->
                {% include 'partials/_header.html' %}

                <!-- ═══ MESSAGES FLASH ═══ -->
                {% include 'partials/_alerts.html' %}

                <!-- ═══ CONTENU DE LA PAGE ═══ -->
                <main class="container-fluid p-4">
                    {% block content %}
                    {% endblock %}
                </main>

                <!-- ═══ FOOTER ═══ -->
                {% include 'partials/_footer.html' %}

            </div>
        </div>

    {% else %}
        <!-- Layout sans sidebar (pages publiques : login, etc.) -->
        {% include 'partials/_alerts.html' %}
        {% block content %}{% endblock %}
    {% endif %}

    <!-- Bootstrap 5 JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <!-- Chart.js (pour les graphiques des dashboards) -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- JS personnalisé -->
    <script src="{% static 'js/main.js' %}"></script>

    {% block extra_js %}{% endblock %}
</body>
</html>
```

---

### Sidebar & Header

#### `templates/partials/_sidebar.html`

```html
{% load static %}

<!-- Sidebar -->
<div class="bg-dark text-white" id="sidebar-wrapper" style="min-width: 260px; min-height: 100vh;">

    <!-- Logo & Titre -->
    <div class="sidebar-heading text-center py-4 border-bottom border-secondary">
        <img src="{% static 'img/logo_spad.png' %}" alt="SPAD" height="50" class="mb-2">
        <div class="fw-bold fs-6 text-white">SPAD Cameroun</div>
        <small class="text-muted">Suivi Médical Personnalisé</small>
    </div>

    <!-- Navigation -->
    <div class="list-group list-group-flush mt-2">

        <!-- Accueil / Dashboard -->
        <a href="{% url 'dashboard' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3
                  {% if request.resolver_match.url_name == 'dashboard' %}active{% endif %}">
            <i class="bi bi-speedometer2 me-2"></i> Tableau de Bord
        </a>

        <!-- Section Patients -->
        <div class="sidebar-section-title text-muted small px-3 pt-3 pb-1 text-uppercase">Gestion</div>

        <a href="{% url 'patients:liste' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3
                  {% if 'patients' in request.resolver_match.namespace %}active{% endif %}">
            <i class="bi bi-people-fill me-2"></i> Patients
        </a>

        <a href="{% url 'visites:liste' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-calendar-check me-2"></i> Visites
        </a>

        <a href="{% url 'prescriptions:liste' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-clipboard2-pulse me-2"></i> Prescriptions
        </a>

        <!-- Section Administration (visible Admin seulement) -->
        {% if user.role == 'admin' %}
        <div class="sidebar-section-title text-muted small px-3 pt-3 pb-1 text-uppercase">Administration</div>

        <a href="{% url 'accounts:liste_utilisateurs' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-person-gear me-2"></i> Utilisateurs
        </a>

        <a href="{% url 'rapports:index' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-bar-chart-fill me-2"></i> Rapports
        </a>

        <a href="{% url 'facturation:liste' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-receipt me-2"></i> Facturation
        </a>
        {% endif %}

        <!-- Alertes -->
        <a href="{% url 'alertes:liste' %}"
           class="list-group-item list-group-item-action bg-dark text-white border-0 py-3">
            <i class="bi bi-bell-fill me-2"></i> Alertes
            {% if nb_alertes_non_lues > 0 %}
            <span class="badge bg-danger float-end">{{ nb_alertes_non_lues }}</span>
            {% endif %}
        </a>

    </div>

    <!-- Profil utilisateur en bas -->
    <div class="position-absolute bottom-0 w-100 border-top border-secondary p-3">
        <div class="d-flex align-items-center">
            <i class="bi bi-person-circle fs-4 me-2 text-info"></i>
            <div>
                <div class="text-white small fw-bold">{{ user.get_full_name }}</div>
                <div class="text-muted" style="font-size:11px;">{{ user.get_role_display }}</div>
            </div>
            <a href="{% url 'accounts:deconnexion' %}" class="ms-auto text-danger">
                <i class="bi bi-box-arrow-right fs-5"></i>
            </a>
        </div>
    </div>

</div>
<!-- Fin Sidebar -->
```

#### `templates/partials/_header.html`

```html
<!-- Header / Navbar supérieure -->
<nav class="navbar navbar-expand-lg navbar-light bg-white border-bottom shadow-sm px-4">

    <!-- Bouton toggle sidebar (mobile) -->
    <button class="btn btn-outline-secondary me-3" id="sidebarToggle">
        <i class="bi bi-list fs-5"></i>
    </button>

    <!-- Fil d'Ariane -->
    <nav aria-label="breadcrumb">
        <ol class="breadcrumb mb-0">
            <li class="breadcrumb-item"><a href="{% url 'dashboard' %}">Accueil</a></li>
            {% block breadcrumb %}{% endblock %}
        </ol>
    </nav>

    <!-- Partie droite du header -->
    <div class="ms-auto d-flex align-items-center gap-3">

        <!-- Barre de recherche rapide -->
        <form class="d-none d-md-flex" action="{% url 'patients:recherche' %}" method="get">
            <div class="input-group">
                <input type="search" name="q" class="form-control form-control-sm"
                       placeholder="Rechercher un patient...">
                <button class="btn btn-sm btn-outline-secondary" type="submit">
                    <i class="bi bi-search"></i>
                </button>
            </div>
        </form>

        <!-- Icône Alertes -->
        <a href="{% url 'alertes:liste' %}" class="btn btn-sm btn-outline-warning position-relative">
            <i class="bi bi-bell fs-5"></i>
            {% if nb_alertes_non_lues > 0 %}
            <span class="position-absolute top-0 start-100 translate-middle badge rounded-pill bg-danger">
                {{ nb_alertes_non_lues }}
            </span>
            {% endif %}
        </a>

        <!-- Menu utilisateur -->
        <div class="dropdown">
            <button class="btn btn-sm btn-outline-primary dropdown-toggle" data-bs-toggle="dropdown">
                <i class="bi bi-person-circle me-1"></i>{{ user.prenom }}
            </button>
            <ul class="dropdown-menu dropdown-menu-end shadow">
                <li><h6 class="dropdown-header">{{ user.get_full_name }}</h6></li>
                <li><hr class="dropdown-divider"></li>
                <li><a class="dropdown-item" href="{% url 'accounts:profil' %}">
                    <i class="bi bi-person me-2"></i>Mon Profil</a>
                </li>
                <li><a class="dropdown-item text-danger" href="{% url 'accounts:deconnexion' %}">
                    <i class="bi bi-box-arrow-right me-2"></i>Se Déconnecter</a>
                </li>
            </ul>
        </div>

    </div>
</nav>
<!-- Fin Header -->
```

#### `templates/partials/_footer.html`

```html
<!-- Footer -->
<footer class="bg-white border-top mt-auto py-3 px-4">
    <div class="d-flex justify-content-between align-items-center">
        <small class="text-muted">
            &copy; {% now "Y" %} <strong>SPAD Cameroun</strong> — Tous droits réservés
        </small>
        <small class="text-muted">
            PRM Suivi Médical Personnalisé | Version 1.0
        </small>
    </div>
</footer>
<!-- Fin Footer -->
```

#### `templates/partials/_alerts.html`

```html
<!-- Messages Flash Django -->
{% if messages %}
<div class="container-fluid px-4 pt-3">
    {% for message in messages %}
    <div class="alert alert-{{ message.tags|default:'info' }} alert-dismissible fade show" role="alert">
        <i class="bi bi-{% if message.tags == 'success' %}check-circle{% elif message.tags == 'error' or message.tags == 'danger' %}x-circle{% else %}info-circle{% endif %} me-2"></i>
        {{ message }}
        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Fermer"></button>
    </div>
    {% endfor %}
</div>
{% endif %}
```

---

## 📊 Dashboards & Vues à Implémenter

### Dashboard Administrateur

#### Vue — `apps/accounts/views.py` (extrait)

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin
from apps.patients.models import Patient
from apps.visites.models import Visite
from apps.alertes.models import Alerte
from django.utils import timezone


@login_required
def dashboard(request):
    """
    Vue principale du tableau de bord.
    Affiche des statistiques différentes selon le rôle de l'utilisateur.
    """
    context = {}

    # ─── Statistiques communes ────────────────────────────────────────────
    context['total_patients'] = Patient.objects.filter(statut='actif').count()
    context['total_visites_aujourd_hui'] = Visite.objects.filter(
        date_prevue__date=timezone.now().date()
    ).count()
    context['nb_alertes_non_lues'] = Alerte.objects.filter(lue=False).count()

    # ─── Dashboard Administrateur ─────────────────────────────────────────
    if request.user.role == 'admin':
        from apps.accounts.models import Utilisateur
        context['total_utilisateurs'] = Utilisateur.objects.count()
        context['visites_semaine'] = Visite.objects.filter(
            date_prevue__week=timezone.now().isocalendar()[1]
        ).count()
        context['patients_recents'] = Patient.objects.order_by('-date_admission')[:5]
        context['alertes_critiques'] = Alerte.objects.filter(
            type='critique', lue=False
        ).order_by('-date')[:5]
        return render(request, 'dashboard/admin_dashboard.html', context)

    # ─── Dashboard Médecin ────────────────────────────────────────────────
    elif request.user.role == 'medecin':
        context['mes_patients'] = Patient.objects.filter(
            medecin_referent=request.user
        ).count()
        context['mes_visites_aujourd_hui'] = Visite.objects.filter(
            medecin=request.user,
            date_prevue__date=timezone.now().date()
        )
        return render(request, 'dashboard/medecin_dashboard.html', context)

    # ─── Dashboard Infirmier ──────────────────────────────────────────────
    elif request.user.role == 'infirmier':
        context['mes_visites_aujourd_hui'] = Visite.objects.filter(
            infirmier=request.user,
            date_prevue__date=timezone.now().date()
        )
        return render(request, 'dashboard/infirmier_dashboard.html', context)

    return render(request, 'dashboard/admin_dashboard.html', context)
```

#### Template — `templates/dashboard/admin_dashboard.html`

```html
{% extends 'base.html' %}
{% load static %}

{% block title %}Tableau de Bord — Admin{% endblock %}

{% block breadcrumb %}
<li class="breadcrumb-item active">Tableau de Bord</li>
{% endblock %}

{% block content %}

<!-- ═══ EN-TÊTE DU DASHBOARD ═══ -->
<div class="d-flex justify-content-between align-items-center mb-4">
    <div>
        <h4 class="fw-bold mb-0">Bonjour, {{ user.prenom }} 👋</h4>
        <small class="text-muted">{% now "l d F Y" %} — Tableau de bord Administrateur</small>
    </div>
    <a href="{% url 'patients:creer' %}" class="btn btn-primary">
        <i class="bi bi-plus-circle me-1"></i> Nouveau Patient
    </a>
</div>

<!-- ═══ CARTES STATISTIQUES ═══ -->
<div class="row g-4 mb-4">

    <div class="col-sm-6 col-xl-3">
        <div class="card border-0 shadow-sm h-100">
            <div class="card-body d-flex align-items-center">
                <div class="rounded-circle bg-primary bg-opacity-10 p-3 me-3">
                    <i class="bi bi-people-fill text-primary fs-4"></i>
                </div>
                <div>
                    <div class="fs-2 fw-bold text-primary">{{ total_patients }}</div>
                    <div class="text-muted small">Patients Actifs</div>
                </div>
            </div>
        </div>
    </div>

    <div class="col-sm-6 col-xl-3">
        <div class="card border-0 shadow-sm h-100">
            <div class="card-body d-flex align-items-center">
                <div class="rounded-circle bg-success bg-opacity-10 p-3 me-3">
                    <i class="bi bi-calendar-check text-success fs-4"></i>
                </div>
                <div>
                    <div class="fs-2 fw-bold text-success">{{ total_visites_aujourd_hui }}</div>
                    <div class="text-muted small">Visites Aujourd'hui</div>
                </div>
            </div>
        </div>
    </div>

    <div class="col-sm-6 col-xl-3">
        <div class="card border-0 shadow-sm h-100">
            <div class="card-body d-flex align-items-center">
                <div class="rounded-circle bg-warning bg-opacity-10 p-3 me-3">
                    <i class="bi bi-bell-fill text-warning fs-4"></i>
                </div>
                <div>
                    <div class="fs-2 fw-bold text-warning">{{ nb_alertes_non_lues }}</div>
                    <div class="text-muted small">Alertes Non Lues</div>
                </div>
            </div>
        </div>
    </div>

    <div class="col-sm-6 col-xl-3">
        <div class="card border-0 shadow-sm h-100">
            <div class="card-body d-flex align-items-center">
                <div class="rounded-circle bg-info bg-opacity-10 p-3 me-3">
                    <i class="bi bi-person-badge text-info fs-4"></i>
                </div>
                <div>
                    <div class="fs-2 fw-bold text-info">{{ total_utilisateurs }}</div>
                    <div class="text-muted small">Utilisateurs</div>
                </div>
            </div>
        </div>
    </div>

</div>

<!-- ═══ GRAPHIQUES & TABLEAUX ═══ -->
<div class="row g-4">

    <!-- Graphique des visites de la semaine -->
    <div class="col-lg-8">
        <div class="card border-0 shadow-sm">
            <div class="card-header bg-white border-0 pt-3">
                <h6 class="fw-bold mb-0"><i class="bi bi-bar-chart me-2 text-primary"></i>Visites de la Semaine</h6>
            </div>
            <div class="card-body">
                <canvas id="visitesChart" height="120"></canvas>
            </div>
        </div>
    </div>

    <!-- Alertes critiques -->
    <div class="col-lg-4">
        <div class="card border-0 shadow-sm">
            <div class="card-header bg-white border-0 pt-3">
                <h6 class="fw-bold mb-0"><i class="bi bi-exclamation-triangle me-2 text-danger"></i>Alertes Critiques</h6>
            </div>
            <div class="card-body p-0">
                {% if alertes_critiques %}
                <ul class="list-group list-group-flush">
                    {% for alerte in alertes_critiques %}
                    <li class="list-group-item border-0 py-3">
                        <div class="d-flex align-items-start">
                            <span class="badge bg-danger me-2 mt-1">!</span>
                            <div>
                                <div class="small fw-bold">{{ alerte.patient.nom }} {{ alerte.patient.prenom }}</div>
                                <div class="small text-muted">{{ alerte.message|truncatechars:50 }}</div>
                                <div class="text-muted" style="font-size:11px;">{{ alerte.date|timesince }} ago</div>
                            </div>
                        </div>
                    </li>
                    {% endfor %}
                </ul>
                {% else %}
                <div class="text-center py-4 text-muted">
                    <i class="bi bi-check-circle fs-3 text-success"></i>
                    <p class="small mt-2">Aucune alerte critique</p>
                </div>
                {% endif %}
            </div>
        </div>
    </div>

    <!-- Patients récents -->
    <div class="col-12">
        <div class="card border-0 shadow-sm">
            <div class="card-header bg-white border-0 pt-3 d-flex justify-content-between">
                <h6 class="fw-bold mb-0"><i class="bi bi-person-lines-fill me-2 text-primary"></i>Patients Récemment Admis</h6>
                <a href="{% url 'patients:liste' %}" class="btn btn-sm btn-outline-primary">Voir tout</a>
            </div>
            <div class="card-body p-0">
                <div class="table-responsive">
                    <table class="table table-hover mb-0">
                        <thead class="table-light">
                            <tr>
                                <th>N° Dossier</th>
                                <th>Nom Complet</th>
                                <th>Date d'Admission</th>
                                <th>Médecin Référent</th>
                                <th>Statut</th>
                                <th>Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for patient in patients_recents %}
                            <tr>
                                <td><code>{{ patient.numero_dossier }}</code></td>
                                <td><strong>{{ patient.nom }} {{ patient.prenom }}</strong></td>
                                <td>{{ patient.date_admission|date:"d/m/Y" }}</td>
                                <td>{{ patient.medecin_referent.get_full_name|default:"Non assigné" }}</td>
                                <td>
                                    <span class="badge bg-{% if patient.statut == 'actif' %}success{% else %}secondary{% endif %}">
                                        {{ patient.get_statut_display }}
                                    </span>
                                </td>
                                <td>
                                    <a href="{% url 'patients:detail' patient.pk %}" class="btn btn-sm btn-outline-primary">
                                        <i class="bi bi-eye"></i>
                                    </a>
                                </td>
                            </tr>
                            {% empty %}
                            <tr>
                                <td colspan="6" class="text-center text-muted py-4">Aucun patient enregistré</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

</div>
{% endblock %}

{% block extra_js %}
<script>
// Graphique des visites de la semaine (données factices pour prototype)
const ctx = document.getElementById('visitesChart').getContext('2d');
new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi', 'Samedi', 'Dimanche'],
        datasets: [{
            label: 'Visites Effectuées',
            data: [8, 12, 7, 15, 10, 5, 3],
            backgroundColor: 'rgba(13, 110, 253, 0.7)',
            borderColor: 'rgba(13, 110, 253, 1)',
            borderWidth: 1,
            borderRadius: 6,
        }]
    },
    options: {
        responsive: true,
        plugins: { legend: { display: false } },
        scales: { y: { beginAtZero: true, ticks: { stepSize: 5 } } }
    }
});
</script>
{% endblock %}
```

---

### Dashboard Médecin/Infirmier

#### Template — `templates/dashboard/medecin_dashboard.html`

```html
{% extends 'base.html' %}

{% block title %}Mon Espace — {{ user.get_full_name }}{% endblock %}

{% block breadcrumb %}
<li class="breadcrumb-item active">Mon Tableau de Bord</li>
{% endblock %}

{% block content %}

<div class="d-flex justify-content-between align-items-center mb-4">
    <div>
        <h4 class="fw-bold mb-0">Dr. {{ user.prenom }} {{ user.nom }}</h4>
        <small class="text-muted">Planning du {% now "d F Y" %}</small>
    </div>
</div>

<!-- Cartes rapides -->
<div class="row g-4 mb-4">
    <div class="col-md-4">
        <div class="card border-0 shadow-sm bg-primary text-white">
            <div class="card-body text-center py-4">
                <i class="bi bi-people-fill fs-1 mb-2"></i>
                <div class="fs-1 fw-bold">{{ mes_patients }}</div>
                <div class="opacity-75">Mes Patients</div>
            </div>
        </div>
    </div>
    <div class="col-md-4">
        <div class="card border-0 shadow-sm bg-success text-white">
            <div class="card-body text-center py-4">
                <i class="bi bi-calendar-check fs-1 mb-2"></i>
                <div class="fs-1 fw-bold">{{ mes_visites_aujourd_hui.count }}</div>
                <div class="opacity-75">Visites Aujourd'hui</div>
            </div>
        </div>
    </div>
    <div class="col-md-4">
        <div class="card border-0 shadow-sm bg-warning text-dark">
            <div class="card-body text-center py-4">
                <i class="bi bi-bell-fill fs-1 mb-2"></i>
                <div class="fs-1 fw-bold">{{ nb_alertes_non_lues }}</div>
                <div class="opacity-75">Alertes</div>
            </div>
        </div>
    </div>
</div>

<!-- Planning des visites du jour -->
<div class="card border-0 shadow-sm">
    <div class="card-header bg-white border-0 pt-3">
        <h6 class="fw-bold mb-0">
            <i class="bi bi-clock me-2 text-primary"></i>Mes Visites du Jour
        </h6>
    </div>
    <div class="card-body">
        {% if mes_visites_aujourd_hui %}
        <div class="list-group">
            {% for visite in mes_visites_aujourd_hui %}
            <a href="{% url 'visites:detail' visite.pk %}"
               class="list-group-item list-group-item-action border-0 mb-2 rounded shadow-sm">
                <div class="d-flex justify-content-between align-items-center">
                    <div>
                        <strong>{{ visite.patient.nom }} {{ visite.patient.prenom }}</strong>
                        <br>
                        <small class="text-muted">
                            <i class="bi bi-geo-alt me-1"></i>{{ visite.patient.adresse }}
                        </small>
                    </div>
                    <div class="text-end">
                        <div class="fw-bold text-primary">{{ visite.date_prevue|date:"H:i" }}</div>
                        <span class="badge bg-{% if visite.statut == 'effectuee' %}success{% elif visite.statut == 'planifiee' %}primary{% else %}secondary{% endif %}">
                            {{ visite.get_statut_display }}
                        </span>
                    </div>
                </div>
            </a>
            {% endfor %}
        </div>
        {% else %}
        <div class="text-center py-5 text-muted">
            <i class="bi bi-calendar-x fs-1"></i>
            <p class="mt-2">Aucune visite planifiée pour aujourd'hui</p>
        </div>
        {% endif %}
    </div>
</div>

{% endblock %}
```

---

## 🔗 Configuration des URLs

#### `prm_spadcameroun/urls.py`

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),

    # Pages d'authentification
    path('accounts/', include('apps.accounts.urls', namespace='accounts')),

    # Dashboard principal
    path('', include('apps.accounts.urls')),  # Redirige vers dashboard ou login

    # Applications métier
    path('patients/', include('apps.patients.urls', namespace='patients')),
    path('visites/', include('apps.visites.urls', namespace='visites')),
    path('prescriptions/', include('apps.prescriptions.urls', namespace='prescriptions')),
    path('alertes/', include('apps.alertes.urls', namespace='alertes')),
    path('rapports/', include('apps.rapports.urls', namespace='rapports')),
    path('facturation/', include('apps.facturation.urls', namespace='facturation')),

] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

#### Exemple — `apps/patients/urls.py`

```python
from django.urls import path
from . import views

app_name = 'patients'

urlpatterns = [
    path('', views.liste_patients, name='liste'),
    path('nouveau/', views.creer_patient, name='creer'),
    path('<int:pk>/', views.detail_patient, name='detail'),
    path('<int:pk>/modifier/', views.modifier_patient, name='modifier'),
    path('<int:pk>/supprimer/', views.supprimer_patient, name='supprimer'),
    path('recherche/', views.recherche_patient, name='recherche'),
]
```

#### Exemple — `apps/accounts/urls.py`

```python
from django.urls import path
from django.contrib.auth import views as auth_views
from . import views

app_name = 'accounts'

urlpatterns = [
    path('', views.dashboard, name='dashboard'),
    path('connexion/', views.ConnexionView.as_view(), name='connexion'),
    path('deconnexion/', auth_views.LogoutView.as_view(), name='deconnexion'),
    path('profil/', views.profil, name='profil'),
    path('utilisateurs/', views.liste_utilisateurs, name='liste_utilisateurs'),
]
```

---

## 🎨 CSS Personnalisé de base

Créer `static/css/style.css` :

```css
/* ═══ VARIABLES GLOBALES ═══ */
:root {
    --spad-primary: #0d6efd;
    --spad-dark: #1a1a2e;
    --spad-sidebar-width: 260px;
    --spad-green: #198754;
}

/* ═══ SIDEBAR ═══ */
#sidebar-wrapper {
    width: var(--spad-sidebar-width);
    transition: width 0.3s ease;
    position: sticky;
    top: 0;
    height: 100vh;
    overflow-y: auto;
}

#sidebar-wrapper .list-group-item.active {
    background-color: var(--spad-primary) !important;
    color: white !important;
    border-left: 4px solid #fff;
}

#sidebar-wrapper .list-group-item:hover:not(.active) {
    background-color: rgba(255, 255, 255, 0.08) !important;
}

/* ═══ PAGE CONTENT ═══ */
#page-content-wrapper {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}

main {
    flex: 1;
}

/* ═══ CARTES STATISTIQUES ═══ */
.card {
    transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.card:hover {
    transform: translateY(-2px);
    box-shadow: 0 .5rem 1rem rgba(0,0,0,.12) !important;
}

/* ═══ SIDEBAR SECTION TITLES ═══ */
.sidebar-section-title {
    letter-spacing: 0.08em;
    font-size: 0.7rem;
}

/* ═══ RESPONSIVE ═══ */
@media (max-width: 768px) {
    #sidebar-wrapper {
        width: 0;
        overflow: hidden;
    }

    #sidebar-wrapper.show {
        width: var(--spad-sidebar-width);
    }
}
```

#### `static/js/main.js`

```javascript
// ═══ TOGGLE SIDEBAR (MOBILE) ═══
document.getElementById('sidebarToggle')?.addEventListener('click', function() {
    document.getElementById('sidebar-wrapper').classList.toggle('show');
});

// ═══ AUTO-FERMETURE DES ALERTES FLASH APRÈS 5 SECONDES ═══
document.addEventListener('DOMContentLoaded', function() {
    setTimeout(function() {
        document.querySelectorAll('.alert').forEach(function(alert) {
            new bootstrap.Alert(alert).close();
        });
    }, 5000);
});
```

---

## 📝 Commandes Récapitulatives

```bash
# ─── MISE EN PLACE COMPLÈTE DU PROJET ──────────────────────────────────────

# 1. Activer l'environnement virtuel
source env/bin/activate          # Linux/macOS
# ou : env\Scripts\activate      # Windows

# 2. Créer toutes les apps
cd apps
python ../manage.py startapp accounts
python ../manage.py startapp patients
python ../manage.py startapp visites
python ../manage.py startapp prescriptions
python ../manage.py startapp alertes
python ../manage.py startapp rapports
python ../manage.py startapp facturation

# 3. Créer les dossiers pour les templates et les statics
mkdir -p ../templates/partials
mkdir -p ../templates/dashboard
mkdir -p ../templates/accounts
mkdir -p ../templates/patients
mkdir -p ../templates/visites
mkdir -p ../static/css
mkdir -p ../static/js
mkdir -p ../static/img
mkdir -p ../media

# 4. Effectuer les migrations
cd ..
python manage.py makemigrations
python manage.py migrate

# 5. Créer le superutilisateur
python manage.py createsuperuser

# 6. Collecter les fichiers statiques (production)
python manage.py collectstatic

# 7. Lancer le serveur
python manage.py runserver

# ─── ACCÈS ─────────────────────────────────────────────────────────────────
# Application : http://127.0.0.1:8000/
# Admin Django : http://127.0.0.1:8000/admin/
```

---

## 👥 Équipe & Contributeurs

| Rôle | Nom |
|------|-----|
| Chef de Projet | À définir |
| Développeur Backend | À définir |
| Développeur Frontend | À définir |
| Analyste / UML | À définir |
| DBA / MongoDB | À définir |

---

> **📌 Note Pédagogique :** Ce projet étudiant vise à illustrer la conception et le développement d'une application médicale réelle avec Django. L'accent est mis sur la structuration du code, la séparation des responsabilités entre les apps, et la qualité de l'interface utilisateur. Les fonctionnalités à implémenter en priorité sont : le dashboard administrateur, la gestion des patients (liste + détail), et le système de connexion/déconnexion.
