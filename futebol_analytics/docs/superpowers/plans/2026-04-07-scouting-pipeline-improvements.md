# Scouting Pipeline Improvements — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Melhorar o notebook de scouting do COPEL FC com fórmulas de score distintas por posição, substituição da biblioteca transfermarkt-wrapper por web scraping com cache, e célula de configuração centralizada.

**Architecture:** Todas as mudanças ficam em um único notebook Jupyter. A função de busca de mercado é centralizada com cache local em JSON para evitar requisições repetidas. Scores são calculados separadamente para DF, MF e FW antes da seleção dos top jogadores.

**Tech Stack:** Python 3.12, pandas, matplotlib, requests, beautifulsoup4, json (stdlib)

---

## Mapeamento de Arquivos

| Arquivo | Ação | Responsabilidade |
|---------|------|-----------------|
| `futebol_analytics/Analytics_copel_FC_v2 .ipynb` | Modificar | Notebook principal — todas as mudanças vão aqui |
| `futebol_analytics/data/market_cache.json` | Criar | Cache local de valores de mercado do Transfermarkt |

> **Nota:** O arquivo do notebook tem um espaço no final do nome (`v2 .ipynb`). Sempre referencie com aspas no terminal: `"Analytics_copel_FC_v2 .ipynb"`

---

## Task 1: Atualizar imports e adicionar célula de configuração

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 4 (code, imports)

A célula 4 atual importa `from tmkt import TMKT` e instala `transfermarkt-wrapper`. Isso precisa ser removido e substituído por `beautifulsoup4`.

- [ ] **Step 1: Substituir o conteúdo da célula 4 (imports)**

Use NotebookEdit para substituir a célula 4 pelo seguinte conteúdo:

```python
import pandas as pd
import matplotlib.pyplot as plt
from math import pi
import requests
from bs4 import BeautifulSoup
import json
import sys
import subprocess
from datetime import datetime, timedelta
import warnings
warnings.filterwarnings('ignore')

# Instalar beautifulsoup4 se necessário
try:
    from bs4 import BeautifulSoup
except ImportError:
    subprocess.check_call([sys.executable, '-m', 'pip', 'install', 'beautifulsoup4'])
    from bs4 import BeautifulSoup

# Configurar display pandas
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', 100)
```

- [ ] **Step 2: Inserir nova célula de configuração após a célula 4**

Inserir uma célula markdown e uma célula code logo após os imports (posição 5 e 6 no notebook — as existentes se deslocam):

Célula markdown:
```markdown
## 1. Configurações
Ajuste os parâmetros abaixo conforme necessário antes de executar o pipeline.
```

Célula code:
```python
# ── CONFIGURAÇÕES ──────────────────────────────────────────────
IDADE_MIN = 20
IDADE_MAX = 26
MINUTOS_MIN = 900
TOP_N = 5                  # Quantos jogadores por posição
CACHE_VALIDADE_DIAS = 30   # Dias antes de re-buscar valor de mercado
# ───────────────────────────────────────────────────────────────
```

- [ ] **Step 3: Verificar execução**

Executar as células de import + configuração e confirmar que não há erros de importação. Saída esperada: nenhuma mensagem de erro, variáveis `IDADE_MIN`, `IDADE_MAX`, `MINUTOS_MIN`, `TOP_N`, `CACHE_VALIDADE_DIAS` definidas.

- [ ] **Step 4: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb"
git commit -m "refactor: replace tmkt imports with requests+bs4, add config cell"
```

---

## Task 2: Criar função de web scraping com cache local

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 15 (função auxiliar de mercado)

A célula 15 atual define `buscar_informacoes_jogador` usando TMKT async. Substituir completamente por scraping síncrono com cache.

- [ ] **Step 1: Criar arquivo de cache inicial**

```bash
echo "{}" > "futebol_analytics/data/market_cache.json"
```

- [ ] **Step 2: Substituir o conteúdo da célula 15**

Substituir toda a célula 15 pelo seguinte código:

```python
CACHE_PATH = 'data/market_cache.json'
HEADERS = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 '
                  '(KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
    'Accept-Language': 'pt-BR,pt;q=0.9,en;q=0.8',
}

def _carregar_cache() -> dict:
    """Carrega o cache local de valores de mercado."""
    try:
        with open(CACHE_PATH, 'r', encoding='utf-8') as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return {}

def _salvar_cache(cache: dict) -> None:
    """Salva o cache local de valores de mercado."""
    with open(CACHE_PATH, 'w', encoding='utf-8') as f:
        json.dump(cache, f, ensure_ascii=False, indent=2)

def _cache_valido(entrada: dict) -> bool:
    """Verifica se a entrada do cache ainda é válida (dentro do prazo)."""
    if 'data_consulta' not in entrada:
        return False
    data_consulta = datetime.fromisoformat(entrada['data_consulta'])
    return datetime.now() - data_consulta < timedelta(days=CACHE_VALIDADE_DIAS)

def buscar_valor_mercado(nome: str) -> dict:
    """
    Busca o valor de mercado de um jogador no Transfermarkt.
    
    Usa cache local (data/market_cache.json) para evitar requisições repetidas.
    Se o scraping falhar, retorna N/A sem interromper o pipeline.
    
    Parâmetros:
        nome: Nome do jogador conforme consta no dataset
    
    Retorna:
        dict com chaves: Nome, Valor_Mercado, Moeda, Data_Contrato, Clube_Atual
    """
    cache = _carregar_cache()
    
    # Verificar cache
    if nome in cache and _cache_valido(cache[nome]):
        print(f"  [cache] {nome}")
        return cache[nome]
    
    print(f"  [buscando] {nome}...")
    resultado = {
        'Nome': nome,
        'Valor_Mercado': 'N/A',
        'Moeda': 'EUR',
        'Data_Contrato': 'N/A',
        'Clube_Atual': 'N/A',
        'data_consulta': datetime.now().isoformat(),
    }
    
    try:
        # Etapa 1: Busca por nome
        url_busca = f'https://www.transfermarkt.com/schnellsuche/ergebnis/schnellsuche'
        resp = requests.get(url_busca, params={'query': nome}, headers=HEADERS, timeout=10)
        resp.raise_for_status()
        soup = BeautifulSoup(resp.text, 'html.parser')
        
        # Encontrar primeiro resultado de jogador
        resultado_jogador = soup.select_one('table.items tbody tr')
        if not resultado_jogador:
            print(f"  [não encontrado] {nome}")
            cache[nome] = resultado
            _salvar_cache(cache)
            return resultado
        
        # Extrair link do perfil
        link_tag = resultado_jogador.select_one('td.hauptlink a')
        if not link_tag:
            print(f"  [sem link] {nome}")
            cache[nome] = resultado
            _salvar_cache(cache)
            return resultado
        
        href = link_tag.get('href', '')  # ex: /pedri/profil/spieler/700852
        
        # Etapa 2: Busca do perfil
        url_perfil = f'https://www.transfermarkt.com{href}'
        resp2 = requests.get(url_perfil, headers=HEADERS, timeout=10)
        resp2.raise_for_status()
        soup2 = BeautifulSoup(resp2.text, 'html.parser')
        
        # Valor de mercado
        valor_tag = soup2.select_one('a.data-header__market-value-wrapper')
        if valor_tag:
            resultado['Valor_Mercado'] = valor_tag.get_text(strip=True)
        
        # Clube atual
        clube_tag = soup2.select_one('span[itemprop="affiliation"] a')
        if clube_tag:
            resultado['Clube_Atual'] = clube_tag.get_text(strip=True)
        
        # Contrato
        for item in soup2.select('li.data-header__label'):
            texto = item.get_text(strip=True)
            if 'Contract' in texto or 'Contrato' in texto:
                valor = item.select_one('span.data-header__content')
                if valor:
                    resultado['Data_Contrato'] = valor.get_text(strip=True)
                break
        
    except requests.RequestException as e:
        print(f"  [erro de rede] {nome}: {e}")
    except Exception as e:
        print(f"  [erro] {nome}: {e}")
    
    # Salvar no cache mesmo em caso de N/A (evita re-buscar jogadores não encontrados)
    cache[nome] = resultado
    _salvar_cache(cache)
    return resultado


def buscar_mercado_top(df_top: pd.DataFrame) -> pd.DataFrame:
    """
    Busca valores de mercado para todos os jogadores de um DataFrame top-N.
    
    Parâmetros:
        df_top: DataFrame com coluna 'Player'
    
    Retorna:
        DataFrame com colunas adicionais de mercado mescladas
    """
    resultados = []
    for _, row in df_top.iterrows():
        info = buscar_valor_mercado(row['Player'])
        resultados.append(info)
    
    df_mercado = pd.DataFrame(resultados)
    return df_top.reset_index(drop=True).join(
        df_mercado[['Valor_Mercado', 'Data_Contrato', 'Clube_Atual']].reset_index(drop=True)
    )
```

- [ ] **Step 3: Verificar que a célula executa sem erros**

Rodar a célula 15 e confirmar que as funções ficam definidas. Saída esperada: nenhum output (apenas definições de função).

- [ ] **Step 4: Teste manual rápido com um jogador**

Adicionar temporariamente numa célula vazia e executar:
```python
resultado = buscar_valor_mercado("Pedri")
print(resultado)
```
Saída esperada: dict com `Valor_Mercado` preenchido ou `N/A` se bloqueado — mas **sem exceção não tratada**.

Após verificar, remover essa célula temporária.

- [ ] **Step 5: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb" "futebol_analytics/data/market_cache.json"
git commit -m "feat: add transfermarkt web scraping with local JSON cache"
```

---

## Task 3: Scores distintos por posição — Zagueiros e Volantes

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 12

A célula 12 atual calcula um único `Score` para DF e MF juntos. Precisamos de `Score_DF` e `Score_MF` separados.

- [ ] **Step 1: Substituir o conteúdo da célula 12**

```python
# Filtro para selecionar zagueiros e volantes
df_filtred_df_mf = df_filtred[
    df_filtred['Pos'].str.contains('^(DF|MF)(,|$)|\b(DF|MF)\b', na=False)
]

# Calcular estatísticas descritivas (mantido para referência)
tkl_stats = df_filtred_df_mf['Tkl'].describe()
tklw_stats = df_filtred_df_mf['TklW'].describe()
prgp = df_filtred_df_mf['PrgP'].describe()

# Filtros mínimos de performance (aplicados a DF e MF)
df_stats_df_mf = df_filtred_df_mf[
    (df_filtred_df_mf['PrgP'] > 20) &    # Passes progressivos
    (df_filtred_df_mf['Cmp%'] >= 75) &   # Precisão de passes (%)
    (df_filtred_df_mf['Tkl'] >= 20) &    # Total de tackles
    (df_filtred_df_mf['TklW'] >= 15)     # Tackles vencidos
].copy()

# ── SCORE ZAGUEIRO (DF) ──────────────────────────────────────
# Foco em defesa sólida e saída de bola limpa
df_stats_df_mf['Score_DF'] = (
    df_stats_df_mf['TklW'] * 2.0 +    # Tackles vencidos — peso alto
    df_stats_df_mf['Int'] * 1.5 +     # Interceptações
    df_stats_df_mf['Cmp%'] * 0.5 +    # Precisão de passes
    df_stats_df_mf['PrgP'] * 0.8      # Passes progressivos
)

# ── SCORE VOLANTE (MF) ───────────────────────────────────────
# Equilíbrio entre construção de jogo e marcação
df_stats_df_mf['Score_MF'] = (
    df_stats_df_mf['PrgP'] * 1.5 +    # Passes progressivos — peso alto
    df_stats_df_mf['Cmp%'] * 0.8 +    # Precisão de passes
    df_stats_df_mf['TklW'] * 1.2 +    # Tackles vencidos
    df_stats_df_mf['Int'] * 1.0       # Interceptações
)

df_stats_df_mf
```

- [ ] **Step 2: Verificar execução**

Rodar a célula e confirmar que o DataFrame resultante tem as colunas `Score_DF` e `Score_MF`. Saída esperada: DataFrame com ambas as colunas numéricas preenchidas.

- [ ] **Step 3: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb"
git commit -m "feat: add distinct scoring formulas for DF and MF positions"
```

---

## Task 4: Atualizar seleção de zagueiros com Score_DF

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 17

A célula 17 usa `'Score'` — precisa usar `'Score_DF'`.

- [ ] **Step 1: Substituir o conteúdo da célula 17**

```python
# Selecionar os TOP_N melhores zagueiros pelo Score_DF
df_top_df = (
    df_stats_df_mf[df_stats_df_mf['Pos'].str.contains('DF')]
    .nlargest(TOP_N, 'Score_DF')
)
df_top_df
```

- [ ] **Step 2: Verificar execução**

Rodar e confirmar que o DataFrame retorna `TOP_N` (5) zagueiros ordenados por `Score_DF` decrescente.

---

## Task 5: Atualizar seleção de volantes com Score_MF

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 23

A célula 23 usa `'Score'` — precisa usar `'Score_MF'`.

- [ ] **Step 1: Substituir o conteúdo da célula 23**

```python
# Selecionar os TOP_N melhores volantes pelo Score_MF
df_top_mf = (
    df_stats_df_mf[df_stats_df_mf['Pos'].str.contains('MF')]
    .nlargest(TOP_N, 'Score_MF')
)
df_top_mf
```

- [ ] **Step 2: Verificar execução**

Rodar e confirmar que o DataFrame retorna `TOP_N` (5) volantes ordenados por `Score_MF` decrescente.

- [ ] **Step 3: Commit (tasks 4 e 5 juntas)**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb"
git commit -m "feat: use Score_DF for defenders and Score_MF for midfielders selection"
```

---

## Task 6: Atualizar score e seleção de atacantes com Score_FW

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 29

A célula 29 usa `Score` com pesos antigos. Atualizar para `Score_FW` com os pesos do design.

- [ ] **Step 1: Substituir o conteúdo da célula 29**

```python
# Filtro para selecionar atacantes
df_filtred_fw = df_filtred[
    df_filtred['Pos'].str.contains('^(FW|MF)(,|$)|\b(FW|MF)\b', na=False)
]

# Calcular estatísticas descritivas
kp_stats = df_filtred_fw['KP'].describe()
ppa_stats = df_filtred_fw['PPA'].describe()
xg_stats = df_filtred_fw['xG'].describe()
prgp_stats = df_filtred_fw['PrgP'].describe()

# Filtros mínimos de performance para atacantes
df_stats_fw = df_filtred_fw[
    (df_filtred_fw['PrgP'] > 40) &     # Passes progressivos
    (df_filtred_fw['xG'] >= 1.5) &     # Expected goals
    (df_filtred_fw['KP'] >= 15) &      # Passes chave
    (df_filtred_fw['PPA'] >= 10) &     # Passes para ações de perigo
    (df_filtred_fw['TklW'] >= 15) &    # Tackles vencidos
    (df_filtred_fw['Int'] >= 10)       # Interceptações
].copy()

# ── SCORE ATACANTE (FW) ──────────────────────────────────────
# Foco em criação, finalização e contribuição defensiva
df_stats_fw['Score_FW'] = (
    df_stats_fw['xG'] * 1.5 +     # Expected goals — foco em finalização
    df_stats_fw['KP'] * 1.5 +     # Passes chave
    df_stats_fw['PPA'] * 1.3 +    # Passes para ações de perigo
    df_stats_fw['TklW'] * 0.8 +   # Contribuição defensiva
    df_stats_fw['Int'] * 0.8      # Interceptações
)
df_stats_fw = df_stats_fw.sort_values(by='Score_FW', ascending=False)

# Selecionar os TOP_N melhores atacantes
df_top_fw = df_stats_fw.nlargest(TOP_N, 'Score_FW')
df_top_fw
```

- [ ] **Step 2: Verificar execução**

Rodar e confirmar que o DataFrame retorna `TOP_N` (5) atacantes ordenados por `Score_FW` decrescente.

- [ ] **Step 3: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb"
git commit -m "feat: add Score_FW formula and update forward selection"
```

---

## Task 7: Substituir chamadas TMKT pela nova função buscar_mercado_top

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Células 21, 27 e 33

As células 21, 27 e 33 usam `async with TMKT()`. Substituir pela função síncrona `buscar_mercado_top`.

- [ ] **Step 1: Substituir célula 21 (mercado dos zagueiros)**

```python
print("Buscando valores de mercado — Top Zagueiros")
df_top_df_mercado = buscar_mercado_top(df_top_df)
df_top_df_mercado[['Player', 'Squad', 'Age', 'Score_DF', 'Valor_Mercado', 'Data_Contrato', 'Clube_Atual']]
```

- [ ] **Step 2: Substituir célula 27 (mercado dos volantes)**

```python
print("Buscando valores de mercado — Top Volantes")
df_top_mf_mercado = buscar_mercado_top(df_top_mf)
df_top_mf_mercado[['Player', 'Squad', 'Age', 'Score_MF', 'Valor_Mercado', 'Data_Contrato', 'Clube_Atual']]
```

- [ ] **Step 3: Substituir célula 33 (mercado dos atacantes)**

```python
print("Buscando valores de mercado — Top Atacantes")
df_top_fw_mercado = buscar_mercado_top(df_top_fw)
df_top_fw_mercado[['Player', 'Squad', 'Age', 'Score_FW', 'Valor_Mercado', 'Data_Contrato', 'Clube_Atual']]
```

- [ ] **Step 4: Verificar execução das 3 células**

Rodar as células 21, 27 e 33. Saída esperada:
- Mensagens `[buscando]` ou `[cache]` para cada jogador
- DataFrame com colunas de mercado preenchidas (ou `N/A` em caso de falha)
- **Nenhuma exceção não tratada**

- [ ] **Step 5: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb" "futebol_analytics/data/market_cache.json"
git commit -m "feat: replace TMKT async calls with synchronous scraping function"
```

---

## Task 8: Consolidar plot_radar_chart em definição única

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Células 19, 25, 31

A função `plot_radar_chart` está definida 3 vezes (células 19, 25 e 31). Mover a definição para a célula 19 e nas células 25 e 31 apenas chamar a função já definida.

- [ ] **Step 1: Manter célula 19 com a definição completa**

Verificar que a célula 19 já contém a definição de `plot_radar_chart` e a chamada para zagueiros. Se estiver correta, não alterar.

- [ ] **Step 2: Substituir célula 25 — remover redefinição**

A célula 25 redefine `plot_radar_chart` e plota volantes. Substituir por apenas a chamada:

```python
# Gráfico de radar — Top Volantes
plot_radar_chart(
    df_top_mf[['Player', 'PrgP', 'Cmp%', 'TklW', 'Int', 'Score_MF']].rename(columns={'Score_MF': 'Score'}),
    title=f'Top {TOP_N} Volantes — COPEL FC'
)
```

- [ ] **Step 3: Substituir célula 31 — remover redefinição**

```python
# Gráfico de radar — Top Atacantes
plot_radar_chart(
    df_top_fw[['Player', 'xG', 'PPA', 'KP', 'TklW', 'Int']],
    title=f'Top {TOP_N} Atacantes — COPEL FC'
)
```

- [ ] **Step 4: Verificar execução dos gráficos**

Rodar as 3 células de gráfico e confirmar que os 3 radares são gerados sem erros.

- [ ] **Step 5: Commit**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb"
git commit -m "refactor: consolidate plot_radar_chart to single definition"
```

---

## Task 9: Atualizar resumo executivo e validação final

**Arquivo:** `futebol_analytics/Analytics_copel_FC_v2 .ipynb` — Célula 37

A célula 37 referencia `Score` — atualizar para usar as colunas corretas de cada posição.

- [ ] **Step 1: Substituir a célula 37**

```python
print("\n" + "="*100)
print("RESUMO EXECUTIVO — TOP TALENTOS PARA COPEL FC")
print("="*100)

print(f"\n{'─'*100}")
print(f"TOP {TOP_N} ZAGUEIROS (Score_DF)")
print(f"{'─'*100}")
cols_df = ['Player', 'Squad', 'Comp', 'Age', 'Score_DF', 'Valor_Mercado', 'Data_Contrato']
cols_df_disp = [c for c in cols_df if c in df_top_df_mercado.columns]
print(df_top_df_mercado[cols_df_disp].to_string(index=False))

print(f"\n{'─'*100}")
print(f"TOP {TOP_N} VOLANTES (Score_MF)")
print(f"{'─'*100}")
cols_mf = ['Player', 'Squad', 'Comp', 'Age', 'Score_MF', 'Valor_Mercado', 'Data_Contrato']
cols_mf_disp = [c for c in cols_mf if c in df_top_mf_mercado.columns]
print(df_top_mf_mercado[cols_mf_disp].to_string(index=False))

print(f"\n{'─'*100}")
print(f"TOP {TOP_N} ATACANTES (Score_FW)")
print(f"{'─'*100}")
cols_fw = ['Player', 'Squad', 'Comp', 'Age', 'Score_FW', 'Valor_Mercado', 'Data_Contrato']
cols_fw_disp = [c for c in cols_fw if c in df_top_fw_mercado.columns]
print(df_top_fw_mercado[cols_fw_disp].to_string(index=False))

print("\n" + "="*100)
```

- [ ] **Step 2: Executar o notebook completo (Run All)**

Pressionar `Ctrl+Shift+Enter` ou usar "Run All Cells". Verificar:
- [ ] Nenhuma célula com erro vermelho
- [ ] 3 gráficos de radar gerados
- [ ] Tabelas de top jogadores exibidas para as 3 posições
- [ ] Busca de mercado retorna valores ou N/A sem travar
- [ ] Resumo executivo exibe as 3 tabelas

- [ ] **Step 3: Commit final**

```bash
git add "futebol_analytics/Analytics_copel_FC_v2 .ipynb" "futebol_analytics/data/market_cache.json"
git commit -m "feat: update executive summary with position-specific scores, complete pipeline"
```

---

## Critérios de Sucesso

- [ ] Imports não incluem `tmkt` ou `transfermarkt-wrapper`
- [ ] Variáveis de configuração (`IDADE_MIN`, `TOP_N`, etc.) definidas em célula única no topo
- [ ] `df_stats_df_mf` tem colunas `Score_DF` e `Score_MF` distintas
- [ ] Zagueiros ordenados por `Score_DF`, Volantes por `Score_MF`, Atacantes por `Score_FW`
- [ ] `plot_radar_chart` definida apenas uma vez
- [ ] Busca de mercado usa `requests` + `BeautifulSoup` sem dependência do `tmkt`
- [ ] `data/market_cache.json` criado e atualizado após execução
- [ ] Falha na busca de mercado retorna `N/A` sem interromper o pipeline
- [ ] Notebook executa do início ao fim sem erros
