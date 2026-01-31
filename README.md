# 🛡️ DGX RedTeam Suites (H200 Edition)

> **Infrastructure de Pentest Offensif & Agent Autonome IA sur NVIDIA DGX H200.**

Ce dépôt centralise les configurations Docker optimisées pour déployer deux environnements de hacking distincts sur une infrastructure DGX :
1.  🤖 **AI-AGENT Edition :** Environnement "Cerveau" avec LLM (Ollama/Qwen), GPU activé et Scripts Python d'automatisation.
2.  ⚔️ **PURE ARSENAL Edition :** Environnement "Muscle" sous Debian 12, ultra-léger, contenant uniquement l'arsenal offensif.

---

## 📋 Architecture & Comparatif

| Caractéristique | 🤖 **AI-AGENT Edition** | ⚔️ **PURE ARSENAL Edition** |
| :--- | :--- | :--- |
| **Cas d'usage** | **Agent Autonome**, RAG, Tool Calling, Scripting IA | **Pentest Manuel**, Audit Infra/Web, Red Team |
| **Fichier Docker** | `Dockerfile.agent` | `Dockerfile.pure` |
| **Base OS** | `ollama/ollama:latest` (Ubuntu + CUDA) | `debian:bookworm` (Debian 12 Stable) |
| **Moteur IA** | **Ollama** (Pré-installé & Configuré) | **Aucun** (Système Clean) |
| **GPU H200** | **100% Utilisation** (Inférence Qwen3-30B) | Disponible (Hashcat/Cracking optionnel) |
| **Taille Image** | Lourde (~10 Go + Modèles) | Légère (~3 Go) |

### 🧠 Schéma de Fonctionnement

```mermaid
graph TD
    User[Pentester / User] -->|Prompt ou Commande| Docker
    subgraph "NVIDIA DGX H200 Host"
        Docker[Conteneur Docker]
        GPU[NVIDIA H200 GPU]
        
        subgraph "AI-AGENT Edition"
            Ollama[Svr Ollama]
            Agent[Script Python]
            Tools1[Nmap/Nuclei]
            Ollama <-->|Inference| GPU
            Agent <-->|Tool Calling| Tools1
            Agent <-->|Décision JSON| Ollama
        end

        subgraph "PURE ARSENAL Edition"
            Term[Bash Terminal]
            Tools2[Nmap/Nuclei/NetExec]
            Term --> Tools2
        end
    end

🚀 Pré-requis (Hôte DGX)
Avant de commencer, assurez-vous que les drivers NVIDIA sont opérationnels sur l'hôte :

Bash

# 1. Vérifier la détection du H200
nvidia-smi

# 2. Vérifier que Docker accède au GPU
docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi
1️⃣ Utilisation : L'Édition "AI-AGENT"
Cette version est conçue pour l'automatisation intelligente via web_agent.py et le modèle Qwen3-Coder.

🏗️ Build
Assurez-vous que le fichier Dockerfile.agent est à la racine.

Bash

# Construction de l'image
docker build -f Dockerfile.agent -t pentestia:agent .
⚡ Run (Lancement)
Nous lançons le conteneur en arrière-plan (-d) avec un volume persistant pour les modèles Ollama (pour éviter de retélécharger 20Go à chaque fois).

Bash

docker run -d \
  --name pentest-agent \
  --gpus=all \
  --network host \
  --restart unless-stopped \
  -v "$(pwd):/app" \
  -v ollama_storage:/root/.ollama \
  pentestia:agent
🧠 Configuration Initiale (Premier Lancement)
Une fois le conteneur démarré, installez le "Cerveau" :

Bash

# 1. Entrer dans le conteneur
docker exec -it pentest-agent bash

# 2. Télécharger le modèle optimisé H200
ollama pull qwen3-coder:30b

# 3. Lancer l'Agent Autonome
python3 web_agent.py
2️⃣ Utilisation : L'Édition "PURE ARSENAL"
Cette version est conçue pour les tests d'intrusion manuels, sans la surcharge de l'IA.

🏗️ Build
Assurez-vous que le fichier Dockerfile.pure est à la racine.

Bash

# Construction de l'image
docker build -f Dockerfile.pure -t pentestia:pure .
⚡ Run (Lancement)
Accès direct au terminal Debian avec tous les outils pré-chargés.

Bash

docker run -it \
  --name pentest-pure \
  --network host \
  -v "$(pwd):/app" \
  pentestia:pure
🧰 Liste des Outils Inclus
Les deux images contiennent l'intégralité de cet arsenal :

🌐 Web Recon & Analysis
ProjectDiscovery Stack : nuclei, httpx, subfinder, katana, naabu, dnsx, notify.

Tomnomnom Suite : gf (avec patterns), qsreplace, waybackurls.

Essential : gau, hakrawler, dalfox, gobuster, amass (v4), sqlmap, arjun, dirsearch, uro, tldextract.

Manual Tools : Findomain, Corsy.

💀 Infra & Active Directory
NetExec (nxc) : Audit AD & Network (remplace CrackMapExec).

Impacket / Certipy / BloodHound : Attaque Active Directory complète.

Exploitation : Metasploit Framework, Searchsploit.

Man-in-the-Middle : Responder, Mitm6.

Pivot : Ligolo-ng, Chisel, Proxychains4.

Enumeration : Kerbrute, Evil-WinRM.

🤖 IA & Scripting (Agent Edition Uniquement)
Ollama Client (API locale)

ChromaDB (Mémoire Vectorielle / RAG)

Pydantic (Validation stricte des sorties IA)

ReportLab / FPDF2 (Génération PDF)

Asyncio / Loguru (Haute performance Python)

❓ Troubleshooting
Erreur : externally-managed-environment (Python)

C'est normal sur Debian 12 / Ubuntu 24.04. Solution : N'utilisez pas pip install en root. Nos outils sont installés via pipx (isolés) et vos scripts Python doivent utiliser l'environnement virtuel pré-configuré : /opt/venv/bin/python.

Erreur : Ollama connection refused

Attendez 5 à 10 secondes après le démarrage du conteneur pentest-agent. L'initialisation du serveur GPU prend un court instant. Vérifiez avec docker logs pentest-agent.

Performances :

Sur un DGX H200, privilégiez toujours des modèles quantifiés larges (ex: qwen3-coder:30b ou llama3:70b). Les modèles 7B sous-utiliseraient massivement votre matériel.
