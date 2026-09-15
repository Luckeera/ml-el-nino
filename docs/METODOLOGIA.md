# Metodologia

## 1. Objetivo

Construir e comparar modelos de classificação capazes de indicar se um novo
episódio de El Niño começará dentro de 1, 3, 6, 9 ou 12 meses.

O projeto possui duas dimensões de comparação:

1. verificar como o desempenho varia com a antecedência da previsão;
2. verificar se variáveis atmosféricas e subsuperficiais compensam a redução
   do período histórico disponível.

## 2. Unidade de observação

Cada linha da base consolidada representará um mês. Todas as variáveis de
entrada de uma linha precisam ser informações que estariam disponíveis até o
fim daquele mês.

Uma estrutura inicial esperada é:

| data | nino12 | nino3 | nino34 | nino4 | soi | olr | heat_content |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 1979-01 | ... | ... | ... | ... | ... | ... | ... |

## 3. Definição do evento

O RONI é uma média móvel de três meses da anomalia relativa da região
Niño 3.4. Para a classificação histórica, o limiar de +0,5 °C precisa ser
mantido por cinco temporadas consecutivas e sobrepostas de três meses.

O processamento deverá:

1. ordenar as temporadas do RONI cronologicamente;
2. identificar sequências qualificadas de pelo menos cinco temporadas;
3. marcar somente o primeiro período de cada sequência como início do evento;
4. impedir que a continuidade de um evento seja interpretada como novo início.

Essa regra deve ser implementada e testada antes da geração dos alvos.

As temporadas serão associadas ao respectivo mês central:

| Temporada | Mês central | Temporada | Mês central |
| --- | --- | --- | --- |
| DJF | janeiro | JJA | julho |
| JFM | fevereiro | JAS | agosto |
| FMA | março | ASO | setembro |
| MAM | abril | SON | outubro |
| AMJ | maio | OND | novembro |
| MJJ | junho | NDJ | dezembro |

## 4. Alvos por horizonte

Para cada horizonte `h`, será criada uma coluna binária:

```text
el_nino_em_1_mes
el_nino_em_3_meses
el_nino_em_6_meses
el_nino_em_9_meses
el_nino_em_12_meses
```

O valor será `1` quando houver início de um episódio depois da data da linha e
até o final do horizonte. Caso contrário, será `0`.

Linhas finais cujo horizonte ainda não pode ser observado não devem receber
automaticamente o valor `0`; o alvo deve permanecer ausente e a linha deve ser
removida daquele experimento.

## 5. Conjuntos de variáveis

### Modelo 2

| Variável | Interpretação |
| --- | --- |
| Niño 1+2 | Temperatura do Pacífico equatorial mais oriental |
| Niño 3 | Temperatura do Pacífico equatorial oriental |
| Niño 3.4 | Principal região oceânica associada ao ENSO |
| Niño 4 | Temperatura do Pacífico equatorial central-oeste |
| SOI | Diferença padronizada de pressão entre Tahiti e Darwin |

Período comum completo: janeiro de 1951 a dezembro de 2025.

### Modelo 3

Utiliza todas as variáveis do Modelo 2 e acrescenta:

| Variável | Interpretação |
| --- | --- |
| OLR | Radiação de onda longa associada à convecção tropical |
| Conteúdo de calor | Anomalia de temperatura nos 300 m superiores do Pacífico equatorial |

Período comum completo: janeiro de 1979 a dezembro de 2025.

O MEI.v2 não entrará na primeira versão porque já combina temperatura,
pressão, ventos e OLR. Sua inclusão junto das variáveis componentes poderia
introduzir redundância. Ele poderá ser avaliado posteriormente como experimento
separado.

## 6. Tratamento dos valores ausentes

Os valores sentinela `-99.9`, `-999.9` e `-9999.0` devem ser convertidos para
`NaN` imediatamente após a leitura.

Serão consideradas duas estratégias para o Modelo 3:

### Casos completos

Treinar somente entre 1979 e 2025, quando todas as variáveis estão presentes.
Essa é a abordagem mais simples de explicar e reproduzir.

### Algoritmo com suporte a NaN

Manter as linhas de 1951 a 1978 com OLR e conteúdo de calor ausentes. Essas
linhas podem contribuir para as relações entre Niño, SOI e alvo, mas não
informam a influência das duas variáveis ausentes.

Não se deve preencher um bloco de 29 anos com zero, média ou interpolação.
Isso criaria observações climáticas artificiais. Faltas pontuais dentro do
período observado poderão receber tratamento separado, ajustado apenas com os
dados de treinamento.

## 7. Engenharia de atributos

Cada indicador poderá gerar atributos que descrevam estado e evolução:

- valores atrasados em 1, 3, 6 e 12 meses;
- médias móveis de 3, 6 e 12 meses;
- diferença em relação ao mês anterior;
- tendência recente;
- mês do ano codificado ciclicamente com seno e cosseno.

Toda janela deve terminar na data da observação. Uma média que inclua meses
futuros produziria vazamento de dados.

## 8. Divisão temporal

Não será utilizada divisão aleatória. Registros climáticos consecutivos são
correlacionados, e uma divisão aleatória permitiria usar o futuro para prever o
passado.

Plano inicial:

- desenvolvimento e validação: dados anteriores a 2010;
- teste final: janeiro de 2010 a dezembro de 2025;
- validação interna: janelas temporais expansivas, como `TimeSeriesSplit`.

O período de teste deverá ser igual em todos os experimentos comparados.

## 9. Comparações controladas

Para separar o efeito de mais variáveis do efeito de mais anos, serão feitas
três execuções com apenas duas definições de modelo:

| Execução | Variáveis | Período de treinamento | Questão respondida |
| --- | --- | --- | --- |
| Modelo 2 longo | Modelo 2 | 1951–2009 | Benefício do histórico longo |
| Modelo 2 comum | Modelo 2 | 1979–2009 | Controle para o período do Modelo 3 |
| Modelo 3 completo | Modelo 3 | 1979–2009 | Benefício de OLR e calor |

Se for usada a estratégia com `NaN`, uma quarta execução poderá comparar o
Modelo 3 desde 1951 com sua versão de casos completos.

## 10. Modelos candidatos

A seleção definitiva ainda não foi realizada. Candidatos adequados incluem:

- regressão logística como modelo interpretável;
- Random Forest ou Gradient Boosting para relações não lineares;
- `HistGradientBoostingClassifier` para o experimento com `NaN` nativo.

Redes neurais não são prioridade devido ao tamanho reduzido da série e ao
pequeno número de episódios independentes.

## 11. Avaliação

A acurácia não deve ser usada isoladamente porque El Niño é uma classe menos
frequente. Para cada horizonte serão avaliados:

- matriz de confusão;
- precisão;
- recall;
- F1-score;
- balanced accuracy;
- PR-AUC e, secundariamente, ROC-AUC;
- probabilidades previstas e calibração, se o modelo permitir.

Também serão usados modelos de referência:

- previsão da classe majoritária;
- climatologia histórica;
- persistência do estado recente, quando aplicável.

## 12. Limitações conhecidas

- Os meses consecutivos não são observações independentes.
- Existem poucos episódios completos mesmo em mais de 70 anos.
- Produtos climáticos podem ser revisados após a primeira publicação.
- Os previsores Niño utilizam ERSSTv5, enquanto o RONI oficial utiliza
  ERSSTv6.
- A ausência estrutural de OLR e calor antes de 1979 coincide com a passagem do
  tempo e pode ser aprendida pelo modelo como marcador de período.
- Bom desempenho retrospectivo não transforma o modelo em previsão climática
  operacional.
