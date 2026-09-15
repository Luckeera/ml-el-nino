# Roteiro do notebook

O notebook final deve alternar células Markdown e código. Cada resultado precisa
ser acompanhado por uma interpretação em linguagem natural; apenas executar o
código não é suficiente.

## 1. Título

Sugestão:

> Previsão da ocorrência de El Niño com indicadores oceânicos e atmosféricos

## 2. Contextualização do problema

Explicar:

- o que é ENSO;
- o que caracteriza El Niño;
- por que previsões antecipadas são relevantes;
- que o projeto avaliará diferentes horizontes de antecedência;
- que se trata de um experimento acadêmico, não operacional.

Encerrar a seção com a pergunta de pesquisa e os objetivos geral e específicos.

## 3. Contextualização dos dados

Apresentar:

- NOAA/CPC e NOAA/PSL como fontes;
- cobertura temporal de cada série;
- significado das regiões Niño;
- significado de SOI, OLR e conteúdo de calor;
- RONI como fonte dos rótulos;
- diferença entre os Modelos 2 e 3;
- motivo para utilizar 1951 e 1979 como inícios dos experimentos.

Incluir uma tabela de dicionário de dados com nome, unidade, papel e fonte de
cada coluna.

## 4. Importações e configuração

Importar apenas as bibliotecas necessárias. Configurar semente aleatória quando
o algoritmo possuir componentes estocásticos.

## 5. Leitura e consolidação

Mostrar separadamente:

1. leitura dos arquivos brutos;
2. conversão das datas;
3. seleção das colunas;
4. conversão dos sentinelas para `NaN`;
5. junção pela data;
6. ordenação cronológica;
7. validação de duplicidades e lacunas.

Apresentar `head()`, `tail()`, `shape`, `info()` e um resumo dos valores
ausentes.

## 6. Análise exploratória

Gráficos e perguntas recomendados:

- evolução temporal de todas as variáveis;
- destaque dos episódios de El Niño;
- distribuição de cada indicador;
- correlação entre indicadores;
- quantidade e duração dos episódios;
- proporção entre as classes para cada horizonte;
- mapa de valores ausentes ao longo do tempo.

Depois de cada gráfico, registrar o padrão observado e sua possível relação
com o problema.

## 7. Construção do alvo

Explicar a regra do RONI, identificar as sequências qualificadas e gerar os
alvos de 1, 3, 6, 9 e 12 meses.

Validar manualmente alguns episódios conhecidos para confirmar que o algoritmo
marcou o início e os horizontes corretamente.

## 8. Preparação das variáveis

Criar atrasos, médias móveis, tendências e representação cíclica do mês.
Explicar por que cada transformação pode ajudar.

Separar os conjuntos do Modelo 2 e do Modelo 3 sem alterar a base bruta.

## 9. Divisão temporal

Exibir em uma linha do tempo:

```text
treino e validação                           teste final
1951/1979 ───────────────────────── 2009 | 2010 ─────────── 2025
```

Explicar por que `train_test_split` aleatório não é adequado e como o
`TimeSeriesSplit` preserva a ordem cronológica.

## 10. Modelos de referência

Antes dos modelos de Machine Learning, calcular o desempenho da classe
majoritária, da climatologia e, se aplicável, da persistência.

Um modelo treinado só é útil se superar essas referências.

## 11. Treinamento

Para cada algoritmo e horizonte:

1. ajustar transformações somente no treino;
2. selecionar hiperparâmetros usando validação temporal;
3. registrar configuração e semente;
4. manter o teste final sem contato até a escolha do modelo.

## 12. Avaliação

Apresentar uma tabela comparando modelos e horizontes, além de:

- matrizes de confusão;
- precisão, recall e F1-score;
- balanced accuracy;
- curvas Precision-Recall;
- desempenho em função da antecedência.

O gráfico principal deve colocar o horizonte no eixo horizontal e uma ou mais
métricas no eixo vertical.

## 13. Comparação dos experimentos

Responder explicitamente:

- o Modelo 3 superou o Modelo 2 no mesmo período de 1979–2025?
- o histórico adicional do Modelo 2 melhorou seu desempenho?
- manter linhas antigas com `NaN` ajudou ou prejudicou o Modelo 3?
- qual horizonte apresentou o melhor equilíbrio entre antecedência e
  desempenho?

## 14. Conclusão

Retomar a pergunta de pesquisa, apresentar a resposta sustentada pelas
métricas, reconhecer as limitações e sugerir extensões futuras.

Possíveis extensões:

- testar MEI.v2;
- comparar ERSSTv5 e ERSSTv6;
- utilizar dados espaciais de OISST ou ERA5;
- prever intensidade, e não apenas ocorrência;
- produzir probabilidades calibradas.

