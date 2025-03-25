FROM ubuntu:22.04

# Installation des dépendances
RUN apt-get update && apt-get install -y \
    python3.12 \
    python3-pip \
    ffmpeg \
    sox \
    libsox-fmt-all \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Configuration de l'environnement Python
RUN pip3 install --upgrade pip

# Création du répertoire de travail
WORKDIR /app

# Copie des fichiers de configuration
COPY requirements.txt .

# Installation des dépendances Python
RUN pip3 install -r requirements.txt

# Définition des variables d'environnement
ENV CUDA_VISIBLE_DEVICES=0
ENV TORCH_CUDNN_V8_API_ENABLED=1

# Commande par défaut
CMD ["python3", "src/core/main.py"]
