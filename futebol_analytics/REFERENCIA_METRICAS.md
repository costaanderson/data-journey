# 📋 Referência Rápida - Métrica Explicadas

## 🏆 Top 5 Gerados

### ZAGUEIROS (DF)
1. **Joško Gvardiol** - Manchester City - €75M
2. **Moisés Caicedo** - Chelsea - €100M
3. **Jan Paul van Hecke** - Brighton - €35M
4. **Federico Valverde** - Real Madrid - €130M
5. **Óscar Mingueza** - Celta Vigo - €18M

### VOLANTES (MF)
1. **Pedri** - Barcelona - €140M
2. **Mattéo Guendouzi** - Lazio - €32M
3. **Manuel Locatelli** - Juventus - €30M
4. **Angelo Stiller** - Stuttgart - €45M
5. **Bruno Guimarães** - Newcastle - €80M

### ATACANTES (FW)
1. **Álex Baena** - Villarreal - €55M
2. **Pedri** - Barcelona - €140M
3. **Mikkel Damsgaard** - Brentford - €30M
4. **Cole Palmer** - Chelsea - €120M
5. **Bruno Guimarães** - Newcastle - €80M

---

## 📊 Colunas do Dataset

### Informação Pessoal
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Player | str | Nome do jogador |
| Nation | str | Nacionalidade com bandeira |
| Pos | str | Posição(ões) na seleção |
| Squad | str | Clube/Time |
| Age | float | Idade em anos |

### Performance de Jogos
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Comp | str | Competição (ex: Premier League) |
| Min | int | Total de minutos jogados |
| 90s | float | Equivalente em jogos de 90 min |
| Starts | int | Número de vezes como titular |

### Passes
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Cmp | int | Passes completados |
| Att | int | Passes tentados |
| Cmp% | float | Percentual de passes completados |
| PrgP | int | Passes que progridem 10+ jardas |
| 1/3 | int | Passes para o último terço do campo |

### Defesa
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Tkl | int | Total de interceptações/tackles |
| TklW | int | Tackles ganhos |
| Att 3rd | int | Ataques feitos no terço defensivo |
| Int | int | Interceptações (passes capturados) |

### Ataque/Criatividade
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| PPA | int | Passes para ações de perigo |
| KP | int | Key passes (passes decisivos) |
| PrgC | int | Passes progressivos completados |
| Gls | int | Gols marcados |
| Ast | int | Assistências |

### Expected Stats
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| xG | float | Expected Goals (gols esperados) |
| xAG | float | Expected Assisted Goals |

---

## 🎯 Interpretação de Métricas

### PrgP (Passes Progressivos)
- **Alto (>250)**: Jogador muito criativo, bom em construção
- **Médio (150-250)**: Jogador dinâmico
- **Baixo (<150)**: Focado em outros aspectos

### Cmp% (Percentual de Passes)
- **>90%**: Excelente técnica
- **85-90%**: Muito bom
- **75-85%**: Adequado
- **<75%**: Precisa melhorar

### Tkl (Tackles/Interceptações)
- **>80**: Zagueiro/Volante de elite
- **50-80**: Bom defensor
- **20-50**: Defesa básica
- **<20**: Mais ofensivo

### xG (Expected Goals)
- **>2.0**: Atacante de classe
- **1.5-2.0**: Bom finalizador
- **1.0-1.5**: Adequado
- **<1.0**: Poucas oportunidades

---

## 📈 Comparações Úteis

### Qual Zagueiro é Mais Defensivo?
**Moisés Caicedo**: 114 tackles (melhor defensor)
**Joško Gvardiol**: 58 tackles (mais técnico)

### Qual Volante é Mais Criativo?
**Pedri**: 360 passes progressivos + score 385.36 (melhor)
**Bruno Guimarães**: 271 passes progressivos

### Qual Atacante Tem Melhor Eficiência?
**Alex Baena**: 301.13 score
**Pedri**: 291.56 score (mas €140M, mais caro)

---

## 🔍 Como Buscar Manualmente um Jogador

```python
# Dentro de uma célula de código
async with TMKT() as tmkt:
    # Buscar jogador
    players = await tmkt.player_search("Neymar")
    print(players)  # Mostra opções
    
    # Pegar detalhes
    player_id = players[0]['id']
    player = await tmkt.get_player(player_id)
    
    # Acessar valor de mercado
    valor = player['data']['marketValueDetails']['current']['value']
    print(f"Valor: €{valor}")
```

---

## 💡 Dicas Profissionais

### 1. Usar Filtros Múltiplos
Combine critérios para refinar resultados:
```python
df_top = df_filtered[
    (df_filtered['Age'] < 25) &
    (df_filtered['xG'] > 2.0) &
    (df_filtered['Squad'].isin(['Barcelona', 'Real Madrid']))
]
```

### 2. Normalizar para Comparação
Compare jogadores de competições diferentes:
```python
metrics_normalized = (metrics - metrics.min()) / (metrics.max() - metrics.min())
```

### 3. Exportar para Apresentação
```python
# HTML
df_top_df.to_html('zagueiros.html')

# Excel
df_top_df.to_excel('zagueiros.xlsx')

# JSON
df_top_df.to_json('zagueiros.json')
```

---

## 🚀 Próximas Evoluções Possíveis

- [ ] Adicionar dados de lesões
- [ ] Comparar com tendências históricas
- [ ] Análise de evolução de valor
- [ ] Integração com StatsBomb (dados mais detalhados)
- [ ] Dashboard interativo com Plotly
- [ ] Previsão de cotações futuras com ML
- [ ] Análise de redes (passador → receptor)

---

## 📚 Referências Externas

- **Transfermarkt**: https://www.transfermarkt.com
- **Football Data**: https://fbref.com
- **Wyscout**: Para vídeos de performance
- **StatsBomb**: Dados avançados

---

**Versão**: 1.0
**Atualizado**: Novembro 2025
