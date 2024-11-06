# Installation de ArgoCD

Ce document décrit le processus d'installation d'ArgoCD sur une machine Ubuntu 22.04 ou 24.04.

## Documentation Officielle

Pour plus de détails, consultez : [ArgoCD Documentation](https://argo-cd.readthedocs.io/en/stable/getting_started/)

## Prérequis

- Un cluster Kubernetes opérationnel.
- `kubectl` configuré pour interagir avec votre cluster.

## Étapes d'installation

### 1. Créer le namespace ArgoCD

```bash
kubectl create namespace argocd
```

### 2. Installer ArgoCD

Exécutez la commande suivante pour appliquer le manifeste d'installation d'ArgoCD :

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Exposer le serveur ArgoCD

Pour accéder à l'interface web d'ArgoCD, vous pouvez soit modifier le type de service en `LoadBalancer`, soit utiliser le port-forwarding :

#### Option 1: Exposer le service avec NodePort

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

#### Option 2: Utiliser le port-forwarding

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### 4. Accéder à l'interface web d'ArgoCD

Pour vous connecter à ArgoCD, le nom d'utilisateur par défaut est admin. Vous devez obtenir le mot de passe initial avec la commande suivante :

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Notes

- Assurez-vous que votre cluster Kubernetes dispose des ressources nécessaires pour exécuter ArgoCD.
- Vous pouvez personnaliser davantage la configuration d'ArgoCD en modifiant les fichiers de manifeste après l'installation.
