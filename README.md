# Plateforme Académique Intelligente — Prototypage

Ce dossier contient un **prototype HTML statique** (maquettes intégrées) d’une plateforme académique intelligente et collaborative.

## Vision (février 2026)

Une plateforme tout-en-un qui accompagne l’étudiant sur 3 axes interconnectés :

- **Organisation** : calendrier unifié, tâches, rappels, anticipation des échéances.
- **Apprentissage** : génération IA (fiches, quiz, podcasts, exercices) à partir de cours et documents.
- **Collaboration** : sessions de révision, quiz multi-joueurs, partage de ressources, réputation.

## Démarrage rapide (prototypes)

Ouvrir la page d’index :

- `index.html` (hub de navigation + parcours rapide)

### Lancer en local (recommandé)

Pour éviter les soucis de chemins (ex: dossier `login page/` avec espace), utilisez un petit serveur HTTP :

```bash
cd "maquette retenu/prototypage"
python3 -m http.server 8000
```

Puis ouvrir :

- http://localhost:8000/index.html

> Les écrans ajoutent une mini barre de navigation “Index prototypes” en haut de page pour naviguer rapidement.

## Structure du dossier

- `index.html` : page d’entrée “essai de prototypage” (cartes + captures + parcours)
- `*/code.html` : un écran prototype par variante
- `*/screen.png` : capture d’aperçu associée à chaque écran
- `doc/` : cahiers de référence (fonctionnalités + stack)

## Écrans disponibles

- `login page/code.html` — Authentification (porte d’entrée du parcours)
- `student_dashboard_home_variant_1/code.html` — Dashboard étudiant (tâches, stats, widgets)
- `student_profile_page_variant_2/code.html` — Profil étudiant (infos + cours)
- `calendar_weekly_view_variant_1/code.html` — Calendrier (vue semaine)
- `grade_management_interface_variant_1/code.html` — Notes & moyennes (table + filtres)
- `interactive_quiz_interface_variant_2/code.html` — Quiz interactif (progression, timer, score)
- `ai_content_generation_variant_1/code.html` — Hub génération IA (upload/paste)
- `ai_generation_results_variant_2/code.html` — Résultat IA (vue document / modes d’étude)

## Parcours utilisateur (exemple)

Scénario cible (résumé) :

1. L’étudiant se connecte et renseigne son profil.
2. Il centralise ses cours/tâches dans le calendrier et reçoit des notifications.
3. Il uploade des cours : l’IA extrait + génère fiche/quiz/podcast.
4. Les notes alimentent la moyenne et déclenchent des alertes (early warning).
5. En cas de difficulté, il rejoint une session collaborative ou trouve un tuteur via matching.

## Modules fonctionnels (cahier des fonctionnalités)

1. **Identité & contexte académique** (auth, profil, emploi du temps)
2. **Organisation intelligente** (tâches, calendrier unifié, notifications)
3. **Suivi de performance** (notes, calcul moyenne, alertes, dashboards)
4. **Apprentissage assisté par IA** (fiches, quiz, podcasts, extraction doc)
5. **Planification intelligente** (suggestions, planning de révision)
6. **Collaboration & social learning** (matching, sessions, ressources, quiz live, réputation)

## Priorisation (MVP vs suite)

**MVP essentiel** (valeur immédiate) : auth/profil, calendrier unifié, tâches (manuel + extraction basique), notes+moyennes, early warning, génération IA basique (fiches+quiz), sessions collaboratives, notifications basiques.

**Différenciateurs clés** : matching intelligent, planning adaptatif, quiz temps réel type Kahoot, bibliothèque collaborative, podcast IA.

## Architecture cible (cahier stack technique)

Recommandation : **microservices hybride**

- **Frontend** : React + Vite, Tailwind/MUI, Redux/Zustand, Socket.io-client
- **Backend Core** : Laravel 11 (API REST, auth Sanctum, logique métier)
- **Service IA** : Python + FastAPI (génération, extraction PDF, OCR, TTS)
- **Temps réel** : Node.js + Express + Socket.io (rooms quiz/sessions/chat/whiteboard), Redis possible pour scaling

---

## Architecture de la Base de Données

### Schéma relationnel (Backend Laravel - PostgreSQL/MySQL)

#### Table `users` (Étudiants)

| Champ                 | Type                | Description                         |
| --------------------- | ------------------- | ----------------------------------- |
| `id`                  | BIGINT PK           | Identifiant unique                  |
| `email`               | VARCHAR(255) UNIQUE | Email de connexion                  |
| `password`            | VARCHAR(255)        | Hash du mot de passe                |
| `nom`                 | VARCHAR(100)        | Nom de famille                      |
| `prenom`              | VARCHAR(100)        | Prénom                              |
| `universite`          | VARCHAR(255)        | Nom de l'université                 |
| `filiere`             | VARCHAR(255)        | Filière d'études (ex: Informatique) |
| `niveau`              | ENUM                | L1, L2, L3, M1, M2, Doctorat        |
| `objectif_moyenne`    | DECIMAL(4,2)        | Moyenne visée (ex: 13.50)           |
| `style_apprentissage` | ENUM                | visuel, auditif, kinesthésique      |
| `avatar_url`          | VARCHAR(500)        | URL photo de profil                 |
| `xp_total`            | INT DEFAULT 0       | Points d'expérience cumulés         |
| `niveau_gamification` | INT DEFAULT 1       | Niveau actuel (gamification)        |
| `created_at`          | TIMESTAMP           | Date de création                    |
| `updated_at`          | TIMESTAMP           | Dernière modification               |

#### Table `matieres` (Matières suivies)

| Champ             | Type              | Description                              |
| ----------------- | ----------------- | ---------------------------------------- |
| `id`              | BIGINT PK         | Identifiant unique                       |
| `user_id`         | BIGINT FK → users | Étudiant concerné                        |
| `nom`             | VARCHAR(255)      | Nom de la matière (ex: Algèbre Linéaire) |
| `code`            | VARCHAR(50)       | Code matière (ex: MATH-201)              |
| `coefficient`     | DECIMAL(4,2)      | Coefficient de la matière                |
| `niveau_maitrise` | INT(1-5)          | Auto-évaluation de maîtrise (★)          |
| `professeur`      | VARCHAR(255)      | Nom du professeur                        |
| `couleur`         | VARCHAR(7)        | Code couleur hex (#1337ec)               |
| `created_at`      | TIMESTAMP         | Date d'ajout                             |

#### Table `emploi_temps` (Cours planifiés)

| Champ          | Type                 | Description                                     |
| -------------- | -------------------- | ----------------------------------------------- |
| `id`           | BIGINT PK            | Identifiant unique                              |
| `user_id`      | BIGINT FK → users    | Étudiant concerné                               |
| `matiere_id`   | BIGINT FK → matieres | Matière du cours                                |
| `jour_semaine` | ENUM                 | lundi, mardi, mercredi, jeudi, vendredi, samedi |
| `heure_debut`  | TIME                 | Heure de début (ex: 09:00)                      |
| `heure_fin`    | TIME                 | Heure de fin (ex: 11:00)                        |
| `salle`        | VARCHAR(100)         | Salle/localisation                              |
| `type_cours`   | ENUM                 | CM, TD, TP                                      |
| `recurrence`   | BOOLEAN              | Répété chaque semaine                           |
| `created_at`   | TIMESTAMP            | Date d'ajout                                    |

#### Table `taches` (Devoirs & tâches)

| Champ          | Type                 | Description                               |
| -------------- | -------------------- | ----------------------------------------- | ---------------------------- |
| `id`           | BIGINT PK            | Identifiant unique                        |
| `user_id`      | BIGINT FK → users    | Étudiant concerné                         |
| `matiere_id`   | BIGINT FK → matieres | Matière associée                          |
| `titre`        | VARCHAR(255)         | Titre de la tâche                         |
| `description`  | TEXT                 | Description détaillée                     |
| `type`         | ENUM                 | devoir, revision, examen, projet, lecture |
| `date_limite`  | DATETIME             | Date et heure limite                      |
| `priorite`     | ENUM                 | haute, moyenne, basse                     |
| `temps_estime` | INT                  | Temps estimé (en minutes)                 |
| `statut`       | ENUM                 | a_faire, en_cours, termine                |
| `origine`      | ENUM                 | manuelle, extraction_ia                   | Comment la tâche a été créée |
| `completed_at` | TIMESTAMP NULL       | Date de complétion                        |
| `created_at`   | TIMESTAMP            | Date de création                          |

#### Table `notes` (Évaluations & résultats)

| Champ             | Type                 | Description                                            |
| ----------------- | -------------------- | ------------------------------------------------------ |
| `id`              | BIGINT PK            | Identifiant unique                                     |
| `user_id`         | BIGINT FK → users    | Étudiant concerné                                      |
| `matiere_id`      | BIGINT FK → matieres | Matière évaluée                                        |
| `type_evaluation` | ENUM                 | devoir, controle_continu, examen_partiel, examen_final |
| `intitule`        | VARCHAR(255)         | Nom de l'évaluation (ex: DS Chapitre 3)                |
| `note_obtenue`    | DECIMAL(5,2)         | Note obtenue (ex: 14.50)                               |
| `note_maximale`   | DECIMAL(5,2)         | Barème (ex: 20.00)                                     |
| `coefficient`     | DECIMAL(4,2)         | Coefficient de l'évaluation                            |
| `date_evaluation` | DATE                 | Date de l'évaluation                                   |
| `commentaire`     | TEXT NULL            | Commentaire du professeur                              |
| `created_at`      | TIMESTAMP            | Date d'enregistrement                                  |

#### Table `alertes` (Early Warning System)

| Champ               | Type                  | Description                                     |
| ------------------- | --------------------- | ----------------------------------------------- |
| `id`                | BIGINT PK             | Identifiant unique                              |
| `user_id`           | BIGINT FK → users     | Étudiant concerné                               |
| `matiere_id`        | BIGINT FK → matieres  | Matière en difficulté                           |
| `type_alerte`       | ENUM                  | moyenne_faible, tendance_baisse, echec_imminent |
| `niveau_severite`   | ENUM                  | jaune, orange, rouge                            |
| `moyenne_actuelle`  | DECIMAL(4,2)          | Moyenne actuelle de la matière                  |
| `message`           | TEXT                  | Message d'alerte personnalisé                   |
| `actions_suggerees` | JSON                  | Liste d'actions recommandées                    |
| `is_read`           | BOOLEAN DEFAULT false | Alerte lue ou non                               |
| `created_at`        | TIMESTAMP             | Date de déclenchement                           |

#### Table `sessions_collaboratives` (Sessions de révision)

| Champ              | Type                 | Description                            |
| ------------------ | -------------------- | -------------------------------------- |
| `id`               | BIGINT PK            | Identifiant unique                     |
| `organisateur_id`  | BIGINT FK → users    | Créateur de la session                 |
| `matiere_id`       | BIGINT FK → matieres | Matière concernée                      |
| `titre`            | VARCHAR(255)         | Titre de la session                    |
| `description`      | TEXT                 | Description détaillée                  |
| `chapitre`         | VARCHAR(255)         | Chapitre ou thème précis               |
| `date_debut`       | DATETIME             | Date et heure de début                 |
| `duree_minutes`    | INT                  | Durée estimée                          |
| `format`           | ENUM                 | en_ligne, presentiel                   |
| `lien_visio`       | VARCHAR(500) NULL    | Lien Zoom/Meet si en ligne             |
| `localisation`     | VARCHAR(255) NULL    | Lieu si présentiel                     |
| `max_participants` | INT                  | Nombre max de participants             |
| `niveau_requis`    | ENUM                 | debutant, intermediaire, avance        |
| `statut`           | ENUM                 | planifiee, en_cours, terminee, annulee |
| `created_at`       | TIMESTAMP            | Date de création                       |

#### Table `inscriptions_sessions` (Participants sessions)

| Champ                  | Type                                | Description                  |
| ---------------------- | ----------------------------------- | ---------------------------- |
| `id`                   | BIGINT PK                           | Identifiant unique           |
| `session_id`           | BIGINT FK → sessions_collaboratives | Session concernée            |
| `user_id`              | BIGINT FK → users                   | Participant inscrit          |
| `statut_participation` | ENUM                                | inscrit, presente, absente   |
| `note_donnee`          | INT(1-5) NULL                       | Note donnée à la session (★) |
| `commentaire`          | TEXT NULL                           | Feedback de l'étudiant       |
| `created_at`           | TIMESTAMP                           | Date d'inscription           |

#### Table `ressources_partagees` (Bibliothèque collaborative)

| Champ                | Type                   | Description                                             |
| -------------------- | ---------------------- | ------------------------------------------------------- |
| `id`                 | BIGINT PK              | Identifiant unique                                      |
| `user_id`            | BIGINT FK → users      | Créateur de la ressource                                |
| `matiere_id`         | BIGINT FK → matieres   | Matière concernée                                       |
| `titre`              | VARCHAR(255)           | Titre de la ressource                                   |
| `type_ressource`     | ENUM                   | fiche_revision, exercice, sujet_examen, schema, mindmap |
| `fichier_url`        | VARCHAR(500)           | URL du fichier stocké                                   |
| `tags`               | JSON                   | Mots-clés (array)                                       |
| `universite`         | VARCHAR(255)           | Université d'origine                                    |
| `professeur`         | VARCHAR(255) NULL      | Professeur associé                                      |
| `note_moyenne`       | DECIMAL(3,2) DEFAULT 0 | Note communautaire (★)                                  |
| `nb_telechargements` | INT DEFAULT 0          | Compteur de téléchargements                             |
| `is_validated`       | BOOLEAN DEFAULT false  | Validé par modération                                   |
| `created_at`         | TIMESTAMP              | Date d'upload                                           |

#### Table `contenus_ia` (Contenus générés par IA)

| Champ              | Type                 | Description                           |
| ------------------ | -------------------- | ------------------------------------- |
| `id`               | BIGINT PK            | Identifiant unique                    |
| `user_id`          | BIGINT FK → users    | Étudiant ayant généré                 |
| `matiere_id`       | BIGINT FK → matieres | Matière concernée                     |
| `type_contenu`     | ENUM                 | fiche_resume, quiz, podcast, exercice |
| `titre`            | VARCHAR(255)         | Titre du contenu                      |
| `contenu_json`     | JSON                 | Données structurées du contenu        |
| `fichier_url`      | VARCHAR(500) NULL    | URL PDF/audio si applicable           |
| `source_doc_url`   | VARCHAR(500) NULL    | Document source uploadé               |
| `temps_generation` | INT NULL             | Temps de génération (secondes)        |
| `created_at`       | TIMESTAMP            | Date de génération                    |

#### Table `quiz_questions` (Banque de questions)

| Champ              | Type                    | Description                     |
| ------------------ | ----------------------- | ------------------------------- |
| `id`               | BIGINT PK               | Identifiant unique              |
| `contenu_ia_id`    | BIGINT FK → contenus_ia | Quiz parent                     |
| `matiere_id`       | BIGINT FK → matieres    | Matière concernée               |
| `question`         | TEXT                    | Énoncé de la question           |
| `type_question`    | ENUM                    | qcm, vrai_faux, texte_libre     |
| `options`          | JSON                    | Options de réponse [A, B, C, D] |
| `reponse_correcte` | VARCHAR(1)              | Lettre de la bonne réponse      |
| `explication`      | TEXT                    | Explication de la réponse       |
| `difficulte`       | ENUM                    | facile, moyen, difficile        |
| `created_at`       | TIMESTAMP               | Date de création                |

#### Table `quiz_resultats` (Performances aux quiz)

| Champ             | Type                    | Description                      |
| ----------------- | ----------------------- | -------------------------------- |
| `id`              | BIGINT PK               | Identifiant unique               |
| `user_id`         | BIGINT FK → users       | Étudiant ayant passé le quiz     |
| `contenu_ia_id`   | BIGINT FK → contenus_ia | Quiz passé                       |
| `score`           | INT                     | Nombre de bonnes réponses        |
| `total_questions` | INT                     | Nombre total de questions        |
| `temps_passe`     | INT                     | Temps passé (secondes)           |
| `reponses_json`   | JSON                    | Détail des réponses données      |
| `notions_faibles` | JSON                    | Notions mal maîtrisées détectées |
| `created_at`      | TIMESTAMP               | Date de passage                  |

#### Table `badges` (Système de gamification)

| Champ            | Type               | Description                   |
| ---------------- | ------------------ | ----------------------------- |
| `id`             | BIGINT PK          | Identifiant unique            |
| `code`           | VARCHAR(50) UNIQUE | Code du badge (ex: STREAK_7J) |
| `nom`            | VARCHAR(255)       | Nom du badge                  |
| `description`    | TEXT               | Description du badge          |
| `icone_url`      | VARCHAR(500)       | URL de l'icône                |
| `xp_requis`      | INT NULL           | XP nécessaire pour débloquer  |
| `condition_json` | JSON               | Conditions de déblocage       |
| `created_at`     | TIMESTAMP          | Date de création              |

#### Table `badges_utilisateurs` (Badges obtenus)

| Champ         | Type               | Description           |
| ------------- | ------------------ | --------------------- |
| `id`          | BIGINT PK          | Identifiant unique    |
| `user_id`     | BIGINT FK → users  | Étudiant ayant obtenu |
| `badge_id`    | BIGINT FK → badges | Badge obtenu          |
| `unlocked_at` | TIMESTAMP          | Date de déblocage     |

#### Table `notifications` (Système de notifications)

| Champ         | Type                  | Description                                            |
| ------------- | --------------------- | ------------------------------------------------------ |
| `id`          | BIGINT PK             | Identifiant unique                                     |
| `user_id`     | BIGINT FK → users     | Destinataire                                           |
| `type`        | ENUM                  | rappel_cours, rappel_tache, alerte, suggestion, social |
| `titre`       | VARCHAR(255)          | Titre de la notification                               |
| `message`     | TEXT                  | Message détaillé                                       |
| `lien_action` | VARCHAR(500) NULL     | URL de l'action liée                                   |
| `is_read`     | BOOLEAN DEFAULT false | Lue ou non                                             |
| `created_at`  | TIMESTAMP             | Date d'envoi                                           |

### Relations clés

- **users** ↔ **matieres** (1:N) : Un étudiant suit plusieurs matières
- **users** ↔ **taches** (1:N) : Un étudiant a plusieurs tâches
- **users** ↔ **notes** (1:N) : Un étudiant a plusieurs notes
- **matieres** ↔ **notes** (1:N) : Une matière contient plusieurs notes
- **users** ↔ **sessions_collaboratives** (1:N) : Un étudiant organise plusieurs sessions
- **sessions_collaboratives** ↔ **inscriptions_sessions** (1:N) : Une session a plusieurs participants
- **users** ↔ **contenus_ia** (1:N) : Un étudiant génère plusieurs contenus IA
- **contenus_ia** ↔ **quiz_questions** (1:N) : Un quiz contient plusieurs questions

---

## Architecture des APIs

### 🔧 Backend Laravel (API Core)

**Base URL** : `https://api.plateforme-academique.com/api/v1`

**Authentification** : Bearer Token (Laravel Sanctum)

#### Module Authentification

```
POST   /auth/register          # Inscription étudiant
POST   /auth/login             # Connexion (retourne token)
POST   /auth/logout            # Déconnexion
GET    /auth/me                # Profil utilisateur connecté
PUT    /auth/profile           # Mise à jour profil
```

**Body exemple (register)** :

```json
{
  "email": "sophie@etudiant.fr",
  "password": "SecurePass123!",
  "nom": "Martin",
  "prenom": "Sophie",
  "universite": "IFRI",
  "filiere": "Informatique",
  "niveau": "L2",
  "objectif_moyenne": 13.5
}
```

**Response** :

```json
{
  "success": true,
  "data": {
    "user": {...},
    "token": "12|randomtoken..."
  }
}
```

#### Module Matières

```
GET    /matieres               # Liste des matières de l'étudiant
POST   /matieres               # Ajouter une matière
GET    /matieres/{id}          # Détails d'une matière
PUT    /matieres/{id}          # Modifier une matière
DELETE /matieres/{id}          # Supprimer une matière
```

**Body exemple (POST)** :

```json
{
  "nom": "Algorithmique Avancée",
  "code": "INFO-301",
  "coefficient": 4.0,
  "niveau_maitrise": 3,
  "professeur": "Dr. Dupont",
  "couleur": "#1337ec"
}
```

#### Module Emploi du temps

```
GET    /emploi-temps           # Emploi du temps de l'étudiant
POST   /emploi-temps/import    # Import ICS/CSV
POST   /emploi-temps/cours     # Ajouter un cours
PUT    /emploi-temps/cours/{id}
DELETE /emploi-temps/cours/{id}
GET    /emploi-temps/creneaux-libres  # Détection créneaux libres
```

**Body exemple (ajouter cours)** :

```json
{
  "matiere_id": 5,
  "jour_semaine": "lundi",
  "heure_debut": "09:00",
  "heure_fin": "11:00",
  "salle": "Amphi A",
  "type_cours": "CM",
  "recurrence": true
}
```

#### Module Tâches

```
GET    /taches                 # Liste des tâches (filtrable)
POST   /taches                 # Créer une tâche
GET    /taches/{id}
PUT    /taches/{id}
DELETE /taches/{id}
PATCH  /taches/{id}/complete   # Marquer comme terminée
POST   /taches/extract         # Extraction auto depuis document
```

**Body exemple (POST)** :

```json
{
  "matiere_id": 5,
  "titre": "Rendre TP Tri Fusion",
  "description": "Implémenter l'algorithme en Python",
  "type": "devoir",
  "date_limite": "2026-02-20 23:59:00",
  "priorite": "haute",
  "temps_estime": 120
}
```

**Body extraction (POST /taches/extract)** :

```json
{
  "document_url": "https://storage.../cours_reseaux.pdf",
  "matiere_id": 3
}
```

**Response extraction** :

```json
{
  "success": true,
  "taches_extraites": [
    {
      "titre": "TP Configuration Routeur",
      "date_limite": "2026-02-25",
      "type": "devoir",
      "created": true
    }
  ]
}
```

#### Module Notes & Moyennes

```
GET    /notes                  # Liste des notes
POST   /notes                  # Ajouter une note
GET    /notes/{id}
PUT    /notes/{id}
DELETE /notes/{id}
POST   /notes/import-ocr       # Import note via OCR
GET    /moyennes               # Calcul moyennes (par matière + générale)
GET    /moyennes/prevision     # Simulation prévisionnelle
```

**Body exemple (POST note)** :

```json
{
  "matiere_id": 5,
  "type_evaluation": "controle_continu",
  "intitule": "DS Chapitre 3",
  "note_obtenue": 14.5,
  "note_maximale": 20.0,
  "coefficient": 2.0,
  "date_evaluation": "2026-02-10"
}
```

**Response moyennes** :

```json
{
  "moyenne_generale": 13.25,
  "matieres": [
    {
      "id": 5,
      "nom": "Algorithmique",
      "moyenne": 15.5,
      "coefficient": 4.0,
      "nb_evaluations": 3
    },
    {...}
  ]
}
```

#### Module Alertes (Early Warning)

```
GET    /alertes                # Alertes actives de l'étudiant
GET    /alertes/{id}
PATCH  /alertes/{id}/read      # Marquer comme lue
POST   /alertes/check          # Forcer vérification (cron)
```

**Response exemple** :

```json
{
  "alertes": [
    {
      "id": 12,
      "matiere": "Réseaux Informatiques",
      "type_alerte": "moyenne_faible",
      "niveau_severite": "rouge",
      "moyenne_actuelle": 8.5,
      "message": "Risque élevé d'échec détecté",
      "actions_suggerees": [
        "Trouve un tuteur dans la communauté",
        "Augmente ton temps de révision de 2h/semaine"
      ]
    }
  ]
}
```

#### Module Sessions Collaboratives

```
GET    /sessions               # Sessions disponibles (filtrable)
POST   /sessions               # Créer une session
GET    /sessions/{id}
PUT    /sessions/{id}
DELETE /sessions/{id}
POST   /sessions/{id}/join     # S'inscrire à une session
POST   /sessions/{id}/leave    # Se désinscrire
POST   /sessions/{id}/rate     # Noter la session (post-event)
GET    /sessions/mes-sessions  # Sessions où je suis inscrit
GET    /sessions/mes-organisations # Sessions que j'ai créées
```

**Body exemple (créer session)** :

```json
{
  "matiere_id": 3,
  "titre": "Révision TCP/IP & Routage",
  "description": "Session intensive sur les protocoles",
  "chapitre": "Chapitre 5 - Couche Réseau",
  "date_debut": "2026-02-22 14:00:00",
  "duree_minutes": 120,
  "format": "en_ligne",
  "lien_visio": "https://meet.google.com/abc-def-ghi",
  "max_participants": 8,
  "niveau_requis": "intermediaire"
}
```

#### Module Ressources Partagées

```
GET    /ressources             # Bibliothèque (filtrable, searchable)
POST   /ressources             # Upload nouvelle ressource
GET    /ressources/{id}
DELETE /ressources/{id}        # Supprimer (si owner)
POST   /ressources/{id}/rate   # Noter la ressource
POST   /ressources/{id}/download # Télécharger (incrémente compteur)
GET    /ressources/top-rated   # Ressources les mieux notées
```

**Body exemple (upload)** :

```json
{
  "matiere_id": 5,
  "titre": "Fiche Tri Rapide & Complexité",
  "type_ressource": "fiche_revision",
  "fichier": "<multipart/form-data>",
  "tags": ["algorithme", "tri", "complexite"],
  "professeur": "Dr. Dupont"
}
```

#### Module Gamification

```
GET    /badges                 # Liste tous les badges disponibles
GET    /badges/mes-badges      # Badges obtenus par l'étudiant
GET    /classements            # Classements globaux/par matière
GET    /xp/historique          # Historique gains XP
```

#### Module Notifications

```
GET    /notifications          # Liste notifications (paginé)
PATCH  /notifications/{id}/read
PATCH  /notifications/mark-all-read
```

---

### 🐍 Service IA Python (FastAPI)

**Base URL** : `https://ia.plateforme-academique.com/api/v1`

**Authentification** : API Key (header `X-API-Key`)

#### Génération de Contenus

```
POST   /ai/generate-summary    # Générer fiche de révision
POST   /ai/generate-quiz       # Générer QCM
POST   /ai/generate-podcast    # Générer podcast audio (TTS)
POST   /ai/generate-exercises  # Générer exercices (matières scientifiques)
```

**Body exemple (generate-summary)** :

```json
{
  "contenu_source": "Texte du cours ou URL PDF",
  "matiere": "Réseaux Informatiques",
  "niveau_detail": "moyen",
  "format_sortie": "pdf"
}
```

**Response** :

```json
{
  "success": true,
  "summary": {
    "titre": "Protocoles TCP/IP",
    "contenu_html": "<h2>Points clés</h2>...",
    "fichier_pdf_url": "https://storage.../fiche_123.pdf",
    "temps_generation": 8.5
  }
}
```

**Body exemple (generate-quiz)** :

```json
{
  "contenu_source": "Texte du cours",
  "matiere": "Algorithmique",
  "nb_questions": 15,
  "difficulte": "moyen"
}
```

**Response** :

```json
{
  "success": true,
  "quiz": {
    "titre": "QCM Algorithmique - Tri",
    "questions": [
      {
        "question": "Quelle est la complexité moyenne du tri rapide ?",
        "options": ["O(n)", "O(n log n)", "O(n²)", "O(log n)"],
        "reponse_correcte": "B",
        "explication": "Le tri rapide a une complexité...",
        "difficulte": "moyen"
      },
      {...}
    ]
  }
}
```

#### Extraction de Documents

```
POST   /ai/extract-from-pdf    # Extraction texte + structure depuis PDF
POST   /ai/extract-from-image  # OCR depuis image (notes manuscrites)
```

**Body exemple (extract-from-pdf)** :

```json
{
  "pdf_url": "https://storage.../cours.pdf",
  "extract_tasks": true,
  "extract_dates": true,
  "extract_structure": true
}
```

**Response** :

```json
{
  "success": true,
  "extraction": {
    "texte_complet": "Contenu extrait...",
    "plan_cours": ["Introduction", "Chapitre 1", ...],
    "devoirs_detectes": [
      {
        "titre": "TP Configuration Routeur",
        "date_limite": "2026-02-25",
        "description": "..."
      }
    ],
    "dates_importantes": ["2026-03-10: Examen Final"],
    "references": ["RFC 791", ...]
  }
}
```

**Body exemple (extract-from-image - OCR)** :

```json
{
  "image_url": "https://storage.../copie_notee.jpg",
  "type_extraction": "note"
}
```

**Response** :

```json
{
  "success": true,
  "ocr_result": {
    "texte_detecte": "14.5/20 Très bon travail",
    "note_detectee": 14.5,
    "note_maximale": 20.0,
    "confidence": 0.95
  }
}
```

#### Génération Audio (Podcasts)

```
POST   /ai/generate-podcast
```

**Body exemple** :

```json
{
  "texte_source": "Contenu du cours à convertir",
  "voix": "fr-FR-DeniseNeural",
  "vitesse": 1.0,
  "titre": "Cours Réseaux - Chapitre 5"
}
```

**Response** :

```json
{
  "success": true,
  "podcast": {
    "titre": "Cours Réseaux - Chapitre 5",
    "duree_secondes": 420,
    "fichier_mp3_url": "https://storage.../podcast_123.mp3",
    "temps_generation": 12.3
  }
}
```

---

### ⚡ Service Temps Réel Node.js (Socket.io)

**Base URL (WebSocket)** : `wss://realtime.plateforme-academique.com`

**Authentification** : Token JWT passé lors de la connexion

#### Events Socket.io (Client → Server)

##### Quiz Collaboratifs

```javascript
// Rejoindre un quiz
socket.emit("join-quiz", {
  quiz_id: 42,
  user_id: 123,
  token: "jwt_token",
});

// Envoyer une réponse
socket.emit("answer-question", {
  quiz_id: 42,
  question_id: 5,
  reponse: "B",
  temps_reponse: 8.5,
});

// Quitter le quiz
socket.emit("leave-quiz", { quiz_id: 42 });
```

##### Sessions de Révision

```javascript
// Rejoindre une session
socket.emit("join-session", {
  session_id: 18,
  user_id: 123,
  token: "jwt_token",
});

// Envoyer un message chat
socket.emit("send-message", {
  session_id: 18,
  message: "Quelqu'un peut expliquer TCP ?",
  timestamp: Date.now(),
});

// Dessiner sur tableau blanc
socket.emit("whiteboard-draw", {
  session_id: 18,
  action: "line",
  coordinates: { x1: 10, y1: 20, x2: 50, y2: 60 },
  color: "#000000",
  width: 2,
});
```

#### Events Socket.io (Server → Client)

##### Quiz

```javascript
// Question suivante diffusée à tous
socket.on("quiz-update", (data) => {
  // data = { question_index: 3, question: {...}, time_limit: 20 }
});

// Classement en temps réel
socket.on("leaderboard-update", (data) => {
  // data = [{ user: 'Sophie', score: 850 }, ...]
});

// Quiz terminé
socket.on("quiz-ended", (data) => {
  // data = { final_scores: [...], winner: {...} }
});
```

##### Sessions

```javascript
// Nouveau message reçu
socket.on("new-message", (data) => {
  // data = { user: 'Marc', message: '...', timestamp: ... }
});

// Mise à jour tableau blanc
socket.on("whiteboard-update", (data) => {
  // data = { action: 'line', coordinates: {...}, ... }
});

// Participant rejoint/quitte
socket.on("participant-update", (data) => {
  // data = { action: 'joined', user: {...} }
});
```

##### Notifications Push

```javascript
// Notification temps réel
socket.on("notification", (data) => {
  // data = { type: 'alerte', titre: '...', message: '...' }
});
```

---

## API REST vs WebSocket - Quand utiliser quoi ?

| Fonctionnalité     | Protocole             | Justification                  |
| ------------------ | --------------------- | ------------------------------ |
| Authentification   | REST (Laravel)        | Opération ponctuelle           |
| CRUD Tâches/Notes  | REST (Laravel)        | Opérations standard            |
| Génération IA      | REST (Python FastAPI) | Processus async long           |
| Quiz collaboratifs | WebSocket (Node.js)   | Synchronisation temps réel     |
| Chat sessions      | WebSocket (Node.js)   | Bidirectionnel instantané      |
| Tableau blanc      | WebSocket (Node.js)   | Dessin collaboratif temps réel |
| Notifications push | WebSocket (Node.js)   | Push instantané                |

---

## Références

- `doc/Cahier_Fonctionnalites_Unifie.docx`
- `doc/Cahier_Stack_Technique.docx`

> Note : un dossier `doc/_extracted/` peut exister si on a extrait le texte des `.docx` pour analyse/README.
