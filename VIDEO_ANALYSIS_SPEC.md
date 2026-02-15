# 🎯 PIPELINE D'ANALYSE VIDÉO (TECHNICAL SPEC)

Ce document détaille le fonctionnement interne du système d'intelligence artificielle pour la détection des meilleurs moments dans les vidéos longues.

---

## 📋 VUE D'ENSEMBLE DU PROCESSUS

```
UPLOAD → PRÉ-TRAITEMENT → ANALYSE MULTIDIMENSIONNELLE → FUSION → SCORING
```

## 1️⃣ PHASE DE PRÉ-TRAITEMENT
- **Extraction des métadonnées** : Utilisation de `ffprobe` pour obtenir durée, bitrate, codec, fps.
- **Préparation des flux** : 
    - Extraction audio (PCM 16kHz).
    - Génération de proxy (480p).
    - Extraction d'images clés (1 fps).
    - Segmentation en chunks (30s) pour parallélisation.

## 2️⃣ ANALYSE AUDIO (MULTIPLE DÉTECTEURS)
- **Transcription (STT)** : Whisper (large-v3) avec word-timestamps.
- **Pics Sonores** : Calcul de l'énergie acoustique (pics locaux).
- **Classification** : Détection des rires, applaudissements, exclamations (YAMNet).
- **Analyse Tonale** : Détection de l'excitation et des émotions vocales.

## 3️⃣ ANALYSE VIDÉO (MULTIPLE DÉTECTEURS)
- **Changement de scènes** : PySceneDetect (ContentDetector).
- **Visages** : Suivi des intervenants clés et détection des gros plans.
- **OCR** : Détection du texte à l'écran (titres, slides).
- **Saliency** : Moments où l'attention visuelle est maximale.

## 4️⃣ ANALYSE SÉMANTIQUE (CONTENU)
- **Mots-clés** : Extraction des concepts clés (RAKE/YAKE).
- **Q&A** : Identification des échanges questions/réponses.
- **Émotions** : Fusion texte + audio pour détecter les pics émotionnels.

## 5️⃣ FUSION ET SCORING
- **Agrégation temporelle** : Regroupement (clusters) des détections proches.
- **Scoring composite** : Pondération selon le profil (Podcast, Conférence, Sport).
- **Ordonnancement** : Filtrage des doublons et classement par score final.

---

## 🚀 TECHNOLOGIES CLÉS
- **Vidéo/Audio** : FFmpeg, OpenCV, Librosa.
- **IA/ML** : Whisper, PyTorch, face_recognition.
- **Infrastructure** : BullMQ, Redis, Temporal.io.
