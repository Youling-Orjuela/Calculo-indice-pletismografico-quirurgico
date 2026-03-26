# Cálculo ambulatorio del índice pletismográfico quirúrgico (SPI)
## Introducción
El dolor posoperatorio continúa siendo uno de los desafíos más relevantes en la aplicación actual de los agentes anestésicos. Se estima que entre el 20 % y el 80 % de los pacientes sometidos a procedimientos quirúrgicos experimentan dolor agudo posoperatorio de intensidad moderada a severa, lo que refleja un manejo analgésico intraoperatorio frecuentemente insuficiente [1]. Esta realidad impone la necesidad de contar con métodos objetivos y cuantitativos que permitan evaluar en tiempo real el equilibrio entre la activación nociceptiva y el efecto de la analgesia administrada durante la cirugía bajo anestesia general.

La nocicepción, entendida como el procesamiento neural de estímulos potencialmente dañinos, se distingue del dolor como experiencia subjetiva consciente. Bajo anestesia general, el paciente no reporta dolor, pero su sistema nervioso autónomo continúa respondiendo a los estímulos nociceptivos, generando cambios fisiológicos medibles como modificaciones en la frecuencia cardíaca, la vasomotricidad periférica y la variabilidad de la señal fotopletismográfica (PPG) [2]. Monitorizar estos cambios de forma continua y objetiva es, por lo tanto, fundamental para guiar la administración de analgésicos y reducir el riesgo de dolor posoperatorio.

### La señal fotopletismográfica (PPG) como base de la monitorización

La fotopletismografía (PPG) es una técnica óptica no invasiva que permite detectar variaciones en el volumen sanguíneo periférico mediante la medición de cambios en la absorción o reflexión de luz infrarroja a través del tejido. La señal PPG resultante presenta dos componentes principales:

- **Componente pulsátil (AC):** Refleja las variaciones cíclicas del volumen sanguíneo asociadas a cada latido cardíaco. Su amplitud (PPGA, del inglés PPG Amplitude) está fuertemente influenciada por el tono vasomotor periférico: ante un estímulo nociceptivo, la activación simpática produce vasoconstricción periférica, lo que reduce la amplitud de la onda de pulso.
- **Componente continua (DC):** Representa la absorción basal de luz por los tejidos y la sangre venosa, y varía lentamente con cambios en la perfusión tisular.

Adicionalmente, del análisis temporal de la señal PPG se puede extraer el intervalo entre latidos (HBI, del inglés Heart Beat Interval), equivalente al intervalo R-R del electrocardiograma, cuyo inverso corresponde a la frecuencia cardíaca. Ante activación nociceptiva, el sistema nervioso simpático incrementa la frecuencia cardíaca, reduciendo el HBI [3].
Estas dos variables (la amplitud de la onda de pulso (PPGA) y el intervalo entre latidos (HBI)) constituyen la base fisiológica sobre la que se construye el índice pletismográfico quirúrgico.

El índice pletismográfico quirúrgico (SPI), desarrollado por GE Healthcare, es un indicador adimensional derivado de la señal PPG que cuantifica el balance entre la nocicepción y la analgesia durante la anestesia general [3]. El SPI varía en un rango de 0 a 100, donde valores elevados indican una mayor activación nociceptiva (mayor estrés fisiológico) y valores bajos indican un estado analgésico adecuado.
Clínicamente, el rango objetivo de SPI para una analgesia intraoperatoria adecuada se sitúa entre 20 y 50 [4]. Se recomienda mantener el SPI por debajo de 50 y evitar incrementos superiores a 10 unidades, ya que estos pueden indicar un estímulo doloroso no controlado [5].

### Modelo Matemático del SPI

El índice pletismográfico quirúrgico se calcula a partir de dos características normalizadas de la señal PPG: la amplitud de la onda de pulso (PPGA) y el intervalo entre latidos (HBI). Para su cálculo, ambas variables se normalizan respecto a valores de referencia establecidos durante una ventana temporal basal, obteniendo así el PPGA normalizado (nPPGA) y el HBI normalizado (nHBI), ambos con valores en el intervalo [0, 1] [4].

La fórmula del SPI corresponde a una combinación lineal ponderada de estas dos variables normalizadas, representadas de la siguiente manera:

$$
SPI = 100 - (0.7 \cdot nPPGA + 0.3 \cdot nHBI) \times 100
$$

donde:

- **nPPGA** es la amplitud pico a pico de la onda de pulso PPG normalizada al rango de referencia individual del paciente.
- **nHBI** es el intervalo entre latidos consecutivos, normalizado de forma análoga.
- **Los coeficientes de ponderación 0.7 y 0.3** reflejan la mayor contribución de la vasomotricidad periférica (capturada por PPGA) frente a la variación de la frecuencia cardíaca (capturada por HBI) en la respuesta nociceptiva [4].

## Procedimiento
Para llevar a cabo este laboratorio se empleó el sensor MAX30102, el cual integra un pulsioxímetro y un monitor de frecuencia cardíaca. Este dispositivo cuenta con dos LED, uno rojo de 660 nm y otro infrarrojo de 920 nm, además de incorporar un filtro óptico, un fotodetector y un conversor analógico-digital (ADC) de 16 bits [6].

El sensor se conectó a un microcontrolador ESP32, y se diseñó un código en MATLAB encargado de capturar la señal obtenida.

Para la adquisición de la señal, un integrante del grupo colocó su dedo sobre el sensor, configurando el sistema para realizar una captura de 2 minutos. Durante los primeros 40 segundos, el sujeto permaneció en reposo en posición sentada. Al cumplirse este tiempo, se aplicó la prueba Cold Pressor Test (CPT) durante los siguientes 40 segundos. Finalmente, el sujeto regresó a las condiciones iniciales de reposo durante los últimos 40 segundos, hasta completar el tiempo total de adquisición.

Durante cada uno de estos intervalos, se registraron los valores del índice pletismográfico quirúrgico (SPI) obtenidos.

### Cold Pressor Test
El Cold Pressor Test (CPT) es una prueba cardiovascular que consiste en introducir la mano en un recipiente con agua fría (generalmente entre 0 y 4 °C) durante aproximadamente un minuto. Esta prueba se utiliza para inducir una respuesta de estrés fisiológico, permitiendo evaluar variables como la respuesta autonómica y la tolerancia al dolor. En caso de que el dolor se vuelva intolerable, el participante puede retirar la mano antes de completar el tiempo establecido [7].

Debido a que en el laboratorio no se permite el uso de agua, se implementó una modificación del procedimiento. Esta consistió en el uso de una banda con tres compartimientos rellenos de gel, previamente congelados, la cual se colocó alrededor de la extremidad del participante. Este método permite emular el estímulo térmico del CPT sin necesidad de emplear agua, manteniendo el objetivo de inducir una respuesta fisiológica similar.

## Adquisión de la señal
```
MatLab
clc;
clear;
close all;

port = "COM6";
baud = 115200;
fs = 100;

baselineDuration = 40;
cptDuration = 40;
defaultDuration = "120";

refractoryTime = 0.40;
dcWindowSec = 1.20;
smoothSamples = 5;
spiWinBeats = 5;
invertSignal = true;
alphaSPI = 0.25;

wPPA = 0.33;
wPPI = 0.67;

ratioMin = 0.20;
ratioMax = 2.00;
```
#Configuración inicial y parámetros del sistema
Este bloque establece todos los parámetros de operación del sistema antes de iniciar la adquisición.

port y baud definen la conexión serial con la ESP32. La tasa de 115200 baudios garantiza suficiente ancho de banda para transmitir muestras a 100 Hz sin pérdida de datos.
fs = 100 corresponde a la frecuencia de muestreo en Hz. A 100 Hz se capturan 100 muestras por segundo, suficiente para resolver los componentes morfológicos de la señal PPG (cuya energía relevante está por debajo de 10 Hz).
baselineDuration = 40 y cptDuration = 40 definen las dos fases experimentales principales: una fase de reposo basal (0–40 s) y una fase de estrés mediante Cold Pressor Test —CPT— (40–80 s). El CPT es un protocolo estándar en fisiología del estrés que consiste en exposición de la mano a agua helada, generando una respuesta simpática medible.
refractoryTime = 0.40 impone un período refractario mínimo entre picos detectados (400 ms), equivalente a descartar frecuencias cardíacas superiores a 150 BPM, evitando detecciones espurias por ruido de alta frecuencia.
dcWindowSec = 1.20 define la ventana temporal para estimar la componente de corriente continua (DC) de la señal PPG. La señal cruda del sensor incluye una componente DC lenta asociada a la absorción óptica basal del tejido, que debe eliminarse para aislar la componente pulsátil (AC).
smoothSamples = 5 es el número de muestras empleadas en el suavizado por media móvil, que actúa como filtro paso-bajo simple para reducir ruido de alta frecuencia.
invertSignal = true corrige la polaridad de la señal. Dependiendo del hardware del sensor, el pulso puede aparecer como una depresión en lugar de un pico; la inversión garantiza que los picos sistólicos sean máximos locales.
alphaSPI = 0.25 es el coeficiente de suavizado exponencial del SPI. Con α = 0.25, cada nuevo valor tiene un peso del 25% y el historial acumulado el 75%, proporcionando estabilidad temporal al índice.
wPPA = 0.33 y wPPI = 0.67 son los pesos de ponderación del PPA (Pulse Peak Amplitude) y PPI (Pulse-to-Pulse Interval) en el cálculo del SPI. Se asigna mayor peso al PPI porque el intervalo entre pulsos es un indicador más robusto de la actividad del sistema nervioso autónomo que la amplitud.
ratioMin y ratioMax delimitan el rango válido de las razones normalizadas PPA/PPA_ref y PPI/PPI_ref, evitando que artefactos o latidos ectópicos distorsionen el cálculo.
[![spi1.jpg](https://i.postimg.cc/g0r7VnL6/spi1.jpg)](https://postimg.cc/PLsKhf4t)
[![spi2.jpg](https://i.postimg.cc/9FMyFT6p/spi2.jpg)](https://postimg.cc/jCpDMWdw)
[![spi3.jpg](https://i.postimg.cc/pTWpcMxp/spi3.jpg)](https://postimg.cc/KKWG4V12)
[![spi4.jpg](https://i.postimg.cc/ydx7S1sP/spi4.jpg)](https://postimg.cc/SXBBBpX2)
## Análisis
• Análisis 1: Compare los valores del SPI obtenidos durante la práctica con
los que frecuentemente se observan durante una cirugía para proporcionar
el nivel óptimo de anestesia.
• Análisis 2: Evalúe el alcance y las posibles limitaciones de emplear el
sistema desarrollado para cuantificar el dolor que percibe una persona.

El sistema desarrollado, basado en la señal PPG y el cálculo del SPI, permite evaluar de forma objetiva la respuesta fisiológica asociada a la nocicepción, a partir de cambios en la amplitud del pulso (PPGA) y el intervalo entre latidos (HBI). Esto lo convierte en una herramienta útil para monitorear el estrés y la activación del sistema nervioso autónomo de manera continua y no invasiva, especialmente en contextos donde el paciente no puede comunicar dolor.

Sin embargo, su principal limitación es que el SPI no mide el dolor directamente, sino la nocicepción. El dolor es una experiencia subjetiva influenciada por factores emocionales y psicológicos que no se reflejan completamente en señales fisiológicas.

Además, la señal PPG puede verse afectada por factores como el movimiento, la presión del sensor o la perfusión periférica, lo que impacta la precisión del SPI. También, respuestas fisiológicas similares pueden generarse por estrés o ansiedad, lo que puede llevar a interpretaciones erróneas.

En conclusión, el sistema es útil para evaluar cambios fisiológicos asociados al estrés o nocicepción, pero debe considerarse como un indicador complementario, no como una medida directa del dolor.

## Conclusiones
## Referencias 
[1] J. L. Apfelbaum, C. Chen, S. S. Mehta y T. J. Gan, “Postoperative pain
experience: results from a national survey suggest postoperative pain
continues to be undermanaged,” Anesthesia & Analgesia, vol. 97, no. 2, pp.
534–540, 2003. https://doi.org/10.1213/01.ANE.0000068822.10113.9E.

[2] T. Ledowski, “Objective monitoring of nociception: a review of current
commercial solutions,” British Journal of Anaesthesia, vol. 123, no. 2, pp.
e312–e321, 2019. https://doi.org/10.1016/j.bja.2019.03.024.

[3] V. Bonhomme, K. Uutela, G. Hans, I. Maquoi, J. D. Born y J. F. Brichant,
“Comparison of the Surgical Pleth IndexTM with haemodynamic variables to
assess nociception-anti-nociception balance during general anaesthesia,”
British Journal of Anaesthesia, vol. 106, no. 1, pp. 101–111, 2011.
https://doi.org/10.1093/bja/aeq291.

[4] M. Huiku, K. Uutela, M. van Gils, I. Korhonen, M. Kymäläinen, P. Meriläinen,
M. Paloheimo, M. Rantanen, P. Takala, H. Viertiö-Oja y A. Yli-Hankala,
“Assessment of surgical stress during general anaesthesia,” British Journal of
Anaesthesia, vol. 98, no. 4, pp. 447–455, 2007.
https://doi.org/10.1093/bja/aem004.

[5] S. Funcke, S. Sauerlaender, H. O. Pinnschmidt, B. Saugel, K. Bremer, D. A.
Reuter, R. Nitzschke y otros, “Validation of innovative techniques for
monitoring nociception during general anesthesia: a clinical study using
tetanic and intracutaneous electrical stimulation,” Anesthesiology, vol. 127,
no. 2, pp. 272–283, 2017. https://doi.org/10.1097/ALN.0000000000001670.

[6] “MAX30102 - Sensor de concentración de Oxígeno y Ritmo cardíaco - Electronilab,” Electronilab, Mar. 12, 2026. https://electronilab.co/tienda/max30100-sensor-de-concentracion-de-oxigeno-y-ritmo-cardiaco/?srsltid=AfmBOoqiTAVjVyqg8rbmHV2--vj8PzahtCs0zrUgqFnTVDIwkiPpI9yh

[7] Y. S. Can, B. Arnrich, and C. Ersoy, “Stress detection in daily life scenarios using smart phones and wearable sensors: A survey,” Journal of Biomedical Informatics, vol. 92, p. 103139, Feb. 2019, doi: 10.1016/j.jbi.2019.103139.

