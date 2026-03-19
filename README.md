# 📈 Trading-Crypto — Backend Spring Boot

> Backend d'une application de trading de cryptomonnaies, conçu avec les contraintes de sécurité et de fiabilité du secteur **FinTech**.

---

## 📋 Sommaire

- [À propos du projet](#-à-propos-du-projet)
- [Architecture](#-architecture)
- [Stack technique](#-stack-technique)
- [Fonctionnalités](#-fonctionnalités)
- [Prérequis](#-prérequis)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Lancement](#-lancement)
- [API — Endpoints principaux](#-api--endpoints-principaux)
- [Choix techniques & bonnes pratiques](#-choix-techniques--bonnes-pratiques)
- [Roadmap](#-roadmap)
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

## ✨ Fonctionnalités

- 🔐 **Authentification & Autorisation** — Inscription, connexion, tokens JWT, refresh token.
- 💰 **Gestion du portefeuille** — Consultation des soldes par devise (BTC, ETH, USDT…).
- 📥 **Dépôts** — Crédit d'un compte utilisateur en cryptomonnaie ou en monnaie fiat.
- 🔄 **Échanges (swaps)** — Conversion atomique d'une devise vers une autre au prix du marché.
- 📤 **Retraits** — Débit sécurisé avec vérification de solde et de limites.
- 📜 **Historique des transactions** — Consultation paginée de toutes les opérations.
- 📊 *(Futur)* **Prix en temps réel** — Intégration d'un microservice Python de scraping de cours.

---

## ✅ Prérequis

- **Java 17+** (JDK)
- **Maven 3.8+**
- **PostgreSQL 15+** (ou Docker)
- *(Optionnel)* **Docker & Docker Compose**

---

## 🚀 Installation

```bash
# 1. Cloner le dépôt
git clone https://github.com/Armand00608/Trading-Crypto.git
cd Trading-Crypto

# 2. Démarrer PostgreSQL via Docker (optionnel)
docker-compose up -d db

# 3. Compiler le projet
mvn clean install -DskipTests
```

---

## ⚙️ Configuration

Copiez le fichier d'exemple et renseignez vos variables locales :

```bash
cp src/main/resources/application-local.yml.example \
   src/main/resources/application-local.yml
```

Variables clés dans `application.yml` / `application-local.yml` :

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/trading_crypto
    username: ${DB_USER}
    password: ${DB_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: validate

jwt:
  secret: ${JWT_SECRET}
  expiration-ms: 86400000   # 24 h
```

> ⚠️ **Ne jamais committer** `application-local.yml` ni aucun fichier contenant des secrets — ils sont listés dans `.gitignore`.

---

## ▶️ Lancement

```bash
# Avec Maven
mvn spring-boot:run -Dspring-boot.run.profiles=local

# Ou avec le JAR compilé
java -jar target/trading-crypto-*.jar --spring.profiles.active=local
```

L'API est accessible par défaut sur : `http://localhost:8080`

---

## 📡 API — Endpoints principaux

| Méthode | Endpoint | Description | Auth |
|---|---|---|---|
| `POST` | `/api/auth/register` | Inscription d'un nouvel utilisateur | Non |
| `POST` | `/api/auth/login` | Connexion — retourne un JWT | Non |
| `GET` | `/api/wallet` | Soldes du portefeuille | JWT |
| `POST` | `/api/wallet/deposit` | Dépôt d'un montant | JWT |
| `POST` | `/api/wallet/withdraw` | Retrait d'un montant | JWT |
| `POST` | `/api/trade/swap` | Échange entre deux devises | JWT |
| `GET` | `/api/transactions` | Historique paginé | JWT |

---

## 🔍 Choix techniques & bonnes pratiques

### `BigDecimal` plutôt que `float` / `double`
Les montants financiers sont systématiquement représentés avec `BigDecimal` pour éviter les erreurs d'arrondi inhérentes aux types flottants IEEE-754 — une exigence non négociable en FinTech.

### Transactions atomiques (`@Transactional`)
Chaque opération d'échange (débit d'une devise + crédit d'une autre) est encapsulée dans une transaction JPA unique. En cas d'erreur, un rollback automatique garantit la cohérence des données.

### Schéma SQL optimisé (PostgreSQL)
- Types `NUMERIC(20, 8)` pour les soldes.
- Contraintes `CHECK` sur les montants positifs.
- Index sur les colonnes fréquemment filtrées (`user_id`, `created_at`).

### Sécurité
- Mots de passe hashés avec **BCrypt**.
- Tokens JWT signés (algorithme HS256) avec expiration courte.
- Validation systématique des entrées utilisateur (Bean Validation — `@Valid`).

---

## 🗺 Roadmap

- [x] Authentification JWT
- [x] CRUD portefeuille & transactions
- [x] Opérations dépôt / retrait / échange
- [ ] Microservice Python — scraping prix en temps réel (CoinGecko / Binance API)
- [ ] WebSocket — prix en streaming côté client
- [ ] Tests d'intégration complets (Testcontainers + PostgreSQL)
- [ ] Pipeline CI/CD (GitHub Actions)
- [ ] Documentation OpenAPI / Swagger UI

---

## 📄 Licence

Ce projet est distribué sous licence **MIT**. Voir le fichier `LICENSE` pour plus de détails.