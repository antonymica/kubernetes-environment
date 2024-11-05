# Installation de Prometheus et Grafana sur Minikube

Ce document décrit le processus d'installation de Prometheus et Grafana sur un cluster Minikube.

## Documentation Officielle

Pour plus de détails, consultez : [Setup Prometheus and Grafana in Minikube](https://blog.marcnuri.com/prometheus-grafana-setup-minikube)

## Prérequis

- Un cluster Minikube opérationnel.
- kubectl configuré pour interagir avec votre cluster.

## Créer un namespace pour Monitoring

Avant d'installer Prometheus, créez un namespace appelé `monitoring` :

```bash
kubectl create namespace monitoring
```

## Installation de Prometheus

### 1. Ajouter le dépôt Prometheus

Ajoutez le dépôt Helm de Prometheus :

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

### 2. Installer Prometheus

Exécutez la commande suivante pour installer Prometheus :

```bash
helm install prometheus prometheus-community/prometheus -n monitoring
```

### 3. Exposer le service Prometheus

Exposez le service Prometheus avec un type NodePort :

```bash
kubectl patch svc prometheus-server -n monitoring -p {"spec": {"type": "NodePort"}}'
```

### 4. Vérifier les pods de Prometheus

Pour vérifier que les pods de Prometheus sont en cours d'exécution :

```bash
kubectl get pods -l app.kubernetes.io/instance=prometheus -n monitoring
```

### 5. Accéder à Prometheus

Utilisez la commande suivante pour accéder à l'interface web de Prometheus :

```bash
minikube service prometheus-server -n monitoring
```

## Installation de Grafana

### 1. Ajouter le dépôt Grafana

Ajoutez le dépôt Helm de Grafana :

```bash
helm repo add grafana https://grafana.github.io/helm-charts
```

### 2. Installer Grafana

Installez Grafana avec la commande suivante :

```bash
helm install grafana grafana/grafana -n monitoring
```

### 3. Exposer le service Grafana

Exposez le service Grafana avec un type NodePort :

```bash
kubectl patch svc grafana -n monitoring -p {"spec": {"type": "NodePort"}}'
```

### 4. Obtenir le mot de passe administrateur de Grafana

Pour récupérer le mot de passe admin par défaut :

```bash
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

### 5. Accéder à Grafana

Utilisez la commande suivante pour accéder à l'interface web de Grafana :

```bash
minikube service grafana -n monitoring
```

## Notes

- Assurez-vous que votre cluster Minikube dispose des ressources nécessaires pour exécuter Prometheus et Grafana.
- Une fois Grafana installé, vous pouvez configurer vos sources de données et tableaux de bord selon vos besoins.

