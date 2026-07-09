# AgenticForm Infrastructure

## Architecture
- **Backend**: Spring Boot 3 / Java 21 (API REST)
- **Frontend**: React / Vite (Nginx)
- **Data**: MySQL 8.0 (Relationnel) & MongoDB 7.0 (NoSQL)

## Sécurité & Conformité
- **Durcissement**: Images conteneurisées avec utilisateur non-root (`spring`/`nginx-unprivileged`).
- **Isolation**: Variables d'environnement standardisées et secrets ignorés par Git.
- **Robustesse**: Healthchecks configurés pour chaque service.

## Démarrage rapide
1. Copier `.env.example` en `.env` et configurer les secrets.
2. `docker-compose up --build`