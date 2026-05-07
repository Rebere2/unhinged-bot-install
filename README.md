# Unhinged LLM — Installateur automatique

IA locale en une commande : **Ollama + Open WebUI** avec modèle et system prompt personnalisables.

Compatible **Arch Linux / CachyOS** et **Debian / Ubuntu / Proxmox VE**.

---

## Installation rapide

**Option simple (une ligne) :**
```bash
curl -fsSL https://raw.githubusercontent.com/Rebere2/unhinged-bot-install/main/install.sh | bash
```

**Option avec arguments (recommandé) :**
```bash
curl -fsSL https://raw.githubusercontent.com/Rebere2/unhinged-bot-install/main/install.sh -o install.sh
bash install.sh
# Ou avec options :
bash install.sh --cpu --model qwen2.5:3b
```

> Compatible bash et fish shell.

---

## Options

| Option | Description | Défaut |
|--------|-------------|--------|
| `--model <nom>` | Modèle Ollama à installer | `dolphin3:8b` |
| `--port <port>` | Port pour Open WebUI | `3000` |
| `--cpu` | Force le mode CPU (sans GPU) | auto-détecté |
| `--prompt <texte>` | System prompt personnalisé | (voir ci-dessous) |

---

## Exemples

```bash
# Installation par défaut (dolphin3:8b, GPU auto-détecté)
bash install.sh

# Serveur sans GPU, modèle léger
bash install.sh --cpu --model qwen2.5:3b

# Modèle différent sur port 8080
bash install.sh --model llama3.1:8b --port 8080

# Avec un system prompt personnalisé
bash install.sh --prompt "Tu es un assistant expert en développement Python et DevOps."
```

---

## Ce que fait le script

1. **Détecte l'OS** (Arch/CachyOS ou Debian/Proxmox)
2. **Détecte le GPU** (NVIDIA, ou bascule en CPU)
3. **Installe Docker** si absent
4. **Installe Ollama** si absent
5. **Configure Ollama** pour écouter sur toutes les interfaces (`0.0.0.0`)
6. **Télécharge le modèle** choisi
7. **Crée un modèle personnalisé** `assistant-local` avec votre system prompt
8. **Lance Open WebUI** avec volume persistant et redémarrage automatique

---

## Installation sur serveur Proxmox / VPS

Sur un serveur (Proxmox, VPS Debian...), lancez en root :

```bash
curl -fsSL https://raw.githubusercontent.com/Rebere2/unhinged-bot-install/main/install.sh -o install.sh
bash install.sh --cpu --model qwen2.5:3b --port 3000
```

> Sans GPU dédié, utilisez des modèles légers (3b ou 7b quantifiés Q4).
> Le modèle `qwen2.5:3b` est un bon compromis vitesse/qualité en CPU.

L'interface sera accessible à : `http://VOTRE_IP_SERVEUR:3000`

---

## Mise à jour de Open WebUI

```bash
docker pull ghcr.io/open-webui/open-webui:main
docker rm -f open-webui
bash install.sh
```

Vos conversations sont conservées dans le volume Docker `open-webui-data`.

---

## Commandes utiles post-installation

```bash
# Lister les modèles disponibles
ollama list

# Voir les modèles chargés en VRAM
ollama ps

# Logs de Open WebUI
docker logs -f open-webui

# Statut du service Ollama
systemctl status ollama          # si root/serveur
systemctl --user status ollama   # si utilisateur desktop

# Télécharger un nouveau modèle
ollama pull mistral:7b
```

---

## Tester en local (si Ollama/Docker déjà installés)

```bash
# Clone le dépôt
git clone https://github.com/Rebere2/unhinged-bot-install.git
cd unhinged-bot-install

# Lance le script — il détecte ce qui est déjà installé et ne réinstalle pas
bash install.sh
```

Le script est **idempotent** : il ne réinstalle pas ce qui est déjà présent.

---

## Structure du dépôt

```
.
└── install.sh    # Script principal
└── README.md     # Ce fichier
```

---

## Compatibilité testée

| Système | Statut |
|---------|--------|
| CachyOS (KDE, kernel v3/v4) | ✅ |
| Arch Linux | ✅ |
| Debian 12 (Bookworm) | ✅ |
| Proxmox VE 8+ | ✅ |
| Ubuntu 22.04 / 24.04 | ✅ |
