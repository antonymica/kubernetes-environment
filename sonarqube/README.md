# Installation de SonarQube sur Docker

Ce guide vous explique comment installer SonarQube sur une instance Docker, en utilisant Docker Compose pour gérer les services nécessaires.

## Documentation Officielle

Pour plus de détails, consultez :
- [Installation de SonarQube depuis Docker](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/)
- [Installer SonarQube sur une instance AWS Ubuntu](https://medium.com/@shubnimkar/install-sonarqube-server-on-aws-ubuntu-instance-using-docker-compose-93d43cdc4312)

## Prérequis

- Un système Ubuntu à jour.
- Un utilisateur avec des privilèges sudo.

## Étapes d'installation

### 1. Installer Docker

Exécutez les commandes suivantes pour installer Docker et Docker Compose :

```bash
sudo apt update -y
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose -y
sudo service docker status
sudo service docker start
```

### 2. Configurer les paramètres système

Ajoutez les paramètres suivants dans le fichier de configuration sysctl.conf :

```bash
sudo vi /etc/sysctl.conf
```

Ajoutez les lignes suivantes à la fin du fichier :

```bash
vm.max_map_count=262144
fs.file-max=65536
```

Rechargez les paramètres :

```bash
sudo sysctl -p
```

### 3. Ajouter l'utilisateur au groupe Docker

Ajoutez votre utilisateur au groupe Docker pour exécuter Docker sans sudo :

```bash
sudo groupadd docker
sudo usermod -aG docker ubuntu
```

### 4. Vérifier l'installation de Docker

Vérifiez que Docker est correctement installé et en cours d'exécution :

```bash
docker images
docker ps
```

### 5. Installer SonarQube avec Docker Compose

Créez des volumes pour persister les données de SonarQube :

```bash
docker volume create --name sonarqube_data
docker volume create --name sonarqube_logs
docker volume create --name sonarqube_extensions
```

### 6. Créer le fichier docker-compose.yml

Éditez le fichier docker-compose.yml avec les configurations suivantes :

```bash
nano docker-compose.yml
```

Ajoutez le contenu suivant :

```bash
services:
  sonarqube:
    image: sonarqube:community
    hostname: sonarqube
    container_name: sonarqube
    read_only: true
    depends_on:
      db:
        condition: service_healthy
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
      - sonarqube_temp:/opt/sonarqube/temp
    ports:
      - "9000:9000"
  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 5
    hostname: postgresql
    container_name: postgresql
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_temp:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

### 7. Démarrer SonarQube

Démarrez SonarQube en utilisant Docker Compose :

```bash
docker-compose up -d
```

### 8. Vérifier l'exécution

Vérifiez que les images sont en cours d'exécution :

```bash
docker images
docker ps
```

Vous pouvez également utiliser :

```bash
docker-compose ps
```

### 9. Accéder à SonarQube

Ouvrez votre navigateur et accédez à SonarQube via l'URL suivante :

```bash
http://SERVER_IP:9000
```

Utilisez les identifiants suivants pour vous connecter :

    `Utilisateur` : `admin`
    `Mot de passe` : `admin`

### 10. Pour supprimer SonarQube

Si vous souhaitez arrêter et supprimer les conteneurs et les réseaux créés par Docker Compose, exécutez la commande suivante :

```bash
docker-compose down
```

# Notes

  -  Assurez-vous que Docker et Docker Compose sont correctement installés avant de commencer l'installation de SonarQube.
  -  Vérifiez la documentation officielle pour des instructions détaillées et des options de configuration avancées.
  