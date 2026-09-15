# Dados ENSO

[Voltar ao projeto](../../../../README.md) | [Metodologia](../../../../docs/METODOLOGIA.md) | [Roteiro do notebook](../../../../docs/ROTEIRO_NOTEBOOK.md)

Séries climáticas mensais baixadas de fontes oficiais da NOAA em
**15 de setembro de 2026**. Os arquivos em `raw/` foram preservados no formato
fornecido pelas fontes e não devem ser editados manualmente.

## Arquivos

| Arquivo | Conteúdo | Cobertura observada no arquivo | Fonte |
| --- | --- | --- | --- |
| `raw/nino_regions_ersstv5.txt` | SST e anomalias das regiões Niño 1+2, 3, 3.4 e 4 | jan/1950–jun/2026 | [NOAA/CPC](https://www.cpc.ncep.noaa.gov/data/indices/ersst5.nino.mth.91-20.ascii) |
| `raw/soi_cpc.txt` | Southern Oscillation Index (SOI), incluindo valores padronizados | jan/1951–ago/2026 | [NOAA/CPC](https://www.cpc.ncep.noaa.gov/data/indices/soi) |
| `raw/roni_ersstv6.txt` | Relative Oceanic Niño Index (RONI) por temporada de três meses | DJF/1950–JJA/2026 | [NOAA/CPC](https://www.cpc.ncep.noaa.gov/data/indices/RONI.ascii.txt) |
| `raw/olr_equatorial_cpc.txt` | OLR equatorial original, anômala e padronizada | jun/1974–ago/2026 | [NOAA/CPC](https://www.cpc.ncep.noaa.gov/data/indices/olr) |
| `raw/olr_equatorial_cpc.csv` | OLR equatorial padronizada, em formato tabular | jun/1974–ago/2026 | [NOAA/PSL](https://psl.noaa.gov/data/correlation/olr.csv) |
| `raw/heat_content_cpc.txt` | Anomalia de temperatura nos 300 m superiores do Pacífico em três regiões | jan/1979–ago/2026 | [NOAA/CPC](https://www.cpc.ncep.noaa.gov/products/analysis_monitoring/ocean/index/heat_content_index.txt) |
| `raw/heat_content_cpc.csv` | Conteúdo de calor de 160°E a 80°W, em formato tabular | jan/1979–ago/2026 | [NOAA/PSL](https://psl.noaa.gov/data/correlation/heatcentra.csv) |

Os pares `.txt`/`.csv` de OLR e conteúdo de calor são representações
alternativas das mesmas fontes. O TXT conserva mais campos; o CSV simplifica a
leitura com pandas.

## Uso planejado

### Modelo 2 — histórico longo

- Período comum completo: **janeiro de 1951 a dezembro de 2025**.
- Previsores: anomalias Niño 1+2, Niño 3, Niño 3.4, Niño 4 e SOI padronizado.
- Alvo: episódios derivados do RONI.

### Modelo 3 — completo

- Período comum completo: **janeiro de 1979 a dezembro de 2025**.
- Previsores: todos os do Modelo 2 mais OLR padronizada e conteúdo de calor
  do Pacífico equatorial (160°E–80°W).
- Alvo: episódios derivados do RONI.
- Em algoritmos com suporte nativo a valores ausentes, as linhas de 1951–1978
  podem ser mantidas com `NaN` em OLR e conteúdo de calor.

## Valores ausentes

As fontes usam sentinelas negativos em vez de células vazias, principalmente
`-99.9`, `-999.9` e `-9999.0`. Esses valores precisam ser convertidos para
`NaN` durante a preparação dos dados.

Os meses de 2026 não disponíveis também aparecem como sentinelas em alguns
arquivos. Por isso, 2026 não deve ser tratado como ano completo.

## Compatibilidade dos produtos

Os quatro previsores Niño foram mantidos em um único produto ERSSTv5 para que
tenham a mesma metodologia e climatologia. O alvo usa RONI/ERSSTv6, atualmente
adotado pela NOAA para classificação oficial do ENSO. Essa diferença de versão
deve ser documentada no notebook e pode ser avaliada posteriormente em uma
análise de sensibilidade.
