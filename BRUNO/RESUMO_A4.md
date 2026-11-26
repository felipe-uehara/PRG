# Resumo VLSI/CMOS — pasta BRUNO

### 1. Regras de projeto (Weste)
- **λ = L_min/2** (200 nm → 0,1 µm; 180 nm → 90 nm; 0,8 µm → 0,4 µm). Para mapear para a foundry, cada regra λ × λ deve ser ≥ a regra da foundry. Se for muito maior, sobra área.
- Contatos MOSIS (SCMOS/SUBM/DEEP): tamanho 2×2; sobreposição 1,5; contato–contato **2/3/4**; até a porta 2; contato de poly até outro poly (5.5.b) **4/5/5**.
- **Pitch de metal com contato** = contato + 2·sobreposição + espaçamento → M1: 2+2+3 = **7λ**. Para reduzir, escalone os contatos (≈ 6,5λ no M1). Nunca fica abaixo de largura + espaçamento (M6 ≥ 10λ).
- **Pitch de transistor**: sem contato = poly 2 + espaçamento 3 = **5λ**; com contato = 2 + 2·2 + 2 = **8λ**.
- Foundry de 200 nm → **SUBM**. SCMOS viola 5.3/6.3 e 5.5.b; DEEP desperdiça área em 5.3/6.3.

### 2. Interconexão (Weste)
- **R = ρl/(tw) = R□·l/w**. ρ (µΩ·cm): Ag 1,6; Cu 1,7; Au 2,2; Al 2,8; W 5,3.
- Mesma R: w_Al ≥ (2,8/1,7)·w_Cu = 1,65 × 780 = **1285 nm**.
- **C = C_gnd + C_adj**. C_gnd vai para as camadas de cima e de baixo (∝ w/h + franjas). C_adj vai para os vizinhos (∝ t/s). Fio típico ≈ 0,2 fF/µm. Interpole w e s nas tabelas. Linha de 10 µm: Cu 2,21–2,55 **fF**; Al 2,34–2,90 fF (a unidade é fF, não pF).
- h maior ⇒ **C_gnd cai**; C_adj quase não muda (mas pesa mais no total).

### 3. MOSFET (Sedra, cap. 4)
- V_OV = V_GS − V_t. **Triodo** (V_DS < V_OV): i = k'(W/L)[V_OV·V_DS − V_DS²/2]; r_DS = 1/[k'(W/L)V_OV].
- **Saturação**: i = ½k'(W/L)V_OV²(1 + λV_DS); V_A = V'_A·L; λ = 1/V_A; r_o = V_A/I_D.
- **PMOS**: use V_SG, V_SD e |V_t|. Conduz se V_G ≤ V_S − |V_t|; triodo se V_D ≥ V_G + |V_t|.
- **Efeito de corpo**: V_t = V_t0 + γ[√(2φ_f + V_SB) − √(2φ_f)].
- Respostas: 4.4 triodo/sat/sat · 4.5 275/320/320 µA · 4.6 256 µA, 500 Ω · 4.7 40 V; 0,025 V⁻¹; 0,51 mA; 80 kΩ; 25 µA · 4.8 V_G ≤ 4 V; V_OV = 0,5 V; V_G = 3,5 V; r_o = 0,67 MΩ; 78/82,5 µA · 4.9 1,23 V.

### 4–5. Capacitâncias do MOSFET
- **C_ox = ε_ox/t_ox** (ε_ox = 3,45×10⁻¹¹ F/m). Porta: **C_ox·W·L + 2·C_ov·W**. Com difusão lateral: L_eff = L − 2L_d.
- Canal por região: corte → tudo para o corpo; triodo → ½ S e ½ D; saturação → ⅔ S e 0 D.
- **Junção**: C = C_j·A/(1 + V_R/φ)^m_j + C_jsw·P/(1 + V_R/φ_sw)^m_jsw (cai com V_R). P = 2L_s + W (sem o lado da porta) ou 2(L_s + W).
- **Nó de saída**: C_out = 2C_gd (Miller) + C_db + C_g da carga. Carga armazenada: Q = C·ΔV.
- tsmc035 (W 1,5; L 0,5; L_d 0,05 µm): A_g = 0,75 µm²; C_g ≈ 3,36 fF; AD = 1,8 µm², PD = 5,4 µm, C_DB(0 V) ≈ 3,2 fF; C_ov = 0,35 × 1,5 = 0,53 fF por lado.
- Processo de 0,25 µm (W 1, L_s 0,5 µm): C_IN = 1,5 + 0,62 = **2,12 fF**; C_OUT = 3,57 / 4,00 / 4,30 fF para V_out = 2,5 / 0,5 / 0 V; Q_IN = 5,3 fC.
- Leiaute com λ = 0,4 µm: L = 0,8 µm, W = 1,6 µm; AD = 20λ² = 3,2 µm², PD = 18λ = 7,2 µm; AS = 36λ² = 5,76 µm², PS = 26λ = 10,4 µm.

### 6. Inversor CMOS
- **Inversor casado**: V_IL = (3V_DD + 2V_t)/8; V_IH = (5V_DD − 2V_t)/8; NM_L = NM_H = V_IL. Ex. 4.41: 2,1 / 2,9 / 2,1 V.
- 4.42: NMOS em triodo com V_OV = 8 V: i = 400µ × (8 × 0,5 − 0,125) = **1,55 mA**.
- **t_PHL = α_n·C/(k'_n(W/L)_n·V_DD)**; α = 2/[7/4 − 3V_t/V_DD + (V_t/V_DD)²]; t_P = (t_PHL + t_PLH)/2.
- **C = 2C_gd1 + 2C_gd2 + C_db1 + C_db2 + C_g3 + C_g4 + C_w**. Exemplo 10.1: C = 6,25 fF; t_PHL = 22,4 ps; t_PLH = 28,6 ps; t_P = 25,5 ps.
- 10.1 (+0,1 pF): o atraso é ∝ C → ≈ 433 ps (o livro dá 437). 10.2 ((W/L)_p = (W/L)_n): área −50%; C = 4,225 fF; t_PHL ≈ 15 ps; t_PLH ≈ 58 ps (os 20,5 ps do livro estão errados). 10.3: **P = f·C·V_DD²** = 19,5 µW.

### 7. Portas complexas
- A PDN (NMOS) realiza ¬F; a PUN (PMOS) é a dual (série ↔ paralelo).
- **Dimensionar pelo pior caso**: k transistores em série → k·n (ou k·p); em paralelo → n (ou p). NAND de k entradas: N = kn, P = p. NOR de k entradas: N = n, P = kp.
- 10.4: NOR4 N 0,75/0,5 e P 12/0,5; NAND4 N 3/0,5 e P 3/0,5; razão de áreas 51/24 = 2,125. 10.5: carga 4, descarga 1.
- 10.8, Y = ¬[A(B + CD)]: p_A = p; p_B = p_C = p_D = 2p; n_A = n_B = 2n; n_C = n_D = 4n.
- 10.9 (NAND4 com todos n e p): t_PLH = t_p e t_PHL = 4t_p.
- **Euler**: monte o grafo (nós = junções, arestas = transistores). O caminho existe se houver 0 ou 2 vértices ímpares. Se PUN e PDN tiverem a mesma sequência, a difusão sai contínua.
- F = ¬A + ¬B + ¬C·¬D = ¬[AB(C + D)]: a ordem A B C D funciona. F = ¬A + (¬B + ¬C)(¬D + ¬E) = ¬[A(BC + DE)]: não há caminho comum → 1 quebra de difusão (ou duplicar A).

### 8. Lógica dinâmica
- φ = 0: **pré-carga** (Q_p liga, Y = V_DD). φ = 1: **avaliação** (Q_e liga e a PDN decide). Y = ¬PDN, com N + 2 transistores.
- **Cascata**: no início da avaliação Y1 ainda está alto e descarrega Y2 indevidamente → use lógica dominó. 10.12: (W/L)_eq = 1; i_D1 = 400/175 µA → média 288 µA; Δt = CΔV/I = 0,56 ns; i_D2 = 100 µA; Δv_Y2 = 1,4 V → v_Y2 = 3,6 V.
- **Redistribuição de carga**: V_Y = V_DD·C_L/(C_L + C_1), mas o nó interno não passa de V_DD − V_t. 10.13 (C_L = 30 fF, q = 150 fC): C_1 = 5 fF → 4,29 V > 4 V → V_Y = (150 − 20)/30 = **4,33 V**; C_1 = 10 fF → **3,75 V**.
- O problema aparece na NAND (A = 1, B = 0) e em ¬(AB + CD) (A = 1, B = 0 e/ou C = 1, D = 0). NOT e NOR não têm. Soluções: keeper ou pré-carga dos nós internos.
