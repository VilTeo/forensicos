<div align="center">

# ForensicOS

**Plataforma de Forense Digital em 3 Camadas**

*Forensicamente sólida · IA opcional · Campo e Laboratório*

[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115+-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://typescriptlang.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?style=flat-square&logo=postgresql&logoColor=white)](https://postgresql.org)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docker.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)

</div>

---

## Visão Geral

ForensicOS é uma plataforma de forense digital projetada para peritos examinadores. Opera em duas modalidades — **Live USB em campo** e **webapp Docker em laboratório** — com uma arquitetura em três camadas que garante operação 100% funcional mesmo sem inteligência artificial.

```
┌─────────────────────────────────────────────────────────────┐
│  CAMADA 3 — IA (opcional, AI_ENABLED=true)                  │
│  Chat investigativo · RAG · NLP · Classificação · Resumos   │
├─────────────────────────────────────────────────────────────┤
│  CAMADA 2 — AUTOMAÇÃO (regras determinísticas)              │
│  Triagem heurística · NSRL · Regex PII · OCR · ExifTool     │
├─────────────────────────────────────────────────────────────┤
│  CAMADA 1 — CORE (sem IA, sem rede externa)                 │
│  Gestão de casos · Chain of custody · TSK · Busca · Laudos  │
└─────────────────────────────────────────────────────────────┘
```

> **Princípio fundamental:** A IA é um plugin, não uma dependência. O sistema é 100% funcional com `AI_ENABLED=false`.

---

## Funcionalidades

### Camada 1 — Core (MVP)

| Funcionalidade | Descrição |
|----------------|-----------|
| **Gestão de Casos** | Criar, atribuir e acompanhar casos com status, prioridade e equipe |
| **Upload de Evidências** | Ingestão segura com validação de magic bytes e detecção de zip bomb |
| **Chain of Custody** | Hash chain SHA-256 append-only com verificação criptográfica |
| **The Sleuth Kit (TSK)** | Análise de imagens de disco — extração de sistema de arquivos completo |
| **Timeline Forense** | Geração e visualização interativa de eventos por fonte e período |
| **Busca Full-Text** | PostgreSQL FTS em artefatos, casos e findings (Elasticsearch na Fase 2) |
| **Marcação de Artefatos** | Perito sinaliza artefatos como relevantes/irrelevantes com justificativa |
| **Findings** | Registro estruturado de descobertas com severidade e origem |
| **Relatórios PDF** | Templates Jinja2 com auto-preenchimento e exportação PDF/HTML |
| **Auth JWT + RBAC** | Roles: `admin`, `examiner`, `reviewer`, `viewer` |
| **Rate Limiting** | 5 logins/min · 3 registros/hora (Redis) |
| **Audit Logging** | Log imutável de todas as ações administrativas e operacionais |
| **YARA Scanner** | Regras customizáveis de detecção em todos os artefatos |

### Camada 2 — Automação

| Funcionalidade | Descrição |
|----------------|-----------|
| **Volatility 3** | Análise de dumps de RAM — processos, conexões, injeção de código |
| **Zeek** | Análise de PCAP — conn, DNS, HTTP, SSL, files logs estruturados |
| **ALEAPP / iLEAPP** | Artefatos Android e iOS — contatos, localização, chats, apps |
| **Plaso / log2timeline** | Timeline unificada de múltiplas fontes de evidência |
| **bulk_extractor** | Extração em larga escala sem montar sistema de arquivos |
| **Binwalk** | Análise de firmware e detecção de sistemas de arquivos embutidos |
| **ClamAV** | Escaneamento de malware com assinaturas atualizadas |
| **NSRL Matching** | Filtro de arquivos do SO conhecido — reduz ruído em ~70% |
| **Regex PII** | Detector de CPF, CNPJ, e-mail, cartão de crédito, carteiras cripto |
| **OCR (Tesseract)** | Extração de texto de imagens (pt-BR + en-US) |
| **ExifTool** | Metadados de fotos — GPS, dispositivo, data, sinais de edição |
| **Triagem Heurística** | Score 0.0–1.0 por artefato baseado em YARA, NSRL, regex e contexto |
| **Relatório Auto-preenchido** | Seções técnicas geradas automaticamente com dados do banco |

### Camada 3 — IA (Opcional)

> Ativada com `AI_ENABLED=true` via `docker-compose.ai.yml`

| Funcionalidade | Descrição |
|----------------|-----------|
| **Chat Investigativo** | Agente conversacional com tool-use sobre artefatos do caso |
| **Busca Semântica (RAG)** | ChromaDB + embeddings — busca por significado, não por palavra exata |
| **NLP — Entidades** | Extração de pessoas, organizações, locais, datas, IPs |
| **Classificação de Artefatos** | Categorização automática: malware, PII, comunicação, documento |
| **Narrativa de Laudo** | Rascunho gerado por IA com confirmação humana obrigatória |
| **Múltiplos Provedores LLM** | Ollama (local) · Anthropic Claude · OpenAI — troca via configuração |

### Funcionalidades Enterprise (Fase 4)

| Funcionalidade | Descrição |
|----------------|-----------|
| **Velociraptor** | Coleta remota de evidências em endpoints (IR) |
| **VirusTotal API** | Enriquecimento de hashes, URLs e domínios com threat intel |
| **Correlação Cruzada** | Detecta conexões entre evidências de casos diferentes |
| **Assinatura ICP-Brasil** | Laudos com validade jurídica (PAdES + carimbo de tempo) |
| **Exportação CASE** | Formato JSON-LD para interoperabilidade com UFED, Cellebrite, Axiom |

---

## Modalidades de Implantação

### Live USB — Coleta em Campo

```
┌──────────────────────────────────────────┐
│           ForensicOS Live USB            │
│                                          │
│  [1] Imagem de Disco (dcfldd + SHA256)   │
│  [2] Captura de RAM (AVML / LiME)        │
│  [3] Extração Mobile (ADB / lockdown)    │
│  [4] Triagem Rápida (YARA / strings)     │
│  [5] Upload para Laboratório             │
│                                          │
│  Write-blocker via udev — read-only      │
└──────────────────────────────────────────┘
```

- Bloqueio automático de escrita em todos os dispositivos (udev)
- Hash SHA-256 calculado durante a imagem e re-verificado no laboratório
- TUI de terminal acessível para peritos não-técnicos
- Funcionamento offline com sincronização posterior

### Webapp Docker — Laboratório

```bash
# Sem IA (padrão)
docker compose up

# Com IA (Ollama local + ChromaDB)
docker compose -f docker-compose.yml -f docker-compose.ai.yml up
```

---

## Arquitetura Técnica

### Stack

| Camada | Tecnologia |
|--------|-----------|
| Backend | Python 3.12+ · FastAPI 0.115+ · SQLAlchemy 2 · Alembic |
| Frontend | React 18 · TypeScript 5 · Tailwind CSS 4 · Vite |
| Banco de Dados | PostgreSQL 16 |
| Busca | PostgreSQL FTS (Fase 1) → Elasticsearch 8 (Fase 2) |
| Vector Store | ChromaDB 0.5+ (Fase 3, apenas com IA) |
| Task Queue | Celery 5 + Redis 7 |
| Storage | MinIO (S3-compatível) |
| Infraestrutura | Docker + Docker Compose |
| Auth | JWT + RBAC customizado |
| Code Quality | ruff (linter + formatter) |
| Testes | pytest + pytest-asyncio + httpx |

### Abstrações (Dependency Inversion)

```python
# Troca de implementação via variável de ambiente, sem mudar código
class ForensicToolIntegration(ABC): ...   # TSK, Volatility, Zeek...
class LLMProvider(ABC): ...               # Ollama <-> Anthropic <-> OpenAI
class ObjectStorage(ABC): ...             # MinIO <-> S3 <-> filesystem
class VectorStore(ABC): ...               # ChromaDB <-> Pinecone <-> Weaviate
```

### Utilitários de Segurança

```python
ForensicHasher     # MD5 + SHA1 + SHA256 em streaming
SafeCommandRunner  # shell=False, timeout, sandbox firejail opcional
PathSanitizer      # prevencao de directory traversal
ArtifactFactory    # normaliza output de qualquer ferramenta
```

### Pipeline de Processamento

```
Evidencia uploaded
       |
       v
[CORE]       -> TSK, YARA, busca FTS, timeline
       |
       v  (se AUTOMATION_ENABLED)
[AUTOMACAO]  -> Volatility, Zeek, NSRL, regex, OCR, heuristica
       |
       v  (se AI_ENABLED e LLM acessivel)
[IA]         -> embeddings, classificacao, entidades, relevancia
```

Cada etapa é independente — falha em uma não interrompe as demais.

---

## Segurança

| Controle | Implementação |
|----------|---------------|
| **Chain of Custody** | SHA-256 de cada entrada, verificável do início ao fim |
| **Upload seguro** | Magic bytes, zip bomb detection, quarentena |
| **CLI segura** | `SafeCommandRunner` com `shell=False` e timeout em todos os comandos |
| **Rate limiting** | Proteção contra brute force e enumeração (Redis) |
| **Audit log** | Rastreabilidade completa — append-only, sem deleção |
| **IA com confirmação** | Findings de IA requerem aprovação do perito antes do laudo |
| **ICP-Brasil** | Assinatura PAdES com carimbo de tempo para validade jurídica |

---

## Critério de Aceitação do MVP

Validado contra o **[NIST CFReDS Hacking Case](https://cfreds.nist.gov/Hacking_Case.html)** — caso de referência com solução publicada pelo NIST.

Um perito deve ser capaz de:

1. Fazer login no webapp
2. Criar um caso e fazer upload da imagem CFReDS
3. Visualizar artefatos extraídos automaticamente via TSK
4. Buscar e encontrar arquivos relevantes por texto
5. Navegar pela timeline de eventos
6. Marcar artefatos como relevantes/irrelevantes
7. Verificar a cadeia de custódia completa e íntegra
8. Exportar um laudo básico em PDF

**Tudo isso com `AI_ENABLED=false`.**

Meta: encontrar ≥ 80% dos artefatos da solução de referência NIST.

---

## Roadmap

| Fase | Foco | Entregas Principais |
|------|------|---------------------|
| **0** | Live USB | ISO, write-blocker, imagem de disco, captura RAM, mobile, TUI |
| **1** | Fundação e MVP | Docker, abstrações, modelos, auth, TSK, busca, relatório |
| **2** | Ferramentas + Automação | Volatility, Zeek, ALEAPP, Elasticsearch, NSRL, regex, OCR |
| **3** | IA Opcional | Ollama/Claude/OpenAI, RAG, chat, NLP, narrativa de laudo |
| **4** | Enterprise | Velociraptor, VirusTotal, ICP-Brasil, CASE export |
| **5** | Refinamento | Plugin API, 100M+ artefatos, DOCX, i18n, admin dashboard |

Acompanhe o progresso nas [Issues](https://github.com/VilTeo/forensicos/issues).

---

## Estrutura do Repositório

```
forensicos/
├── backend/        # FastAPI — API, modelos, pipeline, workers
├── frontend/       # React + TypeScript — webapp de laboratorio
├── workers/        # Celery tasks — processamento assincrono
├── docker/         # Dockerfiles e configuracoes
│   ├── docker-compose.yml       # stack base (sem IA)
│   └── docker-compose.ai.yml   # overlay IA (Ollama + ChromaDB)
├── live-usb/       # Build da ISO e scripts de campo
└── docs/           # Documentacao tecnica
```

---

## Modelos de Dados

```
Case ──────────── Evidence ─────────── ChainOfCustodyEntry
  |                   |                      (append-only)
  |                   |
  |              Artifact ──────────── ArtifactAutomation
  |                   |                      (Fase 2)
  |                   └──────────────── ArtifactAIMetadata
  |                                          (Fase 3, opcional)
  ├── Finding
  └── Report
```

---

## Princípios do Projeto

1. **IA é plugin, não dependência** — sistema 100% funcional sem LLM
2. **Segurança desde o início** — cada módulo define ameaças e mitigações
3. **Testes junto com o código** — escritos antes ou durante a implementação
4. **Simplicidade primeiro** — começa mínimo, cresce com justificativa
5. **Abstrações para extensibilidade** — troca implementações via config, não código

---

## Licença

MIT © ForensicOS Contributors
