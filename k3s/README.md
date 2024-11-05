# Installation de K3S

Ce guide fournit les instructions pour installer, configurer, et désinstaller K3S, une distribution légère de Kubernetes. Les commandes ci-dessous permettent de démarrer un cluster Kubernetes en utilisant K3S sur un serveur Ubuntu.

## Prérequis

- Un serveur Ubuntu accessible via SSH
- Un utilisateur avec des privilèges sudo

### Documentation Officielle

Pour plus de détails, consultez :
- [Documentation K3S](https://docs.k3s.io/installation)
- [Tutoriel DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-setup-k3s-kubernetes-cluster-on-ubuntu)

## Étapes d'installation

1. **Connexion au serveur**

   Connectez-vous à votre serveur via SSH :

   ```bash
   ssh user@your_server_ip
   ```

2. **Installation de K3S**
   
   Utilisez la commande suivante pour installer K3S :

   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```	

3. **Vérification du statut de K3S**
   
   Une fois l'installation terminée, vérifiez que K3S est en cours d'exécution :

   ```bash
   systemctl status k3s
   ```



