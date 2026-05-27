# Fase 1 — Dimensión Ambiental (A)

## 1.1 Medición Inicial
Para la medición he utilizado Website Carbon Calculator y me ha dado este resultado 0.44g of CO2, necesita mejoras urgentes.
<img width="1245" height="679" alt="image" src="https://github.com/user-attachments/assets/12a41ace-486c-4b6a-a76f-7bc7fa0d15a8" />

## 1.2 Identificación de Bloatware
<img width="553" height="944" alt="Captura de pantalla 2026-05-27 111911" src="https://github.com/user-attachments/assets/13df790b-5757-4709-9a66-89402d16b4d8" />
Análisis realizado con el panel Network de Chrome DevTools (F12) al cargar la página de inicio. Total: **38 peticiones · 4,6 MB · 7,21 segundos de carga**.

| \# | Recurso | Tipo | Problema |
| ----- | ----- | ----- | ----- |
| 1 | `Foro-BM-2025.png` | Imagen PNG | PNG sin comprimir para una fotografía. AVIF reduciría su tamaño hasta un 75% con la misma calidad visual |
| 2 | `FotoHomeBloquePersonas`, `FotoHomeBloqueResponsabilidad`, `caratula_memoria_uvesco` (varios) | Imágenes JPEG | Múltiples fotografías de gran tamaño cargadas simultáneamente sin lazy loading, incluyendo imágenes que el usuario no ve hasta hacer scroll |
| 3 | `jquery-3.4.1.js` \+ `bootstrap.min.js` \+ `prensa.js` \+ `elementos_feb2024.js` | JavaScript | jQuery versión 2019 con vulnerabilidades conocidas, cargado síncronamente junto con otros scripts, bloqueando la visualización de la página |

## 1.3. Análisis
Una web corporativa informativa como la de Uvesco debería pesar menos de 1 MB. Descargar 4,6 MB implica que se están sirviendo recursos sin optimizar, algo que en Green Software Engineering se denomina *software bloat* o inflación de software.

Las causas concretas son cuatro:

- Las imágenes se sirven en formatos obsoletos (PNG para fotografías, JPEG sin optimización moderna) cuando AVIF y WebP ofrecen la misma calidad con un 50-75% menos de peso.  
- Se utiliza jQuery 3.4.1, una librería de 2019 con vulnerabilidades de seguridad documentadas, que puede eliminarse completamente y sustituirse por JavaScript nativo moderno.  
- No existe lazy loading: todas las imágenes se descargan simultáneamente aunque el usuario nunca llegue a verlas.  
- Los scripts se cargan de forma síncrona, bloqueando la renderización de la página.

# Fase 2 — Dimensión Social y Equidad (S)

## 2.1 Resultados del test de accesibilidad

Se utilizaron WAVE Web Accessibility Evaluation Tool y Lighthouse (modo Accessibility) para evaluar el cumplimiento de las WCAG 2.2.

| Herramienta | Puntuación | Errores graves | Otras incidencias |
| ----- | ----- | ----- | ----- |
| WAVE | AIM Score: **7,2 / 10** | 7 errores: 3 imágenes sin alt, 3 imágenes enlazadas sin alt, 1 formulario sin etiqueta | 61 alertas, 27 elementos estructurales |
| Lighthouse — Accessibility | **80 / 100** (naranja) | Imágenes sin alt, enlaces sin nombre accesible, viewport con zoom bloqueado | 10 elementos para revisión manual |
<img width="372" height="944" alt="Captura de pantalla 2026-05-27 131224" src="https://github.com/user-attachments/assets/2cc46c43-d8f2-4835-81da-663823f3d4d2" />
<img width="555" height="941" alt="Captura de pantalla 2026-05-27 131507" src="https://github.com/user-attachments/assets/0b5bf3ad-f3cc-4246-993c-68f0a9d3d94d" />

## 2.2 Barreras de accesibilidad identificadas
Barrera grave 1 — Imágenes sin texto alternativo (WCAG 2.2 · Criterio 1.1.1 · Nivel A)

WAVE y Lighthouse detectan 6 imágenes sin atributo `alt`. Los usuarios de lectores de pantalla (personas ciegas o con baja visión) no reciben ninguna información sobre esas imágenes. En las 3 que además funcionan como enlace, el usuario tampoco puede saber adónde le lleva el enlace.

\<\!-- ANTES: invisible para un lector de pantalla \--\>  
\<img src="Foro-BM-2025.png" class="foto-portada"\>

\<\!-- DESPUÉS: accesible para todos los usuarios \--\>  
\<img src="Foro-BM-2025.png"  
     alt="Participantes en el Foro BM 2025, evento corporativo anual de Uvesco"  
     class="foto-portada"\>

\<\!-- Para imágenes que son enlace, el alt describe el destino \--\>  
\<a href="/sostenibilidad"\>  
  \<img src="FotoHomeBloqueResponsabilidad.jpg"  
       alt="Ir a la sección de Responsabilidad Social de Uvesco"\>  
\</a\>

Barrera grave 2 — Zoom bloqueado en móvil (WCAG 2.2 · Criterio 1.4.4 · Nivel AA)

El código HTML incluye `user-scalable=no` en el viewport, impidiendo que personas con baja visión puedan ampliar el contenido en dispositivos móviles.

\<\!-- ANTES: el usuario no puede hacer zoom \--\>  
\<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no"\>

\<\!-- DESPUÉS: el usuario puede ampliar libremente \--\>  
\<meta name="viewport" content="width=device-width, initial-scale=1"\>

# Fase 3 — Dimensión de Gobernanza y Ética (G)

### 3.1 Análisis del banner de cookies
| Elemento | Situación | Valoración |
| ----- | ----- | ----- |
| Botón "ACEPTAR TODAS" | Visible, primer botón | Correcto |
| Botón "RECHAZAR TODAS" | Visible, mismo tamaño que aceptar | **Positivo:** no oculta la opción de rechazo |
| Botón "CONFIGURAR" | Disponible como tercera opción | Correcto |
| Texto informativo | Genérico, no especifica tipos ni finalidades | Mejorable |

El banner de Uvesco **no presenta patrones oscuros graves**. La opción de rechazo es accesible directamente, cumpliendo las directrices de la AEPD (2022). Área de mejora: mayor especificidad en el texto sobre qué cookies se instalan y para qué.

📸 **Evidencia 6:** La captura de WAVE (Evidencia 4\) ya recoge el banner de cookies visible en la parte inferior con los tres botones de igual tamaño.

### 3.2 Análisis del formulario y datos personales
El formulario de contacto no solicita datos excesivos. Sin embargo, se detectan dos aspectos mejorables bajo el RGPD:

| Aspecto | Situación | Obligación legal |
| ----- | ----- | ----- |
| Período de conservación | No se indica durante cuánto tiempo se guardarán los datos | Art. 13.2.a RGPD — debe informarse en el momento de la recogida |
| Claridad de la política de privacidad | Redactada en lenguaje jurídico denso sin resumen comprensible | Art. 12 RGPD — información concisa, transparente e inteligible |

# Fase 4 — Propuesta de Refactorización (Green Coding)

### 4.1 Mejoras ambientales (A)
Sustitución de formatos de imagen:

\<picture\>  
  \<source srcset="foro-bm-2025.avif" type="image/avif"\>  
  \<source srcset="foro-bm-2025.webp" type="image/webp"\>  
  \<img src="foro-bm-2025.jpg"  
       alt="Participantes en el Foro BM 2025"  
       loading="lazy" width="800" height="450"\>  
\</picture\>

| Tipo | Actual | Propuesto | Ahorro |
| ----- | ----- | ----- | ----- |
| Fotografías PNG | PNG | AVIF | –75% |
| Fotos de portada JPEG | JPEG | WebP | –40-50% |
| Logotipos | PNG | SVG inline | –90% |

Reducción de scripts:
\<\!-- ANTES: jQuery bloquea la carga \--\>  
\<script src="jquery-3.4.1.js"\>\</script\>  
\<script src="elementos\_feb2024.js"\>\</script\>

\<\!-- DESPUÉS: sin jQuery, resto diferido \--\>  
\<script src="elementos\_feb2024.js" defer\>\</script\>

### 4.2 Mejoras sociales (S)
| Mejora | Criterio WCAG 2.2 | Impacto |
| ----- | ----- | ----- |
| Alt descriptivo en 6 imágenes | 1.1.1 (Nivel A) | Acceso a usuarios de lector de pantalla |
| Eliminar user-scalable=no | 1.4.4 (Nivel AA) | Zoom en móvil para personas con baja visión |
| Etiqueta label en formulario | 1.3.1 (Nivel A) | Campos identificables por tecnología asistiva |
| HTML semántico (header, nav, main) | 1.3.1 (Nivel A) | Navegación estructurada para lectores de pantalla |

### 4.3 Mejoras de gobernanza (G)
| Mejora | Base legal |
| ----- | ----- |
| Indicar período de conservación en el formulario | Art. 13.2.a RGPD |
| Resumen de política de privacidad en lenguaje claro | Art. 12 RGPD |
| Especificar categorías y finalidades en el banner de cookies | Guía AEPD 2022 |

### 4.4 Reflexión sobre la Paradoja de Jevons
La Paradoja de Jevons establece que cuando la eficiencia en el uso de un recurso mejora, el consumo total tiende a crecer porque la mejora incentiva un mayor uso. Aplicada a la web: si uvesco.es pasa de 7 segundos a 1,5 segundos de carga, puede atraer más visitas y aumentar el consumo total de energía aunque cada visita individual sea más eficiente.

Para evitarlo:
| Estrategia | En qué consiste |
| ----- | ----- |
| CO₂ total como KPI | Medir emisiones totales mensuales, no solo por visita, para hacer visible el impacto real del crecimiento de tráfico |
| Hosting con energía renovable | Migrar a proveedor certificado (Hetzner Green, GreenGeeks) para que el aumento de tráfico no implique más emisiones |
| Caché de larga duración | `Cache-Control: max-age=31536000` en recursos estáticos: visitas recurrentes no vuelven a descargar nada |
| Modo bajo consumo | Versión sin animaciones ni fuentes externas, activable voluntariamente desde el pie de página |

## Herramientas utilizadas
| Herramienta | Fase | URL | Resultado |
| ----- | ----- | ----- | ----- |
| Website Carbon Calculator | Ambiental | websitecarbon.com | Calificación F — peor que el 69% mundial |
| Lighthouse Performance | Ambiental | F12 \> Lighthouse | 69/100 — 7,21 s, 4,6 MB |
| Chrome Network Inspector | Bloatware | F12 \> Network | 38 peticiones, PNG/JPEG sin optimizar, jQuery 3.4.1 |
| WAVE | Social | wave.webaim.org | 7 errores, 61 alertas, AIM 7,2/10 |
| Lighthouse Accessibility | Social | F12 \> Lighthouse | 80/100 — imágenes sin alt, zoom bloqueado |

# Referencias
W3C (2023). WCAG 2.2. https://www.w3.org/TR/WCAG22/  
- Green Software Foundation (2023). Green Software Patterns. https://patterns.greensoftware.foundation/  
- AEPD (2022). Guía sobre el uso de cookies. https://www.aepd.es  
- Website Carbon Calculator. https://www.websitecarbon.com  
- Uvesco S.A. (2024). Memoria anual 2023\. https://www.uvesco.es  
- Reglamento (UE) 2016/679 — RGPD. https://eur-lex.europa.eu
