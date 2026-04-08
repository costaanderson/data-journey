# Referência Rápida - Métricas Explicadas

## Top 5 Atuais (Abril 2026)

### ZAGUEIROS (Score_DF)
| Rank | Jogador | Clube | Score | Valor |
|------|---------|-------|-------|-------|
| 1 | Moisés Caicedo | Chelsea | 436.35 | €110M |
| 2 | Joško Gvardiol | Manchester City | 400.30 | €70M |
| 3 | Federico Valverde | Real Madrid | 368.25 | €120M |
| 4 | Antonee Robinson | Fulham | 358.20 | €25M |
| 5 | Aaron Wan-Bissaka | West Ham | 356.75 | €22M |

### VOLANTES (Score_MF)
| Rank | Jogador | Clube | Score | Valor |
|------|---------|-------|-------|-------|
| 1 | Pedri | Barcelona | 676.72 | €150M |
| 2 | Mattéo Guendouzi | Lazio | 603.04 | €28M |
| 3 | Manuel Locatelli | Juventus | 593.04 | €25M |
| 4 | Angelo Stiller | Stuttgart | 561.82 | €45M |
| 5 | Bruno Guimarães | Newcastle | 557.82 | €75M |

### ATACANTES (Score_FW)
| Rank | Jogador | Clube | Score | Valor |
|------|---------|-------|-------|-------|
| 1 | Álex Baena | Villarreal | 276.35 | €45M |
| 2 | Cole Palmer | Chelsea | 264.45 | €110M |
| 3 | Pedri | Barcelona | 259.00 | €150M |
| 4 | Romano Schmid | Werder Bremen | 254.25 | €15M |
| 5 | Mikkel Damsgaard | Brentford | 250.40 | €30M |

---

## Colunas do Dataset

### Identificação
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Player | str | Nome do jogador |
| Nation | str | Nacionalidade |
| Pos | str | Posição(ões) |
| Squad | str | Clube |
| Comp | str | Competição (ex: Premier League) |
| Age | float | Idade em anos |

### Volume de Jogo
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Min | int | Total de minutos jogados |
| 90s | float | Equivalente em partidas completas |
| Starts | int | Partidas como titular |

### Passes
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Cmp | int | Passes completados |
| Att | int | Passes tentados |
| Cmp% | float | % de passes completados |
| PrgP | int | Passes progressivos (avançam >= 10 jardas) |
| 1/3 | int | Passes para o último terço do campo |

### Defesa
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Tkl | int | Total de tackles |
| TklW | int | Tackles vencidos |
| Int | int | Interceptações (passes capturados) |
| Att 3rd | int | Ações defensivas no terço ofensivo |

### Criatividade / Ataque
| Coluna | Tipo | Descrição |
|--------|------|-----------|
| PPA | int | Passes para ações de perigo na área |
| KP | int | Passes chave (diretos para finalização) |
| PrgC | int | Conduções progressivas |
| Gls | int | Gols |
| Ast | int | Assistências |
| xG | float | Expected Goals (gols esperados) |
| xAG | float | Expected Assisted Goals |

---

## Interpretação de Métricas

### PrgP (Passes Progressivos)
- **> 250**: Jogador muito criativo e dominante na construção
- **150-250**: Jogador dinâmico
- **< 150**: Perfil mais defensivo

### Cmp% (Precisão de Passes)
- **> 90%**: Excelente técnica
- **85-90%**: Muito bom
- **75-85%**: Adequado para o filtro aplicado

### Tkl (Tackles)
- **> 80**: Zagueiro/Volante de elite defensivo
- **50-80**: Bom defensor
- **20-50**: Defesa básica

### xG (Expected Goals)
- **> 2.0**: Atacante de alta classe
- **1.5-2.0**: Bom finalizador (mínimo para o filtro)

---

## Comparações Úteis

### Zagueiros: Defesa vs Técnica
- **Moisés Caicedo**: Maior volume defensivo (score 436.35)
- **Joško Gvardiol**: Mais técnico em passes
- **Antonee Robinson / Wan-Bissaka**: Melhor custo-benefício (€22-25M)

### Volantes: Score vs Custo
- **Pedri**: Melhor score (676.72), mais caro (€150M)
- **Guendouzi / Locatelli**: Boa performance por €25-28M

### Atacantes: Eficiência
- **Álex Baena**: Melhor score/custo (€45M, score 276.35)
- **Cole Palmer**: Alto potencial de valorização (22 anos, €110M)
- **Pedri**: Aparece em MF e FW — jogador versátil

---

## Próximas Evoluções Possíveis

- [ ] Adicionar dados de lesões recentes
- [ ] Dashboard interativo com Plotly
- [ ] Previsão de cotações futuras com ML
- [ ] Integração com StatsBomb (dados mais detalhados)
- [ ] Análise de redes (passador → receptor)
- [ ] Filtro por liga/país de origem

---

**Versão**: 2.0 | **Atualizado**: Abril 2026
