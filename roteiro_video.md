# Material para o roteiro do vídeo (máx. 10 min)

Este arquivo reúne, slide por slide, o que mostrar e a informação disponível. As falas ficam por conta de vocês: leiam o conteúdo de cada bloco e montem o texto do jeito de cada um.

**Regras do vídeo:** no máximo 10 minutos, os dois integrantes com câmera aberta e os dois falando. Meta: terminar em 9 min 30 s.
**Tela:** os slides acompanham a fala. Na hora de mostrar o código e as saídas, alternem para o notebook executado no Colab, na seção indicada em cada bloco.
**Estrutura:** a apresentação tem duas partes. Parte 1 (slides 2 a 7): o dataset escolhido, o que os dados mostram e os insights. Parte 2 (slides 8 a 13): os dois modelos, por que os escolhemos, a comparação dos resultados e os insights.
**Divisão sugerida:** o integrante 1 fala os slides 1 a 7 (parte 1), o integrante 2 fala os slides 8 a 12 (parte 2), e os dois fecham no 13.

## Ordem e tempo sugerido (13 slides)

| # | Slide | Notebook | Quem | Tempo |
|---|---|---|---|---|
| 1 | Capa | Primeira célula | 1 e 2 | 0:20 |
| 2 | O dataset escolhido | Seções 1, 3 e 4 | 1 | 0:50 |
| 3 | Quem são os pacientes | Seções 4 e 5 | 1 | 0:30 |
| 4 | Campos vazios | Seções 4 e 8 | 1 | 0:30 |
| 5 | O alvo é raro | Seções 5.1 e 6.1 | 1 | 0:45 |
| 6 | Insight 1: histórico | Seção 6.4 | 1 | 0:50 |
| 7 | Insight 2: sinais fracos | Seções 6.4 a 6.6 | 1 | 0:45 |
| 8 | Preparo dos dados | Seções 7 a 13 | 2 | 0:50 |
| 9 | Os dois modelos | Seção 14 | 2 | 0:55 |
| 10 | Resultados | Seção 15 | 2 | 0:55 |
| 11 | Matriz de confusão | Seção 15 | 2 | 0:50 |
| 12 | Variáveis importantes | Seção 15 | 2 | 0:40 |
| 13 | Conclusões | Seção 16 | 1 e 2 | 0:55 |

Total: cerca de 9 min 25 s. Se sobrar tempo, mostrem no notebook os histogramas e boxplots (6.2 e 6.3), que não têm slide próprio.

---

# PARTE 1: o dataset e os insights dos dados

## 1. Capa
- Título: previsão de reinternação em menos de 30 dias de pacientes diabéticos.
- Nome dos dois integrantes e a divisão de quem fala o quê.
- Avisar que o roteiro tem duas partes: o dataset e o que os dados mostram, depois os dois modelos e a comparação.
- O código completo está no notebook; os links do notebook, dos slides e do vídeo estão na primeira célula.

## 2. O dataset escolhido
- Diabetes 130-US Hospitals, repositório UCI (dataset 296): **101.766 internações, 50 colunas, 71.518 pacientes únicos, 130 hospitais, 1999 a 2008**.
- Por que este dataset: (1) problema real, pois a reinternação em até 30 dias é indicador de qualidade do cuidado e gera custo alto; (2) dados reais e sujos, com faltantes, inconsistências e vários registros por paciente; (3) alvo desafiador, só cerca de 1 em cada 9 internações volta em menos de 30 dias.
- Pergunta: com os dados da internação, dá para prever quem volta em menos de 30 dias? Classificação binária supervisionada.
- Uso prático: priorizar o acompanhamento pós-alta de quem tem maior risco.

## 3. Quem são os pacientes e o que foi registrado
Estatísticas sobre as 101.766 internações originais (saídas da seção 4 do notebook):
- Faixa etária mais comum: 70 a 80 anos (26.068 internações, 25,6%).
- 74,8% caucasianos (76.099); 53,8% mulheres (54.708).
- Mediana de 4 dias de internação (média 4,4, máximo 14); média de 16 medicamentos por internação.
- 77% recebem medicação para diabetes (`diabetesMed` = Yes, 78.363); 53% têm insulina registrada (`insulin` diferente de "No": 101.766 − 47.383).
- Grupos de variáveis: perfil, admissão e alta, tempo, exames e medicamentos da internação, histórico de visitas, diagnósticos CID-9, glicose e HbA1c, 23 medicamentos.
- Dado extra se sobrar tempo: as contagens de visitas anteriores são muito assimétricas, com mediana zero e máximos altos (76 visitas à emergência, 21 internações). Os outliers foram mantidos porque são casos clínicos reais.

## 4. Muitos campos importantes vêm vazios
- Faltantes (`?` ou nulos): peso 96,9%, glicose sérica 94,7% (exame não feito), HbA1c 83,3% (exame não feito), especialidade médica 49,1%, convênio 39,6%, raça 2,2%.
- Tratamento: peso removido; especialidade, convênio, raça e diagnósticos viram "Desconhecido"; exame não realizado é uma categoria válida.
- Insight: a HbA1c só foi medida em 16,7% das internações (17.018 de 101.766).
- Mostrar no notebook a tabela de faltantes (seção 4) e o código da seção 8.

## 5. O alvo é raro
- `readmitted`: 53,9% "NO" (54.864), 34,9% ">30" (35.545) e 11,2% "<30" (11.357).
- Alvo binário: 1 se "<30", 0 nos demais. Depois da limpeza a taxa de positivos é 9,0% (8,98%); no teste, 1.257 de 13.998.
- Um modelo que sempre diz "não" acerta 91,0% e não detecta nenhum reinternado. Por isso usamos recall, precisão, F1 e ROC-AUC.

## 6. Insight 1: quem já foi internado antes volta mais
- Taxa de reinternação em menos de 30 dias por nº de internações no ano anterior: 8,5% (0), 12,9% (1), 17,3% (2), 20,2% (3), 23,6% (4), 31,3% (5), 34,5% (6), 35,3% (7), 47,8% (8 ou mais). Mais de 5 vezes entre os extremos (5,6×).
- `number_inpatient` tem correlação 0,17 com o alvo, a maior entre as numéricas.
- Valores lidos do gráfico 6.4 do notebook (sobre as 101.766 internações originais): confirmem na saída se quiserem o número exato de cada barra.

## 7. Insight 2: os outros sinais são fracos, um a um
- Por idade a taxa fica em torno de 10 a 12%, com 1,8% (0 a 10), 5,8% (10 a 20) e pico de 14,2% em 20 a 30 anos.
- Correlações com o alvo: internações prévias 0,17; emergência 0,06; nº de diagnósticos 0,05; tempo de internação 0,04; nº de medicamentos 0,04; ambulatório 0,02 (matriz 6.6).
- Tempo de internação: mediana de 4 dias nas duas classes (boxplot 6.5).
- Conclusão que liga as duas partes: nenhuma variável sozinha separa quem volta, então é preciso combinar sinais em um modelo.

---

# PARTE 2: os modelos e a comparação

## 8. Preparo dos dados (igual para os dois modelos)
- Limpeza: de 101.766 para 69.987 linhas. Removidos gênero inválido, óbito e hospice (códigos de alta 11, 13, 14, 19, 20 e 21, que não podem ser reinternados) e mantida só a primeira internação de cada paciente, para evitar vazamento entre treino e teste.
- Atributos: removidos identificadores, `readmitted` (origem do alvo) e colunas constantes; diagnósticos CID-9 agrupados, idade pelo ponto médio da faixa, especialidade e convênio limitados às 10 categorias mais frequentes. Ficam 33 variáveis (9 numéricas e 24 categóricas), que viram 167 colunas depois do One-Hot.
- Divisão 80/20 estratificada: 55.989 para treino e 13.998 para teste, com 9,0% de positivos nos dois.
- `ColumnTransformer` com `StandardScaler` nas numéricas e `OneHotEncoder` nas categóricas, dentro de um `Pipeline`: o pré-processamento é aprendido só no treino.
- Nos dois modelos, `class_weight="balanced"`.

## 9. Os dois modelos e por que os escolhemos
- **Regressão Logística** (baseline): soma ponderada das variáveis padronizadas, convertida em probabilidade. Por quê: simples e interpretável, serve de régua para medir se um modelo mais complexo compensa. Limitação: só enxerga relações lineares.
- **Random Forest** (200 árvores, profundidade máxima 12, mínimo de 20 amostras por folha): por quê: captura relações não lineares e interações, e lida bem com as contagens assimétricas e outliers sem transformação. Limitação: menos interpretável; a importância mostra o uso, não a direção do efeito.
- Mesma entrada, mesmo split (`random_state` 42) e mesmo `class_weight`: a comparação é justa. A pergunta é se a complexidade extra compensa.

## 10. Resultados no conjunto de teste

| Modelo | Acurácia | Precisão | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Regressão Logística | 66,3% | 13,8% | 52,2% | 21,8% | 0,649 |
| Random Forest | 70,8% | 15,4% | 50,1% | 23,6% | 0,659 |
| Sempre prevê "não" | 91,0% | 0% | 0% | 0% | 0,500 |

- O Random Forest ganha em 4 de 5 métricas (acurácia, precisão, F1 e ROC-AUC). A Regressão Logística tem recall dois pontos maior (52,2% contra 50,1%).
- Ganho pequeno: a ROC-AUC sobe só 0,010. Desempenho moderado nos dois: acima do acaso (0,5), longe do ideal (1,0).
- Mostrem no notebook a tabela de métricas e a curva ROC (seção 15).

## 11. Matriz de confusão (teste, 12.741 "não" e 1.257 "sim")

| | Previu "não" | Previu "sim" |
|---|---|---|
| **Regressão Logística** | | |
| Era "não" | 8.627 | 4.114 |
| Era "sim" | 601 | 656 |
| **Random Forest** | | |
| Era "não" | 9.280 | 3.461 |
| Era "sim" | 627 | 630 |

- Troca vantajosa do Random Forest: 653 alarmes falsos a menos (4.114 para 3.461) ao custo de 26 reinternados detectados (656 para 630).
- Precisão de 15,4% (630 de 4.091 alertas): para cada paciente de risco identificado, 5 a 6 recebem acompanhamento sem precisar.
- O falso negativo é o erro mais caro em saúde: 627 reinternados ficam sem alerta.
- Nota: o texto da seção 15 do notebook já foi corrigido para 3.461 falsos positivos, o valor da figura da matriz (consistente com a precisão de 0,154).

## 12. Variáveis importantes (Random Forest)
- Em ordem aproximada, lida do gráfico: alta para casa (`discharge_disposition_id_1`, cerca de 0,09), `number_inpatient` (0,075), `time_in_hospital` (0,057), `num_medications` (0,055), `num_lab_procedures` (0,055), alta tipo 22 (0,05), `age` (0,045) e `number_diagnoses` (0,036).
- Confirma a hipótese inicial: histórico recente e complexidade do caso pesam mais. `number_inpatient` aparece nos dois lados: maior taxa na análise exploratória e 2º lugar no modelo.
- A importância do Random Forest mostra quais variáveis o modelo mais usou, não a direção do efeito. Evitem dizer que alta para casa "reduz" o risco sem mostrar a taxa por tipo de alta.
- Os rótulos "alta para casa" (código 1) e "alta para reabilitação" (código 22) vêm do `IDs_mapping.csv` do UCI: confirmem no arquivo antes de gravar.

## 13. Conclusões
- (1) O histórico é o melhor sinal: 8 ou mais internações prévias, 5,6× a taxa de quem não tem nenhuma; 2º sinal do Random Forest.
- (2) Random Forest ganha, mas pouco: ROC-AUC 0,659 contra 0,649.
- (3) A acurácia engana: "sempre não" dá 91%. Com recall de cerca de 50% e precisão de cerca de 15%, o modelo apoia a triagem, não decide sozinho.
- Próximos passos: ajuste do limiar de decisão (perder um paciente de risco custa mais que um alarme falso), validação cruzada com busca de hiperparâmetros, Gradient Boosting e SMOTE.
- Limitações: dados de 1999 a 2008, só hospitais dos EUA, sem fatores socioeconômicos nem adesão ao tratamento. Ética: usar raça e gênero em modelos clínicos exige cautela para não discriminar.
- Fechamento: o notebook está no GitHub, os links dos slides e do vídeo estão na primeira célula. Agradecimento dos dois.

---

## Checklist de gravação

- [ ] Notebook executado do zero (reiniciar e executar tudo), com as saídas visíveis, e números conferidos com os slides.
- [x] Textos do notebook corrigidos (falsos positivos 3.461, faltantes restantes 123.779, taxa de 8,5% para 47,8%). Se usarem o Colab, reenviem este `.ipynb` ou copiem as três observações.
- [ ] Completar o nome do integrante 2 na capa dos slides e na primeira célula do notebook.
- [ ] Ensaio cronometrado: o limite é 10 minutos.
- [ ] Zoom do navegador ampliado e menu lateral do Colab recolhido, para o código ficar legível.
- [ ] As duas câmeras abertas o tempo todo e os dois com fala.
- [ ] Links do notebook, dos slides e do vídeo colados na primeira célula, com acesso liberado ao professor.
