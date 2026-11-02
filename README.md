# REPROChat

Asistente conversacional basado en **RAG (Retrieval-Augmented Generation)** para consultar la normativa argentina sobre cannabis medicinal y el programa **REPROCANN**:

- **Ley 27.350** (Uso Medicinal de la Planta de Cannabis y sus derivados)
- **Decreto Reglamentario 883/2020**
- **Resolución 800/2021** (Programa REPROCANN) y sus modificatorias (**Resolución 766/2023**, **Resolución 3132/2024** y **Resolución 1780/2025**)

---

## Características de la Interfaz

- **Asistente Conversacional RAG:** Respuestas precisas sustentadas directamente en fragmentos del corpus legal oficial, evitando invenciones o alucinaciones.
- **Soporte Light / Dark Mode:** Conmutador de tema con detección de preferencias del sistema y persistencia en `localStorage`.
- **Consulta de Fuentes:** Popover anclado con enlaces directos a los textos oficiales de las leyes y resoluciones en Argentina.gob.ar.
- **Modal Explicativo "¿QUÉ ES REPROCHAT?":**
  - Componente modal accesible con fondo desenfocado (*backdrop blur*) y estética de bordes rectos.
  - Diseño responsive: composición horizontal en desktop (imagen `iso-edit.png` + texto) y vertical apilada en mobile.
  - Explicación del funcionamiento de la arquitectura RAG, alcance del corpus normativo, aviso de descargo de responsabilidad legal y botón CTA de contacto a Cooperativa Origen.

---

## Arquitectura

- **Frontend:** HTML5 semántico, CSS3 modular (`global.css`) y JavaScript vanilla (`index.html`). Sin dependencias de compilación ni frameworks pesados, listo para desplegar en cualquier servicio de hosting estático (GitHub Pages, Cloudflare Pages, Vercel, Netlify, etc.).
- **Base de datos:** PostgreSQL con extensión `pgvector` en **InsForge** (tabla `documents` con vectores de 1536 dimensiones, índice HNSW y función RPC `match_documents`).
- **Edge Functions (Deno / InsForge):**
  - `functions/ingest.ts`: Divide los documentos normativos en fragmentos (*chunks*), genera embeddings y los persiste en la base de datos vectorial.
  - `functions/ask.ts`: Recibe las consultas de los usuarios, genera el embedding vectorial correspondiente, recupera por similitud coseno los fragmentos normativos más relevantes y genera la respuesta fundamentada.

---

## Estructura del proyecto

```text
├── index.html          # Interfaz principal, chat, modal explicativo y cliente HTTP
├── global.css          # Sistema de diseño, tokens de color, componentes y responsive
├── iso-edit.png        # Composición visual sobre el isologo para la card explicativa
├── isologo.png         # Isologo oficial de REPROChat
├── logo.svg            # Isologotipo de RED.origen en el encabezado
├── fav-icon.svg        # Favicon del sitio e ícono de precarga
├── og-image.png        # Imagen Open Graph para compartir en redes sociales y mensajería
├── functions/
│   ├── ask.ts          # Edge Function de consulta y generación RAG
│   └── ingest.ts       # Edge Function de ingesta y vectorización
└── migrations/
    └── 20261101171837_setup-rag-documents.sql  # Esquema pgvector, políticas RLS y RPC match_documents
```

---

## Variables de entorno (Backend / Edge Functions)

Las funciones en `functions/` utilizan las siguientes variables de entorno en el entorno de ejecución de InsForge:

| Variable | Descripción |
| :--- | :--- |
| `INSFORGE_BASE_URL` | URL base del proyecto en InsForge |
| `ANON_KEY` / `API_KEY` | Clave de acceso al cliente de InsForge |
| `GOOGLE_API_KEY` | *(Opcional)* Clave de Google AI Studio para embeddings y chat con Gemini |
| `OPENROUTER_API_KEY` | *(Opcional)* Clave de OpenRouter como alternativa para embeddings y chat |
| `OPENROUTER_CHAT_MODEL` | *(Opcional)* Modelo de chat por defecto si se usa OpenRouter |

---

## Aviso Legal

REPROChat es un proyecto de carácter informativo y orientativo desarrollado por Cooperativa Origen. La información brindada no posee validez legal ni sustituye el asesoramiento jurídico o médico profesional, y no debe ser utilizada como fuente oficial.
