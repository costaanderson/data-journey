# Quick Start - Como Usar o Notebook

## Execução Rápida (Recomendado)

### 1. Abra o Notebook
```
Analytics_copel_FC_v2 .ipynb
```

### 2. Execute Tudo
```
Kernel → Restart & Run All Cells
```
Tempo estimado: 5-10 minutos (busca de mercado é a etapa mais lenta).

### 3. Observe os Resultados
- 3 gráficos de radar (DF, MF, FW)
- 15 jogadores selecionados (Top 5 por posição)
- Valores de mercado atualizados
- Tabela de resumo executivo final

---

## Fluxo Passo a Passo

### Fase 1: Setup
```python
# Células 3-6: Imports + configurações globais
IDADE_MIN, IDADE_MAX, MINUTOS_MIN, TOP_N, CACHE_VALIDADE_DIAS
```

### Fase 2: Dados
```python
# Células 7-12: Ler CSV, selecionar colunas, tratar NaN, filtros base
```

### Fase 3: Funções Auxiliares
```python
# Células 13-14: buscar_valor_mercado() e buscar_mercado_top()
```

### Fase 4: Rankings e Gráficos
```python
# Células 15-23: Zagueiros — filtro, score, radar, mercado
# Células 24-30: Volantes  — filtro, score, radar, mercado
# Células 31-37: Atacantes — filtro, score, radar, mercado
```

### Fase 5: Resumo (< 1 segundo)
```python
# Célula 40: Resumo executivo com as 3 tabelas consolidadas
```

---

## Interpretando os Resultados

### Gráfico de Radar
- Cada eixo representa uma métrica normalizada (0-100%)
- Cada linha colorida = um jogador
- Categorias variam por posição:
  - **DF**: PrgP, Cmp%, Tkl, TklW, Int, Score
  - **MF**: PrgP, Cmp%, TklW, Int, Score
  - **FW**: xG, PPA, KP, TklW, Int, Score

### Tabela de Resumo
| Coluna | Significado |
|--------|-------------|
| Player | Nome do jogador |
| Squad | Clube atual |
| Age | Idade |
| Score_DF/MF/FW | Pontuação composta (maior = melhor) |
| Valor_Mercado | Cotação no Transfermarkt (€) |
| Data_Contrato | Término do contrato (N/A se não encontrado) |

---

## Resultados Atuais (Abril 2026)

### Melhor Zagueiro
**Moisés Caicedo** (Chelsea) — Score: 436.35 — €110M

### Melhor Volante
**Pedri** (Barcelona) — Score: 676.72 — €150M

### Melhor Atacante
**Álex Baena** (Villarreal) — Score: 276.35 — €45M

---

## Customizações Possíveis

### Alterar Faixa de Idade (Célula 6)
```python
IDADE_MIN = 18  # era 20
IDADE_MAX = 28  # era 26
```

### Alterar Número de Jogadores (Célula 6)
```python
TOP_N = 10  # era 5
```

### Alterar Thresholds de Performance (Célula 17)
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf["PrgP"] > 15) &   # Reduza para mais jogadores
    (df_filtred_df_mf["Cmp%"] >= 70) &
    (df_filtred_df_mf["Tkl"] >= 15) &
    (df_filtred_df_mf["TklW"] >= 10)
]
```

### Exportar Resultados
```python
df_top_df.to_csv('top_zagueiros.csv', index=False)
df_top_mf.to_csv('top_volantes.csv', index=False)
df_top_fw.to_csv('top_atacantes.csv', index=False)
```

---

## Erros Comuns e Soluções

### KeyError nas colunas do gráfico de radar
Execute as células em ordem. Use `Kernel → Restart & Run All Cells`.

### Jogador não encontrado no Transfermarkt
Normal. O pipeline continua com `N/A`. Verifique a grafia do nome no CSV.

### Timeout na conexão
Verifique a internet. Execute novamente a célula de busca de mercado.

### Valor_Mercado = N/A
Normal para jogadores menos conhecidos ou com nome diferente no Transfermarkt.

---

## Fórmulas de Score

### Zagueiro (Score_DF)
```
TklW × 2.0 + Int × 1.5 + Cmp% × 0.5 + PrgP × 0.8
```

### Volante (Score_MF)
```
PrgP × 1.5 + Cmp% × 0.8 + TklW × 1.2 + Int × 1.0
```

### Atacante (Score_FW)
```
xG × 1.5 + KP × 1.5 + PPA × 1.3 + TklW × 0.8 + Int × 0.8
```

---

**Última atualização**: Abril 2026
