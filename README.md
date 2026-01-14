# SynthParam Inference : Inverse Audio Synthesis

![Python](https://img.shields.io/badge/Python-3.10-blue.svg?style=flat&logo=python)
![Domain](https://img.shields.io/badge/Domain-Audio_DSP_&_DL-purple.svg)
![Libs](https://img.shields.io/badge/Libs-Pyo-orange.svg)
![Status](https://img.shields.io/badge/Status-Dataset_Ready-green.svg?style=flat)


## Description du Projet

Ce projet explore le domaine de l'**Inverse Audio Synthesis** (Synthèse Inverse) et de l'**Estimation de Paramètres**.

Contrairement aux approches génératives générant de l'audio brut, l'objectif ici est de construire un modèle d'apprentissage profond (Deep Learning) capable de faire du **Reverse Engineering sonore**. Le modèle écoute un son synthétique et prédit les valeurs exactes des paramètres (*"boutons"*) du synthétiseur nécessaires pour le reproduire.

En résumé, le réseau de neurones agit comme une **oreille absolue pour synthétiseur**, capable d'identifier la structure spectrale et temporelle d'un son (*ex : Oscillateur Sawtooth à 440Hz, Filtre Passe-Bas à 2000Hz, Release long...*).

## Objectifs Techniques

En l'état, le projet se décompose en trois phases critiques :

- [x] **Data Generation** : Création d'un dataset massif de couples `(Audio, Paramètres)` via un moteur DSP programmable.
- [x] **Data Validation** : Mise en place de règles heuristiques pour garantir la cohérence physique des sons (éviter les silences ou les filtrages destructeurs).
- [ ] **Deep Learning** : Entraînement d'une architecture neuronale multi-tâches :
    * **Classification Head :** Pour les variables discrètes (Formes d'ondes, Types de filtres).
    * **Regression Head :** Pour les variables continues (Fréquences, ADSR, Q, Mix).

## Architecture du Dataset

La base de données actuelle est constituée de **10 000 échantillons** générés procéduralement.

### Moteur Audio
* **Librairie :** `Pyo`
* **Synthétiseur :** Architecture soustractive simpliste (Oscillateur + Noise -> Filtre Biquad -> Enveloppe ADSR).

### Stratégie de Génération
Pour éviter les biais d'apprentissage classiques, le script de génération (`pyo_tests.ipynb`) implémente des logiques avancées :
* **Sanity Checks :** Algorithmes vérifiant la relation entre la fréquence fondamentale et le `cutoff` du filtre.
    * *Exemple :* On fait en sorte que si un son généré avec un filtre Passe-Bas (LP), alors ce dernier doit être réglé plus bas que la fréquence de la note (on évite les fichiers silencieux).
* **Distributions Équilibrées :**
    * Fréquences générées sur une échelle logarithmique (perception humaine).
    * Variation des types de filtres (LowPass, HighPass, BandPass simple).
    * Gestion aléatoire mais contrôlée du bruit et de la résonance.

## Structure des Données

Chaque entrée du dataset est composée de :

*  **Input (X) :** Fichier `.wav` (Mono/Stéréo, 44.1kHz).
*  **Target (Y) :** Fichier de métadonnées `.csv` contenant :
    * `duree` (float : sec)
    * `osc_freq` (float : Hz)
    * `osc_type` (int : Sine, Saw, Square...)
    * `osc_vol` (float : 0 à 1)
    * `noise_vol` (float : 0 à 1)
    * `filter_type` (int)
    * `filter_cutoff` (float : Hz)
    * `filter_res` (float)
    * `env_attack`, `env_decay`, `env_sustain`, `env_release` (float : sec)
    * `master_vol` (float  : 0 à 1)

## Installation de l'environement

Il est nécessaire d'avoir Miniconda sur sa machine pour installer l'environement :

```bash
conda env create -f environment.yml
conda activate synth_project
