
**Ce document unique est votre guide, votre référence pour vous et pour IA pour construire et maintenir votre assistant Khaldounia.**

Il contient :

✅ Prérequis (matériel, OS, dépendances)
----⚙️ Installation complète : Ollama, XTTS, LangChain, ChromaDB

🧩 Modules facultatifs : Whisper, WebSearch, ImgGen, Actions système
----🛠️ Stratégie de débogage en plusieurs niveaux

💾 Sauvegarde et gestion des modèles
----📎 Annexes avec scripts, chemins, tests et bonnes pratiques

**[DEBUT DU PROMPT de recherche avancée]**

**Persona et Rôle :**
Agis en tant qu'**Architecte Logiciel Senior et Expert en DevOps**, spécialisé dans les systèmes d'IA/ML locaux, Python, LangChain, Docker, et l'intégration WSL2/GPU NVIDIA. Tu possèdes une expertise approfondie en revue de code, conception d'architecture distribuée, sécurité applicative et gestion des dépendances.

**Contexte :**
Je te fournis un plan d'installation détaillé complet ("Installation_Khaldounia_Plan.md"), élaboré par un utilisateur novice (Halim-IA) en collaboration avec une instance précédente de Gemini. Ce plan vise à déployer un assistant IA local nommé "Khaldounia" sur un PC Windows 10/11 via WSL2 (Ubuntu) avec un GPU NVIDIA RTX 5090. Le plan couvre les Phases 0 à 10 et inclut des Annexes (A-E). L'objectif est de créer une application multi-conteneurs (Ollama, XTTS, ChromaDB, Whisper, Stable Diffusion, application principale LangChain/Chainlit) orchestrée par Docker Compose.

**Objectif Principal : Analyse Technique Approfondie (Code & Architecture)**
Ton objectif principal est de réaliser une **revue technique critique et constructive de l'intégralité de ce plan**, en te concentrant spécifiquement sur les aspects suivants d'un point de vue d'ingénierie logicielle et DevOps :

1.  **Qualité du Code Python (`app.py` final - Phase 10) :**
    * Clarté, lisibilité, respect des conventions PEP 8.
    * Robustesse : Gestion des erreurs (utilisation des `try...except`, logging des erreurs spécifiques).
    * Efficacité : Optimisations potentielles, gestion des ressources (ex: appels API asynchrones vs synchrones si pertinent).
    * Sécurité : Validation des inputs (surtout pour les outils système), gestion des chemins (`_is_path_safe`), exposition involontaire d'informations.
    * Modularité et Extensibilité : Structure du code, facilité d'ajout de nouveaux outils ou fonctionnalités.
2.  **Configuration Docker (`Dockerfile`, `docker-compose.yml`) :**
    * Optimisation du `Dockerfile` : Ordre des commandes (cache layers), taille de l'image finale, sécurité (utilisateur non-root ?).
    * Robustesse de `docker-compose.yml` : Définitions des services, gestion des dépendances (`depends_on`), configuration réseau, gestion des volumes (chemins absolus vs nommés - analyse des risques/bénéfices dans ce contexte), configuration des ressources (CPU/RAM/GPU), arguments CLI pour les services (SD WebUI notamment).
    * Cohérence des versions figées entre `Dockerfile`, `requirements.txt` et `docker-compose.yml`.
3.  **Gestion des Dépendances (`requirements.txt`, Dépendances Système) :**
    * Pertinence et nécessité de chaque dépendance Python listée.
    * Risques liés aux versions figées choisies (conflits potentiels connus, versions obsolètes avec vulnérabilités ?).
    * Complétude des dépendances système dans le `Dockerfile` pour `unstructured` et autres.
4.  **Architecture Logicielle et Intégrations API :**
    * Solidité de l'architecture multi-services : Communication inter-conteneurs (variables d'env, réseau Docker).
    * Fiabilité des appels API dans `app.py` (timeouts, gestion des erreurs HTTP, retries potentiels si nécessaire).
    * Conception de l'Agent LangChain : Choix de ReAct, pertinence des descriptions d'outils pour le LLM, gestion du flux de pensée et des erreurs de parsing.
5.  **Script de Validation (`validate_khaldounia.sh`) :**
    * Robustesse et couverture du script.
    * Potentiels faux positifs/négatifs. Suggestions d'amélioration.
6.  **Sécurité Globale :**
    * Points d'attention spécifiques (ports exposés sur `0.0.0.0`, accès API sans authentification - normal pour du local mais à signaler, validation des inputs de l'agent).
7.  **Applicabilité pour Novice (Aspects Techniques) :**
    * Identifier les étapes de configuration ou les fichiers (`docker-compose.yml`, `app.py` initial) qui présentent le plus de risque d'erreur pour un novice *lors de la modification ou de l'adaptation*.

**Méthodologie d'Analyse Demandée :**
* Analyse méticuleusement chaque phase et chaque fichier de code/configuration fourni dans le plan.
* Évalue la cohérence technique entre les différentes parties.
* Identifie les points forts, les faiblesses, les risques potentiels (techniques, sécurité, performance) et les goulots d'étranglement éventuels.
* Considère les bonnes pratiques de développement logiciel, de gestion de conteneurs et de déploiement local d'IA.
* N'hésite pas à pointer les incohérences ou les ambiguïtés, même mineures.

**Format de Sortie Attendu : Rapport Structuré**
Fournis ta réponse sous forme d'un rapport détaillé et structuré comprenant :
1.  **Synthèse Globale :** Ton évaluation générale de la qualité technique et architecturale du plan.
2.  **Analyse Détaillée par Thème :** Reprends les 7 points listés dans "Objectif Principal" ci-dessus, en développant tes observations pour chacun.
3.  **Points Forts Techniques Clés :** Liste numérotée des aspects particulièrement bien conçus.
4.  **Faiblesses et Risques Techniques Identifiés :** Liste numérotée et **priorisée par criticité** (Critique > Majeur > Mineur), avec justification technique pour chaque point.
5.  **Recommandations d'Amélioration Concrètes :** Liste numérotée et **priorisée** de suggestions spécifiques pour améliorer la robustesse, la sécurité, la performance ou la maintenabilité. Fournis des exemples de code ou de configuration si pertinent. Indique clairement si une recommandation est **critique** (à faire avant installation) ou **optionnelle/post-installation**.

**Ton Attendue :**
Sois critique mais constructif. Fournis des justifications techniques claires pour tes affirmations. Adopte un ton professionnel et expert.

**Instructions Finales :**
Je vais maintenant te fournir le plan complet "Installation_Khaldounia_Plan.md" (Phases 0-10, Annexes A-E) dans le message suivant. Accuse réception du plan une fois reçu, puis commence ton analyse approfondie et génère le rapport structuré comme demandé. Assure-toi que ton analyse couvre bien *tous* les aspects demandés.

**[FIN DU PROMPT de recherche]**



### **Instructions Impératives pour l'IA Guide (A DONNER AVANT TOUT) - 

# 🧠 <strong><small>Prompt Finalisé – IA Guide Projet Khaldounia (Version Intégrale Adaptative)</small></strong>

## 🎯 Votre Rôle Essentiel
Vous êtes **l’IA guide désignée**, une spécialiste de l'assistance technique patiente et pédagogue, dédiée aux **utilisateurs débutants**. Votre unique mission est d'accompagner pas à pas l’utilisateur (**Halim-IA**) dans **l’installation complète et réussie du projet Khaldounia**, en suivant **ABSOLUMENT et STRICTEMENT** le contenu du plan détaillé (`Installation_Khaldounia_Plan.md`) qui sera fourni.
---
## 📚 Source de Vérité et Exception Contrôlée
1.  **Référence Unique :** Le document plan (`Installation_Khaldounia_Plan.md`) est **la seule référence autorisée** et doit être suivi méticuleusement. Toutes instructions, commandes, configurations et procédures doivent s’y conformer.
2.  **❌ Interdiction de Dévier :** Ne proposez **aucune solution externe, aucune commande non écrite dans le plan, aucune improvisation**, même si cela vous semble plus rapide ou plus simple, **SAUF** dans le cas de l'exception ci-dessous.
3.  **🚨 Exception Contrôlée pour Erreur de Plan Suspectée :**
    * **Condition :** Si, et seulement si, durant un diagnostic d'erreur approfondi (**Niveau 2 ou 3** de la section 🆘), vous avez de **fortes présomptions basées sur des preuves concrètes** (logs d'erreur précis, messages système clairs, documentation officielle très récente d'un composant central comme Docker, Ollama, Python, etc.) que le **plan lui-même contient une erreur significative** (ex: commande obsolète, faute de frappe critique bloquante, mauvaise configuration flagrante, version manifestement incompatible signalée dans les logs), vous devez impérativement :
        1.  Arrêter la progression.
        2.  **Signaler clairement** votre suspicion à Halim-IA en indiquant l'étape concernée.
        3.  **Expliquer précisément pourquoi** vous pensez que le plan est en erreur, en citant vos preuves (le message d'erreur exact, le log pertinent, le point de documentation si possible).
        4.  **Demander explicitement l'autorisation** à Halim-IA avant de proposer une correction ciblée ou une alternative documentée.
        5.  Ne **jamais** appliquer une correction ou dévier du plan sans cet accord préalable et explicite de Halim-IA.

---
## 🏗️ Métaphore Architecturale (Aide à la Compréhension)
Pour aider Halim-IA (et vous-même) à visualiser la progression, utilisez l'analogie suivante :
* **La fondation (Phases 0-2):** Mise en place du terrain (Windows/WSL), des fondations solides (Docker) et des plans initiaux (`docker-compose`). C'est la base indispensable.
* **Les murs et circuits (Phases 3-5):** Construction de la structure principale de l'application (`app.py`), installation des circuits essentiels (connexions API, STT, TTS). La maison prend forme.
* **Les pièces spécialisées (Phases 6-8):** Aménagement des pièces avec des fonctions avancées : le bureau pour la recherche web (Agent), l'atelier pour la création d'images (SD), le placard pour ranger les notes (Actions Système).
* **Le système nerveux central (Phases 9-10):** Connexion de l'intelligence et de la mémoire profonde de la maison : chargement des connaissances personnelles et activation de la capacité à les utiliser (RAG).
---
## 🧭 Préparation avant le Démarrage (Phase 0)
Avant de commencer la toute première étape (Phase 0, Étape 0.1), réalisez les actions suivantes :
1.  Effectuez un **résumé synthétique du plan** pour Halim-IA :
    * 📌 Listez les **titres des 11 phases** (Phase 0 à 10).
    * 🧩 Pour chaque phase, expliquez très brièvement son **objectif principal** en 1 phrase simple (en utilisant la métaphore si pertinent).
    * 🗂️ Rappelez l'**ordre logique général** et l'importance de suivre les étapes scrupuleusement.
2.  Confirmez que vous êtes prêt à démarrer avec la Phase 0, Étape 0.1.
---
## 🧱 Méthode d’Interaction – Guidage Structuré (Patron Modulaire)
Pour **chaque instruction ou étape majeure** du plan, structurez **systématiquement** votre réponse comme suit :

1.  **🔹 Contexte et Objectif :**
    * [➕ Expliquez brièvement en 1-2 phrases simples **pourquoi cette étape est nécessaire maintenant** et quel est son but dans la progression globale. Utilisez la métaphore si utile.]
    * [⏱️ Indiquez les **indicateurs temporels et de criticité** prévus pour cette étape : `[⚠️⚠️⚠️]`/`[⚠️⚠️]`/`[⚠️]`, `[⏱️]`/`[⏱️⏱️]`/`[⏱️⏱️⏱️]`]

2.  **🔹 Prérequis :**
    * [🧠 Avant de donner la commande, **énoncez et vérifiez** (ou demandez à Halim-IA de confirmer) la ou les préconditions essentielles pour cette étape. Ex: "Assurons-nous d'être dans le bon dossier..." ou "Vérifions que le service X est bien démarré..."]

3.  **🔹 Action :**
    * [💻 Fournissez **exactement et uniquement** la ou les **commandes complètes** à copier/coller, telles qu'indiquées dans le plan.]
    ```bash
    # Commande(s) ici
    ```
    * [🧾 **Explication :** Décrivez très brièvement (1 phrase) ce que fait la commande principale.]

4.  **🔹 Vérification Post-Exécution :**
    * [🔎 Proposez **une commande ou une observation simple et rapide** pour vérifier que l'action a réussi. Ex: "Pour vérifier, tapez `ls` et vous devriez voir...", "Le résultat attendu est...", "Vérifiez que le message suivant apparaît..."]

5.  **🔹 Attente et Transition :**
    * [🟢 Attendez explicitement la **confirmation de réussite** de Halim-IA ou le **résultat de la vérification** avant de proposer l'étape suivante.]
    * [❌ En cas de retour d'erreur, enclenchez immédiatement la procédure de la section 🆘.]

*(Note : Pour les commandes *extrêmement* simples et répétitives comme `cd` ou `ls` après la première occurrence, vous pouvez alléger légèrement les points 1 et 4, mais fournissez toujours l'Action (3) et attendez la confirmation (5)).*
---
## ⏱️ Indicateurs Temporels et de Criticité (À utiliser systématiquement)
Utilisez ces symboles **au début de la présentation de chaque étape majeure** (Point 1 du Patron Modulaire) :
* `[⚠️⚠️⚠️]` **Étape critique :** Échec = Blocage complet probable, nécessite une attention maximale.
* `[⚠️⚠️]` **Étape importante :** Échec = Fonctionnalité majeure compromise ou débogage complexe à prévoir.
* `[⚠️]` **Étape standard :** Échec = Impact limité ou facilement corrigeable.
* `[⏱️]` **Rapide :** Moins de 2 minutes (exécution commande simple).
* `[⏱️⏱️]` **Modérée :** 2 à 10 minutes (compilation courte, petit téléchargement, configuration).
* `[⏱️⏱️⏱️]` **Longue :** Plus de 10 minutes (téléchargement volumineux, construction Docker longue, entraînement modèle).
---
## 👤 Adaptation Progressive au Profil Utilisateur (Fil Rouge)
Tout en suivant la structure ci-dessus, essayez d'adapter *subtilement* votre niveau de détail :
* **Début :** Partez du principe que Halim-IA est novice, fournissez les explications complètes (sauf pour commandes triviales répétées).
* **Observation :** Notez si Halim-IA semble comprendre vite, pose peu de questions de clarification, réussit les étapes sans erreur. Notez aussi s'il pose beaucoup de questions ou fait des erreurs répétées sur certains types d'opérations.
* **Ajustement Prudent :**
    * Si compréhension rapide → Vous *pouvez* être légèrement plus concis dans les explications "Pourquoi" ou les détails de troubleshooting préventif, **mais ne sautez jamais une vérification essentielle**.
    * Si difficultés ou questions → **Maintenez ou augmentez** le niveau de détail, reformulez, donnez des exemples si nécessaire.
    * Si erreurs récurrentes → **Renforcez les explications et vérifications** sur les points qui posent problème.
* **Priorité :** La clarté, la sécurité et le suivi strict du plan restent **toujours prioritaires** sur la concision.
---
## 🆘 Gestion des Erreurs (Annexe A – Stratégie de Débogage)
Si Halim-IA signale une erreur ou si une vérification échoue :
1.  **Accusé Réception & Protocole :** Accusez réception de l'erreur et annoncez le niveau de protocole de récupération (Jaune/Orange/Rouge, voir ci-dessous).
2.  **🧾 Demande d'Infos :** Demandez **systématiquement** les informations via le modèle de rapport (Annexe A) : erreur exacte, logs complets pertinents (`docker logs [conteneur_suspect] --tail 100` est un bon début).
3.  **🧩 Diagnostic Structuré :** Analysez les informations en suivant les niveaux et l'arbre de diagnostic :
    * **Niveau 1 (Erreurs Simples) :** Faute de frappe évidente dans la commande ? Chemin incorrect ? Problème de copier-coller ? --> Correction simple si évidente.
    * **Niveau 2 (Dépendances/Connectivité) :** Service KO (`docker ps`) ? Port incorrect (`docker-compose.yml`) ? API inaccessible (`curl`) ? Dépendance manquante (logs build/runtime) ? Version incompatible (logs) ? *--> Évaluez ici si l'erreur pourrait venir du plan lui-même (cf. Exception Contrôlée section 📚).*
    * **Niveau 3 (Système/Environnement) :** Conflit de port système ? Manque de RAM/VRAM (`nvidia-smi`, logs OOM) ? Problème de droits sur volume (logs, `docker exec ls`) ? Environnement WSL/Docker corrompu ? *--> Évaluez ici si l'erreur pourrait venir du plan lui-même (cf. Exception Contrôlée section 📚).*
4.  **Proposition Ciblée :** Ne proposez une solution qu'après avoir une hypothèse plausible basée sur le diagnostic.
5.  **⛔ Reconstruction :** Ne suggérez `docker compose down -v` ou `docker system prune` qu'en **ultime recours** et après avoir expliqué les conséquences (perte de données) et obtenu l'accord.
---
## 🚑 Protocoles de Récupération (Gestion de la Gravité)
Adaptez votre communication et vos actions selon la gravité estimée de l'erreur :
* **🟡 Niveau Jaune (Erreurs Mineures/Attendues) :**
    * *Caractéristiques :* Faute de frappe évidente, message d'avertissement non bloquant, erreur de validation simple post-commande.
    * *Action :* Proposer une correction directe et rapide. Maintenir le flux.
    * *Exemple Ton :* "Il semble y avoir une petite coquille ici, essayons avec cette correction : [...]"
* **🟠 Niveau Orange (Erreurs Fonctionnelles Bloquantes) :**
    * *Caractéristiques :* Service qui ne démarre pas, connexion refusée persistante, erreur de build, fonctionnalité clé qui échoue.
    * *Action :* **Stopper la progression.** Annoncer le besoin de diagnostiquer. Suivre la procédure 🆘 (demande logs, niveaux...). Ne continuer qu'une fois le problème résolu.
    * *Exemple Ton :* "Ok, nous avons une erreur qui nous empêche de continuer pour l'instant (Niveau Orange). Pour la résoudre, nous devons d'abord comprendre d'où elle vient. Pouvez-vous me fournir les logs de [...] ?"
* **🔴 Niveau Rouge (Erreurs Critiques/Systémiques) :**
    * *Caractéristiques :* Corruption de volume suspectée, erreurs système profondes (WSL/Docker), incompatibilité majeure persistante, état instable généralisé.
    * *Action :* **Stopper.** Diagnostiquer pour confirmer la gravité. Si confirmée, **recommander explicitement** d'arrêter et d'envisager une restauration depuis la dernière sauvegarde fiable (Annexe B) avant de retenter. Ne pas essayer de "patcher" un système jugé trop instable.
    * *Exemple Ton :* "Attention, l'erreur que nous rencontrons semble critique (Niveau Rouge) et pourrait indiquer un problème plus profond. Poursuivre pourrait aggraver la situation. Je recommande fortement de nous arrêter ici et d'envisager de restaurer la dernière sauvegarde stable avant de réessayer cette phase."

---

## 🌳 Arbre de Diagnostic Principal (Aide Visuelle Simple)
Utilisez cette logique comme guide initial si un problème est détecté :
```
Si Problème:
├── ❓ Est-ce une API/Interface Web Inaccessible?
│   ├── ▶️ Le conteneur service tourne-t-il? (docker ps)
│   │   ├── ❌ Non → Analyser pourquoi il s'arrête (docker logs [service])
│   │   └── ✅ Oui → Le port est-il correct/exposé? (docker ps, docker-compose.yml)
│   │       └── ▶️ Tester connectivité directe (curl http://localhost:[port_hôte])
│
├── ❓ Est-ce un Service qui tourne mais fonctionne mal?
│   ├── ▶️ Y a-t-il des erreurs dans ses logs? (docker logs [service]) → Analyser erreurs spécifiques
│   └── ▶️ Manque-t-il des fichiers/données nécessaires? (Vérifier volumes montés via docker exec / logs)
│
└── ❓ Est-ce une Erreur dans le code Python (assistant_app)?
    ├── ▶️ Erreur de syntaxe Python évidente? (Logs build/runtime)
    └── ▶️ Problème d'import ou de librairie? (Logs build/runtime, vérifier requirements.txt)
```

---

## ⛔ Comportements à Éviter Strictement (Rappels)
* Ne **jamais** suggérer de modifier le plan d'installation sans suivre la procédure d'**Exception Contrôlée** (section 📚).
* Ne **jamais** sauter d'étapes, même si elles semblent évidentes ou répétitives.
* Ne **jamais** passer à la phase suivante si la validation de la phase actuelle (tests, script Annexe E si applicable) n'est pas confirmée comme réussie.
* Ne **jamais** proposer plusieurs solutions alternatives à une erreur sans les **hiérarchiser clairement** (la plus probable/simple d'abord) ou sans expliquer les avantages/inconvénients de chaque.
* Ne **jamais** utiliser un terme technique complexe sans l'**expliquer très simplement** la première fois qu'il apparaît.

---

## 🔍 Auto-vérification Systématique (Avant Réponse Majeure)
Avant de finaliser chaque réponse contenant une instruction majeure ou une conclusion de phase, effectuez mentalement cette check-list :
1.  ⚙️ **Technique :** La commande fournie est-elle syntaxiquement exacte par rapport au plan ?
2.  🔄 **Cohérence :** Cette instruction/explication est-elle cohérente avec les étapes précédentes et les objectifs de la phase ?
3.  🧪 **Idempotence :** (Si applicable) Exécuter cette commande plusieurs fois poserait-il un problème ? (Si oui, avertir ?)
4.  🔒 **Sécurité :** Y a-t-il un risque de sécurité immédiat ou induit par cette action qui mériterait d'être mentionné (même si non présent dans le plan) ?
5.  🧩 **Exhaustivité :** Ai-je bien inclus tous les éléments demandés par le Patron de Réponse Modulaire (Contexte, Prérequis, Action, Explication, Vérification) ?

---

## 📦 Suivi et Validation (Fin de Phase)
- 📋 À la fin de chaque **phase majeure** (ex: après Phase 0, 4, 5, 7, 8, 10) :
  - 🔁 Faire un **récapitulatif très clair et concis** des étapes clés réussies dans cette phase.
  - ✅ Proposer explicitement d'exécuter le **script de validation** (`./validate_khaldounia.sh` depuis `assistant-core`, Annexe E) si pertinent pour cette phase. Attendre le résultat.
  - 💾 Suggérer/rappeler l'étape de **sauvegarde** recommandée (Annexe B).
  - 🧠 Effectuer l'**auto-évaluation** ci-dessous avant de proposer de passer à la phase suivante.

---

## 🔄 Cycle d'amélioration continue (Auto-évaluation Post-Phase)
Après chaque phase majeure et avant de proposer la suivante, effectuez (mentalement) cette auto-évaluation rapide :
1.  **Clarté :** Mes instructions et explications pour la phase terminée étaient-elles claires et faciles à suivre ?
2.  **Exhaustivité :** Ai-je bien couvert tous les aspects nécessaires (commandes, vérifications, avertissements) ?
3.  **Adéquation :** Le niveau de détail semblait-il adapté à la réaction/compréhension de Halim-IA ?
4.  **Amélioration :** Comment puis-je rendre l'assistance pour la *prochaine* phase encore plus fluide ou efficace (ex: anticiper une question, mieux expliquer un concept clé à venir) ?

---

## 📨 Contexte Initial – Transmission du Plan
L’utilisateur (Halim-IA) transmettra le plan `Installation_Khaldounia_Plan.md` par **plusieurs messages successifs**, probablement groupés ainsi :
- 📁 Message 1 : Phases 0–3
- 📁 Message 2 : Phases 4–7
- 📁 Message 3 : Phases 8–10 + Annexes A-E

À chaque réception de partie :
- 📥 **Accuser réception distinctement** de la partie reçue (ex: "Bien reçu la partie 1/3 (Phases 0-3)").
- 🔍 Après réception de la dernière partie, **confirmer explicitement** que vous disposez du plan complet et des annexes nécessaires.
- 🚀 Une fois le plan complet reçu et après avoir effectué le résumé initial (section 🧭), confirmer que vous êtes prêt à commencer l'assistance à la **Phase 0, Étape 0.1**.

*(Les exemples de réponses fournis précédemment restent valables comme illustration du Patron Modulaire et de la Gestion des Erreurs).*

Fin du Prompt

## Sommaire

- [Phase 0 : Prérequis](#phase-0--prérequis-système-et-logiciels-révisée-finale-v2)
- [Phase 1 : Structure des Dossiers](#phase-1--création-de-la-structure-des-dossiers-révisée-finale)
- [Phase 2 : Configuration Docker Compose](#phase-2--configuration-docker-compose-base---versions-figées)
- [Phase 3 : Configuration App Assistant](#phase-3--configuration-app-assistant-base---versions-figées)
- [Phase 4 : Lancement Initial & Tests](#phase-4--lancement-initial--tests-base)
- [Phase 5 : Ajout Reconnaissance Vocale (STT)](#phase-5--ajout-reconnaissance-vocale-sttwhisper---version-figée)
- [Phase 6 : Ajout de la Recherche Web (Agent)](#phase-6--ajout-de-la-recherche-web-agent)
- [Phase 7 : Ajout Génération Image (Stable Diffusion)](#phase-7--ajout-génération-image-stable-diffusion)
- [Phase 8 : Ajout Actions Système (Outils Notes)](#phase-8--ajout-actions-système-outils-notes)
- [Phase 9 : Chargement et Test des Données pour RAG](#phase-9--chargement-et-test-des-données-pour-rag)
- [Phase 10 : Intégration de l'Outil RAG](#phase-10--intégration-de-loutil-rag)
- [Annexe A : Stratégie de Vérification et Débogage](#annexe-a--stratégie-de-vérification-et-débogage-finale-v3)
- [Annexe B : Notes sur la Sauvegarde](#annexe-b--notes-sur-la-sauvegarde-finale)
- [Annexe C : Notes sur la Gestion des Modèles Ollama](#annexe-c--notes-sur-la-gestion-des-modèles-ollama-finale)
- [Annexe D : Notes sur les Mises à Jour Générales](#annexe-d--notes-sur-les-mises-à-jour-générales-finale)
- [Annexe E : Script de Validation Rapide](#annexe-e--script-de-validation-rapide-validate_khaldouniash)
- [Annexe F : Tableau de Référence des Services et Ports](#annexe-f--tableau-de-référence-des-services-et-ports)

---

## Introduction

* **Objectif Général :** Mettre en place une infrastructure logicielle locale complète sur un PC Windows (10/11) utilisant WSL2 pour l'assistant IA conversationnel "Khaldounia". L'installation inclura Ollama (LLM local), XTTS v2 (TTS local), ChromaDB (Base RAG), Stable Diffusion A1111 (Génération Image), Whisper (STT), et une application d'orchestration/Interface basée sur LangChain et Chainlit.

* **Méthode :** Suivre ce document étape par étape. Utiliser VS Code (connecté à WSL) pour créer/éditer les fichiers. Exécuter toutes les commandes dans le terminal WSL (Ubuntu). Signaler toute erreur à l'IA guide.

---

## Phase 0 : Prérequis Système et Logiciels (Révisée Finale V2)

**Objectif :** S'assurer que l'environnement Windows / WSL2 est prêt et que les logiciels de base (Docker, Pilotes NVIDIA, VS Code) sont correctement installés et configurés pour supporter l'exécution de l'IA locale avec accélération GPU et suffisamment de ressources.

**IMPORTANT :** Ces étapes sont **cruciales**. Vérifiez chaque point attentivement avant de passer à la Phase 1.

---

### **Étape 0.0 : Recommandations Matérielles & Estimations VRAM**

* **Explication :** Ce projet lance plusieurs modèles d'IA gourmands en ressources (GPU VRAM, RAM Système). Une configuration minimale est recommandée pour une expérience correcte.
* **Recommandations Indicatives :**
    * **GPU :** NVIDIA CUDA 12+, **8 Go VRAM minimum** (16Go+ fortement recommandés pour SD+LLM, surtout pour une RTX 5090).
    * **RAM Système :** **16 Go minimum** (32Go+ recommandés).
    * **Stockage :** SSD avec 50-100 Go libres.
* **Estimations VRAM (Très Approximatives - peuvent varier !) :** Pour vous donner une idée de l'utilisation *potentielle* de chaque composant sur GPU :

    | Composant                 | Modèle Exemple (Plan)      | VRAM Estimée (GPU ON) | Notes                                      |
    | :------------------------ | :------------------------- | :-------------------- | :----------------------------------------- |
    | Ollama                    | Mistral 7B Q4_K_M          | ~ 4-6 Go              | Dépend fortement de la taille/quantization |
    | XTTS v2                   | `multi-dataset/xtts_v2`    | ~ 2-6 Go              | Varie selon la charge, peut être gourmand  |
    | Stable Diffusion          | SD 1.5 (ex: DreamShaper)   | ~ 4-8 Go (512x512)    | >10Go+ pour haute résolution/batch > 1   |
    | Whisper                   | `small` model              | ~ 1-2 Go              | Modèles `medium`/`large` > 5-10 Go         |

* **Conclusion VRAM :** Lancer SD + Ollama + XTTS sur GPU simultanément **peut facilement dépasser 16Go de VRAM**. La désactivation du GPU (`deploy:` commenté dans `docker-compose.yml`) pour XTTS et/ou Whisper est souvent **nécessaire** sur les cartes < 24Go VRAM. Testez votre configuration.
* **Note :** Si VRAM insuffisante, les calculs peuvent basculer sur CPU (très lent) et utiliser beaucoup de RAM système.

---

### **Étape 0.1 : Installation / Vérification de WSL2 et Ubuntu**

* **Explication :** WSL2 (Windows Subsystem for Linux version 2) est requis pour exécuter Linux sous Windows avec de bonnes performances GPU. Nous utilisons Ubuntu.
* **Action :** Ouvrez une invite de commande Windows (`cmd` ou `PowerShell`) **en tant qu'administrateur**.
* **Commande (Installation si nécessaire) :** Si WSL n'est pas du tout installé :
    ```bash
    wsl --install
    ```
    * *(Explication : Installe WSL, active fonctionnalités Windows, télécharge/installe Ubuntu LTS. Redémarrage possible).*
* **Commande (Vérification après installation/redémarrage) :** Dans une invite Windows normale :
    ```bash
    wsl -l -v
    ```
    * *(Explication : Doit afficher votre distribution (ex: "Ubuntu") avec `VERSION` **2**).*
    * *(Si Version 1, convertir avec `wsl --set-version [NomDistro] 2`).*
    * *(Si plusieurs distributions, assurez-vous qu'Ubuntu est celle par défaut via `wsl --set-default [NomDistroUbuntu]`).*
* **Commande (Mise à jour WSL - Recommandé) :** Dans une invite Windows normale :
    ```bash
    wsl --update
    ```
* **Commande (Définir WSL2 par Défaut - Bonne pratique) :** Dans une invite Windows normale :
    ```bash
    wsl --set-default-version 2
    ```

---

### **Étape 0.2 : Installation / Vérification Pilotes NVIDIA (Support GPU WSL2)**

* **Explication :** Nécessaire pour que WSL2/Docker accèdent à votre GPU NVIDIA (RTX 5090). Requiert des pilotes récents supportant CUDA 12+ et WSL2.
* **Action :** Téléchargez les pilotes depuis NVIDIA : [https://www.nvidia.fr/Download/index.aspx?lang=fr](https://www.nvidia.fr/Download/index.aspx?lang=fr).
    * **Important :** Sélectionnez précisément **votre modèle exact** (RTX 5090), OS (Windows 10/11), et type de pilote (Game Ready/Studio). Prenez une version récente. Sur Windows 10, assurez-vous d'avoir au minimum la build 21H2.
* **Action :** Installez les pilotes (option "Nouvelle installation"). Redémarrez si demandé.
* **Vérification (CRUCIALE - Dans WSL) :** Ouvrez votre terminal WSL/Ubuntu. Tapez :
    ```bash
    nvidia-smi
    ```
    * *(Explication : Doit s'exécuter **SANS ERREUR** dans WSL et afficher les infos de votre RTX 5090 et la version du pilote CUDA. Si erreur, problème de pilote ou d'installation WSL/NVIDIA).*

---

### **Étape 0.3 : Installation / Configuration Docker Desktop**

* **Explication :** Gère les conteneurs des services (Ollama, XTTS, SD...). Doit utiliser WSL2 pour l'accès GPU.
* **Action :** Installez/Mettez à jour Docker Desktop : [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
* **Action :** Lancez Docker Desktop.
* **Configuration (TRÈS IMPORTANT) :**
    1.  **Settings > General** : Cochez **"Use the WSL 2 based engine"**.
    2.  **Settings > Resources > WSL Integration** : Activez l'intégration avec votre distribution Ubuntu (slider **ON**).
    3.  **Settings > Resources > Advanced (ou similaire)** : Vérifiez que l'accès GPU est activé si une option existe ("Enable GPU compute..." ou similaire).
* **Action :** Appliquez & Redémarrez Docker si changements faits.
* **Vérification :** Dans le terminal WSL :
    ```bash
    # 1. Vérifier versions Docker (sans sudo si étape 0.3bis faite)
    docker --version
    docker compose version
    # 2. Test basique Docker
    docker run --rm hello-world
    # 3. TEST GPU DANS DOCKER (TRÈS IMPORTANT)
    docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi
    ```
    * *(Explication : La commande `nvidia/cuda... nvidia-smi` est cruciale. Elle doit télécharger une image test et exécuter `nvidia-smi` *dedans*. Si elle affiche votre GPU (RTX 5090) sans erreur, Docker/WSL2 peuvent utiliser le GPU. Si elle échoue (erreur CUDA, "no device found"...), l'accélération GPU ne fonctionnera pas. Vérifiez pilotes, MAJ WSL, config Docker Desktop avant de continuer).*

---

### **Étape 0.3bis : Configuration des Permissions Docker (Prévention Erreurs)**

* **Explication :** Pour éviter de taper `sudo` avant chaque commande `docker` sous WSL, ajoutez votre utilisateur au groupe `docker`.
* **Action :** Ouvrez votre terminal WSL (Ubuntu).
* **Commande :**
    ```bash
    sudo usermod -aG docker $USER
    ```
* **Action CRUCIALE :** **FERMEZ puis ROUVREZ** votre terminal WSL pour appliquer le changement.
* **Vérification (Optionnelle) :** Tapez `groups` dans le nouveau terminal. `docker` doit apparaître dans la liste. Vous devriez pouvoir lancer `docker ps` sans `sudo`.

---

### **Étape 0.3ter : Vérification/Configuration Limite RAM WSL2 (Prévention Crashs)**

* **Explication :** WSL2 limite sa RAM (par défaut 50% RAM hôte / 8Go max). Augmentez via `.wslconfig` pour éviter les crashs avec les modèles IA.
* **Action (Côté Windows) :**
    1.  Allez dans votre dossier `%UserProfile%` via l'Explorateur Windows.
    2.  Créez/Modifiez le fichier `.wslconfig` (avec un point au début, sans .txt).
* **Action (Contenu `.wslconfig`) :** Ajoutez/Assurez (adaptez `memory`) :
    ```ini
    [wsl2]
    memory=24GB  # EXEMPLE pour 32GB RAM système. Mettez ~75% de votre RAM totale. Adaptez !
    processors=8 # Optionnel: Adaptez à votre CPU (ex: nb de coeurs physiques)
    swap=8GB     # Optionnel: Taille du fichier swap pour WSL2 si besoin
    ```
    * *(Explication : `memory` définit la RAM max pour WSL2. Mettre trop haut peut ralentir Windows).*
* **Action (Appliquer) :** Sauvegardez `.wslconfig`. Dans `cmd`/`PowerShell`, tapez `wsl --shutdown`. WSL redémarrera avec la nouvelle limite à la prochaine ouverture du terminal Ubuntu.

---

### **Étape 0.4 : Installation / Configuration Visual Studio Code (VS Code)**

* **Explication :** Éditeur recommandé avec l'extension WSL pour éditer les fichiers directement dans l'environnement Linux.
* **Action :** Installez/Mettez à jour VS Code : [https://code.visualstudio.com/](https://code.visualstudio.com/)
* **Action :** Lancez VS Code. Allez dans l'onglet Extensions (icône carrés) et installez l'extension **"WSL"** de Microsoft.
* **Vérification / Utilisation :**
    1.  Cliquez sur l'icône verte `><` en bas à gauche de VS Code.
    2.  Choisissez "Connect to WSL". Une nouvelle fenêtre VS Code connectée à votre environnement WSL s'ouvrira.
    3.  Ouvrez un terminal intégré (`Terminal` > `Nouveau Terminal` ou `Ctrl+Maj+ù`). Il doit s'agir d'un terminal **bash Ubuntu**, indiquant votre prompt WSL (ex: `VotreNomWSL@Machine:~$`). C'est dans ce type de terminal que vous exécuterez les commandes des phases suivantes.
    4.  Vous pouvez ouvrir des dossiers WSL via `Fichier > Ouvrir un dossier...` (naviguez vers `/home/VotreNomWSL/...`).

---

### **Étape 0.5 : Installation de Git (Optionnel mais Recommandé)**

* **Explication :** Outil de contrôle de version pour suivre les modifications de vos fichiers de configuration et code. Recommandé pour pouvoir revenir en arrière facilement (voir Annexe B).
* **Action :** Dans le terminal WSL/Ubuntu (celui de VS Code ou un terminal Ubuntu séparé) :
    ```bash
    sudo apt update && sudo apt install git -y
    ```
* **Vérification :**
    ```bash
    git --version
    ```
    *(Doit afficher la version de Git installée).*

---

Fin de la Phase 0.


```markdown
---

## Phase 1 : Création de la Structure des Dossiers (Révisée v4.2 - Préparation Volumes Nommés)

**Objectif :** Mettre en place l'arborescence organisée dans WSL pour accueillir les fichiers de configuration, le code du projet Khaldounia, et **préparer l'emplacement sur l'hôte** pour le fichier vocal de référence qui sera copié dans un volume Docker ultérieurement.

* **Note WSL/Linux :** Rappelez-vous que Linux est sensible à la casse (ex: `MonDossier` != `mondossier`).

---

### Étape 1.1 : Se Placer dans le Répertoire Personnel

* **Explication :** Point de départ standard dans WSL.
* **Commande :**
    ```bash
    cd ~
    ```

### Étape 1.2 : Créer le Dossier Principal du Projet

* **Explication :** Crée le dossier racine `projet-khaldounia`.
* **Commande :**
    ```bash
    mkdir projet-khaldounia
    ```

### Étape 1.3 : Entrer dans le Dossier du Projet

* **Explication :** Navigation vers le dossier créé.
* **Commande :**
    ```bash
    cd projet-khaldounia
    ```

### Étape 1.4 : Créer le Dossier `assistant-core`

* **Explication :** Contient la configuration Docker et le code de l'application principale.
* **Commande :**
    ```bash
    mkdir assistant-core
    ```

### Étape 1.5 : Créer le Sous-Dossier `app`

* **Explication :** Contient le code Python de l'assistant (`app.py`, `requirements.txt`, `Dockerfile`). Se trouvera dans `assistant-core`.
* **Commande :**
    ```bash
    mkdir assistant-core/app
    ```

### Étape 1.6 : Créer des Dossiers pour Préparer les Données TTS (`tts-data`)

* **Explication :** Crée une structure locale dans `projet-khaldounia` pour organiser les fichiers liés à XTTS avant leur utilisation ou copie dans les volumes Docker.
* **Commande (`-p` évite erreur si existe déjà) :**
    ```bash
    mkdir -p tts-data
    ```
* **Commande (Dossier pour fichier vocal de référence) :**
    ```bash
    mkdir -p tts-data/speakers
    ```
* **Commande (Dossier pour info, cache sera géré par volume nommé) :**
    ```bash
    mkdir -p tts-data/cache
    ```

### Étape 1.7 : Préparer et Placer le Fichier Vocal de Référence (`speaker_ref.wav`) sur l'Hôte

* **Explication :** XTTS a besoin d'un fichier audio WAV de bonne qualité (~15-30s, clair, calme, mono, 22050Hz ou 24000Hz, 16bit PCM) de la voix à cloner. Nous plaçons ce fichier dans le dossier créé à l'étape précédente sur notre système WSL (hôte). **Il sera copié dans le volume Docker approprié plus tard via une commande `docker cp` (voir Phase 4)**.
* **Conseils Création (si besoin) :** Utilisez un micro décent, pièce calme, lisez un texte neutre. Logiciels comme Audacity (gratuit) permettent d'enregistrer et d'exporter au bon format. Nommez le fichier `speaker_ref.wav`.

* **Action (Manuelle) :** Une fois le fichier `speaker_ref.wav` prêt sur votre machine Windows, copiez-le vers l'emplacement suivant **DANS WSL** : `~/projet-khaldounia/tts-data/speakers/`.
    * *(Méthode : Ouvrez `~/projet-khaldounia/tts-data/speakers/` dans VS Code connecté à WSL et glissez-déposez le fichier depuis l'explorateur Windows. Ou utilisez la commande `cp /mnt/c/chemin/vers/votre/speaker_ref.wav ~/projet-khaldounia/tts-data/speakers/` en adaptant le chemin source).*
* **Note Importante :** Ce fichier n'est **PAS** directement utilisé depuis cet emplacement par le conteneur XTTS. Il sert de source pour une copie ultérieure dans le volume Docker `khaldounia_xtts_speakers`.

### Étape 1.8 : Vérifier la Structure Créée sur l'Hôte

* **Explication :** Confirme que l'arborescence locale est correcte et que le fichier source est prêt.
* **Action :** Depuis le dossier `~/projet-khaldounia`, exécutez :
    ```bash
    ls -R
    ```
* **Résultat Attendu (Structure sur l'hôte WSL) :**
    ```
    .:
    assistant-core
    tts-data

    ./assistant-core:
    app

    ./assistant-core/app:

    ./tts-data:
    cache
    speakers

    ./tts-data/cache:

    ./tts-data/speakers:
    speaker_ref.wav
    ```
    *(Vérifiez que `speaker_ref.wav` est bien listé dans `speakers`. C'est normal qu'il soit ici sur l'hôte à ce stade).*

---

Fin de la Phase 1 (Révisée v4.2).

```markdown
---

## Phase 2 : Configuration Docker Compose (Base - Révisée v5.0 - Init Ollama + Volumes Nommés XTTS + Ports Locaux + Healthchecks)

**Objectif :** Créer le fichier `docker-compose.yml` pour les services de base (Ollama avec auto-pull, XTTS avec volumes nommés, ChromaDB) et l'application assistante. Utilise des versions d'images Docker figées, des **volumes nommés partout** pour la portabilité, inclut des **healthchecks** pour la stabilité, et **restreint les ports** à l'hôte local (`127.0.0.1`) pour la sécurité.

**Emplacement :** `~/projet-khaldounia/assistant-core/docker-compose.yml`

---

### Étape 2.1 : Se Placer dans le Dossier `assistant-core`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Nous devons être dans le bon dossier pour créer le fichier de configuration principal de Docker.
* **Prérequis :** Le dossier `~/projet-khaldounia/assistant-core` a été créé (Phase 1).
* **Action :**
  ```bash
  cd ~/projet-khaldounia/assistant-core
  ```
  * **Explication :** Change le répertoire courant vers `assistant-core`.
* **Vérification Post-Exécution :**
  ```bash
  pwd
  ```
  * **Résultat Attendu :** Doit afficher `/home/VotreNomWSL/projet-khaldounia/assistant-core` (adaptez `VotreNomWSL`).
* **Attente et Transition :** Confirmez que vous êtes dans le bon dossier.

---

### Étape 2.2 : Créer/Modifier le Fichier `docker-compose.yml`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Préparer le fichier qui définira tous nos services Docker et comment ils interagissent.
* **Prérequis :** Être dans le dossier `assistant-core`. VS Code ou un éditeur de texte (comme `nano`) est disponible.
* **Action :** Utilisez VS Code (`code .` puis clic droit > New File) ou un éditeur en ligne de commande pour créer/ouvrir `docker-compose.yml`.
  ```bash
  # Exemple avec nano (utilisez Ctrl+O pour sauvegarder, Ctrl+X pour quitter)
  nano docker-compose.yml
  ```
  * **Explication :** Ouvre l'éditeur pour le fichier `docker-compose.yml`.
* **Vérification Post-Exécution :** L'éditeur est ouvert, prêt à recevoir le contenu.
* **Attente et Transition :** Préparez-vous à coller le contenu YAML à l'étape suivante.

---

### Étape 2.3 : Coller le Contenu YAML Complet (Révisé v5.0)

* **[⚠️⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Définir l'ensemble des services de base (Ollama, XTTS, ChromaDB, App), leurs configurations (ports, volumes, réseau, dépendances, healthchecks) et l'initialisation d'Ollama. C'est le cœur de notre orchestration Docker.
* **Prérequis :** Le fichier `docker-compose.yml` est ouvert dans un éditeur.
* **Action :** Copiez et collez **l'intégralité** du bloc YAML ci-dessous dans le fichier `docker-compose.yml`. **Aucune modification de chemin n'est nécessaire** car nous utilisons des volumes nommés partout.
  ```yaml
  # Fichier: docker-compose.yml (Révisé v5.0 - Phase 2 Complète)
  # Inclut: Init Ollama, Volumes Nommés XTTS, Ports 127.0.0.1, Healthchecks
  # Projet: Khaldounia (Base)

  version: '3.8'

  services:
    # --- Service LLM (Ollama) ---
    ollama:
      image: ollama/ollama:0.1.41 # <== VERSION FIGÉE
      container_name: ollama
      # AJOUT v5.0: Auto-pull du modèle par défaut au démarrage
      command: sh -c "ollama pull mistral:latest && ollama serve" # Adaptez 'mistral:latest' si un autre modèle est voulu par défaut
      deploy: # Accès GPU NVIDIA
        resources:
          reservations:
            devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
      volumes:
        - khaldounia_ollama_data:/root/.ollama # Persistance modèles Ollama (Volume Nommé)
      # MODIFIÉ v5.0: Port restreint à localhost
      ports: [ "127.0.0.1:11434:11434" ] # API Ollama (Accès local seulement)
      networks: [ assistant_network ]
      restart: unless-stopped
      # AJOUT v5.0: Healthcheck
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:11434/"]
        interval: 30s
        timeout: 10s
        retries: 3
        start_period: 60s # Laisse le temps de démarrer et potentiellement pull

    # --- Service Synthèse Vocale (XTTS) ---
    xtts_server:
      image: ghcr.io/coqui-ai/tts:v0.22.0 # <== VERSION FIGÉE
      container_name: xtts_server
      command: >
        tts-server
        --model_name tts_models/multilingual/multi-dataset/xtts_v2
        --use_cuda true
        --port 8020
        --speaker_wav /app/speakers/speaker_ref.wav
      # MODIFIÉ v5.0: Port restreint à localhost
      ports: [ "127.0.0.1:8050:8020" ] # Hôte:Conteneur (Accès local seulement)
      volumes:
        # MODIFIÉ v5.0: Utilisation de volumes nommés (plus de chemins absolus !)
        - khaldounia_xtts_cache:/root/.local/share/tts # Cache XTTS
        - khaldounia_xtts_speakers:/app/speakers      # Voix référence
        # NOTE v5.0: 'speaker_ref.wav' devra être copié dans le volume via 'docker cp' (voir plan Phase 1 modifiée)
      environment:
        - NVIDIA_VISIBLE_DEVICES=all
        - NVIDIA_DRIVER_CAPABILITIES=compute,utility
        - COQUI_TOS_AGREED=1 # Requis
      deploy: # Accès GPU
        resources:
          reservations:
            devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        # >>> NOTE GESTION VRAM : Si VRAM insuffisante (<16-20Go total), commentez 'deploy:' ici <<<
      shm_size: 8gb # Mémoire partagée
      networks: [ assistant_network ]
      restart: unless-stopped
      # AJOUT v5.0: Healthcheck
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"] # Teste si l'API est démarrée
        interval: 30s
        timeout: 10s
        retries: 3
        start_period: 180s # Laisse largement le temps de charger le modèle TTS

    # --- Service Base de Données Vectorielle (ChromaDB) ---
    chromadb:
      image: chromadb/chroma:0.5.0 # <== VERSION FIGÉE
      container_name: chromadb
      environment:
        - IS_PERSISTENT=TRUE
        - ALLOW_RESET=TRUE
      volumes:
        - khaldounia_chroma_data:/chroma # Persistance données RAG (Volume Nommé)
      # MODIFIÉ v5.0: Port restreint à localhost
      ports: [ "127.0.0.1:8002:8000" ] # Hôte:Conteneur (Accès local seulement)
      networks: [ assistant_network ]
      restart: unless-stopped
      # AJOUT v5.0: Healthcheck
      healthcheck:
        test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"] # Endpoint dédié
        interval: 30s
        timeout: 10s
        retries: 3
        start_period: 30s

    # --- Service Application Assistant (LangChain + Chainlit) ---
    assistant_app:
      # Build local depuis dossier ./app (utilise python:3.11-slim figé dans Dockerfile)
      build: { context: ./app }
      container_name: assistant_app
      # MODIFIÉ v5.0: Port restreint à localhost
      ports: [ "127.0.0.1:8001:8000" ] # UI Chainlit (Accès local seulement)
      volumes:
        # Monte le code source local pour dev facile (les changements sont reflétés)
        - ./app:/app
        # Volume pour le cache des embeddings téléchargés par SentenceTransformer
        - embedding_cache:/app/embedding_cache
        # Volume pour l'historique des chats (sera utilisé en Phase 5+)
        - khaldounia_chat_history:/app/chat_history
      # Note: Variables d'env pour communication interne lues depuis .env par app.py (Phase 5+)
      #       Ex: OLLAMA_HOST=http://ollama:11434, XTTS_SERVER_URL=http://xtts_server:8020, etc.
      depends_on: # Assure que les dépendances démarrent AVANT l'app ET sont SAINES
          ollama: { condition: service_healthy } # AJOUT v5.0: Attend état 'healthy'
          xtts_server: { condition: service_healthy } # AJOUT v5.0: Attend état 'healthy'
          chromadb: { condition: service_healthy } # AJOUT v5.0: Attend état 'healthy'
          # Les dépendances futures (Whisper, SD) devront aussi avoir des healthchecks et être ajoutées ici
          # whisper_server: { condition: service_healthy }
          # stable_diffusion_webui: { condition: service_healthy }
      stdin_open: true # Permet l'interaction si on attache un terminal
      tty: true        # Alloue un pseudo-TTY
      networks: [ assistant_network ]
      restart: unless-stopped
      # AJOUT v5.0: Healthcheck
      healthcheck:
        # Teste si l'UI Chainlit répond (après le démarrage potentiel de l'agent/embeddings)
        test: ["CMD", "curl", "-f", "http://localhost:8000/"]
        interval: 30s
        timeout: 10s
        retries: 5 # Un peu plus de tentatives pour laisser le temps à Chainlit/app.py de s'initialiser
        start_period: 90s # Laisse le temps de build/start/init embeddings

  # --- Réseau Docker ---
  # Crée un réseau isolé pour que les services communiquent par leur nom
  networks:
    assistant_network: { driver: bridge, name: assistant_network }

  # --- Volumes Docker ---
  # Définit les volumes nommés pour la persistance des données
  volumes:
    khaldounia_ollama_data: { driver: local } # Modèles Ollama
    khaldounia_chroma_data: { driver: local } # Base de données ChromaDB
    embedding_cache: { driver: local }       # Cache modèles SentenceTransformer
    khaldounia_chat_history: { driver: local } # Historique des chats (utilisé Phase 5+)
    # AJOUT v5.0: Volumes nommés pour XTTS
    khaldounia_xtts_cache: { driver: local }
    khaldounia_xtts_speakers: { driver: local }
    # Volumes SD (seront définis en Phase 7)
    # khaldounia_sd_config: { driver: local }
    # khaldounia_sd_models: { driver: local }
    # khaldounia_sd_outputs: { driver: local }

  ```
* **Action (Sauvegarde) :** Sauvegardez le fichier `docker-compose.yml` dans votre éditeur (ex: `Ctrl+O`, `Entrée` puis `Ctrl+X` dans `nano`).
* **Vérification Post-Exécution :** Assurez-vous que le fichier `docker-compose.yml` a bien été créé/modifié dans le dossier `assistant-core`.
* **Attente et Transition :** Le fichier de configuration principal est prêt.

---

### Étape 2.4 : Comprendre les Changements Clés (Révisée v5.0)

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Résumer les améliorations et points importants de ce fichier `docker-compose.yml` révisé.
* **Points Clés de cette Version :**
    * ✅ **Versions Figées :** Utilisation de tags spécifiques pour les images Docker (Ollama, XTTS, ChromaDB) pour la stabilité.
    * ✅ **Volumes Nommés Généralisés :** Utilisation de volumes nommés Docker (`khaldounia_ollama_data`, `khaldounia_chroma_data`, `embedding_cache`, `khaldounia_chat_history`, **`khaldounia_xtts_cache`**, **`khaldounia_xtts_speakers`**) pour toutes les données persistantes. C'est plus robuste et portable que les chemins absolus.
    * ✅ **Sécurité des Ports :** Tous les ports exposés sont bindés sur `127.0.0.1`, limitant l'accès à votre machine locale uniquement.
    * ✅ **Initialisation Ollama :** La ligne `command:` dans le service `ollama` force le téléchargement du modèle `mistral:latest` (ou celui spécifié) au démarrage, simplifiant le premier usage.
    * ✅ **Healthchecks & Dépendances Saines :** Chaque service a une section `healthcheck` pour vérifier son état. Le service `assistant_app` utilise `depends_on: ... condition: service_healthy` pour attendre que les autres services soient pleinement opérationnels avant de démarrer, améliorant la stabilité.
    * ✅ **Réseau Isolé :** Le réseau `assistant_network` permet aux conteneurs de communiquer facilement par leur nom.
    * ❗ **Note sur `speaker_ref.wav` :** Puisque le volume `khaldounia_xtts_speakers` est maintenant géré par Docker, vous devrez copier votre fichier vocal dedans après le premier démarrage des conteneurs, en utilisant une commande comme `docker cp chemin/local/speaker_ref.wav xtts_server:/app/speakers/`. Cette étape sera détaillée dans la Phase 1 modifiée ou au début de la Phase 4.
* **Vérification Post-Exécution :** Comprendre les avantages de cette configuration.
* **Attente et Transition :** Prêt à passer à la Phase 3 pour configurer l'application elle-même.

---

Fin de la Phase 2 (Révisée v5.0).
```

```markdown
---

## Phase 3 : Configuration App Assistant (Base - Révisée v4.1 Sécurité/Robustesse)

**Objectif :** Créer les fichiers de configuration et le code initial pour le service `assistant_app` : `Dockerfile` (avec dépendances système et **utilisateur non-root** pour la sécurité), `requirements.txt` (avec **versions figées et mises à jour** pour la sécurité/stabilité), et `app.py` (code Python initial intégrant **timeouts, retries de base, vérification des erreurs HTTP et sanitization des inputs**).

**Emplacement :** `~/projet-khaldounia/assistant-core/app/`

---

### Étape 3.1 : Se Placer dans le Dossier `app`

* **Commande :**
    ```bash
    cd ~/projet-khaldounia/assistant-core/app
    ```
* **Commande (Vérification) :**
    ```bash
    pwd
    ```
    * *(Doit afficher `/home/VotreNomWSL/projet-khaldounia/assistant-core/app`)*.

---

### Étape 3.2 : Créer/Modifier le Fichier `Dockerfile` (Révisé v4.1 - Non-Root User)

* **Explication :** Définit comment construire l'image Docker pour `assistant_app`. Ajout d'un utilisateur `appuser` non-root pour améliorer la sécurité (bonne pratique). Installe les dépendances système et Python.
* **Action :** Créez ou ouvrez le fichier `Dockerfile` dans le dossier `app`.
* **Action :** Collez **l'intégralité** du contenu suivant :

```dockerfile
# Fichier: Dockerfile (Révisé v4.1 - Ajout User Non-Root)
# Rôle: Construit l'image Docker pour le service assistant_app

# Utilise une version spécifique de Python 3.11 slim pour la stabilité
FROM python:3.11-slim

# --- Création Utilisateur Non-Root ---
# Crée un groupe et un utilisateur système sans privilèges pour exécuter l'application
RUN groupadd -r appgroup && useradd --no-log-init -r -g appgroup appuser

WORKDIR /app

# --- Installation Dépendances Système ---
# Installe les paquets nécessaires (avant de copier le code)
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    poppler-utils \
    tesseract-ocr tesseract-ocr-fra \
    libreoffice \
    ffmpeg \
    curl \
    # Note: Vérifiez les logs si 'unstructured' échoue sur certains formats plus tard.
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# --- Installation Dépendances Python ---
# Copie requirements.txt et installe les dépendances Python en tant que root
# pour profiter du cache Docker, avant de changer de user.
COPY --chown=appuser:appgroup requirements.txt requirements.txt
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# --- Copie du Code Application ---
# Copie le reste des fichiers du dossier local (app.py) dans l'image
# et s'assure que l'utilisateur 'appuser' en est propriétaire.
COPY --chown=appuser:appgroup . .

# --- Création Dossiers & Permissions ---
# Crée les dossiers nécessaires et s'assure que appuser peut écrire dedans
# (notamment pour le cache embedding et les futurs uploads/générations)
RUN mkdir -p /app/embedding_cache /app/user_notes /app/generated_images /app/temp_uploads && \
    chown -R appuser:appgroup /app

# --- Passage à l'Utilisateur Non-Root ---
USER appuser

# --- Commande de Démarrage ---
# Lance Chainlit en tant qu'utilisateur 'appuser'.
# -w pour recharger, --host 0.0.0.0 pour écouter (sécurité gérée par port binding 127.0.0.1 dans compose)
CMD ["chainlit", "run", "app.py", "-w", "--host", "0.0.0.0", "--port", "8000"]

```

* **Action :** Sauvegardez le fichier `Dockerfile`.

---

### Étape 3.3 : Créer/Modifier le Fichier `requirements.txt` (Révisé v4.1 - MAJ Sécurité)

* **Explication :** Définit les dépendances Python avec versions figées. **Mise à jour critique** de `langchain-community` pour corriger une vulnérabilité potentielle et ajout de `tenacity` pour la robustesse des appels API.
* **Action :** Créez ou ouvrez le fichier `requirements.txt` dans le dossier `app`.
* **Action :** Remplacez **l'intégralité** du contenu par :

```txt
# Fichier: requirements.txt (Révisé v4.1 - MAJ Sécurité/Robustesse)

# --- Core UI & Orchestration ---
chainlit==1.1.300
langchain==0.2.1
langchain-community==0.2.5 # MAJ CRITIQUE pour Sécurité/Compatibilité (était 0.1.1)
langchain-ollama==0.1.0 # Outil spécifique Ollama
langchainhub==0.1.15
langchain-experimental==0.0.60
pydantic==2.7.1 # Figé pour compatibilité

# --- RAG ---
chromadb-client==0.5.0
sentence-transformers==2.7.0
pypdf==4.2.0
unstructured[local-inference]==0.13.7

# --- API & Utilitaires ---
requests==2.31.0
python-dotenv==1.0.1
numpy==1.26.4
soundfile==0.12.1
tenacity==8.2.3 # AJOUT pour Retries API

# --- Outils Agent ---
duckduckgo-search==5.3.1b1

# --- Note ---
# Ces versions sont choisies pour leur compatibilité supposée début 2025.
# La MAJ de langchain-community est critique. Vérifiez la compatibilité
# globale si des erreurs surviennent après d'autres mises à jour.
```

* **Action :** Sauvegardez le fichier `requirements.txt`.

---

### Étape 3.4 : Créer/Modifier le Fichier `app.py` (Version Initiale Révisée v4.1)

* **Explication :** Code Python initial pour Chainlit. Intègre maintenant des améliorations de robustesse et sécurité : import de `tenacity` et `sanitize_input`, ajout d'un décorateur `@retry` simple, de `timeout` et `raise_for_status` sur l'appel `text_to_speech`, et placeholder pour la sanitization de l'input utilisateur.
* **Action :** Créez ou ouvrez le fichier `app.py` dans le dossier `app`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Version Initiale Révisée v4.1 - Phase 3/4 Base Sécu/Robustesse)
# Rôle: Structure base + Connexions Ollama/ChromaDB/XTTS + Hardening initial

import chainlit as cl
import os
import requests
import base64
from pathlib import Path
import time
import chromadb # Import du client ChromaDB
import logging # Import standard logging

# AJOUT v4.1: Imports pour Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input # Pour prévenir injections

# Configuration du Logging (simple pour le début)
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain (minimum + prépare le futur)
logging.info("--- Début Importation Langchain (Phase 3 Rev 4.1) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# Imports pour fonctionnalités futures (référence)
# ... (autres imports Langchain pour phases futures) ...
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale ---
logging.info("--- Début Chargement Configuration ---")
OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434")
XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020")
CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb")
CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000")
# ... (autres variables comme WHISPER, SD_API, etc.) ...
OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest") # Exemple
EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"
EMBEDDING_CACHE_DIR = Path("/app/embedding_cache") # Assuré créé et accessible par appuser dans Dockerfile
SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads")
logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}")
logging.info(f"Cache Embeddings: {EMBEDDING_CACHE_DIR}")
logging.info("--- Fin Chargement Configuration ---")

# --- Fonctions Utilitaires (Seule TTS est activement utilisée initialement) ---

# AJOUT v4.1: Décorateur Retry de Tenacity (exemple simple)
@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True) # reraise=True pour pouvoir catcher l'erreur finale
def text_to_speech(text: str) -> bytes | None:
    """Appelle l'API XTTS pour générer l'audio avec retry et timeout."""
    api_url = f"{XTTS_SERVER_URL}/generate"
    payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}
    logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'")
    try:
        # AJOUT v4.1: Timeout + raise_for_status
        response = requests.post(api_url, json=payload, timeout=60) # Timeout de 60s
        response.raise_for_status() # Lève une exception pour les erreurs HTTP (4xx, 5xx)
        data = response.json()
        audio_base64 = data.get("audio_base64")
        if audio_base64:
            logging.info("[TTS] Audio reçu avec succès.")
            return base64.b64decode(audio_base64)
        else:
            # Cas où l'API répond 200 OK mais sans données audio (peu probable mais géré)
            logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'. Réponse: {data}")
            return None
    except requests.exceptions.Timeout as e:
         logging.warning(f"[TTS] Timeout lors de l'appel API ({e}). Tenacity va réessayer...")
         raise # Relance l'exception pour que Tenacity la capture et réessaie
    except requests.exceptions.RequestException as e:
        logging.warning(f"[TTS] Erreur requête API ({e}). Tenacity va réessayer...")
        raise # Relance pour Tenacity
    except Exception as e:
        # Capture une erreur inattendue (ex: parsing JSON échoue) qui ne sera pas réessayée
        logging.error(f"[TTS] Erreur inattendue (non-retryable): {e}", exc_info=True)
        return None # Ne pas relancer pour éviter boucle infinie si erreur non liée à l'appel

# --- Placeholders pour fonctions futures (activées dans les phases suivantes) ---
# ... (fonctions transcribe_audio, generate_image_tool, notes, RAG etc. restent placeholders ici) ...
def transcribe_audio(audio_bytes: bytes) -> str | None: logging.warning("[STT] transcribe_audio non actif."); return None
def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] generate_image_tool non actif."); return "Fonctionnalité désactivée."
def _is_path_safe(requested_path: Path) -> bool: return False
def list_notes_safe() -> str: logging.warning("[NOTES] list_notes_safe non actif."); return "Action désactivée."
def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] save_note_safe non actif."); return "Action désactivée."
def read_note_safe(filename: str) -> str: logging.warning("[NOTES] read_note_safe non actif."); return "Action désactivée."
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée."


# --- Initialisation Globale IA (Embeddings, Client ChromaDB, Retriever) ---
logging.info("--- Début Initialisation Globale IA ---")
ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}
try:
    logging.info("[INIT] Chargement SentenceTransformer Embeddings...")
    ia_components["embeddings"] = SentenceTransformerEmbeddings(
        model_name=EMBEDDING_MODEL_NAME,
        cache_folder=str(EMBEDDING_CACHE_DIR) # Chemin du cache
    )
    logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt (cache: {EMBEDDING_CACHE_DIR}).")

    logging.info(f"[INIT] Connexion au client ChromaDB à http://{CHROMADB_HOST}:{CHROMADB_PORT}...")
    ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60)
    ia_components["chroma_client"].heartbeat() # Teste la connexion
    logging.info("[INIT] Client ChromaDB connecté.")

    logging.info(f"[INIT] Préparation Langchain VectorStore '{CHROMA_COLLECTION_NAME}'...")
    ia_components["vector_store_lc"] = ChromaLangchainStore(
        client=ia_components["chroma_client"],
        collection_name=CHROMA_COLLECTION_NAME,
        embedding_function=ia_components["embeddings"]
    )
    ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3})
    logging.info("[INIT] Langchain VectorStore et Retriever prêts.")
    logging.info("--- Fin Initialisation Globale IA ---")

except Exception as e:
    # Erreur critique si l'init échoue
    logging.critical(f"[INIT - ERREUR FATALE] Initialisation globale Embeddings/Chroma échouée: {e}", exc_info=True)
    # Mettre les composants à None pour que les vérifications échouent proprement
    ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}

# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation pour chaque nouvelle session de chat."""
    logging.info("\n--- Nouvelle Session Chat (Phase 3 v4.2 - Setup Initial) ---")
    # Vérifier si l'initialisation globale a réussi
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA (Embeddings/Chroma) non initialisés au démarrage.")
         await cl.Message(content="Erreur critique: Composants IA (Embeddings/Chroma) non initialisés. Vérifiez logs serveur.").send()
         return # Ne pas continuer si l'init de base a échoué

    try:
        logging.info("[Session Start] Initialisation LLM Ollama...")
        llm = ChatOllama(model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.7)
        cl.user_session.set("llm", llm)
        # Stocker retriever/vector store pour utilisation future (RAG)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
        logging.info("[Session Start] LLM et Retriever prêts.")

        # Chaîne LangChain TRES simple (LLM seul) pour test initial Phase 4
        logging.info("[Session Start] Création chaîne LLM simple...")
        # Note: Utilisation de RunnablePassthrough pour passer directement l'input (après sanitization)
        prompt = ChatPromptTemplate.from_template("Question: {input_sanitized}\nRéponse:")
        simple_chain = {"input_sanitized": RunnablePassthrough()} | prompt | llm | StrOutputParser()
        cl.user_session.set("chain", simple_chain)
        logging.info("[Session Start] Chaîne LLM simple prête.")

        logging.info("[Session Start] Configuration UI: Input Texte seulement.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="Entrez votre message...", initial="") ] ).send()

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant (Phase 3 v4.2 - Test Initial) prêt.", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Initialisée et Prête ---")

    except Exception as e:
        # Erreur pendant le setup de la session
        error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"
        logging.critical(error_msg, exc_info=True)
        await cl.Message(content=f"Erreur interne lors du démarrage de la session: {error_msg}", author="Erreur Système").send()

@cl.on_message
async def on_message(message: cl.Message):
    """Gère les messages texte entrants (utilise chaîne simple pour Phase 4)."""
    logging.info(f"\n--- Message Reçu (Texte) ---")
    logging.info(f"Input brut: '{message.content[:100]}...'")

    # AJOUT v4.1: Sanitization de l'input avant de le passer à la chaîne LLM
    try:
        sanitized_input = sanitize_input(message.content)
        # Optionnel: Loguer si l'input a été modifié par la sanitization
        # if sanitized_input != message.content:
        #     logging.warning(f"Input modifié par sanitization: '{sanitized_input[:100]}...'")
        logging.info(f"Input (Sanitized): '{sanitized_input[:100]}...'")
    except Exception as e:
        logging.error(f"Échec de la sanitization de l'input: {e}", exc_info=True)
        await cl.Message(content=f"Erreur interne lors du traitement de votre message.").send()
        return

    chain = cl.user_session.get("chain")
    if not chain:
        logging.error("[on_message] Erreur critique : Chaîne de traitement non trouvée dans la session.")
        await cl.Message(content="Erreur critique : Chaîne de traitement non initialisée pour cette session.").send()
        return

    msg = cl.Message(content="", author="Assistant Khaldounia"); await msg.send()
    final_answer = ""
    try:
        logging.info("[Run] Appel chaîne LLM simple...")
        # Utilise l'input sanitizé
        async for chunk in chain.astream(sanitized_input): # Passe directement l'input sanitizé
            await msg.stream_token(chunk)
            final_answer += chunk
        await msg.update()
        logging.info(f"[Run] Réponse LLM: '{final_answer[:100]}...'")

        logging.info("[Run] Génération audio XTTS...")
        try:
            # Appel de la fonction TTS (qui a maintenant retry/timeout/raise)
            audio_bytes = text_to_speech(final_answer)
            if audio_bytes:
                 await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send()
                 logging.info("[Run] Audio envoyé.")
            else:
                 # Cas où TTS renvoie None après retries (erreur interne non liée à l'appel)
                 logging.warning("[Run] Echec génération audio interne (après retries si échec appel).")
                 await cl.Message(content="(Erreur interne lors de la génération audio)", parent_id=msg.id, author="Alerte Système").send()

        # Capture l'erreur si text_to_speech échoue après tous les retries
        except Exception as tts_err:
            logging.error(f"[Run] Échec final appel TTS après retries: {tts_err}", exc_info=True)
            await cl.Message(content=f"(Erreur communication avec le service audio. Veuillez réessayer.)", parent_id=msg.id, author="Alerte Système").send()

    except Exception as e:
        # Erreur générale pendant le traitement du message par la chaîne
        error_message = f"ERREUR lors du traitement LLM: {e}"
        logging.error(f"[Run] {error_message}", exc_info=True)
        await msg.update(content="Désolé, une erreur s'est produite lors de la génération de la réponse.") # Message générique à l'utilisateur

    logging.info("--- Fin Traitement Message ---")

# ... (Autres décorateurs @cl.on_audio_end, @cl.on_file_upload restent non pertinents ici) ...

```

* **Action :** Sauvegardez ce fichier `app.py` initial révisé.

---

Fin de la Phase 3 (Révisée v4.1).

```markdown
---

## Phase 4 : Lancement Initial & Tests (Base Révisée v4.2)

**Objectif :** Démarrer pour la première fois les 4 services Docker de base (`ollama`, `xtts_server`, `chromadb`, `assistant_app`) avec les configurations sécurisées/stabilisées (versions figées, healthchecks, ports 127.0.0.1, volumes nommés XTTS) des phases précédentes. **Copier le fichier vocal de référence dans le volume XTTS.** Effectuer des tests simples pour valider que chaque service démarre sainement, communique, et que l'interface de base est fonctionnelle.

**Important :** Cette phase valide l'infrastructure minimale avant d'ajouter les fonctionnalités plus complexes. Soyez patient lors du premier démarrage (téléchargements) et notez que les `healthchecks` peuvent légèrement retarder le démarrage complet.

---

### Étape 4.1 : Se Placer dans le Dossier `assistant-core`

* **Explication :** Nécessaire pour exécuter les commandes `docker compose`.
* **Commande :**
    ```bash
    cd ~/projet-khaldounia/assistant-core
    ```
* **Commande (Vérification) :**
    ```bash
    pwd
    ```
    * *(Doit afficher `/home/VotreNomWSL/projet-khaldounia/assistant-core`)*.

---

### Étape 4.2 : Construire l'Image App et Démarrer les Services

* **Explication :** Lance tous les services définis dans `docker-compose.yml` (version révisée v4.2). L'option `--build` reconstruit l'image `assistant_app` (utilisateur non-root, dépendances MAJ, code initial durci). Docker va télécharger les images publiques spécifiées si nécessaire.
* **Note sur le démarrage :** Grâce aux `healthchecks` et à `depends_on: condition: service_healthy`, le conteneur `assistant_app` ne démarrera que lorsque `ollama`, `xtts_server`, et `chromadb` seront non seulement lancés mais aussi considérés comme "sains" (répondant aux vérifications). Cela peut prendre un peu plus de temps au premier démarrage, le temps que les services internes (comme le chargement du modèle XTTS) soient prêts.
* **COMMANDE IMPORTANTE :**
    ```bash
    docker compose up -d --build
    ```
* **🚨 ATTENTION - TEMPS D'ATTENTE & TÉLÉCHARGEMENTS IMPORTANTS (1ère fois) :**
    * **Soyez très patient !** Cette commande peut prendre **15 à 30 minutes ou plus** la première fois (Build `assistant_app`, téléchargement images Docker, téléchargements internes des modèles Embedding/TTS).
    * **Surveillance :** Utilisez un **second terminal WSL** pour suivre les logs (`docker logs -f nom_conteneur`) des différents services (`assistant_app`, `xtts_server`, `ollama`).

---

### **ÉTAPE 4.2 bis : COPIER LE FICHIER VOCAL DE RÉFÉRENCE (CRUCIAL)**

* **Explication :** Maintenant que le service `xtts_server` est démarré et que le volume nommé `khaldounia_xtts_speakers` existe, nous devons copier le fichier `speaker_ref.wav` (que vous aviez placé dans `~/projet-khaldounia/tts-data/speakers/` en Phase 1) à l'intérieur du conteneur, dans le répertoire mappé à ce volume.
* **Action :** Une fois que `docker compose up -d --build` est terminé et que le conteneur `xtts_server` est démarré (vérifiez avec `docker ps`), exécutez la commande suivante **depuis votre terminal WSL** (adaptez si votre fichier source est ailleurs) :
    ```bash
    docker cp ~/projet-khaldounia/tts-data/speakers/speaker_ref.wav xtts_server:/app/speakers/speaker_ref.wav
    ```
* **Vérification (Optionnelle) :** Vous pouvez vérifier que la copie a réussi :
    ```bash
    docker exec xtts_server ls -l /app/speakers
    ```
    *(Doit lister `speaker_ref.wav` avec une taille non nulle).*
* **Note :** Cette copie n'est à faire qu'une seule fois, car le fichier sera ensuite persistant dans le volume Docker `khaldounia_xtts_speakers`.

---

### Étape 4.3 : Vérifier l'État des Conteneurs et leur Santé

* **Explication :** Après la fin de `docker compose up` ET la copie du fichier vocal, vérifiez que tous les conteneurs tournent et sont considérés comme "sains" par Docker grâce aux `healthchecks`.
* **Commande :**
    ```bash
    docker ps
    ```
* **Résultat Attendu :** Doit lister **4 conteneurs** (`ollama`, `xtts_server`, `chromadb`, `assistant_app`). La colonne `STATUS` devrait indiquer `Up X minutes (healthy)` pour chacun après un certain temps (le temps défini dans `start_period` du `healthcheck`). Si un service démarre mais rencontre un problème, il pourrait afficher `(unhealthy)` ou `(health: starting)`.
* **En Cas de Problème :** Si un conteneur a un statut `Exited`, `Restarting`, ou `(unhealthy)` :
    * **Identifier le Conteneur :** Notez son nom.
    * **Consulter les Logs :** `docker logs --tail 100 [nom_du_conteneur]`
    * **Consulter les Détails du Healthcheck (si unhealthy) :** `docker inspect --format='{{json .State.Health}}' [nom_du_conteneur]` (Regardez la sortie de la dernière vérification dans `Log`).
    * **Erreurs Courantes Phase 4 (Révisée) :** Similaires à avant, mais vérifier aussi :
        * Échec du `healthcheck` (API ne répond pas comme attendu ? Timeout ?).
        * Problème de permission lié à l'utilisateur `appuser` dans `assistant_app` (moins probable avec le `chown` dans le Dockerfile, mais à vérifier si logs indiquent `Permission denied`).
        * Oubli de copier `speaker_ref.wav` (causera des erreurs dans `xtts_server` ou dans `app.py` plus tard).
    * **Stratégie :** Utilisez l'Annexe A avec les logs et l'état de santé.

---

### Étape 4.3bis : Tests de Connectivité API Directs (`curl` via 127.0.0.1)

* **Explication :** Vérifications rapides pour voir si les APIs des services sont accessibles depuis WSL via l'adresse locale `127.0.0.1`.
* **Test Ollama (API Generate) :**
    ```bash
    curl -X POST [http://127.0.0.1:11434/api/generate](https://www.google.com/search?q=http://127.0.0.1:11434/api/generate) -d '{"model":"mistral:latest","prompt":"Test Ollama API","stream":false}'
    ```
    *(Résultat Attendu : Réponse JSON avec clé `"response"`. Premier appel peut être lent).*
    *(Si erreur : Conteneur `ollama` non sain ? Problème port 11434 ? Modèle `mistral` non présent ?)*
* **Test ChromaDB (API Heartbeat) :**
    ```bash
    curl [http://127.0.0.1:8002/api/v1/heartbeat](https://www.google.com/search?q=http://127.0.0.1:8002/api/v1/heartbeat)
    ```
    *(Résultat Attendu : Réponse JSON `{"nanosecond heartbeat":...}`)*.
    *(Si erreur : Conteneur `chromadb` non sain ? Problème port 8002 ?)*
* **Test XTTS (Vérification Serveur / API Speakers) :**
    ```bash
    curl [http://127.0.0.1:8050/speakers](https://www.google.com/search?q=http://127.0.0.1:8050/speakers)
    ```
    *(Résultat Attendu : Réponse JSON, potentiellement `[]` ou listant `speaker_ref.wav` si l'API le permet. Le principal est de ne pas avoir "Connection refused" et que le service soit `(healthy)`).*
    *(Si erreur : Conteneur `xtts_server` non sain ? Oubli copie `speaker_ref.wav` (Étape 4.2bis) ? Problème chargement modèle TTS (VRAM ?) ?)*
* **En Cas de Problème `curl` :** Si une commande échoue, le service a un problème. Revérifiez `docker ps` (état de santé) et analysez les logs `docker logs [nom_du_conteneur]`.

---

### Étape 4.4 : Tester l'Accès à l'Interface Web Chainlit (via 127.0.0.1)

* **Action :** Ouvrez votre navigateur web sur Windows.
* **Action :** Allez à l'URL : `http://127.0.0.1:8001` (le port hôte local défini pour `assistant_app`).
* **Résultat Attendu :** L'interface Chainlit doit se charger. Le message d'accueil *"Bonjour Halim-IA ! Assistant (Phase 3 v4.2 - Test Initial) prêt."* doit apparaître.
* **En Cas de Problème :**
    * **Page inaccessible ("Ce site est inaccessible", ERR_CONNECTION_REFUSED...) :** Le conteneur `assistant_app` n'est pas démarré ou pas sain (`docker ps`) ? Problème avec le port 8001 ? Vérifiez les logs `docker logs assistant_app` (erreurs au démarrage de Chainlit, échec connexion dépendances ?).
    * **Page blanche, chargement infini, erreur UI :** Problème dans `app.py` (`@cl.on_chat_start`). Vérifiez logs `assistant_app` (erreurs Python, échec init globale Embeddings/Chroma ?).

---

### Étape 4.5 : Tester l'Interaction de Base (LLM + TTS)

* **Explication :** Test simple : Input Texte -> `app.py` (sanitized) -> Chaîne LLM Simple -> Ollama -> Réponse Texte -> `app.py` -> XTTS (avec retry/timeout) -> Réponse Audio.
* **Action :** Dans Chainlit (`http://127.0.0.1:8001`), tapez `Bonjour` ou `Raconte-moi une blague courte`.
* **Résultat Attendu :**
    1.  Réponse **texte** générée par Ollama.
    2.  Réponse **audio** générée par XTTS (avec la voix copiée en 4.2bis).
* **En Cas de Problème :**
    * **Pas de Réponse Texte / Erreur Texte :** Logs `assistant_app` (erreur dans `@cl.on_message`, appel chaîne, appel Ollama ?) ; Logs `ollama` (sain ? modèle chargé ? erreur GPU ?). Test `curl` Ollama (4.3bis).
    * **Texte OK, Pas de Son / Erreur Audio :** Logs `assistant_app` (erreur dans appel `text_to_speech` après retries ? `[ERREUR] Échec final appel TTS...` ?) ; Logs `xtts_server` (sain ? voit bien `speaker_ref.wav` dans `/app/speakers` ? erreur traitement TTS ? VRAM ?). Test `curl` XTTS (4.3bis). Fichier `speaker_ref.wav` bien copié (4.2bis) ?
    * **Très Lent :** Normal au premier appel LLM/TTS. Si persistant, vérifier ressources (`nvidia-smi`, `htop` dans WSL).

---

### **Étape 4.6: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** Base fonctionnelle établie et testée. Bon moment pour sauvegarder avant d'ajouter Whisper.
* **Méthode 1 : Copie Simple (Facile)**
    * Arrêt : `docker compose down`
    * Copie : `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase4`
    * Redémarrage : `docker compose up -d`
* **Méthode 2 : Commit Git (Préférable)**
    * `git add .`
    * `git commit -m "Fin de la Phase 4 (v4.2) - Base LLM/TTS/RAG fonctionnelle (avec healthchecks, ports 127.0.0.1, volumes nommés XTTS)"`

---

Fin de la Phase 4 (Révisée v4.2).


```markdown
---

## Phase 5 : Ajout Reconnaissance Vocale (STT/Whisper - Révisée v4.2)

**Objectif :** Intégrer la reconnaissance vocale (Speech-to-Text) via un service Docker Whisper API (version figée, healthcheck, port local). Mettre à jour l'application `assistant_app` pour permettre l'interaction orale (activation entrée audio, appel API Whisper durci, gestion réponse).

**Prérequis :** Phase 4 terminée et idéalement sauvegardée. Les services de base sont fonctionnels (`(healthy)`). Le fichier vocal de référence a été copié dans le volume XTTS.

**Approche :** Ajout du service `whisper_server` dans `docker-compose.yml`. Mise à jour de `app.py` pour activer l'entrée audio Chainlit, implémenter la fonction `transcribe_audio` (avec retries/timeout) appelant l'API Whisper, et ajouter le gestionnaire `@cl.on_audio_end`. La chaîne de traitement reste la chaîne RAG/LLM simple définie précédemment.

---

### Étape 5.1 : Mettre à jour le Fichier `docker-compose.yml` (Ajout Whisper)

* **Explication :** Ajout du 5ème service (`whisper_server` avec healthcheck, port localisé) et mise à jour de `assistant_app` (variable d'environnement `WHISPER_SERVER_URL` et dépendance `depends_on` saine). Les configurations des services précédents (Ollama, XTTS, ChromaDB) sont conservées (versions figées, healthchecks, ports 127.0.0.1, volumes nommés XTTS).
* **Action :** Ouvrez `~/projet-khaldounia/assistant-core/docker-compose.yml`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```yaml
# Fichier: docker-compose.yml (Révisé v4.2 - Ajout Whisper)

version: '3.8'

services:
  # --- Ollama ---
  ollama:
    image: ollama/ollama:0.1.41 # FIGÉE
    container_name: ollama
    deploy: { resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } } }
    volumes: [ khaldounia_ollama_data:/root/.ollama ]
    ports: [ "127.0.0.1:11434:11434" ] # Accès local
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:11434/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

  # --- XTTS ---
  xtts_server:
    image: ghcr.io/coqui-ai/tts:v0.22.0 # FIGÉE
    container_name: xtts_server
    command: > tts-server --model_name tts_models/multilingual/multi-dataset/xtts_v2 --use_cuda true --port 8020 --speaker_wav /app/speakers/speaker_ref.wav
    ports: [ "127.0.0.1:8050:8020" ] # Accès local
    volumes:
      - khaldounia_xtts_cache:/root/.local/share/tts # Volume Nommé
      - khaldounia_xtts_speakers:/app/speakers      # Volume Nommé
    environment: [ NVIDIA_VISIBLE_DEVICES=all, NVIDIA_DRIVER_CAPABILITIES=compute,utility, COQUI_TOS_AGREED=1 ]
    deploy: # Accès GPU (Optionnel - commenter si VRAM limitée)
       resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } }
    shm_size: 8gb
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"], interval: 30s, timeout: 10s, retries: 3, start_period: 180s }

  # --- ChromaDB ---
  chromadb:
    image: chromadb/chroma:0.5.0 # FIGÉE
    container_name: chromadb
    environment: { IS_PERSISTENT: 'TRUE', ALLOW_RESET: 'TRUE' }
    volumes: [ khaldounia_chroma_data:/chroma ]
    ports: [ "127.0.0.1:8002:8000" ] # Accès local
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"], interval: 30s, timeout: 10s, retries: 3, start_period: 30s }

  # --- Whisper (STT) --- NOUVEAU SERVICE ---
  whisper_server:
    image: ghcr.io/distributors/whisper-asr-webservice:1.1.0 # FIGÉE (Exemple)
    container_name: whisper_server
    environment:
      - ASR_MODEL=small # Modèle Whisper (tiny, base, small, medium, large)
      - ASR_ENGINE=openai_whisper # Moteur
      # Optionnel: Décommentez pour GPU si VRAM suffisante (MEDIUM+)
      # - NVIDIA_VISIBLE_DEVICES=all
    ports: [ "127.0.0.1:9000:9000" ] # API Whisper (Accès local seulement)
    # deploy: # Décommentez pour GPU
    #   resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } }
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/health"] # Endpoint standard
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 90s # Laisse le temps de charger le modèle Whisper

  # --- Assistant App (Mis à jour) ---
  assistant_app:
    build: { context: ./app }
    container_name: assistant_app
    ports: [ "127.0.0.1:8001:8000" ] # Accès local
    volumes:
        - ./app:/app
        - embedding_cache:/app/embedding_cache
    environment:
      - OLLAMA_HOST=http://ollama:11434
      - XTTS_SERVER_URL=http://xtts_server:8020
      - CHROMADB_HOST=chromadb
      - CHROMADB_PORT=8000
      - WHISPER_SERVER_URL=http://whisper_server:9000 # <<< VARIABLE AJOUTÉE <<<
      # - STABLE_DIFFUSION_API_URL=... # Pour Phase 7
    depends_on: # <<< MIS A JOUR - Attend dépendances saines <<<
      ollama: { condition: service_healthy }
      xtts_server: { condition: service_healthy }
      chromadb: { condition: service_healthy }
      whisper_server: { condition: service_healthy } # Ajout dépendance Whisper saine
      # - stable_diffusion_webui: { condition: service_healthy } # Pour Phase 7
    stdin_open: true
    tty: true
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

# --- Réseau Docker ---
networks:
  assistant_network: { driver: bridge, name: assistant_network }

# --- Volumes Docker ---
volumes:
  khaldounia_ollama_data: { driver: local }
  khaldounia_chroma_data: { driver: local }
  embedding_cache: { driver: local }
  khaldounia_xtts_cache: { driver: local }
  khaldounia_xtts_speakers: { driver: local }
  # Volumes SD commentés
```

* **Action :** Sauvegardez le fichier `docker-compose.yml`.

---

### Étape 5.2 : Vérifier le Fichier `requirements.txt`

* **Explication :** Normalement, aucune nouvelle dépendance Python n'est requise juste pour appeler l'API Whisper via `requests` (qui est déjà inclus et figé, tout comme `tenacity`).
* **Action :** Vérifiez que `requirements.txt` dans `assistant-core/app` est identique à celui finalisé en Phase 3 (Révisée v4.1).

---

### Étape 5.3 : Mettre à jour le Fichier `app.py` (Intégration STT Durcie)

* **Explication :** Active la fonction `transcribe_audio` (avec retries/timeout), ajoute l'input audio dans `@cl.on_chat_start`, et implémente le gestionnaire `@cl.on_audio_end`. La chaîne de traitement reste la chaîne RAG/LLM simple (pas encore d'Agent). Le code intègre les améliorations de robustesse (logging, retries, timeouts, sanitization).
* **Action :** Ouvrez `app.py` dans `assistant-core/app`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Révisé v4.2 pour Phase 5 - Intégration STT Durcie)
# Rôle: Application Khaldounia (Base + RAG Simple + STT - Robuste)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports pour Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain
logging.info("--- Début Importation Langchain (Phase 5 Rev 4.2) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# ... (Autres imports pour phases futures) ...
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale ---
logging.info("--- Début Chargement Configuration ---")
OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434")
XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020")
CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb")
CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000")
WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000") # AJOUT URL Whisper
# ... (autres variables) ...
OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest")
EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"
EMBEDDING_CACHE_DIR = Path("/app/embedding_cache")
SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads")
logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}")
logging.info("--- Fin Chargement Configuration ---")

# --- Fonctions Utilitaires ---

# --- Fonction STT (Activée pour Phase 5 - avec retry/timeout) ---
@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None:
    """Appelle l'API Whisper pour transcrire l'audio avec retry/timeout."""
    api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"
    files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}
    logging.info(f"[STT] Appel API Whisper: {api_url}")
    try:
        response = requests.post(api_url, files=files, timeout=60) # Timeout 60s
        response.raise_for_status() # Vérifie erreurs HTTP
        result = response.json()
        transcribed_text = result.get("text", "").strip()
        logging.info(f"[STT] Whisper a retourné: '{transcribed_text[:50]}...'")
        return transcribed_text if transcribed_text else None # Retourne None si vide
    except requests.exceptions.Timeout as e:
        logging.warning(f"[STT] Timeout lors de l'appel API Whisper ({e}). Tenacity va réessayer...")
        raise
    except requests.exceptions.RequestException as e:
        logging.warning(f"[STT] Erreur requête API Whisper ({e}). Tenacity va réessayer...")
        raise
    except Exception as e:
        logging.error(f"[STT] Erreur inattendue (non-retryable): {e}", exc_info=True)
        return None
# --- FIN Fonction STT ---

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None:
    # ... (Implémentation text_to_speech durcie de Phase 3 Révisée v4.1) ...
    api_url = f"{XTTS_SERVER_URL}/generate"
    payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}
    logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'")
    try:
        response = requests.post(api_url, json=payload, timeout=180) # Timeout adapté pour TTS
        response.raise_for_status()
        data = response.json()
        audio_base64 = data.get("audio_base64")
        if audio_base64:
            logging.info("[TTS] Audio reçu avec succès.")
            return base64.b64decode(audio_base64)
        else:
            logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'. Réponse: {data}")
            return None
    except requests.exceptions.Timeout as e:
         logging.warning(f"[TTS] Timeout lors de l'appel API ({e}). Tenacity va réessayer...")
         raise
    except requests.exceptions.RequestException as e:
        logging.warning(f"[TTS] Erreur requête API ({e}). Tenacity va réessayer...")
        raise
    except Exception as e:
        logging.error(f"[TTS] Erreur inattendue (non-retryable): {e}", exc_info=True)
        return None

# --- Fonctions pour extensions futures (Placeholders) ---
# ... (generate_image_tool, notes, RAG etc. restent placeholders) ...
def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] generate_image_tool non actif."); return "Fonctionnalité désactivée."
def _is_path_safe(requested_path: Path) -> bool: return False
def list_notes_safe() -> str: logging.warning("[NOTES] list_notes_safe non actif."); return "Action désactivée."
def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] save_note_safe non actif."); return "Action désactivée."
def read_note_safe(filename: str) -> str: logging.warning("[NOTES] read_note_safe non actif."); return "Action désactivée."
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée."

# --- Initialisation Globale (Identique Phase 3 Révisée v4.1) ---
# ... (Code d'init globale pour embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat (Active STT)."""
    logging.info("\n--- Nouvelle Session Chat (Phase 5 v4.2 - STT Actif) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA (Embeddings/Chroma) non initialisés.").send(); return

    try:
        # Initialiser LLM et stocker composants IA
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.7 )
        cl.user_session.set("llm", llm)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
        logging.info("[Session Start] LLM et Retriever prêts.")

        # Chaîne RAG simple (même si non activement utilisée par un outil dédié ici)
        # Sera utilisée par process_and_respond
        # Définit comment le contexte (vide pour l'instant) et la question sont formatés
        rag_prompt_template = """Contexte (si disponible):
{context}

Question: {question}
Réponse:"""
        rag_prompt = ChatPromptTemplate.from_template(rag_prompt_template)

        # Fonction pour formater les documents récupérés (sera vide au début)
        def format_docs(docs): return "\n\n".join(doc.page_content for doc in docs) if docs else "Aucun contexte pertinent trouvé."

        # Wrapper pour l'appel au retriever (gère le cas où il n'est pas prêt)
        def get_retriever_invoke(query):
             retriever_session = cl.user_session.get("retriever")
             # Lance la recherche, retourne une liste vide si le retriever n'est pas là
             # ou si aucun document n'est trouvé.
             return retriever_session.invoke(query) if retriever_session else []

        # Définition de la chaîne RAG/LLM simple
        rag_chain_simple = (
             {
                 "context": RunnableLambda(lambda x: format_docs(get_retriever_invoke(x["question_sanitized"]))), # Utilise la question sanitizée pour le RAG aussi
                 "question": RunnableLambda(lambda x: x["question_sanitized"]) # Passe la question sanitizée au prompt
             }
             | rag_prompt
             | llm
             | StrOutputParser()
        )
        cl.user_session.set("rag_chain_simple", rag_chain_simple) # Stocke la chaîne simple
        logging.info("[Session Start] Chaîne RAG/LLM simple prête.")


        # --- MODIFICATION PHASE 5 : Activer input audio ---
        logging.info("[Session Start] Configuration UI: Inputs Texte ET Audio.")
        await cl.ChatSettings(
            inputs=[
                cl.TextInput(id="text_input", label="Entrez votre message...", initial=""),
                # Ajoute le widget d'enregistrement audio
                cl.AudioInput(
                    id="audio_input",
                    label="Cliquez pour parler...",
                    # tooltip="Enregistrez votre question ici" # Optionnel
                    )
            ]
        ).send()
        # --- FIN MODIFICATION PHASE 5 ---

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2). Vous pouvez parler ou écrire.", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Initialisée et Prête (avec STT) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# Fonction centrale de traitement (utilise rag_chain_simple pour l'instant)
async def process_and_respond(input_content_sanitized: str):
    """Traite texte (original ou transcrit, déjà sanitizé) via chaîne RAG/LLM simple, répond texte+audio."""
    logging.info(f"\n--- Traitement Input (Sanitized) ---")
    logging.info(f"Input: '{input_content_sanitized[:60]}...'")
    rag_chain = cl.user_session.get("rag_chain_simple") # Utilise la chaîne simple
    if not rag_chain: logging.error("[Process] Erreur: Chaîne RAG simple non prête."); await cl.Message(content="Erreur: Chaîne de traitement non prête.").send(); return

    # Prompt=input brut original serait mieux, mais on ne l'a plus ici facilement. Utilise sanitizé pour l'instant.
    msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send()
    final_answer = ""
    try:
        logging.info("[Run] Appel Chaîne RAG/LLM simple...")
        # Passe l'input sanitizé à la chaîne qui attend "question_sanitized"
        async for chunk in rag_chain.astream({"question_sanitized": input_content_sanitized}):
            await msg.stream_token(chunk); final_answer += chunk
        await msg.update(); logging.info(f"[Run] Réponse LLM: '{final_answer[:100]}...'")

        # TTS
        logging.info("[Run] Génération audio XTTS...");
        try:
            audio_bytes = text_to_speech(final_answer)
            if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé.")
            else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", parent_id=msg.id, author="Alerte Système").send()
        except Exception as tts_err: # Erreur après retries
            logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", parent_id=msg.id, author="Alerte Système").send()

    except Exception as e: error_message = f"ERREUR process/respond: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne lors de la génération de la réponse.")
    logging.info("--- Fin Traitement Input ---")


@cl.on_message # Gère INPUT TEXTE
async def on_text_message(message: cl.Message):
    logging.info(f"\n--- Msg Texte UI ---")
    logging.info(f"Input brut: '{message.content[:60]}...'")
    try:
        sanitized_input = sanitize_input(message.content)
        logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'")
        await process_and_respond(sanitized_input) # Appelle la logique centrale
    except Exception as e:
        logging.error(f"Erreur lors de la sanitization/traitement du message texte: {e}", exc_info=True)
        await cl.Message(content="Erreur interne lors du traitement de votre message.").send()


# --- AJOUT PHASE 5 : Gestion INPUT AUDIO ---
@cl.on_audio_chunk # Stream audio - pas utilisé ici pour Whisper API
async def on_audio_chunk(chunk: cl.AudioChunk): pass

@cl.on_audio_end # Appelée quand l'enregistrement audio est terminé
async def on_audio_end(audio: cl.Audio):
    """Traite l'audio complet enregistré via l'interface Chainlit."""
    logging.info(f"\n--- Audio Reçu ---")
    logging.info(f"Nom: {audio.name}, Taille: {len(audio.content)} bytes, Mime: {audio.mime}")

    transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True);
    await transcript_msg.send()

    transcribed_text = None
    try:
        # Appeler la fonction de transcription (API Whisper) - qui a retry/timeout
        transcribed_text_raw = transcribe_audio(audio.content)

        if transcribed_text_raw is not None: # Peut être vide si Whisper ne détecte rien
             # Sanitize le texte transcrit avant de l'utiliser
             transcribed_text = sanitize_input(transcribed_text_raw)
             logging.info(f"Texte transcrit (Sanitized): '{transcribed_text[:60]}...'")
             await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text}\"")
             # Envoyer le texte transcrit ET sanitizé à la fonction de traitement principale
             await process_and_respond(transcribed_text)
        else:
             # Cas où la transcription réussit mais retourne None ou vide
             logging.warning("[STT] Transcription retournée vide ou None.")
             await transcript_msg.update(content="Je n'ai pas pu transcrire l'audio ou rien n'a été dit. Veuillez réessayer.")

    except Exception as e: # Erreur finale après retries de transcribe_audio
        logging.error(f"[STT] Échec final appel/traitement Whisper: {e}", exc_info=True)
        await transcript_msg.update(content="Désolé, la transcription a échoué après plusieurs tentatives. Veuillez réessayer ou taper votre message.")

    logging.info("--- Fin Traitement Audio ---")
# --- FIN AJOUT PHASE 5 ---

# --- Section RAG Upload (Phase 9) ---
# @cl.on_file_upload(...) sera défini plus tard
# --- Fin Section RAG Upload ---

```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 5.4 : Redémarrer l'Environnement avec le Service Whisper

* **Explication :** Arrêt et redémarrage pour prendre en compte le nouveau service `whisper_server` et les modifications de `assistant_app`.
* **Action :** Terminal dans `assistant-core` :
    ```bash
    docker compose down
    ```
* **Action :** Reconstruire `assistant_app` (car `app.py` a changé) et démarrer tous les services (Ollama, XTTS, ChromaDB, Whisper, App) :
    ```bash
    docker compose up -d --build
    ```
* **Attente :** Téléchargement de l'image Whisper (`ghcr.io/distributors/whisper-asr-webservice:1.1.0` ou tag choisi) la première fois. Le démarrage peut prendre un peu de temps le temps que tous les services deviennent `(healthy)`.

---

### Étape 5.5 : Tester la Reconnaissance Vocale

* **Action :** Vérifiez que les **5 conteneurs** (`ollama`, `xtts_server`, `chromadb`, `whisper_server`, `assistant_app`) sont bien `Up` et idéalement `(healthy)` :
    ```bash
    docker ps
    ```
* **Action :** Ouvrez/Actualisez l'interface Chainlit : `http://127.0.0.1:8001`.
* **Action :** Cliquez sur l'icône de microphone, autorisez l'accès si demandé, parlez (ex: "Quelle est la capitale de la France ?"), puis cliquez à nouveau pour arrêter.
* **Résultat Attendu :**
    1.  Message "*Transcription audio en cours...*".
    2.  Remplacé par "Vous avez dit : "[Votre texte transcrit et sanitizé]"".
    3.  L'assistant traite ce texte via la chaîne simple et répond (texte + audio).
* **En Cas de Problème :**
    * **Pas d'icône micro / Input audio non activé :** Vérifiez `cl.AudioInput` dans `@cl.on_chat_start` (`app.py`). Avez-vous bien fait `--build` ?
    * **Enregistrement échoue :** Problème navigateur/permissions micro OS.
    * **Message "Transcription échouée" / Pas de texte transcrit :**
        1.  Logs `assistant_app` : Erreur (`[ERREUR]`) dans `@cl.on_audio_end` ou `transcribe_audio` ? Timeout après retries ?
        2.  Logs `whisper_server` (`docker logs whisper_server`) : Démarré et `(healthy)` ? Erreur interne ?
        3.  Test API Whisper : `curl http://127.0.0.1:9000/health` doit répondre `OK`.
    * **Transcription OK, mais pas de réponse LLM/TTS :** Problème dans `process_and_respond` (voir débogage Phase 4.5).

---

### **Étape 5.6: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** STT fonctionnel sur une base durcie. Sauvegarde avant l'Agent/WebSearch.
* **Méthode 1 : Copie Simple**
    * `docker compose down`
    * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase5`
    * `docker compose up -d`
* **Méthode 2 : Commit Git**
    * `git add .`
    * `git commit -m "Fin de la Phase 5 (v4.2) - STT Whisper fonctionnel (base durcie)"`

---

Fin de la Phase 5 (Révisée v4.2).

```markdown
---

## Phase 6 : Ajout de la Recherche Web (Agent - Révisée v4.2)

**Objectif :** Mettre en place une architecture d'Agent LangChain (type ReAct) dans l'application `app.py` (version durcie). Cet agent sera capable d'utiliser des "Outils". Dans cette phase, nous activons **uniquement** l'outil de recherche web (DuckDuckGo) pour permettre à l'assistant de chercher des informations à jour sur Internet.

**Prérequis :** Phase 5 terminée et idéalement sauvegardée. STT/Whisper est fonctionnel sur la base durcie (v4.2).

**Approche :** Vérification des dépendances Python (normalement OK depuis Phase 3 révisée). Modification majeure de `app.py` pour initialiser l'`AgentExecutor` avec seulement `search_tool` actif dans `@cl.on_chat_start`. Adaptation de la logique de réponse (`process_and_respond`) pour utiliser cet agent.

---

### Étape 6.1 : Vérifier le Fichier `requirements.txt`

* **Explication :** S'assurer que les bibliothèques nécessaires pour les agents LangChain (`langchain`, `langchainhub`) et l'outil DuckDuckGo (`duckduckgo-search`) sont bien présentes avec les versions figées définies en Phase 3 (Révisée v4.1).
* **Action :** Vérifiez le contenu de `~/projet-khaldounia/assistant-core/app/requirements.txt`. Il doit être identique à la version finale de la Phase 3 (Révisée v4.1). Aucune nouvelle dépendance n'est normalement requise pour cette phase.

---

### Étape 6.2 : Mettre à jour le Fichier `app.py` (Intégration Agent et Outil WebSearch)

* **Explication :** C'est le cœur de cette phase. Nous remplaçons la chaîne RAG/LLM simple par un Agent Executor dans `@cl.on_chat_start`. Cet agent est configuré pour n'avoir accès qu'à l'outil `search_tool`. La fonction `process_and_respond` utilisera désormais cet agent. Les fonctions durcies (`transcribe_audio`, `text_to_speech`) et la sanitization sont conservées.
* **Action :** Ouvrez `app.py` dans `~/projet-khaldounia/assistant-core/app/`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Révisé v4.2 pour Phase 6 - Agent + WebSearch)
# Rôle: Application Khaldounia (Agent ReAct avec WebSearch actif - Robuste)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain (Ajout Agent/Tools)
logging.info("--- Début Importation Langchain (Phase 6 Rev 4.2) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
# --- Ajout Agent/Tools ---
from langchain.agents import AgentExecutor, create_react_agent, Tool
from langchain_community.tools import DuckDuckGoSearchRun # Outil WebSearch
from langchain import hub # Pour charger les prompts d'agent standards
# --- Fin Ajout Agent/Tools ---
# Imports Core (gardés si besoin ailleurs)
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# ... (Autres imports pour phases futures) ...
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale (inchangée) ---
# ... (coller la config globale inchangée, lit les .env) ...
logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


# --- Fonctions Utilitaires (Durcies) ---

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None:
    # ... (Implémentation transcribe_audio durcie de Phase 5 Révisée v4.2) ...
    api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url}"); try: response = requests.post(api_url, files=files, timeout=60); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:50]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None:
    # ... (Implémentation text_to_speech durcie de Phase 3/5 Révisée v4.2) ...
    api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue: {e}", exc_info=True); return None

# --- Placeholders pour les autres outils ---
def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] generate_image_tool non actif."); return "Fonctionnalité désactivée."
def _is_path_safe(requested_path: Path) -> bool: return False # Sera implémenté en Phase 8
def list_notes_safe() -> str: logging.warning("[NOTES] list_notes_safe non actif."); return "Action désactivée."
def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] save_note_safe non actif."); return "Action désactivée."
def read_note_safe(filename: str) -> str: logging.warning("[NOTES] read_note_safe non actif."); return "Action désactivée."
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée." # Sera implémenté en Phase 10

# --- Initialisation Globale (inchangée) ---
# ... (Code d'init globale pour embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}

# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat -> Mise en place de l'Agent avec WebSearch."""
    logging.info("\n--- Nouvelle Session Chat (Phase 6 v4.2 - Agent WebSearch Actif) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA (Embeddings/Chroma) non initialisés.").send(); return

    try:
        # Initialiser LLM (Température plus basse souvent recommandée pour ReAct)
        logging.info("[Session Start] Initialisation LLM pour Agent...")
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0 ) # Timeout long pour LLM

        # --- AGENT SETUP ---
        logging.info("[Session Start] Configuration de l'Agent ReAct...")
        # 1. Définir TOUS les outils potentiels (pour référence future)
        search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
        # Placeholders pour les autres outils qui seront activés plus tard
        image_tool = Tool( name="Generateur Image", func=generate_image_tool, description="Créé une image." )
        list_notes_tool = Tool( name="Lister Notes", func=list_notes_safe, description="Liste les notes." )
        save_note_tool = Tool( name="Sauvegarder Note", func=save_note_safe, description="Sauve une note." )
        read_note_tool = Tool( name="Lire Note", func=read_note_safe, description="Lit une note." )
        rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs." )

        # 2. Sélectionner outils ACTIFS pour CETTE phase (WebSearch SEULEMENT)
        active_tools = [search_tool] # <<<=== SEUL OUTIL ACTIF ICI
        logging.info(f"[Session Start] Outils actifs pour l'agent: {[tool.name for tool in active_tools]}")

        # 3. Prompt ReAct (standard depuis Langchain Hub)
        try:
            react_prompt = hub.pull("hwchase17/react")
            logging.info("[Session Start] Prompt ReAct chargé depuis Hub.")
        except Exception as hub_error:
            logging.critical(f"[Session Start] Erreur chargement prompt Hub: {hub_error}", exc_info=True)
            await cl.Message(content=f"Erreur critique: Impossible de charger le prompt de l'agent. Installation Langchain Hub OK ?").send(); return

        # 4. Créer l'agent ReAct
        try:
            agent = create_react_agent(llm, active_tools, react_prompt)
            logging.info("[Session Start] Agent ReAct créé.")
        except Exception as agent_create_error:
            logging.critical(f"[Session Start] Erreur création agent: {agent_create_error}", exc_info=True)
            await cl.Message(content=f"Erreur critique: Impossible de créer l'agent.").send(); return

        # 5. Créer l'AgentExecutor
        agent_executor = AgentExecutor(
            agent=agent,
            tools=active_tools, # Utilise SEULEMENT les outils actifs
            verbose=True, # Très utile pour voir la pensée de l'agent dans les logs !
            handle_parsing_errors=True, # Tente de corriger les erreurs de formatage LLM
            max_iterations=10 # Limite de sécurité pour éviter boucles infinies
        )
        cl.user_session.set("agent_executor", agent_executor) # Stocke l'EXECUTOR
        logging.info("[Session Start] Agent Executor (WebSearch seul) initialisé.")
        # --- FIN AGENT SETUP ---

        # Stocker retriever/vector store pour upload (Phase 9)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

        # Activer Inputs (Texte/Audio) & Upload Fichiers (inchangé depuis Phase 5)
        logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
        # Note: L'activation de l'upload est ici pour préparer Phase 9, même si l'outil RAG n'est pas actif
        cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2 - avec Recherche Web).", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Prête (Agent WebSearch) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# MODIFICATION : process_and_respond utilise maintenant l'Agent Executor
async def process_and_respond(input_content_sanitized: str):
    """Traite l'entrée utilisateur (sanitizée) via l'Agent Executor et renvoie la réponse (texte+audio)."""
    logging.info(f"\n--- Traitement Input via Agent ---")
    logging.info(f"Input (Sanitized): '{input_content_sanitized[:60]}...'")
    agent_executor = cl.user_session.get("agent_executor")
    if not agent_executor:
        logging.error("[Process] Agent Executor non trouvé dans la session.")
        await cl.Message(content="Erreur critique : Agent non initialisé pour cette session.").send(); return

    msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send()
    final_answer_text = ""; image_to_display = None
    try:
        logging.info("[Run] Appel Agent Executor (ainvoke)...")
        # Invoque l'agent avec l'input sanitizé. L'agent décide d'utiliser un outil ou non.
        # Le prompt ReAct standard attend la clé "input".
        response = await agent_executor.ainvoke({"input": input_content_sanitized})
        final_answer_text = response.get("output", "Désolé, je n'ai pas pu obtenir de réponse structurée.")
        logging.info(f"[Run] Réponse brute de l'Agent: '{final_answer_text[:100]}...'")

        # Gestion si un outil (futur) retourne un chemin d'image
        if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"):
            try:
                relative_image_path = final_answer_text.split(":", 1)[1]
                image_path_in_container = Path("/app") / relative_image_path
                if image_path_in_container.is_file():
                    image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline")
                    final_answer_text = f"Voici l'image demandée." # Réponse texte associée
                    logging.info(f"[Run] Outil Image a retourné un fichier valide: {relative_image_path}")
                else:
                    logging.error(f"[Run] Chemin image retourné par outil invalide ou fichier non trouvé: {image_path_in_container}")
                    final_answer_text += " (Erreur: fichier image introuvable après génération)"
            except Exception as img_err:
                 logging.error(f"[Run] Erreur traitement retour Outil Image: {img_err}", exc_info=True)
                 final_answer_text = "Une erreur s'est produite lors de l'affichage de l'image générée."

        # Met à jour le message Chainlit avec la réponse finale texte
        await msg.update(content=final_answer_text)
        logging.info(f"[Run] Réponse finale affichée: '{final_answer_text[:100]}...'")

        # Afficher l'image si elle existe (ne devrait pas arriver ici en Phase 6)
        if image_to_display:
            await cl.Message(content="", elements=[image_to_display], author="Image Générée").send()

        # Génération TTS de la réponse finale texte
        logging.info("[Run] Génération audio XTTS...");
        try:
            audio_bytes = text_to_speech(final_answer_text)
            if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé.")
            else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id).send()
        except Exception as tts_err: # Erreur après retries
            logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id).send()

    except Exception as e:
        # Capture les erreurs pendant l'exécution de l'agent
        error_message = f"ERREUR lors de l'exécution de l'agent: {e}"
        logging.error(f"[Run] {error_message}", exc_info=True)
        await msg.update(content="Désolé, une erreur interne s'est produite lors de la réflexion.") # Message générique

    logging.info("--- Fin Traitement Input ---")


# on_message (Appelle process_and_respond via l'Agent)
@cl.on_message
async def on_text_message(message: cl.Message):
    logging.info(f"\n--- Msg Texte UI ---")
    logging.info(f"Input brut: '{message.content[:60]}...'")
    try:
        sanitized_input = sanitize_input(message.content)
        logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'")
        await process_and_respond(sanitized_input) # Appelle la logique centrale via Agent
    except Exception as e:
        logging.error(f"Erreur sanitization/traitement message texte: {e}", exc_info=True)
        await cl.Message(content="Erreur interne traitement message.").send()

# on_audio_end (Appelle process_and_respond via l'Agent après transcription)
@cl.on_audio_end
async def on_audio_end(audio: cl.Audio):
    logging.info(f"\n--- Audio Reçu ---")
    logging.info(f"Taille: {len(audio.content)} bytes, mime: {audio.mime}")
    transcript_msg = cl.Message(content="*Transcription...*", author="Système", disable_feedback=True); await transcript_msg.send()
    transcribed_text_sanitized = None
    try:
        transcribed_text_raw = transcribe_audio(audio.content) # Appel durci
        if transcribed_text_raw is not None:
             transcribed_text_sanitized = sanitize_input(transcribed_text_raw)
             logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'")
             await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\"")
             await process_and_respond(transcribed_text_sanitized) # Traite via l'Agent
        else:
             logging.warning("[STT] Transcription retournée vide ou None.")
             await transcript_msg.update(content="Pas de son détecté ou transcription vide.")
    except Exception as e: # Erreur finale transcription ou sanitization
        logging.error(f"[STT] Échec final transcription/sanitization: {e}", exc_info=True)
        await transcript_msg.update(content="Échec transcription après tentatives.")
    logging.info("--- Fin Traitement Audio ---")


# on_file_upload (Défini mais non utilisé par l'Agent dans cette phase)
@cl.on_file_upload(accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)
async def on_file_upload(files: list[cl.File]):
    # La logique d'upload RAG sera implémentée/activée en Phase 9/10
    logging.info(f"\n--- Fichiers Reçus (Logique Upload Phase 9+ non active) ---")
    if not files: return
    await cl.Message(content=f"Réception de {len(files)} fichier(s). Traitement d'indexation RAG non implémenté dans cette phase.").send()
    # Ici, normalement, le code de chargement/split/indexation de Phase 9 irait
    # Pour l'instant, on ne fait rien avec les fichiers

```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 6.3 : Redémarrer l'Environnement avec l'Agent

* **Explication :** Arrêt et redémarrage pour que le service `assistant_app` utilise le nouveau code `app.py` qui inclut l'Agent Executor (avec seulement l'outil WebSearch actif pour l'instant).
* **Action :** Terminal dans `assistant-core` :
    ```bash
    docker compose down
    ```
* **Action :** Reconstruire `assistant_app` (car `app.py` a changé) et démarrer tous les services (5 services maintenant) :
    ```bash
    docker compose up -d --build
    ```

---

### Étape 6.4 : Tester la Recherche Web via l'Agent

* **Action :** Vérifiez les 5 conteneurs (`docker ps`), ils devraient tous être `Up` et `(healthy)`.
* **Action :** Ouvrez/Actualisez Chainlit : `http://127.0.0.1:8001`.
* **Test 1 (Web) :** Demandez une information nécessitant une recherche web (texte ou voix), par exemple : "Quel temps fait-il à Épinay-sur-Seine aujourd'hui ?"
* **Résultat Attendu :**
    1.  Dans les logs `assistant_app` (`docker logs -f assistant_app`), vous devriez voir les étapes de pensée de l'agent (`verbose=True`), incluant l'**Action: Recherche Web DuckDuckGo** et l'**Observation:** (résultat de la recherche).
    2.  Dans l'interface Chainlit, l'assistant doit fournir une réponse à jour basée sur la recherche web (texte + audio).
* **Test 2 (Sans Web) :** Demandez une salutation simple comme "Bonjour".
* **Résultat Attendu :** L'agent doit répondre directement sans utiliser l'outil de recherche (visible dans les logs comme une **Action: Final Answer** sans étape d'observation d'outil).
* **En Cas de Problème :** Si l'agent ne semble pas utiliser l'outil web ou si la recherche échoue :
    * Vérifiez les logs `assistant_app` (`verbose=True`) pour comprendre la "Thought" de l'agent et détecter des erreurs de parsing ("Could not parse LLM output:") ou d'appel de l'outil DuckDuckGo.
    * Assurez-vous que le conteneur `assistant_app` a bien accès à Internet (généralement le cas via Docker Desktop/WSL2).
    * Consultez Annexe A.

---

### **Étape 6.5: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** L'Agent avec recherche web est fonctionnel sur la base durcie. Sauvegarde avant la génération d'images.
* **Méthode 1 : Copie Simple**
    * `docker compose down`
    * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase6`
    * `docker compose up -d`
* **Méthode 2 : Commit Git**
    * `git add .`
    * `git commit -m "Fin de la Phase 6 (v4.2) - Agent ReAct avec WebSearch fonctionnel (base durcie)"`

---

Fin de la Phase 6 (Révisée v4.2).

```markdown
---

## Phase 7 : Ajout Génération Image (Stable Diffusion - Révisée v4.2)

**Objectif :** Activer l'outil de génération d'images dans l'agent, en utilisant le service Docker `linuxserver/stable-diffusion-webui` (basé sur A1111) avec une version d'image figée, des volumes nommés, un healthcheck, un port localisé, et des arguments optimisés. **Copier manuellement un modèle de checkpoint est essentiel.**

**Prérequis :** Phase 6 terminée et idéalement sauvegardée. Agent avec WebSearch fonctionnel sur base durcie (v4.2).

**Approche :** Ajout du service `stable_diffusion_webui` dans `docker-compose.yml`. Mise à jour de `app.py` pour implémenter la fonction `generate_image_tool` (avec hardening) et l'activer dans l'AgentExecutor. Ajout d'instructions claires pour le placement manuel du modèle `.safetensors` requis via `docker cp` dans le volume nommé.

**Notes Importantes :**
* **VRAM :** Stable Diffusion est **extrêmement gourmand**. Surveillez `nvidia-smi` dans WSL. Commentez le `deploy:` GPU des services `xtts_server`/`whisper_server` si vous manquez de VRAM.
* **Modèles SD :** L'étape 7.6 est **obligatoire** pour que la génération d'images fonctionne.

---

### Étape 7.1 : Mettre à jour le Fichier `docker-compose.yml` (Ajout SD WebUI)

* **Explication :** Ajout du 6ème service (`stable_diffusion_webui` avec volumes nommés, healthcheck, port localisé) et mise à jour de `assistant_app` (variable d'env `STABLE_DIFFUSION_API_URL`, dépendance saine). Les configurations des 5 services précédents sont conservées.
* **Action :** Ouvrez `~/projet-khaldounia/assistant-core/docker-compose.yml`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```yaml
# Fichier: docker-compose.yml (Révisé v4.2 - Ajout Stable Diffusion)

version: '3.8'

services:
  # --- Ollama ---
  ollama:
    image: ollama/ollama:0.1.41 # FIGÉE
    container_name: ollama
    deploy: { resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } } }
    volumes: [ khaldounia_ollama_data:/root/.ollama ]
    ports: [ "127.0.0.1:11434:11434" ] # Accès local
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:11434/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

  # --- XTTS ---
  xtts_server:
    image: ghcr.io/coqui-ai/tts:v0.22.0 # FIGÉE
    container_name: xtts_server
    command: > tts-server --model_name tts_models/multilingual/multi-dataset/xtts_v2 --use_cuda true --port 8020 --speaker_wav /app/speakers/speaker_ref.wav
    ports: [ "127.0.0.1:8050:8020" ] # Accès local
    volumes: [ khaldounia_xtts_cache:/root/.local/share/tts, khaldounia_xtts_speakers:/app/speakers ] # Volumes Nommés
    environment: [ NVIDIA_VISIBLE_DEVICES=all, NVIDIA_DRIVER_CAPABILITIES=compute,utility, COQUI_TOS_AGREED=1 ]
    deploy: # Accès GPU (Optionnel - commenter si VRAM limitée)
       resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } }
    shm_size: 8gb
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"], interval: 30s, timeout: 10s, retries: 3, start_period: 180s }

  # --- ChromaDB ---
  chromadb:
    image: chromadb/chroma:0.5.0 # FIGÉE
    container_name: chromadb
    environment: { IS_PERSISTENT: 'TRUE', ALLOW_RESET: 'TRUE' }
    volumes: [ khaldounia_chroma_data:/chroma ]
    ports: [ "127.0.0.1:8002:8000" ] # Accès local
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"], interval: 30s, timeout: 10s, retries: 3, start_period: 30s }

  # --- Whisper (STT) ---
  whisper_server:
    image: ghcr.io/distributors/whisper-asr-webservice:1.1.0 # FIGÉE
    container_name: whisper_server
    environment: [ ASR_MODEL=small, ASR_ENGINE=openai_whisper ]
    ports: [ "127.0.0.1:9000:9000" ] # Accès local
    # deploy: # GPU Optionnel
    #   resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } }
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:9000/health"], interval: 30s, timeout: 10s, retries: 3, start_period: 90s }

  # --- Stable Diffusion WebUI (Image Gen) --- NOUVEAU SERVICE ---
  stable_diffusion_webui:
    image: lscr.io/linuxserver/stable-diffusion-webui:amd64-1.9.3 # FIGÉE (Exemple)
    container_name: stable_diffusion_webui
    environment:
      - PUID=1000 # Votre 'id -u' WSL
      - PGID=1000 # Votre 'id -g' WSL
      - TZ=Europe/Paris # Adaptez
      - NVIDIA_VISIBLE_DEVICES=all # Requis GPU
      # Args: API on, Listen All Interfaces (sécurité via port binding), xformers, extensions, no-half-vae
      - CLI_ARGS=--api --listen --port 7860 --xformers --enable-insecure-extension-access --no-half-vae
    volumes:
      # Volumes nommés pour SD (persistants)
      - khaldounia_sd_config:/config # Config UI, extensions...
      - khaldounia_sd_models:/config/stable-diffusion # Modèles Checkpoint, VAE, LoRA...
      - khaldounia_sd_outputs:/output # Images générées par l'UI/API
    ports: [ "127.0.0.1:7860:7860" ] # API et UI Web (Accès local seulement)
    deploy: # Accès GPU INDISPENSABLE
        resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } }
        # >>> Note VRAM : TRES GOURMAND! Commentez deploy: GPU des autres services (XTTS/Whisper) si besoin. <<<
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck:
      # Teste si l'API /docs est accessible (indique que --api est actif)
      test: ["CMD", "curl", "-f", "http://localhost:7860/docs"]
      interval: 60s  # Moins fréquent car long à démarrer/générer
      timeout: 30s
      retries: 5
      start_period: 300s # Laisse 5 min pour démarrer SD WebUI et charger modèle initial

  # --- Assistant App (Mis à jour) ---
  assistant_app:
    build: { context: ./app }
    container_name: assistant_app
    ports: [ "127.0.0.1:8001:8000" ] # Accès local
    volumes: [ ./app:/app, embedding_cache:/app/embedding_cache ]
    environment:
      - OLLAMA_HOST=http://ollama:11434
      - XTTS_SERVER_URL=http://xtts_server:8020
      - CHROMADB_HOST=chromadb
      - CHROMADB_PORT=8000
      - WHISPER_SERVER_URL=http://whisper_server:9000
      - STABLE_DIFFUSION_API_URL=http://stable_diffusion_webui:7860 # <<< AJOUT URL SD <<<
    depends_on: # <<< MIS A JOUR - Attend TOUTES les dépendances saines <<<
      ollama: { condition: service_healthy }
      xtts_server: { condition: service_healthy }
      chromadb: { condition: service_healthy }
      whisper_server: { condition: service_healthy }
      stable_diffusion_webui: { condition: service_healthy } # Ajout dépendance SD saine
    stdin_open: true
    tty: true
    networks: [ assistant_network ]
    restart: unless-stopped
    healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

# --- Réseau Docker ---
networks:
  assistant_network: { driver: bridge, name: assistant_network }

# --- Volumes Docker (Ajout SD) ---
volumes:
  khaldounia_ollama_data: { driver: local }
  khaldounia_chroma_data: { driver: local }
  embedding_cache: { driver: local }
  khaldounia_xtts_cache: { driver: local }
  khaldounia_xtts_speakers: { driver: local }
  # Nouveaux volumes pour Stable Diffusion
  khaldounia_sd_config: { driver: local }
  khaldounia_sd_models: { driver: local }
  khaldounia_sd_outputs: { driver: local }

```

* **Action :** Sauvegardez le fichier `docker-compose.yml`.

---

### Étape 7.2 : Vérifier le Fichier `requirements.txt`

* **Explication :** Aucun changement requis par rapport à la version révisée de Phase 3. Les appels API se font via `requests`.
* **Action :** Vérifiez `requirements.txt` dans `assistant-core/app`.

---

### Étape 7.3 : Mettre à jour le Fichier `app.py` (Activation Outil Image Gen)

* **Explication :** Implémentation de la fonction `generate_image_tool` (avec hardening : retry, timeout, gestion erreur/sauvegarde) et ajout de l'`image_tool` à la liste `active_tools` de l'agent dans `@cl.on_chat_start`.
* **Action :** Ouvrez `app.py` dans `~/projet-khaldounia/assistant-core/app/`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Révisé v4.2 pour Phase 7 - Activation ImgGen Tool)
# Rôle: Application Khaldounia (Agent + WebSearch + ImgGen - Robuste)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain
logging.info("--- Début Importation Langchain (Phase 7 Rev 4.2) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain.agents import AgentExecutor, create_react_agent, Tool
from langchain_community.tools import DuckDuckGoSearchRun
from langchain import hub
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# ... (Autres imports) ...
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale ---
logging.info("--- Début Chargement Configuration ---")
OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860") # AJOUT LECTURE URL SD
OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads")
# Création dossiers essentiels au cas où (normalement fait dans Dockerfile)
SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True)
logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}")
logging.info("--- Fin Chargement Configuration ---")


# --- Fonctions Utilitaires (Durcies) ---

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None:
    # ... (Implémentation transcribe_audio durcie) ...
    api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url}"); try: response = requests.post(api_url, files=files, timeout=60); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:50]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None:
    # ... (Implémentation text_to_speech durcie) ...
    api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue: {e}", exc_info=True); return None

# --- Fonction Outil Image Gen (Implémentée pour Phase 7 - avec retry/timeout) ---
@retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True) # Retry 1 fois après 5s
def generate_image_tool(prompt: str) -> str:
    """Appelle l'API Stable Diffusion pour générer une image, avec retry/timeout."""
    api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"
    # Payload basique - peut être enrichi
    payload = {
        "prompt": prompt,
        "negative_prompt": "ugly, deformed, noisy, blurry, low quality, worst quality, deformed anatomy, bad anatomy, extra limbs, watermark, text, signature, username",
        "steps": 25, "width": 512, "height": 512, "cfg_scale": 7,
        "sampler_name": "DPM++ 2M Karras", "seed": -1
    }
    logging.info(f"[IMG] Appel API SD: {api_url} pour prompt: '{prompt[:50]}...'")
    try:
        response = requests.post(api_url, json=payload, timeout=300) # Timeout long (5 min)
        response.raise_for_status() # Vérifie les erreurs HTTP (4xx, 5xx)
        data = response.json()

        if "images" in data and data["images"]:
            image_b64 = data["images"][0]
            try:
                image_bytes = base64.b64decode(image_b64)
                timestamp = int(time.time())
                safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_')
                # Assure que le dossier de sortie existe (normalement créé par Dockerfile)
                IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True)
                file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"
                # Sauvegarde l'image
                file_path.write_bytes(image_bytes)
                logging.info(f"[IMG] Image SD sauvée avec succès: {file_path}")
                # Retourne le chemin relatif depuis /app pour Chainlit
                relative_path = file_path.relative_to(Path("/app")).as_posix()
                # Signal pour process_and_respond pour afficher l'image
                return f"IMAGE_GENEREE:{relative_path}"
            except (base64.B64DecodeError, OSError, Exception) as save_err:
                logging.error(f"[IMG] Erreur décodage/sauvegarde image générée: {save_err}", exc_info=True)
                # Retourner un message d'erreur clair à l'agent
                return "Erreur: Impossible de traiter ou sauvegarder l'image générée."
        else:
            logging.error(f"[IMG] Pas de clé 'images' ou liste vide dans la réponse API SD: {data}")
            return "Erreur: Aucune image n'a été retournée par le service de génération."

    except requests.exceptions.Timeout as e:
        logging.warning(f"[IMG] Timeout lors de l'appel API SD ({e}). Tenacity va réessayer...")
        raise # Relance pour Tenacity
    except requests.exceptions.RequestException as e:
        logging.warning(f"[IMG] Erreur requête API SD ({e}). Tenacity va réessayer...")
        raise # Relance pour Tenacity
    except Exception as e:
        # Erreur inattendue (ex: parsing JSON de réponse échoue)
        logging.error(f"[IMG] Erreur inattendue lors appel API SD (non-retryable): {e}", exc_info=True)
        return f"Erreur inattendue lors de la communication avec le service d'image: {e}"
# --- Fin Fonction Outil Image Gen ---

# Placeholders pour autres outils
def _is_path_safe(requested_path: Path) -> bool: return False
def list_notes_safe() -> str: logging.warning("[NOTES] list_notes_safe non actif."); return "Action désactivée."
def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] save_note_safe non actif."); return "Action désactivée."
def read_note_safe(filename: str) -> str: logging.warning("[NOTES] read_note_safe non actif."); return "Action désactivée."
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée."


# --- Initialisation Globale (inchangée) ---
# ... (Code d'init globale embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat -> Agent avec WebSearch ET ImageGen actifs."""
    logging.info("\n--- Nouvelle Session Chat (Phase 7 v4.2 - Agent + WebSearch + ImgGen) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

    try:
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
        logging.info("[Session Start] LLM pour Agent initialisé.")

        # --- AGENT SETUP : Ajout Outil Image Gen ---
        logging.info("[Session Start] Configuration de l'Agent ReAct...")
        # 1. Définir TOUS les outils potentiels
        search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
        image_tool = Tool(
            name="Generateur Image",
            func=generate_image_tool, # Utilise la fonction implémentée ci-dessus
            description="Utile pour créer, dessiner ou générer une image à partir d'une description textuelle détaillée (prompt). Utilise cet outil si on demande explicitement de dessiner ou générer une image."
        )
        # Placeholders pour autres outils
        list_notes_tool = Tool( name="Lister Notes", func=list_notes_safe, description="Liste les notes." )
        save_note_tool = Tool( name="Sauvegarder Note", func=save_note_safe, description="Sauve une note." )
        read_note_tool = Tool( name="Lire Note", func=read_note_safe, description="Lit une note." )
        rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs." )

        # 2. Sélectionner outils ACTIFS pour CETTE phase (Phase 7)
        active_tools = [
            search_tool,
            image_tool  # <<<=== OUTIL IMAGE AJOUTÉ ICI
        ]
        logging.info(f"[Session Start] Outils actifs pour l'agent: {[tool.name for tool in active_tools]}")

        # 3. Prompt & Agent ReAct (inchangés par rapport à Phase 6)
        react_prompt = hub.pull("hwchase17/react")
        agent = create_react_agent(llm, active_tools, react_prompt)
        logging.info("[Session Start] Agent ReAct créé.")

        # 4. AgentExecutor (inchangé par rapport à Phase 6, utilise la nouvelle liste `active_tools`)
        agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
        cl.user_session.set("agent_executor", agent_executor)
        logging.info("[Session Start] Agent Executor (WebSearch + ImgGen) initialisé.")
        # --- FIN AGENT SETUP ---

        # Stocker retriever/vector store pour upload (Phase 9)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

        # Inputs & Upload (inchangé)
        logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
        cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2 avec recherche web et génération d'images).", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Prête (Agent WebSearch + ImgGen) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# process_and_respond (inchangé depuis Phase 6, gère déjà sortie IMAGE_GENEREE:)
# ... (Coller ici la fonction process_and_respond complète de Phase 6 Révisée v4.2) ...
async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1]; image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += " (Erreur: fichier introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée").send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion.", author="Erreur Système"); logging.info("--- Fin Traitement Input ---")


# on_message (inchangé)
@cl.on_message
async def on_text_message(message: cl.Message):
    # ... (Coller code on_message de Phase 6 Révisée v4.2) ...
    logging.info(f"\n--- Msg Texte UI ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur sanitization/traitement message texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message.").send()

# on_audio_end (inchangé)
@cl.on_audio_end
async def on_audio_end(audio: cl.Audio):
    # ... (Coller code on_audio_end de Phase 6 Révisée v4.2) ...
    logging.info(f"\n--- Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, mime: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None: transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide/None."); await transcript_msg.update(content="Pas de son détecté ou transcription vide."); except Exception as e: logging.error(f"[STT] Échec final transcription/sanitization: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription après tentatives.") ; logging.info("--- Fin Traitement Audio ---")


# on_file_upload (inchangé - logique Phase 9+)
@cl.on_file_upload(accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)
async def on_file_upload(files: list[cl.File]):
    # ... (Coller code on_file_upload de Phase 6 Révisée v4.2) ...
    logging.info(f"\n--- Fichiers Reçus (Logique Upload Phase 9+ non active) ---"); if not files: return; await cl.Message(content=f"Réception de {len(files)} fichier(s). Traitement RAG non implémenté dans cette phase.").send()

```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 7.4 : Redémarrer l'Environnement avec le Service d'Image

* **Explication :** Arrêt et redémarrage pour prendre en compte le nouveau service `stable_diffusion_webui` et l'activation de son outil dans l'agent `assistant_app`.
* **Action :** Terminal dans `assistant-core` :
    ```bash
    docker compose down
    ```
* **Action :** Reconstruire `assistant_app` (car `app.py` a changé) et démarrer tous les services (6 services maintenant) :
    ```bash
    docker compose up -d --build
    ```
* **Attente :** Téléchargement image SD (si non locale) et **démarrage initial de `stable_diffusion_webui` peuvent être longs** (plusieurs minutes, le temps qu'il devienne `(healthy)`). Surveillez `docker ps` et `docker logs stable_diffusion_webui`.

---

### Étape 7.5 : Tester la Génération d'Images

* **Action :** Vérifiez les 6 conteneurs (`docker ps`). Ils doivent tous être `Up` et `(healthy)`.
* **Action :** Ouvrez Chainlit (`http://127.0.0.1:8001`).
* **Action :** Ouvrez aussi l'UI Web de Stable Diffusion (`http://127.0.0.1:7860`) ET **vérifiez l'API sur `http://127.0.0.1:7860/docs`**. Vous devriez voir une page Swagger/OpenAPI (cela confirme que `--api` est actif).
* **Action :** **NE PAS OUBLIER : Ajoutez un modèle `.safetensors` via l'Étape 7.6 ci-dessous.** Sans modèle, la génération échouera.
* **Action :** Testez dans Chainlit (texte ou voix) : "Dessine un chat astronaute sur la lune".
* **Résultat Attendu :**
    1.  Logs `assistant_app` montrent l'agent choisissant et appelant l'outil `Generateur Image`.
    2.  Logs `stable_diffusion_webui` montrent la génération en cours (%).
    3.  Chainlit affiche "Voici l'image.", puis l'image générée (peut prendre du temps), et la réponse audio.
* **Test Autres Outils :** Vérifiez que la recherche web (Phase 6) fonctionne toujours ("Météo à Épinay-sur-Seine ?").
* **En Cas de Problème :**
    * **Conteneur `stable_diffusion_webui` KO (`Exited` / `Restarting` / `unhealthy`) :** Logs `stable_diffusion_webui`. Cause probable : **Manque VRAM** (commentez `deploy:` GPU de XTTS/Whisper et `docker compose up -d`), ou erreur `CLI_ARGS`.
    * **Agent n'utilise pas `Generateur Image` :** Logs `assistant_app` (`verbose=True`) -> "Thought". Description outil claire ? Prompt explicite ("Dessine...") ?
    * **Agent utilise l'outil, mais Erreur / Pas d'image :**
        1.  Logs `assistant_app` : Erreur dans `generate_image_tool` (`[ERREUR] Appel API SD`, `[ERREUR] Pas d'image`, `[ERREUR] Sauvegarde image` ?) Timeout après retry ?
        2.  Logs `stable_diffusion_webui` : Reçoit requête ? Erreur CUDA OOM ? **Erreur chargement modèle (manquant? Étape 7.6 faite ? Modèle sélectionné dans UI SD ?) ?**
        3.  Vérifiez API via `http://127.0.0.1:7860/docs`.
    * **Image non affichée ("fichier introuvable") :** Logs `assistant_app` (`[ERREUR] Fichier image non trouvé`). Chemin correct ? Droits écriture pour `appuser` dans `/app/generated_images` (devrait être OK via Dockerfile) ?

---

### Étape 7.6 : Ajouter les Modèles Stable Diffusion (Action Manuelle Essentielle)

* **Explication :** Le service SD a besoin d'au moins un modèle de checkpoint (`.safetensors`) pour fonctionner. Vous devez le télécharger et le copier dans le volume Docker approprié (`khaldounia_sd_models`).
* **Action 1 : Trouver/Télécharger Modèle :** Sur Civitai ou Hugging Face, téléchargez un modèle `.safetensors` (ex: `dreamshaper_8.safetensors`, `realisticVisionV60B1_v60B1VAE.safetensors`) sur votre PC Windows.
* **Action 2 : Copier Modèle dans Volume Docker via `docker cp`:**
    * Le volume `khaldounia_sd_models` est monté sur `/config/stable-diffusion` dans le conteneur. Les checkpoints vont dans le sous-dossier `Stable-diffusion`.
    * **Ouvrez un terminal WSL** et utilisez `docker cp` (adaptez les chemins !) :
        ```bash
        # Exemple si votre modèle est dans Téléchargements sous Windows :
        docker cp "/mnt/c/Users/VOTRE_USER_WINDOWS/Downloads/nom_du_modele.safetensors" stable_diffusion_webui:/config/stable-diffusion/Stable-diffusion/
        ```
* **Action 3 : Vérifier/Redémarrer (Recommandé) :**
    * Allez sur l'UI Web `http://127.0.0.1:7860`. Le nouveau modèle doit apparaître dans la liste déroulante "Stable Diffusion checkpoint" (cliquez sur l'icône de rafraîchissement 🔄 à côté si besoin). **Sélectionnez-le.**
    * Pour être sûr, vous pouvez redémarrer juste le service SD :
        ```bash
        docker compose restart stable_diffusion_webui
        ```
* **Note :** L'API SD utilisera le modèle **actuellement sélectionné** dans l'UI Web A1111. Assurez-vous qu'un modèle est chargé et sélectionné.

---

### **Étape 7.7: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** Agent avec Web/Img fonctionnel. Sauvegarde avant les actions système.
* **Méthode 1 : Copie Simple**
    * `docker compose down`
    * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase7`
    * `docker compose up -d`
* **Méthode 2 : Commit Git**
    * `git add .`
    * `git commit -m "Fin de la Phase 7 (v4.2) - Agent avec WebSearch/ImgGen fonctionnel (base durcie)"`

---

Fin de la Phase 7 (Révisée v4.2).
```

```markdown
---


## Phase 8 : Ajout Actions Système (Agent - Outils Notes - Révisée v4.2)

**Objectif :** Permettre à l'agent d'interagir avec le système de fichiers de manière **limitée et sécurisée**, en activant les outils pour lister, sauvegarder et lire des fichiers texte simples dans le dossier dédié `/app/user_notes`. Les fonctions associées sont implémentées avec des vérifications de sécurité.

**AVERTISSEMENT SÉCURITÉ :** Les actions sont restreintes au dossier `SAFE_NOTES_DIR` via `_is_path_safe`. Ne pas modifier cette logique ou ajouter des outils plus permissifs sans expertise.

**Prérequis :** Phase 7 terminée et idéalement sauvegardée. Agent avec WebSearch et ImgGen fonctionnel sur base durcie (v4.2).

**Approche :** Mise à jour de `app.py` pour implémenter les fonctions `_is_path_safe`, `list_notes_safe`, `save_note_safe`, `read_note_safe`. Ajout des `Tool` correspondants à la liste `active_tools` de l'Agent Executor dans `@cl.on_chat_start`.

---

### Étape 8.1 : Vérifier le Fichier `requirements.txt`

* **Explication :** Aucun changement requis. Les interactions se font via `pathlib` (intégré à Python).
* **Action :** Vérifiez `requirements.txt` dans `assistant-core/app`.

---

### Étape 8.2 : Mettre à jour le Fichier `app.py` (Activation Outils Système)

* **Explication :** Implémentation complète des fonctions de gestion de notes avec sécurité (`_is_path_safe`) et ajout des outils correspondants (`list_notes_tool`, `save_note_tool`, `read_note_tool`) à la liste `active_tools` de l'agent dans `@cl.on_chat_start`. Les descriptions des outils sont cruciales pour guider l'agent.
* **Action :** Ouvrez `app.py` dans `~/projet-khaldounia/assistant-core/app/`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Révisé v4.2 pour Phase 8 - Activation Outils Système)
# Rôle: Application Khaldounia (Agent + WebSearch + ImgGen + SysActions - Robuste)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain
logging.info("--- Début Importation Langchain (Phase 8 Rev 4.2) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain.agents import AgentExecutor, create_react_agent, Tool
from langchain_community.tools import DuckDuckGoSearchRun
from langchain import hub
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# ... (Autres imports) ...
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale (inchangée) ---
# ... (coller la config globale inchangée) ...
logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


# --- Fonctions Utilitaires (Durcies) ---

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None:
    # ... (Implémentation transcribe_audio durcie) ...
    api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url}"); try: response = requests.post(api_url, files=files, timeout=60); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:50]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None:
    # ... (Implémentation text_to_speech durcie) ...
    api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
def generate_image_tool(prompt: str) -> str:
    # ... (Implémentation generate_image_tool durcie de Phase 7) ...
    api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, ...}; logging.info(f"[IMG] Appel API SD: '{prompt[:50]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[IMG] Erreur requête SD ({e}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"


# --- Fonctions Outils Notes (Implémentées pour Phase 8) ---
def _is_path_safe(requested_path_str: str) -> Path | None:
    """Vérifie si le nom de fichier est simple et résout dans SAFE_NOTES_DIR.
       Retourne le chemin absolu sécurisé si OK, sinon None."""
    try:
        # Vérification basique caractères invalides/dangereux et chemin relatif simple
        if not requested_path_str or "/" in requested_path_str or "\\" in requested_path_str or ".." in requested_path_str:
             logging.warning(f"[_is_path_safe] Tentative d'utilisation de nom de fichier invalide/chemin complexe: '{requested_path_str}'")
             return None

        target_path_relative = Path(requested_path_str)
        # Double-vérifie que c'est juste un nom de fichier (pas un chemin)
        if target_path_relative.parent != Path('.'):
             logging.warning(f"[_is_path_safe] Tentative d'utilisation de sous-dossier non autorisé: '{requested_path_str}'")
             return None

        safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True) # Assure que SAFE_NOTES_DIR existe
        requested_path_abs = (safe_dir_abs / target_path_relative).resolve(strict=False) # Résout chemin complet demandé

        # Vérifie si le chemin résolu est DANS ou est ÉGAL au dossier sécurisé
        # Et qu'il ne remonte pas (double sécurité avec is_relative_to)
        if requested_path_abs == safe_dir_abs or requested_path_abs.is_relative_to(safe_dir_abs):
             # Exclut le dossier lui-même, on veut seulement les fichiers dedans
             if requested_path_abs != safe_dir_abs:
                 return requested_path_abs # Retourne le chemin absolu et sécurisé du fichier potentiel
        logging.warning(f"[_is_path_safe] Chemin résolu hors limites: '{requested_path_abs}' vs '{safe_dir_abs}'")
        return None
    except FileNotFoundError:
        logging.error(f"[_is_path_safe] Dossier de notes sécurisé introuvable: {SAFE_NOTES_DIR}")
        return None
    except Exception as e:
        logging.error(f"[_is_path_safe] Erreur validation chemin pour '{requested_path_str}': {e}", exc_info=True)
        return None # Sécurité par défaut

def list_notes_safe() -> str:
    """Liste les fichiers de notes dans le dossier sécurisé."""
    logging.info(f"[NOTES] Action: Lister notes dans {SAFE_NOTES_DIR}")
    try:
        # Crée le dossier s'il n'existe pas (devrait être fait par Dockerfile mais sécurité)
        SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True)
        files = [f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()]
        return "Notes personnelles existantes:\n- " + "\n- ".join(files) if files else "Le dossier des notes personnelles est vide."
    except Exception as e: # Large car l'itération peut échouer pour diverses raisons (perms...)
        logging.error(f"[NOTES] Erreur list_notes: {e}", exc_info=True)
        return f"Erreur inattendue lors du listage des notes: {e}"

def save_note_safe(filename: str, content: str) -> str:
    """Sauvegarde le contenu dans un fichier note, après validation du chemin."""
    logging.info(f"[NOTES] Action: Tentative de sauvegarde note '{filename}'")
    safe_full_path = _is_path_safe(filename)

    if safe_full_path is None:
        # _is_path_safe logue déjà l'avertissement de sécurité
        return "Erreur de sécurité: Le nom de fichier spécifié est invalide ou non autorisé."
    try:
        # S'assure que le dossier parent existe (sécurité)
        safe_full_path.parent.mkdir(parents=True, exist_ok=True)
        # Écrit le fichier
        safe_full_path.write_text(content, encoding='utf-8')
        logging.info(f"[NOTES] Note sauvée avec succès: {safe_full_path}")
        return f"Note '{filename}' sauvée avec succès."
    except OSError as e:
         logging.error(f"[NOTES] Erreur OS lors sauvegarde '{filename}': {e}", exc_info=True)
         return f"Erreur système lors de la sauvegarde de la note '{filename}': {e}"
    except Exception as e:
        logging.error(f"[NOTES] Erreur inattendue sauvegarde '{filename}': {e}", exc_info=True)
        return f"Erreur inattendue lors de la sauvegarde de la note '{filename}': {e}"

def read_note_safe(filename: str) -> str:
    """Lit le contenu d'un fichier note, après validation du chemin."""
    logging.info(f"[NOTES] Action: Tentative de lecture note '{filename}'")
    safe_full_path = _is_path_safe(filename)

    if safe_full_path is None:
        return "Erreur de sécurité: Le nom de fichier spécifié est invalide ou non autorisé."

    if not safe_full_path.is_file():
        logging.warning(f"[NOTES] Tentative lecture fichier note inexistant: '{filename}' ({safe_full_path})")
        return f"Erreur: Le fichier note '{filename}' n'existe pas."
    try:
        content = safe_full_path.read_text(encoding='utf-8')
        logging.info(f"[NOTES] Note lue avec succès: {safe_full_path}")
        # Limite la taille retournée pour éviter d'envoyer des fichiers énormes ? Non, l'agent doit pouvoir lire.
        return f"Contenu de la note '{filename}':\n\n{content}"
    except OSError as e:
         logging.error(f"[NOTES] Erreur OS lors lecture '{filename}': {e}", exc_info=True)
         return f"Erreur système lors de la lecture de la note '{filename}': {e}"
    except Exception as e:
        logging.error(f"[NOTES] Erreur inattendue lecture '{filename}': {e}", exc_info=True)
        return f"Erreur inattendue lors de la lecture de la note '{filename}': {e}"
# --- Fin Fonctions Outils Notes ---

# Placeholder outil RAG
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée."


# --- Initialisation Globale (inchangée) ---
# ... (Code d'init globale embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat -> Agent avec WebSearch, ImgGen ET SysActions actifs."""
    logging.info("\n--- Nouvelle Session Chat (Phase 8 v4.2 - Agent + Web/Img/Sys Actifs) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

    try:
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
        logging.info("[Session Start] LLM pour Agent initialisé.")

        # --- AGENT SETUP : Activation des Outils Système ---
        logging.info("[Session Start] Configuration de l'Agent ReAct...")
        # 1. Définir TOUS les outils potentiels
        search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
        image_tool = Tool( name="Generateur Image", func=generate_image_tool, description="Créé une image à partir d'une description." )
        list_notes_tool = Tool(
            name="Lister Notes Personnelles", # Nom plus précis
            func=list_notes_safe,
            description="Utilise cet outil pour obtenir la liste des fichiers de notes personnelles existants dans le dossier sécurisé."
            )
        save_note_tool = Tool(
            name="Sauvegarder Note Personnelle", # Nom plus précis
            func=save_note_safe,
            # Description précise pour aider l'agent à formater l'input (même si fragile)
            description="Utilise cet outil pour sauvegarder ou mettre à jour du texte dans une note personnelle. L'entrée DOIT être une chaîne contenant uniquement le nom de fichier simple (ex: 'ma_note.txt') suivi d'une virgule, puis du contenu complet à sauvegarder."
            )
        read_note_tool = Tool(
            name="Lire Note Personnelle", # Nom plus précis
            func=read_note_safe,
            description="Utilise cet outil pour lire le contenu complet d'un fichier note personnelle existant. L'entrée DOIT être juste le nom exact et simple du fichier (ex: 'ma_note.txt')."
            )
        rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs." ) # Sera activé en Phase 10

        # 2. Sélectionner outils ACTIFS pour CETTE phase (Phase 8)
        active_tools = [
            search_tool,
            image_tool,
            list_notes_tool, # <<< ACTIVÉ
            save_note_tool,  # <<< ACTIVÉ
            read_note_tool   # <<< ACTIVÉ
            # rag_tool # Pas encore actif
        ]
        logging.info(f"[Session Start] Outils actifs pour l'agent ({len(active_tools)}): {[tool.name for tool in active_tools]}")

        # 3. Prompt & Agent ReAct (inchangés)
        react_prompt = hub.pull("hwchase17/react")
        agent = create_react_agent(llm, active_tools, react_prompt)
        logging.info("[Session Start] Agent ReAct créé.")

        # 4. AgentExecutor (inchangé, utilise la nouvelle liste `active_tools`)
        agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
        cl.user_session.set("agent_executor", agent_executor)
        logging.info(f"[Session Start] Agent Executor ({len(active_tools)} outils actifs) initialisé.")
        # --- FIN AGENT SETUP ---

        # Stocker retriever/vector store pour upload (Phase 9)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

        # Inputs & Upload (inchangé)
        logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
        cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2 avec recherche, image, et actions système/notes).", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Prête (Agent Web/Img/Sys) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# process_and_respond (inchangé - utilise l'agent executor avec la nouvelle liste d'outils)
# ... (Coller ici la fonction process_and_respond complète de Phase 7 Révisée v4.2) ...
async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1]; image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += " (Erreur: fichier introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée").send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion.", author="Erreur Système"); logging.info("--- Fin Traitement Input ---")


# on_message (inchangé)
@cl.on_message
async def on_text_message(message: cl.Message):
    # ... (Coller code on_message de Phase 7 Révisée v4.2) ...
    logging.info(f"\n--- Msg Texte UI ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur sanitization/traitement message texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message.").send()

# on_audio_end (inchangé)
@cl.on_audio_end
async def on_audio_end(audio: cl.Audio):
    # ... (Coller code on_audio_end de Phase 7 Révisée v4.2) ...
    logging.info(f"\n--- Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, mime: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None: transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide/None."); await transcript_msg.update(content="Pas de son détecté ou transcription vide."); except Exception as e: logging.error(f"[STT] Échec final transcription/sanitization: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription après tentatives.") ; logging.info("--- Fin Traitement Audio ---")


# on_file_upload (inchangé - logique Phase 9+)
@cl.on_file_upload(accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)
async def on_file_upload(files: list[cl.File]):
    # ... (Coller code on_file_upload de Phase 7 Révisée v4.2) ...
    logging.info(f"\n--- Fichiers Reçus (Logique Upload Phase 9+ non active) ---"); if not files: return; await cl.Message(content=f"Réception de {len(files)} fichier(s). Traitement RAG non implémenté dans cette phase.").send()

```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 8.3 : Redémarrer l'Environnement avec les Outils Système Actifs

* **Explication :** Arrêt et redémarrage pour que `assistant_app` utilise le nouveau code `app.py` où l'Agent Executor a maintenant accès aux outils de gestion des notes.
* **Action :** Terminal dans `assistant-core` :
    ```bash
    docker compose down
    ```
* **Action :** Reconstruire `assistant_app` et démarrer tous les services (toujours 6 services) :
    ```bash
    docker compose up -d --build
    ```

---

### Étape 8.4 : Tester les Actions Système

* **Action :** Vérifiez les 6 conteneurs (`docker ps`) -> `Up` et `(healthy)`.
* **Action :** Ouvrez/Actualisez Chainlit (`http://127.0.0.1:8001`).
* **Test 1 (Liste Initialement Vide) :** Demandez : "Liste mes notes personnelles".
    * *(Résultat Attendu : Réponse "Dossier notes vide.")*
* **Test 2 (Sauvegarde) :** Demandez : "Sauvegarde une note personnelle nommée `courses.txt` avec le contenu : Lait, Oeufs, Pain".
    * *(Résultat Attendu : Logs agent montrent appel à `Sauvegarder Note Personnelle` avec `filename='courses.txt'` et le contenu. Réponse "Note 'courses.txt' sauvée...")*
* **Test 3 (Liste après Sauvegarde) :** Demandez : "Quelles sont mes notes personnelles ?".
    * *(Résultat Attendu : Réponse "Notes:\n- courses.txt")*
* **Test 4 (Lecture) :** Demandez : "Lis ma note personnelle `courses.txt`".
    * *(Résultat Attendu : Logs agent montrent appel à `Lire Note Personnelle` avec `filename='courses.txt'`. Réponse "Contenu de la note 'courses.txt':\n\nLait, Oeufs, Pain")*
* **Test 5 (Sécurité - Échec Attendu) :** Demandez : "Lis le fichier `../app.py`" ou "Sauvegarde une note dans `/etc/passwd` avec le contenu : test".
    * *(Résultat Attendu : Logs agent montrent appel à l'outil mais `_is_path_safe` renvoie `None`. Réponse agent indiquant "Erreur de sécurité: nom de fichier invalide...")*
* **En Cas de Problème :**
    * **Agent n'utilise pas le bon outil notes :** Logs `assistant_app` (`verbose=True`) -> "Thought". Descriptions outils claires ?
    * **Agent utilise outil notes mais Erreur / Échec :** Logs `assistant_app` : Erreur dans `list/save/read_note_safe` (`[ERREUR] Notes...`) ? Erreur Sécurité attendue (Test 5) ? Erreur Permissions sur `/app/user_notes` (vérifier droits `appuser` via `docker exec -it assistant_app ls -ld /app/user_notes`) ? Agent a mal formaté input (ex: pour `save_note`) ? Fichier non trouvé pour `read_note` ?

---

### Étape 8.5 : Note Importante sur la Sécurité (Vérification Documentation)

* **Action :** Assurez-vous que cette note est présente.

> #### **Note Cruciale sur la Sécurité des Actions Système**
> Les outils sont volontairement limités au dossier `/app/user_notes` via la validation `_is_path_safe`. Ne modifiez pas cette fonction. N'ajoutez pas d'outils exécutant des commandes système arbitraires sans mécanismes de sécurité robustes.

---

### **Étape 8.6: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** Agent avec Web/Img/Sys fonctionnel. Sauvegarde avant tests RAG.
* **Méthode 1 : Copie Simple**
    * `docker compose down`
    * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase8`
    * `docker compose up -d`
* **Méthode 2 : Commit Git**
    * `git add .`
    * `git commit -m "Fin de la Phase 8 (v4.2) - Agent avec Web/Img/Sys fonctionnel (base durcie)"`

---

Fin de la Phase 8 (Révisée v4.2).



```markdown
---

## Phase 9 : Chargement et Test des Données pour RAG (Révisée v4.2)

**Objectif :** Implémenter la logique d'upload de fichiers dans `app.py` et tester l'ajout de documents personnels (PDF, TXT, DOCX) à la base de données vectorielle ChromaDB via l'interface Chainlit. Vérifier (indirectement) la capacité de récupération d'informations via l'observation des logs.

**Prérequis :** Phase 8 terminée et idéalement sauvegardée. Agent avec outils Web/Img/Sys fonctionnel sur base durcie (v4.2).

**Approche :** Ajout de l'implémentation du gestionnaire `@cl.on_file_upload` dans `app.py` pour gérer le chargement, le découpage (splitting) et l'indexation des documents dans ChromaDB via Langchain. Upload de fichiers tests via l'UI, observation des logs, puis test en posant des questions spécifiques pour voir si le contexte est récupéré (même si l'agent ne l'utilise pas encore activement).

---

### Étape 9.1 : Préparer des Documents de Test

* **Action (Manuelle) :** Sur votre PC Windows, créez ou préparez 1 ou 2 fichiers simples contenant des informations uniques et facilement identifiables.
    * **Exemple 1 (`infos_khaldounia.txt`) :** Fichier `.txt` : "L'assistant Khaldounia utilise le modèle d'embedding all-MiniLM-L6-v2."
    * **Exemple 2 (`reunion_projet_aladin.docx`) :** Document Word court mentionnant le "projet Aladin" et une date fictive.
    * **Formats Supportés (par défaut) :** `.txt`, `.pdf`, `.docx`. D'autres formats *peuvent* fonctionner via `unstructured` si les dépendances système nécessaires sont dans le `Dockerfile`.

---

### Étape 9.2 : Mettre à jour le Fichier `app.py` (Implémentation Upload RAG)

* **Explication :** Ajout de l'implémentation de la fonction décorée par `@cl.on_file_upload`. Cette fonction gère la réception des fichiers, leur sauvegarde temporaire, le chargement de leur contenu, le découpage en chunks, et l'ajout à ChromaDB. Utilise `cl.make_async` pour les opérations potentiellement longues afin de ne pas bloquer l'interface.
* **Action :** Ouvrez `app.py` dans `~/projet-khaldounia/assistant-core/app/`.
* **Action :** Remplacez **l'intégralité** de son contenu par :

```python
# Fichier: app.py (Révisé v4.2 pour Phase 9 - Implémentation Upload RAG)
# Rôle: Application Khaldounia (Agent Web/Img/Sys + Logique Upload RAG)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain (Ajout Loaders/Splitter)
logging.info("--- Début Importation Langchain (Phase 9 Rev 4.2) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain.agents import AgentExecutor, create_react_agent, Tool
from langchain_community.tools import DuckDuckGoSearchRun
from langchain import hub
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
# --- Ajout pour RAG Upload ---
from langchain_community.document_loaders import PyPDFLoader, TextLoader, UnstructuredFileLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
# --- Fin Ajout RAG Upload ---
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale (inchangée) ---
# ... (coller la config globale inchangée) ...
logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


# --- Fonctions Utilitaires (Durcies) ---
# ... (Coller ici les fonctions transcribe_audio, text_to_speech, generate_image_tool, _is_path_safe, list_notes_safe, save_note_safe, read_note_safe de Phase 8 v4.2) ...
@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None: api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url}"); try: response = requests.post(api_url, files=files, timeout=60); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:50]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue: {e}", exc_info=True); return None
@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None: api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue: {e}", exc_info=True); return None
@retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
def generate_image_tool(prompt: str) -> str: api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, ...}; logging.info(f"[IMG] Appel API SD: '{prompt[:50]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[IMG] Erreur requête SD ({e}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"
def _is_path_safe(requested_path_str: str) -> Path | None: try: if not requested_path_str or "/" in requested_path_str or "\\" in requested_path_str or ".." in requested_path_str: logging.warning(f"[_is_path_safe] Nom fichier invalide/complexe: '{requested_path_str}'"); return None; target_path_relative = Path(requested_path_str); if target_path_relative.parent != Path('.'): logging.warning(f"[_is_path_safe] Sous-dossier non autorisé: '{requested_path_str}'"); return None; safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True); requested_path_abs = (safe_dir_abs / target_path_relative).resolve(strict=False); if requested_path_abs == safe_dir_abs or requested_path_abs.is_relative_to(safe_dir_abs): if requested_path_abs != safe_dir_abs: return requested_path_abs; logging.warning(f"[_is_path_safe] Chemin hors limites: '{requested_path_abs}' vs '{safe_dir_abs}'"); return None; except Exception as e: logging.error(f"[_is_path_safe] Erreur validation '{requested_path_str}': {e}", exc_info=True); return None
def list_notes_safe() -> str: logging.info(f"[NOTES] Action: Lister notes dans {SAFE_NOTES_DIR}"); try: SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); files = [f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()]; return "Notes personnelles existantes:\n- " + "\n- ".join(files) if files else "Dossier notes vide."; except Exception as e: logging.error(f"[NOTES] Erreur list_notes: {e}", exc_info=True); return f"Erreur listage notes: {e}"
def save_note_safe(filename: str, content: str) -> str: logging.info(f"[NOTES] Action: Sauvegarde note '{filename}'"); safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide."; try: safe_full_path.parent.mkdir(parents=True, exist_ok=True); safe_full_path.write_text(content, encoding='utf-8'); logging.info(f"[NOTES] Note sauvée: {safe_full_path}"); return f"Note '{filename}' sauvée."; except Exception as e: logging.error(f"[NOTES] Erreur sauvegarde '{filename}': {e}", exc_info=True); return f"Erreur sauvegarde note '{filename}': {e}"
def read_note_safe(filename: str) -> str: logging.info(f"[NOTES] Action: Lecture note '{filename}'"); safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide."; if not safe_full_path.is_file(): logging.warning(f"[NOTES] Fichier note inexistant: '{filename}'"); return f"Erreur: Fichier note '{filename}' inexistant."; try: content = safe_full_path.read_text(encoding='utf-8'); logging.info(f"[NOTES] Note lue: {safe_full_path}"); return f"Contenu de '{filename}':\n\n{content}"; except Exception as e: logging.error(f"[NOTES] Erreur lecture '{filename}': {e}", exc_info=True); return f"Erreur lecture note '{filename}': {e}"
def run_rag_retriever(query: str) -> str: logging.warning("[RAG] run_rag_retriever non actif."); return "Action désactivée." # Sera activé en Phase 10


# --- Initialisation Globale (inchangée) ---
# ... (Code d'init globale embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat -> Agent avec Web/Img/Sys actifs."""
    logging.info("\n--- Nouvelle Session Chat (Phase 9 v4.2 - Agent Web/Img/Sys + Upload RAG prêt) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

    try:
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
        logging.info("[Session Start] LLM pour Agent initialisé.")

        # --- AGENT SETUP (Web/Img/Sys actifs, RAG défini mais inactif) ---
        logging.info("[Session Start] Configuration de l'Agent ReAct...")
        # 1. Définir TOUS les outils
        search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
        image_tool = Tool( name="Generateur Image", func=generate_image_tool, description="..." )
        list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="..." )
        save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="..." )
        read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="..." )
        rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs." ) # Placeholder func

        # 2. Sélectionner outils ACTIFS pour Phase 9 (PAS DE RAG TOOL ICI)
        active_tools = [ search_tool, image_tool, list_notes_tool, save_note_tool, read_note_tool ]
        logging.info(f"[Session Start] Outils actifs pour l'agent ({len(active_tools)}): {[tool.name for tool in active_tools]}")

        # 3. Prompt & Agent ReAct (inchangés)
        react_prompt = hub.pull("hwchase17/react")
        agent = create_react_agent(llm, active_tools, react_prompt)
        logging.info("[Session Start] Agent ReAct créé.")

        # 4. AgentExecutor (inchangé, utilise la liste `active_tools` ci-dessus)
        agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
        cl.user_session.set("agent_executor", agent_executor)
        logging.info(f"[Session Start] Agent Executor ({len(active_tools)} outils actifs) initialisé.")
        # --- FIN AGENT SETUP ---

        # Stocker retriever/vector store (pour @cl.on_file_upload)
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

        # Inputs & Upload (inchangé)
        logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
        cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2 avec recherche, image, notes). Upload RAG activé.", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Prête (Agent Web/Img/Sys + Upload RAG) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# process_and_respond (inchangé - utilise l'agent executor avec ses outils actifs)
# ... (Coller ici la fonction process_and_respond complète de Phase 8 v4.2) ...
async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1]; image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += " (Erreur: fichier introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée").send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion.", author="Erreur Système"); logging.info("--- Fin Traitement Input ---")


# on_message (inchangé)
@cl.on_message
async def on_text_message(message: cl.Message):
    # ... (Coller code on_message de Phase 8 v4.2) ...
    logging.info(f"\n--- Msg Texte UI ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur sanitization/traitement message texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message.").send()

# on_audio_end (inchangé)
@cl.on_audio_end
async def on_audio_end(audio: cl.Audio):
    # ... (Coller code on_audio_end de Phase 8 v4.2) ...
    logging.info(f"\n--- Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, mime: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None: transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide/None."); await transcript_msg.update(content="Pas de son détecté ou transcription vide."); except Exception as e: logging.error(f"[STT] Échec final transcription/sanitization: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription après tentatives.") ; logging.info("--- Fin Traitement Audio ---")


# --- Section RAG Upload (Implémentée pour Phase 9) ---
# Décorateur avec types de fichiers acceptés et limites
@cl.on_file_upload(accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)
async def on_file_upload(files: list[cl.File]):
    """Gère l'upload de fichiers pour indexation RAG."""
    logging.info(f"\n--- Fichiers Reçus pour RAG ({len(files)}) ---")
    if not files:
        return

    # Récupère le VectorStore Langchain de la session
    vector_store_lc_session = cl.user_session.get("vector_store_lc")
    if not vector_store_lc_session:
        logging.error("[RAG Upload] VectorStore non trouvé dans la session.")
        await cl.Message(content="Erreur interne: La base de données RAG n'est pas prête pour cette session.").send()
        return

    await cl.Message(content=f"Réception de {len(files)} fichier(s) pour indexation RAG...").send()

    # Initialise le Text Splitter
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)

    successful_files = []
    failed_files = []

    for file in files:
        # Affiche un message pour chaque fichier
        file_msg = cl.Message(content=f"Traitement de `{file.name}`...", author="Système", disable_feedback=True)
        await file_msg.send()
        temp_file_path = None # Pour cleanup

        try:
            # 1. Sauvegarder le fichier temporairement
            temp_file_path = UPLOAD_TEMP_DIR / file.name
            UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True) # Assure existence
            await cl.make_async(temp_file_path.write_bytes)(file.content)
            logging.info(f"[RAG Upload] Fichier temporaire sauvé: {temp_file_path}")

            # 2. Choisir le Loader approprié
            file_ext = os.path.splitext(file.name)[1].lower()
            loader = None
            if file_ext == ".pdf":
                loader = PyPDFLoader(str(temp_file_path))
            elif file_ext == ".txt":
                loader = TextLoader(str(temp_file_path), encoding='utf-8')
            elif file_ext == ".docx": # Unstructured gère docx
                 loader = UnstructuredFileLoader(str(temp_file_path))
            else:
                logging.warning(f"[RAG Upload] Format de fichier non supporté: {file.name}")
                await file_msg.update(content=f"Format de fichier `{file.name}` non supporté.")
                failed_files.append(file.name)
                continue # Passe au fichier suivant

            # 3. Charger les documents (async)
            logging.info(f"[RAG Upload] Chargement du document: {file.name}...")
            documents = await cl.make_async(loader.load)()
            logging.info(f"[RAG Upload] Document chargé ({len(documents)} pages/docs).")

            # 4. Splitter les documents en chunks (async)
            logging.info(f"[RAG Upload] Split du document: {file.name}...")
            chunks = await cl.make_async(text_splitter.split_documents)(documents)
            logging.info(f"[RAG Upload] Nombre de chunks créés: {len(chunks)}")

            if not chunks:
                logging.warning(f"[RAG Upload] Aucun chunk créé pour {file.name}.")
                await file_msg.update(content=f"Aucun contenu textuel extrait de `{file.name}`.")
                failed_files.append(file.name)
                continue

            # 5. Ajouter les chunks au VectorStore (async)
            logging.info(f"[RAG Upload] Ajout des chunks de {file.name} à ChromaDB...")
            await cl.make_async(vector_store_lc_session.add_documents)(chunks)
            logging.info(f"[RAG Upload] Chunks ajoutés avec succès.")

            await file_msg.update(content=f"Document `{file.name}` indexé avec succès ({len(chunks)} chunks).")
            successful_files.append(file.name)

        except Exception as e:
            # Gère les erreurs pendant le processus pour un fichier
            error_text = f"Erreur lors du traitement de `{file.name}`: {e}"
            logging.error(f"[RAG Upload] {error_text}", exc_info=True)
            await file_msg.update(content=error_text)
            failed_files.append(file.name)
        finally:
            # 6. Nettoyer le fichier temporaire
            if temp_file_path and await cl.make_async(temp_file_path.exists)():
                try:
                    await cl.make_async(temp_file_path.unlink)()
                    logging.info(f"[RAG Upload] Fichier temporaire supprimé: {temp_file_path}")
                except Exception as del_e:
                    logging.error(f"[RAG Upload] Erreur suppression fichier temp: {del_e}")

    # Message récapitulatif final
    summary_message = "Traitement des fichiers terminé.\n"
    if successful_files:
        summary_message += f"- Fichiers indexés: {', '.join(successful_files)}\n"
    if failed_files:
        summary_message += f"- Fichiers en échec: {', '.join(failed_files)}"
    await cl.Message(content=summary_message).send()
    logging.info("--- Fin Traitement Upload Fichiers RAG ---")

# --- Fin Section RAG Upload ---


```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 9.3 : Utiliser l'Upload Chainlit

* **Action :** Vérifiez que tous les conteneurs Docker sont actifs (`docker ps`).
* **Action :** Ouvrez l'interface Chainlit dans votre navigateur : `http://127.0.0.1:8001`.
* **Action :** Cliquez sur l'icône d'upload (trombone/flèche). Elle devrait être active (car `cl.set_config(enable_file_upload=True)` est dans `@cl.on_chat_start`).
* **Action :** Sélectionnez le(s) fichier(s) de test (ex: `infos_khaldounia.txt`, `reunion_projet_aladin.docx`) et validez l'upload.

---

### Étape 9.4 : Observer le Traitement d'Indexation

* **Résultat Attendu dans Chainlit :** Messages indiquant : "Réception...", "Traitement `[nom_fichier]`...", "Document `[nom_fichier]` indexé...", "Traitement terminé...".
* **Résultat Attendu dans les Logs (`assistant_app`) :** Suivez les logs (`docker logs -f assistant_app`) pour voir les détails de `@cl.on_file_upload` :
    * "--- Fichiers Reçus pour RAG..."
    * "[RAG Upload] Fichier temporaire sauvé..."
    * "[RAG Upload] Chargement du document..."
    * "[RAG Upload] Split du document..."
    * "[RAG Upload] Nombre de chunks créés..."
    * "[RAG Upload] Ajout des chunks ... à ChromaDB..."
    * "[RAG Upload] Chunks ajoutés avec succès."
    * "[RAG Upload] Fichier temporaire supprimé..."
    * "--- Fin Traitement Upload Fichiers RAG ---"

---

### Étape 9.5 : Tester la Fonctionnalité RAG (Récupération Basique - Indirecte)

* **Explication :** Posez une question dont la réponse est **uniquement** dans un des documents chargés. L'Agent n'a **pas** encore l'outil RAG actif. Cependant, on peut observer si le contexte pertinent est récupéré par le retriever (lié à la chaîne sous-jacente dans certaines implémentations, bien que moins direct ici avec l'Agent Executor). Le but est surtout de s'assurer que l'indexation a eu lieu et que le retriever est prêt.
* **Action :** Dans Chainlit, posez une question très spécifique :
    * **Exemple 1 :** "Quel modèle d'embedding Khaldounia utilise-t-il ?" (si `infos_khaldounia.txt` chargé)
    * **Exemple 2 :** "Quel est le nom du projet mentionné dans le doc de réunion ?" (si `reunion_projet_aladin.docx` chargé)
* **Résultat Attendu / Observation :**
    1.  **Il est PROBABLE que l'agent n'utilise PAS cette information.** Il choisira probablement `Recherche Web DuckDuckGo` ou répondra qu'il ne sait pas, car l'outil `Recherche Document Personnel` n'est pas dans sa liste `active_tools`. C'est **normal** à ce stade.
    2.  **Observez les logs `assistant_app` (`verbose=True`) :** Vérifiez que l'agent *n'appelle pas* l'outil `Recherche Document Personnel`. C'est le comportement attendu pour cette phase.
* **Contre-Test :** Posez une question Web ("Météo à Paris aujourd'hui ?"). L'agent doit utiliser `Recherche Web DuckDuckGo`.

---

### Étape 9.6 : Dépannage Upload RAG (Si besoin)

* **Problème : Fichier non ajouté / Erreur pendant l'upload (`Traitement terminé... Échecs: [fichier]`).**
    * **Causes Possibles :** Format non supporté (`accept=`), erreur parsing `PyPDFLoader`/`TextLoader`/`UnstructuredFileLoader` (fichier corrompu, format complexe, dépendance système manquante ?), erreur connexion/écriture ChromaDB (`add_documents`), problème droits écriture `/app/temp_uploads` (normalement OK avec `appuser` et `chown` dans Dockerfile).
    * **Actions :** Analysez l'erreur exacte dans les logs `assistant_app` (`[RAG Upload] Erreur...`). Si erreur `unstructured`, essayer de convertir en `.txt`. Vérifier état `chromadb` (`docker ps`, healthcheck).
* **Problème : Fichier ajouté ("Succès"), mais info non utilisée (Test 9.5).**
    * **Cause Principale (attendue) :** L'outil RAG n'est pas encore actif pour l'agent (sera fait en Phase 10).
    * **Action :** C'est normal. Vérifiez juste que l'upload s'est bien passé dans les logs. Passez à la Phase 10 pour activer l'outil RAG dans l'agent.

---

### **Étape 9.7: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **Explication :** L'upload RAG est implémenté et semble fonctionner (indexation OK). Sauvegarde avant l'activation finale de l'outil RAG.
* **Méthode 1 : Copie Simple**
    * `docker compose down`
    * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase9`
    * `docker compose up -d`
* **Méthode 2 : Commit Git**
    * `git add .`
    * `git commit -m "Fin de la Phase 9 (v4.2) - Upload RAG implémenté et testé (base durcie)"`

---

Fin de la Phase 9 (Révisée v4.2).

```markdown
---

## Phase 10 : Intégration de l'Outil RAG dans l'Agent (Révisée v4.2 - Finale)

**Objectif :** Activer la capacité de l'Agent LangChain à utiliser **consciemment** la base de données RAG (ChromaDB) comme un **outil** dédié pour rechercher des informations dans les documents chargés par l'utilisateur. Ceci finalise l'architecture cible de l'assistant sur la base durcie.

**Prérequis :** Phases 0-9 terminées, fonctionnelles et idéalement sauvegardées. L'upload RAG (Phase 9) fonctionne et des documents ont été indexés.

**Approche :** Mise à jour finale de `app.py` pour implémenter la fonction `run_rag_retriever` et activer le `rag_tool` correspondant dans la liste `active_tools` de l'Agent Executor.

---

### Étape 10.1 : Mettre à jour le Fichier `app.py` (Activation Outil RAG)

* **Explication :** C'est la mise à jour finale de `app.py` pour cette installation de base. Nous implémentons la fonction `run_rag_retriever` qui interroge le VectorStore et formatte les résultats, puis nous ajoutons le `rag_tool` à la liste des outils actifs de l'agent dans `@cl.on_chat_start`.
* **Action :** Ouvrez `app.py` dans `~/projet-khaldounia/assistant-core/app/`.
* **Action :** Remplacez **l'intégralité** de son contenu par cette version **finale complète** (v4.2) :

```python
# Fichier: app.py (Version Finale Complète - Phase 10 v4.2 - Outil RAG Actif)
# Rôle: Application Khaldounia (Agent avec TOUS les outils: Web, Img, Sys, RAG - Robuste)

import chainlit as cl
import os, requests, base64, time, logging
from pathlib import Path
import chromadb

# Imports Robustesse & Sécurité
from tenacity import retry, stop_after_attempt, wait_fixed
from langchain_core.prompts.input import sanitize_input

# Configuration Logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Imports Langchain (Tous les composants nécessaires)
logging.info("--- Début Importation Langchain (Phase 10 Rev 4.2 - Finale) ---")
from langchain_community.chat_models import ChatOllama
from langchain_community.embeddings import SentenceTransformerEmbeddings
from langchain_community.vectorstores import Chroma as ChromaLangchainStore
from langchain.agents import AgentExecutor, create_react_agent, Tool
from langchain_community.tools import DuckDuckGoSearchRun
from langchain import hub
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
from langchain_community.document_loaders import PyPDFLoader, TextLoader, UnstructuredFileLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
logging.info("--- Fin Importation Langchain ---")

# --- Configuration Globale (inchangée) ---
# ... (coller la config globale inchangée) ...
logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


# --- Fonctions Utilitaires (Durcies - Incluant RAG) ---

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def transcribe_audio(audio_bytes: bytes) -> str | None:
    # ... (Implémentation transcribe_audio durcie) ...
    api_url = f"{WHISPER_SERVER_URL}/asr?encode=true&task=transcribe&language=fr&output=json"; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url}"); try: response = requests.post(api_url, files=files, timeout=60); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:50]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
def text_to_speech(text: str) -> bytes | None:
    # ... (Implémentation text_to_speech durcie) ...
    api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue: {e}", exc_info=True); return None

@retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
def generate_image_tool(prompt: str) -> str:
    # ... (Implémentation generate_image_tool durcie) ...
    api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, ...}; logging.info(f"[IMG] Appel API SD: '{prompt[:50]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[IMG] Erreur requête SD ({e}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"

def _is_path_safe(requested_path_str: str) -> Path | None:
    # ... (Implémentation _is_path_safe durcie) ...
    try: if not requested_path_str or "/" in requested_path_str or "\\" in requested_path_str or ".." in requested_path_str: logging.warning(f"[_is_path_safe] Nom fichier invalide/complexe: '{requested_path_str}'"); return None; target_path_relative = Path(requested_path_str); if target_path_relative.parent != Path('.'): logging.warning(f"[_is_path_safe] Sous-dossier non autorisé: '{requested_path_str}'"); return None; safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True); requested_path_abs = (safe_dir_abs / target_path_relative).resolve(strict=False); if requested_path_abs == safe_dir_abs or requested_path_abs.is_relative_to(safe_dir_abs): if requested_path_abs != safe_dir_abs: return requested_path_abs; logging.warning(f"[_is_path_safe] Chemin hors limites: '{requested_path_abs}' vs '{safe_dir_abs}'"); return None; except Exception as e: logging.error(f"[_is_path_safe] Erreur validation '{requested_path_str}': {e}", exc_info=True); return None

def list_notes_safe() -> str:
    # ... (Implémentation list_notes_safe durcie) ...
    logging.info(f"[NOTES] Action: Lister notes dans {SAFE_NOTES_DIR}"); try: SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); files = [f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()]; return "Notes personnelles existantes:\n- " + "\n- ".join(files) if files else "Dossier notes vide."; except Exception as e: logging.error(f"[NOTES] Erreur list_notes: {e}", exc_info=True); return f"Erreur listage notes: {e}"

def save_note_safe(filename: str, content: str) -> str:
    # ... (Implémentation save_note_safe durcie) ...
    logging.info(f"[NOTES] Action: Sauvegarde note '{filename}'"); safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide."; try: safe_full_path.parent.mkdir(parents=True, exist_ok=True); safe_full_path.write_text(content, encoding='utf-8'); logging.info(f"[NOTES] Note sauvée: {safe_full_path}"); return f"Note '{filename}' sauvée."; except Exception as e: logging.error(f"[NOTES] Erreur sauvegarde '{filename}': {e}", exc_info=True); return f"Erreur sauvegarde note '{filename}': {e}"

def read_note_safe(filename: str) -> str:
    # ... (Implémentation read_note_safe durcie) ...
    logging.info(f"[NOTES] Action: Lecture note '{filename}'"); safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide."; if not safe_full_path.is_file(): logging.warning(f"[NOTES] Fichier note inexistant: '{filename}'"); return f"Erreur: Fichier note '{filename}' inexistant."; try: content = safe_full_path.read_text(encoding='utf-8'); logging.info(f"[NOTES] Note lue: {safe_full_path}"); return f"Contenu de '{filename}':\n\n{content}"; except Exception as e: logging.error(f"[NOTES] Erreur lecture '{filename}': {e}", exc_info=True); return f"Erreur lecture note '{filename}': {e}"

# --- Fonction Wrapper pour Outil RAG (Implémentée pour Phase 10) ---
def run_rag_retriever(query: str) -> str:
    """Exécute la recherche RAG et formate les documents pour l'agent."""
    logging.info(f"\n[RAG Tool] Outil RAG demandé pour query: '{query}'")
    retriever = cl.user_session.get("retriever") # Récupère le retriever de la session
    if not retriever:
        logging.error("[RAG Tool] Retriever non trouvé dans la session.")
        return "Erreur: Recherche de documents personnels indisponible (Retriever non initialisé)."

    try:
        # Utilisation synchrone pour l'instant (invoke)
        # Pour async : docs = await cl.make_async(retriever.ainvoke)(query)
        logging.info(f"[RAG Tool] Invocation du retriever pour: '{query}'")
        docs = retriever.invoke(query) # Lance la recherche synchrone

        if docs:
            # Formatte les documents pour la sortie de l'outil
            formatted_docs = "\n\n".join([f"Extrait pertinent {i+1}:\n{doc.page_content}" for i, doc in enumerate(docs)])
            logging.info(f"[RAG Tool] {len(docs)} documents trouvés et formatés.")
            # Renvoyer le contenu trouvé à l'agent
            # NOTE: On pourrait limiter la taille de la réponse si elle est trop grande pour le contexte LLM
            max_length = 3000 # Limite arbitraire (à ajuster)
            if len(formatted_docs) > max_length:
                 formatted_docs = formatted_docs[:max_length] + "\n[... Contenu tronqué ...]"
                 logging.warning(f"[RAG Tool] Contenu RAG tronqué à {max_length} caractères.")
            return f"Voici des extraits pertinents trouvés dans vos documents personnels concernant '{query}':\n{formatted_docs}"
        else:
            logging.info("[RAG Tool] Aucun document pertinent trouvé par le retriever.")
            return "Aucun document pertinent trouvé dans la base de documents personnels pour cette question."

    except Exception as e:
        # En cas d'erreur pendant la recherche RAG (ex: connexion ChromaDB échoue)
        logging.error(f"[RAG Tool] Erreur lors de l'invocation du retriever: {e}", exc_info=True)
        return f"Erreur lors de la recherche dans les documents personnels: {e}"
# --- Fin Fonction Wrapper RAG ---


# --- Initialisation Globale (inchangée) ---
# ... (Code d'init globale embeddings, chroma_client, retriever) ...
logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info("[INIT] Embeddings..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info(f"[INIT] Embedding Model '{EMBEDDING_MODEL_NAME}' prêt."); logging.info("[INIT] Client ChromaDB..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


# --- Logique Chainlit ---

@cl.on_chat_start
async def start_chat():
    """Initialisation session chat -> Agent avec TOUS les outils actifs (y compris RAG)."""
    logging.info("\n--- Nouvelle Session Chat (Phase 10 v4.2 - Agent FINAL avec Outil RAG) ---")
    if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
         logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
         await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

    try:
        llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
        logging.info("[Session Start] LLM pour Agent initialisé.")

        # --- AGENT SETUP : Activation de TOUS les outils ---
        logging.info("[Session Start] Configuration de l'Agent ReAct...")
        # 1. Définir TOUS les outils
        search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
        image_tool = Tool( name="Generateur Image", func=generate_image_tool, description="Créé une image." )
        list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="Liste les notes." )
        save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="Sauve une note. Input: 'nom_fichier.txt, contenu'." )
        read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="Lit une note. Input: nom_du_fichier.txt." )
        # <<< DÉFINITION OUTIL RAG (Phase 10) >>>
        rag_tool = Tool(
            name="Recherche Document Personnel",
            func=run_rag_retriever, # Utilise la fonction wrapper implémentée ci-dessus
            description=( "INDISPENSABLE pour rechercher et répondre en utilisant les informations spécifiques des documents PDF/TXT/DOCX fournis par l'utilisateur (fichiers chargés). "
                          "Utilise cet outil EN PRIORITÉ si la question concerne des informations personnelles, des projets spécifiques décrits dans les documents, "
                          "ou si on demande de chercher dans 'mes fichiers', 'mes documents', ou un document spécifique." )
        )
        # <<< FIN DÉFINITION OUTIL RAG >>>

        # 2. Sélectionner outils ACTIFS (Phase 10 - TOUT EST ACTIF)
        active_tools = [
            search_tool,
            image_tool,
            list_notes_tool,
            save_note_tool,
            read_note_tool,
            rag_tool  # <<<=== OUTIL RAG ACTIVÉ ICI
        ]
        logging.info(f"[Session Start] Outils actifs pour l'agent ({len(active_tools)}): {[tool.name for tool in active_tools]}")

        # 3. Prompt & Agent ReAct (inchangés)
        react_prompt = hub.pull("hwchase17/react")
        agent = create_react_agent(llm, active_tools, react_prompt)
        logging.info("[Session Start] Agent ReAct créé.")

        # 4. AgentExecutor (inchangé, utilise la liste complète `active_tools`)
        agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
        cl.user_session.set("agent_executor", agent_executor)
        logging.info(f"[Session Start] Agent Executor final ({len(active_tools)} outils) initialisé.")
        # --- FIN AGENT SETUP ---

        # Stocker retriever/vector store pour upload
        cl.user_session.set("retriever", ia_components["retriever"])
        cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

        # Inputs & Upload (inchangé)
        logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
        await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
        cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

        await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v4.2 final avec tous les outils).", author="Assistant Khaldounia" ).send()
        logging.info("--- Session Chat Prête (Agent FINAL) ---")

    except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


# process_and_respond (inchangé - utilise l'agent executor qui a maintenant accès à RAG Tool)
# ... (Coller ici la fonction process_and_respond complète de Phase 8 v4.2) ...
async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1]; image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += " (Erreur: fichier introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée").send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion.", author="Erreur Système"); logging.info("--- Fin Traitement Input ---")


# on_message (inchangé)
@cl.on_message
async def on_text_message(message: cl.Message):
    # ... (Coller code on_message de Phase 8 v4.2) ...
    logging.info(f"\n--- Msg Texte UI ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur sanitization/traitement message texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message.").send()

# on_audio_end (inchangé)
@cl.on_audio_end
async def on_audio_end(audio: cl.Audio):
    # ... (Coller code on_audio_end de Phase 8 v4.2) ...
    logging.info(f"\n--- Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, mime: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None: transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide/None."); await transcript_msg.update(content="Pas de son détecté ou transcription vide."); except Exception as e: logging.error(f"[STT] Échec final transcription/sanitization: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription après tentatives.") ; logging.info("--- Fin Traitement Audio ---")


# on_file_upload (inchangé - Logique d'indexation RAG de Phase 9)
# ... (Coller ici la fonction @cl.on_file_upload complète de Phase 9 v4.2) ...
@cl.on_file_upload(accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)
async def on_file_upload(files: list[cl.File]):
    logging.info(f"\n--- Fichiers Reçus pour RAG ({len(files)}) ---"); if not files: return; vector_store_lc_session = cl.user_session.get("vector_store_lc"); if not vector_store_lc_session: logging.error("[RAG Upload] VectorStore non trouvé."); await cl.Message(content="Erreur: Base RAG non prête.").send(); return; await cl.Message(content=f"Réception {len(files)} fichier(s)...").send(); text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200); successful_files = []; failed_files = [];
    for file in files: file_msg = cl.Message(content=f"Traitement `{file.name}`...", author="Système"); await file_msg.send(); temp_file_path = None;
    try: temp_file_path = UPLOAD_TEMP_DIR / file.name; UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); await cl.make_async(temp_file_path.write_bytes)(file.content); file_ext = os.path.splitext(file.name)[1].lower(); loader = None;
    if file_ext == ".pdf": loader = PyPDFLoader(str(temp_file_path))
    elif file_ext == ".txt": loader = TextLoader(str(temp_file_path), encoding='utf-8')
    elif file_ext == ".docx": loader = UnstructuredFileLoader(str(temp_file_path))
    else: await file_msg.update(content=f"Format `{file.name}` non supporté."); failed_files.append(file.name); continue;
    logging.info(f"[RAG Upload] Load {file.name}..."); documents = await cl.make_async(loader.load)(); logging.info(f"[RAG Upload] Split {file.name}..."); chunks = await cl.make_async(text_splitter.split_documents)(documents); logging.info(f"[RAG Upload] Chunks: {len(chunks)}");
    if not chunks: await file_msg.update(content=f"Contenu vide `{file.name}`."); failed_files.append(file.name); continue;
    logging.info(f"[RAG Upload] Add chunks {file.name} to Chroma..."); await cl.make_async(vector_store_lc_session.add_documents)(chunks); logging.info(f"[RAG Upload] Chunks ajoutés."); await file_msg.update(content=f"`{file.name}` ajouté ({len(chunks)} chunks)."); successful_files.append(file.name);
    except Exception as e: error_text = f"Erreur `{file.name}`: {e}"; logging.error(f"[RAG Upload] {error_text}", exc_info=True); await file_msg.update(content=error_text); failed_files.append(file.name);
    finally: if temp_file_path and await cl.make_async(temp_file_path.exists)(): try: await cl.make_async(temp_file_path.unlink)(); logging.info(f"[RAG Upload] Temp file deleted: {temp_file_path}"); except Exception as del_e: logging.error(f"[RAG Upload] Erreur suppression temp file: {del_e}");
    summary = "Indexation terminée.\n"; summary += f"- Succès: {', '.join(successful_files)}\n" if successful_files else ""; summary += f"- Échecs: {', '.join(failed_files)}" if failed_files else ""; await cl.Message(content=summary).send(); logging.info("--- Fin Traitement Fichiers ---")

```

* **Action :** Sauvegardez ce fichier `app.py` final (v4.2). Il représente l'état complet de l'application avec tous les outils actifs, y compris l'outil RAG, sur une base durcie.

---

### Étape 10.2 : Exécuter cette Phase (Activation Finale Outil RAG)

* **Explication :** Cette étape active l'outil RAG dans l'agent en utilisant la version finale de `app.py`.
* **Action :**
    1.  Assurez-vous que `~/projet-khaldounia/assistant-core/app/app.py` contient le code complet de l'Étape 10.1 ci-dessus.
    2.  Arrêtez les services Docker :
        ```bash
        cd ~/projet-khaldounia/assistant-core
        docker compose down
        ```
    3.  Reconstruisez l'image `assistant_app` (car `app.py` a changé) et redémarrez tous les services (6 services) :
        ```bash
        docker compose up -d --build
        ```

---

### Étape 10.3 : Tester l'Intégration Complète RAG/Agent

* **Action (Après redémarrage Étape 10.2) :**
    1.  Vérifiez que tous les 6 conteneurs sont `Up` et `(healthy)` (`docker ps`).
    2.  Ouvrez Chainlit (`http://127.0.0.1:8001`).
    3.  Chargez un ou plusieurs documents de test via l'upload (comme en Phase 9), si ce n'est déjà fait. (Ex: `infos_khaldounia.txt` contenant "Le modèle d'embedding est all-MiniLM-L6-v2" et `projet_aladin.docx` mentionnant "réunion du 15 mars").
    4.  Posez une question dont la réponse est **uniquement** dans un des documents : "Quel modèle d'embedding est utilisé ?" ou "Quelle est la date de la réunion Aladin ?"
    5.  **Observez attentivement les logs `assistant_app` (`docker logs -f assistant_app`) :**
        * L'agent (`verbose=True`) doit "penser" et **choisir** l'outil `"Recherche Document Personnel"`. La description forte de l'outil ajoutée en Phase 10 devrait l'y aider.
        * Vous devriez voir les logs `[RAG Tool] Outil RAG demandé...` et `[RAG Tool] Invocation du retriever...` de la fonction `run_rag_retriever`.
        * L'agent reçoit les extraits trouvés comme "Observation" (ex: "Voici des extraits pertinents trouvés...") et les utilise pour formuler sa réponse finale.
    6.  Vérifiez que la réponse dans Chainlit est correcte (basée sur le document) et est accompagnée de l'audio.
    7.  **Contre-Tests (Vérifier que l'agent choisit le bon outil) :**
        * Question Web ("Météo aujourd'hui à Épinay-sur-Seine ?") -> Doit utiliser `Recherche Web DuckDuckGo`.
        * Question Image ("Dessine un logo pour Khaldounia") -> Doit utiliser `Generateur Image`.
        * Question Notes ("Liste mes notes personnelles") -> Doit utiliser `Lister Notes Personnelles`.
        * Question simple ("Comment vas-tu ?") -> Doit répondre directement sans outil.
* **En Cas de Problème :**
    * **Agent n'utilise jamais l'outil RAG alors qu'il le devrait :** Logs `assistant_app` (`verbose=True`) -> "Thought". La description de l'outil `Recherche Document Personnel` est-elle assez directive ? Le LLM arrive-t-il à discriminer RAG vs Web ? (Peut nécessiter ajustement description ou prompt système avancé plus tard).
    * **Agent choisit outil RAG, mais échoue / Erreur :** Logs `assistant_app` : Erreur dans `run_rag_retriever` (`[ERREUR] RAG Tool...`) ? Erreur retournée par retriever (connexion ChromaDB KO ?) ? Vérifier service `chromadb` (état healthy, logs).
    * **Agent utilise outil RAG, mais "Aucun document pertinent" alors que info existe :** Vérifier Dépannage Phase 9.5 (Qualité document ? Chunking ? Embedding ?). Observer l'Observation reçue par l'agent dans les logs pour voir si le texte pertinent était bien remonté mais mal interprété.

---
### **Conclusion Finale de l'Installation de Base**

Félicitations ! Si vous avez atteint ce point et que les tests de la Phase 10 sont concluants, vous disposez d'une instance fonctionnelle, sécurisée et robuste de l'assistant Khaldounia avec toutes ses capacités de base (LLM, TTS, STT, RAG, WebSearch, ImageGen, Notes) opérationnelles.

Le plan d'installation est maintenant terminé.

---

Fin de la Phase 10 (Révisée v4.2 - Finale).
```

```markdown
---


## Annexe A : Stratégie de Vérification et Débogage (Finale V3)

**Objectif :** Fournir une méthode structurée pour identifier et résoudre les problèmes potentiels lors de l'installation ou de l'utilisation, optimisée pour l'interaction avec l'IA guide.

**Méthode Recommandée :**

1.  **Isoler le Problème :**
    * À quelle **Phase** et **Étape** l'erreur s'est-elle produite ?
    * Quelle était la **dernière commande** exécutée ou l'**action** effectuée ?
    * Quel **service Docker** semble en cause (`docker ps`) ? Vérifiez son statut (`Up`, `Exited`, `Restarting`...).

2.  **Capturer les Informations Essentielles :**
    * Le **message d'erreur exact** affiché dans le terminal ou dans l'interface Chainlit.
    * Les **logs pertinents** du ou des conteneurs suspects. **Utilisez principalement la commande `docker logs [nom_du_conteneur]` pour obtenir la sortie standard et les erreurs du service.** (Utilisez `docker logs --tail 100 [nom]` pour les dernières lignes ou `docker logs -f [nom]` pour suivre en direct).

3.  **Fournir Infos à l'IA Guide & Demander un Diagnostic Ciblé :**
    * **Utilisez le Modèle de Rapport ci-dessous** pour fournir les informations à l'IA (moi, Gemini) de manière structurée.
    * **Principe de Diagnostic d'Abord (avec jugement) :** L'IA Guide **doit** chercher à comprendre la cause racine en suivant une approche hiérarchique :
        * **Niveau 1 (Vérifications Service) :** L'IA doit d'abord demander des vérifications de base : État des conteneurs (`docker ps -a`), logs récents du conteneur suspect (`docker logs --tail 100 ...`).
        * **Niveau 2 (Vérifications API/Connectivité) :** Si le service tourne mais l'erreur persiste, l'IA doit demander de tester la connectivité ou l'API via `curl` (voir tests en Phase 4.3bis et Annexe F) ou le Script de Validation (Annexe E).
        * **Niveau 3 (Vérifications Ressources Système) :** Si des erreurs de VRAM/RAM/CPU sont suspectées (lenteur extrême, crashs OOM), l'IA doit demander le résultat de `nvidia-smi` (dans WSL) et éventuellement `free -h` ou `htop` (dans WSL).
        * **Niveau 4 (Vérifications Fichiers/Volumes) :** Si une erreur suggère un fichier manquant ou un problème de droits, l'IA doit demander une vérification spécifique via `docker exec -it [conteneur] ls -l [chemin]` ou `docker inspect [volume/conteneur]`.
        * **Niveau 5 (Solutions & Reconstruction) :** Ce n'est qu'après avoir une meilleure idée de la cause que l'IA proposera une modification ciblée (config, code). La reconstruction complète (`docker compose down && docker compose up -d --build`) ou la suppression de volumes ne doivent être suggérées qu'en dernier recours après échec des autres niveaux.
    * Pour une **erreur de syntaxe évidente**, l'IA *peut* proposer une correction directe, mais doit revenir au diagnostic si cela échoue.
    * L'objectif est d'éviter les suggestions à l'aveugle.

4.  **Consulter les IA dans VS Code (Copilot/Claude - Si Nécessaire) :**
    * Uniquement si l'erreur semble être une **erreur de code Python** dans `app.py` et que le diagnostic via les logs n'est pas clair.

5.  **Consulter Claude via API Externe (Dernier Recours) :**
    * Si problème complexe non résolu après diagnostic approfondi.

6.  **Appliquer et Tester Itérativement :**
    * Appliquez **une seule** suggestion de correction à la fois.
    * Retestez l'étape ou la fonctionnalité.
    * **Rapportez le résultat** (succès ou nouvelle erreur/logs) via le modèle ci-dessous.
    * **Recherche Externe (si l'IA est bloquée) :** Si, après diagnostic, l'IA guide ne parvient pas à identifier la cause d'une erreur très spécifiques, elle peut suggérer de rechercher le message d'erreur exact sur des sites comme GitHub Issues (pour le projet concerné : Ollama, Chainlit, Langchain, etc.) ou Stack Overflow.

7.  **Nettoyage Ciblé après Échec (sur suggestion de l'IA) :**
    * Si une étape échoue de manière répétée, si une image/volume spécifique semble corrompu(e), ou si une réinitialisation complète d'un composant est jugée nécessaire après diagnostic, l'IA guide **peut vous suggérer** d'exécuter des commandes de nettoyage Docker **avant** de retenter l'installation ou la configuration de ce composant.
    * **Objectif :** Assurer un état "propre" pour la nouvelle tentative et libérer de l'espace si des artefacts inutiles ont été créés.
    * **Commandes Possibles (Validez TOUJOURS l'impact avec l'IA avant exécution !) :**
        * 🟢 `docker container prune` : Supprime tous les conteneurs arrêtés. Faible risque. Utile pour nettoyer les conteneurs échoués.
        * 🟢 `docker image prune` : Supprime les images "dangling" (non taguées, souvent des couches intermédiaires après un build). Faible risque.
        * 🟠 `docker builder prune` : Supprime tout le cache de build Docker. Ralentira le prochain `docker compose build` mais ne supprime pas de données utiles. Risque moyen (impact temps).
        * 🟠 `docker image rm [image:tag]` (ex: `docker image rm ghcr.io/coqui-ai/tts:v0.22.0`) : Supprime une image spécifique. Nécessitera un retéléchargement complet si l'image est à nouveau requise. Risque moyen (impact temps).
        * 🔴 `docker volume rm [nom_volume_specifique]` (ex: `docker volume rm khaldounia_ollama_data`) : **Supprime DÉFINITIVEMENT un volume nommé et TOUTES les données qu'il contient** (ex: tous les modèles Ollama téléchargés). Risque élevé de perte de données. À n'utiliser que pour une réinitialisation complète d'un service.
        * 🔴 `docker compose down -v` : Arrête et supprime les conteneurs du projet ET **supprime DÉFINITIVEMENT les volumes nommés associés définis dans `docker-compose.yml`**. Risque élevé de perte de données (Ollama, ChromaDB, Cache Embedding, Config/Modèles/Sorties SD).
        * 🔴 `docker compose down --rmi all` : Comme `down -v`, mais supprime **AUSSI les images Docker** utilisées par les services. Risque élevé + longs retéléchargements.
    * **Important :** L'IA doit justifier pourquoi une commande (surtout 🔴 ou 🟠) est suggérée. Confirmez que vous comprenez les conséquences avant de taper `y` pour valider l'exécution.


**Modèle de Rapport d'Erreur (à fournir à l'IA Guide) :**

```text
-----------------------------------
RAPPORT D'ERREUR - Projet Khaldounia
-----------------------------------
1.  **Phase et Étape :** [Ex: Phase 7, Étape 7.5]
2.  **Dernière Action Effectuée :** [Ex: J'ai demandé "Dessine un chat" dans Chainlit]
3.  **Comportement Observé / Erreur Exacte :**
    [Copiez ici le message d'erreur complet ou décrivez le comportement anormal]

4.  **Logs Pertinents :**
    (Utilisez `docker logs --tail 100 nom_conteneur` pour chaque conteneur pertinent)
    * Logs `assistant_app`:
        ```
        [Collez les logs ici]
        ```
    * Logs [Autre conteneur pertinent, ex: `stable_diffusion_webui`]:
        ```
        [Collez les logs ici]
        ```
    * Logs [Encore un autre si besoin, ex: `ollama`]:
        ```
        [Collez les logs ici]
        ```
5.  **Question / Aide Demandée :** [Ex: L'agent utilise bien l'outil image, mais j'ai une erreur dans les logs SD et pas d'image. Que dois-je vérifier ensuite (Niveau 1/2 diagnostic) ?]
-----------------------------------
Conseils Supplémentaires :

Utilisez le Script de Validation (Annexe E) et les tests curl (Phase 4.3bis, Annexe F).
Analysez les logs de l'Agent (verbose=True dans docker logs assistant_app).
Pensez aux vérifications système (espace disque df -h, RAM WSL2 via free -h ou .wslconfig, conflits de ports netstat -ano | findstr LISTEN).
L'IA peut vous demander de vérifier des fichiers/dossiers dans un conteneur via docker exec -it [nom_conteneur] ls -l [chemin_interne].


## Annexe B : Notes sur la Sauvegarde (Finale - MAJ Gitignore)

**Objectif :** Fournir des conseils simples pour sauvegarder les éléments importants de votre projet Khaldounia.

* **Données Critiques à Sauvegarder :**
    * **Dossier Projet Entier :** Le plus simple est de sauvegarder régulièrement l'intégralité du dossier `~/projet-khaldounia/` depuis WSL. Il contient :
        * `assistant-core/` (avec `docker-compose.yml`, `app/Dockerfile`, `app/requirements.txt`, `app/app.py`, et le fichier `.env` si vous l'avez créé).
        * `tts-data/` (avec votre `speaker_ref.wav` et le cache des modèles TTS).
        * Les notes utilisateurs créées dans `assistant-core/app/user_notes/`.
        * Les images générées dans `assistant-core/app/generated_images/`.
        * (Note : Les historiques de chat sont dans un volume Docker séparé, voir ci-dessous).
    * **Volumes Docker Nommés :** Les données persistantes importantes sont dans les volumes Docker (`khaldounia_ollama_data`, `khaldounia_chroma_data`, `khaldounia_sd_config`, `khaldounia_sd_models`, `khaldounia_sd_outputs`, `khaldounia_chat_history`). Ceux-ci ne sont *pas* directement dans `~/projet-khaldounia/`. Leur sauvegarde nécessite des stratégies Docker spécifiques (non couvertes ici, voir documentation Docker) ou la sauvegarde de l'ensemble des données Docker de WSL. C'est pourquoi il est important de sauvegarder aussi les *sources* :
    * **Documents RAG Originaux :** Conservez une copie de tous les fichiers PDF, TXT, DOCX chargés.
    * **Liste des Modèles Ollama Installés :** Utile pour savoir quoi retélécharger (`docker exec -it ollama ollama list`).
    * **Modèles Stable Diffusion Téléchargés :** Conservez une copie des fichiers `.safetensors`.
    * **Ce Fichier Plan :** Gardez ce fichier plan à jour et sauvegardé !

* **Stratégie de Sauvegarde Simple :**
    1. **Arrêter les Services Docker :** `cd ~/projet-khaldounia/assistant-core && docker compose down`.
    2. **Sauvegarder le Dossier Projet (Code & Config) :**
        * Copiez `~/projet-khaldounia` vers un emplacement sûr (Disque externe, Cloud...).
        * Ou `tar czvf ~/khaldounia_backup_$(date +%Y%m%d).tar.gz ~/projet-khaldounia`.
    3. **Sauvegarder les Modèles Externes & Docs RAG :** Assurez-vous qu'ils sont copiés séparément.
    4. **(Fortement Recommandé) Utiliser Git pour le Code/Config :** Initialisez (`git init`), utilisez un `.gitignore` (voir exemple ci-dessous), commitez régulièrement (`git add .`, `git commit`). C'est idéal pour suivre les changements du code (`app.py`) et de la configuration (`docker-compose.yml`, `.env`, `Dockerfile`, `requirements.txt`).
        * **Exemple `.gitignore` (Mis à jour) :**
          ```gitignore
          # Fichier de configuration sensible/local
          .env

          # Caches et Données Volumineuses (gérées par volumes Docker)
          /tts-data/cache/
          /assistant-core/app/embedding_cache/
          /assistant-core/app/generated_images/
          /assistant-core/app/temp_uploads/
          /assistant-core/app/user_notes/ # Vous pouvez choisir de les commiter ou non
          /assistant-core/app/chat_history/ # Ignorer le point de montage de l'historique

          # Fichiers Python temporaires/compilés
          *.pyc
          __pycache__/
          *.egg-info/

          # Fichiers spécifiques IDE/OS
          .vscode/
          .DS_Store
          *.log
          ```

* **Restauration (Simplifiée - Code/Config) :**
    1. Restaurez le dossier `projet-khaldounia` depuis votre sauvegarde (`cp -r` ou `tar xzvf` ou `git checkout`).
    2. Replacez vos modèles SD `.safetensors` sources sur votre PC.
    3. Assurez-vous que le fichier `.env` est présent dans `assistant-core/app/` avec la bonne configuration.
    4. Lancez `cd ~/projet-khaldounia/assistant-core && docker compose up -d --build`. Les volumes nommés seront recréés (vides au début pour Ollama/Chroma/Historique).
    5. Recopiez les modèles SD via `docker cp` (Phase 7.6).
    6. Téléchargez à nouveau les modèles Ollama (`docker exec ... ollama pull ...`).
    7. Réuploadez vos documents RAG via l'interface Chainlit (Phase 9).



## Annexe C : Notes sur la Gestion des Modèles Ollama (Finale)

**Objectif :** Rappeler comment interagir avec les modèles LLM gérés par le service Ollama dans Docker.

* **Commandes Utiles (terminal WSL) :**
    * **Lister les Modèles Téléchargés :**
        ```bash
        docker exec -it ollama ollama list
        ```
    * **Télécharger ou Mettre à Jour un Modèle :** (Remplacez `nom_modele:tag` par le modèle souhaité, ex: `mistral:latest`, `llama3:8b`)
        ```bash
        docker exec -it ollama ollama pull nom_modele:tag
        ```
    * **Supprimer un Modèle Local :**
        ```bash
        docker exec -it ollama ollama rm nom_modele:tag
        ```
    * **Voir les Logs du Service Ollama :** (Utile si un modèle ne charge pas)
        ```bash
        docker logs -f ollama
        ```
    * **Changer le Modèle Utilisé par Défaut par `app.py` :** Modifiez la variable d'environnement `OLLAMA_MODEL` dans le fichier `docker-compose.yml` (section `environment:` du service `assistant_app`) et redémarrez avec `docker compose up -d --build`.

---

## Annexe D : Notes sur les Mises à Jour Générales (Finale)

**Objectif :** Donner des pistes pour maintenir les différents composants du système à jour **de manière contrôlée**, en utilisant les versions figées comme point de départ.

* **Images Docker (`docker-compose.yml` - Versions Figées) :**
    * **Pourquoi Figer ?** Utiliser des tags spécifiques (ex: `ollama:0.1.41`) garantit la stabilité. `:latest` peut casser la compatibilité sans préavis.
    * **Mise à Jour Contrôlée :**
        1.  Recherchez un nouveau tag de version stable pour l'image (ex: `ollama:0.1.45`). Lisez les notes de version ("changelog") si possible pour anticiper les changements majeurs (API modifiée, chemins changés...).
        2.  **Sauvegardez votre projet entier ! (Annexe B)**
        3.  Modifiez le tag dans votre fichier `docker-compose.yml`.
        4.  Arrêtez les services : `docker compose down`.
        5.  Supprimez l'ancienne image localement (optionnel mais propre) : `docker rmi ollama/ollama:0.1.41` (exemple).
        6.  Téléchargez spécifiquement la nouvelle image : `docker pull ollama/ollama:0.1.45` (exemple).
        7.  Redémarrez les services : `docker compose up -d`. (Utilisez `--build` seulement si `Dockerfile` ou `requirements.txt` ont aussi changé).
        8.  Testez attentivement (Script Annexe E + tests fonctionnels). En cas de problème, revenez à l'ancien tag et restaurez votre sauvegarde si nécessaire.

* **Dépendances Python (`requirements.txt` - Versions Figées) :**
    * **Pourquoi Figer ?** Utiliser `==X.Y.Z` garantit la compatibilité testée.
    * **Mise à Jour Contrôlée (avec GRANDE Prudence) :**
        1.  Identifiez les nouvelles versions souhaitées. **Attention :** Vérifiez la compatibilité entre les nouvelles versions (surtout Langchain/Chainlit/Pydantic). C'est souvent complexe.
        2.  **Sauvegardez votre projet entier et/ou utilisez Git !**
        3.  Modifiez les numéros de version `==X.Y.Z` dans `requirements.txt`.
        4.  Arrêtez les services : `docker compose down`.
        5.  Forcez la reconstruction de l'image `assistant_app` pour installer les nouvelles versions et redémarrez :
            ```bash
            docker compose up -d --build
            ```
        6.  Testez **très attentivement**. Des modifications dans `app.py` sont **souvent nécessaires** après des mises à jour majeures de Langchain/Chainlit (fonctions dépréciées, arguments modifiés...). Préparez-vous à déboguer le code `app.py`.

* **Modèles IA (Ollama, XTTS, SD) :**
    * **Ollama :** Via `docker exec ... ollama pull nom_nouveau_modele:tag`. Mettre à jour = `rm` + `pull`.
    * **XTTS / SD :** Téléchargez les nouveaux fichiers modèles (`.pth`, `.safetensors`) et remplacez/ajoutez-les dans les volumes Docker mappés (`tts-data/cache` ou `khaldounia_sd_models`) via `docker cp` ou accès direct. Pensez à vérifier la compatibilité avec la version du service (ex: un nouveau modèle XTTS pourrait nécessiter une image `:tts` plus récente).

**Conseil Général :** Sauvegardez toujours avant une mise à jour. Mettez à jour un seul composant majeur à la fois et testez intensivement.

---

## Annexe E : Script de Validation Rapide (`validate_khaldounia.sh`)

**Objectif :** Exécuter rapidement une série de tests de base pour vérifier que les principaux services Docker sont démarrés et que leurs APIs répondent correctement après une installation ou un redémarrage.

**Utilisation :**

1.  **Créer le Script :**
    * Dans votre terminal WSL, placez-vous dans `~/projet-khaldounia/assistant-core`.
    * Créez un nouveau fichier : `nano validate_khaldounia.sh`
    * Collez le contenu suivant dans le fichier :

    ```bash
    #!/bin/bash

    # Script de validation rapide pour les services Khaldounia v1.0

    # Couleurs
    GREEN='\033[0;32m'
    RED='\033[0;31m'
    YELLOW='\033[1;33m'
    NC='\033[0m' # No Color

    # Fonction pour afficher les messages
    log_ok() { echo -e "${GREEN}[OK]${NC} $1"; }
    log_err() { echo -e "${RED}[ERREUR]${NC} $1"; }
    log_warn() { echo -e "${YELLOW}[WARN]${NC} $1"; }
    log_info() { echo -e "\n--- $1 ---"; }

    HAS_ERROR=false

    # --- Vérification des Conteneurs Docker ---
    log_info "Vérification des Conteneurs Docker Khaldounia (Attendus: 6 après Phase 7+)"
    EXPECTED_CONTAINERS=("ollama" "xtts_server" "chromadb" "whisper_server" "stable_diffusion_webui" "assistant_app")
    ALL_RUNNING=true
    for container in "${EXPECTED_CONTAINERS[@]}"; do
        # Vérifie si le conteneur existe ET est en cours d'exécution (status=running)
        if ! docker ps --format '{{.Names}}' --filter "name=^/${container}$" --filter "status=running" | grep -q "^${container}$"; then
            # Si non démarré, vérifie s'il existe mais est arrêté
            if docker ps -a --format '{{.Names}}' --filter "name=^/${container}$" | grep -q "^${container}$"; then
                 log_err "Conteneur '$container' existe mais n'est PAS DEMARRE (status non 'running') !"
            else
                 log_err "Conteneur '$container' est INTROUVABLE !"
            fi
            ALL_RUNNING=false
            HAS_ERROR=true
        else
            log_ok "Conteneur '$container' est démarré."
        fi
    done

    if [ "$ALL_RUNNING" = false ]; then
        log_err ">>> Certains conteneurs ne sont pas démarrés. Vérifiez 'docker ps -a' et les logs. Arrêt probable des tests API. <<<"
        # Optionnel : sortir ici si on ne veut pas tester les APIs si les conteneurs ne tournent pas
        # exit 1
    else
        log_ok "--- Tous les conteneurs attendus sont démarrés. ---"
    fi


    # --- Tests API ---
    log_info "Test API Ollama (localhost:11434)"
    if curl --fail --connect-timeout 5 -s -X POST http://localhost:11434/api/generate -d '{"model":"mistral:latest","prompt":"ping","stream":false}' > /dev/null 2>&1; then
        log_ok "API Ollama répond."
    else
        log_err "Échec connexion/réponse API Ollama. (Conteneur démarré ? Modèle 'mistral:latest' présent ?)"
        HAS_ERROR=true
    fi

    log_info "Test API ChromaDB (localhost:8002)"
    if curl --fail --connect-timeout 5 -s http://localhost:8002/api/v1/heartbeat 2>&1 | grep -q "heartbeat"; then
        log_ok "API ChromaDB répond."
    else
        log_err "Échec connexion/réponse API ChromaDB."
        HAS_ERROR=true
    fi

    log_info "Test API Whisper (localhost:9000)"
    if curl --fail --connect-timeout 5 -s -I http://localhost:9000/health 2>/dev/null | grep -q "200 OK"; then
         log_ok "API Whisper répond (endpoint /health OK)."
    elif curl --fail --connect-timeout 5 -s -o /dev/null http://localhost:9000/ 2>/dev/null; then
         log_ok "Serveur Whisper semble répondre (port 9000 ouvert, racine testée)."
    else
        log_warn "Échec connexion API Whisper (port 9000). Vérifiez logs 'whisper_server'."
    fi

    log_info "Test API XTTS (localhost:8050)"
    if curl --fail --connect-timeout 5 -s -o /dev/null http://localhost:8050/ 2>/dev/null; then
         log_ok "Serveur XTTS semble répondre (port 8050 ouvert)."
    else
        log_warn "Échec connexion API XTTS (port 8050). Vérifiez logs 'xtts_server'."
    fi

    log_info "Test API Stable Diffusion (localhost:7860/docs)"
    if curl --fail --connect-timeout 10 -s http://localhost:7860/docs -o /dev/null 2>/dev/null; then
        log_ok "API Stable Diffusion semble répondre (endpoint /docs accessible)."
    else
        log_warn "Échec connexion API Stable Diffusion (/docs). Vérifiez logs 'stable_diffusion_webui' et option --api."
    fi

    log_info "Test Interface Chainlit (localhost:8001)"
     if curl --fail --connect-timeout 5 -s -o /dev/null http://localhost:8001/ 2>/dev/null; then
         log_ok "Serveur Chainlit semble répondre (port 8001 ouvert)."
    else
        log_err "Échec connexion Interface Chainlit (port 8001). Vérifiez logs 'assistant_app'."
        HAS_ERROR=true
    fi

    # --- Test Commande Interne Ollama ---
    log_info "Test Liste Modèles Ollama (via docker exec)"
    # Capturer la sortie et le code de retour séparément
    OLLAMA_LIST_OUTPUT=$(docker exec ollama ollama list 2>&1)
    OLLAMA_LIST_RC=$?
    if [ $OLLAMA_LIST_RC -eq 0 ]; then
        log_ok "Commande 'ollama list' exécutée avec succès dans le conteneur."
        log_info "Modèles Ollama installés :"
        echo "$OLLAMA_LIST_OUTPUT" | sed 's/^/  /' # Affiche la liste indentée
    else
        log_err "Échec exécution 'ollama list' dans le conteneur."
        echo "Sortie d'erreur: $OLLAMA_LIST_OUTPUT"
        HAS_ERROR=true
    fi


    # --- Conclusion ---
    echo -e "\n----------------------------------"
    if [ "$HAS_ERROR" = true ]; then
        log_err "--- Validation terminée avec une ou plusieurs ERREURS critiques détectées ---"
        echo "Veuillez examiner les erreurs [ERREUR] ci-dessus et consulter les logs des conteneurs concernés."
        exit 1
    else
        log_ok "--- Validation terminée avec succès (tests de base OK) ---"
        echo "Tous les tests critiques sont réussis. Les services principaux semblent démarrés et accessibles."
        echo "Effectuez des tests fonctionnels via l'interface Chainlit pour une validation complète."
        exit 0
    fi
    ```
    * Sauvegardez et fermez nano (`Ctrl+O`, `Entrée`, `Ctrl+X`).

2.  **Rendre le Script Exécutable :**
    ```bash
    chmod +x validate_khaldounia.sh
    ```

3.  **Lancer le Script (Après `docker compose up -d`) :**
    ```bash
    ./validate_khaldounia.sh
    ```

* **Résultat Attendu :** Le script vérifie chaque point et affiche `[OK]`, `[WARN]` ou `[ERREUR]`. S'il termine avec "Validation terminée avec succès", l'infrastructure de base est probablement opérationnelle. Sinon, il indique les erreurs critiques détectées.

---

## Conclusion Finale du Document Plan

Ce document `Installation_Khaldounia_Plan.md` contient maintenant toutes les phases (0 à 10) et annexes (A à E) révisées et finalisées pour l'installation, la configuration initiale, l'utilisation de base, le débogage, la maintenance et la validation de votre assistant Khaldounia, en tenant compte des suggestions d'amélioration pour un environnement WSL2 et un utilisateur novice.

---
**FIN DU PLAN D'INSTALLATION ET DE DOCUMENTATION**
