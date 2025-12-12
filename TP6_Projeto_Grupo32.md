# TP6_Projeto_Grupo32

## a) Esquema geral da arquitetura lógica
- Uma única LAN cobre todos os edifícios; o **CD/BD/FD** fica em **ZT5 (Fábrica)** com a ligação GE do operador e agrega o backbone de campus em FO monomodo.
- Topologia em estrela de campus: ZT5→ZT3 (Showroom, 300 m), ZT5→ZT1 (Escritórios, 70 m), ZT5→ZT6 (Armazém, 100 m). Cada ligação usa duas fibras (ativo/backup) de um cabo SM de 12F; ZT1↔ZT2, ZT4↔ZT5 e ZT6↔ZT7 usam FO SM 12F intra-edifício.
- Backbone de edifício em estrela simples: ZT1↔ZT2, ZT4↔ZT5, ZT6↔ZT7. Todos os FD terminam cablagem horizontal de cobre.
- VLANs separadas para dados, voz, Wi‑Fi e videovigilância; roteamento e política central em ZT5 (stack L3).

Diagrama textual (campus):

```
[Showroom ZT3]
       |
       | FO SM 12F 300 m
       |
[FD ZT2]--FO 45 m--[BD/FD ZT1]--FO SM 12F 70 m--[CD/BD/FD ZT5]--FO SM 12F 100 m--[BD/FD ZT6]--FO 65 m--[FD ZT7]
                                  \
                                   \__ FO SM 12F 50 m __ [FD ZT4]
```

## b) Dimensionamento e escolha de equipamentos passivos em cada distribuidor
- **Cabos horizontais**: Cat6A F/UTP (S/FTP na fábrica). Chicotes de 2 m (TO) e 1 m (bastidor) para cada posto.
- **Cabos de backbone**: FO monomodo, 12 fibras, com terminação LC/UPC em painéis de emenda; permite 1/10/40G.
- **Patch panels**: 24/48p Cat6A blindados, com guias de cabo horizontais e verticais.
- **Bastidores**: 42U em ZT1, 27U nas restantes ZT; PDU dupla e ventilação forçada.
- **UPS**: linha interativa/on-line dimensionada para ~30 min do consumo de cada bastidor (mín. 2 kVA em ZT1, 1 kVA nos demais).
- **Identificação de tomadas**: etiqueta **[Edifício]-[ZT]-R[rack]-PP[patch]-T[nº]** (ex.: `ESC-ZT1-R1-PP02-T15`). Numeração sequencial por bastidor: Tomada 1 Bastidor 1, Tomada 2 Bastidor 1, etc., refletida no patch panel.

## c) Dimensionamento e escolha dos equipamentos ativos em cada distribuidor
- **Switches acesso/PoE** conforme tabela do planeamento (PoE+ para AP/câmaras) com **9×48p PoE+** (ZT1×3, ZT2×1, ZT3×3, ZT4×1, ZT5×1) e **2×24p PoE+** (ZT6, ZT7). Stack L3 no CD em ZT5 para routing, VRRP/HSRP e ACLs.
- **Router de operadora** em ZT5 (porta RJ45 GE) ligado a um par de switch L3 em uplink redundante.
- **Servidores on-prem** (ZT5): base de dados (2×GE, RAID/NAS local) e servidor de impressão/spool; restantes serviços em cloud.
- **Wi‑Fi**: AP Wi‑Fi6 PoE+ com controlador na cloud ou virtual no stack L3. SSID corporativo e convidado com VLANs separadas; zonas de cobertura seguem o mesmo critério do edifício de Escritórios (um AP por 150 m², sem reutilização entre edifícios/pisos).
- **Videovigilância**: câmaras PoE 1080p H.264 ligadas às portas de acesso; gravação no serviço cloud (conforme T+G=5).

## d) Esquema de identificação dos distribuidores
- **CD/BD/FD-ZT5**: código "CD-FAB-ZT5".
- **BD/FD-ZT1**, **FD-ZT2**, **BD/FD-ZT3**, **FD-ZT4**, **BD/FD-ZT6**, **FD-ZT7**.
- Rotulagem de fibras: campus-[origem]-[destino]-[par]; cobre: [FD]-[rack]-[patch]-[porta].

## e) Desenho do esquema de colocação dos equipamentos no bastidor (exemplo CD em ZT5)
1. PDU dupla com proteção; UPS 2 kVA.
2. Patch panel FO 12F (campus) + bandeja de emenda.
3. Patch panel FO 12F (ZT4) e reservas.
4. Patch panels Cat6A (≥3×48p) para tomadas e uplinks internos.
5. 2×Switch 48p POE+ empilhados (dados/voz/AP/câmaras).
6. Router do operador e firewall/core L3.
7. Servidor BD e servidor de impressão.
8. Organização de cabos com guias horizontais/verticais.

Esquema textual (vista frontal):

```
42U ZT1
U42-38: Ventoinhas / espaço reserva
U37: PDU
U36: UPS 2 kVA
U35: Patch FO (campus)
U34: Patch FO (ZT2)
U33-30: Patch Cat6A (tomadas)
U29-26: Patch Cat6A (uplinks/AP/câmaras)
U25-18: Switch 48p PoE+ (stack)
U17-15: Firewall / Router operador
U14-11: Servidor BD + Servidor impressão
U10-1: Guias verticais/horizontais
```

## f) Traçados da cablagem e localização das tomadas/bastidor (Armazém)
- FD principal em **ZT6**; FD secundário em **ZT7** (65 m de FO SM). Calhas aéreas ao longo do corredor principal; descidas protegidas para as zonas operacionais.
- Tomadas distribuídas a ~32 m do FD mais próximo, priorizando pontos próximos de portas/cais de carga. 40% das tomadas partem de ZT7 para cobrir a área remota.
- Identificação de caminhos principais, fixação a teto e separação de circuitos de energia.

## g) Serviço adicional para grupos pares – Partilha de Ficheiros
- Uso de **serviço cloud** (T+G=5) com NAS virtual ou Object Storage; sincronização via SMB/NFS exposto por appliance virtual em ZT1 para caching.
- ACL por grupos, quotas por equipa e snapshots horários. Autenticação integrada com AD/LDAP.

## h) Orçamento detalhado com preços pesquisados
| Item | Quantidade | Custo unitário (€) | Total (€) | Fonte (URL consultada) |
| --- | --- | --- | --- | --- |
| **Cablagem e acessórios de cobre** |  |  |  |  |
| Cabo Cat6A F/UTP (Escritórios, Showroom, Armazém) | 13.216 m | 0,35 | 4.626 | https://www.chip7.pt/
| Cabo Cat6A S/FTP (Fábrica, proteção EMI) | 2.208 m | 0,50 | 1.104 | https://www.castroelectronica.pt/
| Tomadas RJ45 Cat6A + keystone + faceplate dupla | 482 | 8,50 | 4.097 | https://www.chip7.pt/
| Patch cords 2 m (postos) | 482 | 4,00 | 1.928 | https://www.chip7.pt/
| Patch cords 1 m (bastidor) | 482 | 3,00 | 1.446 | https://www.chip7.pt/
| Patch panels 48p Cat6A blindados | 11 | 95 | 1.045 | https://www.chip7.pt/
| Patch panels 24p Cat6A blindados | 6 | 60 | 360 | https://www.chip7.pt/
| Guias/organizadores horizontais/verticais | 18 | 20 | 360 | https://www.chip7.pt/
| Calha técnica / eletrocalha (cobre) | 1.100 m | 6,50 | 7.150 | http://lidertronica.com/
| **Fibra ótica e FO passive** |  |  |  |  |
| FO SM 12F (campus + edifícios) | 950 m | 0,60 | 570 | https://www.aquario.pt/
| Bandejas de emenda + cassete LC | 10 | 55 | 550 | https://www.aquario.pt/
| Conetores/pigtails LC/UPC | 60 | 6 | 360 | https://www.aquario.pt/
| Chicotes FO LC-LC 2 m | 40 | 9 | 360 | https://www.chip7.pt/
| SFP+ 10G SM (campus, 6 pares) | 12 | 120 | 1.440 | https://www.aquario.pt/
| SFP 1G SM (backbone edifícios, 3 pares) | 6 | 45 | 270 | https://www.chip7.pt/
| **Bastidores e UPS** |  |  |  |  |
| Bastidor 42U c/ PDU dupla (ZT1) | 1 | 650 | 650 | https://www.castroelectronica.pt/
| Bastidor 27U c/ PDU | 6 | 420 | 2.520 | https://www.castroelectronica.pt/
| Ventoinhas + kits de fixação | 7 | 70 | 490 | https://www.castroelectronica.pt/
| UPS on-line 2 kVA (ZT1) | 1 | 650 | 650 | https://www.chip7.pt/
| UPS on-line 1 kVA (restantes) | 6 | 320 | 1.920 | https://www.chip7.pt/
| **Equipamento ativo** |  |  |  |  |
| Switch 48p PoE+ (L3 nos nós core) | 9 | 1.350 | 12.150 | https://www.aquario.pt/
| Switch 24p PoE+ acesso | 2 | 750 | 1.500 | https://www.aquario.pt/
| Controladora Wi‑Fi (virtual/appliance) | 1 | 900 | 900 | https://www.aquario.pt/
| AP Wi‑Fi6 PoE+ | 25 | 180 | 4.500 | https://www.chip7.pt/
| Câmaras IP PoE 1080p (28 + 10% spare) | 31 | 120 | 3.720 | https://www.aquario.pt/
| NVR/Storage cloud gateway (apoio videovigilância) | 1 | 1.000 | 1.000 | https://www.aquario.pt/
| Servidor BD (on-prem) | 1 | 2.000 | 2.000 | https://www.chip7.pt/
| Servidor impressão/spool | 1 | 1.200 | 1.200 | https://www.chip7.pt/
| Firewall/UTM NGFW c/ 1G WAN | 1 | 1.500 | 1.500 | https://www.aquario.pt/
| Licenças SW (firewall + Wi‑Fi + NVR) | 1 | 1.200 | 1.200 | https://www.aquario.pt/
| **Serviços e instalação** |  |  |  |  |
| Mão-de-obra instalação cobre (≈18.800€ @1,1€/m + terminações) | — | — | 18.800 | — |
| Mão-de-obra instalação FO (lançamento + fusões) | — | — | 4.000 | — |
| Testes e certificação (cobre + FO) | — | — | 2.500 | — |
| Etiquetagem e documentação (impressoras, etiquetas, software) | — | — | 800 | — |
| Contingência técnica 10% | — | — | 8.767 | — |
| **Total estimado** | — | — | **≈96.400 €** | — |

Notas:
- Preços pesquisados nas lojas indicadas (URLs na tabela) em valores de retalho à data da elaboração; substitua por cotações formais para compra em volume.
- Contingência cobre variações cambiais, cabos adicionais para rotas alternativas e imprevistos de obra.
- A mão-de-obra está dimensionada para um projeto real: lançamento, terminação, certificação, etiquetagem completa e coordenação em obra.
