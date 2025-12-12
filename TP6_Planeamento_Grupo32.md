# TP6_Planeamento_Grupo32

## Premissas usadas
- Turno **T=3**, Grupo **G=2** (logo T+G=5 e a ligação ao exterior fica no **ZT5 da Fábrica** conforme a alínea c).
- Áreas e distâncias lidas diretamente das figuras do enunciado:
  - **Escritórios**: A = 300 + 2·(T·10+G) = 364 m²; B = 364 m²; C = 250 − (7·10+G) = 178 m² → total 906 m².
  - **Show Room**: círculo com diâmetro (20+T+G)=25 m → área π·(12,5)²=490,9 m²; descontando ZT3 (12 m²) → 478,9 m².
  - **Armazém**: retângulo (5·(T+G))×(20+T+G)=25×25=625 m²; descontando ZT6 (22 m²) e ZT7 (20 m²) → 583 m².
  - **Fábrica**: retângulo 30 m × (50−G)=30×48=1440 m²; menos ZT4 e ZT5 (6 m × 6 m cada, 72 m²) → 1368 m².
  - Distâncias de campus da Figura 1: ZT1↔ZT3 = **200 + 50·G = 300 m**; ZT1↔ZT6 = **50 + 10·G = 70 m**; ZT6↔ZT5 = **100 m**; aresta interna ZT4↔ZT5 = **50 m**.
- Densidade base: 2 tomadas RJ45 por cada 10 m². Ajustes indicados no enunciado: Armazém = 1/3 da densidade base; Showroom = dobro; Fábrica = 1/4 da densidade base. As zonas técnicas e corredores não têm tomadas.
- Cada edifício tem 7 câmaras (4 exteriores + 3 interiores) e pontos de acesso Wi‑Fi dimensionados a 1 AP/150 m² (arredondado por excesso). AP e câmaras usam as tomadas já contabilizadas.
- Comprimento médio da cablagem horizontal: 60 − 10·T + G = **32 m**.

## a) Quantidade de tomadas por edifício (cálculos visíveis)
- Fórmula base: \( \text{tomadas}=\text{área}\times0{,}2\) (2 tomadas/10 m²).
- Ajustes por edifício: Showroom ×2; Fábrica ×1/4; Armazém ×1/3; Escritórios = base.

| Edifício | Área útil | Cálculo | Tomadas calculadas |
| --- | --- | --- | --- |
| Escritórios | 906 m² | 906 × 0,2 | 181,2 → **182** |
| Showroom | 478,9 m² | 478,9 × 0,2 × 2 | 191,6 → **192** |
| Fábrica | 1368 m² | 1368 × 0,2 ÷ 4 | 68,4 → **69** |
| Armazém | 583 m² | 583 × 0,2 ÷ 3 | 38,9 → **39** |
| **Total** | — | — | **482** |

- Total de utilizadores = 482 ÷ 2 = **241**.
- Colaboradores remotos = 10 + G = **12**.

### Sistema de identificação de tomadas
- Padrão de etiqueta: **[Edifício]-[ZT]-R[rack]-PP[patch]-T[nº tomada]** (ex.: `ESC-ZT1-R1-PP02-T15`).
- As tomadas de área de trabalho são numeradas sequencialmente por bastidor (Tomada 1 Bastidor 1, Tomada 2 Bastidor 1, ...); o mesmo número é replicado no patch panel correspondente.
- Fibra e cobre seguem a mesma lógica, com cores distintas para dados/voz/videovigilância/Wi‑Fi.

## b) Diagrama de rede e localização dos distribuidores
- **CD/BD primário = ZT5 (Fábrica)**: ligação à operadora (alínea c) e concentração do backbone de campus.
- **ZT1 (Escritórios)**: **BD/FD** para Escritórios A/B, interligado ao CD por FO (aresta ZT1↔ZT5 via 100 m + 70 m, com equipamentos óticos para 10G). **ZT2**: **FD** para Escritórios C, ligado a ZT1 por FO (45 m).
- **ZT3 (Showroom)**: **BD/FD** ligado ao CD (ZT5) por FO SM 12F (300 m).
- **ZT4 (Fábrica)**: **FD** ligado ao CD (50 m) para cobrir a ala oposta.
- **ZT6 (Armazém)**: **BD/FD** ligado ao CD (70 m), expandido a **ZT7** (65 m) para a zona remota.

Diagrama textual (estrela a partir do CD em ZT5):

```
           [Showroom ZT3]
                |
                | FO SM 12F 300 m
                |
[FD ZT2]--FO 45m--[BD/FD ZT1]--FO SM 12F (70 m) --[BD/FD ZT6]--FO SM 12F 65 m--[FD ZT7]
                              \
                               \__ FO SM 12F 50 m __ [FD ZT4]
```

Para Wi‑Fi (exercício b), seguem-se as zonas de cobertura definidas no edifício de Escritórios: AP colocados de forma homogénea por cada 150 m², sem reutilização entre edifícios ou pisos.

## c) Cablagem por subsistema
### Horizontal
| Edifício | Tipo de cabo | Tomadas | Comprimento médio | Cálculo | Comprimento total |
| --- | --- | --- | --- | --- | --- |
| Escritórios | Cat6A F/UTP | 182 | 32 m | 182 × 32 | 5824 m |
| Showroom | Cat6A F/UTP | 192 | 32 m | 192 × 32 | 6144 m |
| Fábrica | Cat6A **S/FTP** (EMI moderadas) | 69 | 32 m | 69 × 32 | 2208 m |
| Armazém | Cat6A F/UTP | 39 | 32 m | 39 × 32 | 1248 m |

### Backbone de edifício (entre FD do mesmo edifício)
| Ligação | Distância | Meio | Observações |
| --- | --- | --- | --- |
| ZT1 ↔ ZT2 | 45 m | FO SM 12F | Permite 10G/40G e redundância lógica. |
| ZT4 ↔ ZT5 | 50 m | FO SM 12F | Segmentação da fábrica. |
| ZT6 ↔ ZT7 | 65 m | FO SM 12F | Cobertura integral do armazém. |

### Backbone de campus (ligando ao CD no ZT5)
| Origem (CD) | Destino (BD) | Distância (figura) | Meio | Observações |
| --- | --- | --- | --- | --- |
| ZT5 | ZT3 | 300 m | FO SM 12F | Showroom. |
| ZT5 | ZT1 | 70 m | FO SM 12F | Escritórios. |
| ZT5 | ZT6 | 100 m | FO SM 12F | Armazém. |

## d) Dimensionamento de ativos por distribuidor
Número de portas = utilizadores locais + câmaras + AP + servidores/roteador + 20% de margem.

| Distribuidor | Base de cálculo | Margem 20% | Portas (≥) | Proposta |
| --- | --- | --- | --- | --- |
| ZT1 (BD/FD) | 73 utilizadores + 4 câmaras + 5 AP = 82 | 82 × 1,2 = 98,4 | 100 | 2×48p POE+ (stack) |
| ZT2 (FD) | 18 utilizadores + 3 câmaras + 2 AP = 23 | 23 × 1,2 = 27,6 | 28 | 1×24p POE+ |
| ZT3 (BD/FD) | 96 utilizadores + 7 câmaras + 4 AP = 107 | 107 × 1,2 = 128,4 | 129 | 3×48p POE+ |
| ZT4 (FD) | 21 utilizadores + 4 câmaras + 6 AP = 31 | 31 × 1,2 = 37,2 | 38 | 1×48p POE+ |
| ZT5 (CD/BD/FD) | 13 utilizadores + 3 câmaras + 4 AP + 1 router + 2 servidores = 23 | 23 × 1,2 = 27,6 | 28 | 1×24p POE+ |
| ZT6 (BD/FD) | 12 utilizadores + 4 câmaras + 2 AP = 18 | 18 × 1,2 = 21,6 | 22 | 1×24p POE+ |
| ZT7 (FD) | 8 utilizadores + 3 câmaras + 2 AP = 13 | 13 × 1,2 = 15,6 | 16 | 1×24p POE+ |

Todos os distribuidores incluem UPS dimensionada para pelo menos 30 minutos.

## e) Cálculo de fluxos de débito (241 utilizadores presenciais + 12 remotos)
Servidores/localização para T+G=5 (coluna **Grupos com T+G=5 ou 6** do enunciado):
- **Cloud**: E-mail, Videovigilância, VoIP, Ficheiros.
- **On-prem (ZT5/CD)**: Base de dados e Impressão.

### Tabela 1 — Tráfego **LAN** (aplicações com servidor na empresa)
| Aplicação | Utilizadores aplicacionais | Simult. | DSN | Cálculo | Tráfego agregado |
| --- | --- | --- | --- | --- | --- |
| Base de dados (locais) | 81% de 241 = 195 | 40% | 128 kbps | 195 × 0,4 × 0,128 | **10,0 Mbps** |
| Impressão | 241 | 7% | 2048 kbps | 241 × 0,07 × 2,048 | **34,6 Mbps** |
| **Subtotal LAN (interno)** | — | — | — | — | **≈44,6 Mbps** |

### Tabela 2 — Tráfego **Internet** (aplicações em cloud + remotos)
| Aplicação | Utilizadores aplicacionais | Simult. | DSN | Cálculo | Tráfego agregado |
| --- | --- | --- | --- | --- | --- |
| E-mail (cloud) | 90% de 241 = 217 **+12 remotos** = 229 | 67% | 384 kbps | 229 × 0,67 × 0,384 | **58,9 Mbps** |
| Videovigilância (cloud) | 28 câmaras | 100% | 2048 kbps | 28 × 2,048 | **57,3 Mbps** |
| Base de dados (remotos) | 12 remotos | 40% | 128 kbps | 12 × 0,4 × 0,128 | **0,61 Mbps** |
| VoIP (cloud) | 30% de 241 = 72 | 20% | 32 kbps | 72 × 0,2 × 0,032 | **0,46 Mbps** |
| Ficheiros (cloud) | 86% de 241 = 207 **+12 remotos** = 219 | 60% | 512 kbps | 219 × 0,6 × 0,512 | **67,2 Mbps** |
| Internet (navegação) | 75% de 241 = 181 | 78% | 512 kbps | 181 × 0,78 × 0,512 | **72,2 Mbps** |
| **Subtotal Internet** | — | — | — | — | **≈256 Mbps** |

Observação: Ao separar LAN (servidores internos) de Internet (cloud + remotos), evidencia-se a necessidade de ~256 Mbps de acesso externo para 241 utilizadores presenciais + 12 remotos.
