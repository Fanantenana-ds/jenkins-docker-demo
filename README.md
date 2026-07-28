# 🚀 TP CI/CD avec Jenkins, Docker et Spring Boot

## 📖 Description

Ce projet est un TP permettant d'apprendre la mise en place d'une chaîne **CI/CD (Continuous Integration / Continuous Deployment)** en utilisant :

- Java 17
- Spring Boot
- Maven
- Git & GitHub
- Jenkins
- Docker
- Windows 11

L'objectif est d'automatiser la compilation, les tests, la création d'une image Docker ainsi que le déploiement de l'application grâce à Jenkins.

---

# 🎯 Objectifs du TP

À la fin de ce TP, l'étudiant sera capable de :

- créer une application Spring Boot
- utiliser Maven pour compiler le projet
- gérer le code avec Git et GitHub
- créer une image Docker
- exécuter une application dans un conteneur Docker
- installer et configurer Jenkins
- connecter Jenkins à GitHub
- créer un pipeline CI/CD
- automatiser le déploiement avec Docker

---

# 🛠 Technologies utilisées

| Technologie | Version |
|-------------|----------|
| Java | 17 |
| Spring Boot | 4.1.0 |
| Maven | 3.9.16 |
| Git | 2.54.0 |
| Docker Desktop | 29.5.2|
| Jenkins | 2.563 |
| Windows | 11 |

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
- VS Code

---

# 📌 Étape 1 : Cloner le projet

```bash
git clone https://github.com/Fanantenana-ds/jenkins-docker-demo.git
```

Entrer dans le projet :

```bash
cd jenkins-docker-demo
```

---

# 📌 Étape 2 : Compiler le projet

```bash
mvn clean package
```

Cette commande :

- télécharge les dépendances
- compile le projet
- exécute les tests
- génère le fichier JAR

Le fichier est créé dans :

```
target/
```

---

# 📌 Étape 3 : Construire l'image Docker

```bash
docker build -t jenkins-docker-demo .
```

Vérifier :

```bash
docker images
```

---

# 📌 Étape 4 : Lancer le conteneur

```bash
docker run -d -p 8081:8080 --name jenkins-demo jenkins-docker-demo
```

Vérifier :

```bash
docker ps
```

---

# 📌 Étape 5 : Tester l'application

Ouvrir :

```
http://localhost:8081
```

Résultat attendu :

```
Hello Jenkins Docker!
```

---

# 📌 Étape 6 : Installer Jenkins

Configurer :

- Java
- Maven
- Git
- GitHub Credentials (PAT)

Créer ensuite un Pipeline Jenkins.

---

# 📌 Étape 7 : Pipeline CI/CD

Le pipeline réalise automatiquement :

1. Cloner le dépôt GitHub
2. Compiler le projet
3. Exécuter les tests
4. Générer le fichier JAR
5. Construire l'image Docker
6. Arrêter l'ancien conteneur
7. Déployer le nouveau conteneur

---

# 🔄 Pipeline CI/CD

```
GitHub
   │
   ▼
Jenkins
   │
   ▼
Clone
   │
   ▼
Build Maven
   │
   ▼
Tests
   │
   ▼
Package
   │
   ▼
Docker Build
   │
   ▼
Docker Run
   │
   ▼
Application déployée
```

---

# 📦 Commandes utiles

Compiler :

```bash
mvn clean package
```

Construire l'image :

```bash
docker build -t jenkins-docker-demo .
```

Lancer le conteneur :

```bash
docker run -d -p 8081:8080 --name jenkins-demo jenkins-docker-demo
```

Voir les conteneurs :

```bash
docker ps
```

Voir les images :

```bash
docker images
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
docker rmi jenkins-docker-demo
```

---

# 👨‍💻 Auteur

**Manaosoa RANDRIA (Fanantenana-ds)**

GitHub :

https://github.com/Fanantenana-ds

---

# 📚 Compétences acquises

✔ Java

✔ Spring Boot

✔ Maven

✔ Git

✔ GitHub

✔ Docker

✔ Jenkins

✔ CI/CD

✔ Pipeline

✔ Déploiement automatique

---

# 📄 Licence

Projet réalisé dans le cadre d'un TP d'apprentissage du CI/CD.
