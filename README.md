# 🚀 CI/CD Pipeline avec Jenkins, Docker, Spring Boot, GitHub et Cloudflare Tunnel

![Java](https://img.shields.io/badge/Java-17-orange)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-4.1.0-brightgreen)
![Maven](https://img.shields.io/badge/Maven-3.9.16-yellow)
![Docker](https://img.shields.io/badge/Docker-Latest-blue)
![Jenkins](https://img.shields.io/badge/Jenkins-2.563-red)
![GitHub](https://img.shields.io/badge/GitHub-Repository-black)
![CI/CD](https://img.shields.io/badge/CI/CD-Automatisation-success)

---

# 📖 Présentation du projet

Ce projet a été réalisé dans le cadre d'un **Travail Pratique (TP)** sur l'intégration continue (Continuous Integration - CI) et le déploiement continu (Continuous Deployment - CD).

L'objectif principal est de mettre en place une chaîne complète d'automatisation permettant de compiler, tester, construire, publier et déployer automatiquement une application Spring Boot à chaque modification du code source.

Pour atteindre cet objectif, plusieurs outils DevOps sont utilisés :

- Java
- Spring Boot
- Maven
- Git
- GitHub
- Jenkins
- Docker
- Docker Hub
- Cloudflare Tunnel

L'ensemble de ces technologies permet d'automatiser tout le cycle de développement logiciel.

---

# 🎯 Objectifs du TP

À la fin de ce TP, l'étudiant sera capable de :

- Comprendre les principes de l'intégration continue (CI)
- Comprendre les principes du déploiement continu (CD)
- Créer une application Spring Boot
- Compiler une application avec Maven
- Utiliser Git pour gérer les versions
- Héberger un projet sur GitHub
- Construire une image Docker
- Déployer une application dans un conteneur Docker
- Installer et configurer Jenkins
- Connecter Jenkins à GitHub
- Configurer les Credentials GitHub et Docker Hub
- Mettre en place un Pipeline CI/CD
- Configurer un Webhook GitHub
- Utiliser Cloudflare Tunnel pour rendre Jenkins accessible depuis Internet
- Automatiser entièrement le déploiement de l'application

---

# 🛠 Technologies utilisées

| Technologie       | Version           |
| ----------------- | ----------------- |
| Java              | 17                |
| Spring Boot       | 4.1.0             |
| Maven             | 3.9.16            |
| Git               | 2.54              |
| GitHub            | Cloud             |
| Docker Desktop    | 29.5.2            |
| Docker Hub        | Dernière version |
| Jenkins           | 2.563             |
| Cloudflare Tunnel | Latest            |
| Windows           | 11                |

---

# 📚 Présentation des technologies

## ☕ Java

Java est le langage de programmation utilisé pour développer l'application.

Il permet de créer des applications robustes, portables et multiplateformes.

---

## 🌱 Spring Boot

Spring Boot est un framework Java permettant de développer rapidement des applications web.

Il simplifie énormément la configuration des projets Java.

---

## 📦 Maven

Maven est un outil de gestion de projet.

Il permet notamment de :

- télécharger automatiquement les dépendances
- compiler le projet
- lancer les tests
- générer le fichier JAR

---

## 🐙 Git

Git est un système de gestion de versions.

Il permet de suivre toutes les modifications du projet.

---

## ☁ GitHub

GitHub est la plateforme qui héberge le dépôt Git.

C'est également GitHub qui envoie les Webhooks vers Jenkins lorsqu'un développeur effectue un Push.

---

## 🤖 Jenkins

Jenkins est un serveur d'intégration continue.

Il surveille le dépôt GitHub et exécute automatiquement toutes les étapes définies dans le Jenkinsfile.

---

## 🐳 Docker

Docker permet de créer une image contenant toute l'application.

Cette image peut ensuite être exécutée sur n'importe quelle machine disposant de Docker.

---

## 🐳 Docker Hub

Docker Hub est un registre permettant de publier et partager les images Docker.

---

## 🌍 Cloudflare Tunnel

Cloudflare Tunnel permet d'exposer Jenkins sur Internet sans ouvrir de port sur la box Internet.

Il crée une URL publique qui redirige vers Jenkins exécuté en local.

Grâce à cela, GitHub peut communiquer avec Jenkins via les Webhooks.

---

# 📁 Structure du projet

```
jenkins-docker-demo/

│
├── src/
│   ├── main/
│   └── test/
│
├── Dockerfile
├── Jenkinsfile
├── pom.xml
├── README.md
└── .gitignore
```

---

# ⚙ Prérequis

Avant de commencer, installer :

- Java JDK 17
- Maven
- Git
- Docker Desktop
- Jenkins
- Cloudflared
- VS Code

Vérifier également que Docker Desktop est démarré avant d'exécuter le Pipeline.

---

# 📌 Installation du projet

Cloner le dépôt GitHub :

```bash
git clone https://github.com/Fanantenana-ds/jenkins-docker-demo.git
```

Entrer dans le projet :

```bash
cd jenkins-docker-demo
```

---

# 📌 Compilation du projet

Compiler l'application :

```bash
mvn clean package
```

Cette commande effectue automatiquement :

- téléchargement des dépendances
- compilation
- exécution des tests
- génération du fichier JAR

Le fichier est généré dans :

```
target/
```

---

# 📌 Construction de l'image Docker

Construire l'image :

```bash
docker build -t manaosoa/jenkins-docker-demo:latest .
```

Lister les images :

```bash
docker images
```

---

# 📌 Exécution du conteneur

Créer le conteneur :

```bash
docker run -d -p 8081:8080 --name jenkins-demo manaosoa/jenkins-docker-demo:latest
```

Vérifier :

```bash
docker ps
```

Tester ensuite :

```
http://localhost:8081
```

---

# ⚙ Configuration de Jenkins

Dans Jenkins, configurer :

- Java
- Maven
- Git
- GitHub Personal Access Token
- Docker Hub Personal Access Token

Créer ensuite un Pipeline utilisant le fichier :

```
Jenkinsfile
```

présent dans le dépôt GitHub.

---

# 🔐 Configuration des Credentials

Deux Credentials sont nécessaires.

## GitHub

Type :

```
Username with password
```

Nom d'utilisateur :

```
GitHub Username
```

Mot de passe :

```
GitHub Personal Access Token
```

ID :

```
github-token
```

---

## Docker Hub

Type :

```
Username with password
```

Nom d'utilisateur :

```
Nom d'utilisateur Docker Hub
```

Mot de passe :

```
Docker Hub Personal Access Token
```

ID :

```
dockerhub-token
```

---

# 🌐 Configuration de Cloudflare Tunnel

Lancer Cloudflare Tunnel :

```bash
cloudflared tunnel --url http://localhost:8080
```

Cloudflare génère alors une URL publique :

```
https://xxxxxxxx.trycloudflare.com
```

Cette URL permet à GitHub d'accéder à Jenkins.

---

# 🔗 Configuration du Webhook GitHub

Dans GitHub :

```
Settings

↓

Webhooks

↓

Add Webhook
```

Payload URL :

```
https://xxxxx.trycloudflare.com/github-webhook/
```

Content Type :

```
application/json
```

Événement :

```
Just the push event
```

---

# ⚙ Fonctionnement complet du Pipeline

Le Pipeline exécute automatiquement les étapes suivantes :

## 1. Checkout

Clone le projet depuis GitHub.

---

## 2. Build Maven

Compile le projet.

---

## 3. Tests

Exécute les tests unitaires.

---

## 4. Package

Construit le fichier JAR.

---

## 5. Docker Build

Construit une image Docker.

---

## 6. Docker Login

Se connecte automatiquement à Docker Hub.

---

## 7. Docker Push

Publie l'image Docker sur Docker Hub.

---

## 8. Stop Container

Arrête l'ancien conteneur.

---

## 9. Remove Container

Supprime l'ancien conteneur.

---

## 10. Docker Run

Déploie automatiquement le nouveau conteneur.

---

# 🔄 Architecture globale

```
                Développeur
                     │
              git add / commit
                     │
                     ▼
                git push
                     │
                     ▼
                 GitHub
                     │
                  Webhook
                     │
                     ▼
          Cloudflare Tunnel
                     │
                     ▼
                 Jenkins
                     │
      ┌──────────────┴──────────────┐
      ▼                             ▼
 Build Maven                    Tests
      │
      ▼
 Package (.jar)
      │
      ▼
 Docker Build
      │
      ▼
 Docker Login
      │
      ▼
 Docker Push
      │
      ▼
 Docker Run
      │
      ▼
 Application Spring Boot
```

---

# 🔄 Fonctionnement détaillé du Workflow

Le workflow complet est le suivant :

1. Le développeur modifie le code dans VS Code.
2. Il effectue un :

```
git add .
```

3. Puis :

```
git commit
```

4. Ensuite :

```
git push
```

5. GitHub reçoit le nouveau code.
6. GitHub déclenche automatiquement le Webhook.
7. Le Webhook envoie une requête HTTP vers Cloudflare Tunnel.
8. Cloudflare Tunnel redirige cette requête vers Jenkins.
9. Jenkins détecte l'événement Push.
10. Jenkins lance automatiquement le Pipeline.
11. Maven compile le projet.
12. Les tests sont exécutés.
13. Le fichier JAR est généré.
14. Docker construit une nouvelle image.
15. Jenkins se connecte à Docker Hub.
16. L'image Docker est publiée sur Docker Hub.
17. L'ancien conteneur est arrêté.
18. L'ancien conteneur est supprimé.
19. Un nouveau conteneur est créé.
20. L'application est immédiatement disponible.

Aucune intervention manuelle n'est nécessaire après le `git push`.

---

# 📦 Commandes utiles

Compiler :

```bash
mvn clean package
```

Lancer les tests :

```bash
mvn test
```

Construire l'image :

```bash
docker build -t manaosoa/jenkins-docker-demo:latest .
```

Lister les images :

```bash
docker images
```

Lister les conteneurs :

```bash
docker ps
```

Arrêter un conteneur :

```bash
docker stop jenkins-demo
```

Supprimer un conteneur :

```bash
docker rm jenkins-demo
```

Supprimer une image :

```bash
docker rmi manaosoa/jenkins-docker-demo
```

Lancer Cloudflare :

```bash
cloudflared tunnel --url http://localhost:8080
```

---

# ⚠ Difficultés rencontrées et solutions

| Problème rencontré              | Solution apportée                                               |
| --------------------------------- | ---------------------------------------------------------------- |
| Jenkins incompatible avec Java 17 | Installation de Java 21 pour Jenkins                             |
| Docker Login refusé              | Création d'un Personal Access Token Docker Hub                  |
| Mauvais identifiants Docker Hub   | Mise à jour des Credentials Jenkins                             |
| Erreur Webhook 404                | Correction de l'URL du Webhook GitHub                            |
| Erreur 502 Cloudflare             | Vérification que Jenkins était bien démarré sur le port 8080 |
| Tunnel Cloudflare inaccessible    | Redémarrage du tunnel avec la bonne URL (`localhost:8080`)    |
| Docker non lancé                 | Démarrage de Docker Desktop avant le Pipeline                   |

---

# 🎓 Compétences acquises

À l'issue de ce TP, les compétences suivantes ont été développées :

- Java
- Spring Boot
- Maven
- Git
- GitHub
- GitHub Webhooks
- Jenkins
- Jenkins Pipeline
- Docker
- Docker Hub
- Cloudflare Tunnel
- Intégration Continue (CI)
- Déploiement Continu (CD)
- Automatisation des builds
- Déploiement automatisé
- Gestion des conteneurs Docker
- Gestion des Credentials sécurisés
- Débogage d'une chaîne CI/CD

---

# 🚀 Perspectives d'amélioration

Plusieurs évolutions peuvent être envisagées pour ce projet :

- Déploiement sur un serveur Linux
- Utilisation de Docker Compose
- Déploiement sur Kubernetes
- Intégration de SonarQube pour l'analyse de qualité du code
- Mise en place de tests d'intégration
- Gestion de plusieurs environnements (Développement, Test et Production)

---

# 👨‍💻 Auteur

**Manaosoa RANDRIA**

GitHub :

https://github.com/Fanantenana-ds

---

# 📄 Licence

Ce projet a été réalisé dans le cadre d'un Travail Pratique (TP) d'apprentissage des concepts DevOps, de l'intégration continue (CI) et du déploiement continu (CD). Il est destiné à un usage pédagogique et peut servir de base pour des projets d'automatisation plus avancés.
