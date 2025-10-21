- Enlaces: `[texto](url)` o `[[nota interna]]`
    
- Listas: `- item` o `1. item`
    
- Tablas: usa `| encabezado |` con separadores `---`
    
- Código: bloques con o `` `inline` ``
    
- Matemáticas: `$ecuación$` o `$$bloque$$`
    
- Citas: `> texto`
    
- Callouts: `> [!tipo] texto`
    
- Callouts anidados: añade `>` dentro de otro
    
- Callouts plegables: `> [!tipo]-` o `> [!tipo]+`
    
- Callouts con iconos: usa el tipo (note, tip, info, warning, etc.)
    
- Referencias cruzadas: `[[nombre de nota]]`
    
- Etiquetas: `#etiqueta`
    
- Bloques `<details>`: `<details><summary>título</summary>contenido</details>`
    
- Dataview: bloque `dataview` con consultas
    
- Referencias a secciones: `[texto](#seccion)`
    
- Plantillas Templater: usa `<% %>` o `{{variable}}` según configuración
    
- YAML: `---` al inicio con claves y valores
    
- Diagramas Mermaid: `mermaid` seguido del grafo
    
- Imagen: `![](ruta o url)`
    
- Comentarios: `%% comentario %%`


- note
    
- abstract
    
- info
    
- todo
    
- tip
    
- success
    
- question
    
- warning
    
- failure
    
- danger
    
- bug
    
- example
    
- quote
    
- summary
    
- important
    
- caution
    
- attention
    
- faq
  
  ### 📜 Prompt — _Obsidian Notes Formatting Standard_

> Use this whenever you format or rewrite my study notes for **Obsidian**.

---

#### 🎯 General Goal

Create perfectly structured, professional, and visually consistent **Obsidian notes** for technical study (e.g., Computer Graphics, Programming, AI).  
The notes should be clear, didactic, visually balanced, and ready to paste directly into Obsidian.

---

#### 🧱 Structure Rules

1. Always begin with a **summary callout** (`> [!summary]`) describing the main idea of the section.
    
2. Use consistent **visual hierarchy**:
    
    - `###` for main concepts.
        
    - `####` only for subtopics inside long sections.
        
3. Separate sections clearly with `---` lines.
    
4. Keep **English technical language** throughout (no translations).
    
5. Never include YAML properties or tags unless explicitly requested.
    

---

#### 💬 Callout Guidelines

- **Use callouts regularly and consistently**:
    
    - `> [!summary]` — short overview at the top of the file.
        
    - `> [!info]` — factual clarifications or definitions.
        
    - `> [!example]` — all examples must go here, never left as plain bullet points.
        
    - `> [!tip]` — practical advice, visualization hints, or study reminders.
        
    - `> [!warning]` — highlight common mistakes or limitations.
        
    - `> [!note]` — extra theoretical or contextual comments.
        
    - `> [!important]` — key takeaways or core formulas.
        
- Never leave an example outside a callout.
    
- Keep callout placement uniform across sections.
    
- Only use 1–2 callouts per section unless conceptually justified.
    

---

#### 📊 Visual Elements

- Use **tables** for comparisons or property listings.
    
- Use **code blocks** for syntax, GLSL, or math (with language specified).
    
- Use **math blocks** for formulas:
    
    - Inline: `$x = y + z$`
        
    - Block:
        
        x=(oldX−oldLeft)(oldRight−oldLeft)×(newRight−newLeft)x = \frac{(oldX - oldLeft)}{(oldRight - oldLeft)} \times (newRight - newLeft) x=(oldRight−oldLeft)(oldX−oldLeft)​×(newRight−newLeft)
- Keep diagrams, examples, and structured data consistent with the rest of the note.
    

---

#### 🧩 Content Integrity

- Never remove or shorten original information.
    
- Reorganize only to **improve clarity**, not to omit.
    
- Preserve all examples, definitions, and enumerations.
    
- Expand explanations only when it improves conceptual understanding.
    

---

#### 🧠 Pedagogical Style

- Aim for a **clear academic tone** — concise, structured, neutral.
    
- Avoid redundancy but ensure conceptual completeness.
    
- Keep every section self-contained and logically connected to the previous one.
    
- Add short connecting phrases between topics if needed for coherence.
    

---

#### ✅ Output Expectation

Each note must be:

- Ready to paste into Obsidian.
    
- Fully formatted with Markdown syntax.
    
- Consistent in callout style, layout, and spacing.
    
- Easy to navigate visually and logically.