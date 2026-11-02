# REPROChat

Asistente conversacional basado en **RAG (Retrieval-Augmented Generation)** para consultar la normativa argentina sobre cannabis medicinal y **REPROCANN**:

- **Ley 27.350** (Uso Medicinal de la Planta de Cannabis y sus derivados)
- **Decreto Reglamentario 883/2020**
- **Resolución 800/2021** (Programa REPROCANN) y sus modificatorias (**Resolución 766/2023**, **Resolución 3132/2024** y **Resolución 1780/2025**)

---

## Arquitectura

- **Frontend:** HTML5, CSS3 (`global.css`) y JavaScript vanilla (`index.html`). Sin dependencias de build, listo para desplegar en cualquier hosting estático (GitHub Pages, Cloudflare Pages, Vercel, Netlify, etc.).
- **Base de datos:** PostgreSQL con `pgvector` en **InsForge** (`documents` con vectores de 1536 dimensiones e índice HNSW + función RPC `match_documents`).
- **Edge Functions (Deno / InsForge):**
  - `functions/ingest.ts`: Divide documentos normativos en fragmentos (*chunks*), genera embeddings (`gemini-embedding-001` / `text-embedding-3-small`) y los almacena en `public.documents`.
  - `functions/ask.ts`: Recibe la consulta del usuario, genera el embedding de búsqueda, recupera los fragmentos más relevantes mediante similitud coseno y genera una respuesta estrictamente anclada al corpus normativo.

---

## Estructura del proyecto

```text
├── index.html          # Interfaz principal del chat y cliente HTTP
├── global.css          # Estilos y diseño responsive
├── logo.svg            # Logo RED.origen en el encabezado
├── fav-icon.svg        # Favicon e ícono de carga
├── og-image.svg        # Imagen Open Graph para compartir en redes
├── functions/
│   ├── ask.ts          # Edge Function de consulta RAG
│   └── ingest.ts       # Edge Function de ingesta y vectorización
└── migrations/
    └── 20261101171837_setup-rag-documents.sql  # Esquema pgvector, RLS y RPC match_documents
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
