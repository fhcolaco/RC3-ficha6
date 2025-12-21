# TP6_Planeamento_Grupo32

## Premissas usadas
- Turno **T=3**, Grupo **G=2** (T+G=5, ligação ao exterior em **ZT5/Fábrica**).
- Áreas e distâncias lidas das figuras (OCR das plantas):
  - **Escritórios**: \((300+2·(10·T+G))·2 + 250 − (10·T−G)) = (300+2·32)·2 + 250 − 28 = 950\) m².
  - **Show Room** (circular): diâmetro \((20+T−G)=21\) m → \(r=10{,}5\) m ⇒ \(A=\pi r^2≈346{,}36\) m²; menos ZT3 (12 m²) → **334,36 m²**.
  - **Armazém**: retângulo \((5·(T+G)) × (20+G) = (5·5)×22\) → 550 m²; menos ZT6 (5×6 m=30 m²) e ZT7 (20 m²) → **500 m²**.
  - **Fábrica**: retângulo 30 m × (50−G)=30×48=1440 m²; menos ZT4 e ZT5 (6×8 m cada, 48+48) → **1344 m²**.
  - Distâncias de campus (Figura 1): ZT1↔ZT3 = **200 + 50·G = 300 m**; ZT1↔ZT6 = **50 + 10·G = 70 m**; ZT6↔ZT5 = **100 m**; aresta interna ZT4↔ZT5 = **50 m**.
- Densidade base: 2 tomadas RJ45/10 m². Ajustes: Armazém = 1/3; Showroom = 2×; Fábrica = 1/4. Zonas técnicas/corredores sem tomadas.
- Cada edifício tem 7 câmaras (4 exteriores + 3 interiores) e AP Wi‑Fi dimensionados a 1 AP/150 m² (arredondado por excesso). AP e câmaras usam tomadas já contabilizadas.
- Comprimento médio da cablagem horizontal: **32 m** (60 − 10·T + G).

## a) Quantidade de tomadas por edifício (cálculos visíveis)
- Fórmula base: \( \text{tomadas}=\text{área}\times0{,}2\) (2 tomadas/10 m²), com os multiplicadores de densidade indicados.

| Edifício | Área útil | Cálculo | Tomadas calculadas |
| --- | --- | --- | --- |
| Escritórios | 950 m² | 950 × 0,2 | 190,0 → **190** |
| Showroom | 334,36 m² | 334,36 × 0,2 × 2 | 133,74 → **134** |
| Fábrica | 1344 m² | 1344 × 0,2 ÷ 4 | 67,2 → **68** |
| Armazém | 500 m² | 500 × 0,2 ÷ 3 | 33,33 → **34** |
| **Total** | — | — | **426** |

- Total de utilizadores = 426 ÷ 2 = **213**.
- Colaboradores remotos = 10 + G = **12**.

### Sistema de identificação de tomadas
- Padrão: **[Edifício]-[ZT]-R[rack]-PP[patch]-T[nº tomada]** (ex.: `ESC-ZT1-R1-PP02-T15`). Numeração “Tomada n Bastidor m” replicada no patch panel.
- Fibra e cobre seguem o mesmo código, com cores distintas para dados/voz/videovigilância/Wi‑Fi.

## b) Diagrama de rede e localização dos distribuidores
- **CD/BD primário = ZT5 (Fábrica)**: ligação à operadora (alínea c) e concentração do backbone de campus.
- **ZT1 (Escritórios)**: **BD/FD** para Escritórios A/B, interligado ao CD por FO (aresta ZT1↔ZT5 via 100 m + 70 m). **ZT2**: **FD** para Escritórios C, ligado a ZT1 por FO (45 m).
- **ZT3 (Showroom)**: **BD/FD** ligado ao CD (ZT5) por FO SM 12F (300 m).
- **ZT4 (Fábrica)**: **FD** ligado ao CD (50 m) para a ala oposta.
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

Para Wi‑Fi (exercício b), seguem-se as zonas de cobertura do edifício de Escritórios: AP colocados de forma homogénea por cada 150 m², sem reutilização entre edifícios ou pisos.

## c) Cablagem por subsistema
### Horizontal
| Edifício | Tipo de cabo | Tomadas | Comprimento médio | Cálculo | Comprimento total |
| --- | --- | --- | --- | --- | --- |
| Escritórios | Cat6A F/UTP | 190 | 32 m | 190 × 32 | 6080 m |
| Showroom | Cat6A F/UTP | 134 | 32 m | 134 × 32 | 4288 m |
| Fábrica | Cat6A **S/FTP** (EMI moderadas) | 68 | 32 m | 68 × 32 | 2176 m |
| Armazém | Cat6A F/UTP | 34 | 32 m | 34 × 32 | 1088 m |

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
Número de portas = utilizadores locais + câmaras + AP + servidores/roteador (quando aplicável) + 20% de margem.

| Distribuidor | Base de cálculo | Margem 20% | Portas (≥) | Proposta |
| --- | --- | --- | --- | --- |
| ZT1 (BD/FD) | 73 utilizadores + 5 câmaras + 5 AP = 83 | 83 × 1,2 = 99,6 | 100 | **3×48p PoE+ empilhados** |
| ZT2 (FD) | 22 utilizadores + 2 câmaras + 2 AP = 26 | 26 × 1,2 = 31,2 | 32 | **1×48p PoE+** |
| ZT3 (BD/FD) | 67 utilizadores + 7 câmaras + 3 AP = 77 | 77 × 1,2 = 92,4 | 93 | **2×48p PoE+** |
| ZT4 (FD) | 20 utilizadores + 4 câmaras + 5 AP = 29 | 29 × 1,2 = 34,8 | 35 | **1×48p PoE+** |
| ZT5 (CD/BD/FD) | 14 utilizadores + 3 câmaras + 4 AP + 1 router + 2 servidores = 24 | 24 × 1,2 = 28,8 | 29 | **1×48p PoE+ L3** (core/roteamento) |
| ZT6 (BD/FD) | 10 utilizadores + 4 câmaras + 2 AP = 16 | 16 × 1,2 = 19,2 | 20 | **1×24p PoE+** |
| ZT7 (FD) | 7 utilizadores + 3 câmaras + 2 AP = 12 | 12 × 1,2 = 14,4 | 15 | **1×24p PoE+** |

Todos os distribuidores incluem UPS dimensionada para pelo menos 30 minutos.

## e) Cálculo de fluxos de débito (213 utilizadores presenciais + 12 remotos)
Servidores/localização para T+G=5 (coluna **Grupos com T+G=5 ou 6** do enunciado):
- **Cloud**: E-mail, Videovigilância, VoIP, Ficheiros.
- **On-prem (ZT5/CD)**: Base de dados e Impressão.

### Tabela 1 — Tráfego **LAN** (aplicações com servidor na empresa)
| Aplicação | Utilizadores aplicacionais | Simult. | DSN | Cálculo | Tráfego agregado |
| --- | --- | --- | --- | --- | --- |
| Base de dados (locais) | 81% de 213 = 173 | 40% | 128 kbps | 173 × 0,4 × 0,128 | **8,86 Mbps** |
| Impressão | 213 | 7% | 2048 kbps | 213 × 0,07 × 2,048 | **30,54 Mbps** |
| **Subtotal LAN (interno)** | — | — | — | — | **≈39,4 Mbps** |

### Tabela 2 — Tráfego **Internet** (aplicações em cloud + remotos)
| Aplicação | Utilizadores aplicacionais | Simult. | DSN | Cálculo | Tráfego agregado |
| --- | --- | --- | --- | --- | --- |
| E-mail (cloud) | 90% de 213 = 192 **+12 remotos** = 204 | 67% | 384 kbps | 204 × 0,67 × 0,384 | **52,5 Mbps** |
| Videovigilância (cloud) | 28 câmaras | 100% | 2048 kbps | 28 × 2,048 | **57,3 Mbps** |
| Base de dados (remotos) | 12 remotos | 40% | 128 kbps | 12 × 0,4 × 0,128 | **0,61 Mbps** |
| VoIP (cloud) | 30% de 213 = 64 | 20% | 32 kbps | 64 × 0,2 × 0,032 | **0,41 Mbps** |
| Ficheiros (cloud) | 86% de 213 = 184 **+12 remotos** = 196 | 60% | 512 kbps | 196 × 0,6 × 0,512 | **60,2 Mbps** |
| Internet (navegação) | 75% de 213 = 160 | 78% | 512 kbps | 160 × 0,78 × 0,512 | **63,9 Mbps** |
| **Subtotal Internet** | — | — | — | — | **≈235,0 Mbps** |

Observação: Ao separar LAN (servidores internos) de Internet (cloud + remotos), evidencia-se a necessidade de ~235 Mbps de acesso externo para 213 utilizadores presenciais + 12 remotos.
