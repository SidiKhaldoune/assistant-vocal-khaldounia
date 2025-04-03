
## Phase 0 : Prérequis Système et Logiciels (Révisée v5.0 - Sécurité / Robustesse)

**Objectif :** S'assurer que l'environnement Windows / WSL2 est **parfaitement opérationnel** et que les logiciels de base (Docker, Pilotes NVIDIA récents, VS Code) sont correctement installés et configurés pour supporter l'exécution de l'IA locale avec **accélération GPU vérifiée** et **suffisamment de ressources allouées**.

**[⚠️⚠️⚠️] CRITICITÉ : Cette phase est la plus critique. Une erreur ici empêchera tout le reste de fonctionner.** Prenez votre temps et vérifiez chaque sous-étape méticuleusement. N'hésitez pas à demander de l'aide si un test échoue.

---

### **Étape 0.0 : Recommandations Matérielles & Estimations VRAM**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Comprendre les besoins matériels pour éviter les mauvaises surprises de performance ou les crashs dus au manque de ressources.
* **Recommandations Indicatives :**
    * **GPU :** NVIDIA avec support **CUDA 12+**. **Minimum 12Go VRAM fortement recommandé** (16Go+ idéal pour tout faire tourner confortablement, 24Go+ pour la haute résolution SD). Votre RTX 5090 est excellente.
    * **RAM Système :** **Minimum 32 Go** recommandé (16Go peuvent suffire si vous désactivez le GPU pour certains services, mais ce sera très juste).
    * **Stockage :** SSD NVMe avec **au moins 100 Go libres** (modèles IA + cache + volumes Docker peuvent occuper beaucoup d'espace).
* **Estimations VRAM (Approximatives - Peuvent varier !) :**
    *(Tableau identique à la version précédente, insistant sur le besoin potentiel >16Go)*
    | Composant            | Modèle Exemple (Plan)   | VRAM Estimée (GPU ON) | Notes                                  |
    | :------------------- | :---------------------- | :-------------------- | :----------------------------------------- |
    | Ollama               | Mistral 7B Q4\_K\_M     | \~ 4-6 Go             | Dépend taille/quantization              |
    | XTTS v2              | `multi-dataset/xtts_v2` | \~ 2-6 Go             | Varie selon charge, peut être gourmand |
    | Stable Diffusion     | SD 1.5 (ex: DreamShaper) | \~ 4-8 Go (512x512)   | >10Go+ pour haute résolution/batch > 1 |
    | Whisper              | `small` model           | \~ 1-2 Go             | Modèles `medium`/`large` > 5-10 Go        |
* **Conclusion VRAM :** Lancer SD + Ollama + XTTS sur GPU simultanément **peut facilement dépasser 16Go de VRAM**. La désactivation du GPU (`deploy:` commenté dans `docker-compose.yml`, voir Phase 2+) pour XTTS et/ou Whisper sera **probablement nécessaire** même sur des cartes avec 16Go, et peut être envisagée sur des cartes 24Go pour libérer de la VRAM pour SD. **Soyez prêt à ajuster cela.**
* **Attente et Transition :** Compris ? Passons à la vérification de WSL2.

---

### **Étape 0.1 : Installation / Vérification de WSL2 et Ubuntu**

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Peut nécessiter redémarrage/téléchargement)
* **Contexte et Objectif :** Assurer que WSL version 2 est installé, à jour, et qu'Ubuntu est la distribution par défaut. C'est la base de notre environnement Linux sous Windows.
* **Prérequis :** Accès administrateur à Windows. Connexion Internet.
* **Action :**
    1.  Ouvrez `PowerShell` ou `cmd` **en tant qu'administrateur** sous Windows.
    2.  **Commande (Installation initiale si WSL n'est pas du tout présent) :**
        ```powershell
        wsl --install
        ```
        *(Installe WSL, composants Windows nécessaires, et Ubuntu LTS par défaut. Un redémarrage Windows sera probablement requis.)*
    3.  **Commande (Vérification POST-INSTALL/REDÉMARRAGE - CRUCIALE) :** Ouvrez une invite de commande Windows **normale** :
        ```powershell
        wsl -l -v
        ```
        * **Explication :** Doit lister vos distributions. Vérifiez que `Ubuntu` (ou le nom de votre distro Ubuntu) a bien `STATE: Running` (ou `Stopped`) et **`VERSION: 2`**.
        * **Correction si Version 1 :** `wsl --set-version VotreNomUbuntu 2` (remplacez `VotreNomUbuntu`). Cela peut prendre du temps.
        * **Correction si non démarré :** Essayez `wsl` pour lancer Ubuntu.
    4.  **Commande (Définir Ubuntu par défaut - si plusieurs distros) :**
        ```powershell
        wsl --set-default VotreNomUbuntu # Remplacez par le nom exact listé précédemment
        ```
    5.  **Commande (Mise à jour WSL - Recommandé) :**
        ```powershell
        wsl --update
        ```
    6.  **Commande (Définir WSL2 par défaut pour futures installations - Bonne pratique) :**
        ```powershell
        wsl --set-default-version 2
        ```
* **Vérification Post-Exécution :** La commande `wsl -l -v` montre Ubuntu en version 2. Vous pouvez ouvrir un terminal Ubuntu en tapant `wsl` ou en lançant l'application Ubuntu.
* **Attente et Transition :** Confirmez que WSL2 et Ubuntu sont fonctionnels. Avez-vous rencontré des problèmes ?

---

### **Étape 0.2 : Installation / Vérification Pilotes NVIDIA (Support GPU WSL2)**

* **[⚠️⚠️⚠️] [⏱️⏱️⏱️]** (Téléchargement + Installation + Redémarrage potentiel)
* **Contexte et Objectif :** Indispensable pour que WSL2 et Docker puissent utiliser votre GPU NVIDIA. Nécessite des pilotes récents compatibles WSL2.
* **Prérequis :** Connaître le modèle exact de votre GPU (RTX 5090). Windows 10 (build 21H2+) ou Windows 11.
* **Action :**
    1.  **Téléchargez les pilotes NVIDIA les plus récents** pour votre système : [https://www.nvidia.fr/Download/index.aspx?lang=fr](https://www.nvidia.fr/Download/index.aspx?lang=fr)
        * Sélectionnez Type de produit : **GeForce**
        * Série de produits : **GeForce RTX 50 Series** (ou équivalent si le nom change)
        * Gamme de produits : **GeForce RTX 5090** (ou votre modèle exact)
        * Système d'exploitation : **Windows 10 64-bit** ou **Windows 11**
        * Type de téléchargement : **Game Ready Driver (GRD)** (recommandé) ou Studio Driver (SD)
        * Langue : Français
    2.  Lancez l'installateur téléchargé.
    3.  Choisissez **"Pilote graphique NVIDIA et GeForce Experience"** (ou juste le pilote si vous préférez).
    4.  Sélectionnez **"Personnalisée (Avancé)"** lors des options d'installation.
    5.  Cochez la case **"Effectuer une nouvelle installation"**. Cela nettoie les anciens pilotes et peut résoudre des conflits.
    6.  Terminez l'installation. **Redémarrez Windows si demandé.**
* **Vérification (ABSOLUMENT CRUCIALE - DANS WSL) :**
    1.  **Ouvrez votre terminal WSL/Ubuntu** (tapez `wsl` dans l'invite Windows ou lancez l'app Ubuntu).
    2.  **Tapez la commande :**
        ```bash
        nvidia-smi
        ```
* **Résultat Attendu :** La commande doit s'exécuter **SANS ERREUR** et afficher un tableau avec les informations de votre RTX 5090, la version du pilote NVIDIA, et la version CUDA supportée (doit être 12.x).
* **En Cas d'Échec (`nvidia-smi: command not found` ou autre erreur) :**
    * **Problème le plus courant :** L'installation du pilote ou WSL est incomplète/corrompue.
    * **Actions :**
        1.  Assurez-vous d'avoir redémarré Windows après l'installation du pilote.
        2.  Ré-exécutez `wsl --update` dans PowerShell Admin.
        3.  Réinstallez le pilote NVIDIA (avec "nouvelle installation").
        4.  Vérifiez que votre version de Windows est compatible (Minimum Win10 21H2).
        5.  **Ne continuez PAS tant que `nvidia-smi` ne fonctionne pas correctement DANS WSL.**
* **Attente et Transition :** Confirmez que `nvidia-smi` fonctionne dans WSL. Avez-vous eu des erreurs ?

---

### **Étape 0.3 : Installation / Configuration Docker Desktop**

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Installation + Configuration)
* **Contexte et Objectif :** Installer et configurer Docker Desktop pour qu'il utilise WSL2 et puisse accéder au GPU NVIDIA. C'est l'outil qui gérera nos conteneurs.
* **Prérequis :** WSL2 fonctionnel (Étape 0.1), Pilotes NVIDIA fonctionnels DANS WSL (Étape 0.2).
* **Action :**
    1.  Téléchargez et installez la dernière version stable de **Docker Desktop** depuis le site officiel : [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
    2.  Pendant l'installation, assurez-vous que l'option **"Use WSL 2 instead of Hyper-V"** (ou similaire) est **cochée**.
    3.  Lancez Docker Desktop après l'installation. Il peut prendre quelques minutes pour démarrer son moteur. L'icône de baleine dans la barre d'état système doit être stable.
    4.  **Configuration (TRÈS IMPORTANT) :**
        * Ouvrez les **Settings** (icône roue dentée) dans Docker Desktop.
        * Allez dans **General**. Vérifiez que **"Use the WSL 2 based engine"** est **coché et activé**.
        * Allez dans **Resources > WSL Integration**.
            * Assurez-vous que **"Enable integration with my default WSL distro"** est **activé (ON)**.
            * Sous **"Enable integration with additional distros"**, vérifiez que le slider pour votre distribution **Ubuntu** est aussi **activé (ON)**.
        * **(Optionnel - Vérification GPU)** Allez dans **Resources > Advanced**. Il n'y a généralement plus d'option explicite "Enable GPU Compute" ici si l'intégration WSL fonctionne, mais vérifiez qu'il n'y a pas de message d'erreur lié au GPU.
    5.  Cliquez sur **"Apply & Restart"** si vous avez fait des changements.
* **Vérification (Multi-étapes - CRUCIALES) :** Ouvrez votre **terminal WSL/Ubuntu** :
    1.  **Test Docker de Base :**
        ```bash
        docker --version
        docker compose version # 'compose' est maintenant intégré
        docker run --rm hello-world
        ```
        *(Doit afficher les versions et exécuter le conteneur `hello-world` sans erreur.)*
    2.  **TEST ACCÈS GPU DANS DOCKER (LE PLUS IMPORTANT) :**
        ```bash
        docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi
        ```
        * **Explication :** Cette commande télécharge une image CUDA de test (peut prendre un peu de temps la 1ère fois) et essaie d'exécuter `nvidia-smi` *à l'intérieur* du conteneur en utilisant le GPU.
        * **Résultat Attendu :** Doit afficher la même sortie que `nvidia-smi` exécuté directement dans WSL (les infos de votre RTX 5090).
        * **En Cas d'Échec (Erreur `docker: Error response from daemon: ... NVIDIA driver ...`, ou `could not select device driver ...`, ou `no CUDA-capable device is detected`) :** C'est un **bloqueur critique**. Cela signifie que Docker ne peut pas accéder à votre GPU.
            * **Causes Possibles :** Problème d'installation/version pilote NVIDIA, problème configuration WSL Integration dans Docker Desktop, version WSL/Noyau Linux obsolète (`wsl --update`), conflit logiciel.
            * **Actions :** Revérifiez Étape 0.2, revérifiez la config Docker Desktop WSL Integration, redémarrez Docker Desktop, redémarrez WSL (`wsl --shutdown` dans PowerShell puis relancez Ubuntu), redémarrez Windows.
            * **Ne continuez PAS tant que ce test GPU Docker ne réussit pas.**
* **Attente et Transition :** Confirmez que le test `hello-world` ET le test `nvidia/cuda ... nvidia-smi` réussissent.

---

### **Étape 0.3bis : Configuration des Permissions Docker (Facultatif mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Éviter de devoir taper `sudo` avant chaque commande `docker` dans WSL en ajoutant votre utilisateur WSL au groupe `docker`.
* **Prérequis :** Docker Desktop installé et fonctionnel. Être dans le terminal WSL/Ubuntu.
* **Action :**
    1.  Exécutez la commande suivante :
        ```bash
        sudo usermod -aG docker $USER
        ```
    2.  **ACTION CRUCIALE : Fermez COMPLÈTEMENT votre terminal WSL/Ubuntu, puis rouvrez-en un nouveau.** Le changement de groupe ne prend effet que dans les nouvelles sessions.
* **Vérification Post-Exécution :** Dans le **nouveau** terminal WSL, tapez :
    ```bash
    docker ps
    ```
    * *(La commande doit s'exécuter sans demander de mot de passe `sudo`.)*
    * *(Optionnel : Tapez `groups` et vérifiez que `docker` apparaît dans la liste.)*
* **Attente et Transition :** Confirmez si vous avez pu exécuter `docker ps` sans `sudo`.

---

### **Étape 0.3ter : Vérification/Configuration Limite RAM WSL2 (Prévention Crashs OOM)**

* **[⚠️⚠️] [⏱️]** (Modification fichier + redémarrage WSL)
* **Contexte et Objectif :** Par défaut, WSL2 limite la RAM qu'il peut utiliser (souvent 50% de la RAM hôte ou 8Go max). Avec plusieurs modèles IA, cela peut causer des crashs "Out Of Memory" (OOM). Il est **fortement recommandé** d'augmenter cette limite via un fichier `.wslconfig`.
* **Prérequis :** Connaître la quantité de RAM totale de votre PC Windows.
* **Action (Côté Windows) :**
    1.  Ouvrez l'Explorateur de Fichiers Windows.
    2.  Dans la barre d'adresse, tapez `%UserProfile%` et appuyez sur Entrée. Cela vous amène à votre dossier utilisateur (ex: `C:\Users\VotreNom`).
    3.  Cherchez un fichier nommé `.wslconfig` (avec un point au début).
        * **S'il n'existe pas :** Faites Clic droit > Nouveau > Document texte. Nommez-le `.wslconfig.` (avec le point final). Windows demandera si vous voulez changer l'extension, dites Oui. Le fichier devrait maintenant s'appeler `.wslconfig`.
        * **S'il existe :** Ouvrez-le avec un éditeur de texte (Bloc-notes, VS Code...).
    4.  **Ajoutez ou modifiez le contenu** pour qu'il ressemble à ceci (adaptez la valeur `memory`) :
        ```ini
        [wsl2]
        memory=48GB  # EXEMPLE pour 64GB RAM système. Mettez ~75% de votre RAM totale. Adaptez !
        processors=12 # Optionnel: Adaptez au nb de coeurs CPU souhaité pour WSL (ex: 75% des coeurs logiques)
        swap=16GB     # Optionnel: Taille du fichier swap WSL si la RAM est dépassée.
        # gpuCompute=true # Normalement plus nécessaire si pilotes et Docker sont OK
        ```
        * **IMPORTANT :** Choisissez une valeur `memory` raisonnable (ex: 60-80% de votre RAM totale). Laisser trop peu pour Windows peut aussi causer des problèmes. Pour une RTX 5090 et des modèles lourds, une allocation généreuse (si RAM dispo) est préférable. `48GB` est un exemple pour un système avec 64GB. Pour 32GB système, essayez `24GB`.
    5.  Sauvegardez le fichier `.wslconfig`.
    6.  **Appliquer la Modification :** Ouvrez une invite de commande Windows (cmd ou PowerShell, pas besoin d'admin) et tapez :
        ```powershell
        wsl --shutdown
        ```
        * *(Cela arrête toutes les instances WSL. Attendez quelques secondes.)*
* **Vérification Post-Exécution :** Ouvrez un **nouveau** terminal WSL/Ubuntu. La nouvelle limite de RAM sera active. (Vous pouvez vérifier avec `free -h` dans WSL, la colonne `total` pour `Mem:` devrait refléter la limite configurée).
* **Attente et Transition :** Confirmez que vous avez créé/modifié `.wslconfig` et exécuté `wsl --shutdown`.

---

### **Étape 0.4 : Installation / Configuration Visual Studio Code (VS Code)**

* **[⚠️] [⏱️⏱️]** (Installation + Extension)
* **Contexte et Objectif :** Mettre en place l'éditeur recommandé pour modifier facilement les fichiers du projet directement dans l'environnement WSL.
* **Prérequis :** WSL/Ubuntu fonctionnel.
* **Action :**
    1.  Installez/Mettez à jour **VS Code** depuis : [https://code.visualstudio.com/](https://code.visualstudio.com/)
    2.  Lancez VS Code.
    3.  Allez dans l'onglet **Extensions** (icône carrés sur le côté gauche, ou `Ctrl+Shift+X`).
    4.  Recherchez et installez l'extension nommée **"WSL"** publiée par **Microsoft**.
* **Vérification / Utilisation :**
    1.  Une fois l'extension installée, cliquez sur l'icône verte `><` en bas à gauche de la fenêtre VS Code.
    2.  Choisissez **"Connect to WSL"** dans le menu déroulant qui apparaît en haut.
    3.  Une **nouvelle fenêtre VS Code** va s'ouvrir. En bas à gauche, elle doit indiquer `WSL: Ubuntu` (ou le nom de votre distro), confirmant que vous êtes connecté à votre environnement Linux.
    4.  Ouvrez un terminal intégré dans cette nouvelle fenêtre (`Terminal` > `Nouveau Terminal` ou `Ctrl+Maj+ù`). Il doit s'agir d'un **terminal bash Ubuntu** (`votrenom@machine:~$`), pas d'un terminal Windows. C'est dans **ce type de terminal VS Code connecté à WSL** que vous exécuterez la majorité des commandes des phases suivantes.
    5.  Vous pouvez maintenant ouvrir le dossier de votre projet (qui sera créé en Phase 1) via `Fichier > Ouvrir un dossier...` et en naviguant vers `/home/votrenomwsl/projet-khaldounia`.
* **Attente et Transition :** Confirmez que vous avez installé VS Code, l'extension WSL, et que vous pouvez ouvrir un terminal bash Ubuntu connecté à WSL dans VS Code.

---

### **Étape 0.5 : Installation de Git (Optionnel mais Fortement Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Installer Git pour le contrôle de version. Extrêmement utile pour suivre les modifications des fichiers de configuration/code et pouvoir revenir en arrière facilement en cas de problème (voir Annexe B).
* **Prérequis :** Être dans un terminal WSL/Ubuntu (celui de VS Code connecté ou un terminal Ubuntu séparé).
* **Action :**
    ```bash
    sudo apt update && sudo apt install git -y
    ```
* **Vérification :**
    ```bash
    git --version
    ```
    * *(Doit afficher la version de Git installée, ex: `git version 2.34.1`).*
* **Attente et Transition :** Confirmez si Git est installé.

---
**Tous les prérequis critiques ont-ils été validés avec succès ?**

Fin de la Phase 0 (Révisée v5.0).



## Phase 1 : Création de la Structure des Dossiers (Révisée v5.0)

**Objectif :** Mettre en place l'arborescence organisée dans WSL pour accueillir les fichiers de configuration, le code du projet Khaldounia, et préparer l'emplacement **sur l'hôte WSL** pour le fichier vocal de référence. Ce fichier sera copié dans un volume Docker nommé ultérieurement (Étape 4.2 bis).

* **Note WSL/Linux :** Rappelez-vous que Linux est sensible à la casse (ex: `MonDossier` != `mondossier`). Utilisez la tabulation pour auto-compléter les noms de dossiers/fichiers et éviter les erreurs.

---

### Étape 1.1 : Se Placer dans le Répertoire Personnel

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Assurer que nous commençons au bon endroit dans l'arborescence WSL, généralement le dossier personnel de l'utilisateur.
* **Prérequis :** Terminal WSL/Ubuntu ouvert (idéalement via VS Code connecté à WSL).
* **Action :**
    ```bash
    cd ~
    ```
    * **Explication :** `cd` change de répertoire, `~` est un raccourci pour votre dossier personnel (ex: `/home/votrenomwsl`).
* **Vérification Post-Exécution :**
    ```bash
    pwd
    ```
    * **Résultat Attendu :** Affiche votre chemin personnel (ex: `/home/votrenomwsl`).
* **Attente et Transition :** Confirmez que vous êtes dans votre répertoire personnel.

---

### Étape 1.2 : Créer le Dossier Principal du Projet

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Créer le dossier racine qui contiendra tous les éléments du projet Khaldounia.
* **Prérequis :** Être dans le répertoire personnel (`~`).
* **Action :**
    ```bash
    mkdir projet-khaldounia
    ```
    * **Explication :** `mkdir` crée un nouveau répertoire.
* **Vérification Post-Exécution :**
    ```bash
    ls -d projet-khaldounia/
    ```
    * **Résultat Attendu :** Affiche `projet-khaldounia/` sans erreur.
* **Attente et Transition :** Confirmez la création du dossier.

---

### Étape 1.3 : Entrer dans le Dossier du Projet

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Se positionner dans le dossier racine du projet pour les étapes suivantes.
* **Prérequis :** Le dossier `projet-khaldounia` existe.
* **Action :**
    ```bash
    cd projet-khaldounia
    ```
* **Vérification Post-Exécution :**
    ```bash
    pwd
    ```
    * **Résultat Attendu :** Affiche le chemin complet vers `projet-khaldounia` (ex: `/home/votrenomwsl/projet-khaldounia`).
* **Attente et Transition :** Confirmez que vous êtes dans le bon dossier.

---

### Étape 1.4 : Créer les Sous-Dossiers Principaux

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Créer la structure de base : `assistant-core` pour le code et la configuration Docker, et `tts-data` pour préparer les données XTTS sur l'hôte.
* **Prérequis :** Être dans le dossier `projet-khaldounia`.
* **Action (crée les deux dossiers en une commande) :**
    ```bash
    mkdir assistant-core tts-data
    ```
* **Vérification Post-Exécution :**
    ```bash
    ls -l
    ```
    * **Résultat Attendu :** Doit lister les deux dossiers `assistant-core` et `tts-data`.
* **Attente et Transition :** Confirmez la création des sous-dossiers.

---

### Étape 1.5 : Créer les Sous-Dossiers Spécifiques

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Créer le dossier `app` (qui contiendra le code Python) à l'intérieur de `assistant-core`, et les dossiers `speakers` et `cache` (pour info) dans `tts-data`. L'option `-p` évite les erreurs si les dossiers parents existent déjà.
* **Prérequis :** Être dans le dossier `projet-khaldounia`.
* **Action :**
    ```bash
    mkdir -p assistant-core/app tts-data/speakers tts-data/cache
    ```
* **Vérification Post-Exécution :**
    ```bash
    ls -R assistant-core/ tts-data/
    ```
    * **Résultat Attendu :** Doit montrer `app` dans `assistant-core` et `cache`, `speakers` dans `tts-data`.
* **Attente et Transition :** Confirmez la création de la structure finale.

---

### Étape 1.6 : Préparer et Placer le Fichier Vocal de Référence (`speaker_ref.wav`) sur l'Hôte WSL

* **[⚠️⚠️] [⏱️⏱️]** (Nécessite la création/préparation du fichier audio)
* **Contexte et Objectif :** XTTS a besoin d'un fichier audio WAV de référence (voix à cloner). Nous allons placer ce fichier dans un dossier **sur notre système WSL** (`tts-data/speakers`). **Important :** Ce fichier servira de **source** pour une copie ultérieure (via `docker cp` en Phase 4) dans le **volume Docker nommé** `khaldounia_xtts_speakers` utilisé par le conteneur XTTS. Il n'est PAS utilisé directement depuis cet emplacement.
* **Prérequis :** Avoir un fichier `speaker_ref.wav` prêt.
    * **Conseils Création (si besoin) :**
        * Qualité : Enregistrement clair, sans bruit de fond, 15-30 secondes.
        * Format : **WAV, mono, 16 bits PCM, fréquence 22050 Hz ou 24000 Hz.** Utilisez un logiciel comme Audacity (gratuit) pour enregistrer et exporter au bon format. Nommez-le exactement `speaker_ref.wav`.
* **Action (Manuelle - Copie du fichier) :** Une fois le fichier `speaker_ref.wav` prêt sur votre machine Windows :
    1.  **Méthode 1 (VS Code - Recommandé) :**
        * Ouvrez le dossier `~/projet-khaldounia/tts-data/speakers` dans VS Code connecté à WSL.
        * Faites glisser-déposer votre fichier `speaker_ref.wav` depuis l'Explorateur Windows dans le panneau de l'explorateur de fichiers de VS Code (dans le dossier `speakers`).
    2.  **Méthode 2 (Commande `cp` - Adaptez les chemins) :**
        * Ouvrez un terminal WSL.
        * Trouvez le chemin Windows de votre fichier (ex: `C:\Users\Halim\Downloads\speaker_ref.wav`).
        * Le chemin correspondant dans WSL sera `/mnt/c/Users/Halim/Downloads/speaker_ref.wav` (adaptez la lettre de lecteur `c` et le chemin).
        * Exécutez la commande (adaptez le chemin source !) :
        ```bash
        cp /mnt/c/Users/Halim/Downloads/speaker_ref.wav ~/projet-khaldounia/tts-data/speakers/
        ```
* **Vérification Post-Exécution :**
    ```bash
    ls -l ~/projet-khaldounia/tts-data/speakers/
    ```
    * **Résultat Attendu :** Doit lister `speaker_ref.wav` avec une taille de fichier non nulle.
* **Attente et Transition :** Confirmez que le fichier `speaker_ref.wav` est bien présent dans `~/projet-khaldounia/tts-data/speakers/`.

---

### Étape 1.7 : Vérifier la Structure Complète Créée sur l'Hôte WSL

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Confirmer visuellement que toute l'arborescence locale est correcte avant de passer à la configuration Docker.
* **Prérequis :** Être dans le dossier `~/projet-khaldounia`.
* **Action :**
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
    *(Vérifiez que `speaker_ref.wav` est bien listé dans `speakers` et que les autres dossiers sont présents).*
* **Attente et Transition :** Confirmez que la structure affichée correspond à celle attendue.

---

Fin de la Phase 1 (Révisée v5.0). La structure de base est prête sur votre système WSL.




## Phase 2 : Configuration Docker Compose (Base - Révisée v5.0 - Sécurité / Robustesse)

**Objectif :** Créer le fichier `docker-compose.yml` pour les services de base (Ollama avec auto-pull, XTTS avec volumes nommés, ChromaDB) et l'application assistante. Utilise des versions d'images Docker figées, des **volumes nommés partout**, inclut des **healthchecks**, **restreint les ports** à `127.0.0.1`, et ajoute des **commentaires clairs sur la gestion VRAM**.

**Emplacement :** `~/projet-khaldounia/assistant-core/docker-compose.yml`

---

### Étape 2.1 : Se Placer dans le Dossier `assistant-core`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Le fichier `docker-compose.yml` doit être créé à la racine du contexte de build de notre application principale.
* **Prérequis :** Le dossier `~/projet-khaldounia/assistant-core` existe (Phase 1). Être dans le terminal WSL/Ubuntu.
* **Action :**
    ```bash
    cd ~/projet-khaldounia/assistant-core
    ```
* **Vérification Post-Exécution :**
    ```bash
    pwd
    ```
    * **Résultat Attendu :** Doit afficher `/home/VotreNomWSL/projet-khaldounia/assistant-core`.
* **Attente et Transition :** Confirmez que vous êtes dans le bon dossier.

---

### Étape 2.2 : Créer/Modifier le Fichier `docker-compose.yml`

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Préparer le fichier central qui orchestrera tous nos services Docker.
* **Prérequis :** Être dans `assistant-core`. VS Code connecté à WSL est ouvert ou un éditeur comme `nano` est disponible.
* **Action :**
    * **Méthode 1 (VS Code - Recommandé) :**
        * Si vous n'avez pas encore ouvert le dossier dans VS Code : tapez `code .` dans le terminal pour ouvrir `assistant-core` dans VS Code.
        * Dans l'explorateur de fichiers de VS Code (panneau de gauche), faites un clic droit dans la zone vide et choisissez "Nouveau fichier".
        * Nommez le fichier `docker-compose.yml`.
    * **Méthode 2 (`nano`) :**
        ```bash
        nano docker-compose.yml
        ```
* **Vérification Post-Exécution :** Un fichier `docker-compose.yml` vide est ouvert dans votre éditeur, prêt à recevoir le contenu YAML.
* **Attente et Transition :** Préparez-vous à copier-coller le contenu YAML.

---

### Étape 2.3 : Coller le Contenu YAML Complet (Révisé v5.0)

* **[⚠️⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Définir les services Ollama, XTTS, ChromaDB et l'application assistante, avec leurs configurations (versions, ports locaux, volumes nommés, dépendances saines, healthchecks) et des avertissements VRAM clairs.
* **Prérequis :** Le fichier `docker-compose.yml` est ouvert dans un éditeur.
* **Action :** Copiez et collez **l'intégralité** du bloc YAML ci-dessous dans le fichier `docker-compose.yml`. Lisez attentivement les commentaires, notamment sur la VRAM.
    ```yaml
    # Fichier: docker-compose.yml (Révisé v5.0 - Phase 2 Complète)
    # Base Khaldounia: Ollama, XTTS, ChromaDB, App
    # Intègre: Versions figées, Volumes nommés, Ports 127.0.0.1, Healthchecks, Depends_on healthy, Avertissements VRAM

    version: '3.8' # Version de la syntaxe Compose

    services:
      # --- Service LLM (Ollama) ---
      ollama:
        image: ollama/ollama:0.1.41 # <== VERSION FIGÉE (Stable et testée pour ce plan)
        container_name: ollama
        # Commande pour auto-pull le modèle par défaut au 1er démarrage
        command: sh -c "ollama serve" # Auto-pull retiré ici, sera fait via exec si besoin pour simplifier 1er démarrage
        deploy: # Configuration Accès GPU NVIDIA
          resources:
            reservations:
              devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        volumes:
          # Volume nommé pour persister les modèles Ollama téléchargés
          - khaldounia_ollama_data:/root/.ollama
        ports:
          # Port API Ollama, restreint à l'accès local (127.0.0.1) pour la sécurité
          - "127.0.0.1:11434:11434" # format: "ip_hôte:port_hôte:port_conteneur"
        networks:
          - assistant_network # Attache le service au réseau défini plus bas
        restart: unless-stopped # Redémarre automatiquement sauf si arrêté manuellement
        healthcheck: # Vérifie si le service est opérationnel
          test: ["CMD", "curl", "-f", "http://localhost:11434/"] # Teste l'API de base
          interval: 30s   # Intervalle entre les vérifications
          timeout: 10s    # Temps max pour la réponse
          retries: 3      # Nb de tentatives avant de marquer comme 'unhealthy'
          start_period: 60s # Temps de grâce au démarrage avant les 1ères vérifs

      # --- Service Synthèse Vocale (XTTS) ---
      xtts_server:
        image: ghcr.io/coqui-ai/tts:v0.22.0 # <== VERSION FIGÉE
        container_name: xtts_server
        # Commande pour démarrer le serveur TTS avec GPU et speaker par défaut
        command: >
          tts-server
          --model_name tts_models/multilingual/multi-dataset/xtts_v2
          --use_cuda true
          --port 8020
          --speaker_wav /app/speakers/speaker_ref.wav
        ports:
          # Port API XTTS, restreint à l'accès local
          - "127.0.0.1:8050:8020"
        volumes:
          # Volume nommé pour le cache des modèles TTS
          - khaldounia_xtts_cache:/root/.local/share/tts
          # Volume nommé pour les fichiers voix de référence (sera peuplé via 'docker cp')
          - khaldounia_xtts_speakers:/app/speakers
        environment:
          - NVIDIA_VISIBLE_DEVICES=all # Nécessaire pour voir le GPU
          - NVIDIA_DRIVER_CAPABILITIES=compute,utility
          - COQUI_TOS_AGREED=1 # Acceptation des termes Coqui
        # [!!! GESTION VRAM CRITIQUE - XTTS !!!]
        # XTTS peut consommer 2-6Go+ de VRAM. Si votre VRAM totale est limitée (< 20-24Go)
        # ou si vous rencontrez des erreurs OOM (Out Of Memory) avec Stable Diffusion plus tard,
        # commentez ENTIÈREMENT la section 'deploy:' ci-dessous pour forcer XTTS à utiliser le CPU (plus lent).
        deploy: # Configuration Accès GPU (À COMMENTER SI VRAM LIMITÉE)
           resources:
             reservations:
               devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        shm_size: '8gb' # Augmente la mémoire partagée, recommandé pour TTS/IA
        networks:
          - assistant_network
        restart: unless-stopped
        healthcheck:
          # Teste si l'API XTTS répond (endpoint /speakers est simple)
          test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"]
          interval: 30s
          timeout: 10s
          retries: 3
          start_period: 180s # Laisse ~3 min pour charger le modèle TTS au démarrage

      # --- Service Base de Données Vectorielle (ChromaDB) ---
      chromadb:
        image: chromadb/chroma:0.5.0 # <== VERSION FIGÉE
        container_name: chromadb
        environment:
          - IS_PERSISTENT=TRUE # Active la persistance
          - ALLOW_RESET=TRUE   # Permet de réinitialiser la DB via API si besoin
          # - ANONYMIZED_TELEMETRY=FALSE # Optionnel: Désactiver la télémétrie anonyme
        volumes:
          # Volume nommé pour stocker la base de données vectorielle (RAG)
          - khaldounia_chroma_data:/chroma
        ports:
          # Port API ChromaDB, restreint à l'accès local
          - "127.0.0.1:8002:8000"
        networks:
          - assistant_network
        restart: unless-stopped
        healthcheck:
          # Teste l'endpoint 'heartbeat' de ChromaDB
          test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"]
          interval: 30s
          timeout: 10s
          retries: 3
          start_period: 30s # ChromaDB démarre assez vite

      # --- Service Application Assistant (LangChain + Chainlit) ---
      assistant_app:
        # Construit l'image localement à partir du dossier ./app et du Dockerfile qu'il contient
        build: { context: ./app }
        container_name: assistant_app
        ports:
          # Port UI Chainlit, restreint à l'accès local
          - "127.0.0.1:8001:8000"
        volumes:
          # Monte le code source local dans le conteneur pour refléter les changements en direct (utile pour dev)
          - ./app:/app
          # Volume nommé pour le cache des modèles d'embedding téléchargés par SentenceTransformer
          - embedding_cache:/app/embedding_cache
          # Volume nommé pour l'historique des chats (sera utilisé plus tard)
          # Note: Le dossier /app/chat_history doit exister dans le conteneur (voir Dockerfile Phase 3)
          - khaldounia_chat_history:/app/chat_history
        environment:
          # Définit les URLs pour que app.py puisse contacter les autres services par leur nom
          # Ces variables sont lues par os.getenv() dans app.py
          - OLLAMA_HOST=http://ollama:11434
          - XTTS_SERVER_URL=http://xtts_server:8020
          - CHROMADB_HOST=chromadb
          - CHROMADB_PORT=8000
          # Les URLs pour Whisper et SD seront ajoutées dans les phases suivantes
          # - WHISPER_SERVER_URL=http://whisper_server:9000
          # - STABLE_DIFFUSION_API_URL=http://stable_diffusion_webui:7860
        depends_on: # Assure que les dépendances démarrent AVANT l'app ET sont SAINES
          ollama:
            condition: service_healthy # Attend que le healthcheck d'Ollama soit OK
          xtts_server:
            condition: service_healthy # Attend que le healthcheck de XTTS soit OK
          chromadb:
            condition: service_healthy # Attend que le healthcheck de ChromaDB soit OK
          # Les dépendances futures (Whisper, SD) devront aussi avoir des healthchecks et être ajoutées ici
          # whisper_server: { condition: service_healthy }
          # stable_diffusion_webui: { condition: service_healthy }
        stdin_open: true # Garde le STDIN ouvert, utile pour attacher un terminal interactif
        tty: true        # Alloue un pseudo-TTY
        networks:
          - assistant_network
        restart: unless-stopped
        healthcheck:
          # Teste si l'UI Chainlit de base répond
          test: ["CMD", "curl", "-f", "http://localhost:8000/"]
          interval: 30s
          timeout: 10s
          retries: 5 # Un peu plus de tentatives pour laisser le temps à Chainlit/app.py de s'initialiser
          start_period: 90s # Laisse 1.5 min pour build/start/init embeddings

    # --- Réseau Docker ---
    # Crée un réseau isolé pour que les services communiquent par leur nom
    networks:
      assistant_network:
        driver: bridge # Type de réseau par défaut, adapté pour un seul hôte
        name: assistant_network # Nom explicite du réseau

    # --- Volumes Docker ---
    # Définit les volumes nommés pour la persistance des données. Ils sont gérés par Docker.
    volumes:
      khaldounia_ollama_data: { driver: local } # Stocke les modèles LLM Ollama
      khaldounia_chroma_data: { driver: local } # Stocke la base de données RAG
      embedding_cache: { driver: local }        # Cache modèles SentenceTransformer
      khaldounia_chat_history: { driver: local } # Stocke l'historique des chats
      khaldounia_xtts_cache: { driver: local }   # Cache modèles/données XTTS
      khaldounia_xtts_speakers: { driver: local } # Stocke fichier(s) voix référence XTTS
      # Les volumes pour Stable Diffusion seront définis en Phase 7
      # khaldounia_sd_config: { driver: local }
      # khaldounia_sd_models: { driver: local }
      # khaldounia_sd_outputs: { driver: local }

    ```
* **Action (Sauvegarde) :** Sauvegardez le fichier `docker-compose.yml` dans votre éditeur (`Ctrl+O`, `Entrée`, `Ctrl+X` dans `nano` ; `Ctrl+S` dans VS Code).
* **Vérification Post-Exécution :** Le fichier `docker-compose.yml` existe dans `assistant-core` et contient le YAML ci-dessus.
* **Attente et Transition :** Le fichier d'orchestration principal est prêt.

---

### Étape 2.4 : Comprendre les Changements Clés (Révisée v5.0)

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** S'assurer que les points importants de cette configuration sont compris.
* **Points Clés de cette Version :**
    * ✅ **Versions Figées :** Utilisation de tags spécifiques pour les images Docker pour la stabilité et la reproductibilité.
    * ✅ **Volumes Nommés Généralisés :** Toutes les données persistantes (modèles Ollama, DB Chroma, cache embeddings, cache TTS, voix TTS, historique chat) utilisent des volumes nommés Docker. C'est robuste, portable et géré par Docker.
    * ✅ **Sécurité des Ports :** Tous les ports exposés sont restreints à `127.0.0.1` (localhost), empêchant l'accès depuis d'autres machines sur le réseau.
    * ✅ **Healthchecks & Dépendances Saines :** Chaque service a une vérification de santé (`healthcheck`), et `assistant_app` attend (`depends_on: condition: service_healthy`) que ses dépendances soient prêtes avant de démarrer, améliorant la stabilité au lancement.
    * ✅ **Réseau Isolé :** Le réseau `assistant_network` permet aux conteneurs de communiquer facilement et de manière sécurisée par leur nom de service (ex: `ollama`, `xtts_server`).
    * ❗ **Note VRAM :** Des commentaires explicites sont ajoutés pour vous rappeler de potentiellement désactiver l'accès GPU (`deploy:`) pour `xtts_server` (et plus tard `whisper_server`) si vous manquez de VRAM.
    * ❗ **Note `speaker_ref.wav` :** Le fichier vocal de référence que vous avez placé dans `tts-data/speakers` (Phase 1) devra être **copié manuellement** dans le volume Docker `khaldounia_xtts_speakers` via la commande `docker cp` après le premier démarrage des conteneurs (voir Étape 4.2 bis).
* **Vérification Post-Exécution :** Assurez-vous d'avoir compris ces points clés.
* **Attente et Transition :** Prêt à configurer l'application elle-même en Phase 3.

Fin de la Phase 2 (Révisée v5.0).



## Phase 3 : Configuration App Assistant (Base - Révisée v5.0 - Dockerfile Optimisé)

**Objectif :** Créer les fichiers essentiels pour le service `assistant_app` :
1.  `Dockerfile` : Définit comment construire l'image Docker (avec Python 3.11 slim, dépendances système, **utilisateur non-root** pour la sécurité, et **ordre optimisé pour le cache**).
2.  `.dockerignore` : (Nouveau) Fichier pour exclure des éléments inutiles du contexte de build Docker.
3.  `requirements.txt` : Liste les dépendances Python avec **versions figées** (identique à v4.1, déjà robuste).
4.  `app.py` : Code Python initial (v4.1) intégrant les bases (imports, config, fonctions TTS/STT durcies mais désactivées, init Chroma/Embeddings, Chainlit basique pour Phase 4).

**Emplacement :** Fichiers à créer/modifier dans `~/projet-khaldounia/assistant-core/app/`

---

### Étape 3.1 : Se Placer dans le Dossier `app`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Se positionner dans le dossier qui contiendra le code source et la configuration de build de l'application assistante.
* **Prérequis :** Être dans le terminal WSL, le dossier `~/projet-khaldounia/assistant-core/app` existe (Phase 1).
* **Action :**
    ```bash
    cd ~/projet-khaldounia/assistant-core/app
    ```
* **Vérification Post-Exécution :**
    ```bash
    pwd
    ```
    * **Résultat Attendu :** Affiche `/home/VotreNomWSL/projet-khaldounia/assistant-core/app`.
* **Attente et Transition :** Confirmez que vous êtes dans le dossier `app`.

---

### Étape 3.2 : Créer/Modifier le Fichier `Dockerfile` (Révisé v5.0 - Optimisé & Sécurisé)

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Définir les instructions pour construire l'image Docker de `assistant_app`. Cette version inclut un utilisateur non-root `appuser`, installe les dépendances système et Python, copie le code, et définit la commande de démarrage. L'ordre est optimisé pour utiliser le cache de build Docker efficacement.
* **Prérequis :** Être dans le dossier `app`.
* **Action :** Créez ou ouvrez le fichier nommé `Dockerfile` (sans extension) dans ce dossier (`app/`).
* **Action :** Collez **l'intégralité** du contenu suivant :

    ```dockerfile
    # Fichier: Dockerfile (Révisé v5.0 - Projet Khaldounia)
    # Rôle: Construit l'image Docker pour le service assistant_app
    # Optimisations: Ordre pour cache, User non-root, Slim base

    # Étape 1: Base d'exécution
    # Utilise une version spécifique de Python 3.11 slim pour la stabilité et la taille
    FROM python:3.11-slim as base

    # Définir les variables d'environnement tôt (non dépendantes des fichiers copiés)
    ENV PYTHONUNBUFFERED=1 \
        # Empêche Python de bufferiser stdout/stderr, utile pour voir les logs en temps réel
        PYTHONDONTWRITEBYTECODE=1 \
        # Empêche Python de créer des fichiers .pyc
        DEBIAN_FRONTEND=noninteractive \
        # Évite les questions interactives lors de l'installation de paquets Debian
        PATH="/home/appuser/.local/bin:${PATH}"
        # Ajoute le répertoire local bin de l'utilisateur au PATH

    # Créer l'utilisateur et le groupe non-root avant toute autre opération
    # Utiliser un UID/GID fixe est une bonne pratique, mais dynamique est plus simple ici.
    RUN groupadd -r appgroup && useradd --no-log-init -r -g appgroup -d /home/appuser -m appuser

    # Définir le répertoire de travail
    WORKDIR /app

    # Installer les dépendances système (avant de copier le code Python)
    # Cette couche sera mise en cache si les paquets ne changent pas.
    RUN apt-get update && apt-get install -y --no-install-recommends \
        # --- Dépendances pour 'unstructured' et traitements de fichiers ---
        poppler-utils \
        # Pour l'extraction de texte depuis PDF
        tesseract-ocr tesseract-ocr-fra \
        # OCR pour images dans PDF/etc. (langue française incluse)
        libreoffice \
        # Pour convertir/extraire depuis des formats Office (ex: .docx via unstructured)
        # --- Autres dépendances utiles ---
        ffmpeg \
        # Pour traitement audio/vidéo (si besoin futur pour STT/TTS ou unstructured)
        curl \
        # Utile pour les healthchecks ou tests API depuis le conteneur
        build-essential \
        # Requis si certaines dépendances Python ont besoin de compiler du code C
        # --- Nettoyage ---
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*

    # Copier uniquement le fichier requirements.txt pour installer les dépendances Python
    # Cette couche sera mise en cache si requirements.txt ne change pas.
    COPY --chown=appuser:appgroup requirements.txt requirements.txt

    # Installer les dépendances Python en tant que root (pour l'accès système si nécessaire)
    # puis passer à l'utilisateur non-root. Le cache pip peut être géré par des volumes
    # ou des outils de build plus avancés, mais ici on garde simple.
    RUN pip install --no-cache-dir --upgrade pip && \
        pip install --no-cache-dir -r requirements.txt

    # Créer les répertoires nécessaires AVANT de copier le reste du code
    # et s'assurer que appuser en est propriétaire. Fait avant USER appuser.
    RUN mkdir -p /app/embedding_cache /app/user_notes /app/generated_images /app/temp_uploads /app/chat_history && \
        chown -R appuser:appgroup /app /home/appuser

    # Passer à l'utilisateur non-root pour les étapes suivantes et l'exécution
    USER appuser

    # Copier le reste du code de l'application (app.py, etc.)
    # Cette couche sera invalidée si n'importe quel fichier du dossier local 'app' change.
    COPY --chown=appuser:appgroup . .

    # Exposer le port sur lequel Chainlit écoutera à l'intérieur du conteneur
    EXPOSE 8000

    # Définir la commande par défaut pour lancer l'application Chainlit
    # Utilise l'utilisateur non-root 'appuser'.
    # -w active le rechargement automatique (utile pour le développement)
    # --host 0.0.0.0 permet au serveur d'accepter des connexions externes AU CONTENEUR
    # (la sécurité est gérée par le binding 127.0.0.1 dans docker-compose.yml)
    CMD ["chainlit", "run", "app.py", "-w", "--host", "0.0.0.0", "--port", "8000"]

    # Note sur Multi-Stage Build (Optimisation Future Optionnelle):
    # Pour réduire encore la taille, on pourrait utiliser une étape de build séparée
    # pour installer les dépendances (notamment celles nécessitant build-essential)
    # et ne copier que les libs installées et le code source dans l'image finale
    # basée sur python:3.11-slim, sans build-essential.
    ```

* **Action :** Sauvegardez le fichier `Dockerfile`.

---

### Étape 3.3 : Créer le Fichier `.dockerignore` (Nouveau - Bonne Pratique)

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Créer un fichier `.dockerignore` pour indiquer à Docker quels fichiers/dossiers du répertoire `app` local **ne doivent pas** être envoyés au démon Docker lors de la construction de l'image (`COPY . .`). Cela accélère le build et réduit la taille du contexte envoyé.
* **Prérequis :** Être dans le dossier `app`.
* **Action :** Créez un nouveau fichier nommé `.dockerignore` (avec un point au début) dans le dossier `app/`.
* **Action :** Collez le contenu suivant :

    ```gitignore
    # Fichier: .dockerignore (Projet Khaldounia - assistant-core/app)
    # Rôle: Exclut des fichiers du contexte de build Docker

    # Exclure les fichiers Git
    .git
    .gitignore

    # Exclure les fichiers IDE spécifiques
    .vscode/
    *.code-workspace

    # Exclure les caches et fichiers compilés Python
    __pycache__/
    *.pyc
    *.pyo
    *.pyd
    *.egg-info/
    .pytest_cache/
    .mypy_cache/

    # Exclure les environnements virtuels locaux (si utilisés)
    .venv/
    venv/
    env/

    # Exclure les fichiers de logs locaux (si générés dans app/)
    *.log

    # Exclure les dossiers de données générées/persistantes dans l'app locale
    # (car elles sont gérées par des volumes Docker nommés dans le conteneur)
    # ATTENTION: NE PAS EXCLURE SI VOUS VOULEZ LES COPIER DANS L'IMAGE !
    # Ici on les exclut car ils sont montés via volumes ou créés par l'app.
    # embedding_cache/  # Exclu car géré par volume nommé
    # generated_images/ # Exclu car généré par l'app
    # temp_uploads/     # Exclu car temporaire
    # user_notes/       # Exclu car généré par l'app
    # chat_history/     # Exclu car géré par volume nommé

    # Autres fichiers spécifiques au projet ou à l'OS
    .DS_Store
    Thumbs.db
    ```

* **Action :** Sauvegardez le fichier `.dockerignore`.

---

### Étape 3.4 : Créer/Modifier le Fichier `requirements.txt` (Révisé v4.1 - Stable)

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Définir précisément les dépendances Python requises par `app.py`, avec des versions spécifiques pour assurer la stabilité et la reproductibilité. Cette version (identique à la v4.1 du plan original) inclut les bibliothèques nécessaires pour toutes les phases jusqu'à la 10.
* **Prérequis :** Être dans le dossier `app`.
* **Action :** Créez ou ouvrez le fichier `requirements.txt` dans le dossier `app/`.
* **Action :** Assurez-vous qu'il contient **exactement** le contenu suivant (vérifiez attentivement les versions) :

    ```txt
    # Fichier: requirements.txt (Révisé v4.1 - Stable pour Plan v5.0)

    # --- Core UI & Orchestration ---
    chainlit==1.1.300
    langchain==0.2.1
    langchain-community==0.2.5 # Version MAJ (était 0.1.1)
    langchain-ollama==0.1.0
    langchainhub==0.1.15
    langchain-experimental==0.0.60
    pydantic==2.7.1 # Figé pour compatibilité

    # --- RAG ---
    chromadb-client==0.5.0
    sentence-transformers==2.7.0
    pypdf==4.2.0
    unstructured[local-inference]==0.13.7 # Inclut dépendances locales

    # --- API & Utilitaires ---
    requests==2.31.0
    python-dotenv==1.0.1
    numpy==1.26.4
    soundfile==0.12.1 # Potentiellement requis par STT/TTS ou unstructured
    tenacity==8.2.3 # Pour les retries API robustes

    # --- Outils Agent ---
    duckduckgo-search==5.3.1b1

    # Note : Ces versions sont choisies pour leur compatibilité supposée début 2025.
    # La mise à jour de langchain-community est importante.
    # Toute mise à jour future doit être faite avec prudence (voir Annexe D).
    ```

* **Action :** Sauvegardez le fichier `requirements.txt`.

---

### Étape 3.5 : Créer/Modifier le Fichier `app.py` (Version Initiale Révisée v4.1 pour Phase 4)

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Créer le point d'entrée initial du code Python (`app.py`) pour le service `assistant_app`. Cette version est volontairement **simple** et ne contient que le strict nécessaire pour tester l'infrastructure de base (Ollama, XTTS, ChromaDB) en Phase 4. Elle inclut déjà les mécanismes de robustesse (imports, logging, config, fonctions `text_to_speech` / `transcribe_audio` durcies mais **désactivées/placeholders**, init Chroma/Embeddings, chaîne LLM très simple). L'agent et les autres outils seront ajoutés progressivement dans les phases suivantes.
* **Prérequis :** Être dans le dossier `app`.
* **Action :** Créez ou ouvrez le fichier `app.py` dans le dossier `app/`.
* **Action :** Collez **l'intégralité** du contenu suivant :

    ```python
    # Fichier: app.py (Version Initiale Révisée v4.1 - Base pour Phase 4)
    # Rôle: Structure minimale pour tester Ollama, XTTS, Chroma via Chainlit.
    # Inclut imports futurs, config, fonctions durcies (désactivées), init IA, chaîne LLM simple.

    import chainlit as cl
    import os
    import requests
    import base64
    from pathlib import Path
    import time
    import chromadb # Import du client ChromaDB
    import logging # Import standard logging

    # Imports pour Robustesse & Sécurité (seront utilisés dans les vraies fonctions)
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input # Pour prévenir injections

    # Configuration du Logging (simple pour le début)
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (minimum + prépare le futur)
    logging.info("--- Début Importation Langchain (Phase 3 Base) ---")
    from langchain_community.chat_models import ChatOllama
    from langchain_community.embeddings import SentenceTransformerEmbeddings
    from langchain_community.vectorstores import Chroma as ChromaLangchainStore
    from langchain_core.prompts import ChatPromptTemplate
    from langchain_core.output_parsers import StrOutputParser
    from langchain_core.runnables import RunnablePassthrough # Pas besoin de Lambda ici
    # Imports pour fonctionnalités futures (référence, non utilisés ici)
    # from langchain.agents import AgentExecutor, create_react_agent, Tool
    # from langchain_community.tools import DuckDuckGoSearchRun
    # from langchain import hub
    # ... etc ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale ---
    logging.info("--- Début Chargement Configuration ---")
    OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434")
    XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020") # Utilisé par text_to_speech
    CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb")
    CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000")
    # URLs pour Whisper, SD, etc. lues mais non utilisées dans cette version initiale
    WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000")
    STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860")
    # Modèle LLM par défaut
    OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest") # Assurez-vous qu'il est disponible
    # Constantes RAG (utilisées pour init)
    EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"
    EMBEDDING_CACHE_DIR = Path("/app/embedding_cache") # Assuré créé et accessible par appuser dans Dockerfile
    # Constantes pour dossiers (vérifiés/créés ci-dessous pour sécurité)
    SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history")
    # Création dossiers essentiels au cas où (normalement fait dans Dockerfile)
    SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True)
    logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}")
    logging.info(f"Cache Embeddings: {EMBEDDING_CACHE_DIR}")
    logging.info("--- Fin Chargement Configuration ---")

    # --- Fonctions Utilitaires (Seule TTS est activement utilisée initialement) ---

    # Fonction TTS (avec retry/timeout - nécessaire pour Phase 4)
    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        """Appelle l'API XTTS pour générer l'audio avec retry et timeout."""
        api_url = f"{XTTS_SERVER_URL}/generate"
        # Utilise le speaker par défaut défini dans docker-compose.yml command
        payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}
        logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'")
        try:
            response = requests.post(api_url, json=payload, timeout=180) # Timeout long pour TTS
            response.raise_for_status() # Lève une exception pour les erreurs HTTP (4xx, 5xx)
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
             raise # Relance l'exception pour que Tenacity la capture et réessaie
        except requests.exceptions.RequestException as e:
             logging.warning(f"[TTS] Erreur requête API ({e}). Tenacity va réessayer...")
             raise # Relance pour Tenacity
        except Exception as e:
             # Capture une erreur inattendue qui ne sera pas réessayée
             logging.error(f"[TTS] Erreur inattendue (non-retryable): {e}", exc_info=True)
             return None # Ne pas relancer pour éviter boucle infinie si erreur non liée à l'appel

    # --- Placeholders pour fonctions futures (activées dans les phases suivantes) ---
    def transcribe_audio(audio_bytes: bytes) -> str | None: logging.warning("[STT] Fonction transcribe_audio PAS ENCORE ACTIVE."); return "Fonctionnalité désactivée."
    def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] Fonction generate_image_tool PAS ENCORE ACTIVE."); return "Fonctionnalité désactivée."
    def _is_path_safe(requested_path: Path) -> bool: logging.warning("[NOTES] Fonction _is_path_safe PAS ENCORE ACTIVE."); return False
    def list_notes_safe() -> str: logging.warning("[NOTES] Fonction list_notes_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] Fonction save_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def read_note_safe(filename: str) -> str: logging.warning("[NOTES] Fonction read_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale IA (Embeddings, Client ChromaDB) ---
    # Fait une seule fois au démarrage de l'application
    logging.info("--- Début Initialisation Globale IA ---")
    ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}
    try:
        logging.info(f"[INIT] Chargement SentenceTransformer Embeddings ({EMBEDDING_MODEL_NAME})...")
        ia_components["embeddings"] = SentenceTransformerEmbeddings(
            model_name=EMBEDDING_MODEL_NAME,
            cache_folder=str(EMBEDDING_CACHE_DIR) # Chemin du cache
        )
        logging.info(f"[INIT] Embedding Model prêt (cache: {EMBEDDING_CACHE_DIR}).")

        logging.info(f"[INIT] Connexion au client ChromaDB à http://{CHROMADB_HOST}:{CHROMADB_PORT}...")
        # Note: Utiliser chromadb.HttpClient pour se connecter au serveur distant
        ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60)
        # Vérifier la connexion
        ia_components["chroma_client"].heartbeat()
        logging.info("[INIT] Client ChromaDB connecté et heartbeat OK.")

        logging.info(f"[INIT] Initialisation Langchain VectorStore pour collection '{CHROMA_COLLECTION_NAME}'...")
        # Ceci initialise l'objet Langchain qui INTERAGIT avec la collection ChromaDB.
        # La collection elle-même est gérée par le serveur ChromaDB.
        ia_components["vector_store_lc"] = ChromaLangchainStore(
            client=ia_components["chroma_client"],
            collection_name=CHROMA_COLLECTION_NAME,
            embedding_function=ia_components["embeddings"]
            # Note: La création de la collection est implicite ici si elle n'existe pas.
        )
        logging.info("[INIT] Langchain VectorStore prêt.")
        logging.info("--- Fin Initialisation Globale IA ---")

    except Exception as e:
        # Erreur critique si l'init échoue (connexion ChromaDB, chargement embeddings)
        logging.critical(f"[INIT - ERREUR FATALE] Initialisation globale Embeddings/Chroma échouée: {e}", exc_info=True)
        # Mettre les composants à None pour que les vérifications échouent proprement
        ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation pour chaque nouvelle session de chat (Phase 4 Test)."""
        logging.info("\n--- Nouvelle Session Chat (Phase 3/4 Base - Test Initial) ---")
        # Vérifier si l'initialisation globale a réussi
        if not ia_components.get("vector_store_lc"): # Vérifie le composant clé
            logging.error("[Session Start] Erreur critique: Composants IA (Embeddings/Chroma) non initialisés.")
            await cl.Message(content="Erreur critique: Composants IA (Embeddings/Chroma) non initialisés au démarrage du serveur. Vérifiez les logs serveur.").send()
            return # Ne pas continuer si l'init de base a échoué

        try:
            logging.info("[Session Start] Initialisation LLM Ollama...")
            llm = ChatOllama(
                model=OLLAMA_MODEL,
                base_url=OLLAMA_BASE_URL,
                temperature=0.7, # Température standard pour conversation
                request_timeout=120.0 # Timeout pour les appels LLM
            )
            # Tester la connexion LLM (optionnel mais utile)
            # try:
            #     await llm.ainvoke("Ping") # Test simple
            #     logging.info("[Session Start] Connexion LLM OK.")
            # except Exception as llm_err:
            #     logging.error(f"[Session Start] Échec connexion LLM Ollama: {llm_err}")
            #     await cl.Message(content="Erreur: Impossible de contacter le service LLM Ollama.").send()
            #     # On pourrait choisir de ne pas continuer ici, mais on laisse pour voir si ça se résout
            cl.user_session.set("llm", llm) # Stocke le LLM dans la session

            # Stocker le vector store pour utilisation future (Phase 9/10)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            logging.info("[Session Start] LLM et VectorStore prêts pour la session.")

            # Chaîne LangChain TRES simple (Input -> Prompt -> LLM -> Output String) pour test initial Phase 4
            logging.info("[Session Start] Création chaîne LLM simple...")
            # Note: Pas besoin de sanitization ici car pas d'agent, mais bonne pratique de l'ajouter tôt
            prompt = ChatPromptTemplate.from_template("Question: {user_input}\nRéponse:")
            simple_chain = prompt | llm | StrOutputParser()
            cl.user_session.set("chain", simple_chain)
            logging.info("[Session Start] Chaîne LLM simple prête.")

            logging.info("[Session Start] Configuration UI: Input Texte seulement pour Phase 4.")
            # Pour Phase 4, seul l'input texte est nécessaire
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="Entrez votre message...", initial="") ] ).send()

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant (Phase 3/4 - Test Initial) prêt.", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Initialisée et Prête ---")

        except Exception as e:
            # Erreur pendant le setup de la session
            error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"
            logging.critical(error_msg, exc_info=True)
            await cl.Message(content=f"Erreur interne irrécupérable lors du démarrage de la session: {error_msg}. Consultez les logs.", author="Erreur Système").send()

    @cl.on_message
    async def on_message(message: cl.Message):
        """Gère les messages texte entrants (utilise chaîne simple pour Phase 4)."""
        logging.info(f"\n--- Message Reçu (Texte) ---")
        logging.info(f"Input brut: '{message.content[:100]}...'")

        # Récupérer la chaîne simple de la session
        chain = cl.user_session.get("chain")
        if not chain:
            logging.error("[on_message] Erreur critique : Chaîne de traitement non trouvée dans la session.")
            await cl.Message(content="Erreur critique : Chaîne de traitement non initialisée pour cette session. Essayez de rafraîchir.").send()
            return

        # Créer le message de réponse vide pour le streaming
        msg = cl.Message(content="", author="Assistant Khaldounia"); await msg.send()
        final_answer = ""
        try:
            logging.info("[Run] Appel chaîne LLM simple (astream)...")
            # Passe l'input utilisateur directement à la chaîne simple
            # Pas besoin de sanitization pour ce test simple, mais sera ajouté plus tard
            async for chunk in chain.astream({"user_input": message.content}):
                await msg.stream_token(chunk)
                final_answer += chunk
            await msg.update() # Finalise le message texte
            logging.info(f"[Run] Réponse LLM reçue: '{final_answer[:100]}...'")

            # Générer l'audio pour la réponse texte
            logging.info("[Run] Tentative de génération audio XTTS...")
            try:
                # Appel de la fonction TTS (qui a retry/timeout/raise)
                audio_bytes = text_to_speech(final_answer)
                if audio_bytes:
                    # Envoyer l'audio dans un message séparé
                    await cl.Message(
                        content="",
                        elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)],
                        author="Audio Khaldounia" # Auteur différent pour distinguer
                    ).send()
                    logging.info("[Run] Audio envoyé.")
                else:
                    # Cas où TTS renvoie None après retries (erreur interne non liée à l'appel)
                    logging.warning("[Run] Echec génération audio interne (après retries si échec appel).")
                    await cl.Message(content="(Erreur interne lors de la génération audio)", parent_id=msg.id, author="Alerte Système", indent=1).send()

            # Capture l'erreur si text_to_speech échoue après tous les retries
            except Exception as tts_err:
                logging.error(f"[Run] Échec final appel TTS après retries: {tts_err}", exc_info=True)
                await cl.Message(content=f"(Erreur communication avec le service audio. Veuillez réessayer.)", parent_id=msg.id, author="Alerte Système", indent=1).send()

        except Exception as e:
            # Erreur générale pendant le traitement du message par la chaîne
            error_message = f"ERREUR lors du traitement LLM: {e}"
            logging.error(f"[Run] {error_message}", exc_info=True)
            # Mettre à jour le message initial avec une erreur générique
            await msg.update(content="Désolé, une erreur s'est produite lors de la génération de la réponse.")

        logging.info("--- Fin Traitement Message ---")

    # Les autres décorateurs @cl.on_audio_end, @cl.on_file_upload ne sont pas pertinents/implémentés ici
    # Ils seront ajoutés dans les phases suivantes.

    ```

* **Action :** Sauvegardez ce fichier `app.py` initial révisé.




Fin de la Phase 3 (Révisée v5.0).  




## Phase 4 : Lancement Initial & Tests (Base Révisée v5.0)

**Objectif :** Démarrer pour la première fois les **4 services Docker de base** (`ollama`, `xtts_server`, `chromadb`, `assistant_app`) en utilisant les configurations robustes et sécurisées des phases précédentes (versions figées, healthchecks, ports 127.0.0.1, volumes nommés). **Copier le fichier vocal de référence dans le volume XTTS.** Effectuer des tests simples pour valider que chaque service démarre sainement (`(healthy)`), communique, et que l'interface Chainlit de base est fonctionnelle.

**[⚠️⚠️⚠️] Importance :** Cette phase valide l'infrastructure minimale avant d'ajouter les fonctionnalités plus complexes (STT, Agent, etc.). Soyez patient lors du premier démarrage (téléchargements initiaux, initialisation des services, attente des healthchecks).

---

### Étape 4.1 : Se Placer dans le Dossier `assistant-core`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Les commandes `docker compose` doivent être exécutées depuis le dossier contenant le fichier `docker-compose.yml`.
* **Prérequis :** Être dans le terminal WSL/Ubuntu. Le dossier `~/projet-khaldounia/assistant-core` existe et contient `docker-compose.yml` (Phase 2) et le sous-dossier `app` (Phase 1 & 3).
* **Action :**
    ```bash
    cd ~/projet-khaldounia/assistant-core
    ```
* **Vérification Post-Exécution :**
    ```bash
    pwd
    ```
    * **Résultat Attendu :** Affiche `/home/VotreNomWSL/projet-khaldounia/assistant-core`.
* **Attente et Transition :** Confirmez que vous êtes dans le dossier `assistant-core`.

---

### Étape 4.2 : Construire l'Image App et Démarrer les Services

* **[⚠️⚠️⚠️] [⏱️⏱️⏱️]** (Très long la première fois !)
* **Contexte et Objectif :** Lancer tous les services définis dans `docker-compose.yml` (version révisée v5.0). L'option `--build` force la reconstruction de l'image `assistant_app` en utilisant le `Dockerfile` (v5.0) et le code `app.py` (v4.1 initial) de la Phase 3. Docker va également télécharger les images publiques spécifiées (`ollama`, `tts`, `chroma`) si elles ne sont pas déjà présentes localement.
* **Note sur le démarrage et les Healthchecks :** Grâce aux `healthchecks` et à `depends_on: condition: service_healthy` dans `docker-compose.yml`, le conteneur `assistant_app` ne démarrera **que lorsque** `ollama`, `xtts_server`, et `chromadb` seront non seulement lancés mais aussi considérés comme **sains ("healthy")** par Docker. Cela peut prendre plusieurs minutes, surtout pour `xtts_server` qui doit charger son modèle. C'est normal et garantit une meilleure stabilité.
* **Prérequis :** Connexion Internet stable et rapide recommandée pour les téléchargements initiaux. Assez d'espace disque.
* **COMMANDE IMPORTANTE :**
    ```bash
    docker compose up -d --build
    ```
    * `-d` : Mode détaché (le terminal reste utilisable).
    * `--build` : Force la reconstruction de l'image `assistant_app`.
* **🚨 ATTENTION - TEMPS D'ATTENTE & TÉLÉCHARGEMENTS IMPORTANTS (1ère fois) :**
    * **Soyez très patient !** Cette commande peut prendre **15 à 45 minutes ou plus** la première fois, en fonction de votre connexion et des performances de votre machine. Cela inclut :
        * Build de l'image `assistant_app` (installation dépendances système/Python).
        * Téléchargement des images Docker (`ollama`, `tts`, `chroma`).
        * Téléchargements internes aux conteneurs lors de leur premier démarrage (modèles Embedding, modèle TTS, potentiellement modèle Ollama si non pré-pullé).
    * **Surveillance (Recommandé) :** Utilisez un **second terminal WSL** pour suivre la progression et les logs des différents services, par exemple :
        ```bash
        # Dans un autre terminal WSL:
        docker logs -f assistant_app
        # docker logs -f xtts_server
        # docker logs -f ollama
        # docker logs -f chromadb
        ```
        *(Utilisez `Ctrl+C` pour arrêter de suivre les logs d'un conteneur).*
* **Attente et Transition :** Attendez que la commande `docker compose up -d --build` se termine dans le premier terminal (retour au prompt). Les conteneurs devraient être en cours de démarrage en arrière-plan. Ne passez pas à l'étape suivante avant la fin de cette commande.

---

### **ÉTAPE 4.2 bis : COPIER LE FICHIER VOCAL DE RÉFÉRENCE DANS LE VOLUME (CRUCIAL)**

* **[⚠️⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Puisque nous utilisons un volume Docker nommé (`khaldounia_xtts_speakers`) pour les voix XTTS (bonne pratique), nous devons **copier manuellement** le fichier `speaker_ref.wav` (préparé en Phase 1.6) depuis notre hôte WSL **dans ce volume**, via le conteneur `xtts_server` qui le monte. Cette étape est **indispensable** pour que XTTS puisse utiliser votre voix de référence.
* **Prérequis :** La commande `docker compose up -d --build` est terminée. Le conteneur `xtts_server` est démarré (il peut encore être en `(health: starting)`). Le fichier `speaker_ref.wav` existe bien dans `~/projet-khaldounia/tts-data/speakers/` sur votre hôte WSL.
* **Action :** Exécutez la commande suivante **depuis votre terminal WSL** (normalement depuis `~/projet-khaldounia/assistant-core`, mais le chemin source est absolu donc le dossier courant importe peu ici) :
    ```bash
    docker cp ~/projet-khaldounia/tts-data/speakers/speaker_ref.wav xtts_server:/app/speakers/speaker_ref.wav
    ```
    * `docker cp` : Commande pour copier des fichiers vers/depuis un conteneur.
    * `~/projet-khaldounia/tts-data/speakers/speaker_ref.wav` : Chemin source sur l'hôte WSL.
    * `xtts_server:/app/speakers/speaker_ref.wav` : Chemin destination -> `nom_conteneur`:`chemin_absolu_dans_conteneur`. Le dossier `/app/speakers` dans le conteneur correspond au montage du volume `khaldounia_xtts_speakers`.
* **Vérification Post-Exécution (Optionnelle mais recommandée) :**
    ```bash
    docker exec xtts_server ls -l /app/speakers
    ```
    * **Résultat Attendu :** Doit lister `speaker_ref.wav` avec sa taille et les permissions correctes.
* **Note :** Cette copie n'est à faire **qu'une seule fois**. Le fichier est maintenant persistant dans le volume Docker nommé `khaldounia_xtts_speakers`, même si vous arrêtez/redémarrez les conteneurs (sauf si vous supprimez le volume explicitement avec `docker volume rm` ou `docker compose down -v`).
* **Attente et Transition :** Confirmez que la commande `docker cp` s'est exécutée sans erreur.

---

### Étape 4.3 : Vérifier l'État des Conteneurs et leur Santé (`healthy`)

* **[⚠️⚠️] [⏱️⏱️]** (Attendre que les services deviennent `healthy`)
* **Contexte et Objectif :** Après le démarrage et la copie du fichier vocal, vérifier que les 4 conteneurs (`ollama`, `xtts_server`, `chromadb`, `assistant_app`) tournent correctement et que leur état de santé est passé à `(healthy)`. C'est la confirmation que les healthchecks réussissent.
* **Prérequis :** Étape 4.2 et 4.2bis terminées.
* **Action :** Exécutez la commande suivante (répétez-la toutes les 30 secondes si nécessaire) :
    ```bash
    docker ps
    ```
* **Résultat Attendu :**
    * Doit lister **4 conteneurs** : `ollama`, `xtts_server`, `chromadb`, `assistant_app`.
    * La colonne `STATUS` doit idéalement indiquer `Up X minutes (healthy)` pour **chacun** des 4 conteneurs.
    * Il est normal de voir `(health: starting)` pendant les premières minutes (surtout pour `xtts_server` et `assistant_app` qui attendent les autres). Patientez.
* **En Cas de Problème :** Si un conteneur a un statut `Exited`, `Restarting`, ou reste indéfiniment en `(unhealthy)` :
    1.  **Identifier le Conteneur Problématique.**
    2.  **Consulter ses Logs Détaillés :** `docker logs [nom_du_conteneur]` (sans `--tail` pour voir depuis le début si besoin). Cherchez les messages `ERROR`, `CRITICAL`, `FATAL`.
    3.  **Consulter les Détails du Healthcheck (si `unhealthy`) :**
        ```bash
        docker inspect --format='{{json .State.Health}}' [nom_du_conteneur]
        ```
        *(Regardez la sortie `Log` de la dernière vérification pour comprendre pourquoi elle échoue).*
    4.  **Erreurs Courantes Phase 4 (Révisée) :**
        * Manque VRAM (Ollama ou XTTS ne démarre pas / crash - Vérifiez `nvidia-smi` dans WSL). Solution : Commentez la section `deploy:` GPU de `xtts_server` dans `docker-compose.yml` et faites `docker compose up -d`.
        * Problème chargement modèle XTTS (VRAM ou fichier `speaker_ref.wav` non copié/invalide). Vérifiez logs `xtts_server`.
        * Échec connexion `assistant_app` à un autre service (ex: ChromaDB down). Vérifiez logs `assistant_app` (`[INIT - ERREUR FATALE]`). Vérifiez que le service dépendant est bien `(healthy)`.
        * Erreur dans le code initial `app.py`. Vérifiez logs `assistant_app`.
    5.  **Stratégie :** Utilisez l'Annexe A avec les logs et l'état de santé pour diagnostiquer avec l'IA guide. Ne passez pas à la suite tant que les 4 services ne sont pas `Up` et `(healthy)`.
* **Attente et Transition :** Confirmez que les 4 conteneurs sont bien `Up ... (healthy)`.

---

### Étape 4.3bis : Tests de Connectivité API Directs (`curl` depuis WSL)

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Vérifier rapidement si les APIs des services sont accessibles depuis WSL via l'adresse locale `127.0.0.1` et les ports hôtes définis dans `docker-compose.yml`.
* **Prérequis :** Les 4 conteneurs sont `Up (healthy)`.
* **Action :** Exécutez ces commandes `curl` dans votre terminal WSL :
    * **Test Ollama (API Generate - Basic Ping) :**
        ```bash
        curl -X POST [http://127.0.0.1:11434/api/generate](http://127.0.0.1:11434/api/generate) -d '{"model":"mistral:latest","prompt":"ping","stream":false}' --connect-timeout 10 -fsS # -f fail fast, -s silent, -S show error
        ```
        * **Résultat Attendu :** Réponse JSON contenant une clé `"response"`. Le premier appel peut être un peu lent si le modèle doit être chargé en mémoire par Ollama. Ne doit PAS afficher d'erreur `curl`.
        * *(Si erreur : Conteneur `ollama` non sain ? Port 11434 inaccessible ? Modèle `mistral:latest` non présent ? -> `docker exec ollama ollama pull mistral:latest`)*
    * **Test ChromaDB (API Heartbeat) :**
        ```bash
        curl [http://127.0.0.1:8002/api/v1/heartbeat](http://127.0.0.1:8002/api/v1/heartbeat) --connect-timeout 5 -fsS
        ```
        * **Résultat Attendu :** Réponse JSON `{"nanosecond heartbeat":...}` sans erreur `curl`.
        * *(Si erreur : Conteneur `chromadb` non sain ? Port 8002 inaccessible ?)*
    * **Test XTTS (API Speakers - Vérifie serveur) :**
        ```bash
        curl [http://127.0.0.1:8050/speakers](http://127.0.0.1:8050/speakers) --connect-timeout 10 -fsS
        ```
        * **Résultat Attendu :** Réponse JSON (peut être `[]` ou lister `speaker_ref.wav`). Le principal est de recevoir une réponse JSON valide (code 200 OK) sans erreur `curl`.
        * *(Si erreur : Conteneur `xtts_server` non sain ? Oubli/erreur copie `speaker_ref.wav` (Étape 4.2bis) ? Problème chargement modèle TTS (VRAM ?) ?)*
* **En Cas de Problème `curl` :** Si une commande `curl` échoue (affiche une erreur `curl: ...`), le service correspondant a un problème. Revérifiez `docker ps` (état de santé) et analysez les logs `docker logs [nom_du_conteneur]`.
* **Attente et Transition :** Confirmez que les 3 tests `curl` réussissent (pas de message d'erreur `curl`).

---

### Étape 4.4 : Tester l'Accès à l'Interface Web Chainlit

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Vérifier que l'interface web de l'application assistante est accessible depuis votre navigateur Windows.
* **Prérequis :** Le conteneur `assistant_app` est `Up (healthy)`.
* **Action :**
    1.  Ouvrez votre navigateur web sur Windows (Chrome, Firefox, Edge...).
    2.  Allez à l'URL : `http://127.0.0.1:8001` (Notez bien le port `8001` qui est le port hôte mappé).
* **Résultat Attendu :** L'interface Chainlit doit se charger et afficher le message d'accueil : *"Bonjour Halim-IA ! Assistant (Phase 3/4 - Test Initial) prêt."*.
* **En Cas de Problème :**
    * **Page inaccessible (`ERR_CONNECTION_REFUSED`, "Ce site est inaccessible"...) :**
        * Le conteneur `assistant_app` est-il bien `Up (healthy)` ? (`docker ps`)
        * Y a-t-il une erreur dans les logs `docker logs assistant_app` (ex: erreur Python au démarrage, échec connexion Chroma/Ollama lors de `@cl.on_chat_start`) ?
        * Un autre programme sur votre PC utilise-t-il déjà le port 8001 ? (Peu probable mais possible).
    * **Page blanche, chargement infini, erreur interne affichée dans l'UI :**
        * Problème probable dans le code `app.py`, spécifiquement dans la fonction `@cl.on_chat_start`.
        * Vérifiez les logs `docker logs assistant_app` pour des erreurs Python (`Traceback...`).
* **Attente et Transition :** Confirmez que l'interface Chainlit se charge correctement.

---

### Étape 4.5 : Tester l'Interaction de Base (LLM + TTS)

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Effectuer un test de bout en bout simple : Input Texte -> `app.py` -> Chaîne LLM Simple -> Ollama -> Réponse Texte -> `app.py` -> XTTS -> Réponse Audio. Valide la communication entre les services principaux.
* **Prérequis :** Interface Chainlit chargée (Étape 4.4). Conteneurs `ollama` et `xtts_server` sont `(healthy)`. `speaker_ref.wav` a été copié (Étape 4.2bis).
* **Action :**
    1.  Dans l'interface Chainlit (`http://127.0.0.1:8001`), utilisez le champ de saisie "Entrez votre message...".
    2.  Tapez une question simple, par exemple : `Bonjour Khaldounia !` ou `Raconte-moi une blague courte.`
    3.  Appuyez sur Entrée ou cliquez sur le bouton d'envoi.
* **Résultat Attendu :**
    1.  L'assistant affiche une réponse **texte** générée par Ollama.
    2.  Presque immédiatement après, un lecteur audio apparaît et joue la réponse **audio** générée par XTTS (avec la voix de votre fichier `speaker_ref.wav`). L'autoplay est activé.
* **En Cas de Problème :**
    * **Pas de Réponse Texte / Erreur affichée à la place du texte :**
        * Vérifiez logs `assistant_app` : Erreur dans `@cl.on_message` ? Échec de l'appel à la chaîne LLM (`chain.astream`) ? Erreur retournée par Ollama ?
        * Vérifiez logs `ollama` : Est-il toujours `(healthy)` ? Y a-t-il des erreurs de chargement de modèle ou des erreurs GPU ?
        * Revérifiez le test `curl` Ollama (Étape 4.3bis).
    * **Réponse Texte OK, mais Pas de Son / Erreur Audio affichée :**
        * Vérifiez logs `assistant_app` : Erreur dans l'appel `text_to_speech` ? Message `[ERREUR] Échec final appel TTS...` ? Message `(Erreur interne génération audio)` ou `(Erreur communication service audio)` dans Chainlit ?
        * Vérifiez logs `xtts_server` : Est-il toujours `(healthy)` ? Voit-il bien le fichier `/app/speakers/speaker_ref.wav` (via `docker exec xtts_server ls -l /app/speakers`) ? Y a-t-il une erreur de traitement TTS ? Manque de VRAM (si GPU activé pour XTTS) ?
        * Revérifiez le test `curl` XTTS (Étape 4.3bis).
        * Assurez-vous que l'Étape 4.2bis (copie `speaker_ref.wav`) a été faite correctement.
    * **Très Lent :** C'est normal pour le tout premier appel (chargement des modèles en mémoire). Si la lenteur persiste pour les appels suivants, vérifiez l'utilisation des ressources (`nvidia-smi` dans WSL pour la VRAM, `docker stats` pour CPU/RAM des conteneurs).
* **Attente et Transition :** Confirmez que vous obtenez bien une réponse texte ET audio pour une question simple.

---

### **Étape 4.6: Sauvegarde de Fin de Phase (Optionnel mais Fortement Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** L'infrastructure de base est fonctionnelle et testée. C'est un excellent point pour créer une sauvegarde avant d'ajouter la reconnaissance vocale en Phase 5.
* **Prérequis :** Phase 4 terminée avec succès.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple du Code/Config (Facile)**
        1.  Arrêt des services : `docker compose down` (dans `assistant-core`)
        2.  Copie du dossier projet : `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase4` (dans `~`)
        3.  Redémarrage : `docker compose up -d` (dans `assistant-core`)
        *(Note : Ceci ne sauvegarde PAS les données dans les volumes Docker nommés comme les modèles Ollama téléchargés ou la base ChromaDB).*
    * **Méthode 2 : Commit Git (Préférable pour Code/Config)**
        1.  Assurez-vous d'avoir initialisé Git (Phase 0.5) et d'avoir un `.gitignore` (Phase 3.3).
        2.  Placez-vous dans `~/projet-khaldounia`.
        3.  Ajoutez tous les changements : `git add .`
        4.  Commitez : `git commit -m "Fin de la Phase 4 (v5.0) - Base LLM/TTS/RAG fonctionnelle (tests OK)"`
        *(Idéal pour suivre les modifications du code et de la configuration).*
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la Phase 5.


Fin de la Phase 4 (Révisée v5.0). 
L'assistant peut maintenant parler et écouter (via texte).



## Phase 5 : Ajout Reconnaissance Vocale (STT/Whisper - Révisée v5.0)

**Objectif :** Intégrer la reconnaissance vocale (Speech-to-Text) pour permettre à l'utilisateur de parler à l'assistant. Nous ajoutons un service Docker dédié pour Whisper (API web), mettons à jour la configuration Docker Compose, et modifions `app.py` pour activer l'entrée audio dans Chainlit et appeler l'API Whisper.

**[⚠️⚠️] Importance :** Ajoute une modalité d'interaction clé. Vérifiez la configuration VRAM si vous activez le GPU pour Whisper.

**Prérequis :** Phase 4 terminée et idéalement sauvegardée. Les 4 services de base sont fonctionnels (`(healthy)`). Le fichier vocal de référence a été copié dans le volume XTTS.

**Approche :**
1.  Ajout du service `whisper_server` dans `docker-compose.yml` (avec image figée, healthcheck, port local).
2.  Mise à jour de `assistant_app` dans `docker-compose.yml` (ajout variable d'env `WHISPER_SERVER_URL` et dépendance saine).
3.  Mise à jour de `app.py` :
    * Activation de l'entrée audio (`cl.AudioInput`) dans `@cl.on_chat_start`.
    * Implémentation de la fonction `transcribe_audio` (avec retry/timeout) pour appeler l'API Whisper.
    * Implémentation du gestionnaire `@cl.on_audio_end` pour traiter l'audio enregistré.
    * La logique de réponse (`process_and_respond`) reste celle de la Phase 4 (chaîne LLM simple).

---

### Étape 5.1 : Mettre à jour le Fichier `docker-compose.yml` (Ajout Whisper)

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Ajouter le 5ème service (`whisper_server`) à notre orchestration Docker et s'assurer que `assistant_app` en dépend et peut le contacter.
* **Prérequis :** Être dans le dossier `~/projet-khaldounia/assistant-core`. Le fichier `docker-compose.yml` existe.
* **Action :** Ouvrez `~/projet-khaldounia/assistant-core/docker-compose.yml` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous, qui ajoute le service `whisper_server` et met à jour `assistant_app`. Lisez les commentaires VRAM.

    ```yaml
    # Fichier: docker-compose.yml (Révisé v5.0 - Ajout Whisper - Phase 5)

    version: '3.8'

    services:
      # --- Ollama (Inchangé depuis Phase 2) ---
      ollama:
        image: ollama/ollama:0.1.41 # FIGÉE
        container_name: ollama
        command: sh -c "ollama serve"
        deploy: { resources: { reservations: { devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] } } }
        volumes: [ khaldounia_ollama_data:/root/.ollama ]
        ports: [ "127.0.0.1:11434:11434" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:11434/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

      # --- XTTS (Inchangé depuis Phase 2 - Attention VRAM) ---
      xtts_server:
        image: ghcr.io/coqui-ai/tts:v0.22.0 # FIGÉE
        container_name: xtts_server
        command: > tts-server --model_name tts_models/multilingual/multi-dataset/xtts_v2 --use_cuda true --port 8020 --speaker_wav /app/speakers/speaker_ref.wav
        ports: [ "127.0.0.1:8050:8020" ]
        volumes: [ khaldounia_xtts_cache:/root/.local/share/tts, khaldounia_xtts_speakers:/app/speakers ]
        environment: [ NVIDIA_VISIBLE_DEVICES=all, NVIDIA_DRIVER_CAPABILITIES=compute,utility, COQUI_TOS_AGREED=1 ]
        # [!!! GESTION VRAM CRITIQUE - XTTS !!!]
        # Commentez la section 'deploy:' ci-dessous si VRAM limitée (< 20-24Go) ou erreurs OOM.
        deploy: # À COMMENTER SI VRAM LIMITÉE
           resources:
             reservations:
               devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        shm_size: '8gb'
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"], interval: 30s, timeout: 10s, retries: 3, start_period: 180s }

      # --- ChromaDB (Inchangé depuis Phase 2) ---
      chromadb:
        image: chromadb/chroma:0.5.0 # FIGÉE
        container_name: chromadb
        environment: [ IS_PERSISTENT=TRUE, ALLOW_RESET=TRUE ]
        volumes: [ khaldounia_chroma_data:/chroma ]
        ports: [ "127.0.0.1:8002:8000" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"], interval: 30s, timeout: 10s, retries: 3, start_period: 30s }

      # --- Whisper (STT) --- NOUVEAU SERVICE ---
      whisper_server:
        # Utilise une image webservice pour Whisper (exemple: celle de Optimum ou ASR Webservice)
        # Note: Vérifiez la documentation de l'image choisie pour les variables d'env exactes.
        image: ghcr.io/distributors/whisper-asr-webservice:1.1.0 # <== VERSION FIGÉE (Exemple - Stable)
        container_name: whisper_server
        environment:
          # Variables communes pour ce type d'image (adaptez si nécessaire)
          - ASR_MODEL=small # Modèle Whisper (tiny, base, small, medium, large). 'small' est un bon compromis CPU/VRAM/Qualité.
          - ASR_ENGINE=openai_whisper # Moteur à utiliser (peut varier selon l'image)
          # - ASR_TASK=transcribe # Tâche par défaut (transcribe vs translate)
          # - ASR_LANGUAGE=fr # Optionnel: Forcer la langue si nécessaire
          # [!!! GESTION VRAM - Whisper !!!]
          # Le modèle 'small' utilise ~1-2Go VRAM. 'medium'/'large' > 5-10Go.
          # Si VRAM limitée, NE PAS activer le GPU ici (laissez la section 'deploy:' commentée).
          # Si VRAM suffisante (>24Go) et que vous voulez accélérer Whisper (surtout pour 'medium'+):
          # Décommentez 'deploy:' et 'NVIDIA_VISIBLE_DEVICES'.
          # - NVIDIA_VISIBLE_DEVICES=all # Décommentez pour GPU
        ports:
          # API Whisper, restreint à l'accès local
          - "127.0.0.1:9000:9000" # Port interne standard pour ces images est souvent 9000
        # deploy: # À DÉCOMMENTER (avec env NVIDIA) SI ASSEZ DE VRAM POUR GPU WHISPER
        #    resources:
        #      reservations:
        #        devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        networks:
          - assistant_network
        restart: unless-stopped
        healthcheck:
          # Teste l'endpoint /health ou / (peut varier selon l'image)
          test: ["CMD", "curl", "-f", "http://localhost:9000/health"] # Endpoint standard
          interval: 30s
          timeout: 10s
          retries: 3
          start_period: 90s # Laisse 1.5 min pour charger le modèle Whisper ('small' est rapide)

      # --- Assistant App (Mis à jour) ---
      assistant_app:
        build: { context: ./app }
        container_name: assistant_app
        ports: [ "127.0.0.1:8001:8000" ]
        volumes: [ ./app:/app, embedding_cache:/app/embedding_cache, khaldounia_chat_history:/app/chat_history ]
        environment:
          - OLLAMA_HOST=http://ollama:11434
          - XTTS_SERVER_URL=http://xtts_server:8020
          - CHROMADB_HOST=chromadb
          - CHROMADB_PORT=8000
          - WHISPER_SERVER_URL=http://whisper_server:9000 # <<< VARIABLE AJOUTÉE <<<
          # - STABLE_DIFFUSION_API_URL=http://stable_diffusion_webui:7860 # Pour Phase 7
        depends_on: # <<< MIS A JOUR - Attend TOUTES les dépendances saines <<<
          ollama: { condition: service_healthy }
          xtts_server: { condition: service_healthy }
          chromadb: { condition: service_healthy }
          whisper_server: { condition: service_healthy } # <<< DÉPENDANCE AJOUTÉE <<<
          # - stable_diffusion_webui: { condition: service_healthy } # Pour Phase 7
        stdin_open: true
        tty: true
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/"], interval: 30s, timeout: 10s, retries: 5, start_period: 90s }

    # --- Réseau Docker (Inchangé) ---
    networks:
      assistant_network: { driver: bridge, name: assistant_network }

    # --- Volumes Docker (Inchangés pour cette phase) ---
    volumes:
      khaldounia_ollama_data: { driver: local }
      khaldounia_chroma_data: { driver: local }
      embedding_cache: { driver: local }
      khaldounia_chat_history: { driver: local }
      khaldounia_xtts_cache: { driver: local }
      khaldounia_xtts_speakers: { driver: local }
      # Volumes SD commentés

    ```

* **Action :** Sauvegardez le fichier `docker-compose.yml`.
* **Attente et Transition :** Fichier d'orchestration mis à jour.

---

### Étape 5.2 : Vérifier le Fichier `requirements.txt`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Confirmer qu'aucune nouvelle dépendance Python n'est nécessaire pour cette phase. L'appel à l'API Whisper se fera via `requests`, qui est déjà inclus et figé.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`.
* **Action :** Jetez un œil rapide au fichier `requirements.txt` (Phase 3.4). Il doit être identique à la version stable précédente. Aucune modification n'est nécessaire ici.
* **Attente et Transition :** Dépendances Python confirmées.

---

### Étape 5.3 : Mettre à jour le Fichier `app.py` (Intégration STT Durcie)

* **[⚠️⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Modifier le code Python pour :
    1.  Activer l'input audio dans l'interface Chainlit (`@cl.on_chat_start`).
    2.  Implémenter la fonction `transcribe_audio` pour appeler l'API Whisper (en utilisant `WHISPER_SERVER_URL` et les mécanismes de robustesse).
    3.  Implémenter le handler `@cl.on_audio_end` qui recevra l'audio enregistré, appellera `transcribe_audio`, puis passera le texte transcrit (après sanitization) à la fonction `process_and_respond`.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version initiale v4.1 de Phase 3.5) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous :

    ```python
    # Fichier: app.py (Révisé v5.0 pour Phase 5 - Intégration STT)
    # Rôle: Application Khaldounia (Base + STT actif, logique réponse simple)

    import chainlit as cl
    import os, requests, base64, time, logging
    from pathlib import Path
    import chromadb

    # Imports Robustesse & Sécurité
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input

    # Configuration Logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (Base)
    logging.info("--- Début Importation Langchain (Phase 5) ---")
    from langchain_community.chat_models import ChatOllama
    from langchain_community.embeddings import SentenceTransformerEmbeddings
    from langchain_community.vectorstores import Chroma as ChromaLangchainStore
    from langchain_core.prompts import ChatPromptTemplate
    from langchain_core.output_parsers import StrOutputParser
    from langchain_core.runnables import RunnablePassthrough
    # ... (Imports futurs non utilisés ici) ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale (Lecture WHISPER_SERVER_URL) ---
    logging.info("--- Début Chargement Configuration ---")
    OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434")
    XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020")
    CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb")
    CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000")
    WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000") # <<< LU ICI <<<
    STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860")
    OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest")
    EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"
    EMBEDDING_CACHE_DIR = Path("/app/embedding_cache")
    SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history")
    SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True)
    logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}")
    logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (STT et TTS actives) ---

    # --- Fonction STT (Activée pour Phase 5 - avec retry/timeout) ---
    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None:
        """Appelle l'API Whisper pour transcrire l'audio avec retry/timeout."""
        # Adaptez l'URL et les paramètres si vous utilisez une autre image Whisper
        api_url = f"{WHISPER_SERVER_URL}/asr"
        params = {
            "encode": "true", # Demande à l'API de retourner le résultat en JSON
            "task": "transcribe",
            "language": "fr", # Spécifie la langue pour potentiellement améliorer la précision
            "output": "json"
        }
        # Prépare les données du fichier pour la requête POST multipart/form-data
        files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}
        logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)")
        try:
            # Timeout plus long pour la transcription si nécessaire (ex: 120s)
            response = requests.post(api_url, params=params, files=files, timeout=120)
            response.raise_for_status() # Vérifie erreurs HTTP (4xx, 5xx)
            result = response.json()
            # Extrait le texte transcrit (le champ peut varier selon l'API, souvent 'text')
            transcribed_text = result.get("text", "").strip()
            logging.info(f"[STT] Whisper a retourné: '{transcribed_text[:60]}...'")
            # Retourne None si le texte est vide pour indiquer qu'aucun mot n'a été détecté
            return transcribed_text if transcribed_text else None
        except requests.exceptions.Timeout as e:
            logging.warning(f"[STT] Timeout lors de l'appel API Whisper ({e}). Tenacity va réessayer...")
            raise
        except requests.exceptions.RequestException as e:
            logging.warning(f"[STT] Erreur requête API Whisper ({e}). Tenacity va réessayer...")
            raise
        except Exception as e:
            # Erreur inattendue (ex: parsing JSON, erreur interne Whisper non gérée par HTTP status)
            logging.error(f"[STT] Erreur inattendue (non-retryable): {e}", exc_info=True)
            return None
    # --- FIN Fonction STT ---

    # --- Fonction TTS (Identique Phase 4 - avec retry/timeout) ---
    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        # ... (coller ici la fonction text_to_speech complète de Phase 3.5) ...
        api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None

    # --- Placeholders pour fonctions futures ---
    # ... (coller ici les placeholders generate_image_tool, notes, RAG de Phase 3.5) ...
    def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] Fonction generate_image_tool PAS ENCORE ACTIVE."); return "Fonctionnalité désactivée."
    def _is_path_safe(requested_path: Path) -> bool: logging.warning("[NOTES] Fonction _is_path_safe PAS ENCORE ACTIVE."); return False
    def list_notes_safe() -> str: logging.warning("[NOTES] Fonction list_notes_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] Fonction save_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def read_note_safe(filename: str) -> str: logging.warning("[NOTES] Fonction read_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale (Identique Phase 4) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc de Phase 3.5) ...
    logging.info("--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat (Phase 5 - Active STT)."""
        logging.info("\n--- Nouvelle Session Chat (Phase 5 v5.0 - STT Actif) ---")
        if not ia_components.get("vector_store_lc"):
             logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
             await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

        try:
            # Initialiser LLM
            llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.7, request_timeout=120.0 )
            cl.user_session.set("llm", llm)
            # Stocker vector store pour RAG futur
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            logging.info("[Session Start] LLM et VectorStore prêts pour session.")

            # Chaîne simple (identique Phase 4)
            prompt = ChatPromptTemplate.from_template("Question: {user_input}\nRéponse:")
            simple_chain = prompt | llm | StrOutputParser()
            cl.user_session.set("chain", simple_chain)
            logging.info("[Session Start] Chaîne LLM simple prête.")

            # --- MODIFICATION PHASE 5 : Activer input audio ---
            logging.info("[Session Start] Configuration UI: Inputs Texte ET Audio.")
            await cl.ChatSettings(
                inputs=[
                    cl.TextInput(id="text_input", label="Entrez votre message...", initial=""),
                    # Ajoute le widget d'enregistrement audio
                    cl.AudioInput(
                        id="audio_input",
                        label="Cliquez pour parler...", # Texte du bouton
                        # tooltip="Enregistrez votre question ici" # Optionnel
                        )
                ]
            ).send()
            # --- FIN MODIFICATION PHASE 5 ---

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.0 - STT). Parlez ou écrivez.", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Initialisée et Prête (avec STT) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {error_msg}").send()


    # --- Fonction centrale de traitement (utilise chaîne simple pour l'instant) ---
    async def process_and_respond(input_content: str):
        """Traite texte (original ou transcrit) via chaîne LLM simple, répond texte+audio."""
        # NOTE: Input devrait être sanitizé ici en pratique, mais on attend la Phase 6/Agent
        logging.info(f"\n--- Traitement Input via Chaîne Simple ---")
        logging.info(f"Input: '{input_content[:60]}...'")
        chain = cl.user_session.get("chain")
        if not chain: logging.error("[Process] Chaîne simple non prête."); await cl.Message(content="Erreur: Chaîne non prête.").send(); return

        msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content); await msg.send()
        final_answer = ""
        try:
            logging.info("[Run] Appel Chaîne LLM simple (astream)...")
            async for chunk in chain.astream({"user_input": input_content}):
                await msg.stream_token(chunk); final_answer += chunk
            await msg.update(); logging.info(f"[Run] Réponse LLM reçue: '{final_answer[:100]}...'")

            # TTS (identique Phase 4)
            logging.info("[Run] Génération audio XTTS...");
            try:
                audio_bytes = text_to_speech(final_answer)
                if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia").send(); logging.info("[Run] Audio envoyé.")
                else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send()
            except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send()

        except Exception as e: error_message = f"ERREUR process/respond: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne lors génération réponse.")
        logging.info("--- Fin Traitement Input ---")


    # --- Gestionnaire Message Texte (appelle process_and_respond) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        logging.info(f"\n--- Msg Texte UI Reçu ---")
        # NOTE: Ajout sanitization ici, même si pas d'agent encore
        try:
            sanitized_input = sanitize_input(message.content)
            logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'")
            await process_and_respond(sanitized_input) # Appelle la logique centrale
        except Exception as e:
            logging.error(f"Erreur lors de la sanitization/traitement du message texte: {e}", exc_info=True)
            await cl.Message(content="Erreur interne lors du traitement de votre message.").send()


    # --- AJOUT PHASE 5 : Gestion INPUT AUDIO ---
    @cl.on_audio_chunk # Stream audio - pas utilisé ici pour Whisper API
    async def on_audio_chunk(chunk: cl.AudioChunk): pass # Ne rien faire avec les chunks

    @cl.on_audio_end # Appelée quand l'enregistrement audio est terminé par l'utilisateur
    async def on_audio_end(audio: cl.Audio):
        """Traite l'audio complet enregistré via l'interface Chainlit."""
        logging.info(f"\n--- Fichier Audio Reçu ---")
        logging.info(f"Nom: {audio.name}, Taille: {len(audio.content)} bytes, Type: {audio.mime}")

        # Afficher un message pendant la transcription
        transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True);
        await transcript_msg.send()

        transcribed_text_sanitized = None
        try:
            # 1. Appeler la fonction de transcription (API Whisper) - qui a retry/timeout
            logging.info("[Audio End] Appel de transcribe_audio...")
            transcribed_text_raw = transcribe_audio(audio.content)

            if transcribed_text_raw is not None and transcribed_text_raw != "":
                # 2. Sanitize le texte transcrit avant de l'utiliser
                transcribed_text_sanitized = sanitize_input(transcribed_text_raw)
                logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'")
                # Mettre à jour le message "Transcription en cours..." avec le résultat
                await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\"")
                # 3. Envoyer le texte transcrit ET sanitizé à la fonction de traitement principale
                await process_and_respond(transcribed_text_sanitized)
            else:
                # Cas où la transcription réussit mais retourne None ou vide
                logging.warning("[STT] Transcription retournée vide ou None (aucun mot détecté?).")
                await transcript_msg.update(content="Je n'ai pas pu transcrire l'audio ou rien n'a été dit. Veuillez réessayer.")

        except Exception as e: # Erreur finale après retries de transcribe_audio ou erreur sanitization
            logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True)
            await transcript_msg.update(content="Désolé, la transcription a échoué après plusieurs tentatives. Veuillez réessayer ou taper votre message.")

        logging.info("--- Fin Traitement Audio ---")
    # --- FIN AJOUT PHASE 5 ---

    # --- Section RAG Upload (Phase 9) --- Sera implémenté plus tard ---
    # @cl.on_file_upload(...)
    # --- Fin Section RAG Upload ---

    ```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 5.4 : Redémarrer l'Environnement avec le Service Whisper

* **[⚠️⚠️⚠️] [⏱️⏱️⏱️]** (Téléchargement image Whisper la 1ère fois)
* **Contexte et Objectif :** Arrêter l'environnement Docker actuel, reconstruire l'image `assistant_app` (car `app.py` a changé), et démarrer tous les services, y compris le nouveau `whisper_server`.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`.
* **Action :**
    1.  Arrêter les services (si actifs) :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` et démarrer tous les services (maintenant 5) :
        ```bash
        docker compose up -d --build
        ```
* **Attente :** Soyez patient. Docker va télécharger l'image Whisper (`ghcr.io/distributors/whisper-asr-webservice:1.1.0` ou autre) si nécessaire. Le démarrage peut prendre un peu de temps, notamment à cause des `depends_on: service_healthy`. Surveillez `docker ps` jusqu'à ce que les 5 services soient `Up (healthy)`.

---

### Étape 5.5 : Tester la Reconnaissance Vocale

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Vérifier que l'entrée audio fonctionne, que l'API Whisper transcrit correctement, et que la réponse LLM/TTS est générée à partir du texte transcrit.
* **Prérequis :** Les 5 conteneurs (`ollama`, `xtts_server`, `chromadb`, `whisper_server`, `assistant_app`) sont `Up (healthy)`.
* **Action :**
    1.  Ouvrez/Actualisez l'interface Chainlit : `http://127.0.0.1:8001`.
    2.  **Trouvez l'icône de microphone** à côté du champ de saisie de texte.
    3.  Cliquez sur l'icône, votre navigateur vous demandera probablement l'**autorisation d'utiliser le microphone**. **Autorisez-le.**
    4.  Parlez clairement dans votre microphone (ex: "Quelle est la capitale de la France ?").
    5.  Cliquez à nouveau sur l'icône (qui est peut-être devenue un carré ou autre indicateur d'enregistrement) pour **arrêter l'enregistrement**.
* **Résultat Attendu :**
    1.  Un message système apparaît : "*Transcription audio en cours...*".
    2.  Ce message est remplacé par : "Vous avez dit : "[Votre texte transcrit et sanitizé]"".
    3.  L'assistant traite ce texte via la chaîne simple (Ollama) et répond avec un message **texte**.
    4.  Un lecteur audio apparaît et joue la réponse **audio** (XTTS).
* **En Cas de Problème :**
    * **Pas d'icône micro / Input audio non activé dans Chainlit :**
        * Avez-vous bien mis à jour `app.py` avec `cl.AudioInput` dans `@cl.on_chat_start` ?
        * Avez-vous bien redémarré avec `docker compose up -d --build` ?
    * **Enregistrement échoue / Pas d'autorisation micro demandée :**
        * Problème de navigateur ou de permissions du microphone au niveau de Windows. Vérifiez les paramètres de confidentialité micro de Windows et du navigateur pour le site `http://127.0.0.1:8001`.
    * **Message "Transcription échouée" / Reste bloqué sur "Transcription..." :**
        1.  Vérifiez les logs `assistant_app` : Erreur (`[ERREUR]`) dans `@cl.on_audio_end` ou `transcribe_audio` ? Timeout après retries ? `[STT/Audio End] Échec final...` ?
        2.  Vérifiez le conteneur `whisper_server` : Est-il `Up (healthy)` (`docker ps`) ?
        3.  Vérifiez les logs `whisper_server` (`docker logs whisper_server`) : Erreur interne ? Erreur chargement modèle ? Problème VRAM (si GPU activé) ?
        4.  Testez l'API Whisper directement (si possible, selon l'image) : `curl http://127.0.0.1:9000/health` doit répondre OK.
    * **Transcription OK, mais pas de réponse LLM/TTS ensuite :** Le problème se situe dans `process_and_respond` ou les services LLM/TTS. Référez-vous au dépannage de la Phase 4.5.
* **Attente et Transition :** Confirmez que vous pouvez enregistrer, obtenir une transcription correcte, et recevoir une réponse texte + audio.

---

### **Étape 5.6: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** La reconnaissance vocale est intégrée et fonctionnelle sur la base robuste. Bon moment pour sauvegarder avant d'ajouter l'Agent et la recherche web.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple**
        * `docker compose down`
        * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase5`
        * `docker compose up -d`
    * **Méthode 2 : Commit Git**
        * `git add .`
        * `git commit -m "Fin de la Phase 5 (v5.0) - STT Whisper fonctionnel (base robuste)"`
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la Phase 6.

Fin de la Phase 5 (Révisée v5.0).
L'assistant peut maintenant écouter et parler !



## Phase 6 : Ajout de la Recherche Web (Agent - Révisée v5.0)

**Objectif :** Transformer notre assistant simple en un **Agent intelligent** capable d'utiliser des outils. Dans cette phase, nous mettons en place une architecture d'Agent LangChain (type ReAct) et nous activons son **premier outil : la recherche web** (via DuckDuckGo), lui permettant de trouver des informations à jour sur Internet.

**[⚠️⚠️] Importance :** C'est un changement majeur dans l'architecture de l'application. L'assistant ne suit plus une chaîne simple mais "réfléchit" (via le LLM) pour décider s'il doit utiliser un outil.

**Prérequis :** Phase 5 terminée et idéalement sauvegardée. STT/Whisper est fonctionnel sur la base robuste.

**Approche :**
1.  Vérification des dépendances Python (`requirements.txt` doit déjà contenir `langchain`, `langchainhub`, `duckduckgo-search`).
2.  Modification majeure de `app.py` :
    * Remplacement de la chaîne simple par un `AgentExecutor` dans `@cl.on_chat_start`.
    * Configuration de cet agent avec **uniquement** l'outil `search_tool` actif pour l'instant.
    * Adaptation de la logique de réponse (`process_and_respond`) pour utiliser l'agent.

---

### Étape 6.1 : Vérifier le Fichier `requirements.txt`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** S'assurer que les bibliothèques Python nécessaires pour les agents LangChain et l'outil DuckDuckGo sont bien présentes avec les versions figées définies en Phase 3 (Révisée v4.1 / v5.0).
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`.
* **Action :** Vérifiez le contenu de `requirements.txt`. Il doit contenir les lignes :
    ```txt
    # ... autres lignes ...
    langchain==0.2.1
    langchain-community==0.2.5
    langchainhub==0.1.15
    langchain-experimental==0.0.60
    # ... autres lignes ...
    duckduckgo-search==5.3.1b1
    # ... autres lignes ...
    ```
    * *(Normalement, aucune modification n'est requise par rapport à la version stable de Phase 3/5).*
* **Attente et Transition :** Confirmez que les dépendances nécessaires sont listées.

---

### Étape 6.2 : Mettre à jour le Fichier `app.py` (Intégration Agent et Outil WebSearch)

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Modification substantielle du code)
* **Contexte et Objectif :** C'est le cœur de cette phase. Nous remplaçons la logique de chaîne simple par un Agent Executor (ReAct) dans `@cl.on_chat_start`. Cet agent est configuré pour n'avoir accès qu'à l'outil de recherche web (`search_tool`). La fonction `process_and_respond` est adaptée pour invoquer cet agent.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version Phase 5 v5.0) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous, qui introduit l'Agent :

    ```python
    # Fichier: app.py (Révisé v5.0 pour Phase 6 - Agent + WebSearch)
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
    logging.info("--- Début Importation Langchain (Phase 6 Rev 5.0) ---")
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
    # ... (Imports futurs non utilisés ici) ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale (inchangée depuis Phase 5) ---
    # ... (coller ici la config globale complète de Phase 5) ...
    logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (STT/TTS inchangées, placeholders pour autres outils) ---

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None:
        # ... (coller ici la fonction transcribe_audio complète de Phase 5) ...
        api_url = f"{WHISPER_SERVER_URL}/asr"; params = {"encode": "true","task": "transcribe","language": "fr","output": "json"}; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)"); try: response = requests.post(api_url, params=params, files=files, timeout=120); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:60]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue STT: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        # ... (coller ici la fonction text_to_speech complète de Phase 5) ...
        api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None

    # --- Placeholders pour les autres outils (seront implémentés plus tard) ---
    def generate_image_tool(prompt: str) -> str: logging.warning("[IMG] Fonction generate_image_tool PAS ENCORE ACTIVE."); return "Fonctionnalité désactivée."
    def _is_path_safe(requested_path: Path) -> bool: logging.warning("[NOTES] Fonction _is_path_safe PAS ENCORE ACTIVE."); return False
    def list_notes_safe() -> str: logging.warning("[NOTES] Fonction list_notes_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] Fonction save_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def read_note_safe(filename: str) -> str: logging.warning("[NOTES] Fonction read_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale (Identique Phase 5) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc de Phase 5) ...
    logging.info("--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat -> Mise en place de l'Agent avec WebSearch."""
        logging.info("\n--- Nouvelle Session Chat (Phase 6 v5.0 - Agent WebSearch Actif) ---")
        if not ia_components.get("vector_store_lc"): # Vérif simple si init IA globale OK
             logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
             await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

        try:
            # Initialiser LLM (Température souvent plus basse pour ReAct pour être plus factuel)
            logging.info("[Session Start] Initialisation LLM pour Agent...")
            llm = ChatOllama(
                model=OLLAMA_MODEL,
                base_url=OLLAMA_BASE_URL,
                temperature=0.2, # Plus bas pour agent ReAct
                request_timeout=300.0 # Timeout plus long pour appels LLM complexes
            )

            # --- AGENT SETUP (Phase 6) ---
            logging.info("[Session Start] Configuration de l'Agent ReAct...")
            # 1. Définir TOUS les outils potentiels (pour référence et phases futures)
            #    On utilise les placeholders définis plus haut pour les fonctions non actives.
            search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
            image_tool = Tool( name="Generateur Image", func=generate_image_tool, description="Créé une image." )
            list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="Liste les notes." )
            save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="Sauve une note." )
            read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="Lit une note." )
            rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs." )

            # 2. Sélectionner les outils ACTIFS pour CETTE phase (WebSearch SEULEMENT)
            active_tools = [search_tool] # <<<=== SEUL OUTIL ACTIF ICI
            logging.info(f"[Session Start] Outils actifs pour l'agent: {[tool.name for tool in active_tools]}")

            # 3. Charger le Prompt ReAct standard depuis Langchain Hub
            #    Ce prompt contient les instructions pour que le LLM utilise le format Thought/Action/Observation
            try:
                # Utilise un prompt ReAct éprouvé
                react_prompt = hub.pull("hwchase17/react")
                logging.info("[Session Start] Prompt ReAct chargé depuis Langchain Hub.")
            except Exception as hub_error:
                logging.critical(f"[Session Start] Erreur chargement prompt Hub: {hub_error}", exc_info=True)
                await cl.Message(content=f"Erreur critique: Impossible de charger le prompt de l'agent. Le Hub Langchain est-il accessible ?").send(); return

            # 4. Créer l'agent ReAct
            #    Lie le LLM, les outils actifs, et le prompt ensemble
            try:
                agent = create_react_agent(llm, active_tools, react_prompt)
                logging.info("[Session Start] Agent ReAct créé.")
            except Exception as agent_create_error:
                logging.critical(f"[Session Start] Erreur création agent: {agent_create_error}", exc_info=True)
                await cl.Message(content=f"Erreur critique: Impossible de créer l'agent.").send(); return

            # 5. Créer l'AgentExecutor
            #    C'est le runtime qui va faire tourner l'agent (boucle Thought/Action/Observation)
            agent_executor = AgentExecutor(
                agent=agent,
                tools=active_tools, # Fournit la liste des outils que l'agent PEUT utiliser
                verbose=True, # ESSENTIEL pour voir la "pensée" de l'agent dans les logs Docker !
                handle_parsing_errors=True, # Tente de corriger les erreurs de formatage du LLM (ex: si l'Action n'est pas bien formée)
                max_iterations=10, # Sécurité pour éviter les boucles infinies si l'agent se perd
                # max_execution_time=300.0, # Optionnel: Limite de temps totale pour une requête agent
            )
            # Stocker l'EXECUTOR dans la session utilisateur Chainlit
            cl.user_session.set("agent_executor", agent_executor)
            logging.info("[Session Start] Agent Executor (WebSearch seul) initialisé et stocké.")
            # --- FIN AGENT SETUP ---

            # Stocker vector store (pour upload RAG Phase 9)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            logging.info("[Session Start] VectorStore stocké pour session.")

            # Activer Inputs (Texte/Audio) & Upload Fichiers (inchangé depuis Phase 5)
            logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
            # Activation upload fichiers (sera utilisé en Phase 9)
            cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.0 - Agent avec Recherche Web).", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Initialisée et Prête (Agent WebSearch) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur irrécupérable démarrage session: {error_msg}").send()


    # --- MODIFICATION Phase 6 : process_and_respond utilise l'Agent Executor ---
    async def process_and_respond(input_content_sanitized: str):
        """Traite l'entrée utilisateur (sanitizée) via l'Agent Executor et renvoie la réponse (texte+audio)."""
        logging.info(f"\n--- Traitement Input via Agent Executor ---")
        logging.info(f"Input (Sanitized): '{input_content_sanitized[:60]}...'")
        agent_executor = cl.user_session.get("agent_executor")
        if not agent_executor:
            logging.error("[Process] Erreur critique: Agent Executor non trouvé dans la session.")
            await cl.Message(content="Erreur critique : L'agent intelligent n'est pas prêt pour cette session.").send(); return

        # Créer message Chainlit vide pour réponse streamée/finale
        # Ajouter le prompt original sanitizé pour contexte dans l'UI
        msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send()
        final_answer_text = ""; image_to_display = None # Pour gérer sortie image future

        try:
            logging.info("[Run] Invocation de l'Agent Executor (ainvoke)...")
            # Invoque l'agent avec l'input sanitizé.
            # L'agent va utiliser le LLM pour décider s'il doit appeler un outil (ici, DuckDuckGo)
            # ou répondre directement. Le prompt ReAct standard attend la clé "input".
            # La réponse contient la sortie finale de l'agent dans la clé "output".
            response = await agent_executor.ainvoke(
                {"input": input_content_sanitized},
                # Optionnel: Ajouter callbacks pour voir étapes intermédiaires dans Chainlit UI
                # config={"callbacks": [cl.LangchainCallbackHandler(stream_final_answer=True)]}
                # Note: stream_final_answer=True peut être complexe avec la logique TTS/Image séparée.
                # On garde simple pour l'instant: réponse finale texte + audio séparé.
            )
            final_answer_text = response.get("output", "Désolé, je n'ai pas pu formuler de réponse structurée.")
            logging.info(f"[Run] Réponse brute de l'Agent Executor: '{final_answer_text[:100]}...'")

            # --- Gestion Spécifique pour l'Outil Image (préparation Phase 7) ---
            # Si l'outil image (quand il sera actif) retourne un chemin spécial
            if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"):
                try:
                    relative_image_path = final_answer_text.split(":", 1)[1]
                    # Construit le chemin complet dans le conteneur
                    image_path_in_container = Path("/app") / relative_image_path.strip()
                    if image_path_in_container.is_file():
                        # Crée l'élément Image pour Chainlit
                        image_to_display = cl.Image(
                            path=str(image_path_in_container),
                            name=os.path.basename(relative_image_path),
                            display="inline" # Affiche l'image dans le flux de messages
                        )
                        # Modifie la réponse texte pour accompagner l'image
                        final_answer_text = f"Voici l'image demandée concernant '{input_content_sanitized[:30]}...'."
                        logging.info(f"[Run] Outil Image a retourné un fichier valide: {relative_image_path}")
                    else:
                        logging.error(f"[Run] Chemin image retourné par outil invalide ou fichier non trouvé: {image_path_in_container}")
                        # Garde la réponse texte originale de l'agent mais ajoute une erreur
                        final_answer_text += f" (Erreur: le fichier image {relative_image_path} est introuvable après génération)"
                except Exception as img_err:
                    logging.error(f"[Run] Erreur lors du traitement du retour de l'Outil Image: {img_err}", exc_info=True)
                    # Garde la réponse texte originale mais ajoute une erreur
                    final_answer_text += " (Une erreur s'est produite lors de la tentative d'affichage de l'image générée.)"
            # --- Fin Gestion Image ---

            # Met à jour le message Chainlit avec la réponse finale texte de l'agent
            await msg.update(content=final_answer_text)
            logging.info(f"[Run] Réponse finale texte de l'Agent affichée: '{final_answer_text[:100]}...'")

            # Afficher l'image si elle a été générée et préparée
            # (Ne devrait pas arriver en Phase 6, mais la logique est prête)
            if image_to_display:
                await cl.Message(content="", elements=[image_to_display], author="Image Générée", parent_id=msg.id, indent=1).send()

            # Génération TTS de la réponse finale texte (inchangé)
            logging.info("[Run] Tentative de génération audio XTTS...");
            try:
                audio_bytes = text_to_speech(final_answer_text)
                if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia", parent_id=msg.id, indent=1).send(); logging.info("[Run] Audio envoyé.")
                else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send()
            except Exception as tts_err: # Erreur après retries
                logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send()

        except Exception as e:
            # Capture les erreurs pendant l'exécution de l'agent (ex: LLM indisponible, outil échoue, parsing impossible...)
            error_message = f"ERREUR lors de l'exécution de l'agent: {e}"
            logging.error(f"[Run] {error_message}", exc_info=True)
            # Met à jour le message Chainlit avec une erreur générique
            await msg.update(content="Désolé, une erreur interne s'est produite pendant ma réflexion. Vérifiez les logs pour plus de détails.")

        logging.info("--- Fin Traitement Input via Agent ---")
    # --- FIN NOUVEAU process_and_respond ---


    # --- Gestionnaire Message Texte (appelle process_and_respond via Agent) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        logging.info(f"\n--- Msg Texte UI Reçu ---")
        logging.info(f"Input brut: '{message.content[:60]}...'")
        try:
            # Sanitize toujours l'input avant de le passer à l'agent
            sanitized_input = sanitize_input(message.content)
            logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'")
            # Appelle la NOUVELLE logique centrale basée sur l'Agent Executor
            await process_and_respond(sanitized_input)
        except Exception as e:
            logging.error(f"Erreur avant/pendant appel process_and_respond pour texte: {e}", exc_info=True)
            await cl.Message(content="Erreur interne lors du traitement de votre message texte.").send()


    # --- Gestionnaire Fin Audio (appelle process_and_respond via Agent) ---
    @cl.on_audio_end
    async def on_audio_end(audio: cl.Audio):
        logging.info(f"\n--- Fichier Audio Reçu ---")
        logging.info(f"Taille: {len(audio.content)} bytes, Type: {audio.mime}")
        transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True); await transcript_msg.send()
        transcribed_text_sanitized = None
        try:
            # 1. Transcription (inchangé)
            transcribed_text_raw = transcribe_audio(audio.content)
            if transcribed_text_raw is not None and transcribed_text_raw != "":
                # 2. Sanitization (inchangé)
                transcribed_text_sanitized = sanitize_input(transcribed_text_raw)
                logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'")
                await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\"")
                # 3. Appel à la NOUVELLE logique centrale basée sur l'Agent
                await process_and_respond(transcribed_text_sanitized)
            else:
                logging.warning("[STT] Transcription retournée vide ou None."); await transcript_msg.update(content="Transcription vide ou aucun son détecté.")
        except Exception as e: logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription/traitement après tentatives.")
        logging.info("--- Fin Traitement Audio ---")


    # --- Section RAG Upload (Inchangée - Logique Phase 9) --- Sera implémenté plus tard ---
    # @cl.on_file_upload(...)
    # --- Fin Section RAG Upload ---

    ```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 6.3 : Redémarrer l'Environnement avec l'Agent

* **[⚠️⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Arrêter et redémarrer l'environnement Docker pour que le service `assistant_app` utilise le nouveau code `app.py` qui inclut l'Agent Executor (avec uniquement l'outil WebSearch actif pour l'instant).
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`.
* **Action :**
    1.  Arrêter les services :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` (car `app.py` a changé) et démarrer tous les services (toujours 5 services) :
        ```bash
        docker compose up -d --build
        ```
* **Attente :** Attendez que les 5 conteneurs soient `Up (healthy)` (`docker ps`).

---

### Étape 6.4 : Tester la Recherche Web via l'Agent

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Vérifier que l'agent utilise correctement l'outil de recherche web lorsque nécessaire.
* **Prérequis :** Les 5 conteneurs sont `Up (healthy)`. Interface Chainlit accessible.
* **Action :**
    1.  Ouvrez/Actualisez Chainlit : `http://127.0.0.1:8001`.
    2.  **Ouvrez un terminal séparé pour suivre les logs de l'agent :**
        ```bash
        # Dans un autre terminal WSL:
        docker logs -f assistant_app
        ```
    3.  **Test 1 (Web Nécessaire) :** Dans Chainlit, demandez une information nécessitant une recherche web (texte ou voix). Utilisez une question qui n'a pas de réponse évidente ou statique.
        * Exemple : "Quelle est la météo actuelle à Épinay-sur-Seine ?" (Utilise la localisation fournie précédemment).
        * Ou : "Qui a gagné le dernier match de l'équipe de France de football ?"
    4.  **Observez les logs `assistant_app` :** Vous devriez voir la "pensée" de l'agent (car `verbose=True`). Cherchez les lignes :
        * `> Entering new AgentExecutor chain...`
        * `Thought: L'utilisateur demande la météo actuelle [...], je dois utiliser un outil de recherche web.` (La pensée exacte dépendra du LLM)
        * `Action: Recherche Web DuckDuckGo`
        * `Action Input: météo actuelle Épinay-sur-Seine` (ou similaire)
        * `Observation: [Résultat de la recherche DuckDuckGo]`
        * `Thought: J'ai le résultat de la recherche, je peux maintenant formuler la réponse finale.`
        * `Final Answer: [La réponse météo formulée par l'agent]`
        * `> Finished chain.`
    5.  **Vérifiez la réponse dans Chainlit :** Elle doit contenir l'information à jour demandée (texte + audio).
    6.  **Test 2 (Web Non Nécessaire) :** Demandez une salutation simple comme "Bonjour" ou "Comment ça va ?".
    7.  **Observez les logs `assistant_app` :** L'agent devrait répondre directement :
        * `> Entering new AgentExecutor chain...`
        * `Thought: L'utilisateur dit bonjour, je dois répondre poliment.` (ou similaire)
        * `Final Answer: Bonjour ! Comment puis-je vous aider aujourd'hui ?` (ou similaire)
        * `> Finished chain.`
        * **Important :** Il ne doit **pas** y avoir d'étape `Action: Recherche Web DuckDuckGo`.
    8.  **Vérifiez la réponse dans Chainlit :** Simple salutation (texte + audio).
* **En Cas de Problème :**
    * **L'agent n'utilise jamais l'outil WebSearch :**
        * Vérifiez les logs (`verbose=True`). Le LLM pense-t-il qu'il peut répondre sans ? La description de l'outil `DuckDuckGoSearchRun` (implicite de Langchain) est-elle comprise ? Essayez une question encore plus spécifique nécessitant le web.
    * **L'agent essaie d'utiliser l'outil mais échoue :**
        * Logs `assistant_app` : Erreur lors de l'appel à DuckDuckGo ? Problème de connectivité réseau depuis le conteneur `assistant_app` ?
    * **Erreur de Parsing (`Could not parse LLM output: ...`) :**
        * Le LLM n'a pas formaté sa réponse (Action/Action Input/Final Answer) correctement. `handle_parsing_errors=True` tente de corriger, mais peut échouer. Cela dépend de la fiabilité du LLM (Mistral 7B peut parfois faire des erreurs). Réessayer la requête peut fonctionner.
    * **Réponse incorrecte ou incohérente :** Qualité du LLM ou des résultats de recherche.
    * **Stratégie :** Analysez les logs `verbose=True` de l'agent pour comprendre sa décision. Utilisez Annexe A.
* **Attente et Transition :** Confirmez que l'agent utilise la recherche web quand c'est pertinent et répond directement sinon.

---

### **Étape 6.5: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** L'Agent avec recherche web est fonctionnel. Sauvegarde avant d'ajouter la génération d'images.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple**
        * `docker compose down`
        * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase6`
        * `docker compose up -d`
    * **Méthode 2 : Commit Git**
        * `git add .`
        * `git commit -m "Fin de la Phase 6 (v5.0) - Agent ReAct avec WebSearch fonctionnel"`
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la Phase 7.



Fin de la Phase 6 (Révisée v5.0). 
L'assistant est maintenant un agent capable de chercher sur le web !



## Phase 7 : Ajout Génération Image (Stable Diffusion - Révisée v5.1 - Limites Ressources & Gestion Modèle Clarifiée)

**Objectif :** Intégrer un service de génération d'images basé sur Stable Diffusion (via l'image `linuxserver/stable-diffusion-webui` populaire, qui utilise l'interface A1111) et activer l'outil correspondant dans l'agent pour permettre la création d'images à partir de descriptions textuelles (prompts). Cette version inclut des **limites de ressources CPU/Mémoire** pour les conteneurs.

**[⚠️⚠️⚠️] Importance :** Stable Diffusion est **extrêmement gourmand en VRAM GPU**. La réussite de cette phase dépendra fortement des ressources disponibles et de la gestion de la VRAM (voir notes dans `docker-compose.yml`). L'ajout manuel du modèle de checkpoint (Étape 7.6) est **obligatoire**. Les limites CPU/Mémoire ajoutées sont des **exemples à adapter** à votre matériel.

**Prérequis :** Phase 6 terminée et idéalement sauvegardée. L'Agent avec WebSearch est fonctionnel sur la base robuste.

**Approche :**
1.  Mise à jour de `docker-compose.yml` pour ajouter le 6ème service, `stable_diffusion_webui` (avec image figée, volumes nommés dédiés, healthcheck, port local, arguments CLI optimisés, **alertes VRAM critiques**, et **limites CPU/Mémoire**).
2.  Mise à jour de `assistant_app` dans `docker-compose.yml` (ajout variable d'env `STABLE_DIFFUSION_API_URL` et dépendance saine).
3.  Mise à jour de `app.py` :
    * Implémentation de la fonction `generate_image_tool` (avec retry/timeout/sauvegarde) pour appeler l'API Stable Diffusion.
    * Ajout de l'`image_tool` à la liste `active_tools` de l'agent dans `@cl.on_chat_start`.
4.  **Clarification majeure** de l'étape cruciale pour copier le modèle `.safetensors` et le sélectionner dans l'UI Web.

---

### Étape 7.1 : Mettre à jour le Fichier `docker-compose.yml` (Ajout SD WebUI + Limites Ressources)

* **[⚠️⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Utiliser la version finale de `docker-compose.yml` qui inclut maintenant le service Stable Diffusion WebUI ainsi que des **limites de ressources CPU/Mémoire** pour tous les services afin d'améliorer la stabilité globale. **Lisez attentivement les commentaires sur la VRAM et l'adaptation des limites.**
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`. Le fichier `docker-compose.yml` existe (il sera écrasé).
* **Action :** Ouvrez `~/projet-khaldounia/assistant-core/docker-compose.yml` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version **finale v5.1** ci-dessous :

    ```yaml
    # Fichier: docker-compose.yml (Révisé v5.1 - Ajout Limites Ressources - FINAL)
    # Base Khaldounia: Ollama, XTTS, ChromaDB, Whisper, SD, App
    # Intègre: Versions figées, Volumes nommés, Ports 127.0.0.1, Healthchecks, Depends_on healthy,
    #          Avertissements VRAM, ET Limites CPU/Mémoire (À AJUSTER !)

    version: '3.8'

    services:
      # --- Service LLM (Ollama) ---
      ollama:
        image: ollama/ollama:0.1.41 # FIGÉE
        container_name: ollama
        command: sh -c "ollama serve"
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs à votre système !
            limits:
              cpus: '4.0'  # Exemple: Limite à 4 coeurs CPU
              memory: 16G # Exemple: Limite à 16Go de RAM (Ollama peut être gourmand)
            reservations: # Garde les réservations GPU
              devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        volumes: [ khaldounia_ollama_data:/root/.ollama ]
        ports: [ "127.0.0.1:11434:11434" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:11434/"], interval: 30s, timeout: 10s, retries: 3, start_period: 60s }

      # --- Service Synthèse Vocale (XTTS) ---
      xtts_server:
        image: ghcr.io/coqui-ai/tts:v0.22.0 # FIGÉE
        container_name: xtts_server
        command: > tts-server --model_name tts_models/multilingual/multi-dataset/xtts_v2 --use_cuda true --port 8020 --speaker_wav /app/speakers/speaker_ref.wav
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs !
            limits:
              cpus: '2.0'  # Exemple
              memory: 8G  # Exemple (peut nécessiter plus si GPU désactivé)
            reservations:
               # [!!! GESTION VRAM CRITIQUE - XTTS vs SD !!!]
               # Si VRAM limitée (< 20-24Go), commentez la section 'devices:' ci-dessous.
               devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] # À COMMENTER SI VRAM LIMITÉE
          # Garder les réservations GPU même si limites CPU/RAM ajoutées
          # Laisser la partie 'reservations.devices' ici, commenter si VRAM insuffisante
        volumes: [ khaldounia_xtts_cache:/root/.local/share/tts, khaldounia_xtts_speakers:/app/speakers ]
        ports: [ "127.0.0.1:8050:8020" ]
        environment: [ NVIDIA_VISIBLE_DEVICES=all, NVIDIA_DRIVER_CAPABILITIES=compute,utility, COQUI_TOS_AGREED=1 ]
        shm_size: '8gb'
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8020/speakers"], interval: 30s, timeout: 10s, retries: 3, start_period: 180s }

      # --- Service Base de Données Vectorielle (ChromaDB) ---
      chromadb:
        image: chromadb/chroma:0.5.0 # FIGÉE
        container_name: chromadb
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs !
            limits:
              cpus: '1.0'  # Exemple (généralement peu gourmand)
              memory: 4G  # Exemple
        environment: [ IS_PERSISTENT=TRUE, ALLOW_RESET=TRUE ]
        volumes: [ khaldounia_chroma_data:/chroma ]
        ports: [ "127.0.0.1:8002:8000" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"], interval: 30s, timeout: 10s, retries: 3, start_period: 30s }

      # --- Service Reconnaissance Vocale (Whisper) ---
      whisper_server:
        image: ghcr.io/distributors/whisper-asr-webservice:1.1.0 # FIGÉE
        container_name: whisper_server
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs !
            limits:
              cpus: '2.0' # Exemple
              memory: 6G # Exemple (peut varier selon modèle ASR utilisé)
            reservations:
              # [!!! GESTION VRAM - Whisper vs SD !!!]
              # Si VRAM limitée, laissez 'devices:' commenté.
              # Si VRAM OK (>24Go total?), décommentez pour utiliser GPU pour Whisper.
              # devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ] # À DÉCOMMENTER SI ASSEZ DE VRAM
              devices: [] # Laisser vide par défaut pour économiser VRAM pour SD
        environment:
          - ASR_MODEL=small
          - ASR_ENGINE=openai_whisper
          # - NVIDIA_VISIBLE_DEVICES=all # À décommenter si GPU activé dans 'deploy' ci-dessus
        ports: [ "127.0.0.1:9000:9000" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:9000/health"], interval: 30s, timeout: 10s, retries: 3, start_period: 90s }

      # --- Service Génération Image (Stable Diffusion WebUI) ---
      stable_diffusion_webui:
        image: lscr.io/linuxserver/stable-diffusion-webui:amd64-1.9.3 # FIGÉE
        container_name: stable_diffusion_webui
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs !
            limits:
              cpus: '4.0' # Exemple (peut utiliser CPU pour certaines tâches)
              memory: 8G # Exemple (l'UI et l'API ont besoin de RAM)
            reservations: # GPU INDISPENSABLE pour SD
              devices: [ { driver: nvidia, count: 1, capabilities: [gpu] } ]
        environment:
          # [IMPORTANT] Adaptez PUID/PGID à votre utilisateur WSL ('id -u' et 'id -g')
          - PUID=1000
          - PGID=1000
          - TZ=Europe/Paris # Adaptez
          - NVIDIA_VISIBLE_DEVICES=all
          - CLI_ARGS=--api --listen --port 7860 --xformers --enable-insecure-extension-access --no-half-vae
        volumes:
          - khaldounia_sd_config:/config
          - khaldounia_sd_models:/config/stable-diffusion
          - khaldounia_sd_outputs:/output
        ports: [ "127.0.0.1:7860:7860" ]
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:7860/docs"], interval: 60s, timeout: 30s, retries: 5, start_period: 300s }

      # --- Service Application Assistant (LangChain + Chainlit) ---
      assistant_app:
        build: { context: ./app }
        container_name: assistant_app
        deploy:
          resources:
            # [!! IMPORTANT - LIMITES !!] Adaptez ces valeurs !
            limits:
              cpus: '2.0' # Exemple
              memory: 4G # Exemple
        ports: [ "127.0.0.1:8001:8000" ]
        volumes: [ ./app:/app, embedding_cache:/app/embedding_cache, khaldounia_chat_history:/app/chat_history ]
        environment:
          - OLLAMA_HOST=http://ollama:11434
          - XTTS_SERVER_URL=http://xtts_server:8020
          - CHROMADB_HOST=chromadb
          - CHROMADB_PORT=8000
          - WHISPER_SERVER_URL=http://whisper_server:9000
          - STABLE_DIFFUSION_API_URL=http://stable_diffusion_webui:7860 # <<< VARIABLE AJOUTÉE <<<
        depends_on: # <<< MIS A JOUR - Attend TOUTES les dépendances saines <<<
          ollama: { condition: service_healthy }
          xtts_server: { condition: service_healthy }
          chromadb: { condition: service_healthy }
          whisper_server: { condition: service_healthy }
          stable_diffusion_webui: { condition: service_healthy } # <<< DÉPENDANCE AJOUTÉE <<<
        stdin_open: true
        tty: true
        networks: [ assistant_network ]
        restart: unless-stopped
        healthcheck: { test: ["CMD", "curl", "-f", "http://localhost:8000/"], interval: 30s, timeout: 10s, retries: 5, start_period: 90s }

    # --- Réseau Docker (Inchangé) ---
    networks:
      assistant_network: { driver: bridge, name: assistant_network }

    # --- Volumes Docker (Ajout SD) ---
    volumes:
      khaldounia_ollama_data: { driver: local }
      khaldounia_chroma_data: { driver: local }
      embedding_cache: { driver: local }
      khaldounia_chat_history: { driver: local }
      khaldounia_xtts_cache: { driver: local }
      khaldounia_xtts_speakers: { driver: local }
      # Nouveaux volumes pour Stable Diffusion
      khaldounia_sd_config: { driver: local } # Configuration UI, extensions
      khaldounia_sd_models: { driver: local } # Checkpoints, VAE, LoRA...
      khaldounia_sd_outputs: { driver: local } # Images générées

    ```

* **Action :** Sauvegardez ce fichier `docker-compose.yml` mis à jour.
* **Rappel Important :** Les valeurs `limits:` (cpus, memory) sont des exemples. **Adaptez-les à votre système !** Surveillez `docker stats` pour ajuster si nécessaire.
* **Attente et Transition :** Fichier d'orchestration mis à jour pour inclure Stable Diffusion et les limites de ressources.

---

### Étape 7.2 : Vérifier le Fichier `requirements.txt`

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Confirmer qu'aucune nouvelle dépendance Python n'est nécessaire pour appeler l'API SD, qui utilise `requests` (déjà présent).
* **Action :** Vérifiez `requirements.txt` dans `assistant-core/app`. Aucune modification n'est nécessaire par rapport à la version stable précédente.
* **Attente et Transition :** Dépendances confirmées.

---

### Étape 7.3 : Mettre à jour le Fichier `app.py` (Activation Outil Image Gen)

* **[⚠️⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Implémenter la fonction `generate_image_tool` (avec robustesse) et ajouter l'`image_tool` à la liste `active_tools` de l'agent dans `@cl.on_chat_start`.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version Phase 6 v5.0) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous (elle est identique à celle fournie dans la Phase 7 précédente, car l'ajout des limites ne concerne que `docker-compose.yml`) :

    ```python
    # Fichier: app.py (Révisé v5.0 pour Phase 7 - Activation ImgGen Tool)
    # Rôle: Application Khaldounia (Agent + WebSearch + ImgGen - Robuste)

    import chainlit as cl
    import os, requests, base64, time, logging
    from pathlib import Path
    import chromadb
    import json # Pour le payload SD

    # Imports Robustesse & Sécurité
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input

    # Configuration Logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (Agent + Tools)
    logging.info("--- Début Importation Langchain (Phase 7 Rev 5.0) ---")
    from langchain_community.chat_models import ChatOllama
    from langchain_community.embeddings import SentenceTransformerEmbeddings
    from langchain_community.vectorstores import Chroma as ChromaLangchainStore
    from langchain.agents import AgentExecutor, create_react_agent, Tool
    from langchain_community.tools import DuckDuckGoSearchRun
    from langchain import hub
    # ... (Autres imports core) ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale (Lecture STABLE_DIFFUSION_API_URL) ---
    # ... (coller ici la config globale complète de Phase 6) ...
    logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (STT/TTS + ImgGen active) ---

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None:
        # ... (coller ici transcribe_audio complet de Phase 6) ...
        api_url = f"{WHISPER_SERVER_URL}/asr"; params = {"encode": "true","task": "transcribe","language": "fr","output": "json"}; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)"); try: response = requests.post(api_url, params=params, files=files, timeout=120); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:60]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue STT: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        # ... (coller ici text_to_speech complet de Phase 6) ...
        api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None

    # --- Fonction Outil Image Gen (Implémentée pour Phase 7 - avec retry/timeout/sauvegarde) ---
    @retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True) # Retry 1 fois après 5s si échec
    def generate_image_tool(prompt: str) -> str:
        """Appelle l'API Stable Diffusion WebUI (/sdapi/v1/txt2img) pour générer une image."""
        if not STABLE_DIFFUSION_API_URL:
            logging.error("[IMG] URL de l'API Stable Diffusion non configurée (STABLE_DIFFUSION_API_URL).")
            return "Erreur: Le service de génération d'images n'est pas configuré."

        api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"
        payload = {
            "prompt": prompt,
            "negative_prompt": "ugly, deformed, noisy, blurry, low quality, worst quality, deformed anatomy, bad anatomy, extra limbs, watermark, text, signature, username, words, letters, writing",
            "steps": 25, "width": 512, "height": 512, "cfg_scale": 7,
            "sampler_name": "DPM++ 2M Karras", "seed": -1,
            "save_images": False
        }
        logging.info(f"[IMG] Appel API SD: {api_url} pour prompt: '{prompt[:60]}...'")
        try:
            response = requests.post(api_url, json=payload, timeout=300) # Timeout de 5 minutes
            response.raise_for_status()
            data = response.json()

            if "images" in data and data["images"]:
                image_b64 = data["images"][0]
                try:
                    image_bytes = base64.b64decode(image_b64)
                    timestamp = int(time.time())
                    safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_')
                    IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True)
                    file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"
                    file_path.write_bytes(image_bytes)
                    logging.info(f"[IMG] Image SD générée et sauvée avec succès: {file_path}")
                    relative_path = file_path.relative_to(Path("/app")).as_posix()
                    return f"IMAGE_GENEREE:{relative_path}"
                except (base64.B64DecodeError, OSError, Exception) as save_err:
                    logging.error(f"[IMG] Erreur lors du décodage ou de la sauvegarde de l'image générée: {save_err}", exc_info=True)
                    return "Erreur: Impossible de traiter ou sauvegarder l'image générée après réception."
            else:
                logging.error(f"[IMG] Pas de clé 'images' ou liste vide dans la réponse API SD: {data}")
                return "Erreur: Aucune donnée d'image n'a été retournée par le service de génération."

        except requests.exceptions.Timeout as e:
            logging.warning(f"[IMG] Timeout lors de l'appel API SD ({e}). Tenacity va réessayer...")
            raise
        except requests.exceptions.RequestException as e:
            error_detail = f"Code: {e.response.status_code}, Réponse: {e.response.text}" if e.response else str(e)
            logging.warning(f"[IMG] Erreur requête API SD ({error_detail}). Tenacity va réessayer...")
            raise
        except Exception as e:
            logging.error(f"[IMG] Erreur inattendue lors appel API SD (non-retryable): {e}", exc_info=True)
            return f"Erreur inattendue lors de la communication avec le service d'image: {e}"
    # --- Fin Fonction Outil Image Gen ---

    # --- Placeholders pour Notes et RAG ---
    def _is_path_safe(requested_path: Path) -> bool: logging.warning("[NOTES] Fonction _is_path_safe PAS ENCORE ACTIVE."); return False
    def list_notes_safe() -> str: logging.warning("[NOTES] Fonction list_notes_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def save_note_safe(filename: str, content: str) -> str: logging.warning("[NOTES] Fonction save_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def read_note_safe(filename: str) -> str: logging.warning("[NOTES] Fonction read_note_safe PAS ENCORE ACTIVE."); return "Action désactivée."
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale (Identique Phase 6) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc de Phase 6) ...
    logging.info("--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat -> Agent avec WebSearch ET ImageGen actifs."""
        logging.info("\n--- Nouvelle Session Chat (Phase 7 v5.1 - Agent + WebSearch + ImgGen) ---")
        if not ia_components.get("vector_store_lc"):
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
                name="Generateur Image Stable Diffusion",
                func=generate_image_tool,
                description="INDISPENSABLE pour créer, dessiner, générer ou peindre une image à partir d'une description textuelle détaillée (prompt). Utilise cet outil si on demande explicitement de dessiner ou générer une image. Ne l'utilise PAS pour afficher des images existantes ou chercher des images sur le web."
            )
            list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="Liste les notes." )
            save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="Sauve une note." )
            read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="Lit une note." )
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
            logging.info(f"[Session Start] Agent Executor ({len(active_tools)} outils actifs) initialisé.")
            # --- FIN AGENT SETUP ---

            # Stocker vector store (pour upload RAG Phase 9)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            logging.info("[Session Start] VectorStore stocké pour session.")

            # Inputs & Upload (inchangé)
            logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
            cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.1 avec recherche web et génération d'images).", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Prête (Agent WebSearch + ImgGen) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {e}").send()


    # --- process_and_respond (inchangé depuis Phase 6, gère déjà sortie IMAGE_GENEREE:) ---
    # ... (Coller ici la fonction process_and_respond complète de Phase 6 v5.0) ...
    async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}, config={"callbacks": [cl.LangchainCallbackHandler()]}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1].strip(); image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += f" (Erreur: fichier {relative_image_path} introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage image)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée", parent_id=msg.id, indent=1).send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia", parent_id=msg.id, indent=1).send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion."); logging.info("--- Fin Traitement Input via Agent ---")


    # --- on_message (inchangé depuis Phase 6) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        # ... (coller ici on_text_message complet de Phase 6) ...
        logging.info(f"\n--- Msg Texte UI Reçu ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur avant/pendant appel process_and_respond pour texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message texte.").send()

    # --- on_audio_end (inchangé depuis Phase 6) ---
    @cl.on_audio_end
    async def on_audio_end(audio: cl.Audio):
        # ... (coller ici on_audio_end complet de Phase 6) ...
        logging.info(f"\n--- Fichier Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, Type: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None and transcribed_text_raw != "": transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide ou None."); await transcript_msg.update(content="Transcription vide ou aucun son détecté."); except Exception as e: logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription/traitement après tentatives."); logging.info("--- Fin Traitement Audio ---")


    # --- Section RAG Upload (Inchangée - Logique Phase 9) --- Sera implémenté plus tard ---
    # @cl.on_file_upload(...)
    # --- Fin Section RAG Upload ---

    ```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 7.4 : Redémarrer l'Environnement avec le Service d'Image

* **[⚠️⚠️⚠️] [⏱️⏱️⏱️]** (Démarrage SD très long !)
* **Contexte et Objectif :** Arrêter, reconstruire `assistant_app`, et redémarrer l'environnement complet pour inclure le nouveau service `stable_diffusion_webui` (avec limites) et activer l'outil image dans l'agent.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`. Avoir sauvegardé le `docker-compose.yml` (v5.1) et `app.py` (v5.0 Phase 7).
* **Action :**
    1.  Arrêter les services :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` et démarrer tous les services (maintenant 6) :
        ```bash
        docker compose up -d --build
        ```
* **Attente et Patience :**
    * Comme précédemment, le téléchargement initial de l'image SD et surtout son **premier démarrage** peuvent être très longs (5-15+ minutes).
    * Surveillez `docker ps` et attendez que les **6 conteneurs** soient `Up` et idéalement `(healthy)`. Rappelez-vous du `start_period` long pour SD.
    * Surveillez les logs de SD (`docker logs -f stable_diffusion_webui`) pour voir sa progression.

---

### Étape 7.5 : Tester la Génération d'Images via l'Agent

* **[⚠️⚠️⚠️] [⏱️⏱️]** (La génération d'image est lente)
* **Contexte et Objectif :** Vérifier que l'agent choisit l'outil image lorsque demandé, que l'API SD est appelée, et que l'image résultante est affichée dans Chainlit.
* **Prérequis :** Les 6 conteneurs sont `Up (healthy)`. **L'Étape 7.6 (Ajout Modèle SD) a été effectuée.** Interface Chainlit accessible.
* **Action :**
    1.  Ouvrez Chainlit (`http://127.0.0.1:8001`).
    2.  Ouvrez l'UI Web de Stable Diffusion (`http://127.0.0.1:7860`) dans un autre onglet. Assurez-vous qu'un **modèle checkpoint est sélectionné** en haut à gauche (voir Étape 7.6).
    3.  Vérifiez que l'API SD est active en allant sur `http://127.0.0.1:7860/docs`. Vous devez voir la documentation Swagger/OpenAPI.
    4.  **Suivez les logs de l'agent :** `docker logs -f assistant_app`.
    5.  **Dans Chainlit, demandez une image (texte ou voix) :** Utilisez un prompt clair indiquant une génération d'image.
        * Exemple : "Dessine un chat bleu avec un chapeau pointu"
        * Exemple : "Génère une image d'un paysage de montagne au coucher du soleil, style peinture à l'huile"
    6.  **Observez les logs `assistant_app` :** L'agent doit choisir `Action: Generateur Image Stable Diffusion`. L'`Action Input:` doit être le prompt. Vous devriez voir le log `[IMG] Appel API SD...`.
    7.  **Observez Chainlit :** L'assistant devrait répondre "Voici l'image demandée." (texte + audio), puis l'**image générée** doit apparaître. Cela peut prendre de 10 secondes à plusieurs minutes selon votre GPU et le prompt.
    8.  **Contre-Test :** Vérifiez qu'une question web ("Météo ?") utilise toujours l'outil WebSearch.
* **En Cas de Problème :** Référez-vous à la section Dépannage détaillée à la fin de l'Étape 7.6 ci-dessous.

---

### **Étape 7.6 : AJOUTER UN MODÈLE STABLE DIFFUSION (ACTION MANUELLE OBLIGATOIRE ET CRITIQUE !)**

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Téléchargement modèle + Copie + Sélection UI)
* **Contexte et Objectif :** Le service Stable Diffusion WebUI a besoin d'au moins un **modèle de checkpoint** (`.safetensors`) pour fonctionner. **Sans cette étape, la génération d'image échouera systématiquement.**
* **Action 1 : Trouver et Télécharger un Modèle Checkpoint :**
    * Allez sur [Civitai.com](https://civitai.com/) ou [Hugging Face](https://huggingface.co/models?pipeline_tag=text-to-image&sort=downloads).
    * Téléchargez un modèle `.safetensors` pour SD 1.5 (ex: `dreamshaper_8.safetensors`, `realisticVisionV60B1_v60B1VAE.safetensors`).
    * Sauvegardez-le sur votre PC Windows (ex: dans `C:\Users\VotreNom\Downloads`).
* **Action 2 : Copier le Modèle dans le Volume Docker via `docker cp` (Chemin PRÉCIS requis !) :**
    * **Ouvrez un terminal WSL.**
    * **Exécutez la commande `docker cp` en adaptant le chemin source :**

        ```bash
        # Adaptez '/mnt/c/Users/VotreNomWindows/Downloads/nom_du_modele.safetensors' !
        docker cp "/mnt/c/Users/VotreNomWindows/Downloads/nom_du_modele.safetensors" stable_diffusion_webui:/config/stable-diffusion/Stable-diffusion/
        ```
        * **Explication Chemin Destination :** `stable_diffusion_webui` (nom conteneur) + `:` + `/config/stable-diffusion/Stable-diffusion/` (chemin exact DANS le conteneur où A1111 cherche les checkpoints). Respectez la casse !
* **Action 3 : Vérifier et Sélectionner le Modèle dans l'UI Web (INDISPENSABLE !) :**
    1.  Ouvrez ou actualisez l'interface web de Stable Diffusion : `http://127.0.0.1:7860`.
    2.  En haut à gauche, cliquez sur la petite **icône de rafraîchissement (🔄)** à côté de la liste déroulante **"Stable Diffusion checkpoint"**.
    3.  Votre modèle (`nom_du_modele.safetensors`) doit apparaître dans la liste. **CLIQUEZ DESSUS pour le SÉLECTIONNER.**
    4.  Attendez quelques secondes que le modèle soit chargé en mémoire par l'UI Web.
* **Note Importante :** L'API utilisera le modèle **sélectionné dans l'UI**. Si aucun n'est sélectionné, ou si le fichier est invalide, l'API échouera.
* **Dépannage Génération Image (Si Étape 7.5 échoue APRÈS avoir fait 7.6) :**
    * **Erreur VRAM / CUDA Out of Memory :** Cause fréquente. Surveillez `nvidia-smi`. **Solution :** Arrêter (`docker compose down`), commenter `deploy:/resources:/reservations:/devices:` pour `xtts_server` et/ou `whisper_server` dans `docker-compose.yml`, redémarrer (`docker compose up -d`).
    * **Conteneur `stable_diffusion_webui` crash / `unhealthy` :** Logs `docker logs stable_diffusion_webui` -> VRAM ? Erreur `CLI_ARGS` ?
    * **Agent n'utilise pas outil Image :** Logs `assistant_app` (`verbose=True`) -> "Thought". Description outil / Prompt utilisateur clairs ?
    * **Agent utilise outil, mais Erreur / Pas d'image :** Logs `assistant_app` -> Erreur API ? Erreur sauvegarde ? Logs `stable_diffusion_webui` -> OOM ? **`No checkpoints found.`** (Modèle mal copié/sélectionné dans UI?) ?
    * **Image noire/corrompue :** Problème de VAE. Argument `--no-half-vae` présent. Sinon, télécharger un `.vae.pt` ou `.safetensors` VAE correspondant, copier dans `stable_diffusion_webui:/config/stable-diffusion/VAE/`, sélectionner dans UI (Settings > Stable Diffusion > SD VAE).
* **Attente et Transition :** Confirmez que vous avez ajouté un modèle, l'avez sélectionné dans l'UI, et que vous pouvez générer une image simple via Chainlit.

---

### **Étape 7.7: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** L'Agent avec Web/Img est fonctionnel (si VRAM suffisante et modèle OK). Sauvegarde avant d'ajouter les actions système (notes).
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple** (`down`, `cp -r`, `up -d`)
    * **Méthode 2 : Commit Git** (`git add .`, `git commit -m "Fin Phase 7 (v5.1) - Agent Web/Img OK (limites ressources + modèle SD ajouté)"`)
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la Phase 8.


Fin de la Phase 7 (Révisée v5.1).



## Phase 8 : Ajout Actions Système (Outils Notes - Révisée v5.0 - Sécurité)

**Objectif :** Donner à l'agent la capacité d'interagir avec le système de fichiers de manière **limitée et sécurisée**. Nous activons les outils pour lister, sauvegarder, et lire des fichiers texte simples (notes) dans un dossier spécifique et sécurisé (`/app/user_notes` dans le conteneur). La sécurité est primordiale ici.

**[⚠️⚠️⚠️] AVERTISSEMENT SÉCURITÉ :** L'accès au système de fichiers par un agent LLM est intrinsèquement risqué. Les fonctions implémentées ici sont conçues pour être **strictement confinées** au dossier `SAFE_NOTES_DIR` via la validation `_is_path_safe`. **Ne modifiez PAS cette logique de sécurité** et n'ajoutez PAS d'outils système plus permissifs sans une expertise approfondie des risques.

**Prérequis :** Phase 7 terminée et idéalement sauvegardée. L'Agent avec WebSearch et ImgGen est fonctionnel (sous réserve de VRAM et modèle SD).

**Approche :**
1.  Mise à jour de `app.py` :
    * Implémentation des fonctions de gestion de notes : `_is_path_safe` (cruciale pour la sécurité), `list_notes_safe`, `save_note_safe`, `read_note_safe`.
    * Ajout des `Tool` correspondants (`list_notes_tool`, `save_note_tool`, `read_note_tool`) à la liste `active_tools` de l'Agent Executor dans `@cl.on_chat_start`, avec des **descriptions claires** pour guider l'agent sur leur utilisation et le format d'input attendu.

---

### Étape 8.1 : Vérifier les Fichiers de Configuration

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Confirmer qu'aucun changement n'est nécessaire dans `docker-compose.yml` ou `requirements.txt` pour cette fonctionnalité, car elle repose sur des modules Python standard (`pathlib`, `os`).
* **Action :**
    * Vérifiez `docker-compose.yml` : Doit être identique à la fin de la Phase 7.
    * Vérifiez `requirements.txt` : Doit être identique à la version stable précédente.
* **Attente et Transition :** Configuration confirmée.

---

### Étape 8.2 : Mettre à jour le Fichier `app.py` (Activation Outils Système Sécurisés)

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Implémentation des fonctions de notes et activation des outils)
* **Contexte et Objectif :** Implémenter la logique sécurisée pour les opérations sur les fichiers de notes et rendre ces opérations accessibles à l'agent via de nouveaux outils. Les descriptions des outils sont essentielles pour que l'agent les utilise correctement.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version Phase 7 v5.0) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous :

    ```python
    # Fichier: app.py (Révisé v5.0 pour Phase 8 - Activation Outils Système/Notes)
    # Rôle: Application Khaldounia (Agent + WebSearch + ImgGen + SysActions - Robuste)

    import chainlit as cl
    import os, requests, base64, time, logging
    from pathlib import Path
    import chromadb
    import json # Pour le payload SD

    # Imports Robustesse & Sécurité
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input

    # Configuration Logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (Agent + Tools)
    logging.info("--- Début Importation Langchain (Phase 8 Rev 5.0) ---")
    from langchain_community.chat_models import ChatOllama
    from langchain_community.embeddings import SentenceTransformerEmbeddings
    from langchain_community.vectorstores import Chroma as ChromaLangchainStore
    from langchain.agents import AgentExecutor, create_react_agent, Tool
    from langchain_community.tools import DuckDuckGoSearchRun
    from langchain import hub
    # ... (Autres imports core) ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale (inchangée) ---
    # ... (coller ici la config globale complète de Phase 7) ...
    logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (STT/TTS/ImgGen + Fonctions Notes actives) ---

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None:
        # ... (coller ici transcribe_audio complet de Phase 7) ...
        api_url = f"{WHISPER_SERVER_URL}/asr"; params = {"encode": "true","task": "transcribe","language": "fr","output": "json"}; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)"); try: response = requests.post(api_url, params=params, files=files, timeout=120); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:60]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue STT: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        # ... (coller ici text_to_speech complet de Phase 7) ...
        api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
    def generate_image_tool(prompt: str) -> str:
        # ... (coller ici generate_image_tool complet de Phase 7) ...
        if not STABLE_DIFFUSION_API_URL: logging.error("[IMG] URL API SD non configurée."); return "Erreur: Service image non configuré."; api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, "cfg_scale": 7, "sampler_name": "DPM++ 2M Karras", "seed": -1, "save_images": False}; logging.info(f"[IMG] Appel API SD: '{prompt[:60]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: error_detail = f"Code: {e.response.status_code}, Réponse: {e.response.text}" if e.response else str(e); logging.warning(f"[IMG] Erreur requête SD ({error_detail}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"


    # --- Fonctions Outils Notes (Implémentées et Sécurisées pour Phase 8) ---

    # [CRUCIAL] Fonction de validation de chemin
    def _is_path_safe(requested_filename: str) -> Path | None:
        """
        Vérifie si le nom de fichier demandé est simple (pas de chemin, pas de '..', etc.)
        et s'il se résout bien à l'intérieur du dossier sécurisé SAFE_NOTES_DIR.

        Args:
            requested_filename: Le nom de fichier (string) demandé par l'agent.

        Returns:
            Le chemin absolu (Path object) vers le fichier s'il est sûr, sinon None.
        """
        if not requested_filename or isinstance(requested_filename, Path): # Vérifie type
             logging.warning(f"[_is_path_safe] Nom de fichier invalide fourni (Type: {type(requested_filename)}).")
             return None

        # Nettoyage basique et vérification caractères/patterns dangereux
        cleaned_filename = requested_filename.strip()
        if not cleaned_filename or "/" in cleaned_filename or "\\" in cleaned_filename or ".." in cleaned_filename or cleaned_filename.startswith(".") or cleaned_filename.startswith("~"):
            logging.warning(f"[_is_path_safe] Tentative d'utilisation de nom de fichier invalide ou chemin complexe : '{cleaned_filename}'")
            return None

        try:
            # Résout le chemin absolu du dossier sécurisé (échoue si SAFE_NOTES_DIR n'existe pas)
            safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True)
            # Construit le chemin absolu du fichier demandé DANS le dossier sécurisé
            requested_path_abs = (safe_dir_abs / cleaned_filename).resolve(strict=False) # strict=False pour permettre la création

            # Vérification finale : le chemin résolu doit être DANS ou ÉGAL au dossier sécurisé.
            # 'is_relative_to' est une bonne protection contre les attaques de liens symboliques etc.
            # On vérifie aussi explicitement qu'on ne cible pas le dossier lui-même.
            if requested_path_abs != safe_dir_abs and requested_path_abs.is_relative_to(safe_dir_abs):
                # Le chemin est sûr, retourne le Path object absolu
                return requested_path_abs
            else:
                # Si le chemin sort du dossier sécurisé (même via résolution de '..')
                logging.warning(f"[_is_path_safe] Chemin résolu '{requested_path_abs}' est HORS des limites sécurisées '{safe_dir_abs}'.")
                return None

        except FileNotFoundError:
            # Si SAFE_NOTES_DIR n'existe pas et ne peut être résolu
            logging.error(f"[_is_path_safe] Erreur critique: Le dossier de notes sécurisé '{SAFE_NOTES_DIR}' est introuvable.")
            return None
        except Exception as e:
            # Autres erreurs potentielles (permissions, nom de fichier trop long, etc.)
            logging.error(f"[_is_path_safe] Erreur inattendue lors de la validation du chemin pour '{cleaned_filename}': {e}", exc_info=True)
            return None # Sécurité par défaut : refuser en cas de doute

    # Outil pour lister les notes
    def list_notes_safe() -> str:
        """Liste les fichiers de notes existants dans le dossier sécurisé."""
        logging.info(f"[NOTES Tool] Action: Lister notes dans {SAFE_NOTES_DIR}")
        try:
            # S'assure que le dossier existe (créé aussi au démarrage)
            SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True)
            # Liste uniquement les fichiers (pas les sous-dossiers potentiels)
            files = sorted([f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()])
            if files:
                return "Notes personnelles trouvées:\n- " + "\n- ".join(files)
            else:
                return "Le dossier des notes personnelles est actuellement vide."
        except Exception as e:
            logging.error(f"[NOTES Tool] Erreur lors du listage des notes: {e}", exc_info=True)
            return f"Erreur inattendue lors de la tentative de listage des notes: {e}"

    # Outil pour sauvegarder une note
    # ATTENTION: La gestion des arguments via l'agent est fragile.
    # On attend 'filename,content' mais le LLM peut se tromper.
    def save_note_safe(filename_content: str) -> str:
        """
        Sauvegarde ou met à jour le contenu d'une note personnelle dans le dossier sécurisé.
        L'input doit être 'nom_fichier.txt,contenu de la note'.
        """
        logging.info(f"[NOTES Tool] Action: Tentative de sauvegarde note via input: '{filename_content[:100]}...'")

        # Tentative de séparer filename et content basé sur la première virgule
        try:
            filename, content = filename_content.split(',', 1)
            filename = filename.strip()
            content = content.strip() # Garde le contenu potentiellement multiligne
        except ValueError:
            logging.warning(f"[NOTES Tool] Input pour save_note mal formaté (pas de virgule?): '{filename_content[:100]}...'")
            return "Erreur: Format d'entrée incorrect. J'ai besoin du nom de fichier suivi d'une virgule, puis du contenu. Exemple: 'ma_note.txt, Voici le contenu.'"

        # Validation de sécurité du nom de fichier
        safe_full_path = _is_path_safe(filename)
        if safe_full_path is None:
            # _is_path_safe logue déjà l'avertissement
            return "Erreur de sécurité: Le nom de fichier spécifié ('{}') est invalide ou non autorisé.".format(filename)

        # Tentative d'écriture
        try:
            # S'assure que le dossier parent existe (normalement oui)
            safe_full_path.parent.mkdir(parents=True, exist_ok=True)
            # Écrit le fichier (écrase s'il existe)
            safe_full_path.write_text(content, encoding='utf-8')
            logging.info(f"[NOTES Tool] Note '{filename}' sauvée/mise à jour avec succès: {safe_full_path}")
            return f"Note '{filename}' sauvegardée avec succès."
        except OSError as e:
            logging.error(f"[NOTES Tool] Erreur OS lors sauvegarde '{filename}': {e}", exc_info=True)
            return f"Erreur système lors de la sauvegarde de la note '{filename}': {e}"
        except Exception as e:
            logging.error(f"[NOTES Tool] Erreur inattendue sauvegarde '{filename}': {e}", exc_info=True)
            return f"Erreur inattendue lors de la sauvegarde de la note '{filename}': {e}"

    # Outil pour lire une note
    def read_note_safe(filename: str) -> str:
        """Lit le contenu complet d'un fichier note personnelle existant."""
        logging.info(f"[NOTES Tool] Action: Tentative de lecture note '{filename}'")

        # Validation de sécurité du nom de fichier
        safe_full_path = _is_path_safe(filename.strip()) # Strip au cas où l'agent ajoute des espaces
        if safe_full_path is None:
            return "Erreur de sécurité: Le nom de fichier spécifié ('{}') est invalide ou non autorisé.".format(filename)

        # Vérifier si le fichier existe avant de lire
        if not safe_full_path.is_file():
            logging.warning(f"[NOTES Tool] Tentative de lecture d'un fichier note inexistant: '{filename}' ({safe_full_path})")
            # Lister les notes existantes peut aider l'agent
            existing_notes = list_notes_safe()
            return f"Erreur: Le fichier note '{filename}' n'existe pas. {existing_notes}"
        try:
            # Lecture du contenu
            content = safe_full_path.read_text(encoding='utf-8')
            logging.info(f"[NOTES Tool] Note '{filename}' lue avec succès.")
            # Retourne le contenu formaté pour l'agent
            # Limiter la taille si nécessaire ? Pour l'instant, retourne tout.
            return f"Contenu de la note '{filename}':\n\n{content}"
        except OSError as e:
             logging.error(f"[NOTES Tool] Erreur OS lors lecture '{filename}': {e}", exc_info=True)
             return f"Erreur système lors de la lecture de la note '{filename}': {e}"
        except Exception as e:
            logging.error(f"[NOTES Tool] Erreur inattendue lecture '{filename}': {e}", exc_info=True)
            return f"Erreur inattendue lors de la lecture de la note '{filename}': {e}"
    # --- Fin Fonctions Outils Notes ---

    # Placeholder outil RAG
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale (Identique Phase 7) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc de Phase 7) ...
    logging.info("--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat -> Agent avec WebSearch, ImgGen ET SysActions/Notes actifs."""
        logging.info("\n--- Nouvelle Session Chat (Phase 8 v5.0 - Agent + Web/Img/Sys Actifs) ---")
        if not ia_components.get("vector_store_lc"):
             logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
             await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

        try:
            llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
            logging.info("[Session Start] LLM pour Agent initialisé.")

            # --- AGENT SETUP : Activation des Outils Système/Notes ---
            logging.info("[Session Start] Configuration de l'Agent ReAct...")
            # 1. Définir TOUS les outils potentiels
            search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
            image_tool = Tool(
                name="Generateur Image Stable Diffusion",
                func=generate_image_tool,
                description="INDISPENSABLE pour créer, dessiner, générer une image. Input est une description textuelle."
            )
            # Outils Notes utilisant les fonctions sécurisées
            list_notes_tool = Tool(
                name="Lister Notes Personnelles",
                func=list_notes_safe,
                description="Utilise cet outil pour voir la liste des fichiers de notes personnelles qui existent déjà."
            )
            save_note_tool = Tool(
                name="Sauvegarder Note Personnelle",
                func=save_note_safe,
                # Description TRÈS IMPORTANTE pour guider le LLM sur le format d'input
                description="Utilise cet outil pour sauvegarder ou écraser une note personnelle. L'entrée DOIT être une seule chaîne de caractères formatée exactement comme suit : 'nom_du_fichier.txt, contenu complet de la note ici'."
            )
            read_note_tool = Tool(
                name="Lire Note Personnelle",
                func=read_note_safe,
                description="Utilise cet outil pour lire le contenu complet d'une note personnelle existante. L'entrée DOIT être juste le nom exact du fichier (ex: 'ma_liste_courses.txt')."
            )
            # Placeholder RAG
            rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs (non actif)." )

            # 2. Sélectionner outils ACTIFS pour CETTE phase (Phase 8)
            active_tools = [
                search_tool,
                image_tool,
                list_notes_tool, # <<< ACTIVÉ
                save_note_tool,  # <<< ACTIVÉ
                read_note_tool   # <<< ACTIVÉ
                # rag_tool # Pas encore
            ]
            logging.info(f"[Session Start] Outils actifs pour l'agent ({len(active_tools)}): {[tool.name for tool in active_tools]}")

            # 3. Prompt & Agent ReAct (inchangés)
            react_prompt = hub.pull("hwchase17/react")
            agent = create_react_agent(llm, active_tools, react_prompt)
            logging.info("[Session Start] Agent ReAct créé.")

            # 4. AgentExecutor (utilise la nouvelle liste `active_tools`)
            agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
            cl.user_session.set("agent_executor", agent_executor)
            logging.info(f"[Session Start] Agent Executor ({len(active_tools)} outils actifs) initialisé.")
            # --- FIN AGENT SETUP ---

            # Stocker vector store (pour upload RAG Phase 9)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])

            # Inputs & Upload (inchangé)
            logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
            cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.0 avec recherche, image, et gestion des notes).", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Prête (Agent Web/Img/Sys) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {e}").send()


    # --- process_and_respond (inchangé - utilise l'agent executor avec la nouvelle liste d'outils) ---
    # ... (Coller ici la fonction process_and_respond complète de Phase 7 v5.0) ...
    async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}, config={"callbacks": [cl.LangchainCallbackHandler()]}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1].strip(); image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += f" (Erreur: fichier {relative_image_path} introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage image)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée", parent_id=msg.id, indent=1).send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia", parent_id=msg.id, indent=1).send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion."); logging.info("--- Fin Traitement Input via Agent ---")


    # --- on_message (inchangé) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        # ... (coller ici on_text_message complet de Phase 7) ...
        logging.info(f"\n--- Msg Texte UI Reçu ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur avant/pendant appel process_and_respond pour texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message texte.").send()

    # --- on_audio_end (inchangé) ---
    @cl.on_audio_end
    async def on_audio_end(audio: cl.Audio):
        # ... (coller ici on_audio_end complet de Phase 7) ...
        logging.info(f"\n--- Fichier Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, Type: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None and transcribed_text_raw != "": transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide ou None."); await transcript_msg.update(content="Transcription vide ou aucun son détecté."); except Exception as e: logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription/traitement après tentatives."); logging.info("--- Fin Traitement Audio ---")


    # --- Section RAG Upload (Inchangée - Logique Phase 9) --- Sera implémenté plus tard ---
    # @cl.on_file_upload(...)
    # --- Fin Section RAG Upload ---

    ```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 8.3 : Redémarrer l'Environnement avec les Outils Système Actifs

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Mettre à jour le service `assistant_app` pour qu'il utilise le nouveau code `app.py` où l'Agent Executor a maintenant accès aux outils de gestion des notes.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`.
* **Action :**
    1.  Arrêter les services :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` et démarrer tous les services (toujours 6 services) :
        ```bash
        docker compose up -d --build
        ```
* **Attente :** Attendez que les 6 conteneurs soient `Up (healthy)` (`docker ps`).

---

### Étape 8.4 : Tester les Actions Système (Notes)

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Vérifier que l'agent peut lister, sauvegarder et lire des notes dans le dossier sécurisé, et qu'il est bloqué s'il tente d'accéder ailleurs.
* **Prérequis :** Les 6 conteneurs sont `Up (healthy)`. Interface Chainlit accessible.
* **Action :**
    1.  Ouvrez Chainlit (`http://127.0.0.1:8001`).
    2.  **Suivez les logs de l'agent :** `docker logs -f assistant_app`.
    3.  **Test 1 (Liste Initialement Vide) :** Demandez (texte ou voix) : "Peux-tu lister mes notes personnelles ?"
        * **Observation Logs:** Agent doit choisir `Action: Lister Notes Personnelles`, `Action Input:` (vide).
        * **Résultat Attendu Chainlit :** Réponse "Le dossier des notes personnelles est actuellement vide." (ou similaire).
    4.  **Test 2 (Sauvegarde) :** Demandez : "Sauvegarde une note. Le nom du fichier est `courses.txt` et le contenu est : Lait, Oeufs, Pain, Fromage." (Formulez clairement nom ET contenu).
        * **Observation Logs:** Agent doit choisir `Action: Sauvegarder Note Personnelle`. L'`Action Input:` devrait être (approximativement) `"courses.txt, Lait, Oeufs, Pain, Fromage"`. La fonction `save_note_safe` doit logger le succès.
        * **Résultat Attendu Chainlit :** Réponse "Note 'courses.txt' sauvegardée avec succès." (ou similaire).
    5.  **Test 3 (Liste après Sauvegarde) :** Demandez : "Quelles sont mes notes maintenant ?"
        * **Observation Logs:** Agent doit choisir `Action: Lister Notes Personnelles`.
        * **Résultat Attendu Chainlit :** Réponse "Notes personnelles trouvées:\n- courses.txt".
    6.  **Test 4 (Lecture) :** Demandez : "Quel est le contenu de ma note `courses.txt` ?" ou "Lis la note `courses.txt`".
        * **Observation Logs:** Agent doit choisir `Action: Lire Note Personnelle`, `Action Input: courses.txt`. La fonction `read_note_safe` doit logger le succès.
        * **Résultat Attendu Chainlit :** Réponse "Contenu de la note 'courses.txt':\n\nLait, Oeufs, Pain, Fromage".
    7.  **Test 5 (Sécurité - Échec Attendu) :** Essayez d'accéder en dehors du dossier autorisé.
        * Demandez : "Lis le fichier `app.py`" ou "Peux-tu me donner le contenu du fichier `../Dockerfile` ?"
        * Demandez : "Sauvegarde une note nommée `../test_hack.txt` avec le contenu 'test'"
        * **Observation Logs:** Agent peut tenter l'action (`Lire Note Personnelle` ou `Sauvegarder Note Personnelle`). La fonction `_is_path_safe` doit logger un avertissement (`Chemin résolu hors limites...` ou `Nom de fichier invalide...`) et retourner `None`.
        * **Résultat Attendu Chainlit :** L'agent doit répondre avec un message d'erreur indiquant que le nom de fichier est invalide ou non autorisé. Il ne doit PAS afficher le contenu du fichier système ni confirmer la sauvegarde en dehors du dossier notes.
* **En Cas de Problème :**
    * **Agent n'utilise pas le bon outil notes (ou pas du tout) :** Logs (`verbose=True`) -> "Thought". Les descriptions des outils dans `app.py` sont-elles assez claires ? Le prompt utilisateur était-il ambigu ? Essayez de reformuler.
    * **Agent utilise `save_note`, mais échoue ("Format incorrect") :** Le LLM a mal formaté l'`Action Input`. La description de l'outil demande `'filename,content'`, mais le LLM peut se tromper. Essayez de simplifier la demande ou de reformuler. C'est une limitation connue des agents LLM.
    * **Agent utilise l'outil, mais erreur "Système" ou "Inattendue" :** Logs `assistant_app` -> Erreur dans `list/save/read_note_safe` (`[ERREUR] Notes Tool...`) ? Problème de permissions sur `/app/user_notes` (peu probable si Dockerfile v5.0 est correct, mais vérifiable via `docker exec -it assistant_app ls -ld /app/user_notes`) ?
    * **Agent réussit une action de sécurité interdite (Test 5) :** C'est une **faille critique**. Vérifiez IMMÉDIATEMENT l'implémentation de `_is_path_safe` dans `app.py`. Elle ne devrait pas autoriser l'accès en dehors de `SAFE_NOTES_DIR`.
* **Attente et Transition :** Confirmez que les opérations de notes fonctionnent dans le dossier autorisé et que les tentatives d'accès non autorisé échouent avec une erreur de sécurité.

---

### Étape 8.5 : Rappel Important sur la Sécurité

* **[⚠️⚠️⚠️]**
* **Contexte et Objectif :** Réitérer l'avertissement de sécurité concernant les outils système.
* **Action :** Prenez note de ce qui suit :

> #### **Rappel de Sécurité : Outils Système et Agent LLM**
> L'accès au système de fichiers, même limité, introduit des risques. La fonction `_is_path_safe` est votre principale protection ici. Toute modification de cette fonction ou ajout d'outils plus puissants (exécution de commandes shell, etc.) doit être effectué avec une extrême prudence et une compréhension complète des implications en matière de sécurité, même pour un usage local.

---

### **Étape 8.6: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** L'Agent avec Web/Img/Sys(Notes) est fonctionnel. Sauvegarde avant d'aborder la logique d'upload RAG.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple**
        * `docker compose down`
        * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase8`
        * `docker compose up -d`
    * **Méthode 2 : Commit Git**
        * `git add .`
        * `git commit -m "Fin de la Phase 8 (v5.0) - Agent Web/Img/Sys(Notes) fonctionnel"`
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la Phase 9.

---

Fin de la Phase 8 (Révisée v5.0). 
L'assistant peut maintenant gérer des notes personnelles de manière sécurisée.



## Phase 9 : Chargement et Test des Données pour RAG (Révisée v5.0)

**Objectif :** Permettre à l'utilisateur de charger ses propres documents (PDF, TXT, DOCX) via l'interface Chainlit pour qu'ils soient traités et ajoutés à la base de données vectorielle (ChromaDB). Ceci constitue la partie "Ingestion" du système RAG (Retrieval-Augmented Generation). Nous allons implémenter la logique d'upload dans `app.py` et tester l'indexation.

**[⚠️⚠️] Importance :** Cette étape permet de personnaliser la base de connaissances de l'assistant. L'outil RAG pour *utiliser* ces données sera activé en Phase 10.

**Prérequis :** Phase 8 terminée et idéalement sauvegardée. L'Agent avec outils Web/Img/Sys(Notes) est fonctionnel.

**Approche :**
1.  Préparation de documents de test.
2.  Mise à jour de `app.py` pour implémenter la fonction décorée par `@cl.on_file_upload`, qui gérera :
    * La réception des fichiers depuis Chainlit.
    * La sauvegarde temporaire.
    * Le choix du loader Langchain approprié (PDF, TXT, DOCX via Unstructured).
    * Le chargement du contenu.
    * Le découpage (splitting) en chunks.
    * L'ajout des chunks (embeddings) à ChromaDB.
    * La gestion des erreurs et le nettoyage.
3.  Test de l'upload et observation des logs/messages pour confirmer l'indexation.

---

### Étape 9.1 : Préparer des Documents de Test

* **[⚠️] [⏱️⏱️]** (Nécessite de trouver/créer des fichiers)
* **Contexte et Objectif :** Avoir quelques fichiers à disposition pour tester la fonction d'upload. Choisissez des fichiers contenant des informations uniques pour pouvoir vérifier (en Phase 10) si l'assistant les retrouve.
* **Prérequis :** Accès à votre PC Windows.
* **Action (Manuelle) :**
    1.  Créez ou trouvez 1 ou 2 fichiers sur votre PC. Exemples :
        * **Fichier `infos_techniques_khaldounia.txt` (.txt) :**
            ```
            L'assistant Khaldounia utilise le modèle d'embedding 'all-MiniLM-L6-v2'.
            La base de données vectorielle est ChromaDB version 0.5.0.
            Le modèle LLM principal est Mistral 7B.
            ```
        * **Fichier `reunion_projets_2025.docx` (.docx) :**
            Un court document Word mentionnant : "Le projet 'Aladin' a été discuté lors de la réunion du 15 mars 2025. Le projet 'Sinbad' est prévu pour Q3."
        * **Un fichier PDF simple (non protégé par mot de passe).**
    2.  Notez l'emplacement de ces fichiers sur votre PC Windows, vous en aurez besoin pour l'upload.
* **Formats Supportés :** Le code implémenté ci-dessous supporte explicitement `.txt`, `.pdf` (via `PyPDFLoader`), et `.docx` (via `UnstructuredFileLoader`). D'autres formats pourraient fonctionner si `unstructured` les supporte et si les dépendances système nécessaires sont dans le `Dockerfile` (Phase 3.2).
* **Attente et Transition :** Assurez-vous d'avoir au moins un fichier de test prêt.

---

### Étape 9.2 : Mettre à jour le Fichier `app.py` (Implémentation Upload RAG)

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Ajout de la logique d'upload RAG)
* **Contexte et Objectif :** Implémenter la logique de la fonction `@cl.on_file_upload`. Cette fonction sera automatiquement appelée par Chainlit lorsqu'un utilisateur déposera des fichiers dans l'interface. Elle utilise les loaders et splitters Langchain pour traiter les fichiers et `vector_store_lc.add_documents` pour les indexer dans ChromaDB.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version Phase 8 v5.0) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par la version ci-dessous :

    ```python
    # Fichier: app.py (Révisé v5.0 pour Phase 9 - Implémentation Upload RAG)
    # Rôle: Application Khaldounia (Agent Web/Img/Sys + Logique Upload RAG)

    import chainlit as cl
    import os, requests, base64, time, logging
    from pathlib import Path
    import chromadb
    import json

    # Imports Robustesse & Sécurité
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input

    # Configuration Logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (Ajout Loaders/Splitter pour RAG)
    logging.info("--- Début Importation Langchain (Phase 9 Rev 5.0) ---")
    from langchain_community.chat_models import ChatOllama
    from langchain_community.embeddings import SentenceTransformerEmbeddings
    from langchain_community.vectorstores import Chroma as ChromaLangchainStore
    from langchain.agents import AgentExecutor, create_react_agent, Tool
    from langchain_community.tools import DuckDuckGoSearchRun
    from langchain import hub
    # --- Ajout pour RAG Upload ---
    from langchain_community.document_loaders import PyPDFLoader, TextLoader, UnstructuredFileLoader
    from langchain.text_splitter import RecursiveCharacterTextSplitter
    # --- Fin Ajout RAG Upload ---
    # ... (Autres imports core) ...
    logging.info("--- Fin Importation Langchain ---")

    # --- Configuration Globale (inchangée) ---
    # ... (coller ici la config globale complète de Phase 8) ...
    logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (STT/TTS/ImgGen/Notes inchangées) ---
    # ... (Coller ici les fonctions transcribe_audio, text_to_speech, generate_image_tool, _is_path_safe, list_notes_safe, save_note_safe, read_note_safe de Phase 8 v5.0) ...
    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None: api_url = f"{WHISPER_SERVER_URL}/asr"; params = {"encode": "true","task": "transcribe","language": "fr","output": "json"}; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)"); try: response = requests.post(api_url, params=params, files=files, timeout=120); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:60]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue STT: {e}", exc_info=True); return None
    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None: api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None
    @retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
    def generate_image_tool(prompt: str) -> str: if not STABLE_DIFFUSION_API_URL: logging.error("[IMG] URL API SD non configurée."); return "Erreur: Service image non configuré."; api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, "cfg_scale": 7, "sampler_name": "DPM++ 2M Karras", "seed": -1, "save_images": False}; logging.info(f"[IMG] Appel API SD: '{prompt[:60]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: error_detail = f"Code: {e.response.status_code}, Réponse: {e.response.text}" if e.response else str(e); logging.warning(f"[IMG] Erreur requête SD ({error_detail}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"
    def _is_path_safe(requested_filename: str) -> Path | None: if not requested_filename or isinstance(requested_filename, Path): logging.warning(f"[_is_path_safe] Nom de fichier invalide fourni (Type: {type(requested_filename)})."); return None; cleaned_filename = requested_filename.strip(); if not cleaned_filename or "/" in cleaned_filename or "\\" in cleaned_filename or ".." in cleaned_filename or cleaned_filename.startswith(".") or cleaned_filename.startswith("~"): logging.warning(f"[_is_path_safe] Tentative nom fichier invalide/complexe : '{cleaned_filename}'"); return None; try: safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True); requested_path_abs = (safe_dir_abs / cleaned_filename).resolve(strict=False); if requested_path_abs != safe_dir_abs and requested_path_abs.is_relative_to(safe_dir_abs): return requested_path_abs; else: logging.warning(f"[_is_path_safe] Chemin résolu '{requested_path_abs}' HORS limites '{safe_dir_abs}'."); return None; except FileNotFoundError: logging.error(f"[_is_path_safe] Erreur: Dossier notes sécurisé '{SAFE_NOTES_DIR}' introuvable."); return None; except Exception as e: logging.error(f"[_is_path_safe] Erreur validation chemin pour '{cleaned_filename}': {e}", exc_info=True); return None
    def list_notes_safe() -> str: logging.info(f"[NOTES Tool] Action: Lister notes dans {SAFE_NOTES_DIR}"); try: SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); files = sorted([f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()]); if files: return "Notes personnelles trouvées:\n- " + "\n- ".join(files); else: return "Dossier notes vide."; except Exception as e: logging.error(f"[NOTES Tool] Erreur listage notes: {e}", exc_info=True); return f"Erreur listage notes: {e}"
    def save_note_safe(filename_content: str) -> str: logging.info(f"[NOTES Tool] Tentative sauvegarde via input: '{filename_content[:100]}...'"); try: filename, content = filename_content.split(',', 1); filename = filename.strip(); content = content.strip(); except ValueError: logging.warning(f"[NOTES Tool] Input save_note mal formaté: '{filename_content[:100]}...'"); return "Erreur: Format incorrect. Usage: 'nom_fichier.txt, contenu'."; safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide/non autorisé ('{}').".format(filename); try: safe_full_path.parent.mkdir(parents=True, exist_ok=True); safe_full_path.write_text(content, encoding='utf-8'); logging.info(f"[NOTES Tool] Note '{filename}' sauvée: {safe_full_path}"); return f"Note '{filename}' sauvée."; except Exception as e: logging.error(f"[NOTES Tool] Erreur sauvegarde '{filename}': {e}", exc_info=True); return f"Erreur sauvegarde note '{filename}': {e}"
    def read_note_safe(filename: str) -> str: logging.info(f"[NOTES Tool] Tentative lecture note '{filename}'"); safe_full_path = _is_path_safe(filename.strip()); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide/non autorisé ('{}').".format(filename); if not safe_full_path.is_file(): logging.warning(f"[NOTES Tool] Fichier note inexistant: '{filename}'"); existing_notes = list_notes_safe(); return f"Erreur: Fichier note '{filename}' inexistant. {existing_notes}"; try: content = safe_full_path.read_text(encoding='utf-8'); logging.info(f"[NOTES Tool] Note '{filename}' lue."); return f"Contenu de '{filename}':\n\n{content}"; except Exception as e: logging.error(f"[NOTES Tool] Erreur lecture '{filename}': {e}", exc_info=True); return f"Erreur lecture note '{filename}': {e}"

    # Placeholder outil RAG (sera activé en Phase 10)
    def run_rag_retriever(query: str) -> str: logging.warning("[RAG Tool] Fonction run_rag_retriever PAS ENCORE ACTIVE."); return "Action désactivée."


    # --- Initialisation Globale (Inchangée) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc de Phase 8) ...
    logging.info("--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat -> Agent Web/Img/Sys actifs + Prépa Upload RAG."""
        logging.info("\n--- Nouvelle Session Chat (Phase 9 v5.0 - Agent Web/Img/Sys + Upload RAG prêt) ---")
        if not ia_components.get("vector_store_lc"):
             logging.error("[Session Start] Erreur critique: Composants IA non initialisés.")
             await cl.Message(content="Erreur critique: Composants IA non initialisés.").send(); return

        try:
            llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
            logging.info("[Session Start] LLM pour Agent initialisé.")

            # --- AGENT SETUP (Web/Img/Sys actifs, RAG défini mais INACTIF pour l'agent) ---
            logging.info("[Session Start] Configuration de l'Agent ReAct...")
            # 1. Définir TOUS les outils
            search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
            image_tool = Tool( name="Generateur Image Stable Diffusion", func=generate_image_tool, description="..." )
            list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="..." )
            save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="Input: 'nom_fichier.txt, contenu'." )
            read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="Input: nom_du_fichier.txt." )
            rag_tool = Tool( name="Recherche Document Personnel", func=run_rag_retriever, description="Cherche dans les docs (INACTIF)." ) # Fonction placeholder

            # 2. Sélectionner outils ACTIFS pour Phase 9 (PAS DE RAG TOOL pour l'AGENT ici)
            active_tools = [ search_tool, image_tool, list_notes_tool, save_note_tool, read_note_tool ]
            logging.info(f"[Session Start] Outils actifs pour l'agent ({len(active_tools)}): {[tool.name for tool in active_tools]}")

            # 3. Prompt & Agent ReAct
            react_prompt = hub.pull("hwchase17/react")
            agent = create_react_agent(llm, active_tools, react_prompt)
            logging.info("[Session Start] Agent ReAct créé.")

            # 4. AgentExecutor (utilise la liste SANS RAG tool)
            agent_executor = AgentExecutor( agent=agent, tools=active_tools, verbose=True, handle_parsing_errors=True, max_iterations=10 )
            cl.user_session.set("agent_executor", agent_executor)
            logging.info(f"[Session Start] Agent Executor ({len(active_tools)} outils actifs) initialisé.")
            # --- FIN AGENT SETUP ---

            # Stocker vector store (pour @cl.on_file_upload)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            logging.info("[Session Start] VectorStore stocké pour session.")

            # Inputs & Upload (inchangé - upload activé)
            logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
            cl.set_config(enable_file_upload=True, file_upload_accept=["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"], max_files=5, max_size_mb=100)

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.0 Web/Img/Notes). Vous pouvez charger des documents pour RAG.", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Prête (Agent Web/Img/Sys + Upload RAG prêt) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {e}").send()


    # --- process_and_respond (inchangé depuis Phase 8 - utilise l'agent SANS outil RAG) ---
    # ... (Coller ici la fonction process_and_respond complète de Phase 8 v5.0) ...
    async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}, config={"callbacks": [cl.LangchainCallbackHandler()]}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1].strip(); image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += f" (Erreur: fichier {relative_image_path} introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage image)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée", parent_id=msg.id, indent=1).send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia", parent_id=msg.id, indent=1).send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion."); logging.info("--- Fin Traitement Input via Agent ---")


    # --- on_message (inchangé) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        # ... (coller ici on_text_message complet de Phase 8) ...
        logging.info(f"\n--- Msg Texte UI Reçu ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur avant/pendant appel process_and_respond pour texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message texte.").send()

    # --- on_audio_end (inchangé) ---
    @cl.on_audio_end
    async def on_audio_end(audio: cl.Audio):
        # ... (coller ici on_audio_end complet de Phase 8) ...
        logging.info(f"\n--- Fichier Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, Type: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None and transcribed_text_raw != "": transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide ou None."); await transcript_msg.update(content="Transcription vide ou aucun son détecté."); except Exception as e: logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription/traitement après tentatives."); logging.info("--- Fin Traitement Audio ---")


    # --- Section RAG Upload (Implémentée pour Phase 9) ---
    # Définit les types de fichiers acceptés par Chainlit UI
    UPLOAD_ACCEPTED_TYPES = ["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"]
    UPLOAD_MAX_FILES = 5
    UPLOAD_MAX_MB = 100

    @cl.on_file_upload(accept=UPLOAD_ACCEPTED_TYPES, max_files=UPLOAD_MAX_FILES, max_size_mb=UPLOAD_MAX_MB)
    async def on_file_upload(files: list[cl.File]):
        """
        Gère l'upload de fichiers via l'interface Chainlit pour indexation RAG.
        Utilise Langchain Loaders, Text Splitter, et ajoute au VectorStore ChromaDB.
        """
        if not files:
            logging.info("[RAG Upload] Aucun fichier reçu (appel annulé?).")
            return

        logging.info(f"\n--- Début Traitement Upload RAG pour {len(files)} fichier(s) ---")
        await cl.Message(content=f"Réception de {len(files)} fichier(s). Préparation de l'indexation...").send()

        # Récupère le VectorStore Langchain de la session (initialisé dans on_chat_start)
        vector_store_lc = cl.user_session.get("vector_store_lc")
        if not vector_store_lc:
            logging.error("[RAG Upload] VectorStore non trouvé dans la session.")
            await cl.Message(content="Erreur interne: La base de données RAG n'est pas prête pour cette session.").send()
            return

        # Initialise le Text Splitter (peut être ajusté)
        text_splitter = RecursiveCharacterTextSplitter(
            chunk_size=1000, # Taille des morceaux de texte
            chunk_overlap=200, # Chevauchement entre les morceaux pour la continuité
            length_function=len
        )

        successful_files = []
        failed_files_details = {} # Stocke nom_fichier: message_erreur

        # Traite chaque fichier uploadé
        for file in files:
            processing_msg = cl.Message(content=f"Traitement de `{file.name}`...", author="Système RAG", disable_feedback=True)
            await processing_msg.send()
            temp_file_path: Path | None = None # Pour le cleanup

            try:
                # 1. Sauvegarde temporaire du fichier uploadé
                # Utilise le dossier /app/temp_uploads défini dans la config
                UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True)
                temp_file_path = UPLOAD_TEMP_DIR / f"{int(time.time())}_{file.name}" # Nom unique simple
                # Utilise cl.make_async pour exécuter l'opération I/O fichier sans bloquer Chainlit
                await cl.make_async(temp_file_path.write_bytes)(file.content)
                logging.info(f"[RAG Upload] Fichier temporaire sauvé: {temp_file_path}")

                # 2. Sélection du Loader Langchain approprié
                file_ext = os.path.splitext(file.name)[1].lower()
                loader = None
                logging.info(f"[RAG Upload] Détection type fichier '{file_ext}' pour {file.name}")
                if file_ext == ".pdf":
                    loader = PyPDFLoader(str(temp_file_path))
                elif file_ext == ".txt":
                    # Assumer UTF-8, peut nécessiter une détection d'encodage plus robuste si problèmes
                    loader = TextLoader(str(temp_file_path), encoding='utf-8')
                elif file_ext == ".docx":
                    # Unstructured devrait gérer docx si libreoffice est installé (voir Dockerfile)
                    loader = UnstructuredFileLoader(str(temp_file_path))
                # Ajoutez d'autres loaders ici si nécessaire (ex: CSVLoader, JSONLoader...)
                # elif file_ext == ".csv":
                #    from langchain_community.document_loaders.csv_loader import CSVLoader
                #    loader = CSVLoader(file_path=str(temp_file_path))

                if loader is None:
                    raise ValueError(f"Format de fichier non supporté : {file_ext}")

                # 3. Chargement du contenu du document (peut être long pour gros fichiers)
                logging.info(f"[RAG Upload] Chargement du contenu de : {file.name}...")
                # Exécute load() en asynchrone
                documents = await cl.make_async(loader.load)()
                logging.info(f"[RAG Upload] Document chargé ({len(documents)} partie(s) initiale(s)).")

                if not documents:
                     raise ValueError("Aucun contenu textuel n'a pu être extrait du document.")

                # 4. Découpage du document en chunks (peut être long)
                logging.info(f"[RAG Upload] Découpage en chunks de : {file.name}...")
                # Exécute split_documents() en asynchrone
                chunks = await cl.make_async(text_splitter.split_documents)(documents)
                logging.info(f"[RAG Upload] Nombre de chunks créés: {len(chunks)}")

                if not chunks:
                    raise ValueError("Le document a été chargé mais aucun chunk de texte n'a pu être créé après découpage.")

                # 5. Ajout des chunks (avec leurs embeddings) au VectorStore ChromaDB
                logging.info(f"[RAG Upload] Ajout de {len(chunks)} chunks de '{file.name}' à ChromaDB...")
                # Exécute add_documents() en asynchrone. C'est ici que les embeddings sont calculés et envoyés à ChromaDB.
                await cl.make_async(vector_store_lc.add_documents)(chunks)
                logging.info(f"[RAG Upload] Chunks ajoutés avec succès à ChromaDB.")

                # Confirme le succès pour ce fichier
                await processing_msg.update(content=f"Document `{file.name}` indexé avec succès ({len(chunks)} chunks).")
                successful_files.append(file.name)

            except Exception as e:
                # Gère les erreurs spécifiques à ce fichier
                error_text = f"Erreur lors du traitement de `{file.name}`: {str(e)}"
                logging.error(f"[RAG Upload] {error_text}", exc_info=True)
                await processing_msg.update(content=error_text)
                failed_files_details[file.name] = str(e)

            finally:
                # 6. Nettoyage du fichier temporaire, même en cas d'erreur
                if temp_file_path and await cl.make_async(temp_file_path.exists)():
                    try:
                        await cl.make_async(temp_file_path.unlink)()
                        logging.info(f"[RAG Upload] Fichier temporaire supprimé: {temp_file_path}")
                    except Exception as del_e:
                        # Erreur non bloquante mais à logger
                        logging.error(f"[RAG Upload] Erreur lors de la suppression du fichier temporaire {temp_file_path}: {del_e}")

        # Message récapitulatif final après traitement de tous les fichiers
        summary_message = "--- Résultat de l'indexation RAG ---\n"
        if successful_files:
            summary_message += f"✅ Fichiers indexés avec succès : {', '.join(successful_files)}\n"
        if failed_files_details:
            summary_message += "❌ Fichiers en échec :\n"
            for name, err in failed_files_details.items():
                summary_message += f"  - `{name}`: {err}\n"
        await cl.Message(content=summary_message).send()
        logging.info(f"--- Fin Traitement Upload RAG --- Succès: {len(successful_files)}, Échecs: {len(failed_files_details)}")
    # --- Fin Section RAG Upload ---

    ```

* **Action :** Sauvegardez le fichier `app.py`.

---

### Étape 9.3 : Redémarrer l'Environnement pour Activer l'Upload RAG

* **[⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Mettre à jour `assistant_app` pour utiliser le nouveau code `app.py` qui contient la logique d'upload RAG.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`.
* **Action :**
    1.  Arrêter les services :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` et démarrer tous les services (toujours 6 services) :
        ```bash
        docker compose up -d --build
        ```
* **Attente :** Attendez que les 6 conteneurs soient `Up (healthy)` (`docker ps`).

---

### Étape 9.4 : Tester l'Upload de Fichiers RAG

* **[⚠️⚠️] [⏱️⏱️]** (Le traitement peut être long selon la taille/complexité des fichiers)
* **Contexte et Objectif :** Utiliser l'interface Chainlit pour uploader les documents de test préparés et vérifier que le processus d'indexation se déroule correctement.
* **Prérequis :** Les 6 conteneurs sont `Up (healthy)`. Interface Chainlit accessible. Fichiers de test prêts (Étape 9.1).
* **Action :**
    1.  Ouvrez/Actualisez l'interface Chainlit : `http://127.0.0.1:8001`.
    2.  **Suivez les logs de l'application :** `docker logs -f assistant_app`.
    3.  **Localisez l'icône d'upload** dans l'interface Chainlit (souvent un trombone 📎 ou une flèche vers le haut ↑ près de la zone de saisie). Cliquez dessus.
    4.  Votre explorateur de fichiers Windows s'ouvre. **Sélectionnez le ou les fichiers de test** que vous avez préparés (ex: `infos_techniques_khaldounia.txt`, `reunion_projets_2025.docx`). Vous pouvez en sélectionner plusieurs (jusqu'à 5 par défaut).
    5.  Validez la sélection de fichiers.
    6.  **Observez les messages dans Chainlit :**
        * "Réception de X fichier(s)..."
        * "Traitement de `nom_fichier_1.ext`..."
        * "Document `nom_fichier_1.ext` indexé avec succès (Y chunks)."
        * (Répété pour chaque fichier)
        * "--- Résultat de l'indexation RAG ---" avec le résumé des succès/échecs.
    7.  **Observez les logs `assistant_app` :** Vous devriez voir les étapes détaillées pour chaque fichier :
        * "--- Début Traitement Upload RAG..."
        * "[RAG Upload] Fichier temporaire sauvé..."
        * "[RAG Upload] Détection type fichier..."
        * "[RAG Upload] Chargement du contenu..."
        * "[RAG Upload] Découpage en chunks..."
        * "[RAG Upload] Ajout de X chunks ... à ChromaDB..."
        * "[RAG Upload] Chunks ajoutés avec succès."
        * "[RAG Upload] Fichier temporaire supprimé..."
        * "--- Fin Traitement Upload RAG ---"

---

### Étape 9.5 : Tester la Fonctionnalité RAG (Récupération Basique - Observation Indirecte)

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** Bien que l'outil RAG ne soit pas encore **actif** pour l'agent, nous pouvons essayer de poser une question spécifique au contenu chargé pour voir si des informations pertinentes sont récupérées par le système sous-jacent (même si l'agent les ignore probablement). C'est un test indirect de l'indexation.
* **Prérequis :** L'Étape 9.4 a indiqué un succès pour l'indexation d'au moins un fichier contenant des informations uniques.
* **Action :**
    1.  **Suivez les logs de l'agent :** `docker logs -f assistant_app`.
    2.  **Dans Chainlit, posez une question très spécifique** dont la réponse se trouve **uniquement** dans l'un des documents que vous venez de charger.
        * Exemple (si `infos_techniques_khaldounia.txt` chargé) : "Quel est le nom exact du modèle d'embedding utilisé par Khaldounia ?"
        * Exemple (si `reunion_projets_2025.docx` chargé) : "Quand a eu lieu la réunion sur le projet Aladin ?"
    3.  **Observez attentivement les logs `assistant_app` (`verbose=True`) :**
        * **Comportement Attendu (Phase 9) :** L'agent devrait "penser" et probablement choisir `Action: Recherche Web DuckDuckGo` (car l'outil RAG n'est pas dans sa liste `active_tools`) ou répondre qu'il ne sait pas / inventer une réponse générique. Il **ne devrait PAS** choisir `Action: Recherche Document Personnel`.
        * **Ce que cela valide (indirectement) :** Si l'upload en 9.4 a fonctionné, les données *sont* dans ChromaDB, prêtes à être utilisées en Phase 10. L'échec de l'agent à les utiliser ici est normal et confirme que l'outil RAG n'est pas encore actif pour lui.
    4.  **Vérifiez la réponse dans Chainlit :** Elle sera probablement incorrecte ou basée sur le web, **pas** sur vos documents.
* **Contre-Test :** Posez une question Web ("Météo ?"). L'agent doit utiliser `Recherche Web DuckDuckGo`.

---

### Étape 9.6 : Dépannage Upload RAG (Si Étape 9.4 échoue)

* **Problème : Erreur affichée dans Chainlit ou logs pendant l'upload (`Traitement terminé... Échecs: ...`).**
    * **Analyser l'erreur :** Regardez le message d'erreur exact dans Chainlit et les logs `assistant_app` (`[RAG Upload] Erreur...`).
    * **Causes Possibles & Solutions :**
        * **"Format de fichier non supporté" :** Vérifiez que l'extension est bien `.txt`, `.pdf`, ou `.docx`. Pour d'autres formats, `unstructured` peut nécessiter des dépendances système supplémentaires non incluses dans le `Dockerfile` actuel.
        * **Erreur lors du chargement (Loader Error) :** Fichier corrompu ? PDF protégé par mot de passe ? Problème avec `PyPDFLoader` ou `UnstructuredFileLoader` (rare, mais possible). Essayez avec un autre fichier du même type ou convertissez en `.txt`. Si lié à `unstructured` et `.docx`, vérifiez que `libreoffice` est bien installé (logs build Dockerfile).
        * **"Aucun contenu textuel..." ou "Aucun chunk..." :** Le fichier est peut-être vide, ou le loader n'a rien pu en extraire (ex: PDF contenant uniquement des images sans OCR).
        * **Erreur lors de l'ajout à ChromaDB (`add_documents`) :** Problème de connexion au service `chromadb` (est-il `Up (healthy)` ?) ou erreur interne ChromaDB (vérifiez logs `docker logs chromadb`). Problème avec les embeddings (vérifiez logs `assistant_app` au démarrage pour l'init SentenceTransformer).
        * **Erreur de Permissions (rare avec `appuser` et `chown` dans Dockerfile v5.0) :** Vérifiez les logs pour `Permission denied` sur `/app/temp_uploads`.
    * **Stratégie :** Corrigez le fichier si possible, vérifiez l'état des services dépendants (`chromadb`), et réessayez l'upload. Utilisez Annexe A avec l'IA guide.
* **Problème : Upload marqué comme succès, mais info non utilisée (Test 9.5).**
    * **Cause :** Comportement attendu en Phase 9. L'agent n'a pas encore l'outil RAG activé.
    * **Action :** C'est normal. Passez à la Phase 10 pour activer l'outil.

---

### **Étape 9.7: Sauvegarde de Fin de Phase (Optionnel mais Recommandé)**

* **[⚠️] [⏱️]**
* **Contexte et Objectif :** La logique d'upload RAG est implémentée et l'indexation semble fonctionner. Sauvegarde avant l'activation finale de l'outil RAG pour l'agent.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple**
        * `docker compose down`
        * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase9`
        * `docker compose up -d`
    * **Méthode 2 : Commit Git**
        * `git add .`
        * `git commit -m "Fin de la Phase 9 (v5.0) - Upload RAG implémenté et indexation OK"`
* **Attente et Transition :** Sauvegarde effectuée (ou ignorée). Prêt pour la dernière phase fonctionnelle : Phase 10.


Fin de la Phase 9 (Révisée v5.0). 
L'assistant peut maintenant "apprendre" à partir de vos documents !



## Phase 10 : Intégration de l'Outil RAG dans l'Agent (Révisée v5.0 - Finale)

**Objectif :** Permettre à l'Agent d'utiliser activement la base de connaissances personnelles (documents chargés en Phase 9) pour répondre aux questions. Nous allons implémenter la fonction de recherche RAG et l'activer comme un outil pour l'agent. Ceci complète l'ensemble des fonctionnalités de base de Khaldounia.

**[⚠️⚠️⚠️] Importance :** C'est l'étape qui connecte la mémoire personnelle (documents chargés) à l'intelligence de l'agent. La qualité de la description de l'outil RAG est cruciale pour que l'agent l'utilise correctement.

**Prérequis :** Phase 9 terminée et idéalement sauvegardée. La logique d'upload RAG fonctionne et des documents pertinents ont été indexés dans ChromaDB.

**Approche :**
1.  Mise à jour finale de `app.py` :
    * Implémentation de la fonction `run_rag_retriever` qui utilise le `retriever` Langchain pour chercher dans ChromaDB et formater les résultats pour l'agent.
    * Ajout du `rag_tool` (utilisant `run_rag_retriever`) à la liste `active_tools` de l'Agent Executor dans `@cl.on_chat_start`, avec une description forte et directive.
2.  Test complet pour vérifier que l'agent choisit et utilise l'outil RAG lorsque la question concerne le contenu des documents chargés.

---

### Étape 10.1 : Mettre à jour le Fichier `app.py` (Activation Outil RAG - Version Finale)

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Implémentation fonction RAG et activation outil Agent)
* **Contexte et Objectif :** Fournir le code final pour `app.py` qui inclut l'implémentation de la fonction `run_rag_retriever` et active le `rag_tool` pour l'agent.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core/app`. Le fichier `app.py` (version Phase 9 v5.0) existe.
* **Action :** Ouvrez `app.py` dans votre éditeur.
* **Action :** Remplacez **l'intégralité** de son contenu par cette **version finale complète (v5.0)** :

    ```python
    # Fichier: app.py (Version Finale Complète - Phase 10 v5.0 - Outil RAG Actif)
    # Rôle: Application Khaldounia (Agent avec TOUS les outils: Web, Img, Sys/Notes, RAG - Robuste)

    import chainlit as cl
    import os, requests, base64, time, logging
    from pathlib import Path
    import chromadb
    import json

    # Imports Robustesse & Sécurité
    from tenacity import retry, stop_after_attempt, wait_fixed
    from langchain_core.prompts.input import sanitize_input

    # Configuration Logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    # Imports Langchain (Tous les composants nécessaires)
    logging.info("--- Début Importation Langchain (Phase 10 Rev 5.0 - Finale) ---")
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
    # ... (coller ici la config globale complète de Phase 9) ...
    logging.info("--- Début Chargement Configuration ---"); OLLAMA_BASE_URL = os.getenv("OLLAMA_HOST", "http://ollama:11434"); XTTS_SERVER_URL = os.getenv("XTTS_SERVER_URL", "http://xtts_server:8020"); CHROMADB_HOST = os.getenv("CHROMADB_HOST", "chromadb"); CHROMADB_PORT = os.getenv("CHROMADB_PORT", "8000"); WHISPER_SERVER_URL = os.getenv("WHISPER_SERVER_URL", "http://whisper_server:9000"); STABLE_DIFFUSION_API_URL = os.getenv("STABLE_DIFFUSION_API_URL", "http://stable_diffusion_webui:7860"); OLLAMA_MODEL = os.getenv("OLLAMA_MODEL", "mistral:latest"); EMBEDDING_MODEL_NAME = "all-MiniLM-L6-v2"; CHROMA_COLLECTION_NAME = "khaldounia_docs"; EMBEDDING_CACHE_DIR = Path("/app/embedding_cache"); SAFE_NOTES_DIR = Path("/app/user_notes"); IMAGE_OUTPUT_DIR = Path("/app/generated_images"); UPLOAD_TEMP_DIR = Path("/app/temp_uploads"); CHAT_HISTORY_DIR = Path("/app/chat_history"); SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); CHAT_HISTORY_DIR.mkdir(parents=True, exist_ok=True); logging.info(f"Config Lues: Ollama@{OLLAMA_BASE_URL}, XTTS@{XTTS_SERVER_URL}, Chroma@{CHROMADB_HOST}:{CHROMADB_PORT}, Whisper@{WHISPER_SERVER_URL}, SD@{STABLE_DIFFUSION_API_URL}"); logging.info("--- Fin Chargement Configuration ---")


    # --- Fonctions Utilitaires (Toutes actives, y compris RAG) ---

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def transcribe_audio(audio_bytes: bytes) -> str | None:
        # ... (coller ici transcribe_audio complet de Phase 8) ...
        api_url = f"{WHISPER_SERVER_URL}/asr"; params = {"encode": "true","task": "transcribe","language": "fr","output": "json"}; files = {'audio_file': ('audio.wav', audio_bytes, 'audio/wav')}; logging.info(f"[STT] Appel API Whisper: {api_url} (lang=fr)"); try: response = requests.post(api_url, params=params, files=files, timeout=120); response.raise_for_status(); result = response.json(); transcribed_text = result.get("text", "").strip(); logging.info(f"[STT] Whisper: '{transcribed_text[:60]}...'"); return transcribed_text if transcribed_text else None; except requests.exceptions.Timeout as e: logging.warning(f"[STT] Timeout Whisper ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[STT] Erreur requête Whisper ({e}). Retry..."); raise; except Exception as e: logging.error(f"[STT] Erreur inattendue STT: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(3), wait=wait_fixed(2), reraise=True)
    def text_to_speech(text: str) -> bytes | None:
        # ... (coller ici text_to_speech complet de Phase 8) ...
        api_url = f"{XTTS_SERVER_URL}/generate"; payload = {"text": text, "language": "fr", "speaker_wav": "speaker_ref.wav", "output_format": "wav"}; logging.info(f"[TTS] Appel API: {api_url} pour texte: '{text[:40]}...'"); try: response = requests.post(api_url, json=payload, timeout=180); response.raise_for_status(); data = response.json(); audio_base64 = data.get("audio_base64"); if audio_base64: logging.info("[TTS] Audio reçu."); return base64.b64decode(audio_base64); else: logging.error(f"[TTS] Réponse API OK mais pas de 'audio_base64'."); return None; except requests.exceptions.Timeout as e: logging.warning(f"[TTS] Timeout XTTS ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: logging.warning(f"[TTS] Erreur requête XTTS ({e}). Retry..."); raise; except Exception as e: logging.error(f"[TTS] Erreur inattendue TTS: {e}", exc_info=True); return None

    @retry(stop=stop_after_attempt(2), wait=wait_fixed(5), reraise=True)
    def generate_image_tool(prompt: str) -> str:
        # ... (coller ici generate_image_tool complet de Phase 8) ...
        if not STABLE_DIFFUSION_API_URL: logging.error("[IMG] URL API SD non configurée."); return "Erreur: Service image non configuré."; api_url = f"{STABLE_DIFFUSION_API_URL}/sdapi/v1/txt2img"; payload = {"prompt": prompt, "negative_prompt": "ugly...", "steps": 25, "width": 512, "height": 512, "cfg_scale": 7, "sampler_name": "DPM++ 2M Karras", "seed": -1, "save_images": False}; logging.info(f"[IMG] Appel API SD: '{prompt[:60]}...'"); try: response = requests.post(api_url, json=payload, timeout=300); response.raise_for_status(); data = response.json(); if "images" in data and data["images"]: image_b64 = data["images"][0]; try: image_bytes = base64.b64decode(image_b64); timestamp = int(time.time()); safe_prompt_suffix = "".join(c if c.isalnum() else "_" for c in prompt[:30]).rstrip('_'); IMAGE_OUTPUT_DIR.mkdir(parents=True, exist_ok=True); file_path = IMAGE_OUTPUT_DIR / f"generated_{timestamp}_{safe_prompt_suffix}.png"; file_path.write_bytes(image_bytes); logging.info(f"[IMG] Image SD sauvée: {file_path}"); relative_path = file_path.relative_to(Path("/app")).as_posix(); return f"IMAGE_GENEREE:{relative_path}"; except Exception as save_err: logging.error(f"[IMG] Erreur décodage/sauvegarde image: {save_err}", exc_info=True); return "Erreur: Impossible de sauvegarder image générée."; else: logging.error(f"[IMG] Pas d'image dans réponse API SD: {data}"); return "Erreur: Aucune image retournée."; except requests.exceptions.Timeout as e: logging.warning(f"[IMG] Timeout SD ({e}). Retry..."); raise; except requests.exceptions.RequestException as e: error_detail = f"Code: {e.response.status_code}, Réponse: {e.response.text}" if e.response else str(e); logging.warning(f"[IMG] Erreur requête SD ({error_detail}). Retry..."); raise; except Exception as e: logging.error(f"[IMG] Erreur inattendue API SD: {e}", exc_info=True); return f"Erreur inattendue service image: {e}"

    def _is_path_safe(requested_filename: str) -> Path | None:
        # ... (coller ici _is_path_safe complet de Phase 8) ...
        if not requested_filename or isinstance(requested_filename, Path): logging.warning(f"[_is_path_safe] Nom de fichier invalide fourni (Type: {type(requested_filename)})."); return None; cleaned_filename = requested_filename.strip(); if not cleaned_filename or "/" in cleaned_filename or "\\" in cleaned_filename or ".." in cleaned_filename or cleaned_filename.startswith(".") or cleaned_filename.startswith("~"): logging.warning(f"[_is_path_safe] Tentative nom fichier invalide/complexe : '{cleaned_filename}'"); return None; try: safe_dir_abs = SAFE_NOTES_DIR.resolve(strict=True); requested_path_abs = (safe_dir_abs / cleaned_filename).resolve(strict=False); if requested_path_abs != safe_dir_abs and requested_path_abs.is_relative_to(safe_dir_abs): return requested_path_abs; else: logging.warning(f"[_is_path_safe] Chemin résolu '{requested_path_abs}' HORS limites '{safe_dir_abs}'."); return None; except FileNotFoundError: logging.error(f"[_is_path_safe] Erreur: Dossier notes sécurisé '{SAFE_NOTES_DIR}' introuvable."); return None; except Exception as e: logging.error(f"[_is_path_safe] Erreur validation chemin pour '{cleaned_filename}': {e}", exc_info=True); return None

    def list_notes_safe() -> str:
        # ... (coller ici list_notes_safe complet de Phase 8) ...
        logging.info(f"[NOTES Tool] Action: Lister notes dans {SAFE_NOTES_DIR}"); try: SAFE_NOTES_DIR.mkdir(parents=True, exist_ok=True); files = sorted([f.name for f in SAFE_NOTES_DIR.iterdir() if f.is_file()]); if files: return "Notes personnelles trouvées:\n- " + "\n- ".join(files); else: return "Dossier notes vide."; except Exception as e: logging.error(f"[NOTES Tool] Erreur listage notes: {e}", exc_info=True); return f"Erreur listage notes: {e}"

    def save_note_safe(filename_content: str) -> str:
        # ... (coller ici save_note_safe complet de Phase 8) ...
        logging.info(f"[NOTES Tool] Tentative sauvegarde via input: '{filename_content[:100]}...'"); try: filename, content = filename_content.split(',', 1); filename = filename.strip(); content = content.strip(); except ValueError: logging.warning(f"[NOTES Tool] Input save_note mal formaté: '{filename_content[:100]}...'"); return "Erreur: Format incorrect. Usage: 'nom_fichier.txt, contenu'."; safe_full_path = _is_path_safe(filename); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide/non autorisé ('{}').".format(filename); try: safe_full_path.parent.mkdir(parents=True, exist_ok=True); safe_full_path.write_text(content, encoding='utf-8'); logging.info(f"[NOTES Tool] Note '{filename}' sauvée: {safe_full_path}"); return f"Note '{filename}' sauvée."; except Exception as e: logging.error(f"[NOTES Tool] Erreur sauvegarde '{filename}': {e}", exc_info=True); return f"Erreur sauvegarde note '{filename}': {e}"

    def read_note_safe(filename: str) -> str:
        # ... (coller ici read_note_safe complet de Phase 8) ...
        logging.info(f"[NOTES Tool] Tentative lecture note '{filename}'"); safe_full_path = _is_path_safe(filename.strip()); if safe_full_path is None: return "Erreur sécurité: Nom fichier invalide/non autorisé ('{}').".format(filename); if not safe_full_path.is_file(): logging.warning(f"[NOTES Tool] Fichier note inexistant: '{filename}'"); existing_notes = list_notes_safe(); return f"Erreur: Fichier note '{filename}' inexistant. {existing_notes}"; try: content = safe_full_path.read_text(encoding='utf-8'); logging.info(f"[NOTES Tool] Note '{filename}' lue."); return f"Contenu de '{filename}':\n\n{content}"; except Exception as e: logging.error(f"[NOTES Tool] Erreur lecture '{filename}': {e}", exc_info=True); return f"Erreur lecture note '{filename}': {e}"

    # --- Fonction Wrapper pour Outil RAG (Implémentée pour Phase 10) ---
    @retry(stop=stop_after_attempt(2), wait=wait_fixed(3), reraise=True) # Ajout retry simple
    def run_rag_retriever(query: str) -> str:
        """
        Exécute la recherche RAG dans ChromaDB via le retriever Langchain
        et formate les documents trouvés pour la sortie de l'outil de l'agent.
        """
        logging.info(f"\n[RAG Tool] Outil 'Recherche Document Personnel' demandé pour la query: '{query}'")
        # Récupère le retriever Langchain initialisé et stocké dans la session utilisateur
        retriever = cl.user_session.get("retriever")
        if not retriever:
            logging.error("[RAG Tool] ERREUR CRITIQUE: Retriever non trouvé dans la session utilisateur.")
            # Important de retourner une erreur claire à l'agent
            return "Erreur Interne: La fonction de recherche dans les documents personnels n'est pas disponible actuellement (Retriever non initialisé)."

        try:
            # Invoque le retriever de manière synchrone pour obtenir les documents pertinents
            # Le nombre de documents retournés ('k') est défini lors de l'init du retriever
            logging.info(f"[RAG Tool] Invocation du retriever (sync) pour: '{query}'")
            # Note: Pourrait être rendu async avec await retriever.ainvoke(query) si nécessaire
            docs = retriever.invoke(query)

            if docs:
                # Formate les résultats pour être lisibles par l'agent (et l'humain dans les logs)
                formatted_docs = "\n\n---\n\n".join([
                    f"Extrait pertinent du document '{doc.metadata.get('source', 'Inconnu')}' (Chunk {i+1}):\n{doc.page_content}"
                    for i, doc in enumerate(docs)
                ])
                logging.info(f"[RAG Tool] {len(docs)} document(s) pertinent(s) trouvé(s) et formaté(s).")

                # Limite la taille de la réponse pour éviter de surcharger le contexte de l'agent LLM
                max_length = 3500 # Limite de caractères (ajuster si nécessaire)
                if len(formatted_docs) > max_length:
                    formatted_docs = formatted_docs[:max_length] + "\n\n[... Contenu RAG tronqué ...]"
                    logging.warning(f"[RAG Tool] Le contenu RAG retourné a été tronqué à {max_length} caractères.")

                # Retourne la chaîne formatée qui sera l'"Observation" pour l'agent
                return f"Voici des extraits pertinents trouvés dans vos documents personnels concernant '{query}':\n{formatted_docs}"
            else:
                # Cas où le retriever ne trouve aucun document pertinent
                logging.info("[RAG Tool] Aucun document pertinent trouvé par le retriever pour cette query.")
                return "Je n'ai trouvé aucune information pertinente dans vos documents personnels pour répondre à cette question."

        except Exception as e:
            # Gère les erreurs potentielles pendant la recherche RAG
            # (ex: connexion à ChromaDB échoue, erreur d'embedding...)
            logging.error(f"[RAG Tool] Erreur lors de l'invocation du retriever ou du formatage: {e}", exc_info=True)
            return f"Une erreur s'est produite lors de la recherche dans vos documents personnels: {e}"
    # --- Fin Fonction Wrapper RAG ---


    # --- Initialisation Globale (Inchangée) ---
    # ... (coller ici l'init globale embeddings, chroma_client, vector_store_lc, RETRIEVER de Phase 8) ...
    logging.info("\n--- Début Initialisation Globale IA ---"); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}; try: logging.info(f"[INIT] Embeddings ({EMBEDDING_MODEL_NAME})..."); ia_components["embeddings"] = SentenceTransformerEmbeddings(model_name=EMBEDDING_MODEL_NAME, cache_folder=str(EMBEDDING_CACHE_DIR)); logging.info("[INIT] Embedding Model prêt."); logging.info(f"[INIT] Client ChromaDB http://{CHROMADB_HOST}:{CHROMADB_PORT}..."); ia_components["chroma_client"] = chromadb.HttpClient(host=CHROMADB_HOST, port=int(CHROMADB_PORT), timeout=60); ia_components["chroma_client"].heartbeat(); logging.info("[INIT] Client ChromaDB connecté."); logging.info(f"[INIT] VectorStore '{CHROMA_COLLECTION_NAME}'..."); ia_components["vector_store_lc"] = ChromaLangchainStore(client=ia_components["chroma_client"], collection_name=CHROMA_COLLECTION_NAME, embedding_function=ia_components["embeddings"]); logging.info("[INIT] Langchain VectorStore prêt."); logging.info("[INIT] Création Retriever depuis VectorStore (k=3)..."); ia_components["retriever"] = ia_components["vector_store_lc"].as_retriever(search_kwargs={"k": 3}); logging.info("[INIT] Retriever prêt."); logging.info("--- Fin Initialisation Globale IA ---")
    except Exception as e: logging.critical(f"[INIT - ERREUR FATALE]: {e}", exc_info=True); ia_components = {"embeddings": None, "chroma_client": None, "vector_store_lc": None, "retriever": None}


    # --- Logique Chainlit ---

    @cl.on_chat_start
    async def start_chat():
        """Initialisation session chat -> Agent avec TOUS les outils actifs (Web, Img, Sys/Notes, RAG)."""
        logging.info("\n--- Nouvelle Session Chat (Phase 10 v5.0 - Agent FINAL avec Outil RAG) ---")
        # Vérification cruciale que le retriever (dépendant de l'init globale) est prêt
        if not ia_components.get("retriever") or not ia_components.get("vector_store_lc"):
             logging.error("[Session Start] Erreur critique: Composants IA (Retriever/VectorStore) non initialisés.")
             await cl.Message(content="Erreur critique: Composants IA RAG non initialisés. Vérifiez logs serveur.").send(); return

        try:
            llm = ChatOllama( model=OLLAMA_MODEL, base_url=OLLAMA_BASE_URL, temperature=0.2, request_timeout=300.0)
            logging.info("[Session Start] LLM pour Agent initialisé.")

            # --- AGENT SETUP : Activation de TOUS les outils ---
            logging.info("[Session Start] Configuration de l'Agent ReAct...")
            # 1. Définir TOUS les outils avec leurs fonctions implémentées
            search_tool = DuckDuckGoSearchRun(name="Recherche Web DuckDuckGo")
            image_tool = Tool( name="Generateur Image Stable Diffusion", func=generate_image_tool, description="INDISPENSABLE pour créer/dessiner/générer une image. Input: description textuelle." )
            list_notes_tool = Tool( name="Lister Notes Personnelles", func=list_notes_safe, description="Voir la liste des fichiers notes existants." )
            save_note_tool = Tool( name="Sauvegarder Note Personnelle", func=save_note_safe, description="Sauver/écraser une note. Input: 'nom_fichier.txt, contenu'." )
            read_note_tool = Tool( name="Lire Note Personnelle", func=read_note_safe, description="Lire le contenu d'une note. Input: nom_du_fichier.txt." )
            # <<< DÉFINITION OUTIL RAG (Phase 10) >>>
            rag_tool = Tool(
                name="Recherche Document Personnel", # Nom clair pour l'agent
                func=run_rag_retriever, # Utilise la fonction wrapper implémentée ci-dessus
                # Description TRÈS IMPORTANTE et directive pour guider l'agent :
                description=(
                    "INDISPENSABLE pour répondre aux questions en utilisant les informations spécifiques contenues dans les documents PDF, TXT, DOCX fournis par l'utilisateur (fichiers chargés dans la base RAG). "
                    "Utilise cet outil EN PRIORITÉ ABSOLUE si la question concerne des informations personnelles, des projets spécifiques (comme 'Aladin', 'Sinbad'), des détails techniques mentionnés dans les documents chargés, "
                    "ou si l'utilisateur demande explicitement de chercher dans 'mes fichiers', 'mes documents', 'la base de connaissances', ou un document précis ('dans le fichier infos_techniques...'). "
                    "Ne PAS utiliser cet outil pour des connaissances générales ou des informations non présentes dans les documents chargés."
                )
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

            # 4. AgentExecutor (utilise la liste complète `active_tools`)
            agent_executor = AgentExecutor(
                agent=agent,
                tools=active_tools, # Fournit tous les outils actifs à l'Executor
                verbose=True, # Garder True pour débogage
                handle_parsing_errors=True, # Garder True pour robustesse
                max_iterations=10 # Garder une limite
            )
            cl.user_session.set("agent_executor", agent_executor)
            logging.info(f"[Session Start] Agent Executor final ({len(active_tools)} outils) initialisé.")
            # --- FIN AGENT SETUP ---

            # Stocker retriever/vector store (pour upload et outil RAG)
            cl.user_session.set("vector_store_lc", ia_components["vector_store_lc"])
            cl.user_session.set("retriever", ia_components["retriever"]) # Assurer que le retriever est aussi dans la session
            logging.info("[Session Start] VectorStore et Retriever stockés pour session.")


            # Inputs & Upload (inchangé)
            logging.info("[Session Start] Configuration UI: Inputs Texte/Audio/Upload.")
            await cl.ChatSettings( inputs=[ cl.TextInput(id="text_input", label="...", initial=""), cl.AudioInput(id="audio_input", label="...") ] ).send()
            cl.set_config(enable_file_upload=True, file_upload_accept=UPLOAD_ACCEPTED_TYPES, max_files=UPLOAD_MAX_FILES, max_size_mb=UPLOAD_MAX_MB)

            await cl.Message( content=f"Bonjour Halim-IA ! Assistant prêt (v5.0 Final avec tous les outils, y compris RAG).", author="Assistant Khaldounia" ).send()
            logging.info("--- Session Chat Prête (Agent FINAL) ---")

        except Exception as e: error_msg = f"ERREUR CRITIQUE @on_chat_start: {e}"; logging.critical(error_msg, exc_info=True); await cl.Message(content=f"Erreur démarrage session: {e}").send()


    # --- process_and_respond (inchangé - utilise l'agent executor qui a maintenant accès à RAG Tool) ---
    # ... (Coller ici la fonction process_and_respond complète de Phase 9 v5.0) ...
    async def process_and_respond(input_content_sanitized: str): agent_executor = cl.user_session.get("agent_executor"); if not agent_executor: logging.error("[Process] Agent Executor non trouvé."); await cl.Message(content="Erreur: Agent non initialisé.").send(); return; msg = cl.Message(content="", author="Assistant Khaldounia", prompt=input_content_sanitized); await msg.send(); final_answer_text = ""; image_to_display = None; try: logging.info(f"[Run] Appel Agent Executor: '{input_content_sanitized[:50]}...'"); response = await agent_executor.ainvoke({"input": input_content_sanitized}, config={"callbacks": [cl.LangchainCallbackHandler()]}); final_answer_text = response.get("output", "Pas de réponse pertinente."); logging.info(f"[Run] Réponse brute Agent: '{final_answer_text[:100]}...'"); if isinstance(final_answer_text, str) and final_answer_text.startswith("IMAGE_GENEREE:"): try: relative_image_path = final_answer_text.split(":", 1)[1].strip(); image_path_in_container = Path("/app") / relative_image_path; if image_path_in_container.is_file(): image_to_display = cl.Image(path=str(image_path_in_container), name=os.path.basename(relative_image_path), display="inline"); final_answer_text = f"Voici l'image demandée."; logging.info(f"[Run] Image détectée: {relative_image_path}"); else: logging.error(f"[Run] Fichier image non trouvé: {image_path_in_container}"); final_answer_text += f" (Erreur: fichier {relative_image_path} introuvable)"; except Exception as img_err: logging.error(f"[Run] Erreur prépa image: {img_err}", exc_info=True); final_answer_text += " (Erreur affichage image)"; await msg.update(content=final_answer_text); logging.info(f"[Run] Réponse Agent affichée: '{final_answer_text[:100]}...'"); if image_to_display: await cl.Message(content="", elements=[image_to_display], author="Image Générée", parent_id=msg.id, indent=1).send(); logging.info("[Run] Génération audio XTTS..."); try: audio_bytes = text_to_speech(final_answer_text); if audio_bytes: await cl.Message(content="", elements=[cl.Audio(name="response.wav", content=audio_bytes, auto_play=True)], author="Audio Khaldounia", parent_id=msg.id, indent=1).send(); logging.info("[Run] Audio envoyé."); else: logging.warning("[Run] Echec génération audio interne (après retries)."); await cl.Message(content="(Erreur interne génération audio)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as tts_err: logging.error(f"[Run] Échec final appel TTS: {tts_err}", exc_info=True); await cl.Message(content=f"(Erreur communication service audio.)", author="Alerte Système", parent_id=msg.id, indent=1).send(); except Exception as e: error_message = f"ERREUR agent execution: {e}"; logging.error(f"[Run] {error_message}", exc_info=True); await msg.update(content="Désolé, erreur interne pendant la réflexion."); logging.info("--- Fin Traitement Input via Agent ---")


    # --- on_message (inchangé) ---
    @cl.on_message
    async def on_text_message(message: cl.Message):
        # ... (coller ici on_text_message complet de Phase 9) ...
        logging.info(f"\n--- Msg Texte UI Reçu ---"); logging.info(f"Input brut: '{message.content[:60]}...'"); try: sanitized_input = sanitize_input(message.content); logging.info(f"Input (Sanitized): '{sanitized_input[:60]}...'"); await process_and_respond(sanitized_input); except Exception as e: logging.error(f"Erreur avant/pendant appel process_and_respond pour texte: {e}", exc_info=True); await cl.Message(content="Erreur interne traitement message texte.").send()

    # --- on_audio_end (inchangé) ---
    @cl.on_audio_end
    async def on_audio_end(audio: cl.Audio):
        # ... (coller ici on_audio_end complet de Phase 9) ...
        logging.info(f"\n--- Fichier Audio Reçu ---"); logging.info(f"Taille: {len(audio.content)} bytes, Type: {audio.mime}"); transcript_msg = cl.Message(content="*Transcription audio en cours...*", author="Système", disable_feedback=True); await transcript_msg.send(); transcribed_text_sanitized = None; try: transcribed_text_raw = transcribe_audio(audio.content); if transcribed_text_raw is not None and transcribed_text_raw != "": transcribed_text_sanitized = sanitize_input(transcribed_text_raw); logging.info(f"Texte transcrit (Sanitized): '{transcribed_text_sanitized[:60]}...'"); await transcript_msg.update(content=f"Vous avez dit : \"{transcribed_text_sanitized}\""); await process_and_respond(transcribed_text_sanitized); else: logging.warning("[STT] Transcription vide ou None."); await transcript_msg.update(content="Transcription vide ou aucun son détecté."); except Exception as e: logging.error(f"[STT/Audio End] Échec final transcription/traitement: {e}", exc_info=True); await transcript_msg.update(content="Échec transcription/traitement après tentatives."); logging.info("--- Fin Traitement Audio ---")


    # --- on_file_upload (inchangé - Logique d'indexation RAG de Phase 9) ---
    # ... (Coller ici la fonction @cl.on_file_upload complète de Phase 9 v5.0) ...
    UPLOAD_ACCEPTED_TYPES = ["text/plain", "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"]; UPLOAD_MAX_FILES = 5; UPLOAD_MAX_MB = 100; @cl.on_file_upload(accept=UPLOAD_ACCEPTED_TYPES, max_files=UPLOAD_MAX_FILES, max_size_mb=UPLOAD_MAX_MB)
    async def on_file_upload(files: list[cl.File]): logging.info(f"\n--- Début Traitement Upload RAG pour {len(files)} fichier(s) ---"); if not files: return; await cl.Message(content=f"Réception de {len(files)} fichier(s). Préparation...").send(); vector_store_lc = cl.user_session.get("vector_store_lc"); if not vector_store_lc: logging.error("[RAG Upload] VectorStore non trouvé."); await cl.Message(content="Erreur: Base RAG non prête.").send(); return; text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200); successful_files = []; failed_files_details = {};
    for file in files: processing_msg = cl.Message(content=f"Traitement `{file.name}`...", author="Système RAG"); await processing_msg.send(); temp_file_path = None;
    try: temp_file_path = UPLOAD_TEMP_DIR / f"{int(time.time())}_{file.name}"; UPLOAD_TEMP_DIR.mkdir(parents=True, exist_ok=True); await cl.make_async(temp_file_path.write_bytes)(file.content); logging.info(f"[RAG Upload] Temp file sauvé: {temp_file_path}"); file_ext = os.path.splitext(file.name)[1].lower(); loader = None; logging.info(f"[RAG Upload] Détection type '{file_ext}' pour {file.name}");
    if file_ext == ".pdf": loader = PyPDFLoader(str(temp_file_path))
    elif file_ext == ".txt": loader = TextLoader(str(temp_file_path), encoding='utf-8')
    elif file_ext == ".docx": loader = UnstructuredFileLoader(str(temp_file_path))
    else: raise ValueError(f"Format fichier non supporté: {file_ext}");
    logging.info(f"[RAG Upload] Load {file.name}..."); documents = await cl.make_async(loader.load)(); logging.info(f"[RAG Upload] Doc chargé ({len(documents)} partie(s))."); if not documents: raise ValueError("Contenu non extrait."); logging.info(f"[RAG Upload] Split {file.name}..."); chunks = await cl.make_async(text_splitter.split_documents)(documents); logging.info(f"[RAG Upload] Chunks: {len(chunks)}"); if not chunks: raise ValueError("Aucun chunk créé."); logging.info(f"[RAG Upload] Add {len(chunks)} chunks '{file.name}' to Chroma..."); await cl.make_async(vector_store_lc.add_documents)(chunks); logging.info(f"[RAG Upload] Chunks ajoutés."); await processing_msg.update(content=f"`{file.name}` ajouté ({len(chunks)} chunks)."); successful_files.append(file.name);
    except Exception as e: error_text = f"Erreur `{file.name}`: {str(e)}"; logging.error(f"[RAG Upload] {error_text}", exc_info=True); await processing_msg.update(content=error_text); failed_files_details[file.name] = str(e);
    finally: if temp_file_path and await cl.make_async(temp_file_path.exists)(): try: await cl.make_async(temp_file_path.unlink)(); logging.info(f"[RAG Upload] Temp file deleted: {temp_file_path}"); except Exception as del_e: logging.error(f"[RAG Upload] Erreur suppression temp file: {del_e}");
    summary = "--- Résultat Indexation RAG ---\n"; summary += f"✅ Succès: {', '.join(successful_files)}\n" if successful_files else ""; summary += "❌ Échecs:\n" + "".join([f"  - `{n}`: {e}\n" for n, e in failed_files_details.items()]) if failed_files_details else ""; await cl.Message(content=summary).send(); logging.info(f"--- Fin Traitement Fichiers --- Succès: {len(successful_files)}, Échecs: {len(failed_files_details)}")

    ```

* **Action :** Sauvegardez ce fichier `app.py` final (v5.0). Il représente l'état complet de l'application avec tous les outils actifs.

---

### Étape 10.2 : Redémarrer l'Environnement pour Activer l'Outil RAG

* **[⚠️⚠️⚠️] [⏱️⏱️]**
* **Contexte et Objectif :** Appliquer la mise à jour finale de `app.py` pour que l'agent puisse utiliser l'outil RAG.
* **Prérequis :** Être dans `~/projet-khaldounia/assistant-core`.
* **Action :**
    1.  Arrêter les services :
        ```bash
        docker compose down
        ```
    2.  Reconstruire `assistant_app` et démarrer tous les services (toujours 6) :
        ```bash
        docker compose up -d --build
        ```
* **Attente :** Attendez que les 6 conteneurs soient `Up (healthy)` (`docker ps`).

---

### Étape 10.3 : Tester l'Intégration Complète RAG/Agent

* **[⚠️⚠️⚠️] [⏱️⏱️]** (Nécessite des documents indexés et des questions spécifiques)
* **Contexte et Objectif :** Vérifier que l'agent choisit et utilise correctement l'outil "Recherche Document Personnel" lorsque la question posée concerne le contenu des documents chargés en Phase 9.
* **Prérequis :** Les 6 conteneurs sont `Up (healthy)`. Des documents pertinents ont été chargés avec succès via l'upload Chainlit en Phase 9 (ou maintenant).
* **Action :**
    1.  Si vous ne l'avez pas fait ou si vous avez redémarré sans volumes persistants (peu probable avec `docker compose down` simple), **chargez à nouveau vos documents de test** (ex: `infos_techniques_khaldounia.txt`, `reunion_projets_2025.docx`) via l'upload Chainlit (Étape 9.4). Assurez-vous que l'indexation réussit.
    2.  **Suivez attentivement les logs de l'agent :** `docker logs -f assistant_app`.
    3.  **Dans Chainlit, posez une question dont la réponse se trouve EXCLUSIVEMENT dans un des documents chargés :**
        * Exemple : "Quel est le nom exact du modèle d'embedding mentionné dans les infos techniques ?"
        * Exemple : "Peux-tu me rappeler la date de la réunion concernant le projet Aladin ?"
        * Exemple : "Donne-moi un résumé du projet Sinbad basé sur mes documents."
    4.  **Observez les logs `assistant_app` (`verbose=True`) :**
        * L'agent doit "penser" et **choisir** `Action: Recherche Document Personnel`. La description forte ajoutée à l'outil devrait l'y inciter fortement pour ce type de question.
        * L'`Action Input:` doit être la query (la question ou une reformulation).
        * Vous devriez voir les logs de la fonction `run_rag_retriever` : `[RAG Tool] Outil RAG demandé...`, `[RAG Tool] Invocation du retriever...`, `[RAG Tool] X document(s) trouvé(s)...`.
        * L'agent reçoit le texte formaté des documents comme `Observation:`.
        * L'agent utilise cette observation pour formuler sa `Final Answer:`.
    5.  **Vérifiez la réponse dans Chainlit :** Elle doit être **correcte et basée sur le contenu de vos documents**, suivie de l'audio.
    6.  **Contre-Tests (Vérifier la bonne sélection d'outil) :**
        * Question Web ("Météo Épinay-sur-Seine ?") -> Doit utiliser `Recherche Web DuckDuckGo`.
        * Question Image ("Dessine un robot") -> Doit utiliser `Generateur Image Stable Diffusion`.
        * Question Notes ("Liste mes notes") -> Doit utiliser `Lister Notes Personnelles`.
        * Question Générale ("Explique la photosynthèse") -> Ne devrait idéalement PAS utiliser l'outil RAG (sauf si vous avez chargé un doc dessus), mais plutôt répondre directement ou utiliser le Web.
* **En Cas de Problème :**
    * **Agent n'utilise JAMAIS l'outil RAG (préfère WebSearch ou dit "je ne sais pas") :**
        * Logs `assistant_app` -> "Thought". L'agent ignore-t-il l'outil ? La description de l'outil `Recherche Document Personnel` dans `app.py` est-elle assez claire et directive ? Essayez de la renforcer (ex: "UTILISE CET OUTIL EN PREMIER POUR TOUTE QUESTION SUR..."). Le LLM (Mistral 7B) a-t-il du mal à suivre l'instruction de priorité ?
        * Le contenu du document est-il assez distinctif pour que le retriever le trouve pertinent pour la question posée ?
    * **Agent choisit l'outil RAG, mais échoue ou dit "Aucun document pertinent" :**
        * Logs `assistant_app` : Erreur dans `run_rag_retriever` (`[ERREUR] RAG Tool...`) ? Erreur retournée par le retriever (connexion ChromaDB KO ? Base vide ?). Vérifiez état/logs `chromadb`.
        * Si "Aucun document pertinent", l'indexation s'est-elle bien passée (Phase 9.4) ? La question est-elle assez proche du contenu indexé ? Le modèle d'embedding (`all-MiniLM-L6-v2`) est-il adapté ? Essayez une question plus directe ou des mots-clés présents dans le document.
    * **Agent utilise RAG, récupère le bon texte (visible dans Observation log), mais donne une mauvaise réponse finale :** Problème de synthèse/raisonnement du LLM lui-même à partir du contexte fourni. Peut arriver avec des modèles plus petits comme Mistral 7B.
* **Attente et Transition :** Confirmez que l'agent utilise correctement l'outil RAG pour répondre aux questions basées sur vos documents.

---
### **Conclusion Finale de l'Installation de Base (Révisée v5.0)**

Félicitations ! Si vous avez atteint ce point et que les tests de la Phase 10 sont concluants (en particulier l'utilisation correcte de l'outil RAG), vous disposez maintenant d'une instance **complète, fonctionnelle, et robuste** de l'assistant Khaldounia. Il intègre toutes les capacités prévues :
* LLM (Ollama)
* TTS (XTTS)
* STT (Whisper)
* RAG (ChromaDB + Upload + Outil Agent)
* Recherche Web (Outil Agent)
* Génération d'Image (Stable Diffusion + Outil Agent)
* Actions Système/Notes Sécurisées (Outil Agent)

Le plan d'installation principal est terminé. Les Annexes suivantes fournissent des informations complémentaires pour le débogage, la maintenance et la validation.

---

### **Étape 10.4: Sauvegarde Finale (Fortement Recommandé)**

* **[⚠️⚠️] [⏱️]**
* **Contexte et Objectif :** Créer une sauvegarde de l'état fonctionnel complet du projet (code, configuration). C'est la sauvegarde la plus importante.
* **Action (Choisir une méthode) :**
    * **Méthode 1 : Copie Simple**
        * `docker compose down`
        * `cp -r ~/projet-khaldounia ~/projet-khaldounia-backup-phase10-finale`
        * `docker compose up -d`
    * **Méthode 2 : Commit Git (Préférable)**
        * `git add .`
        * `git commit -m "Fin de la Phase 10 (v5.0) - Installation complète fonctionnelle (Agent Web/Img/Sys/RAG)"`
        * Envisagez d'ajouter un tag Git : `git tag v1.0`
* **Attente et Transition :** Sauvegarde effectuée. Prêt pour les Annexes.


Fin de la Phase 10 (Révisée v5.0 - Finale).



## Annexe A : Stratégie de Vérification et Débogage (Révisée v5.0)

**Objectif :** Fournir une méthode structurée et hiérarchisée pour identifier et résoudre les problèmes potentiels lors de l'installation ou de l'utilisation de Khaldounia, optimisée pour l'interaction avec une IA guide.

**Philosophie : Diagnostiquer d'abord, corriger ensuite.** Évitez les modifications à l'aveugle.

**Méthode Recommandée en cas d'Erreur :**

**1. Isoler et Observer :**
    * **Quand ?** À quelle **Phase** et **Étape** exacte l'erreur s'est-elle produite ?
    * **Quoi ?** Quelle était la **dernière commande** exécutée ou l'**action** effectuée dans Chainlit ?
    * **Symptôme ?** Quel est le **comportement exact** observé ? (Message d'erreur, service qui s'arrête, réponse incorrecte, lenteur extrême...)
    * **Quel(s) service(s) suspect(s) ?**
        * Vérifiez l'état de **tous** les conteneurs : `docker ps -a` (notez les statuts : `Up (healthy)`, `Up (unhealthy)`, `Up (health: starting)`, `Restarting`, `Exited`).

**2. Capturer les Informations Essentielles (CRUCIAL) :**
    * **Message d'Erreur EXACT :** Copiez **intégralement** le message d'erreur affiché dans le terminal ou dans l'interface Chainlit.
    * **Logs Pertinents :** Obtenez les logs **récents et pertinents** du ou des conteneurs suspects. Utilisez principalement :
        * `docker logs [nom_du_conteneur]` (pour voir les erreurs depuis le début si besoin)
        * `docker logs --tail 100 [nom_du_conteneur]` (pour les 100 dernières lignes - souvent suffisant)
        * `docker logs -f [nom_du_conteneur]` (pour suivre les logs en direct pendant que vous reproduisez l'erreur)
        * **Conteneurs Clés à Logger :** Souvent `assistant_app` en premier, puis le service avec lequel il interagit (ex: `ollama`, `stable_diffusion_webui`, `xtts_server`, `whisper_server`, `chromadb`).

**3. Communiquer avec l'IA Guide (Diagnostic Structuré) :**
    * **Fournir les Infos :** Utilisez le **Modèle de Rapport d'Erreur** (ci-dessous) pour transmettre clairement les informations collectées (Phase, Étape, Action, Erreur, Logs) à votre IA guide.
    * **Suivre le Diagnostic Hiérarchique (demandé à l'IA) :** L'IA doit vous guider à travers ces niveaux :
        * **➡️ Niveau 1 : État et Logs des Services (Le plus important !)**
            * L'IA demandera/analysera `docker ps -a` et les `docker logs` du/des conteneur(s) suspect(s).
            * **Cherchez :** Messages `ERROR`, `CRITICAL`, `FATAL`, `Traceback` (erreurs Python), `Permission denied`, `OutOfMemoryError (OOM)`, `Connection refused`, `Timeout`.
            * Si un service est `unhealthy`, l'IA demandera : `docker inspect --format='{{json .State.Health}}' [nom_conteneur]` pour voir la cause de l'échec du healthcheck.
        * **➡️ Niveau 2 : Connectivité et API**
            * Si les services tournent mais l'erreur persiste (ex: "Erreur communication service audio").
            * L'IA demandera d'exécuter des tests `curl` ciblés (voir Phase 4.3bis, Annexe F) pour vérifier si les APIs répondent correctement depuis WSL.
            * L'IA pourra demander d'exécuter le script de validation (Annexe E) : `./validate_khaldounia.sh`.
        * **➡️ Niveau 3 : Ressources Système**
            * Si des lenteurs extrêmes, des crashs OOM, ou des erreurs GPU sont suspectés.
            * L'IA demandera la sortie de `nvidia-smi` (exécuté **dans WSL**) pour vérifier l'utilisation/disponibilité de la VRAM.
            * L'IA pourra demander la sortie de `free -h` ou `docker stats` pour vérifier l'utilisation de la RAM système/conteneurs.
        * **➡️ Niveau 4 : Fichiers, Volumes et Permissions**
            * Si une erreur suggère un fichier manquant, un chemin incorrect, ou un problème de droits.
            * L'IA demandera de vérifier l'existence/contenu/permissions de fichiers/dossiers **à l'intérieur** d'un conteneur via : `docker exec -it [nom_conteneur] ls -l [chemin_interne_au_conteneur]`.
            * Exemples : `docker exec xtts_server ls -l /app/speakers`, `docker exec stable_diffusion_webui ls -l /config/stable-diffusion/Stable-diffusion`, `docker exec assistant_app ls -ld /app/user_notes`.
        * **➡️ Niveau 5 : Diagnostic Agent LLM (si applicable)**
            * Si l'agent ne choisit pas le bon outil, boucle, ou donne des réponses incohérentes.
            * L'IA demandera d'analyser attentivement les logs `assistant_app` (avec `verbose=True`) pour voir la séquence `Thought:`, `Action:`, `Action Input:`, `Observation:`.
            * L'IA pourra suggérer de vérifier/modifier les descriptions des `Tool` dans `app.py` ou de reformuler le prompt utilisateur.
        * **➡️ Niveau 6 : Solutions Ciblées et Reconstruction**
            * **Seulement après diagnostic,** l'IA proposera une correction spécifique (modification `docker-compose.yml`, code `app.py`, commande `docker exec` pour corriger quelque chose dans un conteneur, etc.).
            * La **reconstruction** (`docker compose down && docker compose up -d --build`) peut être suggérée si une modification de configuration ou de code l'exige.
            * Le **nettoyage Docker plus invasif** (voir section 7 ci-dessous) ne doit être suggéré qu'en dernier recours et avec des avertissements clairs.

**4. Appliquer et Itérer :**
    * Appliquez **une seule suggestion de correction** à la fois.
    * **Retestez** l'étape ou la fonctionnalité qui posait problème.
    * **Rapportez le résultat** à l'IA (succès ou nouvelle erreur/logs) via le modèle ci-dessous. Si une nouvelle erreur apparaît, recommencez le cycle de diagnostic.

**5. Recherche Externe Ciblée (si IA bloquée) :**
    * Si une erreur très spécifique (ex: un message d'erreur précis d'une bibliothèque ou d'un service) n'est pas résolue après diagnostic, l'IA peut **vous suggérer** de rechercher ce message exact sur des sites comme GitHub Issues (du projet concerné: Ollama, Langchain, Chainlit, A1111-WebUI, etc.) ou Stack Overflow, en précisant les versions logicielles utilisées.

**6. Modèle de Rapport d'Erreur (À fournir à l'IA Guide) :**

```text
-----------------------------------
RAPPORT D'ERREUR - Projet Khaldounia
-----------------------------------
1.  **Phase et Étape :** [Ex: Phase 7, Étape 7.5]
2.  **Dernière Action Effectuée :** [Ex: J'ai demandé "Dessine un chat astronaute" dans Chainlit après avoir ajouté le modèle SD]
3.  **Comportement Observé / Erreur Exacte :**
    [Copiez ici le message d'erreur complet de Chainlit ou du terminal. Décrivez le comportement.]
    [Ex: L'agent a choisi l'outil image, mais après un long moment, Chainlit affiche "Désolé, erreur interne pendant la réflexion."]

4.  **Résultat `docker ps -a` :**
    ```
    [Collez la sortie de docker ps -a ici]
    ```
    *(Indiquez si des services sont 'unhealthy', 'Restarting' ou 'Exited')*

5.  **Logs Pertinents (Utilisez `--tail 100` ou plus si nécessaire) :**
    * **Logs `assistant_app` :**
      ```
      [Collez les logs pertinents de assistant_app ici, notamment les étapes de l'agent et les éventuels Tracebacks Python]
      ```
    * **Logs [Conteneur Suspect N°1, ex: `stable_diffusion_webui`] :**
      ```
      [Collez les logs pertinents ici. Cherchez OOM, Error, Cannot load...]
      ```
    * **Logs [Conteneur Suspect N°2, ex: `ollama` si erreur LLM] :**
      ```
      [Collez les logs pertinents ici]
      ```
    *(Ajoutez d'autres logs si pertinent)*

6.  **Résultat Tests Annexes (si demandés par l'IA ou pertinents) :**
    * Résultat `curl [URL]` : [Succès / Échec + Erreur Curl]
    * Résultat `./validate_khaldounia.sh` : [OK / Erreur + Message]
    * Résultat `nvidia-smi` (dans WSL) : [OK / Erreur / Utilisation VRAM]
    * Résultat `docker exec ... ls -l ...` : [OK / Fichier manquant / Permission denied]

7.  **Question / Aide Demandée :** [Ex: L'agent semble appeler SD, mais j'ai une erreur OOM dans les logs SD. Dois-je commenter la section deploy: GPU de XTTS dans docker-compose.yml et redémarrer ?]
-----------------------------------
```

**7. Commandes de Nettoyage Docker (Utilisation Prudente et sur Suggestion) :**
    * Ces commandes peuvent aider à résoudre des problèmes de cache corrompu, d'images obsolètes, ou à repartir d'un état propre, mais **certaines suppriment des données !** Ne les utilisez que si l'IA guide le suggère après diagnostic et que vous comprenez les conséquences.

    * **🟢 Nettoyage Léger (Peu risqué) :**
        * `docker container prune` : Supprime tous les conteneurs **arrêtés**. Libère de l'espace disque.
        * `docker image prune` : Supprime les images "dangling" (non utilisées par des conteneurs tagués). Libère de l'espace disque.
        * `docker builder prune` : Vide le cache de build Docker. Le prochain `docker compose build` sera plus long.

    * **🟠 Suppression Ciblée (Risque Moyen - Perte de temps/téléchargement) :**
        * `docker image rm [image:tag]` : Supprime une image spécifique (ex: `docker image rm lscr.io/linuxserver/stable-diffusion-webui:amd64-1.9.3`). Forcera un nouveau téléchargement si l'image est requise.
        * `docker compose restart [nom_service]` : Redémarre un service spécifique sans arrêter les autres. Utile pour appliquer un petit changement de config interne parfois.

    * **🔴 Suppression de Données (Risque Élevé - Perte de Données !) :**
        * `docker volume rm [nom_volume_specifique]` : **SUPPRIME DÉFINITIVEMENT** un volume nommé et tout son contenu (ex: `docker volume rm khaldounia_ollama_data` supprime tous vos modèles LLM). À n'utiliser que pour une réinitialisation complète d'un service.
        * `docker compose down -v` : Arrête et supprime les conteneurs du projet ET **SUPPRIME DÉFINITIVEMENT les volumes nommés associés** listés dans `docker-compose.yml` (modèles Ollama, base RAG ChromaDB, historique chat, config/modèles/sorties SD...). **Vous perdrez toutes ces données !**
        * `docker compose down --rmi all` : Comme `down -v`, mais supprime **AUSSI les images Docker** utilisées. Encore plus destructeur et long à récupérer.
        * `docker system prune -a --volumes` : **TRÈS DANGEREUX.** Supprime tous les conteneurs arrêtés, réseaux non utilisés, images dangling, cache de build ET **TOUS les volumes non utilisés par un conteneur démarré**. À éviter sauf si vous savez exactement ce que vous faites.

    * **Validation :** Confirmez toujours avec l'IA les conséquences avant d'exécuter une commande 🟠 ou 🔴.

---

Fin de l'Annexe A (Révisée v5.0).



## Annexe B : Notes sur la Sauvegarde (Révisée v5.0 - .gitignore)

**Objectif :** Fournir des stratégies claires et pratiques pour sauvegarder les différents composants de votre projet Khaldounia, en distinguant le code/configuration des données volumineuses gérées par Docker.

**Pourquoi Sauvegarder ?** Pour pouvoir revenir à un état fonctionnel après une mauvaise manipulation, une mise à jour ratée, une corruption de données, ou un problème matériel.

---

**1. Quoi Sauvegarder ? (Distinction Importante)**

Il y a deux catégories principales d'éléments à considérer :

* **A. Code Source et Fichiers de Configuration :**
    * **Contenu :** L'intégralité du dossier `~/projet-khaldounia/` sur votre système WSL. Cela inclut :
        * `assistant-core/docker-compose.yml` (le cœur de l'orchestration)
        * `assistant-core/app/Dockerfile` (instructions de build de l'app)
        * `assistant-core/app/requirements.txt` (dépendances Python)
        * `assistant-core/app/app.py` (le code de votre assistant)
        * `assistant-core/app/.dockerignore`
        * `assistant-core/validate_khaldounia.sh` (script de validation)
        * `tts-data/speakers/speaker_ref.wav` (votre fichier vocal source)
        * Potentiellement d'autres fichiers que vous ajouteriez (scripts, notes de dev...).
    * **Importance :** Contient toute la logique et la configuration que VOUS avez créée ou personnalisée. Relativement léger.
    * **Méthode de Sauvegarde Idéale :** **Git** (voir section 3).

* **B. Données Persistantes dans les Volumes Docker Nommés :**
    * **Contenu :** Ces données sont gérées par Docker et ne se trouvent PAS directement dans `~/projet-khaldounia/`. Elles résident dans des emplacements gérés par Docker sur votre système WSL. Les volumes concernés sont :
        * `khaldounia_ollama_data`: Contient les modèles LLM téléchargés par Ollama (potentiellement des dizaines de Go).
        * `khaldounia_chroma_data`: Contient la base de données vectorielle avec les embeddings de vos documents RAG (peut grossir avec le nombre de documents).
        * `khaldounia_xtts_cache`: Cache des modèles TTS.
        * `khaldounia_xtts_speakers`: Contient `speaker_ref.wav` (copié via `docker cp`).
        * `embedding_cache`: Cache des modèles SentenceTransformer.
        * `khaldounia_chat_history`: Historique des conversations (si activé).
        * `khaldounia_sd_config`: Configuration de l'UI Stable Diffusion, extensions installées.
        * `khaldounia_sd_models`: Modèles Checkpoint, VAE, LoRA, etc., copiés via `docker cp` ou téléchargés via l'UI SD (potentiellement des dizaines/centaines de Go).
        * `khaldounia_sd_outputs`: Images générées par SD (si sauvegardées dans le volume par défaut).
    * **Importance :** Contient les "grosses données" et l'état généré/appris. Peut être très volumineux.
    * **Méthode de Sauvegarde :** Plus complexe. Soit via des commandes Docker spécifiques pour sauvegarder/restaurer des volumes (voir documentation Docker avancée), soit en sauvegardant les *sources* originales de ces données (voir ci-dessous) et en les réimportant après restauration du code/config.

* **C. Données Sources Externes (À conserver séparément) :**
    * Les **fichiers originaux** des documents que vous avez chargés pour le RAG (PDF, TXT, DOCX...).
    * Les **fichiers `.safetensors`** (ou autres) des modèles Stable Diffusion que vous avez téléchargés.
    * Une **liste des modèles Ollama** que vous utilisez (`docker exec ollama ollama list`).
    * Ce **fichier Plan d'Installation** lui-même !

---

**2. Stratégie de Sauvegarde Simple (Focus Code/Config)**

Cette méthode sauvegarde principalement votre travail de configuration et de codage. Les grosses données (modèles, DB RAG) devront être re-téléchargées ou ré-indexées après restauration.

1.  **Arrêter Proprement les Services :**
    ```bash
    cd ~/projet-khaldounia/assistant-core
    docker compose down
    ```
2.  **Sauvegarder le Dossier Projet :**
    * **Option 1 (Copie Simple) :** Copiez le dossier entier vers un emplacement sûr (autre disque, clé USB, service Cloud synchronisé avec WSL...).
        ```bash
        # Exemple: copie vers un dossier backups dans votre home WSL
        cp -r ~/projet-khaldounia ~/backups/khaldounia_$(date +%Y%m%d_%H%M%S)
        ```
    * **Option 2 (Archive Tar) :** Crée une archive compressée.
        ```bash
        tar czvf ~/khaldounia_backup_$(date +%Y%m%d).tar.gz -C ~ projet-khaldounia
        ```
        *(L'archive `khaldounia_backup_YYYYMMDD.tar.gz` sera dans votre dossier personnel WSL. Copiez-la en lieu sûr.)*
3.  **Sauvegarder les Données Sources Externes (Catégorie C) :** Assurez-vous d'avoir des copies de vos documents RAG originaux et de vos modèles SD téléchargés ailleurs.
4.  **(Optionnel mais Recommandé) Redémarrer les services :**
    ```bash
    cd ~/projet-khaldounia/assistant-core
    docker compose up -d
    ```

---

**3. Stratégie de Sauvegarde Recommandée : Git pour Code/Config**

Git est l'outil idéal pour suivre les modifications de votre code (`app.py`) et de vos fichiers de configuration (`docker-compose.yml`, `Dockerfile`, etc.). Il ne sauvegarde PAS les gros fichiers de données ou les volumes Docker.

1.  **Initialiser Git (une seule fois) :**
    * Placez-vous à la racine du projet : `cd ~/projet-khaldounia`
    * Initialisez le dépôt : `git init`
2.  **Créer/Utiliser un Fichier `.gitignore` :**
    * Créez un fichier nommé `.gitignore` à la racine (`~/projet-khaldounia/.gitignore`).
    * Collez le contenu suivant (adapté de Annexe B v4.2 et Phase 3.3) pour exclure les fichiers inutiles ou générés :
        ```gitignore
        # Fichier .gitignore pour Projet Khaldounia

        # Fichier de configuration potentiellement sensible (si vous en créez un)
        .env

        # Dossiers de données/cache gérés principalement par volumes Docker ou générés
        # (On ne veut pas commiter des Go de modèles ou d'images dans Git)
        /assistant-core/app/embedding_cache/
        /assistant-core/app/generated_images/
        /assistant-core/app/temp_uploads/
        /assistant-core/app/chat_history/
        # /assistant-core/app/user_notes/ # Décommentez si vous NE VOULEZ PAS versionner les notes

        # Fichiers spécifiques IDE/OS
        .vscode/
        *.code-workspace
        .DS_Store
        Thumbs.db

        # Logs et caches Python
        *.log
        *.pyc
        *.pyo
        *.pyd
        __pycache__/
        .pytest_cache/
        .mypy_cache/
        *.egg-info/

        # Environnements virtuels locaux
        .venv/
        venv/
        env/

        # Archives de backup locales
        *.tar.gz
        *.zip
        ```
    * Sauvegardez le fichier `.gitignore`.
3.  **Effectuer des Commits Régulièrement :**
    * Après chaque modification significative ou à la fin de chaque phase réussie :
        ```bash
        # Toujours depuis ~/projet-khaldounia
        git add .                     # Ajoute tous les nouveaux fichiers/modifications (respecte .gitignore)
        git commit -m "Description significative du changement (ex: Fin Phase 8 - Outils Notes OK)"
        ```
4.  **Sauvegarde Externe du Dépôt Git :** Le dépôt Git local (`.git`) est dans `~/projet-khaldounia`. Vous pouvez :
    * Sauvegarder ce dossier comme décrit en section 2.
    * **(Idéal) Pousser (push) votre dépôt vers un service distant** comme GitHub, GitLab, Bitbucket (nécessite la création d'un compte et d'un dépôt distant).

---

**4. Restauration (Simplifiée - Depuis Sauvegarde Code/Config)**

Cette procédure restaure votre code et votre configuration, mais nécessite de réimporter les données volumineuses.

1.  **Arrêter les services Docker (si actifs) :**
    ```bash
    # Placez-vous dans l'ancien dossier s'il existe encore
    cd ~/projet-khaldounia/assistant-core
    docker compose down
    ```
2.  **Supprimer l'Ancien Dossier (si restauration complète) :**
    `rm -rf ~/projet-khaldounia`
3.  **Restaurer le Dossier Projet :**
    * Depuis une copie : `cp -r ~/backups/khaldounia_backup_YYYYMMDD /home/votrenomwsl/projet-khaldounia`
    * Depuis une archive : `tar xzvf ~/khaldounia_backup_YYYYMMDD.tar.gz -C ~`
    * Depuis Git : `git clone VOTRE_URL_DE_DEPOT_DISTANT projet-khaldounia` ou récupérez une version précédente avec `git checkout [commit_hash]`.
4.  **Se Placer dans le Dossier Restauré :** `cd ~/projet-khaldounia/assistant-core`
5.  **Reconstruire et Lancer les Services :**
    ```bash
    # Construit les images (notamment assistant_app) et crée les volumes nommés (vides)
    docker compose up -d --build
    ```
    *(Attendez que tous les services deviennent `(healthy)`)*
6.  **Réimporter les Données Manquantes :**
    * **Voix XTTS :** Recopiez `speaker_ref.wav` via `docker cp` (Étape 4.2 bis).
    * **Modèles SD :** Recopiez vos fichiers `.safetensors` via `docker cp` dans `stable_diffusion_webui:/config/stable-diffusion/Stable-diffusion/` PUIS **sélectionnez le modèle dans l'UI Web SD** (Étape 7.6).
    * **Modèles Ollama :** Téléchargez-les à nouveau via `docker exec ollama ollama pull nom_modele:tag` (Annexe C).
    * **Documents RAG :** Réuploadez vos fichiers via l'interface Chainlit (Phase 9).

---

Fin de l'Annexe B (Révisée v5.0).



## Annexe C : Notes sur la Gestion des Modèles Ollama (Révisée v5.0)

**Objectif :** Fournir les commandes essentielles pour gérer les modèles de langage (LLM) utilisés par le service Ollama qui s'exécute dans son propre conteneur Docker.

**Contexte :** Ollama télécharge et stocke les modèles dans le volume Docker nommé `khaldounia_ollama_data`, qui est monté sur `/root/.ollama` à l'intérieur du conteneur `ollama`. Les commandes suivantes permettent d'interagir avec Ollama *via* le conteneur en cours d'exécution.

---

**Commandes Utiles (à exécuter dans votre terminal WSL) :**

* **1. Lister les Modèles Déjà Téléchargés :**
    * Permet de voir quels modèles sont présents dans le volume `khaldounia_ollama_data` et leur taille.
    * **Commande :**
        ```bash
        docker exec -it ollama ollama list
        ```
    * `-it` : Permet une interaction (même si `list` n'est pas interactif, c'est une bonne habitude pour `docker exec`).
    * `ollama` : Nom du conteneur Docker.
    * `ollama list` : Commande exécutée à l'intérieur du conteneur.

* **2. Télécharger un Nouveau Modèle (ou Mettre à Jour) :**
    * Permet de télécharger un modèle depuis la bibliothèque Ollama (ex: [https://ollama.com/library](https://ollama.com/library)). Si le tag existe déjà localement, il sera mis à jour si une nouvelle version est disponible sur le hub.
    * **Commande (Adaptez `nom_modele:tag`) :**
        ```bash
        # Exemple pour télécharger le modèle Llama 3 8B instruct
        docker exec -it ollama ollama pull llama3:8b-instruct

        # Exemple pour télécharger Mistral (dernière version)
        docker exec -it ollama ollama pull mistral:latest

        # Exemple pour un modèle spécifique de la communauté (nécessite nom complet)
        # docker exec -it ollama ollama pull username/modelname:tag
        ```
    * **Note :** Le téléchargement se fait *depuis le conteneur Docker* et peut prendre du temps et de l'espace disque considérable (plusieurs Go par modèle). Assurez-vous que le conteneur a accès à Internet.

* **3. Supprimer un Modèle Téléchargé Localement :**
    * Permet de libérer de l'espace disque en supprimant un modèle du volume `khaldounia_ollama_data`.
    * **Commande (Adaptez `nom_modele:tag`) :**
        ```bash
        # Exemple pour supprimer la version 8B de Llama 3
        docker exec -it ollama ollama rm llama3:8b-instruct
        ```

* **4. Voir les Informations Détaillées d'un Modèle (y compris son Modelfile) :**
    * Utile pour comprendre comment un modèle a été configuré (template de prompt, paramètres...).
    * **Commande (Adaptez `nom_modele:tag`) :**
        ```bash
        # Afficher les informations générales
        docker exec -it ollama ollama show nom_modele:tag

        # Afficher spécifiquement le Modelfile utilisé
        docker exec -it ollama ollama show nom_modele:tag --modelfile
        ```

* **5. Consulter les Logs du Service Ollama :**
    * Très utile si Ollama ne démarre pas, si un modèle ne charge pas, ou si vous suspectez des erreurs GPU au niveau d'Ollama.
    * **Commande :**
        ```bash
        docker logs ollama
        # ou pour suivre en direct :
        docker logs -f ollama
        ```

* **6. Changer le Modèle Utilisé par Défaut par l'Assistant (`app.py`) :**
    * L'assistant utilise le modèle défini par la variable d'environnement `OLLAMA_MODEL` dans `docker-compose.yml`.
    * **Action :**
        1.  Ouvrez le fichier `~/projet-khaldounia/assistant-core/docker-compose.yml`.
        2.  Localisez la section `environment:` du service `assistant_app`.
        3.  Modifiez la ligne `- OLLAMA_MODEL=...` avec le nouveau `nom_modele:tag` souhaité (ex: `- OLLAMA_MODEL=llama3:8b-instruct`). Assurez-vous que ce modèle a bien été téléchargé (voir commande 1 ou 2).
        4.  Sauvegardez le fichier `docker-compose.yml`.
        5.  Redémarrez les services pour appliquer le changement :
            ```bash
            # Depuis ~/projet-khaldounia/assistant-core
            docker compose down
            docker compose up -d # Pas besoin de --build si seul l'env var change
            ```

---

**Rappel Important :** Les modèles sont stockés dans le volume Docker `khaldounia_ollama_data`. Si vous supprimez ce volume (ex: via `docker compose down -v`), tous les modèles téléchargés seront perdus et devront être re-téléchargés.

---

Fin de l'Annexe C (Révisée v5.0).



## Annexe D : Notes sur les Mises à Jour Générales (Révisée v5.0)

**Objectif :** Fournir des directives et des avertissements sur la manière de mettre à jour les différents composants du système Khaldounia (images Docker, dépendances Python, modèles IA) **de manière contrôlée et prudente**, en minimisant les risques de rupture de compatibilité.

**Philosophie : Stabilité d'abord.** Ce plan utilise des **versions figées (pinned)** pour tous les composants critiques (`docker-compose.yml`, `requirements.txt`). C'est intentionnel pour garantir que l'installation initiale fonctionne de manière reproductible. Les mises à jour apportent de nouvelles fonctionnalités et des correctifs, mais peuvent aussi introduire des changements incompatibles ("breaking changes"). **Abordez toute mise à jour avec prudence.**

**Règle d'Or : SAUVEGARDEZ TOUJOURS AVANT DE METTRE À JOUR !** (Voir Annexe B). Utilisez Git si possible pour pouvoir revenir facilement en arrière.

---

**1. Mise à Jour des Images Docker (Services dans `docker-compose.yml`)**

* **Contexte :** Concerne les images comme `ollama/ollama`, `ghcr.io/coqui-ai/tts`, `chromadb/chroma`, `whisper-asr-webservice`, `linuxserver/stable-diffusion-webui`.
* **Méthode Contrôlée (Recommandée) :**
    1.  **Recherche :** Identifiez un nouveau **tag de version spécifique** (ex: `ollama/ollama:0.1.45` au lieu de `0.1.41`). Évitez `:latest` qui n'est pas stable.
    2.  **Documentation :** Consultez si possible les notes de version ("release notes", "changelog") de la nouvelle image sur Docker Hub, GitHub ou le site du projet. Cherchez des changements majeurs (API modifiée, variables d'environnement changées, chemins de volume modifiés...).
    3.  **🆘 SAUVEGARDE :** Sauvegardez votre projet (`~/projet-khaldounia` et idéalement les sources de données externes) ou faites un commit Git.
    4.  **Modification `docker-compose.yml` :** Mettez à jour le tag de l'image pour le service concerné.
        ```yaml
        # Exemple AVANT:
        # services:
        #   ollama:
        #     image: ollama/ollama:0.1.41
        # Exemple APRÈS:
        services:
          ollama:
            image: ollama/ollama:0.1.45 # <== Mise à jour du tag
        ```
    5.  **Arrêt des Services :**
        ```bash
        cd ~/projet-khaldounia/assistant-core
        docker compose down
        ```
    6.  **(Optionnel mais Propre) Suppression Ancienne Image :**
        ```bash
        docker image rm ollama/ollama:0.1.41 # Adaptez nom et ancien tag
        ```
    7.  **Téléchargement Nouvelle Image :**
        ```bash
        docker pull ollama/ollama:0.1.45 # Adaptez nom et nouveau tag
        ```
        *(Cela permet de détecter les problèmes de téléchargement avant de lancer `up`)*
    8.  **Redémarrage des Services :**
        ```bash
        docker compose up -d
        ```
        *(Pas besoin de `--build` si seul le tag de l'image a changé)*
    9.  **🧪 TESTS APPROFONDIS :**
        * Exécutez le script de validation (`./validate_khaldounia.sh`, Annexe E).
        * Testez **spécifiquement** les fonctionnalités liées au service mis à jour. Si vous avez mis à jour Ollama, testez plusieurs interactions LLM. Si vous avez mis à jour SD, testez la génération d'images.
        * Surveillez les logs (`docker logs [nom_service]`) pour toute nouvelle erreur ou avertissement.
    10. **Retour Arrière (si problème) :** Si la nouvelle version pose problème, modifiez à nouveau `docker-compose.yml` pour remettre l'ancien tag, puis faites `docker compose down && docker compose up -d`. Restaurez votre sauvegarde si nécessaire.

---

**2. Mise à Jour des Dépendances Python (`requirements.txt`)**

* **[⚠️⚠️⚠️] HAUT RISQUE - À FAIRE AVEC EXTRÊME PRUDENCE !**
* **Contexte :** Concerne les bibliothèques listées dans `assistant-core/app/requirements.txt` (ex: `chainlit`, `langchain`, `chromadb-client`, etc.).
* **Risques :** Les mises à jour, en particulier pour des bibliothèques complexes comme Langchain et Chainlit, introduisent **très fréquemment** des changements d'API, des fonctions renommées ou supprimées, ou des changements de comportement qui **nécessiteront des modifications du code `app.py`**. Une simple mise à jour des numéros de version suivie d'un rebuild **échouera souvent** ou provoquera des erreurs d'exécution.
* **Méthode Contrôlée (Si Absolument Nécessaire) :**
    1.  **Recherche & Compatibilité :** Identifiez les nouvelles versions souhaitées (`==X.Y.Z`). **CRUCIAL :** Lisez attentivement les notes de version de **chaque** bibliothèque mise à jour, en particulier Langchain et Chainlit. Vérifiez les incompatibilités connues entre les versions (ex: Langchain vX nécessite Pydantic vY). C'est l'étape la plus difficile.
    2.  **🆘 SAUVEGARDE / GIT :** Faites un commit Git **avant** toute modification de `requirements.txt`. C'est indispensable pour pouvoir revenir en arrière facilement.
    3.  **Modification `requirements.txt` :** Mettez à jour les numéros de version souhaités.
        ```diff
        # Exemple
        - langchain==0.2.1
        + langchain==0.3.0
        - chainlit==1.1.300
        + chainlit==1.2.0
        ```
    4.  **Arrêt des Services :**
        ```bash
        cd ~/projet-khaldounia/assistant-core
        docker compose down
        ```
    5.  **Reconstruction et Redémarrage :** L'option `--build` est **obligatoire** pour réinstaller les dépendances Python dans l'image `assistant_app`.
        ```bash
        docker compose up -d --build
        ```
    6.  **🧪 TESTS EXHAUSTIFS & DÉBOGAGE `app.py` :**
        * Vérifiez d'abord si `assistant_app` démarre (`docker ps`, `docker logs assistant_app`). Des erreurs d'import ou d'initialisation sont fréquentes après mise à jour.
        * Testez **chaque fonctionnalité** de l'assistant : STT, TTS, WebSearch, ImgGen, Notes, Upload RAG, Requêtes RAG.
        * **Attendez-vous à devoir modifier `app.py`** pour l'adapter aux nouvelles versions des bibliothèques (fonctions dépréciées, arguments modifiés, nouvelles méthodes...). Utilisez les messages d'erreur (`Traceback`) dans les logs `assistant_app` pour guider vos corrections.
    7.  **Retour Arrière (si échec ou trop complexe) :** Utilisez Git pour annuler les modifications de `requirements.txt` et `app.py` (`git checkout requirements.txt app.py`), puis faites `docker compose down && docker compose up -d --build` pour revenir à l'état précédent.

---

**3. Mise à Jour des Modèles IA (Ollama, XTTS, SD)**

* **Contexte :** Concerne les fichiers de modèles eux-mêmes (ex: nouveaux LLM, nouveaux checkpoints SD), pas les logiciels qui les exécutent.
* **Méthode :**
    * **Modèles Ollama :**
        * Utilisez les commandes `docker exec` (voir Annexe C) :
        ```bash
        # Télécharger un nouveau modèle/tag
        docker exec -it ollama ollama pull nouveau_modele:tag
        # Supprimer un ancien modèle si besoin
        docker exec -it ollama ollama rm ancien_modele:tag
        ```
        * Pour l'utiliser, mettez à jour `OLLAMA_MODEL` dans `docker-compose.yml` (service `assistant_app`) et redémarrez (`down` + `up -d`).
    * **Modèles XTTS :**
        * L'image utilisée (`ghcr.io/coqui-ai/tts:v0.22.0`) embarque un modèle par défaut. Pour utiliser un autre modèle XTTS (si disponible et compatible), il faudrait potentiellement :
            1. Télécharger les fichiers du nouveau modèle.
            2. Les copier (via `docker cp` ou un bind mount temporaire) dans un emplacement accessible par le conteneur (potentiellement dans le volume `khaldounia_xtts_cache`).
            3. Modifier la `command:` du service `xtts_server` dans `docker-compose.yml` pour pointer vers le nouveau `--model_name` ou `--model_path`.
            4. Redémarrer (`down` + `up -d`). **Vérifiez la compatibilité** du nouveau modèle avec la version de l'image `:tts`.
    * **Modèles Stable Diffusion (`.safetensors`, VAE, LoRA...) :**
        * Téléchargez les nouveaux fichiers modèles sur votre PC.
        * Copiez-les dans les sous-dossiers appropriés du volume `khaldounia_sd_models` via `docker cp` (voir Étape 7.6 pour les checkpoints, chemins similaires pour VAE, Lora, etc.) :
            * Checkpoints -> `stable_diffusion_webui:/config/stable-diffusion/Stable-diffusion/`
            * VAE -> `stable_diffusion_webui:/config/stable-diffusion/VAE/`
            * LoRA -> `stable_diffusion_webui:/config/stable-diffusion/Lora/`
            * Etc.
        * Allez dans l'UI Web SD (`http://127.0.0.1:7860`), utilisez les icônes de rafraîchissement (🔄) si nécessaire, et **sélectionnez** le nouveau modèle/VAE/LoRA que vous voulez utiliser. L'API utilisera les modèles sélectionnés dans l'UI.

**Conseil Général :** Mettez à jour **un seul composant majeur à la fois** (ex: juste une image Docker, ou juste les libs Python, ou juste un modèle IA) et **testez intensivement** avant de passer au suivant. Cela facilite l'identification de la source des problèmes.

---

Fin de l'Annexe D (Révisée v5.0).



## Annexe E : Script de Validation Amélioré (`validate_khaldounia.sh` v2.0)

**Objectif :** Fournir un script Bash pour exécuter rapidement une série de vérifications plus approfondies afin de valider l'état opérationnel des services Khaldounia après une installation ou un redémarrage. Cette version intègre des vérifications de l'état de santé Docker et de l'accès GPU.

**Utilisation :**

1.  **Créer ou Mettre à Jour le Script :**
    * Dans votre terminal WSL, placez-vous dans `~/projet-khaldounia/assistant-core`.
    * Si le fichier `validate_khaldounia.sh` existe déjà, ouvrez-le (`nano validate_khaldounia.sh`). Sinon, créez-le.
    * **Remplacez l'intégralité de son contenu** par le script Bash ci-dessous :

    ```bash
    #!/bin/bash

    # Script de validation amélioré pour les services Khaldounia v2.0
    # Vérifie: État conteneur, Santé Docker, Accès GPU (basique), APIs clés.

    # --- Configuration ---
    # Liste des conteneurs attendus (basée sur Phase 10 finale)
    EXPECTED_CONTAINERS=("ollama" "xtts_server" "chromadb" "whisper_server" "stable_diffusion_webui" "assistant_app")
    # Conteneurs censés avoir accès au GPU
    GPU_CONTAINERS=("ollama" "xtts_server" "stable_diffusion_webui") # Whisper est optionnel
    # URLs des services (depuis les ports mappés sur localhost)
    OLLAMA_API="http://localhost:11434"
    XTTS_API="http://localhost:8050"
    CHROMADB_API="http://localhost:8002"
    WHISPER_API="http://localhost:9000"
    SD_API="http://localhost:7860"
    CHAINLIT_UI="http://localhost:8001"
    # Timeouts pour les tests curl (en secondes)
    CURL_TIMEOUT=10
    CURL_TIMEOUT_SD=20 # SD API peut être plus lent

    # --- Couleurs & Helpers ---
    GREEN='\033[0;32m'
    RED='\033[0;31m'
    YELLOW='\033[1;33m'
    BLUE='\033[0;34m'
    NC='\033[0m' # No Color

    log_ok() { echo -e "${GREEN}[  OK  ]${NC} $1"; }
    log_err() { echo -e "${RED}[ERREUR]${NC} $1"; HAS_CRITICAL_ERROR=true; } # Marque qu'une erreur critique a eu lieu
    log_warn() { echo -e "${YELLOW}[ WARN ]${NC} $1"; }
    log_info() { echo -e "\n${BLUE}--- $1 ---${NC}"; }
    log_step() { echo -en "  -> $1... "; } # Pour afficher étape en cours avant résultat

    HAS_CRITICAL_ERROR=false # Flag pour état final

    # --- Début des Vérifications ---
    log_info "Validation Khaldounia v2.0 - État des Services"
    echo "Nombre de conteneurs attendus: ${#EXPECTED_CONTAINERS[@]}"

    # 1. Vérification État et Santé des Conteneurs
    log_info "1. Vérification État et Santé des Conteneurs Docker"
    ALL_HEALTHY=true
    for container in "${EXPECTED_CONTAINERS[@]}"; do
        log_step "Vérification de '$container'"
        # Vérifier si le conteneur existe et tourne
        if ! docker ps --format '{{.Names}}' --filter "name=^/${container}$" --filter "status=running" | grep -q "^${container}$"; then
            # Vérifier s'il existe mais est arrêté/autre statut
            if docker ps -a --format '{{.Names}}' --filter "name=^/${container}$" | grep -q "^${container}$"; then
                STATUS=$(docker ps -a --format '{{.Status}}' --filter "name=^/${container}$")
                echo -e "${RED}NON DÉMARRÉ (Status: $STATUS)${NC}"
                log_err "Conteneur '$container' existe mais n'est pas en cours d'exécution ('running'). Vérifiez 'docker logs $container'."
            else
                echo -e "${RED}INTROUVABLE${NC}"
                log_err "Conteneur '$container' est introuvable. Avez-vous lancé 'docker compose up -d' ?"
            fi
            ALL_HEALTHY=false
            continue # Passe au conteneur suivant si non démarré
        fi

        # Si démarré, vérifier l'état de santé (health status)
        HEALTH_STATUS=$(docker inspect --format='{{if .State.Health}}{{.State.Health.Status}}{{else}}no healthcheck{{end}}' "$container" 2>/dev/null)

        if [[ "$HEALTH_STATUS" == "healthy" ]]; then
            echo -e "${GREEN}running (healthy)${NC}"
        elif [[ "$HEALTH_STATUS" == "starting" ]]; then
            echo -e "${YELLOW}running (health: starting)${NC}"
            log_warn "Conteneur '$container' est encore en phase de démarrage (health: starting). Patientez ou vérifiez les logs si cela persiste."
            ALL_HEALTHY=false # On considère que tout n'est pas prêt
        elif [[ "$HEALTH_STATUS" == "unhealthy" ]]; then
            echo -e "${RED}running (unhealthy)${NC}"
            HEALTH_LOG=$(docker inspect --format='{{json .State.Health.Log}}' "$container" | tail -n 5) # Dernières lignes du log healthcheck
            log_err "Conteneur '$container' est 'unhealthy'. Dernière vérification échouée. Logs Healthcheck:\n$HEALTH_LOG\nVérifiez 'docker logs $container'."
            ALL_HEALTHY=false
        elif [[ "$HEALTH_STATUS" == "no healthcheck" ]]; then
             echo -e "${YELLOW}running (no healthcheck)${NC}"
             log_warn "Conteneur '$container' tourne mais n'a pas de healthcheck défini dans docker-compose.yml (ou Docker ne le voit pas)."
        else
            echo -e "${YELLOW}running (état santé inconnu: '$HEALTH_STATUS')${NC}"
            log_warn "Conteneur '$container' tourne, mais son état de santé est inconnu ou inattendu ('$HEALTH_STATUS')."
        fi
    done

    if [ "$ALL_HEALTHY" = false ]; then
        log_warn "Certains conteneurs ne sont pas encore 'healthy' ou ont échoué. Les tests API suivants peuvent échouer."
    else
        log_ok "Tous les conteneurs attendus sont démarrés et (au moins initialement) 'healthy'."
    fi

    # 2. Vérification Accès GPU (Basique)
    log_info "2. Vérification Accès GPU dans les Conteneurs Concernés"
    for container in "${GPU_CONTAINERS[@]}"; do
        log_step "Test 'nvidia-smi' dans '$container'"
        # Vérifie si le conteneur est bien en cours d'exécution avant de tenter exec
        if docker ps --format '{{.Names}}' --filter "name=^/${container}$" --filter "status=running" | grep -q "^${container}$"; then
            # Exécute nvidia-smi dans le conteneur, ignore la sortie standard, capture stderr et code retour
            NVIDIA_SMI_ERROR=$(docker exec "$container" nvidia-smi --query-gpu=name --format=csv,noheader 2>&1 >/dev/null)
            NVIDIA_SMI_RC=$?
            if [ $NVIDIA_SMI_RC -eq 0 ]; then
                echo -e "${GREEN}OK${NC}"
            else
                echo -e "${RED}ÉCHEC (Code: $NVIDIA_SMI_RC)${NC}"
                log_err "Échec exécution 'nvidia-smi' dans '$container'. Problème config GPU/Docker ou pilote ? Sortie d'erreur: $NVIDIA_SMI_ERROR"
            fi
        else
             echo -e "${YELLOW}SKIP (Conteneur non démarré)${NC}"
             log_warn "Impossible de tester GPU pour '$container' car il n'est pas démarré."
        fi
    done

    # 3. Tests API Essentiels
    log_info "3. Tests de Connectivité API Essentiels"

    log_step "Test API Ollama ($OLLAMA_API/)"
    if curl --fail --connect-timeout $CURL_TIMEOUT -s -o /dev/null "$OLLAMA_API/"; then log_ok "OK"; else log_err "ÉCHEC"; fi

    log_step "Test API ChromaDB ($CHROMADB_API/api/v1/heartbeat)"
    if curl --fail --connect-timeout $CURL_TIMEOUT -fsS "$CHROMADB_API/api/v1/heartbeat" | grep -q "heartbeat"; then log_ok "OK"; else log_err "ÉCHEC"; fi

    log_step "Test API Whisper ($WHISPER_API/health)"
    if curl --fail --connect-timeout $CURL_TIMEOUT -fsS -o /dev/null "$WHISPER_API/health"; then log_ok "OK"; else log_warn "ÉCHEC (Vérifiez si /health est le bon endpoint pour votre image Whisper)"; fi

    log_step "Test API XTTS ($XTTS_API/speakers)"
    if curl --fail --connect-timeout $CURL_TIMEOUT -fsS -o /dev/null "$XTTS_API/speakers"; then log_ok "OK"; else log_err "ÉCHEC (Vérifiez logs xtts_server, fichier speaker copié?)"; fi

    log_step "Test API Stable Diffusion ($SD_API/docs)"
    if curl --fail --connect-timeout $CURL_TIMEOUT_SD -fsS -o /dev/null "$SD_API/docs"; then log_ok "OK"; else log_warn "ÉCHEC (API non active (--api dans CLI_ARGS?) ou démarrage long?)"; fi

    log_step "Test UI Chainlit ($CHAINLIT_UI/)"
    if curl --fail --connect-timeout $CURL_TIMEOUT -fsS -o /dev/null "$CHAINLIT_UI/"; then log_ok "OK"; else log_err "ÉCHEC (Vérifiez logs assistant_app)"; fi

    # 4. Test Commande Interne Ollama
    log_info "4. Test Liste Modèles Ollama (via docker exec)"
    log_step "Exécution 'ollama list' dans conteneur 'ollama'"
    OLLAMA_LIST_OUTPUT=$(docker exec ollama ollama list 2>&1)
    OLLAMA_LIST_RC=$?
    if [ $OLLAMA_LIST_RC -eq 0 ]; then
        echo -e "${GREEN}OK${NC}"
        log_info "Modèles Ollama trouvés :"
        echo "$OLLAMA_LIST_OUTPUT" | sed 's/^/    /' # Indente la sortie
    else
        echo -e "${RED}ÉCHEC (Code: $OLLAMA_LIST_RC)${NC}"
        log_err "Échec exécution 'ollama list' dans le conteneur 'ollama'. Sortie: $OLLAMA_LIST_OUTPUT"
    fi

    # --- Conclusion ---
    echo -e "\n----------------------------------"
    if [ "$HAS_CRITICAL_ERROR" = true ]; then
        log_err "--- Validation terminée avec une ou plusieurs ERREURS critiques détectées ---"
        echo "Veuillez examiner les messages [ERREUR] ci-dessus et consulter les logs Docker."
        exit 1 # Termine avec un code d'erreur
    else
        log_ok "--- Validation terminée ---"
        if [ "$ALL_HEALTHY" = true ]; then
            log_ok "Tous les services semblent démarrés, sains et les API de base répondent."
            echo "Effectuez des tests fonctionnels via l'interface Chainlit pour une validation complète."
        else
            log_warn "Certains services ne sont pas encore 'healthy' ou des tests API non critiques ont échoué."
            log_warn "Vérifiez les messages [WARN] et les logs si des fonctionnalités semblent manquer."
        fi
        exit 0 # Termine avec succès (même avec des warnings)
    fi
    ```

2.  **Rendre le Script Exécutable :**
    * Si vous venez de créer le fichier ou de le modifier, assurez-vous qu'il est exécutable :
    ```bash
    chmod +x validate_khaldounia.sh
    ```

3.  **Lancer le Script (Après `docker compose up -d`) :**
    * Assurez-vous que vos conteneurs sont démarrés.
    * Exécutez le script :
    ```bash
    ./validate_khaldounia.sh
    ```

* **Interprétation des Résultats :**
    * `[  OK  ]` : Le test est réussi.
    * `[ WARN ]` : Le test a échoué mais n'est pas considéré comme critique, ou le service est encore en démarrage. Investiguez si la fonctionnalité associée pose problème.
    * `[ERREUR]` : Un test critique a échoué. Le script se terminera avec un code d'erreur 1. Vous **devez** investiguer ces erreurs en priorité en utilisant la stratégie de l'Annexe A.

---

Fin de l'Annexe E (Révisée v5.0). 
Ce script amélioré devrait vous aider à diagnostiquer plus rapidement l'état de votre installation.

D'accord, voici l'**Annexe F (Révisée v5.0)**, qui fournit un tableau de référence utile pour les services et leurs ports.

```markdown
---

## Annexe F : Tableau de Référence des Services et Ports (Révisée v5.0)

**Objectif :** Fournir un aperçu rapide des services Docker composant Khaldounia, leurs noms de conteneur, les ports mappés sur votre machine hôte (localhost), et leur rôle principal. Utile pour l'accès aux interfaces web (si disponibles) et pour les tests API (`curl`).

**Rappel :** Tous les ports sont volontairement exposés **uniquement sur `127.0.0.1` (localhost)** pour des raisons de sécurité. Vous ne pourrez y accéder que depuis votre machine Windows où tourne WSL2/Docker.

| Nom du Service (docker-compose) | Nom du Conteneur (`container_name`) | Mapping Port (`127.0.0.1:PORT_HOTE -> PORT_CONTENEUR`) | Accès Principal (Depuis Windows/WSL) | Rôle Principal                     | Notes                                                                 |
| :------------------------------ | :---------------------------------- | :----------------------------------------------------- | :----------------------------------- | :---------------------------------- | :-------------------------------------------------------------------- |
| `ollama`                        | `ollama`                            | `127.0.0.1:11434 -> 11434`                             | API: `http://127.0.0.1:11434`        | Service LLM (Modèle de langage)     | Utilisé par `assistant_app`. Pas d'UI Web par défaut.               |
| `xtts_server`                   | `xtts_server`                       | `127.0.0.1:8050 -> 8020`                             | API: `http://127.0.0.1:8050`        | Synthèse Vocale (Text-to-Speech)    | Utilisé par `assistant_app`. Pas d'UI Web fonctionnelle par défaut. |
| `chromadb`                      | `chromadb`                          | `127.0.0.1:8002 -> 8000`                             | API: `http://127.0.0.1:8002`        | Base de Données Vectorielle (RAG) | Utilisé par `assistant_app` pour RAG. Pas d'UI Web par défaut.     |
| `whisper_server`                | `whisper_server`                    | `127.0.0.1:9000 -> 9000`                             | API: `http://127.0.0.1:9000`        | Reconnaissance Vocale (Speech-to-Text)| Utilisé par `assistant_app`. Pas d'UI Web par défaut.               |
| `stable_diffusion_webui`        | `stable_diffusion_webui`            | `127.0.0.1:7860 -> 7860`                             | UI Web: `http://127.0.0.1:7860`<br>API: `http://127.0.0.1:7860` | Génération d'Images (Stable Diffusion)| API utilisée par `assistant_app`. UI Web A1111 accessible.        |
| `assistant_app`                 | `assistant_app`                     | `127.0.0.1:8001 -> 8000`                             | UI Web: `http://127.0.0.1:8001`        | Application Principale / Interface  | **C'est l'interface principale** pour interagir avec Khaldounia.    |

**Comment Utiliser ce Tableau :**

* **Accès aux Interfaces Web :** Pour les services qui ont une UI Web (`stable_diffusion_webui`, `assistant_app`), utilisez l'URL indiquée dans la colonne "Accès Principal" dans votre navigateur Windows.
* **Tests API (`curl`) :** Lorsque vous utilisez `curl` depuis votre terminal WSL pour tester une API (comme en Phase 4.3bis ou pour le débogage), utilisez l'URL indiquée dans la colonne "Accès Principal". Par exemple, pour tester ChromaDB : `curl http://127.0.0.1:8002/api/v1/heartbeat`.
* **Communication Inter-Conteneurs :** Le code dans `app.py` communique avec les autres services en utilisant leur **nom de service** et leur **port interne** (ex: `http://ollama:11434`, `http://xtts_server:8020`). Ces URLs sont configurées via les variables d'environnement dans la section `assistant_app` de `docker-compose.yml`. Vous n'utilisez PAS les ports hôtes (11434, 8050, etc.) pour la communication *entre* les conteneurs.

 

