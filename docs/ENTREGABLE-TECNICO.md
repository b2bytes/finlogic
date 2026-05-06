# Entregable Técnico · Bendi · Claude Impact Lab Chile 2026

## System prompt principal · Lya (agente orquestador)

Ver archivo completo: [`prompts/lya-system-prompt.md`](../prompts/lya-system-prompt.md).

## Demo en vivo

- **URL**: https://finlogic.one
- **Demo conversacional**: https://finlogic.one/Consulta
- **AgentTrace público**: https://finlogic.one/Transparencia
- **PitchDeck navegable**: https://finlogic.one/PitchDeck
- **Rúbrica auto-validada**: https://finlogic.one/Rubrica

## Herramientas Anthropic usadas

| Herramienta | Usada | Justificación |
|-------------|:-----:|---------------|
| **Prompt Caching** | ✅ | System prompts > 2K tokens (Ley 21.521 + NCG 502 + jurisprudencia). Reduce latencia ~40% y costo. |
| **Citations** | ✅ | Cada respuesta de Lya cita artículo + ley + sourceUrl recuperados desde Pinecone RAG. AgentTrace público. |
| **Extended Thinking** | ✅ | Capa 3 verificador audita precisión normativa, accionabilidad y detecta alucinaciones. |
| Files API | ❌ | Corpus normativo vive en Pinecone Serverless (mejor para semantic search multi-organismo). |
| MCP | ❌ | Roadmap Q3 2026 — exponer FinLogic como MCP server para Claude Desktop. |
| Agent SDK | ❌ | Arquitectura propia con orquestación InvokeLLM + Pinecone RAG inline. Equivalente funcional en producción. |
| Computer Use | ❌ | No aplica — Lya es asistente conversacional, no automatizador de UI. |

## Repo público

https://github.com/finlogic-cl/finlogic

## Equipo

- **Gabriel S.** — Líder · AI Builder
- **Diego B2BYTES** — Compliance API · Backend
- **Paula Garcés** — Producto · Auditoría
- **Martín Campos** — Diseño · UX · Sistema
