# Resumo e resolução dos exercícios — pasta BRUNO

Material de Circuitos Integrados / VLSI (Weste e Sedra). Para cada PDF há um **resumo da teoria cobrada pelos exercícios** e a **resolução passo a passo**. Os PDFs já trazem algumas respostas do livro ou do professor; aqui elas foram refeitas e conferidas. Quando a conta não bate com a resposta impressa, isso está marcado com **⚠️**.

Alguns exercícios dependem de tabelas e exemplos dos livros que não estão nos PDFs (Tabela 3.2 e Tabelas 4.6–4.9 do Weste, Exemplos 10.1 e 10.4 e Figura 10.14 do Sedra, arquivo SPICE `tsmc035`). Os dados usados para essas fontes estão indicados em cada questão.

## Índice

1. [Regras de projeto (Weste)](#1-regras-de-projeto-weste)
2. [Interconexão (Weste)](#2-interconexão-weste)
3. [Transistores MOS (Sedra, cap. 4)](#3-transistores-mos-sedra-cap-4)
4. [Exercício de capacitância do transistor (aula)](#4-exercício-de-capacitância-do-transistor-aula)
5. [Modelo de capacitâncias do transistor](#5-modelo-de-capacitâncias-do-transistor)
6. [Projeto de inversores (Sedra)](#6-projeto-de-inversores-sedra)
7. [Portas complexas (Sedra + slides)](#7-portas-complexas-sedra--slides)
8. [Lógica dinâmica (Sedra)](#8-lógica-dinâmica-sedra)
9. [Fórmulas para decorar](#9-fórmulas-para-decorar)

---

## 1. Regras de projeto (Weste)

Arquivo: `1. Weste_regras_de_projeto_21.pdf` — exercícios 3.6, 3.7 e uma questão avulsa.

### Resumo

- **Regras λ (lambda)**: todas as dimensões do leiaute são múltiplos de λ = metade do comprimento mínimo de canal. Processo de 200 nm → λ = 100 nm; 180 nm → λ = 90 nm; 0,8 µm → λ = 0,4 µm.
- **Conjuntos MOSIS**: SCMOS (processos mais antigos), SUBM (submicrométrico) e DEEP (submicrométrico profundo). As regras crescem de SCMOS para DEEP em alguns itens, porque λ fica menor e certas dimensões físicas não escalam.
- Regras de **contato** mais cobradas (MOSIS, em λ; [fonte](https://www.eecis.udel.edu/~vsaxena/Cadence/SCMOS_rules/scmos-contact.html)):

| Regra | Descrição | SCMOS | SUBM | DEEP |
|---|---|---|---|---|
| 5.1 / 6.1 | Tamanho exato do contato | 2×2 | 2×2 | 2×2 |
| 5.2 / 6.2 | Sobreposição por poly/ativo | 1,5 | 1,5 | 1,5 |
| 5.3 / 6.3 | Espaçamento contato–contato | 2 | 3 | 4 |
| 5.4 / 6.4 | Espaçamento até a porta | 2 | 2 | 2 |
| 5.2.b | Sobreposição por poly (regra alternativa) | 1 | 1 | 1 |
| 5.5.b | Contato de poly até outro poly | 4 | 5 | 5 |
| 5.7.b | Até ativo/poly (vários contatos) | 3 | 3 | 3 |

- **Pitch** = distância centro a centro entre duas linhas (ou dois transistores) vizinhos. **Pitch com contato** é maior que o pitch sem contato, porque o contato exige uma "almofada" de metal mais larga que a linha.
- **Mapeamento entre regras**: para levar um projeto em λ para uma foundry, cada regra λ × (valor de λ) deve ser **≥** a regra da foundry. Se for muito maior, o projeto funciona mas desperdiça área.

### Exercício 3.6 — pitch de metal com contato (SUBM, 6 camadas)

Duas linhas paralelas, cada uma com um contato/via. A almofada de metal em volta do contato tem largura = contato + 2 × sobreposição. O pitch é meia almofada + espaçamento + meia almofada:

```
pitch = largura_contato + 2 × sobreposição + espaçamento_metal
```

Com os valores da Tabela 3.2 usados na resposta do professor:

| Camada | Contato/via | Sobreposição (×2) | Espaçamento | Pitch |
|---|---|---|---|---|
| M1 | 2 | 1 → 2 | 3 | **7λ** |
| M3 | 2 | 2 → 4 | 3 | **9λ** |
| M6 | 2 | 1 → 2 | 5 | **9λ** |

**Como reduzir o pitch:** deslocar os contatos (escalonar) ao longo das linhas, para que duas almofadas nunca fiquem lado a lado. Assim cada almofada fica ao lado de uma linha comum, mais estreita:

```
pitch_escalonado = ½ almofada + espaçamento + ½ largura_da_linha
M1: 2 + 3 + 1,5 = 6,5λ   (contra 7λ com contatos alinhados; o mínimo sem contato é 3 + 3 = 6λ)
```

```
────[■]──────────────
──────────[■]────────
────────────────[■]──
```

⚠️ **Conferência do M6 e do M3.** A largura mínima do M6 no MOSIS é 5λ e o espaçamento 5λ, então duas linhas de M6 sem contato já ocupam 5 + 5 = 10λ. Um pitch com contato de 9λ é impossível. Nas regras MOSIS revisão 8 para processos de 6 metais, a via5 é 3×3 no SUBM e a sobreposição é 1, o que dá 3 + 2 + 5 = **10λ** ([fonte](https://www.eecis.udel.edu/~vsaxena/Cadence/SCMOS_rules/scmos-metal6.html)). Para o M3, a mesma revisão usa sobreposição 1 em processos de 4+ metais (2 só em processos de 3 metais), o que daria 2 + 2 + 3 = 7λ ([fonte](https://www.eecis.udel.edu/~vsaxena/Cadence/SCMOS_rules/scmos-metal3.html)). Confira na Tabela 3.2 da sua edição qual coluna o professor espera.

### Exercício 3.7 — pitch de transistor sem e com contato (SUBM)

Os dois desenhos da Fig. 3.29 são o mesmo circuito: a difusão entre duas portas é o dreno de um transistor e a fonte do outro. O que muda é se há contato nessa difusão.

- **Sem contato**: pitch = largura do poly + espaçamento poly–poly sobre ativo (regra 3.2a = 3λ no SUBM)
  `pitch = 2 × (½ · 2) + 3 = 5λ`
- **Com contato**: pitch = largura do poly + 2 × espaçamento contato–porta (regra 5.4/6.4 = 2λ) + contato (2λ)
  `pitch = 2 × (½ · 2) + 2 × 2 + 2 = 8λ`

### Questão avulsa — qual regra MOSIS mapeia melhor para a foundry de 200 nm?

Passo 1: λ = 200 nm / 2 = 100 nm = 0,1 µm. Passo 2: converter a tabela da foundry para λ. Passo 3: comparar com cada coluna MOSIS (a regra MOSIS precisa ser ≥ a da foundry).

| Regra da foundry | nm | em λ | SCMOS | SUBM | DEEP |
|---|---|---|---|---|---|
| Largura do contato | 200×200 | 2×2 | 2×2 ✓ | 2×2 ✓ | 2×2 ✓ |
| Sobreposição por poly/ativo | 100 | 1 | 1,5 ✓ | 1,5 ✓ | 1,5 ✓ |
| Espaçamento contato–contato (5.3/6.3) | 300 | 3 | **2 ✗** | 3 ✓ | 4 (sobra) |
| Espaçamento até a porta (5.4/6.4) | 200 | 2 | 2 ✓ | 2 ✓ | 2 ✓ |
| Contato de poly até outro poly (5.5.b) | 500 | 5 | **4 ✗** | 5 ✓ | 5 ✓ |
| Vários contatos até ativo/poly (5.7.b) | 300 | 3 | 3 ✓ | 3 ✓ | 3 ✓ |
| Contato de ativo até contato de poly (6.8.b) | 400 | 4 | 4 ✓ | 4 ✓ | 4 ✓ |

**Resposta: SUBM.** Ela atende todas as regras de contato com o valor exato.
- SCMOS viola 5.3/6.3 (2λ = 200 nm < 300 nm) e 5.5.b (4λ = 400 nm < 500 nm).
- DEEP atende tudo, mas em 5.3/6.3 pede 400 nm quando a foundry aceita 300 nm, desperdiçando área.

---

## 2. Interconexão (Weste)

Arquivo: `2. Weste_interconexao_21.pdf` — questões 1 e 2.

### Resumo

- **Resistência de um fio**: `R = ρ·l / (t·w)`, onde t é a espessura e w a largura. Também se escreve `R = R□ · l/w`, com `R□ = ρ/t` (resistência de folha, Ω/□).
- **Resistividades (Tabela 4.6 do Weste, µΩ·cm)**: prata 1,6; cobre 1,7; ouro 2,2; alumínio 2,8; tungstênio 5,3.
- **Capacitância de um fio** tem duas partes:
  - **Cgnd**: para as camadas de cima e de baixo (ou substrato). Parte de placa paralela ≈ ε·w/h, mais as franjas. Diminui quando h (distância vertical) aumenta.
  - **Cadj**: para os fios vizinhos na mesma camada. Cresce com a espessura t e diminui com o espaçamento s.
- As tabelas 4.8/4.9 do Weste dão a capacitância por comprimento (aF/µm) em função de w e s (em múltiplos do mínimo), em dois cenários extremos (limites superior e inferior). Valores intermediários saem por **interpolação**.
- Ordem de grandeza útil: um fio mínimo tem cerca de **0,2 fF/µm** (200 aF/µm).

### Questão 1

Dados: linha de cobre em M3, processo de 180 nm (λ = 90 nm), l = 10 µm, w_Cu = 780 nm, regras SUBM.

**a) Largura mínima de uma linha de alumínio com R ≤ R_Cu**

Com o mesmo comprimento e a mesma espessura t:

```
ρ_Al·l/(t·w_Al) ≤ ρ_Cu·l/(t·w_Cu)  →  w_Al ≥ (ρ_Al/ρ_Cu)·w_Cu = (2,8/1,7)·780 nm
w_Al ≥ 1285 nm ≈ 1,28 µm  (≈ 14,3λ)
```

**b) Capacitância nos limites superior e inferior**

1. Na regra SUBM usada pelo professor, M3 tem largura mínima 5λ e espaçamento mínimo 3λ. Nas tabelas, "w" = 5λ = 450 nm e "s" = 3λ.
2. Cobre: w_Cu = 780/90 = 8,67λ = 1,73 w. Alumínio: w_Al = 1285/90 = 14,3λ = 2,85 w. Espaçamento: o mínimo, 1 s.
3. Interpolar nas tabelas na linha de 1 s, entre as colunas de w vizinhas, e multiplicar por l = 10 µm.

Valores interpolados pelo professor:

| Linha | w (múltiplos) | Upper-bound | Lower-bound |
|---|---|---|---|
| Cobre | 1,73 w | 255 aF/µm → **2,55 fF** | 221 aF/µm → **2,21 fF** |
| Alumínio | 2,85 w | 290 aF/µm → **2,90 fF** | 234 aF/µm → **2,34 fF** |

⚠️ **Unidades na resposta do professor.** O PDF escreve "aF/nm" e "pF". O correto é **aF/µm** e **fF**: 255 aF/µm × 10 µm = 2550 aF = 2,55 fF. O valor 255 aF/nm daria 255 fF/µm, mil vezes acima do típico (~0,2 fF/µm).

Conclusão: o alumínio precisa ser cerca de 65% mais largo para ter a mesma resistência, e com isso ganha mais capacitância (≈ 2,9 fF contra 2,55 fF no limite superior). O produto RC fica pior. Essa é uma das razões da troca do Al pelo Cu.

### Questão 2 — efeito de aumentar h

- **Cgnd diminui.** A parte de placa paralela cai com 1/h, e as franjas também diminuem.
- **Cadj praticamente não muda**, porque depende de t e s, que não mudaram. Na prática sobe um pouco: as linhas de campo que antes terminavam nos planos de cima e de baixo passam a terminar nos vizinhos. Com isso, Cadj ganha peso na capacitância total, e o acoplamento (crosstalk) entre vizinhos fica relativamente mais importante.

---

## 3. Transistores MOS (Sedra, cap. 4)

Arquivo: `3. Sedra_transistores.pdf` — exercícios 4.4 a 4.9.

### Resumo

- **Sobretensão**: `V_OV = v_GS − V_t`. O transistor conduz se V_OV > 0.
- **Triodo** (`v_DS < V_OV`): `i_D = k'_n (W/L) [V_OV·v_DS − v_DS²/2]`.
  Para v_DS pequeno, é um resistor: `r_DS = 1 / [k'_n (W/L) V_OV]`.
- **Saturação** (`v_DS ≥ V_OV`): `i_D = ½ k'_n (W/L) V_OV² (1 + λ v_DS)`.
- **Modulação de canal**: `V_A = V'_A · L`, `λ = 1/V_A`, `r_o = V_A / I_D = 1/(λ I_D)`.
- **PMOS**: as mesmas equações usando v_SG, v_SD e |V_t|. Conduz se `v_SG ≥ |V_t|`. Fica em triodo se `v_SD < v_SG − |V_t|`, ou seja, `v_D > v_G + |V_t|`.
- **Efeito de corpo**: `V_t = V_t0 + γ [√(2φ_f + V_SB) − √(2φ_f)]`.

### 4.4 — região de operação (V_t = 0,7 V, V_GS = 1,5 V → V_OV = 0,8 V)

| V_D = V_DS | Comparação | Região |
|---|---|---|
| 0,5 V | 0,5 < 0,8 | **Triodo** |
| 0,9 V | 0,9 ≥ 0,8 | **Saturação** |
| 3 V | 3 ≥ 0,8 | **Saturação** |

### 4.5 — correntes (k'_n = 100 µA/V², W/L = 10 → k_n = 1 mA/V²)

- (a) Triodo: `i_D = 1 mA/V² × [0,8·0,5 − 0,5²/2] = 1m × 0,275 =` **275 µA**
- (b) e (c) Saturação: `i_D = ½ × 1m × 0,8² =` **320 µA** (sem λ, a corrente não depende de v_DS)

### 4.6 — achar k_n e r_DS

1. Com v_GS = v_DS = 1,2 V: V_OV = 0,5 V e v_DS > V_OV, logo está em saturação. `100 µA = ½ k_n (0,5)²` → **k_n = 800 µA/V²**.
2. Com v_GS = 1,5 V e v_DS = 3 V (saturação): `i_D = ½ × 800µ × 0,8² =` **256 µA**.
3. Com v_GS = 3,2 V e v_DS pequeno: `r_DS = 1/(800µ × 2,5) =` **500 Ω**.

### 4.7 — V_A, λ, I_D, r_o (L = 0,8 µm, W = 16 µm, µ_nC_ox = 200 µA/V², V'_A = 50 V/µm)

- `V_A = 50 × 0,8 =` **40 V**; `λ = 1/40 =` **0,025 V⁻¹**
- `I_D = ½ × 200µ × (16/0,8) × 0,5² × (1 + 0,025 × 1) = 0,5 mA × 1,025 ≈` **0,51 mA**
- `r_o = V_A / I_D = 40 / 0,5 mA =` **80 kΩ** (usando I_D sem o termo λ)
- `ΔI_D = ΔV_DS / r_o = 2 / 80k =` **0,025 mA**

### 4.8 — PMOS com fonte em +5 V (V_t = −1 V, k'_p = 60 µA/V², W/L = 10 → k_p = 600 µA/V²)

- (a) Conduz se `v_SG ≥ 1` → `5 − V_G ≥ 1` → **V_G ≤ 4 V**
- (b) Triodo se `v_SD < v_SG − 1` → `5 − V_D < 5 − V_G − 1` → **V_D ≥ V_G + 1**
- (c) Saturação: **V_D ≤ V_G + 1**
- (d) `75 µA = ½ × 600µ × V_OV²` → **|V_OV| = 0,5 V** → v_SG = 1,5 V → **V_G = 3,5 V**; saturação para **V_D ≤ 4,5 V**
- (e) `r_o = 1/(|λ| I_D) = 1/(0,02 × 75µ) =` **0,67 MΩ**
- (f) `I_D = 75µ (1 + 0,02 v_SD)`:
  - V_D = 3 V → v_SD = 2 V → **78 µA**
  - V_D = 0 V → v_SD = 5 V → **82,5 µA**
  - Resistência aparente: `ΔV/ΔI = 3 V / 4,5 µA =` **0,67 MΩ**, igual ao item (e).

### 4.9 — efeito de corpo

```
V_t = 0,8 + 0,4 × (√(0,7 + 3) − √0,7) = 0,8 + 0,4 × (1,924 − 0,837) = 0,8 + 0,435 ≈ 1,23 V
```

---

## 4. Exercício de capacitância do transistor (aula)

Arquivo: `4. exercicio_cap_transistor_da sala.pdf` — leiaute da Fig. 3.10 do Weste (NMOS com contato de substrato encostado na fonte), com L_d = 0,05 µm.

### Resumo

Capacitâncias de um MOSFET:

| Capacitância | Fórmula | Parâmetro SPICE |
|---|---|---|
| Porta (óxido) | `C_g = C_ox·W·L`, `C_ox = ε_ox/t_ox` | TOX |
| Sobreposição (por lado) | `C_ov = CGSO·W` (ou `C_ox·W·L_d`) | CGSO, CGDO |
| Junção de fundo (área) | `CJ·A / (1 + V_R/PB)^MJ` | CJ, PB, MJ |
| Junção lateral (perímetro) | `CJSW·P / (1 + V_R/PBSW)^MJSW` | CJSW, PBSW, MJSW |

- A **difusão lateral** L_d faz a fonte e o dreno avançarem por baixo da porta. O canal efetivo fica `L_eff = L − 2L_d`, e a faixa L_d de cada lado vira capacitância de sobreposição.
- A capacitância de junção **diminui** com a tensão reversa V_R.

### Leitura do leiaute (Corte 1 = eixo fonte–canal–dreno)

- Altura do ativo n+ = **W = 1,5 µm**; largura do poly = **L = 0,5 µm**.
- Fonte (esquerda): 0,9 µm (região com metal e contato) + 0,2 µm (até a porta) = **1,1 µm**. O p+ à esquerda é o contato de substrato encostado na fonte.
- Dreno (direita): 0,2 µm + 1,0 µm = **1,2 µm**.

> Hipótese: a "solução dada em sala" não está no PDF. As cotas acima foram lidas da figura. Se o professor usou outra convenção, refaça as contas com os mesmos passos.

**Parâmetros do `tsmc035` (NMOS)**, do arquivo usado em cursos no Brasil ([tsmc035.mod](https://github.com/FGMoraes/FGMoraes.github.io/blob/master/microel/lab1/tsmc035.mod)). Os valores mudam um pouco de uma rodada de fabricação para outra, então confira com o arquivo da disciplina:

```
TOX  = 7,7 nm  → C_ox = 3,9·8,854e-12 / 7,7e-9 = 4,48 fF/µm²
CGDO = CGSO = 3,5e-10 F/m = 0,35 fF/µm
CJ   = 8,83e-4 F/m² = 0,883 fF/µm²   PB   = 0,795 V   MJ   = 0,354
CJSW = 2,99e-10 F/m = 0,299 fF/µm    PBSW = 0,989 V   MJSW = 0,187
```

### Respostas

**1) Área da porta:** `A_g = W·L = 1,5 × 0,5 =` **0,75 µm²**

**2) Capacitância de porta:** `C_g = C_ox·W·L = 4,48 fF/µm² × 1,5 µm × 0,5 µm ≈` **3,36 fF**

**3) Dimensões de dreno e fonte:**

| Região | W | L_s | Área | Perímetro 2(W+L_s) | Perímetro sem o lado da porta (2L_s + W) |
|---|---|---|---|---|---|
| Fonte | 1,5 µm | 1,1 µm | 1,65 µm² | 5,2 µm | 3,7 µm |
| Dreno | 1,5 µm | 1,2 µm | 1,80 µm² | 5,4 µm | 3,9 µm |

**4) Capacitâncias do dreno:**

```
C_DB(V) = CJ·AD/(1 + V/PB)^MJ + CJSW·PD/(1 + V/PBSW)^MJSW
        = 0,883·1,8/(1 + V/0,795)^0,354 + 0,299·5,4/(1 + V/0,989)^0,187   [fF]
```

| V_DB | Fundo | Lateral | Total |
|---|---|---|---|
| 0 V | 1,59 fF | 1,62 fF | **3,21 fF** |
| 1,65 V | 1,07 fF | 1,35 fF | **2,41 fF** |
| 3,3 V | 0,89 fF | 1,23 fF | **2,12 fF** |

A fonte está ligada ao substrato pelo contato encostado, então V_SB = 0 e vale o valor de polarização zero: `C_SB = 0,883·1,65 + 0,299·5,2 ≈` **3,01 fF**.

**5) Sobreposição:**

```
C_GSov = C_GDov = CGSO·W = 0,35 fF/µm × 1,5 µm = 0,525 fF   (total 1,05 fF)
```

Calculando pela difusão lateral: `C_ox·L_d·W = 4,48 × 0,05 × 1,5 = 0,34 fF` por lado. O valor do SPICE é maior porque CGSO também inclui as franjas.

**6) Porta com difusão lateral:**

```
L_eff = L − 2L_d = 0,5 − 0,1 = 0,4 µm
C_canal = C_ox·W·L_eff = 4,48 × 1,5 × 0,4 ≈ 2,69 fF
C_porta,total = C_canal + 2·CGSO·W = 2,69 + 1,05 ≈ 3,74 fF
```

Se a sobreposição for calculada como `C_ox·W·L_d`, o total volta a `C_ox·W·L` = 3,36 fF. A difusão lateral só move parte da capacitância do canal para a sobreposição.

---

## 5. Modelo de capacitâncias do transistor

Arquivo: `5. Modelo_capacitancia_transistor.pdf` — questão 1 (capacitâncias dos nós IN e OUT) e questão 2 (dimensões a partir do leiaute em λ).

### Resumo

- **Nó de porta (entrada)**: `C_in = C_ox·W·L + 2·C_ov·W`. Modelo mais fino: em corte, toda a C_ox·W·L vai para o corpo. Em triodo, metade para S e metade para D. Em saturação, 2/3 para S e nada para D.
- **Efeito Miller**: a capacitância porta–dreno liga dois nós que variam em sentidos opostos (entrada sobe, saída desce). Vista da saída, ela vale **2·C_gd**.
- **Nó de saída**: `C_out = 2·C_gd(M1) + C_db(M1) + C_g(próxima porta) (+ fio)`.
- **Difusão** (dreno de comprimento L_s e largura W): `C_db = C_j·L_s·W·K_j + C_jsw·(2L_s + W)·K_jsw`, com `K = 1/(1 + V_R/φ)^m`. O lado do dreno que encosta no canal não conta no perímetro.
- **Carga**: `Q = C·ΔV`.

### Questão 1

**Hipóteses.** Os parâmetros dados (C_ox = 6 fF/µm², C_ov = 0,31 fF/µm, C_j = 2 fF/µm²…) são os do processo genérico de 0,25 µm (Rabaey). O enunciado não informa L, então foi usado **L = 0,25 µm**. Dreno e fonte: W = 1 µm, L_s = 0,5 µm. Substrato em 0 V, então a tensão reversa do dreno de M1 é V_out.

**Componentes:**

```
C_ox·W·L   = 6 × 1 × 0,25 = 1,50 fF
C_ov·W     = 0,31 × 1     = 0,31 fF   (por lado)
Área de dreno  AD = 1 × 0,5 = 0,5 µm²  → C_j0·AD   = 1,00 fF
Perímetro  PD = 2·0,5 + 1 = 2 µm       → C_jsw0·PD = 0,56 fF
```

**Nó IN** (só a porta de M1, como pede a dica):

```
C_IN = 1,50 + 2 × 0,31 = 2,12 fF   (mesmo valor para Vin = 2,5 V e 0 V)
```

Refinamento opcional: com Vin = 2,5 V e Vout = 2,5 V, M1 está saturado e C_IN ≈ (2/3)·1,5 + 0,62 = 1,62 fF.

**Nó OUT** = 2·C_ov (Miller, M1) + C_db(M1) + C_g(M2), com C_g(M2) = 2,12 fF:

| V_out | K_j = (1+V/0,9)^−0,5 | K_jsw = (1+V/0,9)^−0,44 | C_db | **C_OUT** |
|---|---|---|---|---|
| 2,5 V | 0,514 → 0,51 fF | 0,557 → 0,31 fF | 0,83 fF | 0,62 + 0,83 + 2,12 = **3,57 fF** |
| 0,5 V | 0,802 → 0,80 fF | 0,823 → 0,46 fF | 1,26 fF | 0,62 + 1,26 + 2,12 = **4,00 fF** |
| 0 V | 1 → 1,00 fF | 1 → 0,56 fF | 1,56 fF | 0,62 + 1,56 + 2,12 = **4,30 fF** |

Nesse modelo simplificado, os mesmos valores servem para Vin = 2,5 V e Vin = 0 V. O que muda C_OUT é a polarização reversa da junção (V_out), não a entrada.

**b) Carga no nó IN:**

```
Vin = 2,5 V:  Q = C_IN·Vin = 2,12 fF × 2,5 V = 5,3 fC   (≈ 4,05 fC com o refinamento de saturação)
Vin = 0 V:    Q = 0
```

### Questão 2 — dimensões do leiaute (λ = 0,8/2 = 0,4 µm, λ² = 0,16 µm²)

Na figura, o poly da porta tem 2λ. A célula de contato (contato + sobreposições) tem 4λ × 4λ. Entre a célula e a porta há 1λ de n+ (faixa verde), tanto no dreno quanto na fonte.

**a) Canal**

| | Em λ | Em µm |
|---|---|---|
| L | largura do poly = 2λ | **0,8 µm** |
| W | largura do ativo = 4λ | **1,6 µm** |

**b) Dreno** (região de cima: célula de contato 4λ + faixa de 1λ = 5λ de comprimento, 4λ de largura)

| | Em λ | Em µm |
|---|---|---|
| Área | 5λ × 4λ = 20λ² | **3,2 µm²** |
| Perímetro | 5 + 4 + 5 + 4 = 18λ | **7,2 µm** |

**c) Fonte** (região em L embaixo: coluna de 5λ × 4λ sob a porta + célula de 4λ × 4λ à direita)

| | Em λ | Em µm |
|---|---|---|
| Área | 5λ×4λ + 4λ×4λ = 36λ² | **5,76 µm²** |
| Perímetro | 5 + 8 + 4 + 4 + 1 + 4 = 26λ | **10,4 µm** |

⚠️ A resposta do professor dá AS = 5λ×4λ + 5λ×4λ = 40λ² (6,4 µm²). O perímetro que ele mesmo calcula (26λ, com o lado direito de 4λ e o degrau de 1λ) só fecha se a célula da direita tiver 4λ de altura, e a figura mostra isso. Por isso aqui a área usada é **36λ² = 5,76 µm²**.

Os perímetros acima incluem o lado que encosta na porta (4λ). Para o modelo do Rabaey/Sedra, que exclui esse lado, subtraia W = 4λ: PD = 14λ = 5,6 µm e PS = 22λ = 8,8 µm.

---

## 6. Projeto de inversores (Sedra)

Arquivo: `6. Projeto_inversores.pdf` — exercícios 4.41, 4.42, 10.1, 10.2 e 10.3 (os três últimos usam o Exemplo 10.1).

### Resumo

- **Inversor casado** (`k_n = k_p`, `V_tn = |V_tp| = V_t`):
  `V_IL = (3V_DD + 2V_t)/8`, `V_IH = (5V_DD − 2V_t)/8`, `V_M = V_DD/2`
  `NM_L = V_IL`, `NM_H = V_DD − V_IH`, e as duas são iguais.
- **Atraso de propagação**:
  `t_PHL = α_n·C / (k'_n (W/L)_n V_DD)`, `t_PLH = α_p·C / (k'_p (W/L)_p V_DD)`, `t_P = (t_PHL + t_PLH)/2`
  `α = 2 / [7/4 − 3V_t/V_DD + (V_t/V_DD)²]`
- **Capacitância de carga**: `C = 2C_gd1 + 2C_gd2 + C_db1 + C_db2 + C_g3 + C_g4 + C_w` (Miller nas C_gd do inversor que chaveia, mais as portas do inversor seguinte e o fio).
- **Potência dinâmica**: `P = f·C·V_DD²`.
- O atraso é proporcional a C. A área é proporcional a W_n + W_p (com L fixo).

**Dados do Exemplo 10.1 (Sedra, 5ª ed.)**, conferidos pelas respostas de 10.2 (C = 4,225 fF) e 10.3 (19,5 µW): processo de 0,25 µm, C_ox = 6 fF/µm², µ_nC_ox = 115 µA/V², µ_pC_ox = 30 µA/V², V_tn = −V_tp = 0,4 V, V_DD = 2,5 V, (W/L)_n = 0,375/0,25 = 1,5, (W/L)_p = 1,125/0,25 = 4,5, sobreposição 0,3 fF/µm, C_dbn = C_dbp = 1 fF, C_w = 0,2 fF.

```
C_gd1 = 0,3 × 0,375 = 0,1125 fF          C_gd2 = 0,3 × 1,125 = 0,3375 fF
C_g3  = 6·0,375·0,25 + 2·0,3·0,375 = 0,7875 fF
C_g4  = 6·1,125·0,25 + 2·0,3·1,125 = 2,3625 fF
C = 0,225 + 0,675 + 1 + 1 + 0,7875 + 2,3625 + 0,2 = 6,25 fF
α = 2 / (1,75 − 0,48 + 0,0256) = 1,544
t_PHL = 1,544 × 6,25f / (115µ × 1,5 × 2,5) = 22,4 ps
t_PLH = 1,544 × 6,25f / (30µ × 4,5 × 2,5)  = 28,6 ps
t_P   = 25,5 ps
```

### 4.41 — V_IL, V_IH e margens (V_t = 1 V, V_DD = 5 V)

```
V_IL = (15 + 2)/8 = 2,125 ≈ 2,1 V
V_IH = (25 − 2)/8 = 2,875 ≈ 2,9 V
NM_L = NM_H = 2,125 ≈ 2,1 V
```

### 4.42 — corrente máxima com v_O ≤ 0,5 V

Com v_I = V_DD = 10 V, o PMOS está cortado e o NMOS em triodo, com V_OV = 10 − 2 = 8 V e k_n = 20 µA/V² × 20 = 400 µA/V²:

```
i_D = 400µ × [8 × 0,5 − 0,5²/2] = 400µ × 3,875 = 1,55 mA
```

### 10.1 — carga extra de 0,1 pF

```
C = 6,25 + 100 = 106,25 fF  (17 vezes o original)
t_PHL = 22,37 × 17 ≈ 380 ps,  t_PLH = 28,59 × 17 ≈ 486 ps
t_P ≈ 433 ps
```

O livro dá 437 ps. A diferença é de cerca de 1% e deve vir de arredondamento nos valores intermediários.

### 10.2 — (W/L)_p = (W/L)_n = 1,5

- **Área**: `(0,375 + 0,375)/(0,375 + 1,125) = 0,75/1,5 = 0,5` → redução de **50%**.
- **Nova C**: C_gd2 cai para 0,1125 fF e C_g4 para 0,7875 fF:
  `C = 0,225 + 0,225 + 1 + 1 + 0,7875 + 0,7875 + 0,2 =` **4,225 fF** (C_dbp mantida, como pede o enunciado).
- **Atrasos**:
  ```
  t_PHL = 1,544 × 4,225f / (115µ × 1,5 × 2,5) ≈ 15,1 ps
  t_PLH = 1,544 × 4,225f / (30µ × 1,5 × 2,5)  ≈ 58,0 ps
  t_P ≈ 36,5 ps
  ```

⚠️ O livro dá t_PHL = 15,8 ps, t_PLH = 20,5 ps e t_P = 18,1 ps. O t_PLH do livro não faz sentido físico: o PMOS ficou 3 vezes mais fraco e C caiu só 32%, então a subida tem que ficar **mais lenta** (≈ 28,6 × 3 × 0,676 ≈ 58 ps), não mais rápida. Os números do livro parecem ter sido calculados mantendo o (W/L)_p original na fórmula do atraso. A conclusão que vale levar para a prova: igualar os W/L economiza área, mas desequilibra o inversor (t_PLH ≈ 3,8 × t_PHL, a razão k'_n/k'_p).

### 10.3 — potência dinâmica a 500 MHz

```
P = f·C·V_DD² = 500×10⁶ × 6,25×10⁻¹⁵ × 2,5² = 19,5 µW
```

---

## 7. Portas complexas (Sedra + slides)

Arquivo: `7. Portas Complexas.pdf` — exercícios 10.4, 10.5, 10.8, 10.9 e um exercício extra sem resposta (resolvido aqui).

### Resumo

- **CMOS complementar**: a PUN (rede de pull-up, PMOS) é a dual da PDN (rede de pull-down, NMOS). Série vira paralelo e vice-versa. A PDN implementa a função negada (¬F) com as entradas diretas.
- **Dimensionamento pelo pior caso**: cada caminho de condução deve ter a mesma resistência do inversor de referência ((W/L)_n = n, (W/L)_p = p). k transistores em série precisam de W/L = k vezes o do inversor. Transistores em paralelo ficam com W/L igual ao do inversor (pior caso = só um conduzindo).
- **NAND de k entradas**: NMOS = k·n, PMOS = p. **NOR de k entradas**: NMOS = n, PMOS = k·p. Como p já é 2 a 4 vezes n, a NOR ocupa muito mais área. Por isso se prefere NAND.
- **Grafo e caminho de Euler**: cada rede vira um grafo (nós = junções, arestas = transistores com o nome da entrada). Se a PUN e a PDN tiverem um caminho de Euler com a **mesma sequência de entradas**, o leiaute cabe em uma faixa contínua de difusão p e uma de difusão n, com as colunas de poly em comum e sem quebras. Um grafo tem caminho de Euler se tiver 0 ou 2 vértices de grau ímpar.

### 10.4 — NOR4 e NAND4 (L = 0,5 µm, n = 1,5, p = 6)

| Porta | NMOS | PMOS |
|---|---|---|
| NOR4 (NMOS em paralelo, PMOS em série) | n = 1,5 → **W = 0,75 µm** (0,75/0,5) | 4p = 24 → **W = 12 µm** (12/0,5) |
| NAND4 (NMOS em série, PMOS em paralelo) | 4n = 6 → **W = 3 µm** (3/0,5) | p = 6 → **W = 3 µm** (3/0,5) |

```
Área ∝ ΣW:  NOR = 4×0,75 + 4×12 = 51 µm·L,   NAND = 4×3 + 4×3 = 24 µm·L
Área_NOR / Área_NAND = 51/24 = 2,125
```

### 10.5 — razão entre correntes máxima e mínima da NAND4

- (a) Carga, pelos PMOS em paralelo: a corrente máxima ocorre com os 4 ligados, a mínima com só 1. Razão = **4**.
- (b) Descarga, pelos NMOS em série: só existe um caminho, e ele exige os 4 ligados. Razão = **1**.

### 10.8 — dimensionar a porta da Fig. 10.14: Y = ¬[A·(B + C·D)]

- **PDN** (¬Y = A·(B + CD)): A em série com [B em paralelo com (C em série com D)].
- **PUN** (dual): A em paralelo com [B em série com (C em paralelo com D)].

Igualar cada caminho ao inversor, somando "resistências" proporcionais a 1/(W/L):

```
PDN: caminho A–B:   1/n_A + 1/n_B = 1/n           → n_A = n_B = 2n
     caminho A–C–D: 1/(2n) + 1/n_C + 1/n_D = 1/n  → n_C = n_D = 4n
PUN: caminho A:     p_A = p
     caminho B–C (ou B–D): 1/p_B + 1/p_C = 1/p    → p_B = p_C = p_D = 2p
```

Resposta: **p_A = p; p_B = p_C = p_D = 2p; n_A = n_B = 2n; n_C = n_D = 4n.**

### 10.9 — NAND4 dimensionada (Fig. 10.17) × todos com n e p

- Pela Fig. 10.17 (NMOS = 4n, PMOS = p): t_PHL = t_PLH = t_p do inversor.
- Com todos os NMOS = n e PMOS = p:
  - **t_PLH' = t_p** (pior caso: 1 PMOS de W/L = p ligado; com os 4 ligados, t_p/4)
  - **t_PHL' = 4·t_p** (4 NMOS de W/L = n em série equivalem a n/4)

### Exercício extra (sem resposta no PDF)

#### Função 1: F = Ā + B̄ + C̄·D̄

Por De Morgan: **F = ¬[A·B·(C + D)]**.

**1) Esquemático CMOS complementar**

```
                VDD ─────┬──────────┬──────────┐
                         │          │          │
                   A ─┤ PA     B ─┤ PB     C ─┤ PC
                         │          │          │ p1
                         │          │     D ─┤ PD
                         │          │          │
          F ─────────────┴──────────┴──────────┘
                         │
                   A ─┤ NA
                         │ n1
                   B ─┤ NB
                         │ n2
                 ┌───────┴───────┐
           C ─┤ NC           D ─┤ ND
                 └───────┬───────┘
                        GND
```

- PUN: PA ∥ PB ∥ (PC em série com PD), entre VDD e F.
- PDN: NA em série com NB em série com (NC ∥ ND), entre F e GND.

**2) Grafos**

- PDN: arestas A: F–n1, B: n1–n2, C: n2–GND, D: n2–GND.
- PUN: arestas A: VDD–F, B: VDD–F, C: VDD–p1, D: p1–F.

**3) Caminho de Euler com a mesma sequência: A, B, C, D (existe)**

- PDN: `F –A– n1 –B– n2 –C– GND –D– n2` (vértices ímpares: F e n2)
- PUN: `VDD –A– F –B– VDD –C– p1 –D– F` (vértices ímpares: VDD e F)

**4) Diagrama de palitos** (colunas de poly A B C D, uma faixa contínua de cada difusão):

```
              A       B       C       D
              ┃       ┃       ┃       ┃
difusão p:  [VDD]━━━[ F ]━━━[VDD]━━━[p1 ]━━━[ F ]
              ┃       ┃       ┃       ┃
difusão n:  [ F ]━━━[n1 ]━━━[n2 ]━━━[GND]━━━[n2 ]
              ┃       ┃       ┃       ┃
```

Ligações em metal: as duas regiões VDD da difusão p ao trilho de VDD. As regiões F (duas na difusão p, uma na n) juntas na saída. As duas regiões n2 entre si. A região GND ao trilho de terra.

#### Função 2: F = Ā + (B̄ + C̄)·(D̄ + Ē)

Por De Morgan: **F = ¬[A·(B·C + D·E)]**.

**1) Esquemático CMOS complementar**

- PDN (entre F e GND): NA em série com [(NB em série com NC) ∥ (ND em série com NE)].
- PUN (entre VDD e F): PA ∥ [(PB ∥ PC) em série com (PD ∥ PE)].

```
         VDD ──┬────────────┬──────┐
               │            │      │
         A ─┤ PA      B ─┤ PB  C ─┤ PC
               │            └──┬───┘ m
               │            ┌──┴───┐
               │      D ─┤ PD  E ─┤ PE
               │            │      │
         F ────┴────────────┴──────┘
               │
         A ─┤ NA
               │ x
          ┌────┴─────┐
    B ─┤ NB     D ─┤ ND
          │ y        │ z
    C ─┤ NC     E ─┤ NE
          └────┬─────┘
              GND
```

**2) Grafos**

- PDN: A: F–x; B: x–y; C: y–GND; D: x–z; E: z–GND. Graus: F = 1, x = 3, y = 2, z = 2, GND = 2. Vértices ímpares: F e x.
- PUN: A: VDD–F; B, C: VDD–m; D, E: m–F. Graus: VDD = 3, F = 3, m = 4. Vértices ímpares: VDD e F.

**3) Caminhos de Euler compatíveis: não existem**

- Na **PDN**, o nó F tem grau 1, então A fica obrigatoriamente na ponta da sequência. Depois de A, o caminho desce uma pilha série até GND e sobe pela outra. Resultado: B e C ficam juntos, e D e E também (ex.: A B C E D).
- Na **PUN**, se A estiver na ponta, o caminho continua por F (ou VDD) e entra num par paralelo, por exemplo D. No nó m não pode tomar E, porque ficaria preso em F. Precisa ir para B/C e só depois voltar para E. Resultado: o par D/E (ou B/C) fica separado (ex.: A D B C E).
- Nenhuma ordem satisfaz as duas redes ao mesmo tempo. Trocar a ordem dos transistores em série ou colocar A do lado do GND não muda esse argumento.

**4) Leiaute (com uma quebra de difusão)**

Usando a ordem A B C E D, que é Euler na PDN (`F –A– x –B– y –C– GND –E– z –D– x`), a difusão p precisa de **uma quebra** entre C e E:

```
              A       B       C               E       D
              ┃       ┃       ┃               ┃       ┃
difusão p:  [ F ]━━━[VDD]━━━[ m ]━━━[VDD]   [ m ]━━━[ F ]━━━[ m ]
              ┃       ┃       ┃      (quebra) ┃       ┃
difusão n:  [ F ]━━━[ x ]━━━[ y ]━━━[GND]━━━[ z ]━━━[ x ]
              ┃       ┃       ┃               ┃       ┃
```

Ligações em metal: regiões F juntas na saída, os dois VDD no trilho, os três m entre si, os dois x entre si, GND no trilho. Outra saída seria duplicar o transistor A (¬F = A·B·C + A·D·E), o que elimina a quebra ao custo de mais transistores.

---

## 8. Lógica dinâmica (Sedra)

Arquivo: `8. Logica_Dinamica.pdf` — exercícios 10.12, 10.13 e um problema extra.

### Resumo

- **Porta dinâmica com PDN**: PMOS Q_p de pré-carga (porta em φ) entre VDD e a saída Y, a PDN da função, e NMOS Q_e de avaliação (porta em φ) entre a PDN e GND.
  - φ = 0 (**pré-carga**): Q_p liga e Q_e desliga, então Y vai para V_DD.
  - φ = 1 (**avaliação**): Q_p desliga e Q_e liga. Se a PDN conduzir, Y descarrega; senão, Y fica em V_DD guardado em C_L.
  - Y = ¬(função da PDN). Usa N + 2 transistores em vez de 2N, e não há corrente estática.
- **Problema da cascata**: a saída de uma porta dinâmica começa em V_DD na avaliação. Até ela cair, a porta seguinte já começa a descarregar indevidamente e perde carga. Solução: lógica **dominó** (inversor estático em cada saída).
- **Redistribuição (compartilhamento) de carga**: na avaliação, um nó interno da PDN que estava descarregado pode se ligar à saída sem existir caminho até GND. A carga de C_L se divide com C_1 e Y cai:
  - Sem limitação: `V_Y = V_DD·C_L/(C_L + C_1)`.
  - O transistor que liga os nós corta quando o nó interno chega a V_DD − V_t. Se isso acontecer antes de equalizar, `V_Y = [C_L·V_DD − C_1·(V_DD − V_t)] / C_L`.
- **Corrente média** para estimar tempos: `Δt ≈ C·ΔV / I_média`.

### 10.12 — cascata (Fig. E10.12)

Dados (Exemplo 10.4, consistentes com as respostas): V_DD = 5 V, V_t = 1 V, k'_n = 50 µA/V²; todos os NMOS com W/L = 4/2 = 2; C_L1 = C_L2 = 40 fF.

- **(a)** Dois transistores de W/L = 2 em série equivalem a W/L = 2/2: **(W/L)_eq1 = (W/L)_eq2 = 1**.
- **(b)** i_D1 com a porta em V_DD (V_OV = 4 V):
  - v_Y1 = V_DD (saturação): `½ × 50µ × 1 × 4² =` **400 µA**
  - v_Y1 = V_t = 1 V (triodo): `50µ × [4 × 1 − 1²/2] =` **175 µA**
  - Média: **≈ 288 µA**
- **(c)** `Δt = C_L1·ΔV / I_média = 40f × (5 − 1) / 287,5µ ≈` **0,56 ns**
- **(d)** Com v_Y1 = 3 V (meio da excursão), Q_eq2 saturado: `i_D2 = ½ × 50µ × 1 × (3 − 1)² =` **100 µA**
- **(e)** `Δv_Y2 = i_D2·Δt / C_L2 = 100µ × 0,5565n / 40f ≈` **1,4 V** → **v_Y2 ≈ 3,6 V** (em vez de ficar em 5 V)

### 10.13 — redistribuição de carga (Fig. 10.34a)

Dados: V_DD = 5 V, V_tn = 1 V, C_L = 30 fF. Carga inicial `q = C_L·V_DD = 150 fC`. Limite do nó interno: V_DD − V_t = 4 V.

**(a) C_1 = 5 fF**

```
Equalização ideal: V = 150 fC / 35 fF = 4,29 V  > 4 V  → Q_1 corta antes
q_C1 = 5 fF × 4 V = 20 fC
V_Y  = (150 − 20) / 30 = 4,33 V   → queda de 0,67 V
```

**(b) C_1 = 10 fF**

```
Equalização ideal: V = 150 fC / 40 fF = 3,75 V  < 4 V  → Q_1 conduz até equalizar
V_Y = V_X = 3,75 V   → queda de 1,25 V
```

Quanto maior C_1 em relação a C_L, maior a queda.

### Problema extra — NOT, NAND2, NOR2 e Y = ¬(AB + CD) dinâmicos

Estrutura comum:

```
            VDD
             │
     φ ──○┤ Qp   (PMOS de pré-carga)
             │
             ├──────── Y   (C_L para GND)
             │
         [  PDN  ]
             │
     φ ───┤ Qe   (NMOS de avaliação)
             │
            GND
```

| Porta | PDN (de Y para Qe) | Nós internos ligados a Y | Redistribuição de carga? |
|---|---|---|---|
| NOT (Y = Ā) | A | nenhum | **Não** |
| NAND2 (Y = ¬(AB)) | A em série com B (nó X entre eles) | X | **Sim**: A = 1 e B = 0 na avaliação, com X descarregado de um ciclo anterior (por exemplo, A = B = 1 no ciclo anterior e A = 0 durante a pré-carga) |
| NOR2 (Y = ¬(A+B)) | A ∥ B, direto entre Y e Qe | nenhum (o nó acima de Qe só se liga a Y quando há caminho para GND) | **Não** |
| Y = ¬(AB + CD) | (A em série com B) ∥ (C em série com D), nós X1 (A–B) e X2 (C–D) | X1, X2 | **Sim**: A = 1, B = 0 e/ou C = 1, D = 0, sem caminho completo. Pior caso: A = C = 1, B = D = 0, quando C_L divide carga com os dois nós |

Isso confirma a resposta parcial do PDF: os casos com problema são a **NAND** e **Y = ¬(AB + CD)**. Em geral, há risco sempre que existem transistores em série na PDN e o de cima liga sem que o de baixo ligue.

Soluções: pré-carregar os nós internos (PMOS extras em φ), usar um **keeper** (PMOS fraco realimentado pela saída), garantir C_L ≫ C_interno, ou colocar perto de Y o transistor cuja entrada chega por último.

---

## 9. Fórmulas para decorar

| Tema | Fórmula |
|---|---|
| λ | λ = L_min / 2 |
| Pitch de metal com contato | contato + 2·sobreposição + espaçamento |
| Resistência de fio | R = ρ·l/(t·w) = R□·l/w |
| Triodo | i_D = k'(W/L)[V_OV·v_DS − v_DS²/2] |
| Saturação | i_D = ½k'(W/L)V_OV²(1 + λv_DS) |
| r_DS (v_DS pequeno) | 1/[k'(W/L)V_OV] |
| r_o | V_A/I_D = 1/(λI_D), V_A = V'_A·L |
| Efeito de corpo | V_t = V_t0 + γ[√(2φ_f + V_SB) − √(2φ_f)] |
| C_ox | ε_ox/t_ox (ε_ox = 3,9·8,854×10⁻¹² F/m) |
| Porta | C_g = C_ox·W·L (+ 2·C_ov·W) |
| Junção | C = C_j·A/(1 + V_R/φ_b)^m_j + C_jsw·P/(1 + V_R/φ_bsw)^m_jsw |
| L efetivo | L_eff = L − 2L_d |
| Inversor casado | V_IL = (3V_DD + 2V_t)/8, V_IH = (5V_DD − 2V_t)/8 |
| Atraso | t_PHL = α_n·C/(k'_n(W/L)_n V_DD), α = 2/[7/4 − 3V_t/V_DD + (V_t/V_DD)²] |
| Potência dinâmica | P = f·C·V_DD² |
| Série / paralelo (dimensionamento) | k em série → W/L × k; paralelo → W/L do inversor |
| Redistribuição de carga | V_Y = V_DD·C_L/(C_L + C_1), limitado por V_X ≤ V_DD − V_t |
| Tempo com corrente média | Δt = C·ΔV/I_média |
