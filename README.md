# Auris — Cuidando da vida através de dados e IA

> Projeto desenvolvido para o **Oracle + FIAP Challenge 2026**

O Auris é um painel analítico que reúne dados públicos de saúde do Brasil — capacidade hospitalar (leitos) e produção de atendimentos ambulatoriais do SUS — para apoiar a tomada de decisão em gestão hospitalar.

---

## 📊 Dashboard

O painel foi construído em **Power BI** e está dividido em duas páginas:

| Página | Conteúdo |
|---|---|
| **Painel Hospitalar** | Capacidade instalada de leitos no Brasil (existentes, SUS, não-SUS) por estado, região, tipo de leito e gestão |
| **Atendimentos SP** | Produção ambulatorial do SUS em São Paulo, por grupo de procedimento e evolução mensal |

📁 Arquivo do relatório: [`powerbi/Auris_Dashboard.pbix`](./powerbi/)

> Adicione aqui 1–2 screenshots do painel finalizado (`docs/screenshot_leitos.png`, `docs/screenshot_atendimentos.png`) para exibir a prévia direto no README:
> ```markdown
> ![Painel Hospitalar](docs/screenshot_leitos.png)
> ![Atendimentos SP](docs/screenshot_atendimentos.png)
> ```

---

## 🗂️ Fontes de dados

### 1. Leitos Hospitalares — CNES/DataSUS

Cadastro Nacional de Estabelecimentos de Saúde (CNES), módulo de Leitos — capacidade instalada de leitos hospitalares em todo o Brasil, por estabelecimento, tipo de leito, gestão e competência (jan–jul/2026).

- **Fonte oficial:** [CNES/DATASUS](https://cnes.datasus.gov.br/pages/downloads/arquivosBaseDados.jsp)
- **Arquivo bruto:** [`data/raw/LOOKER_LEITOS_BRASIL.csv`](./data/raw/)
- **Arquivos tratados (modelo estrela):** [`data/processed/`](./data/processed/)
  - `Fato_Leitos.csv` — 362.844 registros (CNES, município, UF, competência, tipo de leito, gestão, quantidades)
  - `Dim_UF.csv` — estado e região
  - `Dim_TipoLeito.csv` — Cirúrgico, Clínico, Obstétrico, Pediátrico, Outras Especialidades, Hospital Dia, Complementar
  - `Dim_Gestao.csv` — Municipal, Estadual, Dupla
  - `Dim_NivelDependencia.csv` — Individual, Mantida
  - `Dim_Tempo.csv` — calendário contínuo (jan–jul/2026)

**Tratamento aplicado:** remoção de 13 colunas 100% vazias/irrelevantes, remoção de registros com todos os leitos zerados, tipagem numérica, decodificação de campos categóricos (UF→Região, tipo de leito, gestão), modelagem em esquema estrela.

### 2. Produção Ambulatorial — SIA/SUS (São Paulo)

Sistema de Informações Ambulatoriais do SUS (SIA/SUS) — quantidade de atendimentos/procedimentos ambulatoriais realizados no estado de São Paulo, por procedimento e mês (abr–jul/2026).

- **Fonte oficial:** [TABNET DATASUS — Produção Ambulatorial SP](http://tabnet.datasus.gov.br/cgi/tabcgi.exe?sia/cnv/qasp.def)
- **Arquivo bruto:** [`data/raw/sia_cnv_qasp.csv`](./data/raw/)
- **Arquivos tratados:** [`data/processed/`](./data/processed/)
  - `Fato_Atendimentos_SP.csv` — 9.988 registros (procedimento, grupo SIGTAP, competência, quantidade de atendimentos)
  - `Dim_Procedimento.csv` — catálogo de 2.497 procedimentos únicos, com código e grupo SIGTAP

**Tratamento aplicado:** remoção de rodapé/notas do export do TABNET, separação de código e descrição do procedimento, classificação por grupo oficial SIGTAP, conversão de formato largo (uma coluna por mês) para formato longo, tratamento de valores nulos ("-" → 0).

> ⚠️ **Nota de qualidade dos dados:** o SIA/SUS libera os dados dos últimos meses de forma incremental — competências recentes (ex: mês corrente) tendem a aparecer com volume artificialmente baixo, pois o processamento/consolidação ainda está em andamento. Isso deve ser considerado ao interpretar a evolução mensal no dashboard.

---

## 🧱 Estrutura do repositório

```
auris/
├── README.md
├── data/
│   ├── raw/                    # arquivos originais, sem tratamento
│   │   ├── LOOKER_LEITOS_BRASIL.csv
│   │   └── sia_cnv_qasp.csv
│   └── processed/              # dados tratados, prontos para o Power BI
│       ├── Fato_Leitos.csv
│       ├── Dim_UF.csv
│       ├── Dim_TipoLeito.csv
│       ├── Dim_Gestao.csv
│       ├── Dim_NivelDependencia.csv
│       ├── Dim_Tempo.csv
│       ├── Fato_Atendimentos_SP.csv
│       └── Dim_Procedimento.csv
├── powerbi/
│   └── Auris_Dashboard.pbix
└── docs/
    ├── screenshot_leitos.png
    └── screenshot_atendimentos.png
```

---

## 🔧 Como reproduzir

1. Clone o repositório:
   ```bash
   git clone https://github.com/<seu-usuario>/auris.git
   ```
2. Abra `powerbi/Auris_Dashboard.pbix` no **Power BI Desktop**.
3. Caso precise reconectar as fontes de dados, aponte para os arquivos em `data/processed/`.
4. Os relacionamentos do modelo (esquema estrela) já estão configurados no arquivo `.pbix`.

---

## 🏆 Sobre o desafio

Projeto desenvolvido para o **Oracle + FIAP Challenge 2026**, com foco em transformar dados públicos de saúde em informação acionável para gestão hospitalar.

## 📄 Licença

Os dados utilizados são públicos, disponibilizados pelo Ministério da Saúde (DATASUS/CNES/SIA-SUS). Consulte os termos de uso em [dados.gov.br](https://dados.gov.br).
