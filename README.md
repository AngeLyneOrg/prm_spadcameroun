# 🏥 PRM DE SUIVI MÉDICAL PERSONNALISÉ — SPAD CAMEROUN
### Projet Django | Système de Gestion de Soins à Domicile & Portail Public

---

## 📋 TABLE DES MATIÈRES

1. [Présentation du Projet](#-présentation-du-projet)
2. [Analyse UML](#-analyse-uml)
   - [Acteurs & Cas d'Utilisation](#acteurs--cas-dutilisation)
   - [Diagramme de Classes](#diagramme-de-classes)
3. [Palette de Couleurs & Design System](#-palette-de-couleurs--design-system)
4. [Architecture du Projet Django](#-architecture-du-projet-django)
5. [Installation & Configuration](#-installation--configuration)
   - [Environnement virtuel & dépendances](#étape-1--environnement-virtuel--dépendances)
   - [Configuration MongoDB locale via Djongo](#étape-2--configuration-mongodb-locale-via-djongo)
6. [Applications Django](#-applications-django-startapp)
7. [Structure des Fichiers](#-structure-des-fichiers-du-projet)
8. [Page Publique (Index / Accueil)](#-page-publique-index--accueil)
9. [Système d'Authentification & Tokens Famille](#-système-dauthentification--tokens-famille)
10. [Dashboards par Rôle](#-dashboards-par-rôle)
    - [Dashboard Administrateur Système](#dashboard-administrateur-système)
    - [Dashboard Coordonnateur Personnel](#dashboard-coordonnateur-personnel)
    - [Dashboard Médecin](#dashboard-médecin)
    - [Dashboard AVS](#dashboard-avs)
    - [Dashboard Famille / Patient](#dashboard-famille--patient)
11. [Système de Localisation des AVS](#-système-de-localisation-des-avs)
12. [Gestion des Rapports AVS](#-gestion-des-rapports-avs)
13. [Templates & Layout Responsive](#-templates--layout-responsive)
14. [Configuration des URLs](#-configuration-des-urls)
15. [Commandes Récapitulatives](#-commandes-récapitulatives)

---

## 🎯 Présentation du Projet

**SPAD Cameroun** (Service de Prestation et d'Assistance à Domicile) est une structure spécialisée dans les soins et l'assistance à domicile pour les personnes âgées au Cameroun. Le personnel (AVS — Auxiliaires de Vie Sociale) intervient directement dans les familles pour assurer le bien-être, l'hygiène, la santé, l'alimentation et les activités quotidiennes du patient suivi.

Ce projet est un **PRM (Portail de Relation et Management) de Suivi Médical Personnalisé** qui répond à deux objectifs :

- **Portail public** : présenter SPAD Cameroun, diffuser les actualités de l'association, et attirer de nouvelles familles/patients
- **Application métier** : gérer les patients, les AVS, les plannings, les rapports quotidiens, la localisation du personnel, les prescriptions médicales, et les comptes familles

> **Thème académique** : *PRM de Suivi Médical Personnalisé : Cas de SPAD CAMEROUN*

### Fonctionnement de SPAD Cameroun

```
Famille ──── contracte ────► SPAD Cameroun
                                   │
                        affecte un AVS à la famille
                                   │
                                   ▼
                    AVS intervient au domicile du patient
                    (hygiène, santé, activité, alimentation…)
                                   │
                        rédige des rapports journaliers
                                   │
                                   ▼
                    Médecin consulte & prescrit à distance
                    Coordonnateur supervise & évalue
                    Famille suit l'état de santé en temps réel
```

---

## 📊 Analyse UML

### Acteurs & Cas d'Utilisation

#### Acteurs du Système

| Acteur | Type | Description |
|--------|------|-------------|
| **Visiteur** | Externe | Internaute consultant le site public de SPAD |
| **Famille / Patient** | Compte Token | Membre de la famille accédant aux infos du proche suivi |
| **AVS** | Interne | Auxiliaire de Vie Sociale intervenant au domicile |
| **Médecin** | Interne | Médecin référent d'un ou plusieurs patients |
| **Coordonnateur Personnel** | Interne | Responsable des affectations, plannings et évaluations |
| **Administrateur Système** | Interne | Super-admin gérant les comptes, tokens et actualités |

#### Tableau des Cas d'Utilisation par Acteur

| Acteur | Cas d'Utilisation |
|--------|------------------|
| **Visiteur** | Consulter les actualités SPAD • Consulter les offres de service • S'inscrire / contacter SPAD |
| **Famille / Patient** | Suivre l'état de santé du patient • Consulter les rapports journaliers • Voir les tâches réalisées • Faire des suggestions • Signaler une alerte • Donner un feedback sur les AVS |
| **AVS** | Rédiger les rapports de journée (santé & bien-être) • Suivre l'état de son patient • Enregistrer les médicaments administrés • Avoir sa position géolocalisée • Faire des suggestions santé |
| **Médecin** | Consulter les rapports des patients • Prescrire des médicaments • Rédiger des rapports médicaux |
| **Coordonnateur** | Gérer les AVS (CRUD) • Gérer les patients • Affecter un AVS à une famille • Consulter la localisation en temps réel des AVS • Gérer les plannings AVS (multi-AVS par patient) • Gérer les paiements • Évaluer les AVS |
| **Administrateur** | Gérer les utilisateurs • Gérer les tokens famille • Gérer les statistiques • Gérer les actualités & publications |

#### Diagramme de Cas d'Utilisation (ASCII)

```
╔══════════════════════════════════════════════════════════════════════╗
║                   SYSTÈME PRM — SPAD CAMEROUN                        ║
║                                                                      ║
║  👤 Visiteur              👨‍👩‍👧 Famille / Patient                      ║
║  ├─ Voir actualités       ├─ Suivre état santé                       ║
║  ├─ Voir offres           ├─ Lire rapports                           ║
║  └─ S'inscrire            ├─ Signaler alerte                         ║
║                           ├─ Faire suggestions                       ║
║                           └─ Feedback AVS                            ║
║                                                                      ║
║  👩‍⚕️ AVS                   🩺 Médecin                                  ║
║  ├─ Rédiger rapport       ├─ Consulter rapports                      ║
║  ├─ Enregistrer méds      ├─ Prescrire médicaments                   ║
║  ├─ Géolocalisation       └─ Rédiger rapport médical                 ║
║  └─ Suggestions santé                                                ║
║                                                                      ║
║  📋 Coordonnateur         ⚙️ Administrateur                           ║
║  ├─ Gérer AVS             ├─ Gérer utilisateurs                      ║
║  ├─ Gérer patients        ├─ Gérer tokens famille                    ║
║  ├─ Affectations          ├─ Statistiques globales                   ║
║  ├─ Tracking AVS          └─ Gestion actualités                      ║
║  ├─ Plannings                                                        ║
║  ├─ Paiements                                                        ║
║  └─ Évaluation AVS                                                   ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

### Diagramme de Classes

```
┌─────────────────────┐         ┌──────────────────────────┐
│   Utilisateur       │         │   Patient                │
│ (AbstractBaseUser)  │         ├──────────────────────────┤
├─────────────────────┤         │ - id                     │
│ - id                │         │ - numero_dossier         │
│ - nom               │         │ - nom / prenom           │
│ - prenom            │         │ - date_naissance         │
│ - email             │         │ - sexe                   │
│ - telephone         │         │ - adresse                │
│ - role              │◄────────│ - medecin_referent (FK)  │
│   [admin|coordo|    │         │ - groupe_sanguin         │
│    medecin|avs]     │         │ - allergies              │
│ - photo             │         │ - antecedents            │
│ - date_creation     │         │ - statut                 │
│ - is_active         │         │ - date_admission         │
└─────────────────────┘         └────────────┬─────────────┘
         △                                   │
         │                                   │
    ┌────┴────┐                    ┌──────────┴──────────┐
    │         │                   │                     │
  AVS      Medecin          Affectation           TokenFamille
    │                       ├─ avs (FK)           ├─ patient (FK)
    │ - zone_intervention   ├─ patient (FK)       ├─ token (unique)
    │ - diplome             ├─ date_debut         ├─ membre_nom
    │ - experience          ├─ date_fin           ├─ relation
    │ - evaluation_score    └─ planning_notes     ├─ email
    │                                             ├─ is_active
    │                                             └─ date_expiration
    │
    │ ◄──────────────────────────────────────────────────────┐
    │                                                        │
┌───┴──────────────┐    ┌──────────────────┐    ┌───────────┴────────┐
│   RapportAVS     │    │  Prescription    │    │  LocalisationAVS   │
├──────────────────┤    ├──────────────────┤    ├────────────────────┤
│ - id             │    │ - id             │    │ - avs (FK)         │
│ - avs (FK)       │    │ - patient (FK)   │    │ - latitude         │
│ - patient (FK)   │    │ - medecin (FK)   │    │ - longitude        │
│ - date_rapport   │    │ - medicaments    │    │ - timestamp        │
│ - heure_limite   │    │ - posologie      │    │ - precision        │
│ - soumis_avant   │    │ - date_debut     │    └────────────────────┘
│   _heure_limite  │    │ - date_fin       │
│ - hygiene        │    │ - renouvellable  │    ┌────────────────────┐
│ - alimentation   │    │ - statut         │    │  Actualite         │
│ - sante          │    └──────────────────┘    ├────────────────────┤
│ - activite       │                            │ - titre            │
│ - medicaments    │    ┌──────────────────┐    │ - contenu          │
│ - observations   │    │  Alerte          │    │ - image            │
│ - statut         │    ├──────────────────┤    │ - date_publication │
│   [brouillon|    │    │ - patient (FK)   │    │ - categorie        │
│    soumis|validé]│    │ - type           │    │ - publie           │
│ - analyses       │    │ - message        │    └────────────────────┘
└──────────────────┘    │ - date           │
                        │ - lue            │    ┌────────────────────┐
                        │ - source         │    │   Paiement         │
                        │   [avs|famille|  │    ├────────────────────┤
                        │    systeme]      │    │ - patient (FK)     │
                        └──────────────────┘    │ - montant          │
                                                │ - statut           │
                                                │ - date_echeance    │
                                                │ - mode_paiement    │
                                                └────────────────────┘
```

---

## 🎨 Palette de Couleurs & Design System

Le design system est défini globalement dans `static/css/variables.css` et importé dans tous les templates. Chaque composant utilise ces variables CSS pour garantir la cohérence visuelle.

### Palette Officielle SPAD Cameroun

```css
/* ═══ PALETTE GLOBALE — SPAD CAMEROUN ════════════════════════════════════ */
:root {
    /* ── Couleurs Principales ── */
    --color-primary:        #1A6B4A;  /* Vert SPAD — confiance, santé */
    --color-primary-light:  #2D9E6F;  /* Variante claire */
    --color-primary-dark:   #0F4A32;  /* Variante foncée */
    --color-primary-10:     rgba(26, 107, 74, 0.10);
    --color-primary-20:     rgba(26, 107, 74, 0.20);

    /* ── Couleurs Secondaires ── */
    --color-secondary:      #F4A320;  /* Ambre — énergie, chaleur */
    --color-secondary-light:#F7C060;
    --color-secondary-dark: #C07800;
    --color-secondary-10:   rgba(244, 163, 32, 0.10);

    /* ── Couleur d'Accentuation ── */
    --color-accent:         #E84855;  /* Rouge — alertes, urgences */
    --color-accent-light:   #FF7B84;
    --color-accent-dark:    #B5232E;
    --color-accent-10:      rgba(232, 72, 85, 0.10);

    /* ── Couleurs Neutres ── */
    --color-dark:           #1E293B;  /* Texte principal */
    --color-gray-800:       #334155;
    --color-gray-600:       #64748B;
    --color-gray-400:       #94A3B8;
    --color-gray-200:       #E2E8F0;
    --color-gray-100:       #F1F5F9;
    --color-white:          #FFFFFF;

    /* ── Couleurs Sémantiques ── */
    --color-success:        #16A34A;
    --color-warning:        #D97706;
    --color-danger:         #DC2626;
    --color-info:           #0284C7;

    /* ── Couleurs par Rôle (sidebar, badges) ── */
    --color-role-admin:     #7C3AED;  /* Violet */
    --color-role-coordo:    #0284C7;  /* Bleu */
    --color-role-medecin:   #059669;  /* Vert émeraude */
    --color-role-avs:       #D97706;  /* Ambre */
    --color-role-famille:   #DB2777;  /* Rose */

    /* ── Typographie ── */
    --font-primary:         'Inter', 'Segoe UI', sans-serif;
    --font-size-xs:         0.75rem;
    --font-size-sm:         0.875rem;
    --font-size-base:       1rem;
    --font-size-lg:         1.125rem;
    --font-size-xl:         1.25rem;
    --font-size-2xl:        1.5rem;
    --font-size-3xl:        1.875rem;

    /* ── Espacements ── */
    --spacing-xs:           0.25rem;
    --spacing-sm:           0.5rem;
    --spacing-md:           1rem;
    --spacing-lg:           1.5rem;
    --spacing-xl:           2rem;
    --spacing-2xl:          3rem;

    /* ── Ombres ── */
    --shadow-sm:  0 1px 3px rgba(0,0,0,0.08);
    --shadow-md:  0 4px 12px rgba(0,0,0,0.10);
    --shadow-lg:  0 10px 30px rgba(0,0,0,0.12);

    /* ── Rayons ── */
    --radius-sm:  0.375rem;
    --radius-md:  0.625rem;
    --radius-lg:  1rem;
    --radius-xl:  1.5rem;
    --radius-full: 9999px;

    /* ── Sidebar ── */
    --sidebar-width:        270px;
    --sidebar-collapsed:    72px;
    --header-height:        64px;
}
```

---

## 🏗️ Architecture du Projet Django

```
prm_spadcameroun/                        ← Dossier racine du projet
│
├── prm_spadcameroun/                    ← Package de configuration principal
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
│
├── apps/                                ← Toutes les applications Django
│   ├── core/                            ← Page publique, accueil, actualités
│   ├── accounts/                        ← Auth, rôles, tokens famille
│   ├── patients/                        ← Gestion des patients & dossiers
│   ├── avs/                             ← Gestion du personnel AVS
│   ├── rapports/                        ← Rapports journaliers AVS
│   ├── prescriptions/                   ← Ordonnances & médicaments
│   ├── localisation/                    ← Géolocalisation des AVS
│   ├── alertes/                         ← Notifications & alertes
│   ├── plannings/                       ← Plannings & affectations
│   └── paiements/                       ← Gestion financière
│
├── templates/
│   ├── base_public.html                 ← Layout page publique (index, offres…)
│   ├── base_app.html                    ← Layout application (sidebar + header)
│   ├── partials/
│   │   ├── _navbar_public.html          ← Navigation du site public
│   │   ├── _sidebar.html               ← Sidebar application (responsive)
│   │   ├── _header_app.html            ← Header application
│   │   ├── _footer_public.html
│   │   ├── _footer_app.html
│   │   └── _flash_messages.html
│   ├── core/
│   │   ├── index.html                  ← Page d'accueil publique
│   │   ├── offres.html
│   │   └── contact.html
│   ├── accounts/
│   │   ├── login.html
│   │   ├── token_login.html            ← Connexion via token famille
│   │   └── profil.html
│   ├── dashboard/
│   │   ├── admin_dashboard.html
│   │   ├── coordo_dashboard.html
│   │   ├── medecin_dashboard.html
│   │   ├── avs_dashboard.html
│   │   └── famille_dashboard.html
│   ├── patients/
│   ├── avs/
│   ├── rapports/
│   ├── localisation/
│   └── plannings/
│
├── static/
│   ├── css/
│   │   ├── variables.css               ← Design system (palette, typo, espacements)
│   │   ├── base.css                    ← Styles de base globaux
│   │   ├── public.css                  ← Styles page publique
│   │   └── app.css                     ← Styles application (sidebar, header…)
│   ├── js/
│   │   ├── sidebar.js                  ← Collapse sidebar responsive
│   │   ├── localisation.js             ← Envoi GPS (AVS) et affichage carte (Coordo)
│   │   └── flash.js                    ← Auto-close des messages flash
│   └── img/
│       ├── logo_spad.png
│       ├── hero_spad.jpg
│       └── icons/
│
├── media/
├── requirements.txt
├── .env
└── manage.py
```

---

## ⚙️ Installation & Configuration

### Étape 1 : Environnement virtuel & dépendances

```bash
# Créer et activer l'environnement virtuel
python -m venv env
source env/bin/activate          # Linux/macOS
# env\Scripts\activate           # Windows

# Installer les dépendances
pip install django
pip install djongo                # ORM Django → MongoDB (comme Mongoose pour Node)
pip install pymongo               # Pilote MongoDB
pip install python-dotenv
pip install pillow
pip install django-crispy-forms
pip install channels              # WebSockets (localisation temps réel)
pip install django-leaflet        # Cartes Leaflet.js intégrées à Django

# Générer requirements.txt
pip freeze > requirements.txt
```

### Étape 2 : Configuration MongoDB locale via Djongo

**Djongo** joue le même rôle que Mongoose pour Node.js : il prend vos `models.py` Django classiques et crée automatiquement les collections MongoDB correspondantes lors des migrations. Aucune commande MongoDB manuelle n'est nécessaire.

#### 2.1 — Installer MongoDB localement

**Windows :**
1. Télécharger sur [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
2. Activer « Run MongoDB as a service » lors de l'installation
3. MongoDB écoute sur `localhost:27017`

**Ubuntu/Debian :**
```bash
sudo apt update && sudo apt install -y mongodb
sudo systemctl start mongodb && sudo systemctl enable mongodb
```

**macOS :**
```bash
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

#### 2.2 — Configuration `settings.py`

```python
# ─── settings.py ──────────────────────────────────────────────────────────
import os
from pathlib import Path
from dotenv import load_dotenv

load_dotenv()
BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.getenv('SECRET_KEY', 'changez-moi-en-production')
DEBUG = True
ALLOWED_HOSTS = ['*']

# ─── BASE DE DONNÉES : MongoDB via Djongo (collections créées automatiquement) ──
DATABASES = {
    'default': {
        'ENGINE': 'djongo',
        'NAME': 'prm_spad_db',       # Nom de la base (créée auto si inexistante)
        'ENFORCE_SCHEMA': False,      # Flexibilité NoSQL
        'CLIENT': {
            'host': 'localhost',
            'port': 27017,
            # Décommenter si authentification MongoDB activée :
            # 'username': os.getenv('MONGO_USER'),
            # 'password': os.getenv('MONGO_PASS'),
            # 'authSource': 'admin',
        }
    }
}

# ─── Modèle utilisateur personnalisé ──────────────────────────────────────
AUTH_USER_MODEL = 'accounts.Utilisateur'

# ─── Templates ────────────────────────────────────────────────────────────
TEMPLATES = [{
    'BACKEND': 'django.template.backends.django.DjangoTemplates',
    'DIRS': [BASE_DIR / 'templates'],
    'APP_DIRS': True,
    'OPTIONS': {
        'context_processors': [
            'django.template.context_processors.debug',
            'django.template.context_processors.request',
            'django.contrib.auth.context_processors.auth',
            'django.contrib.messages.context_processors.messages',
            'apps.alertes.context_processors.nb_alertes',  # Alertes globales
        ],
    },
}]

# ─── Statiques & Médias ───────────────────────────────────────────────────
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']
STATIC_ROOT = BASE_DIR / 'staticfiles'
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# ─── Auth redirections ────────────────────────────────────────────────────
LOGIN_URL = '/auth/connexion/'
LOGIN_REDIRECT_URL = '/dashboard/'
LOGOUT_REDIRECT_URL = '/'

# ─── Crispy Forms ─────────────────────────────────────────────────────────
CRISPY_TEMPLATE_PACK = 'bootstrap5'

# ─── Channels (WebSocket — localisation temps réel) ──────────────────────
ASGI_APPLICATION = 'prm_spadcameroun.asgi.application'
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels.layers.InMemoryChannelLayer',
    }
}
```

#### 2.3 — Comment Djongo crée les collections automatiquement

```bash
# Créer les fichiers de migration (équivalent de mongoose.model())
python manage.py makemigrations

# Appliquer les migrations → MongoDB crée les collections automatiquement
python manage.py migrate
# ✅ Les collections 'accounts_utilisateur', 'patients_patient', etc.
#    apparaissent dans la base 'prm_spad_db' sans aucune commande MongoDB manuelle.

# Vérifier la connexion
python manage.py shell
>>> from django.db import connection
>>> connection.ensure_connection()
>>> print("✅ Connexion MongoDB OK")
```

---

## 📦 Applications Django (startapp)

### Créer toutes les applications

```bash
mkdir apps && cd apps
python ../manage.py startapp core
python ../manage.py startapp accounts
python ../manage.py startapp patients
python ../manage.py startapp avs
python ../manage.py startapp rapports
python ../manage.py startapp prescriptions
python ../manage.py startapp localisation
python ../manage.py startapp alertes
python ../manage.py startapp plannings
python ../manage.py startapp paiements
```

### Enregistrer dans `settings.py`

```python
INSTALLED_APPS = [
    # Django natif
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Tiers
    'crispy_forms',
    'crispy_bootstrap5',
    'channels',
    'django_leaflet',
    # Applications SPAD
    'apps.core',
    'apps.accounts',
    'apps.patients',
    'apps.avs',
    'apps.rapports',
    'apps.prescriptions',
    'apps.localisation',
    'apps.alertes',
    'apps.plannings',
    'apps.paiements',
]
```

### Description des applications

| Application | Rôle | Collections MongoDB générées |
|-------------|------|------------------------------|
| `core` | Page publique, actualités, offres | `core_actualite`, `core_offre` |
| `accounts` | Auth, rôles, tokens famille | `accounts_utilisateur`, `accounts_tokenfamille` |
| `patients` | CRUD patients, dossiers, constantes | `patients_patient`, `patients_dossiermédical` |
| `avs` | Personnel AVS, évaluations | `avs_avs`, `avs_evaluation` |
| `rapports` | Rapports journaliers AVS | `rapports_rapportavs`, `rapports_analyse` |
| `prescriptions` | Ordonnances & médicaments | `prescriptions_prescription`, `prescriptions_medicament` |
| `localisation` | Positions GPS des AVS | `localisation_localisationavs` |
| `alertes` | Notifications & alertes critiques | `alertes_alerte` |
| `plannings` | Affectations & plannings AVS | `plannings_affectation`, `plannings_planning` |
| `paiements` | Facturation & paiements | `paiements_facture`, `paiements_paiement` |

---

## 🌐 Page Publique (Index / Accueil)

La première page que voit un visiteur est une page web complète présentant SPAD Cameroun, avant toute authentification. Elle est accessible à l'URL `/` et inclut :

### Structure de la Page d'Accueil (`templates/core/index.html`)

```
┌─────────────────────────────────────────────────────────┐
│  NAVBAR PUBLIQUE                                        │
│  [Logo SPAD]  Accueil  À propos  Services  Actualités  │
│                              [Connexion]  [Espace Famille]│
├─────────────────────────────────────────────────────────┤
│                                                         │
│  HERO SECTION                                           │
│  ┌─────────────────────┐  ┌─────────────────────────┐  │
│  │ "Prendre soin de    │  │                         │  │
│  │  vos proches, notre │  │   [Image famille/soin]  │  │
│  │  engagement"        │  │                         │  │
│  │ [Nos Services]      │  │                         │  │
│  │ [Espace Famille]    │  └─────────────────────────┘  │
│  └─────────────────────┘                               │
├─────────────────────────────────────────────────────────┤
│  CHIFFRES CLÉS  │  XX Familles  │  XX AVS  │  XX Ans   │
├─────────────────────────────────────────────────────────┤
│  NOS SERVICES (cartes)                                  │
│  [Hygiène] [Santé] [Alimentation] [Activités] [Suivi]  │
├─────────────────────────────────────────────────────────┤
│  ACTUALITÉS RÉCENTES (3 cartes)                         │
│  [Carte actu 1] [Carte actu 2] [Carte actu 3]          │
│                              [Voir toutes les actus →] │
├─────────────────────────────────────────────────────────┤
│  COMMENT ÇA MARCHE ? (3 étapes illustrées)              │
├─────────────────────────────────────────────────────────┤
│  TÉMOIGNAGES                                            │
├─────────────────────────────────────────────────────────┤
│  FOOTER  │  Contact  │  Réseaux  │  Mentions légales   │
└─────────────────────────────────────────────────────────┘
```

### Vue associée (`apps/core/views.py`)

```python
from django.shortcuts import render
from .models import Actualite, Offre

def index(request):
    """Page d'accueil publique — visible sans connexion."""
    context = {
        'actualites': Actualite.objects.filter(publie=True).order_by('-date_publication')[:3],
        'offres': Offre.objects.filter(active=True)[:4],
        'nb_familles': 120,   # À remplacer par Patient.objects.count()
        'nb_avs': 45,
        'nb_annees': 8,
    }
    return render(request, 'core/index.html', context)
```

---

## 🔐 Système d'Authentification & Tokens Famille

### Deux modes de connexion

#### 1. Connexion standard (staff interne)
Utilisée par : Admin, Coordonnateur, Médecin, AVS

```
URL : /auth/connexion/
Champs : Email + Mot de passe
Redirection : /dashboard/ (dashboard spécifique selon le rôle)
```

#### 2. Connexion par Token Famille
Un membre de la famille reçoit un token unique généré par l'administrateur. Ce token lui permet d'accéder au dossier de son proche sans créer un compte complet.

```
URL : /auth/token/
Champs : Token (code unique alphanumérique)
Redirection : /famille/dashboard/
```

### Modèle `TokenFamille` (`apps/accounts/models.py`)

```python
import secrets
from django.db import models
from django.utils import timezone

class TokenFamille(models.Model):
    """
    Compte d'accès simplifié pour les membres de la famille d'un patient.
    Généré et géré par l'Administrateur Système.
    """
    patient = models.ForeignKey('patients.Patient', on_delete=models.CASCADE,
                                related_name='tokens_famille')
    token = models.CharField(max_length=64, unique=True, editable=False)
    membre_nom = models.CharField(max_length=100, verbose_name="Nom du membre")
    relation = models.CharField(max_length=50,
                                choices=[('fils','Fils'),('fille','Fille'),
                                         ('conjoint','Conjoint(e)'),('autre','Autre')])
    email = models.EmailField(blank=True)
    telephone = models.CharField(max_length=20, blank=True)
    is_active = models.BooleanField(default=True)
    date_creation = models.DateTimeField(auto_now_add=True)
    date_expiration = models.DateTimeField(null=True, blank=True)
    derniere_connexion = models.DateTimeField(null=True, blank=True)

    def save(self, *args, **kwargs):
        if not self.token:
            self.token = secrets.token_urlsafe(32)
        super().save(*args, **kwargs)

    def is_valid(self):
        if not self.is_active:
            return False
        if self.date_expiration and timezone.now() > self.date_expiration:
            return False
        return True

    def __str__(self):
        return f"Token {self.membre_nom} → {self.patient}"
```

### Middleware d'authentification Token (`apps/accounts/middleware.py`)

```python
from django.utils import timezone
from .models import TokenFamille

class TokenFamilleMiddleware:
    """
    Attache le token famille à la session si présent,
    permettant d'accéder au dashboard famille.
    """
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        token_key = request.session.get('famille_token')
        if token_key:
            try:
                token = TokenFamille.objects.get(token=token_key, is_active=True)
                if token.is_valid():
                    request.famille_token = token
                    request.famille_patient = token.patient
                    token.derniere_connexion = timezone.now()
                    token.save(update_fields=['derniere_connexion'])
                else:
                    del request.session['famille_token']
            except TokenFamille.DoesNotExist:
                pass
        return self.get_response(request)
```

---

## 📊 Dashboards par Rôle

Chaque rôle dispose d'un dashboard personnalisé. La vue centrale redirige automatiquement vers le bon template.

### Vue de dispatch (`apps/accounts/views.py`)

```python
from django.shortcuts import render, redirect
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    role = request.user.role
    dispatch = {
        'admin':    'dashboard/admin_dashboard.html',
        'coordo':   'dashboard/coordo_dashboard.html',
        'medecin':  'dashboard/medecin_dashboard.html',
        'avs':      'dashboard/avs_dashboard.html',
    }
    template = dispatch.get(role)
    if not template:
        return redirect('accounts:login')
    return render(request, template, build_context(request))

def famille_dashboard(request):
    """Dashboard pour les comptes token famille."""
    if not hasattr(request, 'famille_token'):
        return redirect('accounts:token_login')
    patient = request.famille_patient
    context = {
        'patient': patient,
        'rapports': patient.rapports.order_by('-date_rapport')[:10],
        'prescriptions': patient.prescriptions.filter(statut='actif'),
        'alertes': patient.alertes.filter(lue=False),
    }
    return render(request, 'dashboard/famille_dashboard.html', context)
```

---

### Dashboard Administrateur Système

**Accès :** `role == 'admin'`
**Template :** `templates/dashboard/admin_dashboard.html`

#### Fonctionnalités & Widgets

| Section | Description |
|---------|-------------|
| **Statistiques globales** | Cartes : Nb utilisateurs, Nb patients, Nb AVS actifs, Alertes non lues |
| **Gestion des utilisateurs** | Tableau CRUD : créer / modifier / désactiver un compte (Admin, Coordo, Médecin, AVS) |
| **Gestion des tokens famille** | Générer un nouveau token, lister les tokens actifs/expirés, révoquer |
| **Gestion des actualités** | Créer / modifier / publier/dépublier des articles pour la page d'accueil |
| **Statistiques système** | Graphique des connexions, rapports soumis dans les délais, alertes critiques |
| **Logs d'activité** | Historique des dernières actions sur la plateforme |

#### Structure du Template (aperçu)

```html
{% extends 'base_app.html' %}
{% block content %}

<!-- Cartes statistiques -->
<div class="stats-grid">
  <stat-card icon="people" value="{{ total_users }}" label="Utilisateurs" color="primary"/>
  <stat-card icon="person-heart" value="{{ total_patients }}" label="Patients" color="success"/>
  <stat-card icon="briefcase" value="{{ total_avs }}" label="AVS Actifs" color="secondary"/>
  <stat-card icon="bell" value="{{ nb_alertes }}" label="Alertes" color="accent"/>
</div>

<!-- Gestion tokens famille -->
<section class="card mt-4">
  <h5>Tokens Famille Actifs</h5>
  <table><!-- liste des tokens avec actions révoquer/renouveler --></table>
  <button>+ Générer un Token</button>
</section>

<!-- Gestion actualités -->
<section class="card mt-4">
  <h5>Actualités Publiées</h5>
  <!-- Liste des articles avec toggle publié/dépublié -->
</section>

{% endblock %}
```

---

### Dashboard Coordonnateur Personnel

**Accès :** `role == 'coordo'`
**Template :** `templates/dashboard/coordo_dashboard.html`

#### Fonctionnalités & Widgets

| Section | Description |
|---------|-------------|
| **Vue d'ensemble** | Nb AVS en mission aujourd'hui, Nb patients suivis, Affectations en cours |
| **Carte de localisation** | Carte Leaflet affichant la position en temps réel de chaque AVS (point coloré + nom) |
| **Gestion des AVS** | CRUD AVS : ajouter, modifier, désactiver, voir fiche complète |
| **Gestion des patients** | Liste patients, dossier, médecin référent, AVS affecté(s) |
| **Affectations** | Associer un AVS à une famille/patient, définir les dates |
| **Plannings** | Gérer les plannings lorsqu'un patient est suivi par plusieurs AVS (rotation, horaires) |
| **Évaluation AVS** | Formulaire d'évaluation périodique (ponctualité, qualité, comportement) |
| **Paiements** | Suivi des factures et paiements des familles |

---

### Dashboard Médecin

**Accès :** `role == 'medecin'`
**Template :** `templates/dashboard/medecin_dashboard.html`

#### Fonctionnalités & Widgets

| Section | Description |
|---------|-------------|
| **Mes patients** | Liste des patients dont le médecin est référent |
| **Rapports récents** | Derniers rapports soumis par les AVS sur ses patients |
| **Prescriptions actives** | Ordonnances en cours pour chaque patient |
| **Rédiger un rapport médical** | Formulaire de rapport médical (observations, diagnostics) |
| **Prescrire des médicaments** | Créer une prescription liée à un patient |
| **Alertes médicales** | Alertes marquées « urgence » ou « santé critique » |

---

### Dashboard AVS

**Accès :** `role == 'avs'`
**Template :** `templates/dashboard/avs_dashboard.html`

#### Fonctionnalités & Widgets

| Section | Description |
|---------|-------------|
| **Mon patient du jour** | Carte du patient affecté : adresse, état, heure de visite |
| **Rédiger mon rapport** | Formulaire structuré (hygiène, alimentation, santé, activité, médicaments, observations) avec **heure limite** affichée et indicateur soumis/en retard |
| **Mes rapports précédents** | Historique paginé des rapports soumis |
| **Médicaments à administrer** | Liste des médicaments du jour avec cases à cocher |
| **Ma localisation** | Bouton « Activer le suivi GPS » — envoie la position toutes les X minutes |
| **Suggestions santé** | Formulaire pour signaler une observation médicale au médecin |

#### Modèle Rapport AVS (`apps/rapports/models.py`)

```python
from django.db import models
from django.utils import timezone

class RapportAVS(models.Model):
    STATUT_CHOICES = [
        ('brouillon', 'Brouillon'),
        ('soumis',    'Soumis'),
        ('valide',    'Validé par médecin'),
    ]

    avs     = models.ForeignKey('accounts.Utilisateur', on_delete=models.PROTECT,
                                limit_choices_to={'role': 'avs'}, related_name='rapports')
    patient = models.ForeignKey('patients.Patient', on_delete=models.PROTECT,
                                related_name='rapports')

    date_rapport  = models.DateField(default=timezone.now)
    heure_limite  = models.TimeField(default='18:00')  # Heure limite de soumission

    # Sections du rapport
    hygiene       = models.TextField(blank=True)
    alimentation  = models.TextField(blank=True)
    sante         = models.TextField(blank=True)
    activite      = models.TextField(blank=True)
    medicaments   = models.TextField(blank=True)
    observations  = models.TextField(blank=True)

    # Métadonnées
    statut             = models.CharField(max_length=20, choices=STATUT_CHOICES,
                                          default='brouillon')
    date_soumission    = models.DateTimeField(null=True, blank=True)
    soumis_avant_limite = models.BooleanField(null=True)  # Calculé à la soumission

    class Meta:
        unique_together = ('avs', 'patient', 'date_rapport')
        ordering = ['-date_rapport']

    def soumettre(self):
        now = timezone.now()
        self.date_soumission = now
        limite = timezone.make_aware(
            timezone.datetime.combine(self.date_rapport, self.heure_limite)
        )
        self.soumis_avant_limite = (now <= limite)
        self.statut = 'soumis'
        self.save()

    def __str__(self):
        return f"Rapport {self.avs} → {self.patient} [{self.date_rapport}]"
```

---

### Dashboard Famille / Patient

**Accès :** Connexion par token (`/auth/token/`)
**Template :** `templates/dashboard/famille_dashboard.html`

#### Fonctionnalités & Widgets

| Section | Description |
|---------|-------------|
| **Fiche du proche** | Nom, âge, état général, médecin référent, AVS affecté |
| **Derniers rapports** | Liste des rapports journaliers de l'AVS (lecture seule) |
| **Tâches réalisées** | Récapitulatif des tâches du jour : hygiène ✓, alimentation ✓… |
| **Prescriptions actives** | Médicaments prescrits et posologie |
| **Signaler une alerte** | Formulaire pour envoyer une alerte (maladie, accident, inquiétude) |
| **Faire une suggestion** | Formulaire de suggestion pour améliorer les soins |
| **Donner un feedback** | Évaluation des prestations de l'AVS (note + commentaire) |

---

## 📍 Système de Localisation des AVS

### Architecture

```
AVS (navigateur mobile)
    │
    │ Geolocation API (navigator.geolocation.watchPosition)
    │
    ▼
POST /api/localisation/update/  ─────► apps/localisation/views.py
                                              │
                                              ▼
                                    LocalisationAVS.objects.create()
                                    (collection MongoDB : localisation_localisationavs)
                                              │
                                              ▼
                              Coordonnateur → Carte Leaflet.js
                              (polling toutes les 30 sec ou WebSocket)
```

### Modèle (`apps/localisation/models.py`)

```python
class LocalisationAVS(models.Model):
    avs        = models.ForeignKey('accounts.Utilisateur', on_delete=models.CASCADE,
                                   limit_choices_to={'role': 'avs'})
    latitude   = models.FloatField()
    longitude  = models.FloatField()
    precision  = models.FloatField(null=True)   # mètres
    timestamp  = models.DateTimeField(auto_now_add=True)
    en_mission = models.BooleanField(default=True)

    class Meta:
        ordering = ['-timestamp']
        indexes = [models.Index(fields=['avs', '-timestamp'])]
```

### Envoi GPS depuis le navigateur de l'AVS (`static/js/localisation.js`)

```javascript
// Déclenchement par le bouton "Activer le suivi" sur le dashboard AVS
let watchId = null;

function activerSuivi() {
    if (!navigator.geolocation) {
        alert("Géolocalisation non supportée sur cet appareil.");
        return;
    }
    watchId = navigator.geolocation.watchPosition(
        position => envoyerPosition(position),
        err => console.error("Erreur GPS :", err),
        { enableHighAccuracy: true, maximumAge: 30000, timeout: 10000 }
    );
}

async function envoyerPosition(position) {
    await fetch('/api/localisation/update/', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'X-CSRFToken': getCookie('csrftoken'),
        },
        body: JSON.stringify({
            latitude:  position.coords.latitude,
            longitude: position.coords.longitude,
            precision: position.coords.accuracy,
        })
    });
}
```

---

## 📝 Gestion des Rapports AVS

### Flux complet du rapport journalier

```
[AVS rédige le rapport] ─► [Sauvegarde brouillon]
        │
        ▼
[AVS soumet avant l'heure limite] ──► soumis_avant_limite = True ✅
        │                                      │
        │                                      ▼
        │                         [Médecin consulte & valide]
        │
[AVS soumet APRÈS l'heure limite] ──► soumis_avant_limite = False ⚠️
                                       [Alerte au Coordonnateur]
```

### Vues rapports (`apps/rapports/views.py`)

```python
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth.decorators import login_required
from django.utils import timezone
from .models import RapportAVS
from .forms import RapportAVSForm

@login_required
def rediger_rapport(request):
    """Vue AVS — rédiger ou reprendre le rapport du jour."""
    today = timezone.now().date()
    rapport, created = RapportAVS.objects.get_or_create(
        avs=request.user,
        patient=request.user.avs_profile.patient_actuel,
        date_rapport=today,
        defaults={'statut': 'brouillon'}
    )
    if rapport.statut == 'soumis':
        return redirect('rapports:detail', pk=rapport.pk)

    if request.method == 'POST':
        form = RapportAVSForm(request.POST, instance=rapport)
        if form.is_valid():
            rapport = form.save(commit=False)
            if 'soumettre' in request.POST:
                rapport.soumettre()   # Calcule soumis_avant_limite
            else:
                rapport.save()        # Sauvegarde en brouillon
            return redirect('avs:dashboard')
    else:
        form = RapportAVSForm(instance=rapport)

    context = {
        'form': form,
        'rapport': rapport,
        'heure_limite': rapport.heure_limite,
        'heure_actuelle': timezone.now().time(),
    }
    return render(request, 'rapports/rediger.html', context)


@login_required
def analyser_rapport(request, pk):
    """Vue Médecin/Coordonnateur — analyser et valider un rapport."""
    rapport = get_object_or_404(RapportAVS, pk=pk)
    # Logique d'analyse et validation...
    return render(request, 'rapports/analyser.html', {'rapport': rapport})
```

---

## 🎨 Templates & Layout Responsive

### Deux bases templates distinctes

#### `base_public.html` — Site public (visiteurs non connectés)

- Navbar avec logo, liens de navigation, boutons Connexion / Espace Famille
- Pas de sidebar
- Footer complet avec infos de contact

#### `base_app.html` — Application (utilisateurs connectés)

- **Sidebar collapsible** : affichée en version complète sur grand écran, réduite à des icônes sur tablette, et masquée sur mobile (hamburger menu)
- Header avec breadcrumb, barre de recherche, cloche d'alertes, menu utilisateur
- Footer léger

### Sidebar Responsive (`static/js/sidebar.js`)

```javascript
// Comportement sidebar responsive
const sidebar = document.getElementById('sidebar');
const mainContent = document.getElementById('main-content');
const hamburger = document.getElementById('hamburger-btn');

// Collapse sidebar (tablette)
document.getElementById('sidebar-toggle')?.addEventListener('click', () => {
    sidebar.classList.toggle('collapsed');
    mainContent.classList.toggle('expanded');
    localStorage.setItem('sidebarCollapsed', sidebar.classList.contains('collapsed'));
});

// Hamburger (mobile)
hamburger?.addEventListener('click', () => {
    sidebar.classList.toggle('open');
    document.getElementById('sidebar-overlay').classList.toggle('active');
});

// Restaurer état depuis localStorage
if (localStorage.getItem('sidebarCollapsed') === 'true') {
    sidebar.classList.add('collapsed');
    mainContent.classList.add('expanded');
}

// Fermer sidebar mobile en cliquant sur l'overlay
document.getElementById('sidebar-overlay')?.addEventListener('click', () => {
    sidebar.classList.remove('open');
    document.getElementById('sidebar-overlay').classList.remove('active');
});
```

### CSS Sidebar Responsive (`static/css/app.css`)

```css
/* ═══ SIDEBAR ═══ */
#sidebar {
    width: var(--sidebar-width);
    min-height: 100vh;
    background: var(--color-dark);
    transition: width 0.3s ease, transform 0.3s ease;
    position: fixed;
    left: 0; top: 0;
    z-index: 1000;
    overflow: hidden;
}

#sidebar.collapsed {
    width: var(--sidebar-collapsed);
}

#sidebar.collapsed .nav-label,
#sidebar.collapsed .sidebar-title,
#sidebar.collapsed .user-info { display: none; }

#main-content {
    margin-left: var(--sidebar-width);
    transition: margin-left 0.3s ease;
    min-height: 100vh;
}

#main-content.expanded {
    margin-left: var(--sidebar-collapsed);
}

/* ═══ TABLETTE ═══ */
@media (max-width: 992px) {
    #sidebar { width: var(--sidebar-collapsed); }
    #sidebar .nav-label, #sidebar .sidebar-title { display: none; }
    #main-content { margin-left: var(--sidebar-collapsed); }
    #hamburger-btn { display: none; }
}

/* ═══ MOBILE ═══ */
@media (max-width: 768px) {
    #sidebar {
        width: var(--sidebar-width);
        transform: translateX(-100%);
    }
    #sidebar.open { transform: translateX(0); }
    #main-content { margin-left: 0; }
    #hamburger-btn { display: flex; }

    #sidebar-overlay {
        display: none;
        position: fixed;
        inset: 0;
        background: rgba(0,0,0,0.5);
        z-index: 999;
    }
    #sidebar-overlay.active { display: block; }
}
```

---

## 🔗 Configuration des URLs

### `prm_spadcameroun/urls.py`

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),

    # ── Page publique (accueil, actualités, offres) ──
    path('', include('apps.core.urls', namespace='core')),

    # ── Authentification ──
    path('auth/', include('apps.accounts.urls', namespace='accounts')),

    # ── Dashboard (dispatch par rôle) ──
    path('dashboard/', include('apps.accounts.dashboard_urls')),

    # ── Dashboard famille (token) ──
    path('famille/', include('apps.accounts.famille_urls', namespace='famille')),

    # ── Applications métier ──
    path('patients/', include('apps.patients.urls', namespace='patients')),
    path('avs/', include('apps.avs.urls', namespace='avs')),
    path('rapports/', include('apps.rapports.urls', namespace='rapports')),
    path('prescriptions/', include('apps.prescriptions.urls', namespace='prescriptions')),
    path('plannings/', include('apps.plannings.urls', namespace='plannings')),
    path('paiements/', include('apps.paiements.urls', namespace='paiements')),
    path('alertes/', include('apps.alertes.urls', namespace='alertes')),

    # ── API localisation (AJAX) ──
    path('api/localisation/', include('apps.localisation.urls', namespace='localisation')),

] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### URLs par application (exemple `apps/rapports/urls.py`)

```python
from django.urls import path
from . import views

app_name = 'rapports'

urlpatterns = [
    path('',                    views.liste_rapports,  name='liste'),
    path('rediger/',            views.rediger_rapport, name='rediger'),
    path('<str:pk>/',           views.detail_rapport,  name='detail'),
    path('<str:pk>/analyser/',  views.analyser_rapport,name='analyser'),
    path('<str:pk>/valider/',   views.valider_rapport, name='valider'),
]
```

---

## 📝 Commandes Récapitulatives

```bash
# ─── SETUP COMPLET ──────────────────────────────────────────────────────────

# 1. Activer l'env virtuel
source env/bin/activate

# 2. Créer le projet
django-admin startproject prm_spadcameroun .

# 3. Créer toutes les apps
mkdir apps
cd apps
for app in core accounts patients avs rapports prescriptions localisation alertes plannings paiements; do
    python ../manage.py startapp $app
done
cd ..

# 4. Créer l'arborescence templates et static
mkdir -p templates/{partials,core,accounts,dashboard,patients,avs,rapports,prescriptions,plannings,paiements,localisation}
mkdir -p static/{css,js,img/icons}
mkdir -p media

# 5. Migrations → crée les collections MongoDB automatiquement
python manage.py makemigrations
python manage.py migrate

# 6. Superutilisateur
python manage.py createsuperuser

# 7. Lancer
python manage.py runserver

# ─── ACCÈS ──────────────────────────────────────────────────────────────────
# Accueil public        : http://127.0.0.1:8000/
# Connexion staff       : http://127.0.0.1:8000/auth/connexion/
# Connexion famille     : http://127.0.0.1:8000/auth/token/
# Dashboard             : http://127.0.0.1:8000/dashboard/
# Admin Django          : http://127.0.0.1:8000/admin/
```

---

## 👥 Équipe & Rôles

| Rôle | Nom |
|------|-----|
| Chef de Projet | À définir |
| Développeur Backend | À définir |
| Développeur Frontend | À définir |
| Analyste UML | À définir |
| DBA / MongoDB | À définir |

---

> **📌 Note Pédagogique** — Ce projet illustre la conception d'une application web médicale complète avec Django et MongoDB. Les priorités d'implémentation recommandées sont :
>
> 1. **Phase 1** — Page publique (index) + système d'authentification (staff & tokens famille)
> 2. **Phase 2** — Dashboard Administrateur + gestion complète des utilisateurs et tokens
> 3. **Phase 3** — Dashboard Coordonnateur + gestion AVS, patients, affectations et localisation
> 4. **Phase 4** — Dashboard AVS + rapports journaliers + système d'alertes
> 5. **Phase 5** — Dashboard Médecin + prescriptions + Dashboard Famille
