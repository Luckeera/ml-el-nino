# Previsão de El Niño com Machine Learning

Projeto acadêmico para investigar se é possível prever o início de um episódio
de El Niño a partir de indicadores oceânicos e atmosféricos do Pacífico.

O experimento não pretende substituir os sistemas operacionais de previsão
climática. Seu objetivo é aplicar um fluxo completo de Machine Learning:
contextualização, coleta, análise exploratória, preparação temporal, treinamento,
avaliação e interpretação dos resultados.

## Pergunta de pesquisa

> Com base somente nas condições conhecidas em determinado mês, ocorrerá o
> início de um episódio de El Niño dentro de um horizonte futuro definido?

Serão avaliados horizontes de **1, 3, 6, 9 e 12 meses**. Isso permitirá analisar
em qual faixa de antecedência o modelo funciona melhor e como seu desempenho se
degrada quando a previsão é feita mais cedo.

## Experimentos

### Modelo 2 — histórico longo

- Período comum completo: janeiro de 1951 a dezembro de 2025.
- Variáveis: anomalias Niño 1+2, Niño 3, Niño 3.4, Niño 4 e SOI.
- Vantagem: maior quantidade de observações mensais.

### Modelo 3 — conjunto enriquecido

- Período comum completo: janeiro de 1979 a dezembro de 2025.
- Variáveis: todas do Modelo 2, OLR e conteúdo de calor do Pacífico.
- Vantagem: representa melhor o acoplamento entre oceano e atmosfera.

O Modelo 3 também poderá ser treinado desde 1951 com OLR e conteúdo de calor
ausentes antes de 1979, desde que o algoritmo selecionado aceite `NaN`. Nesse
caso, os anos antigos ajudam a aprender apenas as relações das variáveis que
existiam naquele período.

## Variável-alvo

O alvo será construído a partir do **Relative Oceanic Niño Index (RONI)** da
NOAA. Um episódio histórico é caracterizado quando o limiar de +0,5 °C é
atingido por pelo menos cinco temporadas consecutivas e sobrepostas de três
meses.

Para cada data `t` e horizonte `h`, o alvo indicará se o início de um episódio
ocorrerá no intervalo futuro `(t, t + h]`. O RONI será inicialmente reservado
para produzir os rótulos, evitando que o modelo apenas reproduza diretamente a
regra usada para definir o fenômeno.

## Estrutura do repositório

```text
.
├── README.md
├── TASK.md
├── docs/
│   ├── METODOLOGIA.md
│   └── ROTEIRO_NOTEBOOK.md
└── src/
    └── Labs/
        ├── Atividade_01_CRISP_DM.ipynb
        ├── data/
        │   ├── credit_data.csv
        │   └── enso/
        │       ├── README.md
        │       └── raw/
        ├── Lab01/
        └── Lab02/
```

Os notebooks existentes em `Lab01` e `Lab02` são materiais de aula e foram
usados como referência. O notebook da fase inicial do projeto está disponível em
`src/Labs/Atividade_01_CRISP_DM.ipynb`.

## Documentação

- [Requisitos da Atividade 1](TASK.md): diretrizes das fases iniciais do CRISP-DM.
- [Metodologia](docs/METODOLOGIA.md): desenho experimental, preparação,
  treinamento e avaliação.
- [Roteiro do notebook](docs/ROTEIRO_NOTEBOOK.md): estrutura narrativa da
  atividade acadêmica.
- [Catálogo dos dados](src/Labs/data/enso/README.md): arquivos, cobertura,
  fontes oficiais e valores ausentes.

## Estado atual

- [x] Tema e pergunta de pesquisa definidos.
- [x] Fontes oficiais pesquisadas.
- [x] Dados brutos baixados e documentados.
- [x] Modelos 2 e 3 conceitualmente definidos.
- [x] Dados consolidados em uma tabela mensal (tratamento de sentinelas e ordenação).
- [x] Análise exploratória realizada (estatísticas, ausentes, séries temporais e correlações).
- [x] Notebook da Fase Inicial (CRISP-DM 1 e 2) estruturado e documentado.
- [ ] Variável-alvo e horizontes gerados (Fase 3 – Preparação dos Dados).
- [ ] Modelos treinados e comparados (Fase 4 e 5 – Modelagem e Avaliação).

## Fontes principais

- [NOAA/CPC — RONI](https://www.cpc.ncep.noaa.gov/products/analysis_monitoring/enso/roni/)
- [NOAA/CPC — índices atmosféricos e oceânicos](https://www.cpc.ncep.noaa.gov/data/indices/)
- [NOAA/PSL — séries climáticas mensais](https://psl.noaa.gov/data/timeseries/month/)
- [NOAA/NCEI — ERSST](https://www.ncei.noaa.gov/products/extended-reconstructed-sst)

