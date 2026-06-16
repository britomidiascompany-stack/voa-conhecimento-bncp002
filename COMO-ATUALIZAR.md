# Como atualizar o relatório BNCP002 (automação)

Este repositório (`voa-conhecimento-bncp002`) hospeda a **página do lançamento BNCP002**.
A atualização roda **2× ao dia — 09:00 e 16:00 (America/Sao_Paulo)** e regenera os números do `index.html` com dados frescos do Meta Ads.

## Fonte de dados (Meta Ads MCP)
- **Conta:** `528479071959953`
- **Filtro de campanha:** `campaign.name` CONTAINS `BNCP002` (são 3 campanhas de captação CBO: `[01]`, `[02]`, `[03]`)
- **Período:** `date_preset: maximum` (lançamento começou em 13/Jun/2026)

## Queries necessárias
1. **Diário (tabela por dia)** — nível `campaign`, `time_increment: 1`, `maximum`, campos:
   `spend, impressions, reach, frequency, cpm, clicks, ctr, actions:link_click, cost_per_link_click, lead, 3_second_video_plays`
   → somar as 3 campanhas por dia.
2. **Visualizações de página por dia** — nível `campaign`, `time_increment: 1`, campos `spend, cost_per_action_type`
   → usar `cost_per_action_type:landing_page_view`.
3. **Criativos** — nível `ad`, `maximum`, `sort: lead_descending`, `limit: 60`, campos:
   `name, spend, impressions, clicks, ctr, actions:link_click, cost_per_link_click, lead, cost_per_result`
   → **agregar por NOME do criativo** (cada criativo roda nas 3 campanhas = 3 linhas; somar).
4. **Vis. de página por criativo** — nível `ad`, `maximum`, campos `name, spend, cost_per_action_type`.
5. **Alcance/frequência do período** — nível `campaign`, `maximum` (sem time_increment), campos `reach, frequency, impressions`.

## Cálculos (fórmulas exatas)
- **Investimento c/ imposto** = investimento × **1,1383** (imposto Meta de 13,83%)
- **CPM** = (investimento ÷ impressões) × 1000
- **Frequência** = impressões ÷ alcance (alcance do período é desduplicado — NÃO somar dias)
- **Hook Rate** = reproduções de 3s ÷ impressões
- **CPC no Link** = investimento ÷ cliques no link
- **CTR (todos)** = cliques totais ÷ impressões
- **CTR do Link** = cliques no link ÷ impressões
- **Visualizações de Página** = investimento ÷ `cost_per_action_type:landing_page_view` (somar por instância)
- **Connect Rate** = visualizações de página ÷ cliques no link
- **Custo por Vis. de Página** = investimento ÷ visualizações de página
- **CPL** = investimento ÷ leads
- **CPL c/ imposto** = CPL × 1,1383
- **Meta de CPL** = R$ 1,75 (fixo)

## O que atualizar no index.html
- Cards de resumo, funil (8 estágios), tabela diária (transposta) e tabela de criativos.
- **Criativos:** ordenar do que MAIS converteu pro que MENOS (por leads). Nome inteiro + link clicável pro Drive: `https://drive.google.com/drive/folders/1gNcfytaSq9JTKEaBk57p8p0kvW7Vdhqa`
- Atualizar a data no header/rodapé e marcar o dia corrente como **parcial** se a campanha ainda estiver rodando.
- Cores da marca: **preto, roxo, branco** (fundo escuro). NÃO mudar o layout — só os números/textos.

## Publicar
```
git add index.html
git commit -m "chore: atualização automática do relatório BNCP002"
git push origin main
```
O GitHub Pages republica sozinho em ~1 min.
