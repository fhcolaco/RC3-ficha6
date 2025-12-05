# TP6_Planeamento_Grupo32

## Premissas usadas
- Turno **T=3**, Grupo **G=2** (logo T+G=5 e ligação ao exterior em ZT1 do Edifício de Escritórios).
- Sem acesso às plantas originais, foram estimadas áreas úteis para aplicar a densidade de tomadas: Escritórios 800 m², Showroom 400 m², Fábrica 1600 m² e Armazém 900 m². Todas as estimativas estão destacadas e podem ser ajustadas se existirem valores reais.
- Densidade base: 2 tomadas RJ45 por cada 10 m². Ajustes indicados no enunciado: Armazém = 1/3 da densidade base; Showroom = dobro; Fábrica = 1/4 da densidade base. As zonas técnicas e corredores não têm tomadas.
- Cada edifício tem 7 câmaras (4 exteriores + 3 interiores) e pontos de acesso Wi‑Fi dimensionados a 1 AP/150 m² (arredondado por excesso). AP e câmaras usam as tomadas já contabilizadas.
- Distâncias conhecidas: ZT1↔ZT2 = 45 m; ZT4↔ZT5 = 50 m; ZT6↔ZT7 = 65 m. Restantes ligações de campus foram estimadas a partir do esquema (ZT1↔ZT3 ≈ 110 m; ZT1↔ZT4 ≈ 160 m; ZT1↔ZT6 ≈ 220 m) para permitir o planeamento do backbone.
- Comprimento médio da cablagem horizontal: 60 − 10·T + G = **32 m**.

## a) Quantidade de tomadas por edifício
| Edifício | Área estimada | Densidade aplicada | Tomadas calculadas |
| --- | --- | --- | --- |
| Escritórios | 800 m² | Base | 160 |
| Showroom | 400 m² | 2× | 160 |
| Fábrica | 1600 m² | 1/4× | 80 |
| Armazém | 900 m² | 1/3× | 60 |
| **Total** | — | — | **460** |

Total de utilizadores = metade das tomadas = **230**. Colaboradores remotos = **12** (10+G).

### Sistema de identificação de tomadas
- Padrão de etiqueta: **[Edifício]-[ZT]-R[rack]-PP[patch]-T[nº tomada]** (ex.: `ESC-ZT1-R1-PP02-T15`).
- As tomadas de área de trabalho são numeradas sequencialmente por bastidor (Tomada 1 Bastidor 1, Tomada 2 Bastidor 1, ...); o mesmo número é replicado no patch panel correspondente.
- Fibra e cobre seguem a mesma lógica, com cores distintas para dados/voz/videovigilância/Wi‑Fi.

## b) Diagrama de rede e localização dos distribuidores
- **ZT1 (Edifício Escritórios)**: função **CD/BD/FD**, ligação à operadora e cobertura principal do piso.
- **ZT2 (Escritórios)**: **FD** auxiliar ligado a ZT1 por FO (45 m) para equilibrar distâncias de tomadas.
- **ZT3 (Showroom)**: **BD/FD** ligado ao CD por FO (≈110 m).
- **ZT4 (Fábrica)**: **BD/FD** ligado ao CD por FO (≈160 m); cobre metade da fábrica.
- **ZT5 (Fábrica)**: **FD** ligado a ZT4 por FO (50 m) para cobrir a segunda metade.
- **ZT6 (Armazém)**: **BD/FD** ligado ao CD por FO (≈220 m); cobre a zona principal.
- **ZT7 (Armazém)**: **FD** ligado a ZT6 por FO (65 m) para cobrir a zona remota.

Diagrama textual (campus estrela):

```
          [Showroom ZT3]
                 |
                 | FO SM 12F ≈110 m
                 |
[Fábrica ZT5]--FO--[Fábrica ZT4]--FO--[CD/BD/FD ZT1]--FO--[BD/FD ZT6]--FO--[FD ZT7]
                         |                                     
                         | FO SM 50 m                          | FO SM 65 m
                         |                                     |
                    (FD no edifício)                     (FD no edifício)
```

Para Wi‑Fi (exercício b), assumem-se as mesmas zonas de cobertura do edifício de Escritórios: AP colocados de forma homogénea por cada 150 m², sem reutilização entre edifícios ou pisos.

## c) Cablagem por subsistema
### Horizontal
| Edifício | Tipo de cabo | Tomadas | Comprimento médio | Comprimento total |
| --- | --- | --- | --- | --- |
| Escritórios | Cat6A F/UTP | 160 | 32 m | 5120 m |
| Showroom | Cat6A F/UTP | 160 | 32 m | 5120 m |
| Fábrica | Cat6A **S/FTP** (EMI moderadas) | 80 | 32 m | 2560 m |
| Armazém | Cat6A F/UTP | 60 | 32 m | 1920 m |

### Backbone de edifício (entre FD do mesmo edifício)
| Ligação | Distância | Meio | Observações |
| --- | --- | --- | --- |
| ZT1 ↔ ZT2 | 45 m | FO SM 12F | Permite 10G/40G e redundância lógica. |
| ZT4 ↔ ZT5 | 50 m | FO SM 12F | Segmentação da fábrica. |
| ZT6 ↔ ZT7 | 65 m | FO SM 12F | Cobertura integral do armazém. |

### Backbone de campus
| Origem (CD) | Destino (BD) | Distância estimada | Meio | Observações |
| --- | --- | --- | --- | --- |
| ZT1 | ZT3 | 110 m | FO SM 12F | Showroom. |
| ZT1 | ZT4 | 160 m | FO SM 12F | Fábrica. |
| ZT1 | ZT6 | 220 m | FO SM 12F | Armazém. |

## d) Dimensionamento de ativos por distribuidor
Número de portas = utilizadores locais + câmaras + AP + servidores/roteador + 20% de margem.

| Distribuidor | Base de cálculo | Portas (≥) | Proposta |
| --- | --- | --- | --- |
| ZT1 (CD/BD/FD) | 50 utilizadores + 7 câmaras + 6 AP + 1 router + 2 servidores | 78 | 2×48p POE+ (stack) |
| ZT2 (FD) | 30 utilizadores + 0 servidores | 36 | 1×48p POE+ |
| ZT3 (BD/FD) | 80 utilizadores + 7 câmaras + 3 AP | 108 | 2×48p POE+ + 1×24p POE+ |
| ZT4 (BD/FD) | 24 utilizadores + 4 câmaras + 6 AP | 42 | 1×48p POE+ |
| ZT5 (FD) | 16 utilizadores + 3 câmaras + 5 AP | 30 | 1×24p POE+ |
| ZT6 (BD/FD) | 18 utilizadores + 3 câmaras + 4 AP | 30 | 1×24p POE+ |
| ZT7 (FD) | 12 utilizadores + 4 câmaras + 2 AP | 22 | 1×24p POE+ |

Todos os distribuidores incluem UPS dimensionada para pelo menos 30 minutos.

## e) Cálculo de fluxos de débito (base em 230 utilizadores + 12 remotos)
| Aplicação | Utilizadores aplicacionais | Simult. | DSN | Tráfego agregado |
| --- | --- | --- | --- | --- |
| E-mail | 207 (90%) | 67% | 384 kbps | 53.26 Mbps |
| Videovigilância | 28 câmaras | 100% | 2048 kbps | 57.34 Mbps |
| Base de dados | 198 (81% + remotos) | 40% | 128 kbps | 10.11 Mbps |
| VoIP | 69 (30%) | 20% | 32 kbps | 0.45 Mbps |
| Impressão | 230 | 7% | 2048 kbps | 34.82 Mbps |
| Ficheiros | 210 (86% + remotos) | 60% | 512 kbps | 64.51 Mbps |
| **Subtotal LAN** | — | — | — | **≈220.5 Mbps** |
| Internet | 173 (75%) | 78% | 512 kbps | **69.12 Mbps** |

Servidor/localização (T+G=5): E-mail/VoIP/Ficheiros/Videovigilância em **Cloud**; Base de dados e Impressão em **ZT1 (CD)**.
