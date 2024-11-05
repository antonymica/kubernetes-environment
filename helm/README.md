# Installation de Helm

Ce document décrit le processus d'installation de Helm sur une machine Ubuntu 22.04 ou 24.04.

## Documentation Officielle

Pour plus de détails, consultez : [Helm Documentation](https://helm.sh/docs/intro/install/)

## Prérequis

- Un système Ubuntu 22.04 ou 24.04.
- curl et gpg installés.

## Étapes d'installation

### 1. Ajouter la clé de signature de Helm

Exécutez la commande suivante pour ajouter la clé GPG :

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
```

### 2. Installer les paquets nécessaires

Installez apt-transport-https pour permettre l'utilisation de dépôts HTTPS :

```bash
sudo apt-get install apt-transport-https --yes
```

### 3. Ajouter le dépôt Helm

Ajoutez le dépôt stable de Helm :

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
```

### 4. Mettre à jour les paquets

Mettez à jour la liste des paquets :

```bash
sudo apt-get update
```

### 5. Installer Helm

Enfin, installez Helm :

```bash
sudo apt-get install helm
```

## Notes

- Helm est un gestionnaire de packages pour Kubernetes qui facilite l'installation et la gestion des applications Kubernetes.
- Assurez-vous de consulter la documentation officielle pour apprendre à utiliser Helm avec des chartes.