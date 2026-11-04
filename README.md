# TokenIA

Entrena tu propio generador de tokens a partir de tus documentos. Aplicación web de un solo fichero.

**Usar la app:** https://fborrasumh.github.io/tokenia/

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23964639.svg)](https://doi.org/10.5281/zenodo.23964639)

**Idiomas:** español (por defecto), inglés y portugués; selector en la barra superior (o `?lang=en` / `?lang=pt` en la URL).

## Qué hace

1. **Documentos.** Lee en el navegador ficheros .txt, .md, .docx y .pdf con texto (o texto pegado) y los limpia con reglas que se pueden desactivar: palabras cortadas con guion, líneas de un mismo párrafo, números de página, espacios y caracteres que aparecen menos de 5 veces. Muestra el número de caracteres, el vocabulario resultante y el texto final.
2. **Cuaderno.** Propone el tamaño del modelo según la cantidad de texto (diminuto, pequeño o mediano: de unos 0,8 a 14 millones de parámetros), avisa si se elige uno mayor de lo recomendado y genera un cuaderno de Google Colab (`.ipynb`) explicado celda a celda en el idioma de la interfaz. El texto va comprimido dentro del cuaderno; si supera 8 millones de caracteres se descarga aparte como `corpus.txt`.
3. **Entrenar.** El cuaderno entrena desde cero, en la GPU gratuita de Colab, un Transformer tipo Llama a nivel de carácter (RMSNorm, atención causal con RoPE, SwiGLU, embeddings compartidos), muestra la pérdida de entrenamiento y validación con muestras de texto, dibuja la curva y exporta un único fichero `.bin` (cabecera, vocabulario y pesos en 16 bits).
4. **Generar.** El navegador carga el `.bin` y genera texto carácter a carácter con temperatura, longitud y semilla. Colorea cada carácter según la probabilidad con que se eligió y, al pulsarlo, muestra los cinco candidatos que consideró el modelo. Incluye un modelo de ejemplo entrenado con el *Quijote* (dominio público).

## Qué comprueba el código

- **El motor del navegador calcula lo mismo que PyTorch.** El cuaderno guarda una muestra de referencia generada a temperatura 0 con los mismos pesos de 16 bits; al cargar el modelo, la app la repite y compara carácter a carácter, y dice si coincide. En las pruebas, los logits del motor JavaScript y los de PyTorch difieren como máximo en 7·10⁻⁶ (121 posiciones, mismo carácter más probable en todas).
- **El fichero es válido**: firma, versión, cabecera y tamaño exacto; un fichero truncado o ajeno da un error comprensible.
- **Reproducibilidad**: misma semilla y temperatura producen el mismo texto.

## Privacidad

Los documentos se leen y limpian en el navegador y se guardan solo en él (IndexedDB). Salen únicamente si la persona sube el cuaderno a Google Colab: entonces el texto queda en su Google Drive y en la máquina de Colab mientras la usa. La app pide confirmarlo antes de descargar el cuaderno y recomienda no usar documentos confidenciales ni con datos personales. El modelo y la generación no salen nunca del navegador. La app no usa claves de IA ni servidores.

## Límites

- Es un modelo **diminuto** entrenado solo con los textos de la persona: imita vocabulario, puntuación y estilo, pero inventa palabras y frases sin sentido y no comprueba nada de lo que escribe. No es un asistente: continúa el texto que se le da.
- Con poco texto (menos de unos 300.000 caracteres) memoriza fragmentos literales; por debajo de 20.000 caracteres la app no genera el cuaderno.
- Los tiempos de entrenamiento que muestra son orientativos para una GPU T4. Colab gratuito puede no tener GPU libre o cortar sesiones inactivas; el cuaderno tiene un tope de tiempo y guarda el modelo tal como esté.
- Los PDF escaneados no tienen texto: hace falta pasarlos antes por OCR.
- **Qué no se ha probado**: el cuaderno se ha ejecutado de principio a fin en CPU (PyTorch 2.14) con pocos pasos, no en una GPU real de Colab; la parte de GPU (precisión mixta) es código estándar de PyTorch, pero conviene una primera ejecución supervisada.

## Autoría

Fernando Borrás Rocher (Universidad Miguel Hernández de Elche).

Inspirada en [llama2.c](https://github.com/karpathy/llama2.c) y [nanoGPT](https://github.com/karpathy/nanoGPT) de Andrej Karpathy (licencia MIT): la arquitectura, el entrenamiento a nivel de carácter y la inferencia en código propio siguen sus ideas. El código de TokenIA está escrito desde cero.

ORCID: Fernando Borrás Rocher [0000-0002-5519-4573](https://orcid.org/0000-0002-5519-4573)

El texto de ejemplo es «El ingenioso hidalgo don Quijote de la Mancha» de Miguel de Cervantes (Project Gutenberg, EBook #2000, dominio público).

## Para desarrollar

`src/` contiene la interfaz y el motor; `tools/nb_cells.py` es la fuente de las celdas del cuaderno (`python3 tools/gen_cuaderno.py`), `tools/i18n_tabla.py` la de las traducciones (`python3 tools/gen_i18n.py`) y `tools/gen_datos.py` incrusta el fragmento y el modelo de ejemplo. Después, `python3 build.py`. Pruebas: `tests/motor_test.js` (motor en Node) y `tests/ui_test.py` (de punta a punta con Playwright, incluida la ejecución del cuaderno).

## Cómo citar

Borrás Rocher, F. (2026). *TokenIA* (v1.0.0) [Software]. DOI: [10.5281/zenodo.23964639](https://doi.org/10.5281/zenodo.23964639)

## Licencia

MIT. Véase [LICENSE](LICENSE).
