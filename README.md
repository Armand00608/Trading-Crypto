# 📈 Trading-Crypto — Backend Spring Boot

> Backend d'une application de trading de cryptomonnaies, conçu avec les contraintes de sécurité et de fiabilité du secteur **FinTech**.

---

## 📋 Sommaire

- [À propos du projet](#-à-propos-du-projet)
- [Architecture](#-architecture)
- [Stack technique](#-stack-technique)
- [Licence](#-licence)

---

## 🧩 À propos du projet

Ce projet a été réalisé dans le cadre d'une montée en compétence sur l'écosystème **Java Enterprise (Spring Boot)**. Il constitue le cœur transactionnel d'une plateforme de trading de cryptomonnaies, en répondant aux exigences strictes du secteur FinTech :

- **Sécurité** : authentification JWT, gestion fine des rôles et des accès.
- **Fiabilité** : transactions atomiques, typage fort des valeurs monétaires.
- **Intégrité des données** : schéma SQL optimisé, contraintes de base de données.

---

## 🏗 Architecture

```
trading-crypto/
├── src/
│   ├── main/
│   │   ├── java/com/tradingcrypto/
│   │   │   ├── config/          # Configuration Spring (Security, CORS, etc.)
│   │   │   ├── controller/      # Couche REST (endpoints HTTP)
│   │   │   ├── dto/             # Data Transfer Objects (requêtes / réponses)
│   │   │   ├── entity/          # Entités JPA (mappées sur PostgreSQL)
│   │   │   ├── exception/       # Gestion centralisée des erreurs
│   │   │   ├── repository/      # Accès données (Spring Data JPA)
│   │   │   ├── service/         # Logique métier & transactions
│   │   │   └── security/        # JWT, filtres d'authentification
│   │   └── resources/
│   │       ├── application.yml           # Configuration principale
│   │       └── application-local.yml     # (ignoré par git) config locale
│   └── test/                    # Tests unitaires & d'intégration
├── python-service/              # (futur) Microservice Python — scraping & analyse
├── docker-compose.yml           # PostgreSQL + App
├── pom.xml
└── README.md
```

L'application suit un découpage en **couches classique** (Controller → Service → Repository) et est conçue pour une **interopérabilité future** avec un microservice Python dédié à l'analyse de marché et au scraping de prix en temps réel.

---

## 🛠 Stack technique

| Couche | Technologie |
|---|---|
| Langage | Java 17 |
| Framework | Spring Boot 3.x |
| Sécurité | Spring Security + JWT |
| Persistance | Spring Data JPA / Hibernate |
| Base de données | PostgreSQL 15 |
| Build | Maven |
| Conteneurisation | Docker / Docker Compose |
| Tests | JUnit 5, Mockito, Spring Boot Test |
| *(Futur)* Microservice | Python 3.x (FastAPI / Flask) |

---

## 📄 Licence

Ce projet est distribué sous licence **MIT**. Voir le fichier `LICENSE` pour plus de détails.