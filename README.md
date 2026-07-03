# myproject
# 🔄 Pipeline de Dados com Python e DuckDB (Arquitetura Medalhão)

## 🎯 Sobre o projeto

Projeto desenvolvido durante meus estudos de **Engenharia de Dados**, como prática dos conceitos aprendidos no curso.

Este projeto é um pipeline de dados construído em **Python + DuckDB**, simulando uma **arquitetura medalhão (Bronze → Silver → Gold)**, um dos padrões mais usados em Engenharia de Dados para organizar o fluxo de informação desde a ingestão bruta até uma camada final pronta para consumo/análise.

O objetivo foi praticar, de ponta a ponta, as etapas de um pipeline real:
- leitura e ingestão de arquivos CSV;
- deduplicação e padronização de dados;
- modelagem de uma tabela dimensional final.

Todo o processamento foi feito com **SQL (via DuckDB)** e **Pandas**, incluindo o uso de **window functions** para resolver duplicidade de registros — um problema comum em pipelines de ingestão incremental.

---

## 🧱 Arquitetura do pipeline
landing/ (CSV)
│
▼
1️⃣ Ingestão  →  camada Bronze  (bronze_z0019)
│
▼
2️⃣ Enriquecimento  →  camada Silver  (produtos)
│
▼
3️⃣ Refinamento  →  camada Gold  (dim_produtos)

### 1️⃣ Ingestão (`Scripts/ingestao.ipynb`) — Camada Bronze
- Lê o arquivo CSV da pasta `Landing/` (separador `;`, padrão brasileiro).
- Adiciona colunas de rastreabilidade: `nome_arquivo` e `data_ingestao`, registrando de onde e quando cada dado chegou.
- Cria a tabela `bronze_z0019` no DuckDB e insere os dados brutos, sem nenhuma transformação — a ideia da camada Bronze é preservar o dado exatamente como veio da origem.

### 2️⃣ Enriquecimento (`Scripts/enriquecimento.ipynb`) — Camada Silver
- Usa a função de janela `ROW_NUMBER() OVER (PARTITION BY NATBR ORDER BY data_ingestao DESC)` para identificar e remover registros duplicados, mantendo sempre a ingestão mais recente de cada produto.
- Renomeia as colunas para nomes de negócio (`NATBR → ID`, `MAKTX → Nome`, `WERKS → Categoria`, `MAINS → Fornecedor`, `LABST → Preco`).
- Ajusta os tipos de dados (`astype`) — inteiros, strings e floats — corrigindo o fato de tudo chegar como texto do CSV.
- Grava o resultado limpo na tabela `produtos`.

### 3️⃣ Refinamento (`Scripts/refinamento.ipynb`) — Camada Gold
- Parte da tabela `produtos` e remove colunas que não são necessárias para a análise final (`Categoria`, `Fornecedor`).
- Cria a tabela dimensional `dim_produtos`, já com nomes e tipos finais (`id_produto`, `Nome_produto`, `Preco_produto`), pronta para ser consumida por relatórios ou dashboards.

---

## 🛠️ Tecnologias utilizadas

- **Python**
- **Pandas** — manipulação e tipagem de dados
- **DuckDB** — banco analítico local (SQL embarcado)
- **SQL** — window functions, subqueries, DDL/DML
- **Jupyter Notebook** — desenvolvimento e validação incremental

---

## 📂 Estrutura do projeto
projeto/
├─ Landing/                    # arquivos CSV de origem
│  ├─ z0019_1.csv
│  └─ z0019_2.csv
├─ Scripts/                    # notebooks do pipeline
│  ├─ ingestao.ipynb           # camada Bronze
│  ├─ enriquecimento.ipynb     # camada Silver
│  └─ refinamento.ipynb        # camada Gold
├─ dados_duckdb.db             # banco DuckDB local
└─ README.md

---

## 📈 Principais aprendizados

- Aplicação prática de **arquitetura medalhão** (Bronze/Silver/Gold) em um caso simples e didático.
- Uso de **window functions** (`ROW_NUMBER`) para deduplicação baseada na ingestão mais recente — técnica comum em pipelines incrementais.
- Boas práticas de **rastreabilidade** (guardar nome do arquivo e data de ingestão desde a camada bruta).
- Modelagem de uma **tabela dimensional** simples como saída final do pipeline.

---

## 👩‍💻 Autora

**Ianca Rodrigues**
🔗 [LinkedIn](https://linkedin.com/in/IancaRodriguesDadosBi) | [GitHub](https://github.com/iancapa36-lab)
