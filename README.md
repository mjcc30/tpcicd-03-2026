# TP CI/CD - Déploiement Continu avec GitLab CI & GitHub Actions

## 📂 Structure du projet

- `front/` : Application React (Frontend).
- `back/` : API Express (Backend).
- `.gitlab-ci.yml` : Pipeline d'intégration et déploiement pour GitLab CI.
- `.github/workflows/main.yml` : Pipeline d'intégration et déploiement pour GitHub Actions.
- `docker-compose.yml` : Fichier de configuration Docker pour exécuter l'application localement.

## 🐳 Exécuter le projet localement avec Docker

Assurez-vous d'avoir [Docker](https://www.docker.com/) et [Docker Compose](https://docs.docker.com/compose/) installés sur votre machine.

Pour lancer l'application complète (Front + Back), exécutez la commande suivante à la racine du projet :

```bash
docker-compose up -d --build
```

- Le **Frontend** sera accessible sur : [http://localhost:3000](http://localhost:3000)
- Le **Backend** sera accessible sur : [http://localhost:3001](http://localhost:3001)

Pour arrêter les conteneurs :

```bash
docker-compose down
```

## 🚀 CI/CD : Configuration des Pipelines

Le projet est pré-configuré pour être déployé automatiquement sur **Netlify** (pour le frontend) et **Google Cloud Run** (pour le backend) à chaque *push* sur la branche `main`.

### Variables d'environnement / Secrets requis

Pour que les pipelines (GitLab CI ou GitHub Actions) puissent se connecter à vos comptes et déployer le code, vous devez configurer les variables secrètes suivantes dans les paramètres de votre dépôt (GitLab CI/CD Settings ou GitHub Secrets) :

**Pour Google Cloud Run (Backend) :**

- `GCP_PROJECT_ID` : L'ID de votre projet Google Cloud Platform.
- `GCP_SERVICE_ACCOUNT_KEY` : Le contenu JSON de la clé de votre compte de service GCP (ce compte doit avoir les droits d'administration sur Cloud Run et Cloud Build).
- `GCP_REGION` : La région de déploiement (ex: `europe-west1`).
- `CLOUD_RUN_SERVICE_NAME` : Le nom que vous souhaitez donner à votre service Cloud Run (ex: `tp-cicd-backend`).

**Pour Netlify (Frontend) :**

- `NETLIFY_AUTH_TOKEN` : Votre jeton d'accès personnel Netlify (User Settings > Applications).
- `NETLIFY_SITE_ID` : L'ID de l'API de votre site Netlify (Site Settings > Site details > Site information).

### 🦊 GitLab CI

Si vous utilisez GitLab, le pipeline est défini dans le fichier `.gitlab-ci.yml` (qui inclut les configurations de `front/.gitlab-ci.yml` et `back/.gitlab-ci.yml`). 
Les variables doivent être ajoutées dans `Settings > CI/CD > Variables` de votre projet GitLab. Assurez-vous de les masquer (Masked) et de les protéger (Protected) si nécessaire.

### 🐙 GitHub Actions

Si vous utilisez GitHub, le pipeline est défini dans `.github/workflows/main.yml`.
Les variables doivent être ajoutées dans `Settings > Secrets and variables > Actions > New repository secret`.

## 🛠️ Commandes manuelles (sans Docker)

Si vous souhaitez faire tourner les projets manuellement sans Docker (nécessite Node.js v22) :

**Backend :**
```bash
cd back
npm install
node index.js
```

**Frontend :**
```bash
cd front
npm install
npm start
```
