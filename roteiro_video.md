# Roteiro do vídeo (máx. 10 min)

**Meta de duração:** 9 min 45 s, o que deixa 15 s de margem. Falas em torno de 130 palavras por minuto.
**Quem fala:** **A** = Bernardo; **B** = integrante 2. Os dois abrem a câmera durante todo o vídeo.
**Tela:** alterna entre os slides e o notebook (Colab com todas as células já executadas).
Trechos entre `[colchetes]` dependem dos resultados reais: preencham depois de rodar o notebook.

## Linha do tempo

| Tempo | Quem | Tela | Tema |
|---|---|---|---|
| 0:00–0:30 | A e B | Slide 1 | Abertura |
| 0:30–1:15 | A | Slide 2 | Problema |
| 1:15–2:00 | A | Slide 3 + notebook (seções 3–4) | Dataset e importação |
| 2:00–3:15 | A | Slide 4 + notebook (seção 6) | Exploração e gráficos |
| 3:15–4:15 | A | Slide 5 + notebook (seções 7–8) | Limpeza e faltantes |
| 4:15–5:15 | B | Slide 6 + notebook (seções 9–11) | Atributos, tipos, X e y |
| 5:15–6:15 | B | Slide 7 + notebook (seções 12–13) | Treino/teste e padronização |
| 6:15–7:00 | B | Slide 8 + notebook (seção 14) | Modelos |
| 7:00–8:15 | B | Slide 9 + notebook (seção 15) | Resultados |
| 8:15–9:15 | A e B | Slide 10 | Análise crítica |
| 9:15–9:45 | A e B | Slide 11 | Próximos passos e encerramento |

## Falas

### 0:00–0:30 · Abertura (slide 1)
**A:** Olá, eu sou o Bernardo.
**B:** E eu sou o [nome]. Neste vídeo apresentamos o nosso projeto de Ciência de Dados: prever a reinternação de pacientes diabéticos em menos de 30 dias.
**A:** Vamos mostrar o pipeline completo, do download dos dados até a avaliação do modelo, no notebook e nos slides. Eu cubro a parte dos dados, e o [nome] cobre a modelagem.

### 0:30–1:15 · Problema (slide 2) — A
Reinternações em até 30 dias são um indicador de qualidade do cuidado e geram custo alto para o sistema de saúde. Nossa pergunta é: com os dados da própria internação, dá para prever quem volta ao hospital nesse prazo? É um problema de classificação binária: 1 se o paciente foi reinternado em menos de 30 dias, 0 em qualquer outro caso. Se o modelo funcionar, ele ajuda a priorizar o acompanhamento pós-alta.

### 1:15–2:00 · Dataset (slide 3 e notebook) — A
Usamos o *Diabetes 130-US Hospitals*, do repositório UCI, com o link na primeira célula do notebook. São cerca de cem mil internações em 130 hospitais, entre 1999 e 2008, com cerca de cinquenta variáveis numéricas e categóricas. *(Mostrar o notebook:)* aqui baixamos o arquivo direto da UCI e importamos com Pandas. Já vemos o formato da tabela, os tipos de cada coluna e as estatísticas descritivas. Também explicamos cada grupo de variáveis: demografia, procedimentos, histórico de visitas, diagnósticos, exames e 23 colunas de medicamentos.

### 2:00–3:15 · Exploração e gráficos (slide 4 e notebook) — A
O primeiro achado importante: o alvo é desbalanceado. Só cerca de 11% dos pacientes voltam em menos de 30 dias. Isso significa que um modelo que sempre responde “não reinterna” já teria uns 90% de acurácia sem servir para nada, então vamos olhar recall, F1 e ROC-AUC. *(No notebook, mostrar 2 ou 3 gráficos:)* nos histogramas e boxplots, as variáveis de contagem são muito assimétricas e têm outliers. [Citar 1 ou 2 padrões vistos, por exemplo a relação entre internações prévias e o alvo.]

### 3:15–4:15 · Limpeza e faltantes (slide 5 e notebook) — A
Neste dataset, a ausência de dado aparece como ponto de interrogação, então primeiro convertemos para valor nulo. A coluna de peso tinha cerca de 97% faltante, e imputar seria inventar dado, então removemos. Convênio, especialidade, raça e diagnósticos viraram a categoria “Desconhecido”, porque a ausência pode ser informativa. Removemos gênero inválido e pacientes que morreram ou foram para hospice, que não podem ser reinternados. E mantivemos só a primeira internação de cada paciente, para evitar vazamento entre treino e teste. *(Mostrar o “antes e depois” do número de linhas: [____] para [____].)* Agora passo para o [nome].

### 4:15–5:15 · Atributos, tipos, X e y (slide 6 e notebook) — B
Obrigado, Bernardo. Na seleção de atributos, tiramos os identificadores, as colunas constantes e a coluna `readmitted`, porque ela é a origem do alvo e seria vazamento. Agrupamos os diagnósticos CID-9 em categorias clínicas, transformamos a idade no ponto médio da faixa e mantivemos só as dez especialidades mais frequentes. No fim, temos 9 variáveis numéricas, que são contagens e idade, e as demais são categóricas. *(No notebook:)* aqui separamos X e y, com `target` igual a 1 para reinternação em menos de 30 dias.

### 5:15–6:15 · Treino/teste e padronização (slide 7 e notebook) — B
Dividimos em 80% treino e 20% teste, de forma estratificada, para manter a proporção da classe rara nos dois conjuntos. A divisão vem antes de qualquer transformação ajustada. Depois, um `ColumnTransformer` aplica `StandardScaler` nas numéricas e `OneHotEncoder` nas categóricas. Tudo fica dentro de um `Pipeline`, então o pré-processamento é aprendido só no treino e aplicado no teste, sem vazamento.

### 6:15–7:00 · Modelos (slide 8 e notebook) — B
Treinamos dois modelos para poder comparar. A Regressão Logística é o baseline: simples, rápida e interpretável. O Random Forest, com 200 árvores e profundidade limitada a 12, captura interações e relações não lineares. Nos dois usamos `class_weight="balanced"`, que dá mais peso aos erros na classe minoritária.

### 7:00–8:15 · Resultados (slide 9 e notebook) — B
*(Mostrar a tabela de métricas e a matriz de confusão.)* A Regressão Logística teve acurácia [__], recall [__], F1 [__] e ROC-AUC [__]. O Random Forest teve [__], [__], [__] e [__]. [Dizer qual foi melhor e por quê.] Repare que a acurácia isolada engana: o recall da classe que interessa é [__], ou seja, detectamos [__]% dos pacientes que de fato voltaram. A matriz de confusão mostra [__] falsos negativos e [__] falsos positivos. *(Mostrar a importância das variáveis:)* as mais relevantes foram [__], o que [confirma / contradiz] a nossa hipótese inicial.

### 8:15–9:15 · Análise crítica (slide 10) — A e B
**A:** O desempenho [é / não é] suficiente para uso real: [comentar o ROC-AUC e o recall]. Em saúde, deixar passar um paciente de risco costuma custar mais do que um alarme falso, então ajustar o limiar de decisão seria o próximo passo.
**B:** Há limitações: os dados são de 1999 a 2008, só de hospitais dos EUA, e não trazem fatores socioeconômicos nem adesão ao tratamento. Também precisamos de cuidado ético ao usar raça e gênero em modelos clínicos, para não reforçar discriminação.

### 9:15–9:45 · Próximos passos e encerramento (slide 11) — A e B
**B:** Para melhorar, testaríamos busca de hiperparâmetros com validação cruzada, Gradient Boosting como XGBoost ou LightGBM, ajuste de limiar, SMOTE e mais engenharia de atributos.
**A:** O notebook, os slides e este vídeo estão linkados na primeira célula do notebook, e o código está no GitHub.
**A e B:** Obrigado!

## Checklist de gravação

- [ ] Notebook executado do zero (Reiniciar e executar tudo), com as saídas visíveis.
- [ ] Todas as lacunas `[__]` preenchidas com os números reais, no roteiro e no slide 9.
- [ ] Ensaio cronometrado: se passar de 9 min 45 s, cortar detalhes da seção de gráficos.
- [ ] As duas câmeras abertas o tempo todo e os dois com fala, conforme a regra do trabalho.
- [ ] Zoom ou aba do Colab ampliados para o código ficar legível na gravação.
- [ ] Link do vídeo com acesso liberado ao professor e colado na primeira célula do notebook.
