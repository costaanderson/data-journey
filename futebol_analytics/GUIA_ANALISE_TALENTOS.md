# 📊 Guia de Uso - Análise de Talentos COPEL FC

## 🎯 Objetivo
Este notebook realiza uma análise completa de jogadores em 3 posições estratégicas (Zagueiros, Volantes e Atacantes), gerando rankings baseados em performance estatística e buscando informações de mercado via Transfermarkt.

---

## 🔄 Fluxo de Trabalho Completo

### 1️⃣ **Instalação e Importação**
- Instala a biblioteca `transfermarkt-wrapper` (se necessária)
- Importa todas as dependências (pandas, matplotlib, TMKT, etc.)

### 2️⃣ **Leitura e Tratamento de Dados**
```python
# O notebook carrega automaticamente:
df = pd.read_csv('data/players_data_light-2024_2025.csv')
```
- Seleciona 24 colunas relevantes para análise
- Remove valores NaN em métricas de performance
- Filtra jogadores entre 20-26 anos com +900 minutos de jogo

### 3️⃣ **Identificação de Posições**
Posições disponíveis no dataset:
- **DF** = Defensores/Zagueiros
- **MF** = Meio-campistas/Volantes
- **FW** = Atacantes
- **Combinações**: DF,MF | MF,FW | FW,DF

### 4️⃣ **Filtros por Posição**

#### **🛡️ ZAGUEIROS (DF)**
Critérios de seleção:
- Passes Progressivos (PrgP) > 20
- Precisão de Passes (Cmp%) ≥ 75%
- Total de Interceptações (Tkl) ≥ 20
- Interceptações Vencidas (TklW) ≥ 15

Fórmula de Score:
```
Score = PrgP + (Cmp% - 75) × 0.4 + TklW × 0.6
```

#### **🎯 VOLANTES (MF)**
Critérios de seleção:
- Passes Progressivos (PrgP) > 20
- Precisão de Passes (Cmp%) ≥ 75%
- Total de Interceptações (Tkl) ≥ 20
- Interceptações Vencidas (TklW) ≥ 15

Fórmula de Score:
```
Score = PrgP + (Cmp% - 75) × 0.4 + TklW × 0.6
```

#### **⚽ ATACANTES (FW)**
Critérios de seleção:
- Passes Progressivos (PrgP) > 40
- Expected Goals (xG) ≥ 1.5
- Passes Chave (KP) ≥ 15
- Passes para Ações de Perigo (PPA) ≥ 10
- Interceptações Vencidas (TklW) ≥ 15
- Interceptações (Int) ≥ 10

Fórmula de Score:
```
Score = xG × 1.3 + PPA × 1.5 + KP × 1.5 + TklW × 1.0 + Int × 1.2
```

### 5️⃣ **Gráficos de Radar**
Visualiza os 5 melhores jogadores em cada posição com:
- Normalização de 0 a 1 (100%)
- Comparação lado a lado das competências

### 6️⃣ **Busca de Informações de Mercado**
Para cada top 5, o notebook busca:
- **Nome do jogador**
- **ID do Transfermarkt**
- **Valor de Mercado Atual**
- **Moeda do Valor**
- **Data de Término do Contrato**
- **Clube Atual**
- **Posição**
- **Pé Preferido**
- **Idade**
- **Altura**

---

## 💾 Dados Usados

### Colunas Principais:
| Coluna | Descrição |
|--------|-----------|
| Player | Nome do jogador |
| Nation | Nacionalidade |
| Pos | Posição(ões) |
| Squad | Clube |
| Age | Idade |
| Min | Minutos jogados |
| Cmp% | % de passes completados |
| PrgP | Passes Progressivos |
| Tkl | Total de interceptações |
| TklW | Interceptações vencidas |
| Int | Interceptações |
| xG | Expected Goals (esperado) |
| xAG | Expected Assisted Goals |
| KP | Key Passes (passes chave) |
| PPA | Passes para ações de perigo |

---

## 🚀 Como Usar

### Execução Rápida (Recomendado)
1. Abra o notebook `Analytics_copel_FC copy.ipynb`
2. Pressione `Ctrl+Shift+Enter` ou use "Run All Cells"
3. Aguarde a execução completa (~2-3 minutos)

### Execução Célula por Célula
1. **Instalação**: Rode célula de instalação
2. **Importações**: Rode imports
3. **Dados**: Carregue o CSV
4. **Tratamento**: Execute tratamento de dados
5. **Análise**: Execute filtros
6. **Resultados**: Visualize gráficos e tabelas

### Customização Possível

#### Alterar Critérios de Idade
```python
df_filtred = df_selected[
    (df_selected['Age'].between(18, 30)) &  # Altere os valores
    (df_selected['Min'] >= 900)
]
```

#### Alterar Thresholds de Performance
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf['PrgP'] > 15) &  # Reduza o threshold
    (df_filtred_df_mf['Cmp%'] >= 70) &
]
```

#### Alterar Número de Top Jogadores
```python
df_top_df = df_stats_df_mf.nlargest(10, 'Score')  # Pegue top 10 em vez de 5
```

---

## 📈 Saídas Esperadas

### 1. Tabelas
- DataFrame com top 5 jogadores por posição
- Colunas: Player, Squad, Age, Score, Valor_Mercado, Data_Contrato

### 2. Gráficos Radar
- Visualização comparativa de 5 jogadores
- Métricas normalizadas de 0-100%

### 3. Informações de Mercado
- Valor de mercado atual (em € EUR)
- Contratos ativos
- Clubes atuais

### 4. Resumo Executivo
- Tabela consolidada com estatísticas + valores de mercado
- Separada por posição (DF, MF, FW)

---

## ⚠️ Notas Importantes

1. **Dados do CSV**: Atualizado para 2024-2025
2. **Transfermarkt**: Requer internet para buscar informações de mercado
3. **Tempo de Busca**: A busca de mercado pode levar 30-60 segundos
4. **Precisão**: Baseada em nomes de jogadores - pode haver variações

---

## 🔍 Troubleshooting

### Erro: "Jogador não encontrado"
- Verifique a grafia exata do nome no arquivo CSV
- Alguns jogadores podem não estar no Transfermarkt

### Erro: "Timeout na conexão"
- Verifique a conexão com internet
- Tente novamente em alguns minutos

### Dados NaN nas Colunas
- Normal para jogadores com baixa participação
- O tratamento substitui por 0 para fair comparison

---

## 📊 Exemplo de Resultados

```
INFORMAÇÕES DE MERCADO - TOP 5 ZAGUEIROS

Nome: Joško Gvardiol
Clube: Manchester City
Valor de Mercado: €75,000,000
Contrato até: 2028-06-30
Score: 293.48

Nome: Moisés Caicedo
Clube: Chelsea
Valor de Mercado: €80,000,000
Contrato até: 2032-09-15
Score: 264.68
```

---

## 📧 Contato & Feedback
Para sugestões de melhoria ou correções, abra uma issue no repositório.

---

**Última Atualização**: Novembro 2025
