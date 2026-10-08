# Visión por Computador – Práctica 2

Notebook de la práctica 2 de Visión por Computador: manejo básico de imágenes y vídeo con OpenCV, detección de bordes (Canny y Sobel), umbralizado, diferencia de imágenes y sustracción de fondo con webcam. Incluye la resolución de las tres tareas propuestas.

## Contenido

| Fichero | Descripción |
|---|---|
| `VC_P2.ipynb` | Notebook con los ejemplos de la práctica y las soluciones a las tareas |
| `mandril.jpg` | Imagen de entrada (**no incluida**, debe estar en la misma carpeta que el notebook) |
| `README.md` | Este documento |

## Requisitos

- Python 3.11 (el *environment* de la práctica 1 sirve)
- `opencv-python`, `numpy`, `matplotlib`, `Pillow`
- Jupyter (Notebook, JupyterLab o VS Code)
- Webcam, solo para las dos celdas de vídeo en directo y para la versión en vivo del demostrador

```bash
pip install opencv-python numpy matplotlib pillow jupyter
```

## Ejecución

1. Colocar `mandril.jpg` junto a `VC_P2.ipynb`.
2. Abrir el notebook y ejecutar las celdas en orden (el orden importa: las tareas reutilizan variables como `gris`, `canny`, `sobel8` o `img`).
3. En las celdas con webcam, pulsar **ESC** para cerrar la ventana de OpenCV.

## Estructura del notebook

1. **Ejemplos de la práctica:** lectura y conversión BGR→RGB, texto con OpenCV y PIL, conversión a grises, Canny, conteo de píxeles por columnas, Sobel, umbralizado, histograma, diferencia de imágenes, webcam con sustracción de fotogramas y de fondo (MOG2).
2. **Tarea 1:** conteo de píxeles blancos por filas en Canny.
3. **Tarea 2:** umbralizado de Sobel, conteo por filas y columnas y comparación con Canny.
4. **Tarea 3:** demostrador «Arpa virtual».

## Soluciones a las tareas

### Tarea 1: filas en la imagen de Canny
- Se cuentan los píxeles blancos por fila (`cv2.reduce` con `REDUCE_SUM` en el eje 1, dividido entre 255).
- Se calcula `maxfil` y se seleccionan las filas con al menos `0.90*maxfil` píxeles blancos, mostrando su número y sus posiciones.
- Se resaltan esas filas con líneas rojas sobre la imagen de Canny y se dibuja el perfil de conteo con el umbral.
- Se ignora un margen de 3 píxeles en el contorno de la imagen: el mandril estándar tiene un marco en la última fila que Canny detecta como un borde larguísimo y falsea el máximo. Se puede ajustar con `MARGEN`.

### Tarea 2: Sobel umbralizado vs Canny
- Se umbraliza la salida de Sobel ya convertida a 8 bits (`valor_umbral_sobel = 100`, modificable).
- La función `analiza_filas_columnas` calcula conteos por filas y columnas, máximos y las filas/columnas por encima de `0.90*máximo`. La función `resalta` las dibuja (filas en rojo, columnas en verde) sobre la imagen del mandril.
- Se aplica a Sobel y a Canny y se visualizan imagen binaria, mandril con líneas y perfiles de conteo.
- Se incluye una celda de texto con la comparación. Resumen: Sobel da bordes más gruesos y depende mucho del umbral; Canny da bordes finos y más estables, y selecciona de forma más equilibrada las columnas de mayor densidad de contornos.

### Tarea 3: demostrador «Arpa virtual»
Reinterpretación del procesamiento de imagen de *Virtual Air Guitar* (movimiento de las manos que genera música) con ideas de *Messa di voce* (gráficos que nacen de la silueta), usando solo técnicas de esta práctica y sin guantes ni hardware especial:

1. Efecto espejo y segmentación con `BackgroundSubtractorMOG2`.
2. Umbralizado para eliminar sombras y apertura morfológica para quitar ruido.
3. La imagen se divide en 6 bandas horizontales (las cuerdas). En cada banda se cuentan los píxeles no nulos de la máscara, igual que en el conteo por filas/columnas de las tareas anteriores.
4. Si el movimiento en una banda supera un umbral (flanco de subida), la cuerda se «pulsa»: se dibuja vibrando con amortiguación y se muestra/imprime la nota.

La lógica está en la clase `ArpaVirtual`, independiente de la fuente de vídeo. Hay dos formas de probarla:
- **Simulación sintética:** no necesita cámara; una «mano» cruza las cuerdas y se muestran varios fotogramas.
- **En vivo:** webcam (`FUENTE = 0`) o fichero de vídeo (`FUENTE = 'video.mp4'`). Si la fuente no se puede abrir, la celda lo avisa y termina.

Parámetros útiles: `n_cuerdas`, `umbral_activacion` (sensibilidad) y `alto_banda`. Se podría añadir sonido real con `simpleaudio` o `pygame` en el punto indicado en la celda en vivo.

## Notas

- **Tipografías (celda de PIL):** el ejemplo usa `arial.ttf`, que existe en Windows. En Linux/macOS hay que indicar la ruta de otra fuente (por ejemplo, DejaVu Sans). Además, `font_size` debería ser un entero en versiones recientes de Pillow.
- **Webcam:** las celdas con `cv2.VideoCapture(0)` abren ventanas de OpenCV; no funcionan en entornos sin pantalla (servidores, Colab) sin adaptación.
- **Resultados de la comparación:** los valores citados en el notebook (filas/columnas seleccionadas, número de píxeles) corresponden al mandril estándar de 512x512 con los umbrales por defecto. Con otra imagen o con otros umbrales cambiarán.
