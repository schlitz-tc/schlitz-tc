# Schlitz-TC

*'cause every hand's a winner*

Ecosistema de **recerca quantitativa i execució algorítmica** per descobrir actius, validar estratègies amb rigor estadístic i operar-les en paper o live — amb un sol fil conductor des del dashboard.

---

## Què és

Schlitz-TC no és un sol programa: és un **conjunt de repositoris** que cobreixen tot el cicle, de l'univers d'inversió fins a l'ordre al mercat.

| Fase | Què fa | Repositori |
|------|--------|------------|
| **Descobriment** | Univers d'actius, filtres de mercat (regió, país, mercat, categoria…) | market-atlas |
| **Qualitat** | Screening i ràtios fonamentals | analisi-fonamental |
| **Validació** | Walk-forward, ML/GA/RL/MC, GO/NO-GO | 3-5-7 ma2TS |
| **Execució** | Edges actius, risc, ordres IBKR | TRWL (motor + control panel) |
| **Operació** | Interfície unificada Research + Live | dashboard |

La idea central: **cap operació sense un edge demostrat**. El que passa el pipeline de recerca acaba a l'Edge Store; el motor TRWL només opera el que hi ha autoritzat.

---

## Arquitectura

```
                    ┌─────────────────────────┐
                    │   Dashboard  (:5174)    │
                    │  Research  │  Live      │
                    └─────┬───────────┬───────┘
                          │           │
                     :8002│           │:8003
                          ▼           ▼
              ┌───────────────┐   ┌──────────────┐
              │  ma2TS API    │   │  TRWL API    │
              │  pipeline ·   │   │  equity ·    │
              │  experiments  │   │  edges · IB  │
              └───┬───────┬───┘   └──────┬─────┘
                  │       │                │ llegeix
                  ▼       ▼                ▼
            market-   analisi-         edge_store.db
            atlas     fonamental            ▲
            :8000     :8001                 │
                                            │ escriu
                                       TRWL-motor
                                            │
                                            ▼
                                      IB Gateway
```

El **dashboard** orquestra la recerca via ma2TS i monitoritza l'execució via TRWL. Els microserveis de dades (market-atlas, analisi-fonamental) no s'exposen directament a la UI: tot passa per l'API del control panel ma2TS.

---

## Etapes

**1. Descobriment — de l'univers al experiment**  
Configures filtres, executes el discovery des del dashboard i obtens experiments pendents de validació — sense córrer codi a mà. Univers → fonamental → registre d'hipòtesis, en tres passos.

**2. Validació — abans d'operar**  
Walk-forward analysis, agents ML/GA/RL/Monte Carlo i decisions GO/NO-GO explícites. No es confon backtest amb evidència: el que arriba a live ha passat criteris mesurables.

**3. Execució — edges, no intuïció**  
L'unitat operativa és l'**edge** (estrategia + actiu + agent validats), no un portafoli genèric. Un compte, diversos edges actius, risc agregat i perfils conservadors (p. ex. regla 3-5-7). Equity curve i **informe de rendiment HTML** (KPIs, drawdown, mètriques per edge) accessibles des del dashboard Live i el TRWL Control Panel.

**4. Operació — una porta d'entrada, dues modes**  
**Research Lab** per descobrir i validar; **Live Trading** per veure equity, snapshot del darrer cicle, informe de rendiment, risc i posicions en temps real. L'operativa completa (ordres, journal, comandes al motor) viu al TRWL Control Panel; el dashboard Live és el resum executiu.

---

## Repositoris

| Repositori | Rol |
|------------|-----|
| [dashboard](https://github.com/schlitz-tc/dashboard) | Hub UI — Research + Live |
| [market-atlas](https://github.com/schlitz-tc/market-atlas) | Univers d'actius (FinanceDatabase) |
| [analisi-fonamental](https://github.com/schlitz-tc/analisi-fonamental) | Screening fonamental |
| [3-5-7 ma2TS](https://github.com/schlitz-tc/3-5-7-ma2TS) | Motor de recerca (ML/GA/RL/MC) |
| [3-5-7 control panel](https://github.com/schlitz-tc/3-5-7-control-panel) | API d'orquestració + consola Streamlit |
| [TRWL-motor](https://github.com/schlitz-tc/TRWL-motor) | Loop d'execució live/paper |
| [TRWL-control-panel](https://github.com/schlitz-tc/TRWL-control-panel) | API i UI operativa TRWL |

> Aquest repositori (`schlitz-tc`) és la **portada de l'ecosistema**: visió, arquitectura i enllaços. El codi viu als repos germans.

---

## Per començar

1. Clona els repositoris que necessitis (com a mínim **dashboard** i **3-5-7 ma2TS**).
2. Arrenca l'ecosistema des de `dashboard/` → `./open-dashboard.sh`
3. Obre http://localhost:5174/research

Documentació operativa completa:

- **Hub ecosistema:** `dashboard/docs/MANUAL_ECOSISTEMA.md` (v2.0 — instal·lació, ports, pipeline, Live TRWL, informe de rendiment)
- **TRWL (cicle, equity, informe):** `dashboard/docs/TRWL FUNCIONAMENT.md`
- **Motor TRWL:** `TRWL/TRWL-motor/TRWL-motor/docs/MANUAL_TRWL.md` (v2.0)
- **Filtres market-atlas (cascades UI):** `market-atlas/market-atlas/docs/FILTRES_UI_ECOSISTEMA.md`
- **Analisi fonamental:** `analisi-fonamental/analisi-fonamental/MANUAL_INDICADORS.md` · FMP: `docs/analisi-fonamental-fmp-fallback.md` · Pipeline: `docs/analisi-fonamental-correccions-pipeline.md`

---

## Avís

Schlitz-TC és una **eina de recerca i enginyeria quantitativa**. No constitueix assessorament financer ni recomanació d'inversió. L'operativa live requereix IB Gateway, capital real i criteri propi.

---

*Schlitz TC · juliol 2026 (hub v2.0 — TRWL Performance Report v2.2)*
