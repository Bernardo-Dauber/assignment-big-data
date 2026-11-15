# Roteiro do vídeo (máx. 10 min) — apresentação no notebook

**Meta de duração:** 9 min 45 s, com 15 s de margem (cerca de 130 palavras por minuto).
**Quem fala:** **A** = Bernardo; **B** = integrante 2. Os dois mantêm a câmera aberta o tempo todo.
**Tela:** só o notebook no Colab, **já executado do início ao fim**. Os slides ficam como material de apoio e não aparecem no vídeo.
**Como usar:** em cada etapa, rolem até a seção, mostrem a saída da célula e leiam o bloco **🎤 O que encontramos** logo abaixo dela. Troquem os `[__]` pelos números reais antes de gravar.

## Linha do tempo

| Tempo | Quem | Seção do notebook | Tema |
|---|---|---|---|
| 0:00–0:30 | A e B | Topo (primeira célula e guia) | Abertura |
| 0:30–1:15 | A | 1 | Problema |
| 1:15–2:00 | A | 3 e 4 | Dataset e importação |
| 2:00–3:15 | A | 5 e 6 | Alvo e gráficos |
| 3:15–4:15 | A | 7 e 8 | Limpeza e faltantes |
| 4:15–5:15 | B | 9 a 11 | Atributos, tipos, X e y |
| 5:15–6:15 | B | 12 e 13 | Treino/teste e padronização |
| 6:15–7:00 | B | 14 | Modelos |
| 7:00–8:15 | B | 15 | Resultados |
| 8:15–9:15 | A e B | 16 | Análise crítica |
| 9:15–9:45 | A e B | 16 | Próximos passos e encerramento |

## Falas

### 0:00–0:30 · Abertura (topo do notebook)
**A:** Olá, eu sou o Bernardo.
**B:** E eu sou o [nome]. Neste vídeo apresentamos o nosso projeto: prever a reinternação de pacientes diabéticos em menos de 30 dias.
**A:** Está tudo neste notebook, já executado. Aqui na primeira célula estão o dataset, os slides e o vídeo. Eu cubro os dados e o [nome] cobre a modelagem.

### 0:30–1:15 · Problema (seção 1) — A
Reinternações em até 30 dias são um indicador de qualidade do cuidado e geram custo alto. A pergunta é: com os dados da própria internação, dá para prever quem volta nesse prazo? É uma classificação binária, 1 se reinternado em menos de 30 dias e 0 nos demais casos. Se funcionar, ajuda a priorizar o acompanhamento pós-alta.

### 1:15–2:00 · Dataset e importação (seções 3 e 4) — A
Usamos o *Diabetes 130-US Hospitals*, da UCI, com o link na primeira célula. Esta célula baixa o arquivo e importa com Pandas. Lendo o bloco de achados: são **[__] linhas por [__] colunas**, com variáveis numéricas e categóricas. Os faltantes vêm marcados com ponto de interrogação, e `weight` chega a **[__]%**. Também repara que há **[__] pacientes únicos** em [__] internações.

### 2:00–3:15 · Alvo e gráficos (seções 5 e 6) — A
*(Mostrar o gráfico do alvo.)* Só **[__]%** das internações terminam em reinternação em menos de 30 dias. O alvo é desbalanceado, então um modelo que sempre diz “não” já acertaria muito. Por isso vamos olhar recall e F1. *(Mostrar histogramas, boxplots e correlação.)* As variáveis de contagem são assimétricas e têm outliers. [Citar 1 ou 2 padrões que vocês viram, do bloco de achados.]

### 3:15–4:15 · Limpeza e faltantes (seções 7 e 8) — A
Convertemos o ponto de interrogação em valor nulo. Removemos gênero inválido, pacientes que morreram ou foram para hospice, que não podem ser reinternados, e mantivemos só a primeira internação de cada paciente, para evitar vazamento. Passamos de **[__] para [__] linhas**. A coluna `weight` foi removida e as demais viraram “Desconhecido”, e sobraram **[__]** faltantes. Agora passo para o [nome].

### 4:15–5:15 · Atributos, tipos, X e y (seções 9 a 11) — B
Obrigado, Bernardo. Aqui removemos identificadores, colunas constantes e `readmitted`, que é a origem do alvo e seria vazamento. Agrupamos os diagnósticos CID-9, transformamos a idade em ponto médio e limitamos as especialidades às mais frequentes. Ficamos com **[__] numéricas e [__] categóricas**. Separamos X e y, com `target` igual a 1 para reinternação precoce.

### 5:15–6:15 · Treino/teste e padronização (seções 12 e 13) — B
Dividimos em 80% treino e 20% teste, estratificado, para manter a proporção da classe rara. Depois, um `ColumnTransformer` aplica `StandardScaler` nas numéricas e `OneHotEncoder` nas categóricas, e depois do one-hot o treino tem **[__] colunas**. Tudo está num `Pipeline`, então o pré-processamento é aprendido só no treino, sem vazamento.

### 6:15–7:00 · Modelos (seção 14) — B
Treinamos dois modelos. A Regressão Logística é o baseline, simples e interpretável. O Random Forest, com 200 árvores e profundidade limitada, captura interações e relações não lineares. Nos dois usamos `class_weight="balanced"`, que dá mais peso à classe rara.

### 7:00–8:15 · Resultados (seção 15) — B
*(Mostrar a tabela, a matriz de confusão e a curva ROC.)* A Regressão Logística teve acurácia [__], recall [__], F1 [__] e ROC-AUC [__]. O Random Forest teve [__], [__], [__] e [__]. [Dizer qual foi melhor e por quê.] A acurácia isolada engana: o modelo que sempre diz “não” teria **[__]**. O melhor modelo detecta [__]% dos reinternados, com **[__] falsos negativos** e **[__] falsos positivos**. As variáveis mais importantes foram [__], [__] e [__], o que [confirma / contradiz] nossa hipótese inicial.

### 8:15–9:15 · Análise crítica (seção 16) — A e B
**A:** O desempenho [é / não é] suficiente para uso real: [comentar ROC-AUC e recall]. Em saúde, deixar passar um paciente de risco costuma custar mais que um alarme falso, então ajustar o limiar de decisão seria o próximo passo.
**B:** Há limitações: dados de 1999 a 2008, só de hospitais dos EUA, sem fatores socioeconômicos nem adesão ao tratamento. E precisamos de cuidado ético ao usar raça e gênero em modelos clínicos.

### 9:15–9:45 · Próximos passos e encerramento (seção 16) — A e B
**B:** Para melhorar, testaríamos busca de hiperparâmetros com validação cruzada, Gradient Boosting, ajuste de limiar, SMOTE e mais engenharia de atributos.
**A:** O notebook está no GitHub, e os slides e este vídeo estão linkados na primeira célula.
**A e B:** Obrigado!

## Checklist de gravação

- [ ] Notebook executado do zero (Reiniciar e executar tudo), com as saídas visíveis.
- [ ] Todos os `[__]` preenchidos nos blocos 🎤 do notebook e nas falas acima.
- [ ] Ensaio cronometrado: se passar de 9 min 45 s, cortar detalhes dos gráficos.
- [ ] Zoom do navegador ampliado e menu lateral do Colab recolhido, para o código ficar legível.
- [ ] As duas câmeras abertas o tempo todo e os dois com fala.
- [ ] Links dos slides e do vídeo colados na primeira célula, com acesso liberado ao professor.
