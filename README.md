<div align="center">

# ForensicOS

**Plataforma de Forense Digital Open Source — Campo, Laboratório e Aquisição Avançada**

*Forensicamente sólida · IA opcional · Alternativa open source ao Cellebrite*

[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115+-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?style=flat-square&logo=postgresql&logoColor=white)](https://postgresql.org)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docker.com)
[![Issues](https://img.shields.io/github/issues/VilTeo/forensicos?style=flat-square&color=orange)](https://github.com/VilTeo/forensicos/issues)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)

</div>

---

## O que é o ForensicOS

ForensicOS é uma plataforma completa de forense digital open source para peritos examinadores. Combina três componentes:

| Componente | O que faz |
|-----------|-----------|
| **Webapp Docker** | Análise de evidências em laboratório — pipeline, busca, timeline, laudos, colaboração |
| **Live USB** | Coleta em campo — imagem de disco, captura de RAM, extração mobile, triagem |
| **ForensicOS Acquire (FOFA)** | Framework modular de aquisição avançada — extração de dispositivos bloqueados |

> **Base legal:** A extração forense de dispositivo apreendido com mandado judicial é lícita (Art. 13 Lei 12.965/2014, Art. 3º Lei 13.964/2019, STJ HC 512.290/RJ). Todo procedimento gera documentação para admissibilidade probatória.

---

## Arquitetura em 3 Camadas

```
┌──────────────────────────────────────────────────────────────────┐
│  CAMADA 3 — IA (opcional, AI_ENABLED=true)                       │
│  Chat investigativo · RAG · NLP · Classificação · Narrativa      │
├──────────────────────────────────────────────────────────────────┤
│  CAMADA 2 — AUTOMAÇÃO (regras determinísticas, sem LLM)          │
│  Heurística · NSRL · Regex PII · OCR · ExifTool · Esteganografia │
├──────────────────────────────────────────────────────────────────┤
│  CAMADA 1 — CORE (sem IA, sem rede externa)                      │
│  Casos · Custódia · TSK · Busca · Timeline · Laudos              │
└──────────────────────────────────────────────────────────────────┘
         ↑
   100% funcional sem IA
```

**Princípio fundamental:** A IA é um plugin, não uma dependência. `AI_ENABLED=false` é o padrão.

---

## ForensicOS Acquire (FOFA) — Framework de Aquisição Avançada

O FOFA é o módulo que habilita extração de dispositivos bloqueados — o maior diferencial em relação a ferramentas convencionais. Funciona como um framework modular onde cada técnica de extração é um módulo independente.

```
forensicos-acquire > scan
[*] Escaneando dispositivos USB...
[+] #1 Motorola Moto G8 Power — Snapdragon 665 — Estado: AFU
    Módulos compatíveis:
      → qualcomm/edl      (não-invasivo, requer test point)
      → android/avilla    (não-invasivo, requer AFU)

forensicos-acquire > use qualcomm/edl
forensicos-acquire (qualcomm/edl) > set OPERATOR "Perito João Silva CRF/GO-123"
forensicos-acquire (qualcomm/edl) > set AUTHORIZATION "Mandado nº 0001234-56.2026"
forensicos-acquire (qualcomm/edl) > run

[+] Extração concluída · SHA256: abc123... · Laudo: report_20260513.pdf
```

### Conceito BFU/AFU — Fundamental para apreensão

| Estado | Significa | Dados acessíveis | Como ocorre |
|--------|-----------|-----------------|-------------|
| **AFU** | After First Unlock | ~95% do armazenamento | Dispositivo ligado e usado desde o último boot |
| **BFU** | Before First Unlock | ~5% (metadados, cache) | Dispositivo desligado ou recém-reiniciado |

> **Regra crítica:** Nunca reiniciar o dispositivo antes da extração. Um reboot destrói o estado AFU e bloqueia 90% dos dados permanentemente.

### Módulos disponíveis

**Técnicas software (sem hardware adicional):**

| Módulo | Técnica | Chipset | Cobertura |
|--------|---------|---------|-----------|
| `qualcomm/edl` | EDL Mode | Qualcomm Snapdragon | Alta — Motorola, Xiaomi, Samsung A/M |
| `mediatek/brom` | BROM Mode | MediaTek MT6xxx/MT8xxx | Média — Positivo, Nokia, Samsung antigo |
| `mediatek/mtksu` | CVE-2020-0069 | MediaTek Android ≤ 9 | Média — sem hardware, só ADB |
| `apple/checkm8` | checkm8 bootrom | Apple A8-A11 | Alta — iPhone 6 ao X |
| `apple/lockdown` | Pairing file | iOS qualquer (AFU) | Alta — se PC do suspeito apreendido |
| `android/avilla` | Avilla Forensics | Android 12-15 (AFU) | Média — sem root |
| `android/adb-backup` | ADB Backup | Android < 10 | Baixa — parcial |
| `ios/itunes-brute` | Brute force backup | iOS qualquer | Média — PIN de backup 4-6 dígitos |

**Técnicas hardware (requerem equipamento ~R$ 15k):**

| Módulo | Técnica | Cobertura |
|--------|---------|-----------|
| `hardware/isp` | ISP eMMC test pads | Qualquer Android com pads acessíveis |
| `hardware/jtag` | JTAG via pyOCD/OpenOCD | Dispositivos ARM com debug port |
| `hardware/chipoff` | Chip-off eMMC/UFS | Qualquer dispositivo — último recurso |

**Módulos em pesquisa ativa (a desenvolver):**

| Módulo | Chipset | Por que importa |
|--------|---------|----------------|
| `unisoc/brom` | UNISOC T606/T616 | Positivo, Multilaser, Samsung A02s/M02 — sem alternativa open source |
| `hisilicon/brom` | HiSilicon Kirin | Huawei P20/P30/Y9 |
| `rockchip/maskrom` | Rockchip RK3xxx | Tablets Multilaser, Positivo |
| `samsung/exynos` | Samsung Exynos | Galaxy A52/A53/A54 |
| `apple/a12plus` | Apple A12+ | iPhone XS em diante — pesquisa de bootchain |
| `hardware/pin-robot` | Brute force físico | PIN por automação de interface via Raspberry Pi |

---

## Ferramentas Integradas

### Disco e Sistema de Arquivos
| Ferramenta | Função | Fase |
|-----------|--------|------|
| The Sleuth Kit (TSK) | Análise de filesystem, extração de arquivos | 1 |
| Plaso / log2timeline | Super timelines de eventos | 1 |
| bulk_extractor | Extração de emails, URLs, cartões em larga escala | 1 |
| ExifTool | Metadados de arquivos | 1 |
| Scalpel / Foremost | File carving — recuperação de arquivos deletados | 2 |
| Autopsy | Plataforma forense com ecossistema de plugins | 4 |
| IPED | Processamento massivo (~400 GB/h) — Polícia Federal BR | 4 |
| Binwalk | Engenharia reversa de firmware | 4 |
| RegRippy | Windows Registry | 4 |
| Guymager / dc3dd | Imaging forense no Live USB | 0 |

### Memória RAM
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Volatility 3 | Processos, DLLs, conexões, detecção de malware | 2 |
| AVML / LiME | Captura de RAM ao vivo no campo | 0 |

### Rede
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Zeek | Logs estruturados de tráfego de rede | 2 |
| tshark | Extração de objetos e credenciais de PCAPs | 2 |
| Arkime | Indexação de PCAPs grandes | 4 |

### Mobile (Android / iOS)
| Ferramenta | Função | Fase |
|-----------|--------|------|
| ALEAPP | Parser de artefatos Android | 2 |
| iLEAPP | Parser de artefatos iOS | 2 |
| MVT | Detecção de spyware (Pegasus) | 2 |
| SQLite Forensics | Recuperação de registros deletados em apps | 2 |
| Parsers WhatsApp / Telegram / Signal | Extração profunda por app | 2 |
| ADB + libimobiledevice | Extração lógica Android/iOS | 0 |
| AndroidQF | Coleta de artefatos Android | 0 |

### Criptografia e Senhas
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Hashcat + John the Ripper | Cracking de senhas em evidências cifradas | 2 |
| Dislocker + cryptsetup | Containers BitLocker, LUKS, VeraCrypt | 2 |

### Malware e Detecção
| Ferramenta | Função | Fase |
|-----------|--------|------|
| YARA | Detecção de padrões customizáveis | 1 |
| ClamAV | Antivírus open source | 2 |
| Steghide + zsteg + StegDetect | Esteganografia — dados ocultos em imagens | 2 |
| Capa (Mandiant) | Capacidades de executáveis | 4 |

### Inteligência e Correlação
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Neo4j + Cytoscape.js | Grafo de relações — equivalente ao Cellebrite Pathfinder | 4 |
| Leaflet.js + PostGIS | Mapa interativo de artefatos GPS | 4 |
| CDR Analyzer | Call Detail Records + correlação por torre de antena | 4 |
| Blockchain (Blockchair) | Rastreio de criptomoedas on-chain | 4 |
| OSINT (SpiderFoot / Sherlock / Holehe) | Enriquecimento via fontes abertas | 4 |

### Dispositivos Especiais
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Drone Forensics (DJI / Parrot) | Logs de voo, trajetória, vídeo georreferenciado | 4 |
| Vehicle Forensics (CAN bus) | Infotainment, EDR, GPS embarcado | 4 |
| SIM Forensics (pyscard) | Extração de SIM card | 4 |

### Cloud e Comunicações
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Google Takeout parser | Gmail, Drive, Photos, Maps, Chrome | 4 |
| iCloud parser | Backup, Photos, Drive, iMessages | 4 |
| Meta / WhatsApp Cloud | Messages, Activity, Location history | 4 |

### IR Remota e Enterprise
| Ferramenta | Função | Fase |
|-----------|--------|------|
| Velociraptor | Coleta remota de endpoints via VQL | 4 |
| VirusTotal API | Enriquecimento de hashes, URLs e domínios | 4 |
| GRR | Resposta remota em escala | 5 |
| Turbinia | Processamento forense em cloud | 5 |

---

## Funcionalidades da Plataforma

### Gestão de Casos e Evidências
- Casos com número automático (`FOR-2026-0001`), status, prioridade, equipe e tags
- Upload com validação de magic bytes, detecção de zip bomb e quarentena
- Chain of Custody com hash chain SHA-256 — append-only, imutável, verificável
- Suporte a todos os tipos: disk image, memory dump, PCAP, mobile, cloud export, CDR, drone, veículo, SIM

### Análise e Busca
- Busca full-text PostgreSQL (Fase 1) → Elasticsearch (Fase 2)
- Busca semântica por RAG com ChromaDB (Fase 3, opcional)
- Timeline unificada com filtros por fonte, tipo e período
- Mapa interativo de artefatos com coordenadas GPS
- Grafo de relações entre entidades (pessoas, dispositivos, contas, locais)

### Laudos e Exportação
- Relatórios técnico, executivo e laudo pericial em PDF
- Auto-preenchimento com dados estruturados do caso
- Narrativa por IA com confirmação humana obrigatória (Fase 3)
- Assinatura digital ICP-Brasil (PAdES + carimbo de tempo)
- Exportação no formato CASE JSON-LD (interoperável com Cellebrite, Axiom, FTK)

### Segurança
| Controle | Implementação |
|----------|---------------|
| Chain of Custody | SHA-256 de cada entrada, hash chain verificável fim-a-fim |
| Upload | Magic bytes, zip bomb detection, quarentena automática |
| CLI | `SafeCommandRunner` — `shell=False`, timeout, sandbox firejail |
| Rate limiting | 5 logins/min · 3 registros/hora (Redis) |
| Audit log | Todas as ações — append-only, sem deleção |
| IA com confirmação | Findings de IA requerem aprovação humana antes do laudo |
| ICP-Brasil | Validade jurídica dos laudos exportados |

---

## Stack Técnico

| Camada | Tecnologia |
|--------|-----------|
| Backend | Python 3.12+ · FastAPI 0.115+ · SQLAlchemy 2 · Alembic |
| Frontend | React 18 · TypeScript 5 · Tailwind CSS 4 · Vite |
| Banco de Dados | PostgreSQL 16 |
| Busca | PostgreSQL FTS → Elasticsearch 8 (Fase 2) |
| Grafo | Neo4j (Fase 4) |
| Vector Store | ChromaDB 0.5+ (Fase 3, apenas com IA) |
| Task Queue | Celery 5 + Redis 7 |
| Storage | MinIO (S3-compatível) |
| Infraestrutura | Docker + Docker Compose |
| Auth | JWT + RBAC customizado |
| Code Quality | ruff (linter + formatter) |
| Testes | pytest + pytest-asyncio + httpx |

### Containers por fase

| Fase | Containers |
|------|-----------|
| 1 | backend, frontend, worker, postgres, redis, minio (6) |
| 2 | + elasticsearch (7) |
| 3 (IA) | + chromadb, ollama via `docker-compose.ai.yml` (9) |
| 4 | + neo4j (10) |

---

## Roadmap

| Fase | Foco | Issues |
|------|------|--------|
| **0 — Live USB** | ISO, write-blocker, imagem de disco, RAM, mobile, TUI | #18–#24 |
| **1 — Fundação** | Docker, abstrações, modelos, auth, TSK, busca, relatório (MVP) | #1–#17 |
| **2 — Ferramentas** | Volatility, Zeek, ALEAPP, Elasticsearch, criptografia, esteganografia, parsers | #25–#54 |
| **3 — IA** | Ollama/Claude/OpenAI, RAG, chat investigativo, NLP, narrativa | #38–#44 |
| **4 — Enterprise** | Grafo, geolocalização, CDR, blockchain, drones, cloud, OSINT, FOFA | #55–#70 |
| **5 — Refinamento** | Plugins, 100M+ artefatos, DOCX, i18n, admin | — |
| **P — Pesquisa** | UNISOC, HiSilicon, Rockchip, Exynos, iOS A12+, passcode brute force | #71–#76 |

Acompanhe nas [Issues](https://github.com/VilTeo/forensicos/issues) — 76 issues abertas mapeando cada módulo.

---

## MVP — Critério de Aceitação

Validado contra o **[NIST CFReDS Hacking Case](https://cfreds.nist.gov/Hacking_Case.html)**.

Um perito deve conseguir, com `AI_ENABLED=false`:

1. Login → criar caso → upload da imagem CFReDS
2. Ver artefatos extraídos automaticamente via TSK
3. Buscar por palavra-chave e encontrar arquivos relevantes
4. Navegar pela timeline de eventos
5. Marcar artefatos como relevantes/irrelevantes
6. Verificar cadeia de custódia completa e íntegra
7. Gerar e exportar laudo em PDF

Meta: encontrar ≥ 80% dos artefatos da solução de referência NIST.

---

## Estrutura do Repositório

```
forensicos/
├── backend/                  # FastAPI — API, modelos, pipeline, integrações
│   ├── abstractions/         # Interfaces ABC (DIP)
│   ├── integrations/         # Ferramentas forenses (TSK, Volatility, Zeek...)
│   │   ├── apps/             # Parsers WhatsApp, Telegram, Signal
│   │   ├── cloud/            # Google Takeout, iCloud, Meta
│   │   └── osint/            # SpiderFoot, Sherlock, Holehe
│   ├── automation/           # Camada 2 — heurísticas, regex, NSRL
│   ├── models/               # SQLAlchemy models
│   ├── routers/              # Endpoints REST
│   └── services/             # Lógica de negócio
├── frontend/                 # React + TypeScript — webapp de laboratório
├── workers/                  # Celery tasks — processamento assíncrono
│   └── tasks/                # disk, memory, mobile, cloud, CDR, drone, crypto...
├── live-usb/                 # Live USB para coleta em campo
│   ├── acquisition/          # ForensicOS Acquire Framework (FOFA)
│   │   ├── framework/        # Base, CLI, session manager, reporter
│   │   ├── modules/          # Módulos por chipset/técnica
│   │   │   ├── qualcomm/     # EDL
│   │   │   ├── mediatek/     # BROM + mtk-su
│   │   │   ├── apple/        # checkm8, lockdown, BFU
│   │   │   ├── android/      # Avilla, ADB backup
│   │   │   ├── hardware/     # ISP, JTAG, chip-off
│   │   │   └── research/     # UNISOC, HiSilicon, Rockchip, Exynos, A12+
│   │   └── auxiliaries/      # Detecção de chipset, BFU/AFU, test points
│   └── tools/                # CLI: collect, mobile, triage, upload
├── docker/                   # Dockerfiles + nginx + init-db
│   ├── docker-compose.yml    # Stack base (sem IA)
│   ├── docker-compose.ai.yml # Overlay IA (Ollama + ChromaDB)
│   └── docker-compose.prod.yml # Overlay produção (Nginx + SSL)
└── docs/                     # Documentação técnica e procedimentos
    └── procedimentos/
        └── aquisicao-avancada/ # Guias EDL, chip-off, JTAG para laudos
```

---

## Princípios do Projeto

1. **IA é plugin, não dependência** — 100% funcional com `AI_ENABLED=false`
2. **Segurança desde o início** — sanitização, sandboxing e validação em todo módulo
3. **Testes junto com o código** — escritos antes ou durante a implementação
4. **Simplicidade primeiro** — começa mínimo, cresce com justificativa de uso real
5. **Abstrações para extensibilidade** — troca implementações via config, não código
6. **Forense admissível** — cada operação gera hash, log e documentação para laudo

---

## Datasets de Validação

| Dataset | Uso |
|---------|-----|
| [NIST CFReDS Hacking Case](https://cfreds.nist.gov/) | Validação do MVP |
| [Digital Corpora](https://digitalcorpora.org/) | Imagens, PCAPs, dumps |
| [DFRWS Challenges](https://dfrws.org/forensic-challenges/) | Cenários completos |
| [Volatility Samples](https://github.com/volatilityfoundation/volatility3) | Dumps de memória |

---

## Licença

MIT © ForensicOS Contributors
