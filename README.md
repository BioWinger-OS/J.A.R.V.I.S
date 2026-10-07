# J.A.R.V.I.S. 1.0

Assistente local em Python: **modo escrita (padrão)** orientado a **programação**, com voz opcional, memória, plugins, bandeja do Windows e autoaperfeiçoamento seguro.

**Licença:** veja o arquivo [`LICENSE`](LICENSE).  
**Pacote:** `Jarvis_1.0.zip`

---

## Índice

1. [Requisitos](#1-requisitos)
2. [Instalação rápida](#2-instalação-rápida)
3. [Modo escrita e modo escuta](#3-modo-escrita-e-modo-escuta)
4. [Modelo de programação](#4-modelo-de-programação)
5. [Voz do dono (banco de amostras)](#5-voz-do-dono-banco-de-amostras)
6. [Bandeja do sistema e início com o Windows](#6-bandeja-do-sistema-e-início-com-o-windows)
7. [Autoaperfeiçoamento seguro](#7-autoaperfeiçoamento-seguro)
8. [Memória, planos e plugins](#8-memória-planos-e-plugins)
9. [Recursos avançados](#9-recursos-avançados)
10. [Comandos úteis](#10-comandos-úteis)
11. [Estrutura de pastas](#11-estrutura-de-pastas)
12. [Solução de problemas](#12-solução-de-problemas)
13. [Licença e uso responsável](#13-licença-e-uso-responsável)

---

## 1. Requisitos

| Item | Observação |
|------|------------|
| **Windows 10/11** | Recomendado (voz, volume, bandeja, apps) |
| **Python 3.10+** | No PATH |
| **Ollama** | [https://ollama.com](https://ollama.com) |
| Microfone | Só se for usar `modo escuta` |
| GPU NVIDIA (opcional) | Whisper/Ollama mais rápidos |

Dependências Python: `requirements.txt`.

---

## 2. Instalação rápida

```powershell
# 1) Extrair o zip e entrar na pasta
cd Jarvis

# 2) Dependências
python -m pip install -r requirements.txt

# 3) Modelo de programação (padrão do Jarvis 1.0)
ollama pull qwen2.5-coder:7b

# 4) Iniciar (modo escrita)
python jarvis.py
```

Atalho sem janela preta (Windows): clique duas vezes em `Iniciar_Jarvis.vbs`.

---

## 3. Modo escrita e modo escuta

### Padrão: modo escrita
- Você **digita** e pressiona Enter  
- **Não** escuta o microfone  
- **Não** fala as respostas (só texto no terminal)  
- Ideal para programação, revisão de código e comandos longos  

### Comandos de modo

| Comando | Efeito |
|---------|--------|
| `modo escrita` | Só teclado (padrão) |
| `modo escuta` | Liga microfone |
| `modo texto` | Alias de modo escrita |
| `modo voz` | Alias de modo escuta |

No modo escuta, digite de novo `modo escrita` para voltar ao teclado.

---

## 4. Modelo de programação

Padrão: **`qwen2.5-coder:7b`** (via Ollama).

```powershell
ollama pull qwen2.5-coder:7b
```

Outro modelo:

```powershell
$env:JARVIS_MODEL = "deepseek-coder-v2:16b"
python jarvis.py
```

O prompt do assistente prioriza explicações de código, correções e exemplos em markdown.

Modelo de visão de tela (opcional, separado): `llava` ou variável `JARVIS_VISION_MODEL`.

---

## 5. Voz do dono (banco de amostras)

Só necessário se for usar **modo escuta** com bloqueio “só minha voz”.

```powershell
python adicionar_reconhecimento_voz.py
```

| Opção | Função |
|-------|--------|
| **1** | Cadastro completo (várias frases → banco maior) |
| **2** | Adicionar mais amostras |
| **3** | Testar reconhecimento |
| **4** | Status do banco |

- Até **40 amostras** em `voices/banco/`  
- Compara perfil base + adaptativo + banco (melhor match e top-3)  
- WAV temporários são apagados; ficam só embeddings numéricos  

### Comandos de voz

- `banco de voz` — quantas amostras  
- `limpar amostras de voz` — remove só as aprendidas automaticamente  
- `reconhecimento mais fácil` / `mais rigoroso` — limiar  
- `debug de voz` — scores no console  
- `desliga reconhecimento de voz` — aceita qualquer voz  

Whisper em CPU por padrão (`JARVIS_WHISPER_DEVICE=cuda` se tiver GPU).

---

## 6. Bandeja do sistema e início com o Windows

### Bandeja (ícone perto do relógio)
Ao iniciar, se `pystray` e Pillow estiverem instalados, aparece o ícone.

**Botão direito:**

| Menu | Ação |
|------|------|
| Modo escuta (voz) | Liga microfone |
| Modo escrita (texto) | Só teclado |
| Parar fala / comando | Interrompe |
| Status | Online, modo, início automático |
| Iniciar com o Windows | Liga/desliga no login |
| Sair | Encerra |

```powershell
pip install pystray Pillow
```

### Início automático

| Comando | Efeito |
|---------|--------|
| `iniciar com o windows` | Coloca na pasta Inicializar |
| `remover do início` | Remove |
| `você inicia com o windows?` | Consulta status |

Manual: `Win+R` → `shell:startup` → atalho para `Iniciar_Jarvis.vbs`.

---

## 7. Autoaperfeiçoamento seguro

O Jarvis **não reescreve `jarvis.py` sozinho**.

1. Você dá feedback (`isso está errado…`, `responda mais curto…`)  
2. `se auto aprimore` → gera **propostas**  
3. `aplicar melhoria` → aplica só mudanças **seguras**  

### Pode aplicar (com confirmação)
- Correções de fala (STT)  
- Preferências (tom, tamanho)  
- Fatos / aprendizados / orientações  
- Plugins simples em `plugins/`  
- Conhecimento JSON  

### Não aplica sozinho
- Código do núcleo → arquivo em `propostas/` para revisão manual  

| Comando | Efeito |
|---------|--------|
| `se auto aprimore` | Gera propostas |
| `listar propostas` | Lista |
| `aplicar melhoria` | Aplica seguras |
| `aplicar melhoria ID` | Uma específica |
| `rejeitar melhoria` | Descarta |
| `listar feedback` | Histórico de reclamações |

---

## 8. Memória, planos e plugins

### Correções de fala
```text
quando eu disser "futeex", entenda "FuteexYT"
```
Consulte: `minhas correções de fala`.

### Fatos e preferências
- `lembre que ...`  
- `prefiro respostas curtas`  
- `o que você aprendeu comigo`  
- `meu perfil`  

### Planos de tarefa
```text
planejar tarefa organizar meus arquivos de estudo
ver plano
concluí a etapa atual
```

### Plugins
- Exemplo ativo: diga `teste plugin`  
- Crie `plugins/meu_plugin.py` com `def processar(texto):`  
- Inclua o nome em `plugins/ativos.json` e reinicie  
- **Só ative código que você revisou** (roda com os privilégios do Jarvis)

---

## 9. Recursos avançados

Módulos opcionais carregados se os arquivos estiverem presentes:

| Módulo | Exemplos de uso |
|--------|-----------------|
| `recursos_avancados.py` | Sessão, foco profundo, busca semântica, rotinas condicionais |
| `recursos_pro.py` | Agente de tarefas, PDF, GUI orb, mãos livres |
| `recursos_extra.py` | Painel web, rotinas, Spotify (se configurado) |
| `recursos_alto_impacto.py` | Agenda, visão de tela, conversa contínua |
| `recursos_bandeja.py` | Bandeja + startup |
| `recursos_autoaperfeicoar.py` | Feedback e propostas |

### Sessão
- `resumo da sessão` / `limpar sessão`

### Foco
- `modo foco 25 minutos` / `desativa foco`  
- Foco profundo (bloqueio de sites) exige confirmação e é orientado ao Windows  

### Arquivos e tela
- `listar arquivos` / `buscar arquivo ...`  
- `ver minha tela` (modelo de visão no Ollama)  
- `resume o ultimo pdf`

### WhatsApp / web
- Contatos locais e envio via integrações configuradas  
- `abre o youtube` / `abre o google` (navegador padrão)

---

## 10. Comandos úteis

| Área | Exemplos |
|------|----------|
| Modo | `modo escrita`, `modo escuta` |
| Código / IA | perguntas livres no teclado |
| Tempo | `que horas são`, `timer de 5 minutos` |
| Cálculo | `quanto é 25 vezes 4` |
| Sistema | `faz backup`, `status` |
| Voz | `banco de voz`, `calibrar voz` |
| Auto | `se auto aprimore`, `aplicar melhoria` |
| Interromper | `Jarvis` / `para` / `stop` |

---

## 11. Estrutura de pastas

```
Jarvis/
├── jarvis.py                 # núcleo
├── conversa.py               # só conversa
├── adicionar_reconhecimento_voz.py
├── Iniciar_Jarvis.vbs
├── requirements.txt
├── README.md                 # este arquivo
├── LICENSE
├── recursos_*.py             # módulos
├── plugins/
├── memory/                   # memória, feedback, propostas
├── voices/                   # perfil e banco de voz
├── conhecimento/             # JSON de conhecimento
└── propostas/                # ideias de código (revisão humana)
```

---

## 12. Solução de problemas

| Problema | O que tentar |
|----------|----------------|
| `ollama` não encontrado | Instale o Ollama e rode `ollama serve` |
| Modelo ausente | `ollama pull qwen2.5-coder:7b` |
| Microfone não ouve | Digite `modo escuta`; confira permissões do Windows |
| Voz não reconhece | Rode `adicionar_reconhecimento_voz.py` (várias frases) |
| Bandeja não aparece | `pip install pystray Pillow` |
| Import error | `pip install -r requirements.txt` |
| Whisper lento | `JARVIS_WHISPER_DEVICE=cpu` ou modelo `tiny` |

---

## 13. Licença e uso responsável

Leia o arquivo **[LICENSE](LICENSE)** completo.

Resumo:
- Uso pessoal, educacional e profissional permitido  
- Modificar e redistribuir com a licença e avisos de autoria  
- Proibido uso ilegal ou enganoso  
- Sem garantia; o risco de comandos no sistema é seu  
- Não processe voz de terceiros sem consentimento  
- Autoaperfeiçoamento não altera o núcleo sem a sua revisão  

---

**Jarvis 1.0** — assistente local, escrita primeiro, programação no centro, voz sob demanda.
