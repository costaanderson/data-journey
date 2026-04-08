# Estrutura Completa do Notebook

## Visão Geral das 40 Células

```
SEÇÃO 1: CABEÇALHO E PIPELINE
├── Célula 1  (Markdown): Título Principal
└── Célula 2  (Markdown): Diagrama Visual do Pipeline

SEÇÃO 2: IMPORTS E CONFIGURAÇÕES
├── Célula 3  (Markdown): "1. Instalação de Dependências"
├── Célula 4  (Code):     Imports + configurações pandas
├── Célula 5  (Markdown): "1. Configurações"
└── Célula 6  (Code):     Parâmetros ajustáveis (IDADE_MIN, TOP_N, etc.)

SEÇÃO 3: LEITURA E TRATAMENTO DE DADOS
├── Célula 7  (Markdown): "2. Leitura e Tratamento de Dados"
├── Célula 8  (Code):     Ler CSV (165 → 25 colunas)
├── Célula 9  (Code):     Selecionar colunas relevantes
├── Célula 10 (Code):     Tratar NaN (apenas jogadores com Min > 0)
├── Célula 11 (Code):     Código legado fillna (comentado)
└── Célula 12 (Code):     Identificar posições únicas + filtros base

SEÇÃO 4: FUNÇÕES AUXILIARES
├── Célula 13 (Markdown): "3. Função Auxiliar - Busca de Mercado"
└── Célula 14 (Code):     buscar_valor_mercado() + buscar_mercado_top()

SEÇÃO 5: ANÁLISE DE ZAGUEIROS (DF)
├── Célula 15 (Markdown): "SELEÇÃO DE ZAGUEIROS"
├── Célula 16 (Markdown): "4. Seleção de Zagueiros (DF)"
├── Célula 17 (Code):     Filtros DF/MF + cálculo Score_DF e Score_MF
├── Célula 18 (Markdown): "SELEÇÃO DE ZAGUEIROS" (marcador)
├── Célula 19 (Code):     Top N Zagueiros por Score_DF
├── Célula 20 (Code):     Tabela com colunas de interesse
├── Célula 21 (Code):     plot_radar_chart() + gráfico DF
├── Célula 22 (Markdown): "Valor de Mercado dos Top Zagueiros"
└── Célula 23 (Code):     buscar_mercado_top() — Zagueiros

SEÇÃO 6: ANÁLISE DE VOLANTES (MF)
├── Célula 24 (Markdown): "5. Seleção de Volantes (MF)"
├── Célula 25 (Code):     Top N Volantes por Score_MF
├── Célula 26 (Code):     Tabela com colunas de interesse
├── Célula 27 (Code):     Redefinição dinâmica de plot_radar_chart()
├── Célula 28 (Code):     Gráfico de radar — Volantes
├── Célula 29 (Markdown): "Valor de Mercado dos Top Volantes"
└── Célula 30 (Code):     buscar_mercado_top() — Volantes

SEÇÃO 7: ANÁLISE DE ATACANTES (FW)
├── Célula 31 (Markdown): "6. Seleção de Atacantes (FW)"
├── Célula 32 (Code):     Filtros FW + cálculo Score_FW + Top N
├── Célula 33 (Code):     Métricas descritivas DF/MF (referência)
├── Célula 34 (Code):     Gráfico de radar — Atacantes
├── Célula 35 (Markdown): "Valor de Mercado dos Top Atacantes"
├── Célula 36 (Code):     buscar_mercado_top() — Atacantes
└── Célula 37 (Code):     Métricas descritivas FW

SEÇÃO 8: FINALIZAÇÃO
├── Célula 38 (Code):     Célula de exportação (comentada)
├── Célula 39 (Markdown): "7. Resumo Comparativo e Exportação"
└── Célula 40 (Code):     Resumo Executivo Final (3 tabelas)
```

---

## Fluxo de Dados

```
players_data_light-2024_2025.csv (165 colunas, ~5000 linhas)
            ↓
        Pandas Read
            ↓
    Seleção 25 colunas
            ↓
    Tratamento NaN (apenas Min > 0)
            ↓
    Filtro base: Idade 20-26, Min >= 900, sem GK
            ↓
    ┌──────────────┬──────────────┬──────────────┐
    ↓              ↓              ↓
  DF/MF          DF/MF          FW/MF
  filtros        filtros        filtros
  Score_DF       Score_MF       Score_FW
  Top N DF       Top N MF       Top N FW
  Radar DF       Radar MF       Radar FW
  Mercado DF     Mercado MF     Mercado FW
    └──────────────┴──────────────┴──────────────┘
            ↓
    Resumo Executivo (3 tabelas)
```

---

## Gráficos de Radar

### Zagueiros (DF)
- Colunas: `PrgP`, `Cmp%`, `Tkl`, `TklW`, `Int`, `Score`
- `Score_DF` renomeado para `Score` antes de plotar

### Volantes (MF)
- Colunas: `PrgP`, `Cmp%`, `TklW`, `Int`, `Score`
- `Score_MF` renomeado para `Score` antes de plotar

### Atacantes (FW)
- Colunas: `xG`, `PPA`, `KP`, `TklW`, `Int`, `Score`
- `Score_FW` renomeado para `Score` antes de plotar

A função `plot_radar_chart(data, title)` deriva as categorias dinamicamente das colunas do DataFrame recebido (excluindo `Player`).

---

## Configurações Ajustáveis (Célula 6)

```python
IDADE_MIN = 20
IDADE_MAX = 26
MINUTOS_MIN = 900
TOP_N = 5                  # Quantos jogadores por posição
CACHE_VALIDADE_DIAS = 30   # Dias antes de re-buscar valor de mercado
```

---

## Filtros por Posição

### DF e MF (Célula 17)
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf["PrgP"] > 20) &    # Passes progressivos
    (df_filtred_df_mf["Cmp%"] >= 75) &   # Precisão de passes
    (df_filtred_df_mf["Tkl"] >= 20) &    # Total de tackles
    (df_filtred_df_mf["TklW"] >= 15)     # Tackles vencidos
]
```

### FW (Célula 32)
```python
df_stats_fw = df_filtred_fw[
    (df_filtred_fw['PrgP'] > 40) &
    (df_filtred_fw['xG'] >= 1.5) &
    (df_filtred_fw['KP'] >= 15) &
    (df_filtred_fw['PPA'] >= 10) &
    (df_filtred_fw['TklW'] >= 15) &
    (df_filtred_fw['Int'] >= 10)
]
```

---

## Busca de Mercado

- Implementação própria via `requests` + `BeautifulSoup` (scraping do Transfermarkt)
- Cache local em `data/market_cache.json`
- Validade do cache: 30 dias (configurável via `CACHE_VALIDADE_DIAS`)
- Em caso de falha de rede: retorna `N/A` sem interromper o pipeline

---

## Duração Estimada

| Etapa | Duração |
|-------|---------|
| Imports e configuração | < 5s |
| Carregamento dos dados | < 5s |
| Filtros e scores | < 5s |
| Gráficos radar (3x) | < 5s |
| Busca de mercado (15 jogadores) | 3-5 min (1ª vez) / < 1s (cache) |
| Resumo executivo | < 1s |
| **TOTAL** | **~5-10 min** |

---

## Exportação de Resultados

```python
# CSV
df_top_df.to_csv('top_5_zagueiros.csv', index=False)
df_top_mf.to_csv('top_5_volantes.csv', index=False)
df_top_fw.to_csv('top_5_atacantes.csv', index=False)

# Excel
df_top_df.to_excel('top_5_zagueiros.xlsx', index=False)

# JSON
df_top_df.to_json('top_5_zagueiros.json', orient='records')
```

---

**Versão**: 2.0 | **Testado**: Abril 2026 | **Status**: Pronto para Produção
