# AgenticForms — infra

Ce dépôt regroupe le **Docker Compose**, les variables d’environnement d’exemple et la CI d’infra pour **AgenticForms**, un SaaS de formulaires (type Fillout) développé dans le cadre du titre CDA (RNCP 37873).

L’appli tourne en **trois dépôts Git séparés** :

| Dépôt | Rôle |
|-------|------|
| **agenticform-infra** (ici) | Compose, `.env.example`, orchestration locale |
| **agenticform-backend** | API Spring Boot (Java 21) |
| **agenticform-frontend** | SPA React / Vite / TypeScript |

Sur ta machine, `backend/` et `frontend/` sont en général des clones côte à côte (ignorés par le `.gitignore` de l’infra). Un clone **infra seul** ne suffit pas pour builder : il faut aussi les deux autres repos.

---

## Stack locale

- Frontend : React + Vite → servi par Nginx (conteneur)
- Backend : Spring Boot 3 / Java 21
- MySQL 8 (données métier) + MongoDB 7 (messages Contact)
- Mailpit (SMTP de dev, UI sur le port 8025)

Ports (bindés sur `127.0.0.1`) :

| Service | URL |
|---------|-----|
| Frontend | http://localhost:5173 |
| Backend / API | http://localhost:8080 |
| Mailpit | http://localhost:8025 |

---

## Démarrage

1. Cloner les **trois** dépôts au bon endroit (infra avec `backend/` et `frontend/` à côté, comme attendu par `docker-compose.yml`).
2. Copier `.env.example` → `.env` et remplacer tous les `changeme_*` (surtout `JWT_SECRET`, ≥ 32 caractères).
3. Lancer :

```bash
docker compose up --build -d
```

4. Attendre que les healthchecks passent, puis ouvrir http://localhost:5173.

Pour rebuild uniquement le frontend après un changement UI :

```bash
docker compose build frontend --no-cache && docker compose up -d frontend
```

---

## Variables importantes

Tout passe par `.env` (jamais committer ce fichier).

- **Sécurité** : `JWT_SECRET`, `COOKIE_FORCE_SECURE` (`false` en HTTP local, `true` derrière HTTPS)
- **CORS** : `CORS_ALLOWED_ORIGINS` (ex. `http://localhost:5173`)
- **Build frontend** : `VITE_BACKEND_URL`, `VITE_API_BASE_URL`, `VITE_RECAPTCHA_SITE_KEY`
- **OAuth / Stripe / Calendly** : optionnels ; laisser vides si tu ne les utilises pas en local
- **SMTP** : Mailpit en local (`SPRING_MAIL_HOST=mailpit`)

Le détail commenté est dans `.env.example`.

---

## CI

Le workflow GitHub Actions de ce dépôt vérifie surtout que `docker compose config` est valide. Les tests Java et le lint/build frontend sont dans les CI des dépôts backend et frontend.

---

## Périmètre MVP

AgenticForms livre un parcours complet : inscription / login, builder, publication `/t/{id}`, soumissions, résultats, admin, contact (Mongo).

Certaines briques sont volontairement hors démo (Agent IA, Analytics avancés, workflows, etc.) — affichées « Bientôt » dans l’UI.

---

## Auteur

Projet de formation — Ilyaas Abdoul Azis · NEXA Digital School · 2025–2026.
