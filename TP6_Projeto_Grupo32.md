# TP6_Projeto_Grupo32

## a) Esquema geral da arquitetura lógica
- Uma única LAN cobre todos os edifícios; o **CD/BD/FD** em ZT1 faz a ligação GE do operador e agrega o backbone de campus em FO monomodo.
- Topologia em estrela de campus: ZT1→ZT3 (Showroom), ZT1→ZT4 (Fábrica), ZT1→ZT6 (Armazém). Cada ligação usa duas fibras (ativo/backup) de um cabo SM de 12F.
- Backbone de edifício em estrela simples: ZT1↔ZT2, ZT4↔ZT5, ZT6↔ZT7. Todos os FD terminam cablagem horizontal de cobre.
- VLANs separadas para dados, voz, Wi‑Fi e videovigilância; roteamento e política central em ZT1 (stack L3).

Diagrama textual (campus):

```
 [Showroom ZT3]
        |
        | FO SM 12F ≈110 m
        |
[Fáb. ZT5]--FO 50 m--[Fáb. ZT4]--FO 160 m--[CD/BD/FD ZT1]--FO 220 m--[BD/FD ZT6]--FO 65 m--[FD ZT7]
```

## b) Dimensionamento e escolha de equipamentos passivos em cada distribuidor
- **Cabos horizontais**: Cat6A F/UTP (S/FTP na fábrica). Chicotes de 2 m (TO) e 1 m (bastidor) para cada posto.
- **Cabos de backbone**: FO monomodo, 12 fibras, com terminação LC/UPC em painéis de emenda; permite 1/10/40G.
- **Patch panels**: 24/48p Cat6A blindados, com guias de cabo horizontais e verticais.
- **Bastidores**: 42U em ZT1, 27U nas restantes ZT; PDU dupla e ventilação forçada.
- **UPS**: linha interativa/on-line dimensionada para ~30 min do consumo de cada bastidor (mín. 2 kVA em ZT1, 1 kVA nos demais).
- **Identificação de tomadas**: etiqueta **[Edifício]-[ZT]-R[rack]-PP[patch]-T[nº]** (ex.: `ESC-ZT1-R1-PP02-T15`). Numeração sequencial por bastidor: Tomada 1 Bastidor 1, Tomada 2 Bastidor 1, etc., refletida no patch panel.

## c) Dimensionamento e escolha dos equipamentos ativos em cada distribuidor
- **Switches acesso/PoE** conforme tabela do planeamento (POE+ para AP/câmaras). Stack L3 em ZT1 para routing, VRRP/HSRP e ACLs.
- **Router de operadora** em ZT1 (porta RJ45 GE) ligado a um par de switch L3 em uplink redundante.
- **Servidores on-prem** (ZT1): base de dados (2×GE, RAID/NAS local) e servidor de impressão/spool; restantes serviços em cloud.
- **Wi‑Fi**: AP Wi‑Fi6 PoE+ com controlador na cloud ou virtual no stack L3. SSID corporativo e convidado com VLANs separadas; zonas de cobertura seguem o mesmo critério do edifício de Escritórios (um AP por 150 m², sem reutilização entre edifícios/pisos).
- **Videovigilância**: câmaras PoE 1080p H.264 ligadas às portas de acesso; gravação no serviço cloud (conforme T+G=5).

## d) Esquema de identificação dos distribuidores
- **CD/BD/FD-ZT1**: código "CD-ESCR-ZT1".
- **FD-ZT2**, **BD/FD-ZT3**, **BD/FD-ZT4**, **FD-ZT5**, **BD/FD-ZT6**, **FD-ZT7**.
- Rotulagem de fibras: campus-[origem]-[destino]-[par]; cobre: [FD]-[rack]-[patch]-[porta].

## e) Desenho do esquema de colocação dos equipamentos no bastidor (exemplo ZT1)
1. PDU dupla com proteção; UPS 2 kVA.
2. Patch panel FO 12F (campus) + bandeja de emenda.
3. Patch panel FO 12F (ZT2) e reservas.
4. Patch panels Cat6A (≥4×48p) para tomadas e uplinks internos.
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

## h) Orçamento detalhado (valores indicativos de mercado)
| Item | Quantidade | Custo unitário (€) | Total (€) | Fonte típica |
| --- | --- | --- | --- | --- |
| **Cablagem e acessórios de cobre** |  |  |  |  |
| Cabo Cat6A F/UTP (Escritórios, Showroom, Armazém) | 12.160 m | 0,35 | 4.256 | chip7.pt |
| Cabo Cat6A S/FTP (Fábrica, proteção EMI) | 2.560 m | 0,50 | 1.280 | castroelectronica.pt |
| Tomadas RJ45 Cat6A + keystone + faceplate dupla | 460 | 8,50 | 3.910 | chip7.pt |
| Patch cords 2 m (postos) | 460 | 4,00 | 1.840 | chip7.pt |
| Patch cords 1 m (bastidor) | 460 | 3,00 | 1.380 | chip7.pt |
| Patch panels 48p Cat6A blindados | 10 | 95 | 950 | chip7.pt |
| Patch panels 24p Cat6A blindados | 6 | 60 | 360 | chip7.pt |
| Guias/organizadores horizontais/verticais | 18 | 20 | 360 | chip7.pt |
| Calha técnica / eletrocalha (cobre) | 1.000 m | 6,50 | 6.500 | lidertronica.com |
| **Fibra ótica e FO passive** |  |  |  |  |
| FO SM 12F (campus + edifícios) | 760 m | 0,60 | 456 | aquario.pt |
| Bandejas de emenda + cassete LC | 10 | 55 | 550 | aquario.pt |
| Conetores/pigtails LC/UPC | 60 | 6 | 360 | aquario.pt |
| Chicotes FO LC-LC 2 m | 40 | 9 | 360 | chip7.pt |
| SFP+ 10G SM (campus, 6 pares) | 12 | 120 | 1.440 | aquario.pt |
| SFP 1G SM (backbone edifícios, 3 pares) | 6 | 45 | 270 | chip7.pt |
| **Bastidores e UPS** |  |  |  |  |
| Bastidor 42U c/ PDU dupla (ZT1) | 1 | 650 | 650 | castroelectronica.pt |
| Bastidor 27U c/ PDU | 6 | 420 | 2.520 | castroelectronica.pt |
| Ventoinhas + kits de fixação | 7 | 70 | 490 | castroelectronica.pt |
| UPS on-line 2 kVA (ZT1) | 1 | 650 | 650 | chip7.pt |
| UPS on-line 1 kVA (restantes) | 6 | 320 | 1.920 | chip7.pt |
| **Equipamento ativo** |  |  |  |  |
| Switch 48p PoE+ L3 (core/stack) | 5 | 1.350 | 6.750 | aquario.pt |
| Switch 24/48p PoE+ acesso | 5 | 750 | 3.750 | aquario.pt |
| Controladora Wi‑Fi (virtual/appliance) | 1 | 900 | 900 | aquario.pt |
| AP Wi‑Fi6 PoE+ | 26 | 180 | 4.680 | chip7.pt |
| Câmaras IP PoE 1080p (28 + 10% spare) | 31 | 120 | 3.720 | aquario.pt |
| NVR/Storage cloud gateway (apoio videovigilância) | 1 | 1.000 | 1.000 | aquario.pt |
| Servidor BD (on-prem) | 1 | 2.000 | 2.000 | chip7.pt |
| Servidor impressão/spool | 1 | 1.200 | 1.200 | chip7.pt |
| Firewall/UTM NGFW c/ 1G WAN | 1 | 1.500 | 1.500 | aquario.pt |
| Licenças SW (firewall + Wi‑Fi + NVR) | 1 | 1.200 | 1.200 | aquario.pt |
| **Serviços e instalação** |  |  |  |  |
| Mão-de-obra instalação cobre (≈18.800€ @1,1€/m + terminações) | — | — | 18.800 | — |
| Mão-de-obra instalação FO (lançamento + fusões) | — | — | 4.000 | — |
| Testes e certificação (cobre + FO) | — | — | 2.500 | — |
| Etiquetagem e documentação (impressoras, etiquetas, software) | — | — | 800 | — |
| Contingência técnica 10% | — | — | 7.730 | — |
| **Total estimado** | — | — | **85.076 €** | — |

Notas:
- Valores indicativos recolhidos de lojas de retalho/online; ajustar com cotações oficiais e volumes.
- Contingência cobre variações cambiais, cabos adicionais para rotas alternativas e imprevistos de obra.
- A mão-de-obra foi estimada para um projeto real (lançamento, terminação, certificação e etiquetagem completos).
