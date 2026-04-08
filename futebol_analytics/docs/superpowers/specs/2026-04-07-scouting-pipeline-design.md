# Design: Melhoria do Pipeline de Scouting — COPEL FC

**Data:** 2026-04-07  
**Status:** Aprovado  
**Escopo:** Ajuste cirúrgico no notebook `Analytics_copel_FC_v2.ipynb`

---

## Contexto

O pipeline atual analisa ~5000 jogadores das 5 maiores ligas europeias (2024-25) para identificar talentos para o COPEL FC. Ele filtra por idade e minutos jogados, aplica scores por posição e busca valores de mercado via Transfermarkt.

**Limitações identificadas:**
1. Zagueiros e Volantes usam a mesma fórmula de score — não reflete diferenças táticas
2. A biblioteca `transfermarkt-wrapper` é lenta (3-5 min) e instável
3. Parâmetros de configuração estão espalhados por várias células
4. Código de busca de mercado é repetido 3 vezes (uma por posição)

---

## Abordagem Escolhida: Ajuste Cirúrgico (Opção 1)

Manter um único notebook bem organizado, melhorando apenas o que tem maior impacto imediato na qualidade dos resultados e na estabilidade do pipeline.

---

## Seção 1: Fórmulas de Score por Posição

Cada posição passa a ter uma fórmula própria que reflete seu papel tático.

### Zagueiro (DF) — foco em defesa sólida e saída de bola
```
Score = TklW × 2.0 + Int × 1.5 + Cmp% × 0.5 + PrgP × 0.8
```

### Volante (MF) — equilíbrio entre construção e marcação
```
Score = PrgP × 1.5 + Cmp% × 0.8 + TklW × 1.2 + Int × 1.0
```

### Atacante (FW) — foco em criação e finalização
```
Score = xG × 1.5 + KP × 1.5 + PPA × 1.3 + TklW × 0.8 + Int × 0.8
```

**Jogadores com posição combinada** (ex: `MF,FW`) aparecem em ambos os rankings, avaliados pela fórmula específica de cada posição — comportamento intencional, pois um jogador versátil tem valor diferente dependendo do contexto de análise.

---

## Seção 2: Web Scraping do Transfermarkt

Substituir `transfermarkt-wrapper` por scraping direto com `requests` + `BeautifulSoup`.

### Fluxo de busca

1. **Busca por nome**
   ```
   GET https://www.transfermarkt.com/schnellsuche/ergebnis/schnellsuche?query=<nome>
   → Extrai: ID do jogador + URL do perfil
   ```

2. **Busca do perfil**
   ```
   GET https://www.transfermarkt.com/<slug>/profil/spieler/<id>
   → Extrai: valor de mercado, data do contrato, clube, idade, pé
   ```

3. **Cache local**
   - Arquivo: `data/market_cache.json`
   - Antes de qualquer requisição, verifica se o jogador já está em cache
   - Validade: 30 dias (após isso, re-busca automaticamente)
   - Salva data da consulta junto com os dados

4. **Fallback**
   - Se o scraping falhar (bloqueio, timeout, mudança de layout), retorna `N/A`
   - Não interrompe o pipeline
   - Exibe lista de jogadores não encontrados ao final da célula

5. **Headers HTTP**
   - Usa `User-Agent` de browser real para evitar bloqueio básico

### Estratégia de fallback futuro
Se o scraping direto continuar instável após testes, migrar para a API do Football-Data.org (gratuita, mais estável).

---

## Seção 3: Melhorias de Legibilidade no Notebook

### Célula de configuração centralizada
Uma única célula Python logo após os imports, com todos os parâmetros ajustáveis:

```python
# ── CONFIGURAÇÕES ──────────────────────────────
IDADE_MIN = 20
IDADE_MAX = 26
MINUTOS_MIN = 900
TOP_N = 5  # quantos jogadores por posição
CACHE_VALIDADE_DIAS = 30
# ───────────────────────────────────────────────
```

### Estrutura de seções padronizada
```
## 1. Configurações
## 2. Carregamento e Tratamento de Dados
## 3. Análise por Posição — Zagueiros (DF)
## 4. Análise por Posição — Volantes (MF)
## 5. Análise por Posição — Atacantes (FW)
## 6. Valores de Mercado
## 7. Resumo Executivo
```

### Função única de scraping
```python
def buscar_valor_mercado(nome: str) -> dict:
    """Busca valor de mercado no Transfermarkt com cache local."""
    ...
```
Substituir as 3 cópias de código de busca por chamadas a essa função única.

---

## Arquivos Afetados

| Arquivo | Ação |
|---------|------|
| `futebol_analytics/Analytics_copel_FC_v2.ipynb` | Modificar — aplicar todas as melhorias |
| `futebol_analytics/data/market_cache.json` | Criar — cache de valores de mercado |

---

## Critérios de Sucesso

- [ ] Cada posição tem fórmula de score distinta e documentada
- [ ] Busca de mercado funciona sem a biblioteca `transfermarkt-wrapper`
- [ ] Cache local evita re-buscas em execuções subsequentes
- [ ] Falhas na busca de mercado não interrompem o pipeline
- [ ] Todos os parâmetros ajustáveis estão em uma única célula no topo
- [ ] Notebook executa do início ao fim sem erros
