
# 🚀 Configurer un GitLab Runner local avec Docker Desktop (Windows)

Ce guide explique comment déployer un GitLab Runner en utilisant l'exécuteur Docker, avec la gestion du cache activée et le fichier de configuration `config.toml` monté en **lecture seule (`ro`)**.

## 🛠️ Étape 1 : Créer le dossier et générer la configuration

Ouvre **PowerShell** et lance les commandes suivantes pour créer un dossier local et enregistrer le Runner. *Un conteneur temporaire va se lancer pour générer le fichier.*

```powershell
# 1. va dans le dossier local_runner
cd local_runner
# 2. Lancer l'enregistrement interactif
docker run --rm -it -v ${PWD}:/etc/gitlab-runner gitlab/gitlab-runner:latest register
```

**Lors des questions de l'assistant, réponds :**

* **GitLab instance URL** : `https://gitlab.com/` (ou ton URL personnalisée)
* **Registration token** : *Le token fourni dans GitLab (Ton projet -> Settings -> CI/CD -> Runners*
* **Description** : `runner-local-windows`
* **Maintenance note** : `check config.toml`
* **Tags** : `node`
* **Executor** : `docker`
* **Default Docker image** : `node:22`

Un fichier `config.toml` vient d'apparaître dans ton dossier `local_runner`.

---

## ⚙️ Étape 2 : Activer le cache et Docker-in-Docker

Ouvre le fichier `config.toml` avec ton éditeur de texte (VS Code).

Trouve la section `[runners.docker]` et modifie la ligne `volumes` pour ajouter le socket Docker et le dossier `/cache` :

```toml
[runners.docker]
  image = "node:22"
  privileged = false
  disable_entrypoint_overwrite = false
  oom_kill_disable = false
  disable_cache = false
  # 👇 Modifie cette ligne 👇
  volumes =["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
  shm_size = 0
```

*Sauvegarde et ferme le fichier.*

---

## 🚀 Étape 3 : Lancer le GitLab Runner final (Read-Only)

Toujours dans le même dossier via **PowerShell**, lance le conteneur final en arrière-plan. Le fichier `config.toml` sera lié en lecture seule (`:ro`).

```powershell
# 1. Supprimer l'ancien conteneur s'il existe
docker rm -f gitlab-runner

# 2. Démarrer le Runner avec le fichier en Read-Only
docker run -d --name gitlab-runner --restart always -v /var/run/docker.sock:/var/run/docker.sock -v ${PWD}/config.toml:/etc/gitlab-runner/config.toml:ro gitlab/gitlab-runner:latest
```

🎉 **C'est terminé !** Ton Runner est maintenant actif dans Docker Desktop, utilise le cache pour accélérer tes pipelines, et ta configuration locale est protégée en lecture seule.

---

### ⚠️ Note importante concernant le mode "Read-Only"

GitLab effectue parfois une **rotation automatique des tokens de sécurité**. Puisque ton fichier est en lecture seule (`ro`), le Runner ne pourra pas y écrire le nouveau token.

Si un jour ton Runner se déconnecte subitement (statut *Offline* dans GitLab) :

1. Supprime le fichier `config.toml` local.
2. Refais l'**Étape 1** pour générer un nouveau token valide.
3. Refais l'**Étape 2** et redémarre le conteneur (`docker restart gitlab-runner`).
