# Arquitectura FinLogic

## Visión

FinLogic implementa un **pipeline agéntico de 3 capas** sobre un corpus normativo chileno indexado en Pinecone, con AgentTrace público en cada respuesta para garantizar transparencia radical y cero alucinación.

---

## Capa 1 · TRIAGE

- **Modelo**: `gpt-5-mini` (rápido, barato, bueno clasificando)
- **Latencia objetivo**: < 600ms
- **Output**: JSON estructurado con
  - `regulatoryBody`: CMF | SERNAC | SII | CSIRT | BCN | FOGAPE | SERCOTEC | multiple
  - `urgencyLevel`: critical | high | medium | low | resolved
  - `detectedProfile`: camila | don_luis | maria_jose | roberto | general
  - `category`: fraude_digital | cobro_indebido | derechos_arco | contrato_abusivo | normativa_consulta
- **Función**: `functions/processConsultation.js`

## Capa 2 · ESPECIALISTA + RAG

- **Modelo**: `claude-sonnet-4-6`
- **Latencia objetivo**: ~2.1s
- **RAG**: Pinecone Serverless
  - Index: `finlogic-knowledge`
  - Namespace: `finlogic-prod`
  - Embedding: `multilingual-e5-large` (1024d)
  - Métrica: cosine similarity
  - Mínimo score: 0.25
- **Output**: respuesta empática estructurada
  - `fact`: hechos relevantes detectados
  - `translation`: derecho del usuario en lenguaje simple
  - `action`: 1-3 pasos numerados ejecutables hoy
  - `lawsCited`: array de leyes verificadas en RAG
  - `legalDeadlineDays`: plazo en días hábiles si aplica
- **Funciones**: `functions/lyaKnowledgeQuery.js`, `functions/vectorSearch.js`

## Capa 3 · VERIFICADOR

- **Modelo**: `gpt-4o-mini` (async post-respuesta)
- **Latencia**: ~800ms (no bloquea al usuario)
- **Score 0-100** ponderado:
  - Precisión normativa × 0.4
  - Accionabilidad × 0.3
  - Claridad × 0.2
  - Ausencia de alucinación × 0.1
- **Acciones**: registra en `AgentTrace` (entity), genera `OperationalAlert` si score < 60.

---

## Reglas anti-alucinación

✗ Nunca inventar leyes, artículos, montos UF/UTM, plazos.
✗ Nunca citar normativa que no esté en el contexto RAG.
✗ Nunca recomendar instituciones financieras específicas.
✗ Nunca minimizar el problema del usuario.
✓ Siempre citar ley + artículo (ej: "Ley 20.009 Art. 5°", no solo "Ley 20.009").
✓ Siempre indicar plazo legal en días hábiles + consecuencia del vencimiento.
✓ Siempre cerrar con UNA acción concreta ejecutable.

---

## Entities (database)

| Entity | Propósito |
|--------|-----------|
| `AgentTrace` | Auditoría completa de cada consulta — público en /Transparencia |
| `MisCasos` | Caso ciudadano con seguimiento normativo y plazos |
| `GeneratedDocument` | Cartas legales generadas (ARCO, SERNAC, CMF, CSIRT) |
| `LegalDeadline` | Plazos legales con alertas 7d / 3d / 1d |
| `KnowledgeChunk` | Backup vectorial nativo del corpus |
| `UserProfile` | Perfil ciudadano (4 arquetipos + accesibilidad) |
| `PymeProfile` | Perfil pyme con scoring de salud financiera |
| `Subscription` | Suscripción Free / Pro / Compliance API |
| `ComplianceAPIKey` | API keys B2B con rate limiting |

---

## Compliance API B2B

5 endpoints REST orientados a fintechs reguladas Ley 21.521:

| Endpoint | Función |
|----------|---------|
| `POST /check-tmc` | Validación de Tasa Máxima Convencional (Ley 18.010) |
| `POST /verify-entity` | Verifica si una entidad está registrada en CMF (Ley 21.521) |
| `POST /regulatory-impact` | Análisis de impacto regulatorio NCG 502 + 12 módulos |
| `POST /fraud-pattern-match` | Detección patrones fraude (Ley 20.009 + 21.663) |
| `POST /consumer-rights-check` | Validación derechos consumidor (Ley 19.496 + 20.555) |

Función: `functions/lyaComplianceAPIEndpoint.js` · Plan base $490.000 CLP/mes.

---

## Automatizaciones (CRONs)

| Job | Frecuencia | Función |
|-----|------------|---------|
| Check legal deadlines | 8AM diario | `checkLegalDeadlines` |
| Monitor integrations | 15 min | `monitorIntegrations` |
| Detect score anomaly | Horario | `detectScoreAnomaly` |
| Aggregate weekly feedback | Lunes 9AM | `aggregateWeeklyFeedback` |
| Calculate MRR | Medianoche | `calculateMRR` |
| Free → Pro nurturing | Entity trigger | `nurturingFreeToProTrigger` |
| Auto-resolve FAQ tickets | On create | `autoResolveFAQ` |
| Triage support tickets | On create | `triageTicket` |

---

## Privacidad y compliance

- **Ley 21.719** (datos personales) cumplida por diseño:
  - RUT solo se almacena con hash SHA-256, nunca en texto plano.
  - Derecho al olvido: endpoint de eliminación total de datos del usuario.
  - RLS por usuario en todas las entities con `created_by`.
  - Encriptación TLS 1.3 en transito, AES-256 en reposo.
- **Ley 21.663** (ciberseguridad): logs de auditoría, monitoreo de anomalías.
- **AgentTrace público** sin PII: queries y respuestas anonimizadas.
