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

4. **Accès à Kubernetes (kubectl)**
   
   Vérifiez les ressources Kubernetes dans le namespace `kube-system` :

   ```bash
   sudo kubectl get all -n kube-system
   ```

   Pour éviter d'utiliser `sudo` avec `kubectl`, modifiez les permissions du fichier de configuration Kubernetes :

   ```bash
   sudo chmod 644 /etc/rancher/k3s/k3s.yaml
   ```

   Puis:

   ```bash
   echo 'export KUBECONFIG=/etc/rancher/k3s/k3s.yaml' >> ~/.bashrc
   source ~/.bashrc
   ```

5. **Désactivation de Traefik (facultatif)**

   K3S installe par défaut Traefik en tant qu'ingress. Pour désactiver Traefik lors de l'installation, utilisez la commande suivante :

   ```bash
   curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable=traefik" sh -
   ```

   Vous pouvez également désactiver Traefik en ajoutant `disable: traefik` au fichier de configuration de K3S :

   ```bash
   sudo nano /etc/rancher/k3s/config.yaml
   ```

   Ajoutez la ligne suivante au fichier :

   ```bash
   disable: traefik
   ```

   Redémarrez ensuite K3S pour appliquer les modifications :

   ```bash
   sudo systemctl restart k3s
   ```

   Vérifiez les ressources dans le namespace kube-system :

   ```bash
   kubectl get all -n kube-system
   ```

   Et pour installer un nouveau controller ingress (nginx):

   [Documentation ici](https://kubernetes.github.io/ingress-nginx/deploy/?ref=blog.thenets.org#bare-metal)

   - Avec HELM :
      ```bash
      helm upgrade --install ingress-nginx ingress-nginx \
      --repo https://kubernetes.github.io/ingress-nginx \
      --namespace ingress-nginx --create-namespace
      ```

   - Avec Manifest:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/cloud/deploy.yaml
      ```

# Désinstallation de K3S

Pour désinstaller K3S et supprimer toutes les configurations associées :

```bash
/usr/local/bin/k3s-uninstall.sh
```

# Notes
 - La commande `kubectl` peut être utilisée sans `sudo` après modification des permissions du fichier de configuration.
 - Pour plus de flexibilité, les options de configuration de K3S peuvent être ajoutées dans `/etc/rancher/k3s/config.yaml`.
 - Vous pouvez consulter la documentation de K3S pour plus d'informations sur les configurations avancées et les options disponibles : [Documentation K3S](https://docs.k3s.io/).
 - Pour des déploiements en production, envisagez de configurer des sauvegardes régulières de votre cluster Kubernetes.
 - Assurez-vous que votre système d'exploitation et vos paquets sont à jour pour éviter des problèmes de compatibilité.
 - En cas de besoin d'assistance, rejoignez la communauté K3S sur des forums ou des plateformes comme GitHub et Discord pour échanger avec d'autres utilisateurs.
