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
