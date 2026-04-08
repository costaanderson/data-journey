╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║         ANÁLISE COMPLETA DE TALENTOS - COPEL FC - ABRIL 2026                ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝

O QUE VOCÊ RECEBEU
═════════════════════════════════════════════════════════════════════════════

1 NOTEBOOK PRONTO PARA USAR
   • Analytics_copel_FC_v2 .ipynb
   • 40 células organizadas (26 de código + 14 markdown)
   • Executa análise completa de DF, MF e FW
   • Execute via: Kernel → Restart & Run All Cells

15 JOGADORES SELECIONADOS
   • 5 Zagueiros (DF)
   • 5 Volantes (MF)
   • 5 Atacantes (FW)
   • Com valores de mercado em tempo real (Transfermarkt)

3 GRÁFICOS DE RADAR
   • Um por posição (DF, MF, FW)
   • Categorias dinâmicas por posição
   • Normalização 0-100%

═════════════════════════════════════════════════════════════════════════════

COMO EXECUTAR (2 PASSOS)
═════════════════════════════════════════════════════════════════════════════

PASSO 1: Abra o notebook
         → Analytics_copel_FC_v2 .ipynb

PASSO 2: Execute tudo
         → Kernel → Restart & Run All Cells
         → Aguarde ~5-10 minutos (busca de mercado é a etapa mais lenta)

═════════════════════════════════════════════════════════════════════════════

PIPELINE DE ANÁLISE
═════════════════════════════════════════════════════════════════════════════

1. CARREGAMENTO
   • players_data_light-2024_2025.csv (5 grandes ligas europeias 24/25)
   • 165 colunas → 25 selecionadas

2. FILTROS BASE (todas as posições)
   • Idade: 20-26 anos
   • Minutos jogados: >= 900

3. FILTROS POR POSIÇÃO

   ZAGUEIROS e VOLANTES (DF/MF):
   • PrgP > 20      (passes progressivos)
   • Cmp% >= 75%    (precisão de passes)
   • Tkl >= 20      (total de tackles)
   • TklW >= 15     (tackles vencidos)

   ATACANTES (FW/MF):
   • PrgP > 40      (passes progressivos)
   • xG >= 1.5      (expected goals)
   • KP >= 15       (passes chave)
   • PPA >= 10      (passes para área de perigo)
   • TklW >= 15     (tackles vencidos)
   • Int >= 10      (interceptações)

4. FÓRMULAS DE SCORE

   Score_DF (Zagueiro):
   TklW×2.0 + Int×1.5 + Cmp%×0.5 + PrgP×0.8

   Score_MF (Volante):
   PrgP×1.5 + Cmp%×0.8 + TklW×1.2 + Int×1.0

   Score_FW (Atacante):
   xG×1.5 + KP×1.5 + PPA×1.3 + TklW×0.8 + Int×0.8

5. BUSCA DE MERCADO
   • Scraping do Transfermarkt via requests + BeautifulSoup
   • Cache local em data/market_cache.json (validade: 30 dias)

═════════════════════════════════════════════════════════════════════════════

RESULTADOS ATUAIS (última execução)
═════════════════════════════════════════════════════════════════════════════

TOP 5 ZAGUEIROS (Score_DF)
┌─────────────────────────────────────────────────────────┐
│ 1. Moisés Caicedo   (Chelsea)          €110M  436.35   │
│ 2. Joško Gvardiol   (Manchester City)   €70M  400.30   │
│ 3. Federico Valverde (Real Madrid)     €120M  368.25   │
│ 4. Antonee Robinson (Fulham)            €25M  358.20   │
│ 5. Aaron Wan-Bissaka (West Ham)         €22M  356.75   │
└─────────────────────────────────────────────────────────┘

TOP 5 VOLANTES (Score_MF)
┌─────────────────────────────────────────────────────────┐
│ 1. Pedri            (Barcelona)        €150M  676.72   │
│ 2. Mattéo Guendouzi (Lazio)            €28M   603.04   │
│ 3. Manuel Locatelli (Juventus)         €25M   593.04   │
│ 4. Angelo Stiller   (Stuttgart)        €45M   561.82   │
│ 5. Bruno Guimarães  (Newcastle)        €75M   557.82   │
└─────────────────────────────────────────────────────────┘

TOP 5 ATACANTES (Score_FW)
┌─────────────────────────────────────────────────────────┐
│ 1. Álex Baena       (Villarreal)       €45M   276.35   │
│ 2. Cole Palmer      (Chelsea)         €110M   264.45   │
│ 3. Pedri            (Barcelona)       €150M   259.00   │
│ 4. Romano Schmid    (Werder Bremen)    €15M   254.25   │
│ 5. Mikkel Damsgaard (Brentford)        €30M   250.40   │
└─────────────────────────────────────────────────────────┘

═════════════════════════════════════════════════════════════════════════════

TEMPO DE EXECUÇÃO
═════════════════════════════════════════════════════════════════════════════

Imports e configuração:   < 5 segundos
Carregamento dos dados:   < 5 segundos
Filtros e scores:         < 5 segundos
Gráficos de radar:        < 5 segundos
Busca de mercado:         3-5 minutos (API externa — usa cache após 1ª vez)
Resumo executivo:         < 1 segundo
─────────────────────────────────────
TOTAL:                    ~5-10 minutos (< 1 min se cache válido)

═════════════════════════════════════════════════════════════════════════════

REQUISITOS
═════════════════════════════════════════════════════════════════════════════

• Python 3.12+
• pandas
• matplotlib
• requests
• beautifulsoup4
• Conexão com internet (para buscar valores de mercado)

(O notebook instala beautifulsoup4 automaticamente se necessário)

═════════════════════════════════════════════════════════════════════════════

ESTRUTURA DE ARQUIVOS
═════════════════════════════════════════════════════════════════════════════

futebol_analytics/
├── Analytics_copel_FC_v2 .ipynb     ← NOTEBOOK PRINCIPAL
├── README_FINAL.txt                 ← Você está aqui
└── data/
    ├── players_data_light-2024_2025.csv
    └── market_cache.json            ← Cache de valores de mercado

═════════════════════════════════════════════════════════════════════════════

Versão: 2.0
Status: Completo e Testado
Data: Abril 2026

╚══════════════════════════════════════════════════════════════════════════════╝
