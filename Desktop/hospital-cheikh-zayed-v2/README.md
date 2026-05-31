#  Hôpital Cheikh Zayed  Plateforme de Gestion Hospitalière

 Système d'information hospitalier complet basé sur OpenHospital, adapté et amélioré pour l'Hôpital Cheikh Zayed.

---

##  Table des matières

1. [Description du projet](#1-description-du-projet)
2. [Membres du projet](#2-membres-du-projet)
3. [Architecture](#3-architecture)
4. [Code source](#4-code-source)
5. [Gestion des erreurs et UX](#5-gestion-des-erreurs-et-ux)
6. [Validation des données](#6-validation-des-données)
7. [Sécurité](#7-sécurité)
8. [Compatibilité multiplateforme](#8-compatibilité-multiplateforme)
9. [Documentation](#9-documentation)
10. [Accessibilité](#10-accessibilité)
11. [Gestion de la configuration](#11-gestion-de-la-configuration)
12. [Conteneurisation (Bonus)](#12-conteneurisation-bonus)

---

## 1. Description du projet

### Objectifs

La plateforme **Hôpital Cheikh Zayed** est un système d'information hospitalier (SIH) web complet permettant la gestion numérique et centralisée de l'ensemble des activités médicales et administratives de l'hôpital.

### Contexte

Ce projet est développé dans le cadre du cours de **Développement Web**  Semestre 2, Période 2. Il s'appuie sur la base open-source **OpenHospital** (Informatici Senza Frontiere) et l'adapte aux besoins spécifiques de l'Hôpital Cheikh Zayed, avec des améliorations significatives en termes de sécurité, d'expérience utilisateur, de validation et de qualité du code.

### Fonctionnalités principales

 Module  Description 
---------------------
  **Authentification**  Connexion JWT, réinitialisation de mot de passe, gestion de session 
  **Patients**  Enregistrement, recherche, dossier médical complet 
  **Hospitalisations**  Admissions, transferts de service, sorties 
  **Rendez-vous**  Planification, rappels, gestion des créneaux 
  **Laboratoire**  Prescriptions d'examens, résultats, historique 
  **Pharmacie**  Stock médicaments, mouvements, inventaire 
  **Facturation**  Création de factures, paiements, reçus 
  **Personnel**  Gestion des médecins, infirmiers, administrateurs 
  **Statistiques**  Tableaux de bord, rapports, indicateurs clés 
  **Paramètres**  Configuration système, gestion des utilisateurs et permissions 

### Technologies utilisées

**Backend :**
- Java 17  Spring Boot 3.5.6
- Spring Security (JWT)
- Spring Data JPA  Hibernate
- MariaDB / MySQL
- Maven 3.x
- JUnit 5  Mockito (tests)

**Frontend :**
- React 18  TypeScript
- Vite 6 (bundler)
- React Router v6
- TanStack Query v5 (gestion d'état serveur)
- Recharts (graphiques)

**Infrastructure :**
- Docker  Docker Compose
- Nginx (reverse proxy)
- GitHub Actions (CI/CD)

### Cas d'utilisation

- Un **médecin** consulte le dossier d'un patient, prescrit des examens et des médicaments.
- Une **infirmière** enregistre les constantes vitales et gère les hospitalisations.
- Un **pharmacien** gère le stock et valide les dispensations.
- Un **administrateur** crée des comptes, configure les permissions et consulte les statistiques.
- Un **caissier** génère les factures et enregistre les paiements.

---

## 2. Membres du projet

 Nom  Rôle  Responsabilités  Contributions principales 
------------------------------------------------------
 **Walid Regragui**  Chef de projet / Lead Backend  Architecture globale, sécurité, API REST, CI/CD  Configuration Spring Security, JWT, gestion des exceptions, pipeline GitHub Actions 
 **Nabil Zrhdidi**  Développeur Backend  Modules métier backend, tests unitaires  Services patients, hospitalisations, laboratoire, tests JUnit/Mockito 
 **Amrou Boussouf**  Développeur Frontend  Interface utilisateur, composants React, accessibilité  Pages patients, hospitalisations, laboratoire, composants UI accessibles 
 **Yassine Azhari**  Développeur Frontend / DevOps  Intégration API, Docker, déploiement  Pages pharmacie, facturation, Docker Compose, documentation 

---

## 3. Architecture

### Architecture globale

```

                        CLIENT (Browser)                      
              React 18  TypeScript  Vite                    
        
     Pages    Components   Context   TanStack       
     (Views)     (UI)      (Auth)    Query Cache    
        

                           HTTPS / REST API (JSON)
                           Authorization: Bearer JWT

                    NGINX (Reverse Proxy)                      
              TLS termination  Static files                   

                          

                  BACKEND (Spring Boot 3.5.6)                  
        
   Controllers      Services         Repositories      
    (REST API)   (Métier/ACL)    (Spring Data JPA)   
        
   
           Spring Security  JWT Filter Chain               
   

                           JPA / JDBC

                    MariaDB / MySQL                            
              (Données hospitalières  Audit)                  

```

### Séparation des préoccupations

**Frontend (React  TypeScript) :**
- Responsable uniquement de l'affichage et de l'interaction utilisateur.
- Communique avec le backend exclusivement via des appels REST/JSON.
- Aucune logique métier : toutes les règles de gestion sont côté serveur.
- **Justification :** React permet une architecture par composants réutilisables, TypeScript apporte la sûreté de type à la compilation, Vite offre un build ultra-rapide.

**Backend (Spring Boot  Java 17) :**
- Expose une API REST sécurisée par JWT.
- Contient toute la logique métier (managers), la validation, la sécurité.
- Architecture en couches : Controller  Service  Manager  Repository  DB.
- **Justification :** Spring Boot est le standard industriel Java pour les APIs REST. Java 17 apporte les records, les sealed classes et de meilleures performances. JPA/Hibernate abstrait la couche base de données.

### Flux d'authentification

```
Client  POST /api/auth/login  JwtTokenService  JWT Token
Client  GET /api/patients (Bearer token)  WebAuthenticationInterceptor  Validation  Réponse
```

---

## 4. Code source

### Organisation du projet

```
hospital-cheikh-zayed-v2/
 src/                              # Backend Java (Spring Boot)
    main/
       java/org/isf/
          web/
             api/              # Controllers REST
             security/         # JWT, intercepteurs
             service/          # Services web (couche présentation)
             config/           # Configuration Spring
             dto/              # Data Transfer Objects
          patient/              # Module patients
          admission/            # Module hospitalisations
          lab/                  # Module laboratoire
          accounting/           # Module facturation
          menu/                 # Utilisateurs & groupes
          utils/                # Utilitaires transverses
       resources/
           application.properties
           application-test.properties
    test/
        java/org/isf/web/         # Tests unitaires & intégration
 webapp/                           # Frontend React  TypeScript
    src/
       components/               # Composants réutilisables
          ui/                   # Composants UI de base
       pages/                    # Pages de l'application
       lib/                      # Utilitaires, types, API client
       context/                  # Contextes React (Auth)
       hooks/                    # Hooks personnalisés
    package.json
    vite.config.ts
    tsconfig.json
 .github/
    workflows/
        ci.yml                    # Pipeline CI/CD
 Conception UML/                   # Diagrammes UML
 docker-compose.yml
 docker-compose.prod.yml
 Dockerfile.api
 .env.example
 .gitignore
 README.md
```

### Conventions de nommage

 Élément  Convention  Exemple 
-----------------------------
 Classes Java  PascalCase  `PatientBrowserManager` 
 Méthodes Java  camelCase  `getPatientById()` 
 Constantes Java  UPPER_SNAKE_CASE  `MAX_LOGIN_ATTEMPTS` 
 Composants React  PascalCase  `PatientCard.tsx` 
 Hooks React  camelCase préfixé `use`  `usePatients.ts` 
 Fichiers CSS  kebab-case  `patient-card.css` 
 Variables d'env  UPPER_SNAKE_CASE  `OH_API_PORT` 

### Qualité du code

- **Linting :** ESLint (frontend), Checkstyle (backend)
- **Formatage :** Prettier (frontend), Google Java Format (backend)
- **Couverture de tests :** objectif  70% sur les services critiques
- **Code reviews :** obligatoires via Pull Requests avant merge sur `main`

### Gestion des branches Git

```
main                     Production stable
 develop              Intégration continue
    feature/walid-security       Walid : sécurité & CI
    feature/nabil-backend        Nabil : modules backend
    feature/amrou-frontend-ui    Amrou : composants UI
    feature/yassine-devops       Yassine : Docker & facturation
```

**Règles :**
- Jamais de push direct sur `main` ou `develop`.
- Chaque feature branch est mergée via Pull Request avec au moins 1 reviewer.
- Les commits suivent la convention **Conventional Commits** : `feat:`, `fix:`, `docs:`, `test:`, `chore:`.

### Stratégie de synchronisation Git

```bash
# Avant de commencer à travailler
git checkout develop
git pull origin develop
git checkout feature/ma-branche
git rebase develop          # Rebase pour un historique propre

# Après avoir terminé
git push origin feature/ma-branche
# Créer une Pull Request vers develop
```

### Intégration continue (GitHub Actions)

Le pipeline CI (`.github/workflows/ci.yml`) s'exécute à chaque push et PR :
1. **Build backend** : `mvn clean verify`
2. **Tests backend** : JUnit 5  rapport de couverture
3. **Build frontend** : `npm ci && npm run build`
4. **Lint frontend** : ESLint
5. **Build Docker** : vérification des images

### Tests unitaires et d'intégration

**Backend :**
- `@SpringBootTest` pour les tests d'intégration
- `@WebMvcTest` pour les tests de controllers
- `@DataJpaTest` pour les tests de repositories
- Mockito pour les mocks de services

**Frontend :**
- Vitest  React Testing Library pour les composants
- MSW (Mock Service Worker) pour mocker les appels API

---

## 5. Gestion des erreurs et UX

### Côté frontend

- **ErrorBoundary React** (`AppErrorBoundary.tsx`) : capture les erreurs JavaScript non gérées et affiche une page d'erreur conviviale.
- **Gestion des états de chargement** : chaque appel API affiche un spinner ou un skeleton loader via TanStack Query (`isLoading`, `isFetching`).
- **Messages d'erreur explicites** : les erreurs API sont traduites en messages lisibles (ex: "Identifiants incorrects", "Session expirée").
- **Toast notifications** : feedback immédiat pour les actions (succès, erreur, avertissement).
- **Retry automatique** : TanStack Query retente automatiquement les requêtes échouées (réseau).
- **Gestion de la session expirée** : redirection automatique vers `/login` avec message explicatif.

### Côté backend

- **`ApiExceptionHandler`** (`@RestControllerAdvice`) : centralise la gestion de toutes les exceptions.
- **Codes HTTP sémantiques** : 400 (validation), 401 (non authentifié), 403 (non autorisé), 404 (non trouvé), 409 (conflit), 500 (erreur serveur).
- **Format d'erreur uniforme** :
  ```json
  {
    "timestamp": "2026-05-31T18:00:00Z",
    "status": 400,
    "error": "Validation Failed",
    "message": "Le champ 'firstName' est obligatoire",
    "path": "/api/patients"
  }
  ```
- **Logging structuré** : SLF4J  Logback, avec niveaux ERROR/WARN/INFO/DEBUG.

---

## 6. Validation des données

### Frontend (React  TypeScript)

- Validation des formulaires avec des règles déclaratives (longueur, format, obligatoire).
- Affichage des erreurs en temps réel sous chaque champ.
- Désactivation du bouton de soumission si le formulaire est invalide.
- Validation du format email, numéro de téléphone, date de naissance.
- Politique de mot de passe : minimum 8 caractères, majuscule, chiffre, caractère spécial.

### Backend (Spring Boot)

- **Bean Validation (JSR-380)** : annotations `@NotNull`, `@NotBlank`, `@Size`, `@Email`, `@Pattern` sur les DTOs.
- **Validation métier** dans les managers : vérification des contraintes métier (ex: un patient ne peut pas avoir deux admissions actives simultanées).
- **Parameterized queries** via JPA/Hibernate : prévention des injections SQL.
- **Sanitisation des entrées** : suppression des caractères dangereux avant traitement.

---

## 7. Sécurité

### Authentification & Autorisation

- **JWT (JSON Web Token)** : tokens signés avec clé secrète configurable, expiration paramétrable.
- **BCrypt** : hachage des mots de passe avec salt aléatoire (coût configurable).
- **RBAC (Role-Based Access Control)** : permissions granulaires par groupe d'utilisateurs.
- **Verrouillage de compte** : après N tentatives échouées (configurable).
- **Session idle timeout** : déconnexion automatique après inactivité.

### Protection contre les attaques

 Attaque  Protection 
--------------------
 **SQL Injection**  JPA Parameterized Queries, aucune concaténation SQL 
 **XSS**  Échappement automatique React, Content-Security-Policy header 
 **CSRF**  API stateless JWT (pas de cookies de session) 
 **Brute Force**  Rate limiting, verrouillage de compte 
 **IDOR**  Vérification des permissions sur chaque ressource 
 **Secrets exposés**  Variables d'environnement, jamais dans le code 

### Headers de sécurité (Nginx)

```nginx
add_header X-Frame-Options "SAMEORIGIN";
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; modeblock";
add_header Referrer-Policy "strict-origin-when-cross-origin";
add_header Content-Security-Policy "default-src 'self'";
```

### Gestion des variables sensibles

- Toutes les valeurs sensibles (mots de passe DB, clé JWT, SMTP) sont dans des variables d'environnement.
- Le fichier `.env` est dans `.gitignore`  seul `.env.example` est versionné.
- En production, utiliser des secrets managers (Docker Secrets, Vault, etc.).

---

## 8. Compatibilité multiplateforme

### Navigateurs supportés

 Navigateur  Version minimale  Statut 
------------------------------------
 Chrome  90   Supporté 
 Firefox  88   Supporté 
 Safari  14   Supporté 
 Edge  90   Supporté 
 Opera  76   Supporté 

### Responsive Design

- **Mobile-first** : conception d'abord pour mobile, puis adaptation desktop.
- **CSS Grid  Flexbox** : mise en page fluide et adaptative.
- **Breakpoints** : mobile ( 768px), tablette (7681024px), desktop ( 1024px).
- **Touch-friendly** : boutons et zones cliquables  4444px (recommandation WCAG).

### Tests de compatibilité

- Tests manuels sur Chrome, Firefox, Safari, Edge.
- Vite génère automatiquement des bundles compatibles avec les cibles définies dans `vite.config.ts`.
- Utilisation de CSS standard sans préfixes propriétaires.

---

## 9. Documentation

### Documentation du code

**Backend :**
- Javadoc sur toutes les classes publiques et méthodes de service.
- Commentaires explicatifs sur la logique métier complexe.
- `@Operation` (OpenAPI/Swagger) sur les endpoints REST.

**Frontend :**
- JSDoc/TSDoc sur les composants et hooks.
- Props documentées avec TypeScript interfaces.

### Diagrammes UML (dossier `Conception UML/`)

- `class-diagram.puml`  Diagramme de classes principal
- `sequence-login.puml`  Séquence d'authentification
- `sequence-patient-management.puml`  Gestion des patients
- `sequence-billing.puml`  Processus de facturation
- `use-cases.puml`  Cas d'utilisation
- `deployment-diagram.puml`  Architecture de déploiement

### Guide de configuration

Voir section [Gestion de la configuration](#11-gestion-de-la-configuration).

### Instructions de déploiement

**Développement local :**
```bash
# 1. Cloner le dépôt
git clone https://github.com/ENSIAS-MEH/projet-web-team_web.git
cd hospital-cheikh-zayed

# 2. Configurer l'environnement
cp .env.example .env
# Éditer .env avec vos valeurs

# 3. Démarrer avec Docker Compose
docker compose up -d

# 4. Accéder à l'application
# Frontend : http://localhost:8081
# API :      http://localhost:8080
```

**Production :**
```bash
docker compose -f docker-compose.prod.yml up -d
```

### Procédures de maintenance

- **Mise à jour des dépendances** : `mvn versions:display-dependency-updates` (backend), `npm outdated` (frontend).
- **Sauvegarde DB** : script `scripts/backup-db.sh` (à planifier via cron).
- **Rotation des logs** : configurée dans `logback-spring.xml` (rotation quotidienne, rétention 30 jours).
- **Mise à jour JWT secret** : modifier `OH_WEB_JWT_SECRET` dans `.env` et redémarrer l'API (invalide toutes les sessions actives).

---

## 10. Accessibilité

La plateforme respecte les recommandations **WCAG 2.1 niveau AA** :

### Contrastes

- Ratio de contraste texte/fond  4.5:1 (texte normal),  3:1 (grand texte).
- Vérification avec les outils Chrome DevTools Accessibility.

### Navigation clavier

- Tous les éléments interactifs sont accessibles au clavier (Tab, ShiftTab, Enter, Espace).
- Ordre de focus logique et visible (outline CSS non supprimé).
- Raccourcis clavier documentés pour les actions fréquentes.

### Lecteurs d'écran

- Attributs `aria-label`, `aria-describedby`, `aria-live` sur les éléments dynamiques.
- Rôles ARIA sémantiques (`role"alert"`, `role"dialog"`, `role"navigation"`).
- Annonces des changements d'état (chargement, erreur, succès) via `aria-live"polite"`.

### Formulaires accessibles

- Chaque champ a un `label` associé via `htmlFor`/`id`.
- Messages d'erreur liés au champ via `aria-describedby`.
- Champs obligatoires marqués avec `aria-required"true"` et indicateur visuel.

### Images et icônes

- Toutes les images ont un attribut `alt` descriptif.
- Les icônes décoratives ont `aria-hidden"true"`.
- Les icônes fonctionnelles ont un `aria-label`.

---

## 11. Gestion de la configuration

### Variables d'environnement

Copier `.env.example` en `.env` et renseigner les valeurs :

```bash
# Base de données
OH_MARIADB_DATABASEopenhospital
OH_MARIADB_ROOT_PASSWORDmot-de-passe-fort
OH_MARIADB_USERoh_user
OH_MARIADB_PASSWORDmot-de-passe-fort
OH_MARIADB_PORT3306
OH_DB_LANGfr

# API Backend
OH_API_PORT8080
OH_API_PUBLISHED_PORT8080

# Frontend
OH_WEB_PORT8081
OH_WEB_LANGUAGEfr
OH_WEB_JWT_SECRETclé-secrète-256-bits-minimum
OH_WEB_ALLOWED_ORIGINShttp://localhost:8081

# Email (SMTP)
OH_WEB_MAIL_FROMnoreply@hopital-cheikh-zayed.ma
OH_SMTP_HOSTsmtp.example.com
OH_SMTP_PORT587
OH_SMTP_USERNAMEutilisateur
OH_SMTP_PASSWORDmot-de-passe
OH_SMTP_AUTHtrue
OH_SMTP_STARTTLStrue
```

### Séparation des configurations

 Fichier  Usage  Versionné 
---------------------------
 `.env.example`  Template avec valeurs vides   Oui 
 `.env`  Valeurs réelles locales   Non (.gitignore) 
 `application.properties`  Config Spring Boot (sans secrets)   Oui 
 `application-test.properties`  Config pour les tests (H2 in-memory)   Oui 

### Reproductibilité des environnements

- Docker Compose garantit que tous les développeurs utilisent les mêmes versions de services.
- Les versions des dépendances sont fixées (pas de `latest` en production).
- Le fichier `package-lock.json` est versionné pour garantir des builds reproductibles.

---

## 12. Conteneurisation (Bonus)

### Docker

**Images disponibles :**
- `Dockerfile.api`  Backend Spring Boot
- `webapp/Dockerfile`  Frontend React (build multi-stage)
- `Dockerfile-ohdb`  Base de données MariaDB avec schéma initial

### Docker Compose

**Développement (`docker-compose.yml`) :**
```yaml
services:
  db:       # MariaDB
  api:      # Spring Boot (port 8080)
  web:      # React/Nginx (port 8081)
  maildev:  # Serveur mail de test
```

**Production (`docker-compose.prod.yml`) :**
```yaml
services:
  db:      # MariaDB avec volumes persistants
  api:     # Spring Boot
  web:     # React/Nginx
  caddy:   # Reverse proxy avec HTTPS automatique (Let's Encrypt)
```

### Démarrage rapide

```bash
# Développement
docker compose up -d
docker compose logs -f api    # Suivre les logs

# Production
docker compose -f docker-compose.prod.yml up -d

# Arrêt
docker compose down

# Nettoyage complet (supprime les volumes !)
docker compose down -v
```

### Orchestration (Kubernetes  optionnel)

Pour un déploiement à grande échelle, des manifests Kubernetes peuvent être générés depuis les fichiers Docker Compose avec `kompose convert`.

---

##  Démarrage rapide

```bash
git clone https://github.com/ENSIAS-MEH/projet-web-team_web.git
cd hospital-cheikh-zayed
cp .env.example .env
docker compose up -d
# Ouvrir http://localhost:8081
# Login: admin / admin (à changer immédiatement)
```

---

##  Licence

Ce projet est basé sur [OpenHospital](https://github.com/informatici/openhospital)  GNU General Public License v3.0.

---

*Projet académique  Développement Web S2 P2  Institut National des Postes et Télécommunications (INPT)  Hôpital Cheikh Zayed  2025/2026*
