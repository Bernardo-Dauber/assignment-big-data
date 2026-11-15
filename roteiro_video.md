# Material para o roteiro do vídeo (máx. 10 min)

Este arquivo reúne, slide por slide, o que mostrar e a informação disponível. As falas ficam por conta de vocês: leiam o conteúdo de cada bloco e montem o texto do jeito de cada um.

**Regras do vídeo:** no máximo 10 minutos, os dois integrantes com câmera aberta e os dois falando. Meta: terminar em 9 min 30 s.
**Tela:** os slides acompanham a fala. Na hora de mostrar o código e as saídas, alternem para o notebook executado no Colab, na seção indicada em cada bloco.
**Sugestão de divisão:** o integrante 1 (Bernardo) cobre os slides 1 a 6, e o integrante 2 cobre os slides 7 a 11. Os dois fazem o 12 e o 13.

## Ordem e tempo sugerido

| # | Slide | Notebook | Tempo sugerido |
|---|---|---|---|
| 1 | Capa | Primeira célula | 0:20 |
| 2 | Problema | Seção 1 | 0:50 |
| 3 | Dataset | Seções 3 e 4 | 0:50 |
| 4 | Análise exploratória | Seções 5 e 6 | 1:10 |
| 5 | Limpeza | Seções 7 e 8 | 1:10 |
| 6 | Atributos | Seções 9 a 11 | 0:50 |
| 7 | Pipeline | Seções 12 e 13 | 0:50 |
| 8 | Modelos | Seção 14 | 0:40 |
| 9 | Resultados | Seção 15 | 1:10 |
| 10 | Matriz de confusão | Seção 15 | 0:50 |
| 11 | Variáveis importantes | Seção 15 | 0:40 |
| 12 | Análise crítica | Seção 16 | 0:50 |
| 13 | Próximos passos | Seção 16 | 0:30 |

Total: cerca de 10 min 40 s se todos usarem o tempo cheio. Por isso o alvo real é ficar um pouco abaixo em cada slide. Ensaiem com cronômetro e cortem onde passar.

---

## 1. Capa
- Título do trabalho: previsão de reinternação em menos de 30 dias de pacientes diabéticos.
- Nome dos dois integrantes e a divisão de quem fala o quê.
- Avisar que o código completo está no notebook e que os links do notebook, dos slides e do vídeo estão na primeira célula.

## 2. Problema
- A reinternação em até 30 dias é um indicador de qualidade do cuidado hospitalar e gera custo alto.
- Pergunta do projeto: com os dados registrados durante a internação, dá para prever quem volta em menos de 30 dias?
- Tipo de problema: classificação binária supervisionada. O alvo vale 1 se o paciente foi reinternado em menos de 30 dias e 0 nos demais casos.
- Uso prático: priorizar o acompanhamento pós-alta dos pacientes de maior risco.

## 3. Dataset
- Nome: Diabetes 130-US Hospitals for Years 1999–2008, repositório UCI (público, link na primeira célula).
- Tamanho original conhecido do dataset: cerca de 101 mil internações e 50 colunas, com variáveis numéricas e categóricas. Confirmem o número exato na saída da célula de importação.
- Valores faltantes vêm marcados com "?" e não com nulo. A coluna `weight` tem cerca de 97% de faltantes.
- Há vários registros por paciente (o mesmo paciente aparece em mais de uma internação), o que importa na limpeza.
- No notebook: a célula da seção 4 baixa o arquivo e carrega com Pandas, e mostra `shape`, tipos e as primeiras linhas.

## 4. Análise exploratória
- Variável alvo: `readmitted` tem três valores, "<30", ">30" e "NO". Distribuição aproximada no dataset completo: 54% "NO", 35% ">30" e 11% "<30".
- Ao transformar em alvo binário (só "<30" vale 1), a classe positiva fica em cerca de 9% no conjunto de teste (1.257 de 13.998). Ou seja, o alvo é desbalanceado.
- Consequência: um modelo que sempre diz "não" acerta cerca de 91% e é inútil. Por isso a avaliação usa recall, precisão, F1 e ROC-AUC, e não só acurácia.
- Gráficos do notebook: histogramas e boxplots das variáveis numéricas (contagens assimétricas, com outliers) e a matriz de correlação.
- Preencham com 1 ou 2 padrões que vocês viram nos gráficos, pelo que aparecer na saída real.

## 5. Limpeza
- "?" convertido para nulo.
- `weight` removida, por ter cerca de 97% de faltantes.
- Faltantes de `payer_code`, `medical_specialty`, `race` e dos diagnósticos preenchidos com "Desconhecido", para não perder linhas e manter a informação de que o dado não existia.
- Gênero "Unknown/Invalid" removido (pouquíssimos casos).
- Removidos pacientes que morreram ou foram para hospice (códigos de alta 11, 13, 14, 19, 20 e 21): eles não podem ser reinternados, então distorceriam o alvo.
- Mantida só a primeira internação de cada paciente, para evitar vazamento: o mesmo paciente não pode aparecer no treino e no teste.
- Tamanho após a limpeza: aproximadamente 70 mil linhas (deduzido do conjunto de teste de 13.998, que é 20% do total). Confirmem na saída do notebook, junto do número antes da limpeza.

## 6. Atributos
- Removidos: identificadores (`encounter_id`, `patient_nbr`), colunas constantes e `readmitted`, que é a origem do alvo e geraria vazamento.
- Diagnósticos CID-9 agrupados em categorias maiores.
- Idade (faixas como "[50-60)") convertida para o ponto médio da faixa.
- `medical_specialty` e `payer_code` limitadas às 10 categorias mais frequentes, com o resto agrupado em "Outros".
- Resultado final: 9 variáveis numéricas e as demais categóricas (confirmem a contagem exata no notebook).
- Separação de X (atributos) e y (alvo binário).

## 7. Pipeline
- Divisão treino/teste: 80% / 20%, estratificada, para manter a proporção da classe rara nos dois conjuntos. Teste com 13.998 linhas.
- `ColumnTransformer`: `StandardScaler` nas numéricas e `OneHotEncoder` nas categóricas.
- Tudo dentro de um `Pipeline` do scikit-learn: o pré-processamento é aprendido só no treino e aplicado no teste, sem vazamento.
- No notebook, mostrem o número de colunas depois do one-hot.

## 8. Modelos
- Regressão Logística: baseline simples e interpretável.
- Random Forest: 200 árvores com profundidade limitada, captura interações e relações não lineares.
- Nos dois, `class_weight="balanced"`, que dá mais peso à classe rara para compensar o desbalanceamento.
- Por que dois modelos: comparar um modelo linear com um de árvores e escolher o melhor com base nas métricas do teste.

## 9. Resultados (conjunto de teste)

| Modelo | Acurácia | Precisão | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Regressão Logística | 66,3% | 13,8% | 52,2% | 21,8% | 0,649 |
| Random Forest | 70,8% | 15,4% | 50,1% | 23,6% | 0,659 |
| Sempre prevê "não" | 91,0% | 0% | 0% | 0% | 0,500 |

- O Random Forest foi o melhor: ganha em acurácia, precisão, F1 e ROC-AUC. A Regressão Logística tem recall um pouco maior (52,2% contra 50,1%).
- A acurácia engana: prever sempre "não" dá a maior acurácia (91,0%) e não detecta nenhum reinternado.
- ROC-AUC de 0,659: o modelo separa as duas classes melhor que o acaso (0,5), mas longe do ideal (1,0). É um desempenho moderado.
- Mostrem no notebook a tabela de métricas e a curva ROC.

## 10. Matriz de confusão (Random Forest, teste)

|  | Previu "não" | Previu "sim" |
|---|---|---|
| Era "não" | 9.301 (verdadeiro negativo) | 3.440 (falso positivo) |
| Era "sim" | 627 (falso negativo) | 630 (verdadeiro positivo) |

- Recall de 50,1%: o modelo detecta cerca de metade dos reinternados (630 de 1.257).
- Precisão de 15,4%: só cerca de 15% dos alertas estão certos. Para cada paciente de risco identificado, 5 a 6 recebem acompanhamento sem precisar.
- O falso negativo (627 pacientes de risco não identificados) é o erro mais caro em saúde. O falso positivo custa um acompanhamento extra.
- Ajustar o limiar de decisão mudaria esse equilíbrio entre recall e precisão.

## 11. Variáveis importantes (Random Forest)
- `discharge_disposition_id`, em especial o código de alta para casa: está entre as variáveis mais usadas pelo modelo.
- `number_inpatient`: número de internações no último ano, sinal ligado ao uso recente do sistema de saúde.
- `time_in_hospital`, `num_medications` e `num_lab_procedures`: refletem a complexidade da internação atual.
- Leitura: o histórico recente e a complexidade do caso são os principais sinais de retorno precoce, o que confirma a hipótese inicial.
- Cuidado ao falar: a importância do Random Forest mostra quais variáveis o modelo mais usou, não a direção do efeito. Evitem dizer que alta para casa "reduz" o risco ou que internações prévias aumentam o risco "exponencialmente", a menos que mostrem um gráfico no notebook que comprove, como a taxa de reinternação por número de internações prévias.

## 12. Análise crítica
- Desempenho: ROC-AUC de 0,659 e recall de 50,1% são moderados. Serve como apoio à triagem, não para decidir sozinho.
- Trade-off: em saúde, deixar passar um paciente de risco costuma custar mais que um alarme falso.
- Limitações dos dados: 1999 a 2008, só hospitais dos EUA, sem fatores socioeconômicos nem adesão ao tratamento. Os dados da internação explicam só parte da reinternação.
- Ética: usar raça e gênero em modelos clínicos exige cautela para não discriminar.

## 13. Próximos passos
- Busca de hiperparâmetros com validação cruzada.
- Modelos de Gradient Boosting.
- Ajuste do limiar de decisão para priorizar o recall.
- Técnicas de balanceamento como SMOTE.
- Mais engenharia de atributos.
- Fechamento: o notebook está no GitHub, e os links dos slides e do vídeo estão na primeira célula. Agradecimento dos dois.

---

## Checklist de gravação

- [ ] Notebook executado do zero (reiniciar e executar tudo), com as saídas visíveis, e números conferidos com os slides.
- [ ] Confirmar no notebook os valores marcados como "confirmem" acima (tamanho antes e depois da limpeza, contagem de variáveis, colunas após o one-hot).
- [ ] Ensaio cronometrado: o limite é 10 minutos.
- [ ] Zoom do navegador ampliado e menu lateral do Colab recolhido, para o código ficar legível.
- [ ] As duas câmeras abertas o tempo todo e os dois com fala.
- [ ] Links do notebook, dos slides e do vídeo colados na primeira célula, com acesso liberado ao professor.
