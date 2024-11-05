# Installation de GitLab Runner

Ce guide vous explique comment installer GitLab Runner sur une machine Ubuntu, ainsi que les étapes pour l'enregistrer et le configurer.

## Documentation Officielle

Pour plus de détails, consultez :
- [Installation de GitLab Runner (documentation officielle)](https://docs.gitlab.com/runner/install/linux-repository.html)
- [Guide d'installation de GitLab Runner sur Ubuntu](https://linux.how2shout.com/how-to-install-gitlab-runner-on-ubuntu-24-04-or-22-04-linux/)

## Prérequis

- Un système Ubuntu (22.04 ou 24.04 recommandé).
- Un utilisateur avec des privilèges sudo.

## Étapes d'installation

### 1. Mettre à jour le système

Mettez à jour les paquets existants :

```bash
sudo apt update
```

### 2. Installer le dépôt GitLab Runner

Exécutez la commande suivante pour ajouter le dépôt GitLab Runner à votre système :

```bash
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
```

### 3. Installer GitLab Runner

Installez GitLab Runner avec la commande suivante :

```bash
sudo apt install gitlab-runner
```

### 4. Installer une version spécifique de GitLab Runner (facultatif)

Si vous souhaitez installer une version spécifique, utilisez la commande suivante pour lister les versions disponibles :

```bash
apt-cache madison gitlab-runner
```

Puis installez la version souhaitée :

```bash
sudo apt install gitlab-runner=16.5.0
```

### 5. Mettre à jour GitLab Runner

Pour mettre à jour GitLab Runner vers la dernière version, exécutez :

```bash
sudo apt update
sudo apt install gitlab-runner
```

### 6. Enregistrer le Runner

Enregistrez le runner avec un jeton d'authentification :

#### Option 1: Enregistrement interactif

```bash
sudo gitlab-runner register
```

#### Option 2: Enregistrement non interactif avec un token de projet

```bash
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --token "$RUNNER_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner"
```

#### Option 3: Enregistrement non interactif avec des options avancées

```bash
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "$PROJECT_REGISTRATION_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner" \
  --maintenance-note "Notes sur le mainteneur de ce runner" \
  --tag-list "docker,aws" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected"
```

### 7. Démarrer et activer le service GitLab Runner

Démarrez le service GitLab Runner et activez-le pour qu'il se lance automatiquement au démarrage :

```bash
sudo systemctl start gitlab-runner
sudo systemctl enable gitlab-runner
```

### 8. Vérifier l'état du service

Pour vérifier l'état du service GitLab Runner, utilisez la commande suivante :

```bash
sudo systemctl status gitlab-runner --no-pager -l
```

# Notes

- Assurez-vous d'avoir accès à un jeton d'enregistrement pour le runner. Ce jeton peut être trouvé dans l'interface de votre projet GitLab.
- Si vous prévoyez d'utiliser Docker comme exécuteur, assurez-vous que Docker est installé sur votre machine.
- Les options d'enregistrement permettent une personnalisation avancée du runner, telles que les tags, les notes de maintenance et le niveau d'accès.
