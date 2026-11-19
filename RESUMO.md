# Resumo para a prova — Inteligência Artificial (PRG0010)

Resumo feito a partir dos slides do repositório (aulas 2, 3, 6 a 13). Os arquivos das aulas 1, 4 e 5 não estão no repositório, então não aparecem aqui. Slides que só tinham imagens (gráficos, fotos, tabelas desenhadas) não foram lidos; confira-os direto no PDF.

## Índice

1. [Busca (aula 2)](#1-busca-aula-2)
2. [Aprendizado de Máquina: conceitos e avaliação (aula 3)](#2-aprendizado-de-máquina-conceitos-e-avaliação-aula-3)
3. [Classificação e Árvores de Decisão (aula 3, partes 1 e 2)](#3-classificação-e-árvores-de-decisão-aula-3-partes-1-e-2)
4. [Aprendizado Profundo (aula 6)](#4-aprendizado-profundo-aula-6)
5. [Agrupamento e k-means (aula 7)](#5-agrupamento-e-k-means-aula-7)
6. [Aprendizado por Reforço (aula 8)](#6-aprendizado-por-reforço-aula-8)
7. [Processamento de Linguagem Natural (aula 9)](#7-processamento-de-linguagem-natural-aula-9)
8. [LLMs e Agentes Conversacionais (aula 10)](#8-llms-e-agentes-conversacionais-aula-10)
9. [Representação de Conhecimento (aula 11)](#9-representação-de-conhecimento-aula-11)
10. [Ética, Avaliação de Impacto e Regulação (aula 12)](#10-ética-avaliação-de-impacto-e-regulação-aula-12)
11. [Visão Computacional (aula 13)](#11-visão-computacional-aula-13)
12. [Fórmulas para decorar](#12-fórmulas-para-decorar)
13. [Exercícios resolvidos](#13-exercícios-resolvidos)
14. [Perguntas de revisão](#14-perguntas-de-revisão)

---

## 1. Busca (aula 2)

Prof. Fabio Cozman.

### Agentes
- **Agente**: artefato que age sobre um ambiente. Recebe **percepções**, executa **ações**, e usa conhecimento, objetivos e preferências.
- **Agente inteligente**: planeja, decide e aprende levando em conta o que sabe do domínio.
- Características possíveis: modular/hierárquico, determinístico ou não, estado observável ou não, capaz de aprender/planejar ou não, baseado em racionalidade formal ou em imitação.
- **Hipótese do Sistema Físico de Símbolos**: um sistema físico que manipula símbolos tem os meios necessários e suficientes para agir de forma inteligente.

### Problema como busca
Um problema é codificado por:
1. um conjunto de **estados** (alguns iniciais, outros objetivo);
2. uma função de **transições** entre estados.

**Busca** = achar uma sequência de transições do estado inicial até um estado objetivo. Ex.: quebra-cabeça de 8 peças, Blocks World.

**Planejamento STRIPS**: estado descrito por predicados (`on(B,A)`, `on(C,table)`). Cada ação tem **pré-condições**, **delete list** e **add list**. Ex.: `pickup(x)` exige `on(x,table)`, remove `on(x,table)`, adiciona `holding(x)`. A busca pode ser **para frente** (do inicial ao final) ou **para trás** (do final ao inicial, verificando pré-condições).

### Algoritmo genérico de busca em grafo
```
Coloque o estado inicial na fronteira.
Enquanto a fronteira não esvaziar:
    Remova um caminho da fronteira.
    Se o último nó é o objetivo, retorne o caminho.
    Senão, para cada vizinho do último nó, adicione à fronteira o caminho estendido.
```
O que muda entre os algoritmos é **a estrutura da fronteira**:

| Algoritmo | Fronteira | Propriedades |
|---|---|---|
| Profundidade (DFS) | **Pilha** (LIFO) | Pouca memória; faz *backtracking*; pode achar caminhos muito longos |
| Largura (BFS) | **Fila** (FIFO) | Acha o caminho mais **curto** (em nº de arestas); gasta muita memória |
| Aprofundamento iterativo | DFS com limite crescente | Junta a memória baixa do DFS com a garantia do BFS |
| Menor custo (*lowest cost first*) | Fila ordenada por **g(s)** (custo acumulado) | Se os custos são positivos e maiores que uma constante, acha o caminho de menor custo |
| Gulosa (*greedy best-first*) | Ordenada por **h(s)** | Simples, mas poucas garantias |
| **A\*** | Ordenada por **f(s) = g(s) + h(s)** | Ótima com heurística admissível |

Obs.: os slides supõem que o grafo é uma árvore. Em grafos com ciclos é preciso adaptar (ex.: guardar nós já explorados).

### Heurísticas e A\*
- **Heurística h(s)**: estimativa barata do menor custo de s até o objetivo.
- **Admissível**: h(s) ≥ 0 e **nunca superestima** o custo real até o objetivo.
- Como obter: **simplificar (relaxar) o problema** original e resolver a versão simples.
- **Teorema do A\***: se existe solução, o grafo é finito e todo arco tem custo maior que algum número positivo, A\* com heurística admissível **sempre retorna um caminho de custo mínimo**.
- Outras ideias: catálogo de soluções conhecidas, passar por estados que obrigatoriamente serão visitados, **branch-and-bound** (podar o grafo).

### Busca local (procurar um objeto, não um caminho)
- **Hill climbing**: olha só os vizinhos imediatos. Problema: fica preso em **máximo/mínimo local**. Remédios: reiniciar de vários pontos, **local beam search**.
- **Simulated annealing**: busca local, mas pode "pular" aleatoriamente para outro estado. A probabilidade de pular **diminui com o tempo**.
- **Algoritmos genéticos**: população de indivíduos; seleção por **fitness**; **crossover** (ex.: de (x1,x2,x3,x4) e (y1,y2,y3,y4) gera (x1,x2,y3,y4)); **mutação**.
- **Descida do gradiente** (contínuo): parte de um ponto e anda na direção **contrária ao gradiente** até gradiente nulo. Variantes: inicialização, tamanho do passo, **gradiente estocástico** (muitos dados), segundas derivadas.

**Ideia central**: representar estado inicial, estados desejados e transições, e buscar a solução.

---

## 2. Aprendizado de Máquina: conceitos e avaliação (aula 3)

Prof. André de Carvalho.

### Raciocínio
- **Dedução**: do geral para o específico (*top-down*). Ex.: polícia suspeita de Y e busca provas.
- **Indução**: do específico para o geral (*bottom-up*). Ex.: analisa evidências e conclui que foi Y. **AM é indutivo.**

### Definições
- Termo popularizado por **Arthur Samuel (1959, IBM)**, com um programa de damas (usou poda alfa-beta sobre minimax).
- Samuel: dar aos computadores a habilidade de aprender sem serem explicitamente programados.
- **Mitchell (1997)**: um programa aprende com a experiência **E**, em relação a tarefas **T** e medida de desempenho **P**, se seu desempenho em T, medido por P, melhora com E.
  - Xadrez: T = jogar; P = % de vitórias; E = jogos contra si mesmo.
  - Diagnóstico: T = diagnosticar; P = % de acertos; E = pacientes com diagnóstico correto.
- Computação tradicional: pessoa escreve o algoritmo; programa que acerta 90% é ruim. AM: modelo aprende com dados; modelo que acerta 51% pode ser bom.
- AM envolve estatística, álgebra linear, lógica matemática e otimização.

### Aplicações clássicas
| Sistema | Ano | O quê |
|---|---|---|
| Damas (Samuel) | 1959 | Jogar damas |
| NETtalk | 1986 | Rede neural que lê em voz alta (texto → fonemas). 95% treino, 78% teste. Perdeu para o DECtalk (sistema especialista de regras, ~10 anos de desenvolvimento) |
| SPHINX | 1989 | Reconhecer fala |
| ALVINN | 1989 | Dirigir carro. Rede com 960 entradas (30×32 pixels), 4 ocultos, 30 saídas. Andou a 110 km/h |
| TD-Gammon | 1992 | Gamão |

### Dados
- 181 ZB gerados em 2025, ~80% **não estruturados** (imagens, texto). Dados estruturados = tabela (atributos × exemplos).
- **Tipos de atributo**:
  - Qualitativos: **nominal** (cor, profissão) e **ordinal** (ruim/médio/bom).
  - Quantitativos: **intervalar** (data, °C, sem zero absoluto) e **racional** (peso, idade, Kelvin).
- Conjuntos **rotulados**, **não rotulados** e **parcialmente rotulados**. Atributos de entrada (preditivos) e atributo **alvo** (classe ou valor real).

### Tarefas de aprendizado
- **Preditivas** (precisam de rótulo):
  - **Classificação**: saída categórica (fronteira de decisão). Ex.: diagnóstico, fraude.
  - **Regressão**: saída real (aproximação de função). Ex.: preço de imóvel.
- **Generativas**: criam conteúdo novo.
  - **Decodificadoras**: geram o próximo elemento (LLMs como GPT, Gemini, Claude).
  - **Codificadoras-decodificadoras**: tradução, texto↔fala, estrutura 3D de proteínas.
  - Tarefas só codificadoras são preditivas.
- **Prescritivas**: dizem qual entrada gera a saída desejada (controle de robôs, projeto de materiais).
- **Descritivas** (não precisam de rótulo):
  - **Agrupamento**: maximizar similaridade dentro do grupo e dissimilaridade entre grupos.
  - **Associação**: itens frequentes (frequência > supMin) e regras `SE X ENTÃO Y` com X ∩ Y = ∅. Ex.: compra manteiga e queijo → compra pão.
  - **Sumarização**: descrição compacta (média, moda, frequências).

### Modelos, parâmetros e hiperparâmetros
- **Modelo** = função matemática. Ex.: f(x) = ax + b = 2x + 1.
- **Parâmetros** (a, b): ajustados pelo algoritmo.
- **Hiperparâmetros**: ajustados por quem usa o algoritmo (ou por metaheurísticas). Mais hiperparâmetros = mais flexível e mais difícil de ajustar.

### Paradigmas
- **Supervisionado**: usa atributos preditivos + alvo. Treino (indução) → teste (dedução). Classificação e regressão.
  - Baseados em **distância** (k-NN), **otimização/conexionistas** (redes neurais), **probabilidade** (Naive Bayes), **procura/lógicos** (árvores de decisão).
- **Não supervisionado**: só atributos preditivos. Principalmente agrupamento. Avaliação é subjetiva.
- **Semi-supervisionado** (caso especial: aprendizado ativo).
- **Por reforço**.

### Viés indutivo
- Escolhas que restringem o espaço de busca. **Sem viés não há generalização.**
- **Viés de preferência (busca)**: como as hipóteses são procuradas (ex.: preferir hipóteses simples).
- **Viés de representação (linguagem)**: quais hipóteses podem existir (ex.: árvore de decisão, rede neural, rede bayesiana).
- É diferente do **viés estatístico**.

### Viés × variância
| | Viés (estatístico) alto | Variância alta |
|---|---|---|
| O que é | Não aprende os padrões dos dados | Aprende detalhes irrelevantes (ruído) |
| Problema | **Underfitting** | **Overfitting** |
| Analogia | "Desatenção" | "Alucinação" |

Aumentar a complexidade do modelo reduz o viés e aumenta a variância. O ótimo está no meio, onde o erro de teste é mínimo.

| Erro | Overfitting | Correto | Underfitting |
|---|---|---|---|
| Treino | Baixo | Baixo | Alto |
| Teste | Alto | Baixo | Alto |

### Amostragem para avaliação
- **Hold-out**: uma divisão (50/50, 2/3–1/3, 75/25, 80/20). Pouco confiável (sorte/azar).
- **Random subsampling**: vários hold-outs aleatórios.
- **k-fold cross-validation**: divide em k partes; cada uma é teste uma vez. Parte do treino pode virar **validação**.
- **5×2 cross-validation**: 5 vezes, divide em duas metades e troca treino/teste.
- **Leave-one-out**: k = N. Aproxima o erro verdadeiro, mas é caro e tem variância alta. 10-fold aproxima leave-one-out.
- **Bootstrap**: amostragem **com reposição** de tamanho N; o que sobra vira teste. A amostra contém ~**63,2%** dos exemplos originais. Repetido k vezes, faz-se a média.

Desempenho também inclui: custo de treino, tempo, memória e **interpretabilidade**.

### Métricas de regressão
- **SSE** = Σ (yᵢ − f(xᵢ))²
- **MSE** = (1/N) Σ (yᵢ − f(xᵢ))²
- **RMSE** = √MSE (mesma unidade de y, mais fácil de interpretar)
- **MAE** = (1/N) Σ |yᵢ − f(xᵢ)| (mesma unidade de y)
- **MAPE** = (100%/N) Σ |yᵢ − f(xᵢ)| / |yᵢ|
- **R²** = 1 − MSE / Var(y) (**maior é melhor**; as outras: menor é melhor)

### Métricas de classificação
Tipos: binária, multiclasse, multirrótulo, hierárquica, ranking, uma única classe.

**Matriz de confusão** (binária):

| | Predito P | Predito N |
|---|---|---|
| **Real P** | VP | FN |
| **Real N** | FP | VN |

- **FP** = alarme falso (diz que está doente, mas está saudável).
- **FN** = diz que está saudável, mas está doente.

| Métrica | Fórmula | Intuição |
|---|---|---|
| Acurácia | (VP+VN)/(VP+VN+FP+FN) | % de acertos |
| **Precisão** (VPP) | VP/(VP+FP) | "não tem intrusos" |
| **Revocação / Recall / Sensibilidade** (TVP) | VP/(VP+FN) | "todos os positivos são lembrados" |
| **Especificidade** (TVN) | VN/(VN+FP) | negativos reconhecidos |
| VPN | VN/(VN+FN) | |
| TFP | FP/(FP+VN) = 1 − especificidade | |
| TFN | FN/(FN+VP) = 1 − TVP | |
| **F1** | 2·prec·rev/(prec+rev) | média harmônica de precisão e revocação |

**ROC**: eixo x = TFP (1 − especificidade), eixo y = TVP (sensibilidade). Canto (0,1) = classificador perfeito. Diagonal = aleatório. **AUC** ∈ [0,1]: 1 = perfeito, 0,5 = sem poder de discriminação. Calculada somando trapézios; é melhor usar a média de várias AUCs.

---

## 3. Classificação e Árvores de Decisão (aula 3, partes 1 e 2)

Prof. Ricardo Cerri.

### Classificação
- Rótulos **discretos**; objetivo: dizer a classe de um exemplo novo. Ex.: spam, crédito, fraude, dígitos, sentimentos.
- Problemas **lineares** (uma reta separa as classes) e **não lineares**.
- Detectar overfitting olhando só o erro de treino é difícil, por isso é preciso **validar** antes de testar.
- Algoritmos: árvores de decisão, conjuntos de regras, redes neurais, SVM, k-NN, regressão logística, redes bayesianas.
- **Multiclasse**: uma classe entre várias. Estratégias com classificadores binários: **um-contra-todos** e **um-contra-um**.
- **Multirrótulo**: um exemplo pode ter várias classes ao mesmo tempo (imagens, textos, funções de proteínas).

### Árvores de decisão
- **Nós internos** = atributos; **arcos** = valores do atributo; **folhas** = classes.
- Equivalem a um conjunto de **regras SE...ENTÃO** (cada caminho raiz→folha é uma regra).
- Indução: particionar **recursivamente** o treino até cada subconjunto ter uma única classe; meta: classificar o máximo de exemplos com a menor árvore.

**ID3 (Quinlan, 1979)**: só atributos **nominais**; critério = **ganho de informação**; **sem poda**; não trata valores ausentes.

**Entropia** (2 classes): H(S) = −p₁ log₂ p₁ − p₀ log₂ p₀
- Perto de 0: pouca desordem (quase tudo de uma classe).
- Perto de 1: muita desordem. 50%/50% → H = 1 (máximo).
- Com c classes: H(S) = −Σᵢ (nᵢ/n) log₂(nᵢ/n).

**Entropia restante** de um atributo A com d valores:
ERestante(A) = Σₖ [(pₖ+nₖ)/(p+n)] · H(Eₖ)

**Ganho(A) = H(S) − ERestante(A)** → escolhe-se o atributo de **maior ganho**.

Exemplo dos filmes (10 filmes, 5 sucesso e 5 não, H(S) = 1):
- Ganho(País de origem) = 1 − 0,7244 = **0,2756** ← escolhido
- Ganho(Gênero) = 1 − 0,826 = 0,174
- Ganho(Estrela) = 1 − 0,9649 = 0,0351

Depois, o processo se repete em cada ramo.

### Random Forest
- Coleção de árvores de decisão; cada uma treinada numa amostra **bootstrap**.
- Cada árvore pode usar um **subconjunto aleatório de atributos** → árvores pouco correlacionadas.
- Predição final por **votação da maioria** (comitê vence árvore individual).
- **Amostra Out-Of-Bag (OOB)**: exemplos que ficaram fora do bootstrap de uma árvore. Usada para estimar erro e **importância de atributos**: compara o erro OOB original com o erro após **permutar** um atributo. Diferença grande = atributo importante.

---

## 4. Aprendizado Profundo (aula 6)

Prof. Artur Jordão.

### Rede neural
- **Pesos** (reais, inicializados aleatoriamente), **neurônios** (produto escalar entrada·pesos), **camadas**, **ativações** (não lineares).
- Rede como composição de funções: F(x, θ) = f_L(f_{L−1}(... f₁(x, θ₁) ...), θ_L). Cada camada aplica uma transformação não linear.
- Camadas iniciais aprendem padrões simples; camadas profundas, padrões complexos.

### Conceitos
- **Capacidade**: quanto o modelo consegue se ajustar a funções variadas. Com capacidade suficiente, aproxima qualquer função contínua. Controlada pelo nº de camadas e de neurônios. Consenso: redes grandes generalizam melhor.
- **Superparametrização**: mais parâmetros do que exemplos. O risco empírico (erro de treino) chega facilmente a zero; redes conseguem **memorizar rótulos aleatórios**.
- **Arquitetura modular**: projeta-se um **bloco de construção** e ele é repetido (×N) para formar o modelo.
- **Regularização** (modelos profundos precisam muito): **weight decay** (L(W) + λ·Complexidade(W)), **dropout**, **Batch Normalization** (permitiu redes com 100+ camadas), LayerNorm, RMSNorm.

### Redes residuais (ResNet, He et al. 2016)
- **Problema da degradação**: aumentar a profundidade faz a precisão saturar e cair, e **não por overfitting** (o erro de treino também piora).
- **Skip connection**: camada plain produz f(x); camada residual produz **f(x) + x**.
- Justificativa: se a rede rasa F_S ⊂ rede profunda F_D, a profunda não deveria ter erro de treino maior. Hoje quase todas as arquiteturas usam aprendizado residual.

### Dados e leis de escala
- Modelos profundos dependem muito de dados. Ex.: em bases pequenas, ResNet-50 supera ViT.
- **Dados sintéticos** ajudam na avaliação e no aumento de dados.
- **Leis de escala**: leis de potência que relacionam a perda com o tamanho do modelo (N) e dos dados (D), descobertas empiricamente. Ex. do slide: D ∝ N^0,74.

### Marcos históricos
1986 MLP + backpropagation · 2012 AlexNet vence ImageNet · 2015 Batch Norm · 2016 ResNet · 2017 Transformers · 2020 GPT-3 · 2021 CLIP · 2022 ChatGPT · 2023 LLaMA · 2024 Hinton ganha Nobel de Física.

Em **dados tabulares**, redes neurais historicamente não superavam modelos de árvore (XGBoost).

### Desafios
- **Ataques adversariais**: pequenas perturbações (imperceptíveis) fazem a rede errar (placa "Pare" → "Siga"). Causa: superparametrização e alta capacidade.
- **Shortcut learning**: o modelo aprende atalhos que funcionam no benchmark mas falham fora da distribuição. Ex.: detectar pneumonia olhando o "token" do hospital em vez do pulmão.
- **Reward hacking** e incidentes de segurança com agentes (exemplo OpenAI/Hugging Face nos slides).
- Concentração na indústria (~90% dos modelos notáveis); crise de reprodutibilidade e explosão de submissões em conferências.

### Mudança de paradigma
| AM "clássico" | Moderno |
|---|---|
| Um modelo, treinado do zero | Repositório de modelos, muitas vezes via API |
| O modelo é o "ingrediente secreto" | Os **dados** são o ingrediente secreto |
| Fácil de ajustar | Ajuste não trivial |
| Métricas bem definidas | Avaliação por modelos (*LLM-as-a-Judge*) |

**Letramento em IA**: conhecer capacidades e limites, acompanhar a área, uso ético, não tratar a saída do modelo como verdade absoluta.

---

## 5. Agrupamento e k-means (aula 7)

Prof. Eduardo Hruschka.

- **Agrupamento**: achar grupos em que objetos do mesmo grupo são parecidos e de grupos diferentes são distintos → **minimizar distâncias intra-cluster** e **maximizar inter-cluster**.
- "Grupo" é **subjetivo**; o resultado depende de medida de (dis)similaridade, índices de avaliação, parâmetros e domínio (relação com **viés indutivo**).
- Aplicações: segmentação de clientes (frequência, ticket médio, recência, engajamento), recomendação, fraude, segmentação de imagens, expressão gênica, epidemiologia, organização de documentos.

### Algoritmo k-means (MacQueen, 1967)
1. Escolher aleatoriamente **k centros**.
2. Atribuir cada objeto ao **centro mais próximo** (ex.: distância euclidiana).
3. Mover cada centro para a **média (centróide)** do seu cluster.
4. Repetir 2–3 até convergir (máx. de iterações ou mudança mínima dos centróides).

**Função objetivo**: J = Σ_c Σ_{x∈C_c} ‖x − x̄_c‖² (soma dos erros quadráticos = variância intra-cluster).
Visto como otimização alternada:
- **Passo E**: fixa centros e atribui cada ponto ao centro mais próximo.
- **Passo M**: fixa atribuições; derivando J e igualando a zero, o centro ótimo é a **média** dos pontos do cluster.

**Inicialização**: o resultado depende das sementes e pode cair em ótimo local. Solução: rodar várias vezes e escolher a partição de **menor J**.

| Vantagens | Desvantagens |
|---|---|
| Simples e intuitivo | Precisa definir **k** |
| Complexidade **linear** | Sensível à inicialização (mínimos locais) |
| Eficaz em muitos cenários | Só acha clusters **globulares/esféricos** |
| Fácil de interpretar | Partição **rígida** (cada objeto em um só grupo) |
| | Só atributos **numéricos** |
| | Sensível a **outliers** |

**Escolher k**: J sempre diminui com k (ex.: k=1 → 873, k=2 → 173, k=3 → 134), então não dá para minimizar J diretamente. Usa-se o **método do cotovelo** (onde a queda de J deixa de ser grande) ou **índices de validade relativos**.

---

## 6. Aprendizado por Reforço (aula 8)

Profa. Anna Helena Reali Costa.

- Agente autônomo aprende a escolher ações que maximizam a **recompensa acumulada a longo prazo**.
- Características: retorno **atrasado** e ocasional; **o tempo importa** (dados sequenciais, não i.i.d.); ações afetam os dados futuros; aprendizado por **tentativa e erro**.
- Ciclo: estado sₜ → ação aₜ → novo estado sₜ₊₁ e reforço rₜ₊₁. Experiência = ⟨s, a, s', r⟩.

### MDP — Processo de Decisão de Markov ⟨S, A, T, R, γ⟩
- **S**: estados (finito); **A**: ações (finito)
- **T**: S×A×S → [0,1], probabilidade de transição
- **R**: S×A → ℝ, função de reforço
- **γ ∈ [0,1)**: fator de desconto (equilibra recompensa imediata e futura)
- **Hipótese do retorno**: todo objetivo pode ser descrito como maximizar o reforço acumulado esperado.
- Solução: **política π: S → A** que maximiza Q(s,a) = E[Σₜ γᵗ rₜ]. Ótima: **π\*(s) = argmaxₐ Q\*(s,a)**.
- Em AR o agente **não conhece T nem R** no início.

### Q-learning
Estima Q\*(s,a) a partir de experiências:

**Q(sₜ,aₜ) ← (1 − α)·Q(sₜ,aₜ) + α·[rₜ + γ·maxₐ Q(sₜ₊₁, a)]**

- α ∈ [0,1] = taxa de aprendizado (combina o conhecimento antigo com o resultado da nova experiência).

Algoritmo:
1. Inicie Q(s,a) arbitrariamente (ex.: zeros).
2. Observe sₜ.
3. Repita: escolha e execute aₜ; receba rₜ; observe sₜ₊₁; atualize Q; sₜ ← sₜ₊₁.

**Exploração × explotação**: explorar = tentar ações novas; explotar = usar a melhor ação conhecida. **ε-greedy**: explora com probabilidade ε e explota com 1 − ε; ε **diminui** com o tempo.

Exemplo dos slides (salas 0–5, objetivo = 5, γ = 0,8, α = 1, recompensa 100 ao sair): com α = 1, Q(s,a) = r + 0,8·max Q(s',·). Ex.: Q(1,5) = 100; Q(3,1) = 0 + 0,8·100 = 80; Q(2,3) = 0,8·80 = 64. Política ótima: em cada estado, a ação de maior Q.

### Representações e extensões
- **Tabular (enumerada)** vs **fatorada** (atributos de estado).
- **DQN**: rede neural profunda aproxima Q(s,a); estados discretos ou contínuos, ações discretas. Perda: L = E[(r + γ maxₐ' Q(s',a') − Q(s,a))²]. Usa *experience replay* e *target network*.
- **Policy gradient**: otimiza diretamente uma política parametrizada por gradiente. Aprende **políticas estocásticas** (P(a|s)) e lida com **ações contínuas** (ex.: saída N(μ, σ²)).

Aplicações: jogos (Atari, Go, gamão, poker, Dota 2, StarCraft), **RLHF para LLMs**, robótica, finanças, dosagem médica, clusters, carros autônomos, recomendação, energia em casa inteligente, carga de veículos elétricos (multiagente), chatbots.
Tendências: agentes web/multimodais, RL para LLMs, sim-to-real, **Safe RL**.

---

## 7. Processamento de Linguagem Natural (aula 9)

Prof. Thiago Pardo.

- **PLN**: área que investiga métodos e sistemas de processamento computacional da linguagem humana. Tarefas de **interpretação** e **geração**. Subárea da IA, multidisciplinar (computação, linguística, estatística...).
- **Linguagem** = capacidade humana de comunicação (verbal ou não). **Língua** = código de uma comunidade (português, LIBRAS).
- Exemplos: tradução (Apertium, Google, DeepL), sumarização, chatbots (**ELIZA**, ChatGPT), perguntas e respostas (START 1993, **IBM Watson** no Jeopardy!), revisão gramatical (Grammarly), análise de sentimentos, assistentes virtuais, detecção de fake news.

### Trajetória
**Simbolismo → Estatística → Modelagem neural** (com hibridismo e ciclos).
- Simbolismo: regras (ex.: "artigo e substantivo do sujeito devem concordar").
- Estatística: probabilidades (ex.: tradução P(Hexe | witch)).
- Neural: **Transformers** (Vaswani et al., 2017).

Na linguística:
- **Empirismo** (1920–1960 e de 1990 até hoje): mente com mecanismos gerais de associação; foco em dados (**córpus**) e desempenho. **Zellig Harris** e a **hipótese distribucional** (1954).
- **Racionalismo / Gerativismo** (1960–1985): **Chomsky**, linguagem inata (pobreza do estímulo), regras e princípios. Competência × desempenho linguístico. Época dos sistemas baseados em conhecimento.

### Dados
- Do Brown corpus (1964, 1 milhão de palavras) a córpus de centenas de bilhões de tokens em português.
- **Córpus**: conjunto de dados linguísticos sistematizado, representativo e processável por computador.
- 7 questões de anotação (Hovy e Lavid): seleção do material, teoria, anotadores, procedimento, interface, avaliação, disponibilização.
- **Web como córpus**: exige detecção de língua, remoção de duplicados, detecção de toxicidade, origem.
- **Data augmentation**: sinônimos/inserções/deleções (EDA), back-translation, paráfrases.

### Produtos de PLN
- **Recursos**: córpus, léxicos, dicionários, gramáticas.
- **Ferramentas**: tokenizadores/segmentadores, **stemmer** ("corri", "correram" → "corr"), **lematizador** ("corri" → "correr"), **PoS tagger**, chunker/parser, analisadores semânticos, alinhadores, classificadores de polaridade.
- **Aplicações**: tradutores, revisores, sumarizadores, simplificadores, mineradores de opinião.
- Ex. revisor gramatical: tokenização → tagging/parsing (léxico, gramática) → regras de concordância → sugestões.

Português: 6ª língua mais falada; PROPOR (1993), STIL (2003), CEPLN na SBC (2007). Manning (2015): PLN é sobre os problemas da língua, não só sobre bater benchmark.

---

## 8. LLMs e Agentes Conversacionais (aula 10)

Prof. Ricardo Marcacini.

### Modelo de linguagem
Atribui probabilidade a sequências: **P(w₁,…,w_T) = Πₜ P(wₜ | w<ₜ)**.
Geração **autoregressiva**: prevê o próximo token, anexa ao contexto, recalcula a distribuição.

### n-gramas
- Aproxima o contexto pelos n−1 tokens anteriores. Bigrama: P(wₜ|wₜ₋₁) = **C(wₜ₋₁, wₜ) / C(wₜ₋₁)**. Ex.: P(com | artigo) = 2/4 = 0,5.
- Quanto maior n, mais contexto e **menos ocorrências** (esparsidade) → probabilidade zero para continuações nunca vistas.
- Correção: **smoothing** e **backoff** (Katz, Kneser-Ney).

### Representações neurais
- **Embeddings**: palavras viram vetores densos; palavras parecidas ficam próximas (cosseno). Analogia: rei − homem + mulher ≈ rainha. Permitem generalizar para combinações raras.
- Bengio (2003): LM neural. **Word2Vec** (skip-gram e CBOW).
- **RNN** (estado oculto sequencial), **LSTM** (memória e gates, reduz *vanishing gradient*), **GRU** (gates simplificados).
- Limites das recorrentes: sequenciais (difícil paralelizar), dependências longas, estado oculto carrega todo o contexto.

### Transformer
- **Self-attention**: cada token consulta diretamente os outros. **Attention(Q,K,V) = softmax(QKᵀ/√d_k)·V**.
- Bloco: embedding + posição → multi-head attention → residual + normalização → MLP.
- **BERT** (encoder): *masked language modeling*, usa contexto à esquerda e à direita. Bom para classificação, busca, embeddings.
- **GPT** (decoder com máscara causal): prevê o próximo token. Perda: L = −Σ log P(wₜ | w<ₜ). GPT (2018), GPT-2 (2019, zero-shot), GPT-3 (2020, 175B, in-context learning), ChatGPT (2022).
- O que escala: modelo, dados e compute (**leis de escala / compute-optimal**).

### Decodificação
- softmax com **temperatura**: P(wᵢ) = e^{zᵢ/T} / Σⱼ e^{zⱼ/T}. T baixa = mais concentrado/determinístico; T alta = mais diverso.
- **Top-k**: só os k tokens mais prováveis. **Top-p**: menor conjunto cuja probabilidade acumulada atinge p.

### Adaptação
- **In-context learning** (few-shot): exemplos no prompt mudam o comportamento **sem mudar pesos**.
- **Instruction tuning**: fine-tuning supervisionado com pares instrução → resposta.
- Modelo **base** (só continua texto) → **instruído** (segue tarefas) → **alinhado** (utilidade, segurança, estilo).
- **Chat template**: system/user/assistant serializados em uma sequência.
- **Chain-of-thought**: decompor o raciocínio (por prompt, por system prompt ou aprendido no pós-treino).

### RAG e ferramentas
- **RAG**: pergunta → retriever (BM25, embeddings, busca híbrida) → trechos relevantes → prompt enriquecido → LLM. Vantagens: informação atualizada, dados privados, rastreabilidade, especialização.
- **Tool calling**: aprendido no pós-treino; o modelo emite uma chamada estruturada (função + argumentos), recebe o resultado e continua.

### Agentes
- Agente = **sistema de controle**; o LLM é o componente de decisão num ciclo: objetivo → decisão → ação (tool/API) → observação → memória → próxima decisão.
- **Memória**: janela de contexto não é memória persistente. Tipos: **curto prazo** (interação atual), **episódica** (execuções anteriores), **semântica** (conhecimento persistente).
- **Planejamento**: decompor antes de agir evita decisões inconsistentes em tarefas longas.
- **Multiagentes** (coordenador, pesquisador, analista, redator, revisor): especialização e paralelismo, mas custo, coordenação e propagação de erros.
- Questões atuais: modelos fundacionais concentram dependências; **soberania digital** (onde ficam os dados, quem controla o modelo, jurisdição, portabilidade).
- Limitações: **alucinações**, confiabilidade em tarefas longas, avaliação de agentes, custo/energia, segurança.

---

## 9. Representação de Conhecimento (aula 11)

Profa. Renata Wassermann.

- Motivação: quebra-cabeça 8×8 tem ~1,27×10⁸⁹ combinações; humanos resolvem rápido porque **usam conhecimento do problema**. Em problemas de lógica, cada informação nova reduz as combinações (216 → 36 → 12 → 4 → 1).
- **IA simbólica**: identificar o conhecimento, representá-lo em **linguagem formal** e implementar um **mecanismo de inferência**. Permite explicar resultados (*explainable AI*).
- **IA "cognitiva"/conexionista**: muitos dados, redes de elementos simples, função entrada-saída.

### História
- **Dartmouth (1956)**: McCarthy cunha o termo "Inteligência Artificial".
- **McCarthy (1958)**, "Programs with Common Sense": primeiro artigo de IA lógica.
- **DENDRAL** (~1965): primeiro sistema especialista; estrutura de moléculas orgânicas.
- **MYCIN** (~1975): bactérias e antibióticos; ~600 regras e probabilidades; acertava 69%, melhor que especialistas, mas não foi usado por razões **éticas** e pelo **tempo** (≥ 30 min por sessão).
- **Senso comum**: difícil de formalizar (ex.: quebrar um ovo, 66 axiomas). Projeto **Cyc** (Lenat, 1984).

### Agentes baseados em conhecimento
- **Base de conhecimento (BC)**: conjunto de sentenças em linguagem formal.
- **TELL** adiciona sentenças; **ASK** consulta. A resposta deve seguir logicamente da BC (nada é inventado).
- **Mundo do Wumpus**: cheiro perto do Wumpus, brisa perto de poço, brilho no ouro. Desempenho: ouro +1000, morte −1000, passo −1, flecha −10.

### Lógica proposicional
- **Sintaxe** (quais fórmulas são válidas) × **semântica** (como são avaliadas). Ex.: "2+3" e "3+2" têm sintaxe diferente e mesma semântica.
- **Valoração** v: P → {T, F}.
- **Modelos** de α: M(α) = {v | v(α) = T}. Para a BC: M(KB) = ∩ M(α).
- **Consequência lógica**: KB ⊨ α se α é verdadeira em todos os modelos da KB.
- **Verificação de modelos**: enumerar todos os modelos e checar.
- Wumpus: B₁₁ ⇔ (P₁₂ ∨ P₂₁). Com brisa em [2,1]: KB ⊨ ¬P₁₂, mas KB ⊭ ¬P₂₂.
- Raciocínio lógico é correto se as premissas forem corretas.

### Lógica de primeira ordem
- Proposicional: só **fatos** (V/F). Primeira ordem: **objetos, propriedades, relações, funções**, com quantificadores.
- Ex.: ∀y (B(y) ⇔ ∃x (P(x) ∧ Adj(x,y))); ∀x (W(x) ⇒ ¬P(x)).
- Escolher predicados é uma **decisão ontológica** (ex.: W(x) ou LocalizadoEm(w,x)).

### Engenharia de conhecimento (7 passos, ex.: circuito somador)
1. Definir a tarefa · 2. Reunir conhecimento relevante · 3. Definir vocabulário (predicados, funções, constantes) · 4. Codificar conhecimento geral · 5. Codificar a instância específica · 6. Fazer consultas · 7. Depurar a BC (ex.: faltou dizer que 1 ≠ 0).

### Ontologias e Lógicas de Descrição
- Ontologia: **conceitos** (classes), **propriedades** (papéis) e **restrições**. A análise ontológica vem antes da representação.
- **Lógicas de Descrição**: fragmentos **decidíveis** da lógica de primeira ordem.
  - **TBox**: conhecimento terminológico. Ex.: Father ≡ Man ⊓ ∃hasChild.⊤; Mother ⊑ Woman.
  - **ABox**: fatos sobre indivíduos. Ex.: BlackCat(mimi), hasChild(mary, betty).
  - BC = TBox + ABox = ontologia + instâncias.
- **OBDA**: acesso a dados via ontologias (mapeamentos geram grafo de conhecimento).
- Debate: "dados são o que importa" (ML) × "conhecimento é o que importa" (simbólica); tendência de combinar os dois.

---

## 10. Ética, Avaliação de Impacto e Regulação (aula 12)

### Parte 1 — Ética e IA (Prof. João Cortese)
- Debate sobre controle e agência: Harari (IA como "invasão alienígena" que tomará o controle) × Suleyman (são ferramentas, não projetar agência nelas).
- Turing (1950), "Computing Machinery and Intelligence".
- Pontos-chave: entender a IA admitindo riscos **sem catastrofismo**; **algoritmos não são neutros**; explicitar os critérios usados.
- Temas (Coeckelbergh, 2020): ficção × questões reais, vieses, dados e privacidade, trabalho, meio ambiente, estatuto ético de robôs, o que é o humano, explicabilidade, produção científica, recomendações.
- **Viés pode surgir**: na seleção dos dados, nos próprios dados (sub-representação), no algoritmo, nos dados de uso, em correlações espúrias, na equipe que criou e na sociedade.
  - Ex.: Google rotulou pessoas negras como gorilas (2015); classificador de câncer de pele treinado com pele branca teve cerca de metade da acurácia para pacientes afro-americanos.
  - Pergunta filosófica: o algoritmo deve refletir os dados ou corrigir seus vieses?
- **Explicabilidade**: evitar caixas-pretas, permitir transparência e **responsabilização (accountability)**. "Direito à explicação" (ex.: crédito negado). Na saúde: falta de explicação reduz confiança e esconde vieses, mas há quem defenda que acurácia importa mais (London, 2019).
- **Trabalho**: perda de empregos e o sentido do trabalho (relações sociais, pertencimento, virtudes).
- **Ciência**: IA amplia o impacto individual, mas **contrai o alcance coletivo** (concentra-se em áreas ricas em dados); risco de **monoculturas de conhecimento**; "produzimos mais, entendemos menos".
- **Ossificação / "mais do mesmo"**: confiança excessiva nas recomendações (viés de automação); "papagaios estocásticos".
- Boas práticas em ensino/pesquisa: **ser transparente** quanto ao uso, **revisar criticamente**, **responsabilizar-se** pelo resultado.

### Parte 2 — Avaliação ética e regulação (Profa. Cristina Godoy)
- **Avaliação de Impacto Ético (EIA)**: metodologia para **identificar, avaliar e mitigar** riscos éticos de sistemas de IA, com base em IA confiável e direitos fundamentais. Riscos surgem das **interações** entre componentes e stakeholders.
  - Baseada em diretrizes internacionais (Recomendação da **UNESCO, 2021**).
  - Processo em 3 etapas: **diagrama do sistema** (componentes e stakeholders) → **extração dos requisitos éticos** (associar interações a características como justiça, explicabilidade, robustez, privacidade) → **identificação dos riscos** (onde os requisitos não são cumpridos).
- **PL 2338/2023** (marco da IA no Brasil): uso responsável; vale para desenvolvedores e aplicadores; governança proporcional ao **risco**.
  - Art. 4º, XVI: define avaliação de impacto algorítmico. Arts. 12–16: categorização de risco e alto risco. Arts. 18 e 23: obrigações de governança. Arts. 19 e 20: **rotulagem de conteúdo sintético**.
  - **Alto risco**: dano à saúde, segurança ou direitos fundamentais; impacto em grupos vulneráveis ou serviços essenciais; risco sistêmico.
  - Avaliação de impacto **obrigatória antes de colocar no mercado** sistemas de alto risco, com mitigação e atualização contínua.
  - Princípios: **proporcionalidade** (regulação proporcional ao risco) e **precaução** (atenção preventiva mesmo com risco incerto).
- **Transparência Brasil**: avaliações obrigatórias e públicas para IA no Estado; explicação e possibilidade de **contestação**. Riscos: restrição do espaço cívico, privacidade, discriminação algorítmica, prisões injustas. Princípios: explicabilidade, accountability, **devido processo** (revisão humana).
- **Segurança**: "toda IA ética é também segura". Medidas: controle de acesso, logs de auditoria, backups/versionamento, monitoramento, LGPD (arts. 46–49).
- **Prompt injection**: comandos maliciosos escondidos em entradas aparentemente inofensivas fazem o LLM ignorar restrições ou vazar dados. Mitigações: treinamento (ex.: SecAlign), **isolamento entre comandos e dados**, **validação de entradas**, auditoria; tratar toda saída de LLM como potencialmente maliciosa.

---

## 11. Visão Computacional (aula 13)

Prof. Roberto Cesar Jr.

- Humanos são "programados" (seleção natural) para achar faces; a visão natural também é baseada em dados.
- Áreas relacionadas: **processamento de imagens** (imagem → imagem), **computação gráfica** (símbolos → imagem), **visão computacional** (imagem → símbolos/descrição).
- **Imagem digital** = matriz de valores.
  - **Resolução espacial** (amostragem): 512×512 → 16×16 perde detalhes.
  - **Quantização** (bits por pixel): 8 bits = 256 níveis de cinza; 1 bit = preto e branco.
- **Pipeline**: imageamento → pré-processamento → segmentação → descrição → reconhecimento → interpretação.
- Tarefas: descrição de cenas, detecção de objetos, OCR, impressão digital, rastreamento, vigilância.
- **Atributos locais**: bordas, cantos, junções; texturas.
- **Filtragem convolucional**: desliza uma máscara g sobre a imagem f (f ∗ g). Filtro da **média 3×3** (todos os pesos 1/9): suaviza, reduz ruído e **borra bordas**. Também há filtros não lineares (ex.: mediana).
- **ML convencional × CNN**: no convencional, os filtros/atributos são projetados à mão; na **CNN, os núcleos (filtros) são aprendidos** no treinamento.
  - **LeNet-5**: average pooling, ativação sigmoide/tanh, camadas totalmente conectadas no final, treinada no MNIST (60 mil dígitos).
  - **YOLO** (2016): detecção e localização em uma passada; YOLOv5 até ~140 FPS.
  - **Aumentação de dados** e **conjuntos anotados** são essenciais.
- LLMs multimodais ainda erram tarefas simples (ex.: contar ovos numa imagem).
- Aplicações: **SideSeeing** (auditoria de calçadas com vídeo, GPS e sensores de smartphone + validação humana), IA para eventos climáticos extremos, IA para ensino de matemática.
- Desafios: qualidade e representatividade dos dados, erros e generalização, privacidade, vieses, validação humana.

---

## 12. Fórmulas para decorar

| Tema | Fórmula |
|---|---|
| A\* | f(s) = g(s) + h(s); h admissível ⇒ 0 ≤ h(s) ≤ custo real |
| Entropia | H(S) = −Σ pᵢ log₂ pᵢ |
| Ganho de informação | Ganho(A) = H(S) − Σₖ [(pₖ+nₖ)/(p+n)]·H(Eₖ) |
| Acurácia | (VP+VN)/total |
| Precisão | VP/(VP+FP) |
| Revocação/Sensibilidade | VP/(VP+FN) |
| Especificidade | VN/(VN+FP) |
| F1 | 2·P·R/(P+R) |
| TFP | 1 − especificidade |
| MSE / RMSE / MAE | (1/N)Σ(y−ŷ)² / √MSE / (1/N)Σ\|y−ŷ\| |
| R² | 1 − MSE/Var(y) |
| Bootstrap | ~63,2% dos exemplos na amostra de treino |
| k-means | J = Σ_c Σ_{x∈C_c} ‖x − x̄_c‖²; centro = média |
| Q-learning | Q(s,a) ← (1−α)Q(s,a) + α[r + γ maxₐ' Q(s',a')] |
| Política ótima | π\*(s) = argmaxₐ Q\*(s,a) |
| Modelo de linguagem | P(w₁..w_T) = Π P(wₜ \| w<ₜ) |
| Bigrama | P(wₜ\|wₜ₋₁) = C(wₜ₋₁,wₜ)/C(wₜ₋₁) |
| Atenção | softmax(QKᵀ/√d_k)·V |
| Temperatura | P(wᵢ) = e^{zᵢ/T} / Σ e^{zⱼ/T} |
| ResNet | saída = f(x) + x |
| Weight decay | L(W) + λ·Complexidade(W) |

---

## 13. Exercícios resolvidos

**1. Matriz de confusão do slide** (classe 1 = positiva): VP = 50, FN = 0, FP = 10, VN = 40.
- Acurácia = 90/100 = **0,90**
- Precisão = 50/60 ≈ **0,833**
- Revocação = 50/50 = **1,0**
- Especificidade = 40/50 = **0,80**
- F1 = 2·0,833·1/(1,833) ≈ **0,909**

**2. Entropia**: conjunto com 5 positivos e 5 negativos → H = −0,5·log₂0,5 − 0,5·log₂0,5 = **1**. Conjunto só com uma classe → H = **0**.

**3. Ganho**: com H(S) = 1 e ERestante(País) = 0,7244 → Ganho = **0,2756**, o maior entre os atributos, então "País de origem" vai para a raiz.

**4. ROC**: classificadores (TFP, TVP): C1 = (0,3; 0,4), C2 = (0,5; 0,7), C3 = (0,2; 0,6). O C3 tem menos falsos positivos e mais acertos que o C1, então domina o C1. O C2 acerta mais, porém com mais falsos positivos.

**5. Q-learning** (γ = 0,8, α = 1): se Q(1,5) = 100, então Q(3,1) = 0 + 0,8·100 = **80** e Q(2,3) = 0 + 0,8·80 = **64**.

**6. Imagem (exercício da aula 13)**: imagem 640×480, 8 bits por pixel.
- Pixels = 640·480 = **307.200**; níveis de cinza = 2⁸ = **256**.
- Tamanho sem compressão = 307.200 bytes ≈ **300 KB**.
- Filtro da média 3×3: **reduz ruído**, mas **borra as bordas**.

**7. k-means**: por que não escolher k minimizando J? Porque J sempre cai quando k aumenta (com k = N, J = 0). Usa-se o cotovelo ou índices de validade.

---

## 14. Perguntas de revisão

1. Qual estrutura de dados define DFS, BFS, busca de menor custo e A\*?
2. O que é uma heurística admissível e por que ela garante a otimalidade do A\*?
3. Diferencie hill climbing, simulated annealing e algoritmos genéticos.
4. Defina aprendizado segundo Mitchell (T, P, E) e dê um exemplo.
5. Diferencie tarefas preditivas, descritivas, generativas e prescritivas.
6. Qual a diferença entre parâmetro e hiperparâmetro?
7. Explique viés indutivo de preferência e de representação.
8. Relacione viés/variância com underfitting/overfitting.
9. Compare hold-out, k-fold, leave-one-out e bootstrap.
10. Quando precisão importa mais que revocação, e vice-versa? (Ex.: spam × diagnóstico de câncer.)
11. O que o eixo x e o eixo y de uma curva ROC representam? O que significa AUC = 0,5?
12. Como o ID3 escolhe o atributo? Quais limitações ele tem?
13. Como a Random Forest reduz a correlação entre árvores? O que é a amostra OOB?
14. O que é o problema da degradação e como as ResNets resolvem?
15. O que são ataques adversariais e shortcut learning?
16. Descreva o k-means e suas desvantagens.
17. Quais os componentes de um MDP? Qual o papel de γ e de α?
18. O que é o dilema exploração × explotação e como o ε-greedy o trata?
19. Diferencie DQN e policy gradient.
20. Compare racionalismo (Chomsky) e empirismo (Harris) em PLN.
21. Diferencie stemmer e lematizador.
22. Por que n-gramas sofrem com esparsidade? Como embeddings ajudam?
23. Diferencie BERT e GPT.
24. O que é RAG e por que usá-lo?
25. Diferencie in-context learning e instruction tuning.
26. Quais os tipos de memória de um agente?
27. Diferencie lógica proposicional e de primeira ordem.
28. O que significa KB ⊨ α? Como funciona a verificação de modelos?
29. Diferencie TBox e ABox.
30. Onde podem surgir vieses em um sistema de IA?
31. Por que explicabilidade importa? Dê um argumento contra priorizá-la.
32. O que é a EIA e quais são as suas 3 etapas?
33. O que o PL 2338/2023 exige para sistemas de alto risco?
34. O que é prompt injection e como mitigá-lo?
35. Qual o pipeline clássico de visão computacional?
36. Qual a diferença entre ML convencional e CNN em visão?
