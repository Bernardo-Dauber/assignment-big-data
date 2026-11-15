# Material para o roteiro do vídeo (máx. 10 min)

Este arquivo reúne, slide por slide, o que mostrar e a informação disponível. As falas ficam por conta de vocês: leiam o conteúdo de cada bloco e montem o texto do jeito de cada um.

**Regras do vídeo:** no máximo 10 minutos, os dois integrantes com câmera aberta e os dois falando. Meta: terminar em 9 min 30 s.
**Tela:** os slides acompanham a fala. Na hora de mostrar o código e as saídas, alternem para o notebook executado no Colab, na seção indicada em cada bloco.
**Divisão sugerida:** o integrante 1 (Bernardo) fala os slides 2 a 4, o integrante 2 fala os slides 5 a 8, e os dois fecham no 9. A limpeza fica com o integrante 1, rapidamente, no fim do slide 4, mostrando o notebook.

## Ordem e tempo sugerido (9 slides)

| # | Slide | Notebook | Quem | Tempo |
|---|---|---|---|---|
| 1 | Capa | Primeira célula | 1 e 2 | 0:20 |
| 2 | Problema e dados | Seções 1, 3 e 4 | 1 | 1:00 |
| 3 | Alvo desbalanceado | Seções 5 e 6.1 a 6.3 | 1 | 1:00 |
| 4 | Quem já foi muito internado volta mais | Seção 6.4 | 1 | 0:50 |
| 5 | Atributos, pipeline e modelos | Seções 7 a 14 | 2 | 2:00 |
| 6 | Resultados | Seção 15 | 2 | 1:10 |
| 7 | Matriz de confusão | Seção 15 | 2 | 0:50 |
| 8 | Variáveis importantes | Seção 15 | 2 | 0:50 |
| 9 | Análise crítica e próximos passos | Seção 16 | 1 e 2 | 1:00 |

Total: cerca de 9 min 10 s. A limpeza (seções 7 e 8) não tem slide: falem dela mostrando o notebook, dentro do slide 5, e usem o bloco "Limpeza (sem slide)" abaixo. Se sobrar tempo, mostrem no notebook os gráficos de distribuições e outliers (6.2 e 6.3), que não têm slide próprio.

---

## 1. Capa
- Título: previsão de reinternação em menos de 30 dias de pacientes diabéticos.
- Nome dos dois integrantes e a divisão de quem fala o quê.
- Avisar que o código completo está no notebook e que os links do notebook, dos slides e do vídeo estão na primeira célula.

## 2. Problema e dados
- A reinternação em até 30 dias é um indicador de qualidade do cuidado hospitalar e gera custo alto.
- Pergunta: com os dados registrados durante a internação, dá para prever quem volta em menos de 30 dias? Classificação binária supervisionada, com alvo 1 para reinternado em menos de 30 dias e 0 nos demais casos.
- Uso prático: priorizar o acompanhamento pós-alta dos pacientes de maior risco.
- Dataset: Diabetes 130-US Hospitals, 1999 a 2008, repositório UCI (link na primeira célula). Cerca de 100 mil internações, cerca de 50 colunas e 130 hospitais. Confirmem os números exatos na saída da célula de importação.
- Valores faltantes vêm marcados com "?". A coluna `weight` tem cerca de 97% de faltantes. Há vários registros por paciente.

## 3. Alvo desbalanceado
- `readmitted` tem três valores. No dataset completo: cerca de 54% "NO", 35% ">30" e 11% "<30".
- No alvo binário, a classe positiva fica em cerca de 9% no teste (1.257 de 13.998).
- Um modelo que sempre diz "não" acerta cerca de 91% e não detecta ninguém. Por isso a avaliação usa recall, precisão, F1 e ROC-AUC.
- Uma frase sobre as outras variáveis (notebook 6.2 e 6.3): as contagens são muito assimétricas, com mediana zero e máximos altos (por exemplo, 76 visitas à emergência). Os outliers foram mantidos porque são casos clínicos reais.

## 4. Quem já foi muito internado volta mais
- A taxa de reinternação sobe de cerca de 8% (nenhuma internação prévia) para cerca de 48% (8 ou mais).
- Por idade a variação é pequena, com pico em 20 a 30 anos (cerca de 14%) e taxas baixas até 20 anos.
- Valores aproximados, lidos do gráfico 6.4. Confirmem na saída do notebook.
- Ponte para o fim: o tempo de internação é igual nas duas classes (gráfico 6.5), então nenhuma variável sozinha separa quem volta.

## Limpeza (sem slide, mostrar no notebook)
- "?" convertido para nulo.
- `weight` removida, por ter cerca de 97% de faltantes.
- Faltantes de `payer_code`, `medical_specialty`, `race` e dos diagnósticos preenchidos com "Desconhecido".
- Gênero "Unknown/Invalid" removido.
- Removidos pacientes que morreram ou foram para hospice (códigos de alta 11, 13, 14, 19, 20 e 21): não podem ser reinternados.
- Mantida só a primeira internação de cada paciente, para evitar vazamento entre treino e teste.
- Tamanho após a limpeza: cerca de 70 mil linhas (deduzido do teste de 13.998 linhas, que é 20% do total). Confirmem na saída do notebook, junto do número antes da limpeza.

## 5. Atributos, pipeline e modelos
- Removidos: identificadores, colunas constantes e `readmitted` (origem do alvo, geraria vazamento).
- Diagnósticos CID-9 agrupados, idade pelo ponto médio da faixa, `medical_specialty` e `payer_code` limitadas às 10 categorias mais frequentes. 9 variáveis numéricas e as demais categóricas.
- Divisão 80% treino e 20% teste, estratificada.
- `ColumnTransformer` com `StandardScaler` nas numéricas e `OneHotEncoder` nas categóricas, dentro de um `Pipeline`: o pré-processamento é aprendido só no treino.
- Modelos: Regressão Logística (baseline, interpretável) e Random Forest (200 árvores, profundidade 12, captura interações). Nos dois, `class_weight="balanced"`.

## 6. Resultados (conjunto de teste)

| Modelo | Acurácia | Precisão | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Regressão Logística | 66,3% | 13,8% | 52,2% | 21,8% | 0,649 |
| Random Forest | 70,8% | 15,4% | 50,1% | 23,6% | 0,659 |
| Sempre prevê "não" | 91,0% | 0% | 0% | 0% | 0,500 |

- O Random Forest foi o melhor: ganha em acurácia, precisão, F1 e ROC-AUC. A Regressão Logística tem recall um pouco maior (52,2% contra 50,1%).
- A acurácia engana: o baseline tem a maior acurácia e não detecta nenhum reinternado.
- ROC-AUC de 0,659: melhor que o acaso (0,5), longe do ideal (1,0). Desempenho moderado.
- Mostrem no notebook a tabela de métricas e a curva ROC.

## 7. Matriz de confusão (Random Forest, teste)

|  | Previu "não" | Previu "sim" |
|---|---|---|
| Era "não" | 9.301 (verdadeiro negativo) | 3.440 (falso positivo) |
| Era "sim" | 627 (falso negativo) | 630 (verdadeiro positivo) |

- Recall de 50,1%: detecta cerca de metade dos reinternados (630 de 1.257).
- Precisão de 15,4%: só cerca de 15% dos alertas estão certos. Para cada paciente de risco identificado, 5 a 6 recebem acompanhamento sem precisar.
- O falso negativo é o erro mais caro em saúde. O falso positivo custa um acompanhamento extra.

## 8. Variáveis importantes (Random Forest)
- `discharge_disposition_id` (em especial alta para casa), `number_inpatient` e, depois, `time_in_hospital`, `num_medications` e `num_lab_procedures`.
- A relação de `number_inpatient` com a reinternação está comprovada no gráfico do slide 4. Para alta para casa não há gráfico.
- A importância do Random Forest mostra quais variáveis o modelo mais usou, não a direção do efeito. Evitem dizer que alta para casa "reduz" o risco sem mostrar a taxa por tipo de alta.
- Leitura: o histórico recente e a complexidade do caso são os principais sinais de retorno precoce, o que confirma a hipótese inicial.

## 9. Análise crítica e próximos passos
- Desempenho moderado: serve como apoio à triagem, não para decidir sozinho. Nenhuma variável sozinha separa as classes.
- Em saúde, deixar passar um paciente de risco custa mais que um alarme falso, então o ajuste do limiar é o primeiro passo.
- Limitações: dados de 1999 a 2008, só hospitais dos EUA, sem fatores socioeconômicos nem adesão ao tratamento.
- Ética: usar raça e gênero em modelos clínicos exige cautela para não discriminar.
- Próximos passos: ajuste do limiar, busca de hiperparâmetros com validação cruzada, Gradient Boosting, SMOTE e mais engenharia de atributos.
- Fechamento: o notebook está no GitHub, os links dos slides e do vídeo estão na primeira célula. Agradecimento dos dois.

---

## Checklist de gravação

- [ ] Notebook executado do zero (reiniciar e executar tudo), com as saídas visíveis, e números conferidos com os slides.
- [ ] Confirmar no notebook os valores marcados como "confirmem" acima (tamanho antes e depois da limpeza, contagem de variáveis, colunas após o one-hot).
- [ ] Ensaio cronometrado: o limite é 10 minutos.
- [ ] Zoom do navegador ampliado e menu lateral do Colab recolhido, para o código ficar legível.
- [ ] As duas câmeras abertas o tempo todo e os dois com fala.
- [ ] Links do notebook, dos slides e do vídeo colados na primeira célula, com acesso liberado ao professor.
