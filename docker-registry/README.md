# Comment Configurer un Registre Docker Privé sur Ubuntu 22.04

Ce guide vous explique comment installer et configurer Docker Registry sur une machine Ubuntu 22.04 pour stocker et gérer vos images Docker en privé.

## Prérequis

Avant de commencer, assurez-vous d'avoir les éléments suivants :

- Un système Ubuntu 22.04 installé.
- Un Docker installé.
- Un utilisateur avec des privilèges sudo.

## Documentation Officielle

Pour plus de détails, consultez :
- [Documentation de DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-22-04)

## Étape 1 : Installation et Configuration de Docker Registry

Créez un répertoire pour le registre et configurez Docker Compose avec le fichier `docker-compose.yml` :

```bash
mkdir ~/docker-registry
cd ~/docker-registry
mkdir data
nano docker-compose.yml
```
Ajoutez le contenu suivant dans `docker-compose.yml` :

```bash
version: '3'

services:
  registry:
    image: registry:latest
    ports:
    - "5000:5000"
    environment:
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      - ./data:/data

```

Lancez le service Docker Registry :

```bash
docker compose up -d
```

## Étape 2 : Configuration du Transfert de Port avec Nginx

Créez et modifiez la configuration de Nginx pour rediriger le trafic vers le registre Docker :

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

Ajoutez les directives de configuration suivantes dans le bloc `location` :

```bash
  location / {
      if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
        return 404;
      }
      proxy_pass                          http://localhost:5000;
      proxy_set_header  Host              $http_host;
      proxy_set_header  X-Real-IP         $remote_addr;
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
      proxy_read_timeout                  900;
  }
```

Redémarrez Nginx pour appliquer les modifications :

```bash
sudo systemctl restart nginx
```
Vérifiez que le registre est accessible en accédant à `https://your_domain/v2`.

## Étape 3 : Configuration de l'Authentification

Installez l'utilitaire Apache `htpasswd` pour gérer les utilisateurs et les mots de passe :

```bash
sudo apt install apache2-utils -y
```

Créez un répertoire auth et générez un fichier de mot de passe :

```bash
mkdir ~/docker-registry/auth
cd ~/docker-registry/auth
htpasswd -Bc registry.password username
```

Modifiez le fichier `docker-compose.yml` pour ajouter la configuration d'authentification :

```bash
version: '3'

services:
  registry:
    image: registry:latest
    ports:
    - "5000:5000"
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/registry.password
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      - ./auth:/auth
      - ./data:/data
```

Redémarrez le registre :

```bash
cd ~/docker-registry
docker compose up -d
```

## Étape 4 : Exécution du Registre Docker en tant que Service

Modifiez `docker-compose.yml` pour activer le redémarrage automatique :

```bash
...
  registry:
    restart: always
...
```

Exécutez le registre en mode détaché :

```bash
docker compose up -d
```

## Étape 5 : Augmentation de la Taille de Téléchargement de Fichiers pour Nginx

Pour permettre le téléchargement de grandes images, modifiez la configuration Nginx :

```bash
sudo nano /etc/nginx/nginx.conf
```

Ajoutez `client_max_body_size` dans le bloc `http` :

```bash
...
http {
        client_max_body_size 16384m;
        ...
}
...
```

Redémarrez Nginx :

```bash
sudo systemctl restart nginx
```

## Étape 6 : Publier une Image vers votre Registre Docker Privé

Créez une image Docker, connectez-vous au registre, puis publiez l'image :

```bash
docker run -t -i ubuntu /bin/bash
touch /SUCCESS
exit
docker commit $(docker ps -lq) test-image
docker login https://your_domain
docker tag test-image your_domain/test-image
docker push your_domain/test-image
```

## Étape 7 : Tirer une Image depuis votre Registre Docker Privé

Connectez-vous au registre, puis tirez l'image et exécutez-la :

```bash
docker login https://your_domain
docker pull your_domain/test-image
docker run -it your_domain/test-image /bin/bash
```

## Conclusion
Vous avez maintenant configuré un registre Docker privé et sécurisé sur votre serveur Ubuntu 22.04, ce qui vous permet de stocker et gérer vos images Docker en interne.
