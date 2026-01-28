# Arquitetura do Gem — Assessor Pericial DGPM-406

Este documento descreve, em detalhe, a **arquitetura lógica** do Gem “Assessor Pericial DGPM-406”, incluindo: camadas, roteadores, regras de hierarquia normativa, processamento de entradas, uso controlado de ferramentas, padrões de saída (RAW/NORMATIVO/PASSO-A-PASSO) e os mecanismos de auditabilidade (quote-first condicional, templates e blocos de código).

> **Escopo do documento:** explicação arquitetural.  
> **Escopo do Gem:** assessoramento normativo/pericial e redação administrativa auditável, com base textual em DGPM-406 (9ª Rev) e fontes autorizadas.

---

## 1) Visão Geral: o que este Gem faz

O Gem atua como um **assessor normativo** para o contexto médico-pericial militar, com três metas centrais:

1. **Fidelidade textual** às fontes normativas autorizadas (anti-alucinação);
2. **Subsunção normativa estrita** (FATO → CRITÉRIO → CONCLUSÃO);
3. **Geração auditável** de documentos (IS, minutas MSG IS/CAN/FALTA, pareceres), com formato padronizado e pronto para uso.

Ele é desenhado para minimizar riscos de:
- enquadramento normativo incorreto,
- inferências proibidas,
- mistura indevida entre fato e norma,
- produção de textos não auditáveis.

---

## 2) Princípios de Design (por que a arquitetura é assim)

### 2.1 Segurança jurídica como requisito de sistema
A atividade médico-pericial militar, no contexto do Gem, é tratada como **ato administrativo com fé pública**, logo:
- **erro** é risco (nulidade, impacto financeiro, responsabilização),
- **precisão textual** é prioridade.

### 2.2 Separação de camadas: “FATO não vira NORMA”
O Gem opera com separação rígida entre:
- **Dados do Caso**: documentos clínicos, planilhas, CIDs, histórico, fatos;
- **Fontes Normativas**: DGPM-406 e itens autorizados (Nível 2).

Isso evita:
- “normatizar” laudos médicos,
- “criar regra” a partir de fatos,
- atribuir valor normativo a documento clínico.

### 2.3 “Anti-alucinação forense” com quote-first condicional
O Gem usa **quote-first** apenas quando o modo de saída exige prova textual (NORMATIVO/PASSO-A-PASSO).  
Em modo RAW, a prioridade é o documento final pronto, sem “evidências” — mas mantendo referências mínimas quando exigidas pelo template.

---

## 3) Diagrama de Arquitetura (Fluxo de Execução)

### 3.1 Fluxo macro (pipeline)

1) **Entrada do usuário**  
2) **Classificação obrigatória da entrada**  
3) **Roteador de intenção / comando**  
4) **Seleção de modo de saída (RAW/NORMATIVO/PASSO-A-PASSO)**  
5) **Camada normativa (RAG Nível 1 → Busca Nível 2 se autorizado)**  
6) **Subsunção estrita (FATO → CRITÉRIO → CONCLUSÃO)**  
7) **Memória longa de padrões (consistência)**  
8) **Gerador de saída controlada (templates + blocos de código)**  
9) **Validações finais de forma e conformidade**

### 3.2 Diagrama (ASCII)

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
│  SELETOR DE MODO              │  
│  RAW | NORMATIVO | PASSO-A-P  │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  CAMADA NORMATIVA             │  
│  Nível 1: RAG DGPM-406        │  
│  Nível 2: busca controlada    │  
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
│  (coerência entre inspeções)  │  
└───────────────┬──────────────┘  
                │  
                ▼  
┌───────────────────────────────┐  
│  SAÍDA CONTROLADA + AUDITÁVEL │  
│  templates + blocos de código │  
└───────────────────────────────┘  

---

## 4) Camadas Arquiteturais (detalhamento)

### 4.1 Camada de Persona (estilo e linguagem)
Define:
- vocabulário formal e normativo,
- objetividade,
- terminologia oficial,
- proibição de opinião assistencial.

**Função arquitetural:** reduzir “ruído conversacional” e manter padrão institucional.

---

### 4.2 Camada de Verificação de Fidelidade (Meta-instrução 0)
Antes da resposta:
- checa se há base textual,
- checa se há inferência proibida,
- checa aderência a templates e roteadores,
- checa necessidade de citação (conforme modo).

**Função arquitetural:** atuar como “pré-flight checklist”.

---

### 4.3 Camada Quote-first (Meta-instrução 0.1) — condicional por modo
Aplica-se somente em:
- **NORMATIVO**
- **PASSO-A-PASSO**

Regras:
- localizar 1 a 3 trechos ipsi litteris,
- exibir primeiro “EVIDÊNCIAS” (texto normal),
- citar item/anexo/página (Nível 1) ou URL oficial limpa (Nível 2),
- se não houver base textual: declarar ausência.

**Função arquitetural:** impedir “norma inventada”.

---

### 4.4 Roteador de Comando (Meta-instrução 1)
Detecta gatilhos:

- **MSG IS** → Diretriz 12  
- **MSG CAN** → Diretriz 13  
- **MSG FALTA** → Diretriz 14  

Ao detectar um gatilho, o Gem deve executar **apenas** a diretriz correspondente, ignorando as demais.

**Função arquitetural:** prevenir mistura de rotinas e garantir previsibilidade em minutas.

---

### 4.5 Normalização (Meta-instrução 1.1)
Permite:
- variações ortográficas do comando,
- status com equivalências (“TIS ASSINADO” = “TIS Assinado”).

**Função arquitetural:** robustez operacional (inputs reais são “sujos”).

---

### 4.6 Concordância de gênero (Meta-instrução 2)
Regras:
- inferir pelo primeiro nome,
- se ambíguo: neutro (“o(a)”) ou pergunta explícita,
- proibido “chutar”.

**Função arquitetural:** reduzir risco de erro formal e administrativo.

---

### 4.7 Mapeamento Anexo M (Meta-instrução 3) — AÇÃO/INFORMAÇÃO
Fonte única: **ANEXO M** (RAG).

Prioridades:
1. SCNS  
2. RM2  
3. Término de Incapacidade  
4. Laudo com Restrições  
5. Finalidade Específica  
6. Regra Geral  

Extrai:
- **AÇÃO**
- **INFORMAÇÃO**
ambos **ipsi litteris**.

**Função arquitetural:** garantir tramitação correta e destinatários consistentes.

> Nota arquitetural: a regra proíbe usar “tabelas de-para” externas (ex.: CSV auxiliar) para evitar divergência silenciosa do texto normativo.

---

## 5) Fontes de Verdade: Hierarquia Normativa

### 5.1 Nível 1 (RAG)
- DGPM-406 (9ª Rev) + anexos (incl. Anexo M)
- Portaria GM-MD nº 3.551/2021 e demais normas anexadas no contexto RAG

### 5.2 Nível 2 (Busca Controlada)
Somente documentos listados nos itens **1.5.a–f** da DGPM-406, quando não estiverem no RAG.

Prioridade de domínios:
- planalto.gov.br
- in.gov.br
- portais governamentais oficiais
- portalmedico.org.br (quando aplicável)

### 5.3 Regra de conflito
1) Procedimentos/competências/tramitação → DGPM-406 prevalece  
2) Critérios legais de benefícios → Portaria GM-MD 3.551/2021 pode prevalecer em matéria de benefício  
3) Lei federal prevalece sobre Portaria e demais atos infralegais

**Função arquitetural:** padronizar resolução de conflitos e impedir “opinião” do agente.

---

## 6) Processamento de Entradas: Classificação e Tratamento

### 6.1 Classificação obrigatória (Seção 2 do prompt)
Toda entrada vira:
- **Fonte normativa** (Nível 1 / Nível 2 autorizado), ou
- **Dado do caso** (não normativo).

**Regra absoluta:** dado do caso nunca vira norma.

---

### 6.2 Planilhas/CSV
- tratadas como **fatos do caso**;
- proibido completar campo vazio;
- para minutas, fluxo obrigatório:
  - roteador → gênero → Anexo M → validações → template.

**Função arquitetural:** tornar geração de minutas determinística.

---

### 6.3 PDFs clínicos anexados
Quando há PDF clínico e pedido de IS/análise:
- o PDF é **dado do caso**,
- conteúdo deve subsidiar Entrevista/Exame/Justificativa,
- se solicitado IS: transcrição integral obrigatória em bloco adicional após Conclusão.

**Função arquitetural:** auditabilidade (o humano valida o que foi usado).

---

### 6.4 Falha de extração de PDF
Se PDF não for extraível:
- declarar impossibilidade,
- pedir reenvio com texto selecionável ou colagem,
- proibido reconstruir texto.

**Função arquitetural:** impedir “preenchimento por imaginação”.

---

## 7) Uso Controlado de Ferramentas Externas

Ferramentas externas são exceção, somente em 3 cenários:

1) Validar RQE/CFM (site oficial)  
2) Descrição de CID (fonte médica de alta reputação; vira dado do caso)  
3) Recuperar norma externa (Nível 2) listada nos itens 1.5.a–f e ausente no RAG

Qualquer outra busca é proibida.

**Função arquitetural:** reduzir superfície de risco e manter autoridade normativa.

---

## 8) Modos de Saída (RAW / NORMATIVO / PASSO-A-PASSO)

### 8.1 RAW
- entrega apenas o documento pronto,
- sem bloco de evidências,
- referência mínima quando template exige (“Em conformidade com…”).

**Uso típico:** quando o usuário quer texto para colar no TIS/SEI.

### 8.2 NORMATIVO
- inclui bloco EVIDÊNCIAS (quote-first),
- depois a resposta com citações.

**Uso típico:** dúvida normativa, enquadramento legal.

### 8.3 PASSO-A-PASSO
- evidências + roteiro sequencial,
- cada etapa com base normativa.

**Uso típico:** tramitação, procedimento administrativo.

---

## 9) Regras de Formato e Auditabilidade

### 9.1 Blocos de código para cópia/colagem (regra global)
Qualquer texto reutilizável deve estar em bloco de código Markdown.

**Função arquitetural:** reduzir erro humano na transcrição e padronizar output.

---

### 9.2 Inspeção de Saúde (IS) — estrutura obrigatória
Sem anexo:
- 3 blocos (Entrevista / Exame / Conclusão)

Com anexo clínico PDF:
- 4 blocos (Entrevista / Exame / Conclusão / Transcrição integral)

---

### 9.3 Regra de lista (Entrevista e Exame)
Nos blocos 1 e 2:
- lista não numerada com travessões,
- itens terminam com:
  - “;” em todos,
  - “; e” no penúltimo,
  - “.” no último,
- proibido parágrafo fora da lista.

**Função arquitetural:** consistência institucional e legibilidade.

---

### 9.4 Datas e URLs
- datas institucionais em DDMMMAAA (ex.: 16JAN2026),
- transcrição literal não altera datas,
- URL: apenas oficial limpa quando Nível 2; proibido link de busca.

---

## 10) Memória Longa de Padrões: Consistência Intertemporal

### 10.1 O que é
Uma camada de coerência de redação (estrutura e fraseologia) para casos semelhantes, baseada em eixos:
- clínico-periciais,
- administrativos,
- normativos.

### 10.2 Como opera
Ciclo interno silencioso:
1) identificar similaridade (≥ 2 eixos),
2) recuperar padrão,
3) checar coerência (manter/ajustar/corrigir).

### 10.3 Limites
- não cria precedente jurídico,
- não expõe dados sensíveis,
- norma sempre prevalece.

---

## 11) “Checklist” arquitetural de conformidade (validação final)

Antes de responder, o Gem deve conseguir marcar:

- [ ] Classifiquei entradas (norma vs dado do caso)?  
- [ ] Estou usando apenas fontes autorizadas?  
- [ ] Se NORMATIVO/PASSO-A-PASSO: há evidências textuais?  
- [ ] Se minuta: apliquei roteador e diretriz exclusiva?  
- [ ] Se IS: usei a estrutura correta (3 ou 4 blocos)?  
- [ ] Se PDF clínico e IS: transcrevi integralmente?  
- [ ] Mantive formato de listas em Entrevista/Exame?  
- [ ] Não inventei dados ausentes?  
- [ ] Datas e URLs no padrão correto?

---

## 12) Como evoluir esta arquitetura no Git (prática recomendada)

- Mantenha `prompts/prompt-final.md` como **fonte humana** (legível);
- Mantenha `prompts/prompt-final.txt` como **fonte operacional** (copiar/colar);
- Documente mudanças relevantes em *PRs* (pull requests) com:
  - objetivo da mudança,
  - risco mitigado,
  - exemplos de impacto (antes/depois),
  - revisão por pares (quando possível).

---

## 13) Referência de Arquivos do Repositório

- `README.md`: visão geral + diagrama + como usar  
- `docs/arquitetura.md`: este documento (explicação detalhada)  
- `prompts/prompt-final.md`: prompt formatado para leitura  
- `prompts/prompt-final.txt`: prompt “copiar/colar” sem ruídos

---
