# 🎬 Quick Start - Como Usar o Notebook

## ⚡ Execução Rápida (Recomendado)

### 1. Abra o Notebook
```
/workspaces/data-journey/futebol_analytics/Analytics_copel_FC copy.ipynb
```

### 2. Execute Tudo de Uma Vez
- Pressione `Ctrl+Shift+Enter` (ou no macOS: `Cmd+Shift+Enter`)
- OU: Clique em "Run All" no menu do VS Code
- Tempo estimado: 5-10 minutos

### 3. Observe os Resultados
O notebook vai gerar:
- ✅ 3 gráficos de radar (um para cada posição)
- ✅ 15 jogadores top 5 selecionados
- ✅ Valores de mercado atualizados
- ✅ Tabela consolidada final

---

## 🔄 Fluxo Passo a Passo

Se preferir executar célula por célula:

### **Fase 1: Setup (≈1 min)**
```python
# Célula 1: Instalar dependências
# Célula 2: Importar bibliotecas
```

### **Fase 2: Dados (≈1 min)**
```python
# Célula 4: Carregar CSV
# Célula 5: Selecionar colunas
# Célula 6: Tratar NaN
# Célula 7: Identificar posições
```

### **Fase 3: Filtros (≈1 min)**
```python
# Célula 8: Filtros básicos (idade, minutos)
# Célula 9: Filtro por posição + Scoring
```

### **Fase 4: Rankings (≈1 min)**
```python
# Célula 11-12: Top 5 Zagueiros
# Célula 13-15: Gráfico Radar Zagueiros
# Célula 17-18: Top 5 Volantes
# Célula 19-21: Gráfico Radar Volantes
# Célula 23-26: Top 5 Atacantes
# Célula 27-29: Gráfico Radar Atacantes
```

### **Fase 5: Mercado (≈3-5 min)** ⚠️ [MAIS LENTO]
```python
# Célula 16: Buscar mercado - Zagueiros (30-60s)
# Célula 22: Buscar mercado - Volantes (30-60s)
# Célula 30: Buscar mercado - Atacantes (30-60s)
```

### **Fase 6: Resumo (≈1 seg)**
```python
# Célula 31: Exibir resumo executivo
```

---

## 📊 Interpretando os Resultados

### Gráfico de Radar
- **Eixos**: PrgP, Cmp%, Tkl, TklW, Int, Score
- **Zona colorida**: Representa a performance normalizada (0-100%)
- **Cada linha**: Um jogador diferente
- **Formato hexagonal**: Comparação visual de 6 dimensões

### Tabela de Resumo
Coluna | Significado
-------|------------
Player | Nome do jogador
Squad | Clube atual
Age | Idade atual
Score | Pontuação composta (quanto maior, melhor)
Valor_Mercado (€) | Cotação no Transfermarkt em Euros
Contrato | Data de término (N/A = não capturado)

### Exemplos de Top 5

#### 🛡️ Melhor Zagueiro
- **Joško Gvardiol** (Manchester City)
- Score: 293.48
- Valor: €75M
- Performance: Passes precisos, bom em defesa

#### 🎯 Melhor Volante
- **Pedri** (Barcelona)
- Score: 385.36
- Valor: €140M
- Performance: Passes progressivos, criatividade

#### ⚽ Melhor Atacante
- **Álex Baena** (Villarreal)
- Score: 301.13
- Valor: €55M
- Performance: Gols esperados, passes chave

---

## 🎯 Customizações Possíveis

### Mudar Faixa de Idade
Célula 8, linha 2:
```python
(df_selected['Age'].between(18, 30))  # Mude para sua faixa
```

### Aumentar/Diminuir Thresholds
Célula 9, linhas 5-9:
```python
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf['PrgP'] > 15) &      # ← Reduza para mais jogadores
    (df_filtred_df_mf['Cmp%'] >= 70) &     # ← Reduza para mais permissivo
    (df_filtred_df_mf['Tkl'] >= 15) &
    (df_filtred_df_mf['TklW'] >= 10)
]
```

### Selecionar Top 10 em vez de Top 5
Célula 11:
```python
df_top_df = df_stats_df_mf[df_stats_df_mf['Pos'].str.contains('DF')].nlargest(10, 'Score')
```

---

## ⚠️ Possíveis Erros e Soluções

### Erro: "Jogador não encontrado"
**Causa**: Nome não existe no Transfermarkt
**Solução**: Verifique a grafia no CSV original

### Erro: "Timeout na conexão"
**Causa**: Conexão lenta ou Transfermarkt offline
**Solução**: Aguarde alguns minutos e execute novamente

### Valor_Mercado = NaN
**Causa**: Jogador não encontrado ou sem cotação
**Solução**: Normal para jogadores menos famosos

### Gráfico não aparece
**Causa**: Matplotlib não renderizando
**Solução**: Reinicie o kernel e execute novamente

---

## 📈 Próximos Passos Sugeridos

1. **Exportar Resultados**
   ```python
   df_top_df.to_csv('top_5_zagueiros.csv', index=False)
   df_top_mf.to_csv('top_5_volantes.csv', index=False)
   df_top_fw.to_csv('top_5_atacantes.csv', index=False)
   ```

2. **Visualizar Clube por Clube**
   ```python
   df_top_df.groupby('Squad').size()  # Quantos por clube
   ```

3. **Análise de Idade**
   ```python
   df_top_df['Age'].mean()  # Idade média
   ```

4. **Criar Relatório PDF**
   - Use bibliotecas como `reportlab` ou `matplotlib.backends.backend_pdf`

---

## 🎓 Entendendo a Metodologia

### Score de Zagueiros e Volantes
```
Score = PrgP + (Cmp% - 75) × 0.4 + TklW × 0.6
```
- **PrgP**: Peso 1.0 (o mais importante)
- **Cmp%**: Bônus por precisão acima de 75%
- **TklW**: Peso 0.6 (interceptações vencidas)

### Score de Atacantes
```
Score = xG × 1.3 + PPA × 1.5 + KP × 1.5 + TklW × 1.0 + Int × 1.2
```
- **xG**: Gols esperados (peso 1.3)
- **PPA**: Passes para ações de perigo (peso 1.5)
- **KP**: Passes chave (peso 1.5)
- **TklW**: Interceptações vencidas (peso 1.0)
- **Int**: Interceptações (peso 1.2)

---

## 📞 Suporte

Dúvidas? Consulte:
1. `GUIA_ANALISE_TALENTOS.md` (Guia completo)
2. Comentários no notebook
3. Documentação do Transfermarkt: https://transfermarkt.com

---

**Última atualização**: Novembro 2025
