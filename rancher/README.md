# Installation de Rancher

Ce guide fournit les instructions pour installer et configurer Rancher.

## Prérequis

- Un serveur Ubuntu accessible via SSH
- Un utilisateur avec des privilèges sudo
- `kubectl`
- `helm`
- `cert-manager`

### Documentation Officielle

Pour plus de détails, consultez :
- [Documentation Rancher](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster#4-install-cert-manager)
- [Tutoriel Medium](https://ozgur-kolukisa.medium.com/installing-rancher-on-k3s-with-helm-charts-309bc7287240)

## Étapes d'installation

1. **Connexion au serveur**

   Connectez-vous à votre serveur via SSH :

   ```bash
   ssh user@your_server_ip
   ```

2. **Installer kubectl**

    ```bash
    # Téléchargez la dernière version de kubectl
    curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl

    # Vérifiez le package (optionnel mais recommandé)
    curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
    echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    kubectl version --client
    ```

3. **Installer Helm**

    Pour installer Helm, suivez les instructions disponibles [ici](https://github.com/antonymica/kubernetes-environment/tree/main/helm).

4. **Ajouter rancher-stable Helm repo**

    ```bash
    helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
    ```

    Créez un namespace Kubernetes pour Rancher :

    ```bash
    kubectl create namespace cattle-system
    ```

5. **Installer cert-manager**

    Nous utiliserons Let's Encrypt :

    ```bash
    kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.16.1/cert-manager.crds.yaml
    kubectl create namespace cert-manager
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.16.1
    kubectl get pods --namespace cert-manager
    ```

6. **Installer Rancher**

    ```bash
    helm install rancher rancher-stable/rancher \
    --namespace cattle-system \
    --set hostname=rancher.nomdedomain.com \
    --set bootstrapPassword=admin \
    --set ingress.tls.source=letsEncrypt \
    --set letsEncrypt.email=mail@domain.com \
    --set letsEncrypt.ingress.class=nginx
    ```

    `helm install rancher rancher-stable/rancher` : Commande principale, installe Rancher depuis le dépôt `rancher-stable` avec le nom `rancher`.

    `--namespace cattle-system` : Définit le namespace dans le cluster Kubernetes où Rancher sera installé.

    `--set bootstrapPassword=admin` : Définit un mot de passe temporaire pour l'initialisation de Rancher (à sauvegarder en sécurité).

    `--set ingress.tls.source=letsEncrypt` : Utilise Let's Encrypt pour les certificats TLS.

    `--set letsEncrypt.email=mail@domain.com` : Ajoute un e-mail au certificat SSL.

    `--set letsEncrypt.ingress.class=nginx` : Définit la classe d'ingress pour Let's Encrypt sur `nginx`.

    `--set hostname=rancher.nomdedomain.com` : Enregistre le DNS de Rancher (doit être configuré avant ou après l'installation).

    Si vous avez oublié le mot de passe de bootstrap, exécutez :

    ```bash
    kubectl get secret -n cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}{{ "\n" }}'
    ```

    Pour obtenir l'URL d'initialisation du tableau de bord :

    ```bash
    echo https://rancher.nomdedomain.com/dashboard/?setup=$(kubectl get secret -n cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}')
    ```

## Note

    Ce guide couvre les étapes de base pour installer Rancher avec Helm et cert-manager, en utilisant Let's Encrypt pour les certificats SSL.
