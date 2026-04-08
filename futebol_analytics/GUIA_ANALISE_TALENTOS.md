# Guia de Uso - Análise de Talentos COPEL FC

## Objetivo
Este notebook realiza uma análise completa de jogadores em 3 posições estratégicas (Zagueiros, Volantes e Atacantes), gerando rankings baseados em performance estatística e buscando informações de mercado via Transfermarkt.

---

## Fluxo de Trabalho

### 1. Importação e Configuração
- Importa pandas, matplotlib, requests, BeautifulSoup
- Define parâmetros globais: `IDADE_MIN`, `IDADE_MAX`, `MINUTOS_MIN`, `TOP_N`, `CACHE_VALIDADE_DIAS`

### 2. Leitura e Tratamento de Dados
```python
df = pd.read_csv('data/players_data_light-2024_2025.csv', index_col='Rk')
```
- Seleciona 25 colunas relevantes para análise
- Substitui NaN por 0 apenas para jogadores com Min > 0
- Filtra jogadores entre 20-26 anos com >= 900 minutos, excluindo goleiros

### 3. Identificação de Posições
- **DF** = Defensores/Zagueiros
- **MF** = Meio-campistas/Volantes
- **FW** = Atacantes
- **Combinações**: DF,MF | MF,FW | FW,DF

### 4. Filtros por Posição

#### ZAGUEIROS (DF)
Critérios mínimos de performance:
- PrgP > 20 (passes progressivos)
- Cmp% >= 75% (precisão de passes)
- Tkl >= 20 (total de tackles)
- TklW >= 15 (tackles vencidos)

Fórmula de Score:
```
Score_DF = TklW × 2.0 + Int × 1.5 + Cmp% × 0.5 + PrgP × 0.8
```

#### VOLANTES (MF)
Mesmos critérios mínimos de performance que DF.

Fórmula de Score:
```
Score_MF = PrgP × 1.5 + Cmp% × 0.8 + TklW × 1.2 + Int × 1.0
```

#### ATACANTES (FW)
Critérios mínimos de performance:
- PrgP > 40 (passes progressivos)
- xG >= 1.5 (expected goals)
- KP >= 15 (passes chave)
- PPA >= 10 (passes para ações de perigo)
- TklW >= 15 (tackles vencidos)
- Int >= 10 (interceptações)

Fórmula de Score:
```
Score_FW = xG × 1.5 + KP × 1.5 + PPA × 1.3 + TklW × 0.8 + Int × 0.8
```

### 5. Gráficos de Radar
- Função `plot_radar_chart(data, title)` com categorias dinâmicas
- Normalização de 0 a 1 (100%) para comparação justa
- Um gráfico por posição com os Top N jogadores

### 6. Busca de Informações de Mercado
Para cada jogador do top N, o notebook busca via Transfermarkt:
- Valor de Mercado (€)
- Data de término do contrato
- Clube atual

Cache local em `data/market_cache.json` (validade: 30 dias).

---

## Dados Usados

### Colunas Principais
| Coluna | Descrição |
|--------|-----------|
| Player | Nome do jogador |
| Nation | Nacionalidade |
| Pos | Posição(ões) |
| Squad | Clube |
| Age | Idade |
| Min | Minutos jogados |
| Cmp% | % de passes completados |
| PrgP | Passes progressivos |
| Tkl | Total de tackles |
| TklW | Tackles vencidos |
| Int | Interceptações |
| xG | Expected Goals |
| KP | Passes chave |
| PPA | Passes para ações de perigo |

---

## Como Usar

### Execução Completa (Recomendado)
1. Abra `Analytics_copel_FC_v2 .ipynb`
2. `Kernel → Restart & Run All Cells`
3. Aguarde ~5-10 minutos

### Customização

#### Alterar Critérios de Idade (Célula 6)
```python
IDADE_MIN = 18  # era 20
IDADE_MAX = 28  # era 26
```

#### Alterar Thresholds de Performance (Célula 17)
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf["PrgP"] > 15) &   # Reduza para mais jogadores
    (df_filtred_df_mf["Cmp%"] >= 70) &
    (df_filtred_df_mf["Tkl"] >= 15) &
    (df_filtred_df_mf["TklW"] >= 10)
]
```

#### Alterar Número de Jogadores (Célula 6)
```python
TOP_N = 10  # era 5
```

---

## Saídas Esperadas

### Tabelas
- `df_top_df`: Top N zagueiros com colunas Player, Nation, Pos, Squad, Comp, Age, Min, Cmp, Cmp%, PrgP, Tkl, TklW, Int, PrgC, Score_DF
- `df_top_mf`: Top N volantes com colunas Player, Nation, Pos, Squad, Comp, Age, Min, Cmp, Cmp%, PrgP, Tkl, TklW, Int, PrgC, 1/3, Score_MF
- `df_top_fw`: Top N atacantes

### Gráficos
- 3 gráficos de radar (DF, MF, FW)

### Resumo Executivo
- Tabela consolidada com estatísticas + Valor_Mercado + Data_Contrato

---

## Troubleshooting

### Erro: KeyError nas colunas do radar
Certifique-se de executar as células em ordem. A função `plot_radar_chart` deve ser definida antes das chamadas de MF e FW.

### Erro: Jogador não encontrado no Transfermarkt
Normal. O pipeline continua com `N/A` nos campos de mercado.

### Erro: Timeout na conexão
Verifique a conexão com internet. O cache evita re-buscas desnecessárias.

### Dados NaN nas colunas
Normal para jogadores com baixa participação. O tratamento substitui por 0 para jogadores com Min > 0.

---

**Última Atualização**: Abril 2026
