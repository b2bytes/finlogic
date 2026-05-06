# FinLogic

> **Sistema operativo financiero del pueblo de Chile.**
> Justicia financiera con IA regulatoria — CMF, SERNAC, SII, CSIRT — cero alucinación.

[![Demo](https://img.shields.io/badge/demo-finlogic.one-0E7A47?style=flat-square)](https://finlogic.one)
[![Claude](https://img.shields.io/badge/AI-Claude%20Sonnet%204.6-D97757?style=flat-square)](https://www.anthropic.com)
[![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](./LICENSE)
[![Bendi](https://img.shields.io/badge/event-Claude%20Impact%20Lab%20CL%202026-7C3AED?style=flat-square)](https://anthropic.com)

---

## ¿Qué es FinLogic?

Cada año más de **500.000 personas** en Chile presentan un reclamo financiero sin saber qué dice la ley. Un abogado cuesta más de **$200.000**, un reclamo demora **28 días promedio** y los **4 organismos** competentes (CMF, SERNAC, SII, CSIRT) hablan idiomas distintos.

**FinLogic** es el puente: una capa conversacional con IA que traduce el laberinto regulatorio chileno en acciones ejecutables hoy.

### Lo que hace Lya (la asistente IA)

1. **Triage** — clasifica el organismo competente y la urgencia.
2. **Especialista** — genera respuesta empática con cita literal de ley + artículo, fundada en RAG Pinecone.
3. **Verificador** — audita precisión normativa, accionabilidad y ausencia de alucinación.
4. **Documento** — genera la carta legal lista para enviar (SERNAC, CMF, CSIRT, ARCO Ley 21.719).

Cada respuesta tiene un **AgentTrace público** auditable en [/Transparencia](https://finlogic.one/Transparencia).

---

## Arquitectura

```
┌─────────────────────────────────────────────────────────────┐
│                    USUARIO (Web · WhatsApp · Voz)           │
└────────────────────────────┬────────────────────────────────┘
                             ▼
                    ┌──────────────────┐
                    │   LYA · TRIAGE   │ gpt-5-mini · ~600ms
                    │  Clasifica org.  │
                    └────────┬─────────┘
                             ▼
                ┌────────────────────────┐
                │  RAG · Pinecone        │ multilingual-e5-large
                │  34 leyes chilenas     │ 1024d · cosine
                │  finlogic-knowledge    │
                └────────────┬───────────┘
                             ▼
                ┌────────────────────────┐
                │  ESPECIALISTA          │ claude-sonnet-4-6
                │  Cita ley + artículo   │ ~2.1s
                └────────────┬───────────┘
                             ▼
                ┌────────────────────────┐
                │  VERIFICADOR (async)   │ gpt-4o-mini
                │  Score 0-100           │ ~800ms
                └────────────┬───────────┘
                             ▼
                ┌────────────────────────┐
                │  RESPUESTA + DOCUMENTO │
                │  + AgentTrace público  │
                └────────────────────────┘
```

Ver detalle completo en [`docs/ARCHITECTURE.md`](./docs/ARCHITECTURE.md).

---

## Stack

- **Frontend**: React 18 + Vite + TailwindCSS + shadcn/ui + Framer Motion
- **Backend**: Base44 platform · Deno serverless functions
- **IA**: Claude Sonnet 4.6 (especialista) + GPT-5 mini (triage) + GPT-4o mini (verifier)
- **RAG**: Pinecone Serverless · index `finlogic-knowledge` · embedding multilingual-e5-large 1024d
- **Voz**: Whisper STT + ElevenLabs TTS
- **Distribución**: WhatsApp Business API · Widget embed B2G · Compliance API B2B

---

## Capas funcionales (modelo de negocio)

| Capa | Producto | Usuario | Precio |
|------|----------|---------|--------|
| 1 | **FinLogic Free** | Camila / Don Luis | $0 — primera consulta y caso |
| 2 | **FinLogic Pro** | Roberto / María José | $3.990 CLP/mes — casos ilimitados, voz, alertas plazos |
| 3 | **Compliance API B2B** | Fintechs Ley 21.521 | $490.000 CLP/mes base — 10K llamadas + $0,008 USD c/u |
| 4 | **Widget Embed B2G** | SERNAC / municipios / CSIRT | Convenio público |

---

## Cobertura normativa (corpus Pinecone)

- **Ley 20.009** — Fraude tarjetas (5 días hábiles, restitución obligatoria 35 UF)
- **Ley 19.496** — Derechos del consumidor (cláusulas abusivas, garantía 6m, retracto 10d)
- **Ley 20.555** — SERNAC Financiero (CAE/TIR/TER obligatorios)
- **Ley 21.236** — Portabilidad financiera
- **Ley 21.521** + **NCG 502** — Ley Fintech, marco SFA Open Finance (vigencia 4 jul 2026)
- **Ley 21.663** — Ciberseguridad (reporte CSIRT obligatorio)
- **Ley 21.719** — Datos personales, derechos ARCO
- **Ley 21.713** — Reforma tributaria 2024
- **LIR Art. 14 D N°3 y N°8** — Régimen Pro-Pyme
- **Ley 18.010** — Tasa Máxima Convencional (TMC)

Fuentes oficiales: `leychile.cl`, `cmfchile.cl`, `sernac.cl`, `sii.cl`, `csirt.gob.cl`.

---

## El equipo

| Rol | Persona |
|-----|---------|
| Líder · AI Builder | **Gabriel S.** — Orquestación de Lya, pipeline IA, integración Claude |
| Compliance API · Backend | **Diego B2BYTES** — Endpoints, integraciones CMF/SII/CSIRT, infra |
| Producto · Auditoría | **Paula Garcés** — Auditoría de procesos, validación normativa |
| Diseño · UX · Sistema | **Martín Campos** — Design system, marca, accesibilidad WCAG AA |

---

## Estado del proyecto

- ✅ Deploy producción: [finlogic.one](https://finlogic.one)
- ✅ Demo agentic en vivo en [/Consulta](https://finlogic.one/Consulta)
- ✅ AgentTrace público en [/Transparencia](https://finlogic.one/Transparencia)
- ✅ Compliance API documentada en [/api-compliance](https://finlogic.one/api-compliance)
- ✅ Rúbrica auto-validada en [/Rubrica](https://finlogic.one/Rubrica)
- ✅ PitchDeck navegable en [/PitchDeck](https://finlogic.one/PitchDeck)

---

## Claude Impact Lab Chile 2026

Este proyecto compite en el **Claude Impact Lab Chile 2026** (6-7 mayo). Material de evaluación:

- 📄 **Ficha Cívica** — [`docs/FICHA-CIVICA.md`](./docs/FICHA-CIVICA.md)
- 🤖 **Entregable Técnico** — [`docs/ENTREGABLE-TECNICO.md`](./docs/ENTREGABLE-TECNICO.md)
- 🏗 **Arquitectura** — [`docs/ARCHITECTURE.md`](./docs/ARCHITECTURE.md)
- 🧠 **System Prompt principal** — [`prompts/lya-system-prompt.md`](./prompts/lya-system-prompt.md)

---

## Licencia

MIT © 2026 FinLogic Solutions
