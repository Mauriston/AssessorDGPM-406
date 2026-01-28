# 🩺 Assessor Pericial DGPM-406  
### Gem Normativo Médico-Pericial para a Marinha do Brasil

Este repositório contém o **prompt final consolidado** do *Assessor Pericial DGPM-406*, um agente especializado em **assessoramento médico-pericial militar**, projetado para operar com **segurança jurídica**, **anti-alucinação forense** e **subsunção normativa estrita**, conforme a **DGPM-406 (9ª Revisão)** e normas correlatas.

O Gem foi concebido para uso por **Presidentes de Juntas de Saúde**, médicos peritos militares e gestores administrativos da área de saúde da Marinha do Brasil, bem como para **pesquisa aplicada em engenharia de prompts normativos**.

---

## 🎯 Objetivo do Projeto

Fornecer um **prompt de produção** capaz de:

- Assessorar decisões médico-periciais com **base textual obrigatória**;  
- Redigir **Inspeções de Saúde (IS)**, laudos e minutas administrativas **auditáveis**;  
- Evitar inferências clínicas ou normativas não autorizadas;  
- Operar com **RAG hierárquico**, memória longa de padrões e controle rigoroso de saída;  
- Ser **versionável, revisável e reutilizável** em ambientes profissionais.

---

## 🧠 Conceitos-Chave Incorporados

- RAG hierárquico normativo;  
- Quote-first condicional por modo;  
- Subsunção normativa estrita;  
- Separação absoluta entre FATO e NORMA;  
- Templates operacionais auditáveis;  
- Memória longa de consistência pericial;  
- Controle de formato (blocos de código para cópia).

---

## 🏗️ Arquitetura Lógica do Gem

Abaixo está a **visualização arquitetural do prompt**, representando o fluxo de decisão e geração de respostas.

### 🔎 Diagrama Arquitetural (ASCII)

┌───────────────────────────────┐  
│        ENTRADA DO USUÁRIO     │  
│  (Pergunta / Dados / PDFs)   │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  CLASSIFICAÇÃO OBRIGATÓRIA    │  
│  - Fonte Normativa            │  
│  - Dado do Caso               │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  ROTEADOR DE INTENÇÃO         │  
│  (MSG IS / CAN / FALTA / Q&A) │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  CAMADA NORMATIVA (RAG)       │  
│  Nível 1: DGPM-406 + Anexos   │  
│  Nível 2: Busca Controlada    │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  SUBSUNÇÃO ESTRITA            │  
│  FATO → CRITÉRIO → CONCLUSÃO  │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  MEMÓRIA LONGA DE PADRÕES     │  
│  (Consistência Intertemporal) │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  GERADOR DE SAÍDA CONTROLADA  │  
│  - RAW                        │  
│  - NORMATIVO                  │  
│  - PASSO-A-PASSO              │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  SAÍDA AUDITÁVEL              │  
│  (Blocos Markdown Copiáveis)  │  
└───────────────────────────────┘  

---

## 🧩 Componentes da Arquitetura

### 1️⃣ Classificação de Entradas

Toda entrada é **obrigatoriamente classificada** como:

- **Fonte Normativa** (RAG ou Busca Controlada);  
- **Dado do Caso** (laudos, exames, CSV, CIDs).

**Regra de ouro:** Dados do caso **nunca** são tratados como norma.

---

### 2️⃣ RAG Hierárquico

- **Nível 1 (prioritário):** DGPM-406 (9ª Rev) + anexos;  
- **Nível 2 (exceção):** Leis/Portarias listadas nos itens 1.5.a–f da DGPM-406;  
- Busca externa **somente** quando explicitamente autorizada.

---

### 3️⃣ Subsunção Normativa Estrita

Modelo obrigatório:

FATO DO CASO → CRITÉRIO NORMATIVO (citado) → CONCLUSÃO

É proibido:

- Criar critérios;  
- Inferir lacunas;  
- “Ajudar” a norma.

---

### 4️⃣ Memória Longa de Padrões

O Gem mantém **consistência técnica e linguística** entre inspeções semelhantes, observando:

- CID / Grupo nosológico;  
- Finalidade da IS;  
- Situação funcional;  
- Dispositivos normativos aplicados.

Não cria precedente jurídico.  
Não expõe dados sensíveis.

---

### 5️⃣ Controle de Saída (Auditabilidade)

- Texto reutilizável → sempre em **blocos de código Markdown**;  
- IS:
  - 3 blocos (sem anexo);  
  - 4 blocos (com PDF anexado);  
- ENTREVISTA e EXAME CLÍNICO:
  - listas com travessões;  
  - pontuação padronizada (;, ; e, .).

---

## 📂 Estrutura do Repositório
```text
assessor-pericial-dgpm-406/
├─ README.md                 ← este arquivo
├─ prompts/
│  ├─ prompt-final.md        ← versão legível (Markdown)
│  ├─ prompt-final.txt       ← versão copiar/colar
├─ docs/
│  ├─ arquitetura.md         ← explicação detalhada da arquitetura
│  └─ uso-rapido.md          ← guia prático de uso
```
---

## 🚀 Como Usar

1. Abra `prompts/prompt-final.txt`;  
2. Copie **todo o conteúdo**;  
3. Cole no campo **Instructions** do GPT/Gem;  
4. Salve e utilize normalmente.

Para revisão técnica, prefira `prompt-final.md`.

---

## 🔐 Privacidade e Segurança

- Nunca insira dados reais identificáveis em exemplos;  
- Dados reais fornecidos pelo usuário devem ser **mascarados quando possível**;  
- PDFs anexados são tratados como **DADOS DO CASO**, nunca como norma.

---

## 📜 Licença e Aviso Legal

Este repositório contém **engenharia de prompt** e não substitui:

- a leitura direta das normas;  
- o julgamento médico-pericial humano;  
- a autoridade administrativa formal.

Uso sob responsabilidade do operador.

---

## ✍️ Autor

Projeto concebido para uso profissional em **atividade médico-pericial militar**, com foco em:

- segurança jurídica;  
- padronização;  
- auditabilidade;  
- engenharia avançada de prompts normativos.
