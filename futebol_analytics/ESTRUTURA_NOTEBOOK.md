# 📋 Estrutura Completa do Notebook

## 🎯 Visão Geral das 31 Células

```
SEÇÃO 1: CABEÇALHO E PIPELINE
├── Célula 1 (Markdown): Título Principal
├── Célula 2 (Markdown): Diagrama Visual do Pipeline
└── Célula 3 (Python): Instalação de Dependências

SEÇÃO 2: IMPORTS E SETUP
├── Célula 4 (Markdown): "## 1. Instalação de Dependências"
├── Célula 5 (Markdown): "## 2. Leitura e Tratamento de Dados"
└── Célula 6 (Python): Imports de Bibliotecas + Configurações

SEÇÃO 3: CARREGAMENTO DE DADOS
├── Célula 7 (Python): Ler CSV (165 → 24 colunas)
├── Célula 8 (Python): Selecionar Colunas Específicas
├── Célula 9 (Python): Tratar NaN com fillna(0)
├── Célula 10 (Python): Identificar Posições Únicas
├── Célula 11 (Python): Aplicar Filtros Básicos (idade, min)
└── Célula 12 (Python): Filtro DF+MF + Scoring

SEÇÃO 4: FUNÇÕES AUXILIARES
├── Célula 13 (Markdown): "## 3. Função Auxiliar - Busca de Mercado"
└── Célula 14 (Python): Funções buscar_informacoes_jogador() e buscar_top_jogadores_info()

SEÇÃO 5: ANÁLISE DE ZAGUEIROS
├── Célula 15 (Markdown): "## 4. Seleção de Zagueiros (DF)"
├── Célula 16 (Python): Selecionar Top 5 Zagueiros
├── Célula 17 (Python): Formatar Colunas (15 colunas)
├── Célula 18 (Python): Plotar Gráfico de Radar Zagueiros
├── Célula 19 (Markdown): "### Valor de Mercado dos Top 5 Zagueiros"
└── Célula 20 (Python): Buscar e Consolidar Mercado Zagueiros

SEÇÃO 6: ANÁLISE DE VOLANTES
├── Célula 21 (Markdown): "## 5. Seleção de Volantes (MF)"
├── Célula 22 (Python): Selecionar Top 5 Volantes
├── Célula 23 (Python): Info das Colunas
├── Célula 24 (Python): Tabela com Colunas de Interesse
├── Célula 25 (Python): Plotar Gráfico de Radar Volantes
├── Célula 26 (Markdown): "### Valor de Mercado dos Top 5 Volantes"
└── Célula 27 (Python): Buscar e Consolidar Mercado Volantes

SEÇÃO 7: ANÁLISE DE ATACANTES
├── Célula 28 (Markdown): "## 6. Seleção de Atacantes (FW)"
├── Célula 29 (Python): Filtro Atacantes + Scoring Específico
├── Célula 30 (Python): Métricas Descritivas
├── Célula 31 (Python): Plotar Gráfico de Radar Atacantes
├── Célula 32 (Markdown): "### Valor de Mercado dos Top 5 Atacantes"
└── Célula 33 (Python): Buscar e Consolidar Mercado Atacantes

SEÇÃO 8: FINALIZAÇÃO
├── Célula 34 (Python): Métricas Descritivas Atacantes
├── Célula 35 (Python): [Espaço para exportação]
├── Célula 36 (Markdown): "## 7. Resumo Comparativo e Exportação"
└── Célula 37 (Python): Resumo Executivo Final com 3 Tabelas
```

---

## 📊 Fluxo de Dados

```
players_data_light-2024_2025.csv (165 colunas, +5000 linhas)
            ↓
        Pandas Read
            ↓
    Seleção 24 colunas
            ↓
    Tratamento NaN
            ↓
┌─────────────────────────────┐
│  POSIÇÕES IDENTIFICADAS     │
├─────────────────────────────┤
│ DF (Defender)               │
│ MF (Midfielder)             │
│ FW (Forward)                │
│ + Combinações               │
└─────────────────────────────┘
            ↓
    Filtro Básico (idade, min)
            ↓
┌─────────────────────────────┐
│  3 PIPELINES PARALELOS      │
├─────────────────────────────┤
│ 1. ZAGUEIROS (DF)           │
│    ↓ Filtro Específico      │
│    ↓ Scoring: PrgP+Def      │
│    ↓ Top 5                  │
│    ↓ Gráfico Radar          │
│    ↓ Buscar Mercado         │
│                             │
│ 2. VOLANTES (MF)            │
│    ↓ (mesmo processo)       │
│                             │
│ 3. ATACANTES (FW)           │
│    ↓ (mesmo processo)       │
└─────────────────────────────┘
            ↓
    Consolidar Dados
            ↓
    Gerar Resumo
            ↓
    SAÍDA: 3 Tabelas + 3 Gráficos + Mercado
```

---

## 🎨 Gráficos Gerados

### 1. Radar Zagueiros
- Dimensões: PrgP, Cmp%, Tkl, TklW, Int, Score
- Jogadores: Top 5 DF
- Cores: 5 linhas diferentes
- Simetria: Hexágono

### 2. Radar Volantes
- Dimensões: PrgP, Cmp%, TklW, Int, Score, 1/3
- Jogadores: Top 5 MF
- Cores: 5 linhas diferentes
- Simetria: Hexágono

### 3. Radar Atacantes
- Dimensões: xG, PPA, KP, TklW, Int, Score
- Jogadores: Top 5 FW
- Cores: 5 linhas diferentes
- Simetria: Hexágono

---

## 📈 Saídas Esperadas

### Tabelas (DataFrames)
```
Tabela 1: df_top_df
Colunas: Player, Nation, Pos, Squad, Comp, Age, Min, Cmp, Cmp%, PrgP, Tkl, TklW, Int, PrgC, Score

Tabela 2: df_top_mf
Colunas: Player, Nation, Pos, Squad, Comp, Age, Min, Cmp, Cmp%, PrgP, Tkl, TklW, Int, PrgC, 1/3, Score

Tabela 3: df_top_fw
Colunas: Player, Nation, Pos, Squad, Comp, Age, Min, Cmp, Cmp%, PrgP, Tkl, TklW, Int, PrgC, 1/3, Score

Resumo Final: 3 tabelas consolidadas com Valor_Mercado (€) e Contrato
```

### Imagens
- `radar_zagueiros.png`
- `radar_volantes.png`
- `radar_atacantes.png`

### Dados Transfermarkt
Para cada jogador:
- Nome
- ID
- Valor de Mercado (€)
- Moeda
- Data Contrato
- Clube Atual
- Posição
- Pé Preferido
- Idade
- Altura

---

## 🔧 Configurações Ajustáveis

### Célula 8: Seleção de Colunas
```python
selected_columns = [
    'Player', 'Nation', 'Pos', 'Squad', 'Comp', 'Age', 'Min', '90s', 'Starts',
    'Cmp', 'Att', 'Cmp%', 'PrgP', '1/3', 'Tkl', 'TklW', 'Att 3rd', 'Int','PPA','KP',
    'PrgC', 'Att 3rd_stats_possession', 'Gls', 'Ast', 'xG', 'xAG'
]
# Adicione ou remova colunas aqui
```

### Célula 11: Filtros Básicos
```python
df_filtred = df_selected[
    (df_selected['Age'].between(20,26)) &        # ← Mude faixa de idade
    (df_selected['Min']>= 900) &                 # ← Mude minutos
    (df_selected['Pos']!= 'GK')                  # ← Excluir goleiros
]
```

### Célula 12: Filtros DF/MF
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf['PrgP'] > 20) &            # ← Threshold passes prog
    (df_filtred_df_mf['Cmp%'] >= 75) &           # ← Threshold precisão
    (df_filtred_df_mf['Tkl'] >= 20) &            # ← Threshold tackles
    (df_filtred_df_mf['TklW'] >= 15)             # ← Threshold tackles ganhos
]
```

### Célula 29: Filtros FW
```python
df_stats_fw = df_filtred_fw[
    (df_filtred_fw['PrgP']>40) &                 # ← Threshold passes prog
    (df_filtred_fw['xG'] >= 1.5) &               # ← Threshold xG
    (df_filtred_fw['KP'] >= 15) &                # ← Threshold key passes
    (df_filtred_fw['PPA'] >= 10) &               # ← Threshold PPA
    (df_filtred_fw['TklW'] >= 15) &
    (df_filtred_fw['Int'] >= 10)
]
```

---

## 🎯 Duração Estimada

| Etapa | Duração | Detalhes |
|-------|---------|----------|
| Instalação | 30s | transfermarkt-wrapper |
| Imports | 10s | Carregar bibliotecas |
| Dados | 1s | Ler CSV |
| Tratamento | 2s | Processamento |
| Filtros DF | 1s | Cálculos |
| Gráfico DF | 1s | Renderização |
| Busca Mercado DF | 60s | API Transfermarkt |
| Filtros MF | 1s | Cálculos |
| Gráfico MF | 1s | Renderização |
| Busca Mercado MF | 60s | API Transfermarkt |
| Filtros FW | 2s | Cálculos |
| Gráfico FW | 1s | Renderização |
| Busca Mercado FW | 60s | API Transfermarkt |
| Resumo | 1s | Consolidação |
| **TOTAL** | **≈5 min** | Se tudo correr bem |

---

## 🔐 Tratamento de Erros

### Célula 14: Funções com Try/Except
```python
try:
    players = await tmkt.player_search(nome_jogador)
    if not players:
        return {"erro": f"Jogador '{nome_jogador}' não encontrado"}
    # ... resto do código
except Exception as e:
    return {"erro": f"Erro ao buscar: {str(e)}"}
```

### Falhas Esperadas
- Jogador não encontrado no Transfermarkt → Continua com N/A
- Timeout de conexão → Tenta novamente
- Dados incompletos → Preenche com NaN

---

## 💾 Exportação de Resultados

### Excel
```python
df_top_df.to_excel('top_5_zagueiros.xlsx')
df_top_mf.to_excel('top_5_volantes.xlsx')
df_top_fw.to_excel('top_5_atacantes.xlsx')
```

### CSV
```python
df_top_df.to_csv('top_5_zagueiros.csv', index=False)
```

### JSON
```python
df_top_df.to_json('top_5_zagueiros.json')
```

### HTML
```python
df_top_df.to_html('top_5_zagueiros.html')
```

---

## 🚀 Executar Tudo

### Command Line
```bash
jupyter notebook /workspaces/data-journey/futebol_analytics/Analytics_copel_FC\ copy.ipynb
```

### VS Code
1. Abra o notebook
2. Pressione `Ctrl+Shift+Enter` (Run All)

---

**Versão**: 1.0
**Testado**: ✅ Novembro 2025
**Status**: Pronto para Produção
