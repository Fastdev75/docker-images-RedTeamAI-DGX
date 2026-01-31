# 🛡️ DGX RedTeam Suites (H200 Edition)

> **Infrastructure de Pentest Offensif & Agent Autonome IA sur NVIDIA DGX H200.**

Ce dépôt contient les configurations Docker optimisées pour déployer deux environnements distincts :
1.  🤖 **AI-AGENT Edition :** Un environnement complet avec LLM (Ollama), GPU activé et Scripts d'Agent Autonome.
2.  ⚔️ **PURE ARSENAL Edition :** Une distribution Debian 12 "Clean" avec l'arsenal complet (Web/Infra) sans la couche IA.

---

## 📋 Architecture & Comparatif

| Feature | 🤖 **AI-AGENT Edition** | ⚔️ **PURE ARSENAL Edition** |
| :--- | :--- | :--- |
| **Cas d'usage** | **Agent Autonome**, RAG, Scripting IA, Tool Calling | **Pentest Manuel**, Infra, Web, Red Team classique |
| **Base OS** | `ollama/ollama:latest` (Ubuntu + NVIDIA Cuda) | `debian:bookworm` (Debian 12 Stable) |
| **Moteur IA** | **Ollama** (Pré-installé & Configuré) | **Aucun** (Système allégé) |
| **Utilisation GPU** | **100% (Inférence Qwen3-30B)** | Disponible (Hashcat/Cracking si besoin) |
| **Taille Image** | ~6-10 Go (sans les modèles) | ~3-4 Go |
| **Outils** | Full Stack (Go + Python + Metasploit) | Full Stack (Go + Python + Metasploit) |

```mermaid
graph TD
    User[Pentester / User] -->|Prompt/Cmd| Docker
    subgraph "NVIDIA DGX H200 Host"
        Docker[Docker Container]
        GPU[NVIDIA H200 GPU]
        
        subgraph "AI-AGENT Edition"
            Ollama[Ollama Server]
            Agent[Python Agent Script]
            Tools1[Nmap/Nuclei/Metasploit]
            Ollama <-->|Inference| GPU
            Agent <-->|Tool Calling| Tools1
            Agent <-->|JSON Decision| Ollama
        end

        subgraph "PURE ARSENAL Edition"
            Term[Bash Terminal]
            Tools2[Nmap/Nuclei/NetExec]
            Term --> Tools2
        end
    end
