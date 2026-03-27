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
## Librerías e instancias de hardware
```
#include <Wire.h>
#include "MAX30105.h"
#include "BluetoothSerial.h"

MAX30105 particleSensor;
BluetoothSerial SerialBT;

const char* BT_NAME = "ESP32_MAX30102";
```
Este bloque declara las dependencias de software y crea las instancias de los dos periféricos principales del sistema.

Wire.h es la librería estándar de Arduino que implementa el protocolo de comunicación I2C (Inter-Integrated Circuit), también llamado TWI. I2C es un bus serie síncrono de dos hilos (SDA para datos, SCL para reloj) que permite conectar múltiples dispositivos esclavos a un único maestro usando solo dos líneas. El MAX30102 se comunica exclusivamente por I2C, haciendo esta librería indispensable.
MAX30105.h es el driver de SparkFun para la familia MAX30101/MAX30102/MAX30105 de sensores de oximetría y frecuencia cardíaca de Maxim Integrated. Encapsula toda la comunicación de bajo nivel con el sensor (lectura y escritura de registros I2C, gestión del FIFO interno del sensor, configuración de LEDs y ADC) exponiendo una API de alto nivel. Aunque el chip físico es el MAX30102, la librería MAX30105 es compatible por la similitud de registros entre ambos modelos.
MAX30105 particleSensor instancia el objeto driver del sensor. Toda la interacción con el hardware del MAX30102 se realizará a través de los métodos de este objeto.
BluetoothSerial SerialBT instancia el stack de comunicación Bluetooth SPP, exactamente igual que en el firmware GSR. El nombre "ESP32_MAX30102" identifica el dispositivo como el sensor de fotopletismografía en el entorno Bluetooth del laboratorio, diferenciándolo del "ESP32_GSR" si ambos operan simultáneamente.

## Parámetros de hardware y frecuencia de muestreo
```
const int SDA_PIN = 21;
const int SCL_PIN = 22;

const int fs = 100;
const int dt_ms = 1000 / fs;
```
Este bloque define la asignación de pines físicos y los parámetros de temporización del sistema de adquisición.

SDA_PIN = 21 y SCL_PIN = 22 son los pines I2C por defecto del ESP32 en la mayoría de las placas de desarrollo. GPIO 21 corresponde a la línea de datos bidireccional (SDA) y GPIO 22 a la línea de reloj (SCL). Aunque el ESP32 permite reasignar el bus I2C a casi cualquier GPIO mediante la función Wire.begin(SDA, SCL), usar los pines predeterminados garantiza la máxima compatibilidad con el hardware de la placa.
fs = 100 Hz define la frecuencia a la que MATLAB recibirá muestras. Esta elección no es arbitraria: la señal PPG tiene componentes relevantes hasta aproximadamente 10–15 Hz (frecuencia cardíaca fundamental y sus primeros armónicos, más la morfología del pulso), por lo que 100 Hz cumple holgadamente el criterio de Nyquist con un margen de seguridad de 3–5x. Frecuencias más altas no aportarían información adicional pero aumentarían la carga del canal Bluetooth y el costo computacional del procesamiento en MATLAB.
dt_ms = 1000 / fs = 10 ms es el período de muestreo. A diferencia del firmware GSR (40 ms a 25 Hz), aquí el período es 4 veces más corto, lo que significa que el bucle loop() se ejecuta 100 veces por segundo. Esto impone restricciones más estrictas sobre el tiempo de ejecución de cada iteración: todas las operaciones dentro del bucle deben completarse en menos de 10 ms para mantener la cadencia de muestreo.

## Máquina de estados simplificada
```
enum State { IDLE, RUN };
State st = IDLE;
```
A diferencia del firmware GSR que requería cuatro estados para gestionar un protocolo de calibración de dos fases, el firmware PPG implementa una máquina de estados mínima de dos estados.
Esta simplificación es justificada por la naturaleza diferente del procesamiento: el sensor MAX30102 no requiere calibración en el microcontrolador porque toda la normalización, eliminación de componente DC y estimación de referencias basales se realiza en MATLAB con mayor capacidad de cómputo y flexibilidad algorítmica. El firmware de la ESP32 tiene una única responsabilidad: adquirir y transmitir la señal cruda con la menor latencia y el menor procesamiento posible.

IDLE: El dispositivo está conectado pero no transmite datos en formato de streaming. Puede emitir tramas de monitoreo con etiqueta IDLE para indicar que está activo, pero sin generar carga significativa en el canal Bluetooth ni en el buffer de MATLAB.
RUN: Modo de adquisición activa. Cada muestra del sensor se transmite inmediatamente a MATLAB con su timestamp, formando el flujo de datos que alimenta el algoritmo de detección de pulso y cálculo del SPI.

La transición entre estados está controlada exclusivamente por comandos del host ('S' para iniciar, 'P' para pausar), lo que otorga a MATLAB el control total sobre el ciclo de vida de la sesión de adquisición.

## Función setup: inicialización del hardware
```
void setup() {
  Serial.begin(115200);
  Wire.begin(SDA_PIN, SCL_PIN);

  if (!SerialBT.begin(BT_NAME)) {
    while (true) delay(1000);
  }

  if (!particleSensor.begin(Wire, I2C_SPEED_FAST)) {
    SerialBT.println("ERROR,MAX30102_NOT_FOUND");
    while (true) delay(1000);
  }

  particleSensor.setup(60, 4, 2, 100, 411, 4096);

  particleSensor.setPulseAmplitudeRed(0x1F);
  particleSensor.setPulseAmplitudeIR(0x1F);
  particleSensor.setPulseAmplitudeGreen(0);

  st = IDLE;
  SerialBT.println("READY,SEND_S_TO_START");
}
```
Inicialización del hardware en setup()

La función setup() inicializa secuencialmente todos los subsistemas del hardware en orden de dependencia.

Inicialización del bus I2C

La instrucción Wire.begin(SDA_PIN, SCL_PIN) configura el ESP32 como maestro I2C en los pines especificados. Esta instrucción debe ejecutarse antes de cualquier intento de comunicación con el sensor MAX30102.

Detección y verificación del sensor

La instrucción particleSensor.begin(Wire, I2C_SPEED_FAST) intenta detectar el MAX30102 en el bus I2C a 400 kHz, es decir, en modo Fast.

Internamente, la librería lee el registro de identificación del chip (REG_PART_ID = 0xFF) y verifica que el valor retornado sea 0x15, que corresponde al identificador único del MAX30102.

Si la verificación falla, ya sea porque el sensor no está conectado, existe un problema de alimentación o la dirección I2C es incorrecta, el firmware entra en un estado de fallo seguro. En ese caso transmite el mensaje de error "ERROR,MAX30102_NOT_FOUND" antes de bloquearse.

El modo I2C_SPEED_FAST a 400 kHz es preferible al modo estándar de 100 kHz porque reduce el tiempo de transferencia de cada lectura del FIFO, dejando más tiempo de CPU disponible para otras tareas dentro del bucle principal.

Configuración del sensor

La instrucción particleSensor.setup(60, 4, 2, 100, 411, 4096) configura el hardware interno del MAX30102.

En esta configuración, brightness = 60 define la corriente de los LEDs según la escala interna del driver. sampleAverage = 4 indica que el chip promedia internamente cuatro muestras antes de almacenarlas en el FIFO. ledMode = 2 activa el modo dual, usando simultáneamente los LEDs rojo e infrarrojo. sampleRate = 100 establece una frecuencia de muestreo interna de 100 Hz. pulseWidth = 411 define un ancho de pulso de 411 microsegundos, y adcRange = 4096 selecciona el rango del ADC correspondiente a la máxima sensibilidad.

El ancho de pulso de 411 µs es el máximo permitido por el MAX30102 a 100 Hz, lo que maximiza la energía óptica recibida por el fotodetector y, por tanto, mejora la relación señal-ruido de la señal PPG.

El promediado de 4 muestras internas se realiza directamente en hardware dentro del chip antes de que los datos lleguen al FIFO. Esto permite reducir el ruido sin agregar carga computacional adicional a la ESP32.

Configuración de amplitudes de los LEDs

Las instrucciones setPulseAmplitudeRed(0x1F) y setPulseAmplitudeIR(0x1F) fijan la corriente de los LEDs rojo e infrarrojo en un valor moderado.

El valor 0x1F, que corresponde a 31 en decimal, equivale aproximadamente a una corriente de 6 mA.

Por otra parte, el LED verde se apaga explícitamente mediante setPulseAmplitudeGreen(0) porque no se utiliza para PPG estándar. El LED verde suele ser útil en mediciones sobre la muñeca, pero tiene menor penetración en el tejido en comparación con el infrarrojo.

## Bucle principal: recepción de comandos y despacho de estados
```
void loop() {
  while (SerialBT.available()) {
    char c = (char)SerialBT.read();

    if (c == 'S' || c == 's') {
      st = RUN;
      SerialBT.println("STREAM,START");
    }

    if (c == 'P' || c == 'p') {
      st = IDLE;
      SerialBT.println("STREAM,STOP");
    }
  }

  long irRaw = particleSensor.getIR();
  uint32_t t = millis();

  if (st == IDLE) {
    SerialBT.printf("%lu,%ld,IDLE\n", (unsigned long)t, irRaw);
    delay(dt_ms);
    return;
  }

  if (st == RUN) {
    SerialBT.printf("%lu,%ld\n", (unsigned long)t, irRaw);
    delay(dt_ms);
    return;
  }
}
```
El bucle principal implementa el despachador de la FSM con una arquitectura de polling no bloqueante que combina recepción de comandos, lectura del sensor y transmisión de datos en cada iteración.
Recepción de comandos:
El bloque while (SerialBT.available()) procesa todos los bytes pendientes en el buffer de recepción Bluetooth antes de leer el sensor. Los comandos aceptados son 'S'/'s' (Start) y 'P'/'p' (Pause), en mayúscula y minúscula para mayor tolerancia a errores de escritura. Cada transición de estado se confirma al host con un mensaje de protocolo ("STREAM,START" o "STREAM,STOP"), permitiendo que MATLAB sincronice su estado interno con el del firmware.
Lectura del sensor MAX30102:
particleSensor.getIR() lee el valor más reciente del canal infrarrojo desde el FIFO circular interno del MAX30102. El FIFO del sensor puede almacenar hasta 32 muestras, actuando como buffer ante latencias en el bucle principal. A 100 Hz con un dt_ms de 10 ms, idealmente se consume una muestra por iteración, manteniendo el FIFO casi vacío. millis() captura el timestamp inmediatamente después de la lectura, minimizando la jitter de temporización entre la muestra y su marca de tiempo.
Formato de transmisión diferenciado por estado:

En IDLE: timestamp_ms,IR_raw,IDLE — trama completa con etiqueta de estado, útil para monitoreo y diagnóstico.
En RUN: timestamp_ms,IR_raw — trama mínima sin etiqueta, reduciendo el tamaño del paquete Bluetooth y el tiempo de serialización. Este es exactamente el formato que el código MATLAB espera parsear en su bucle de adquisición: parts(1) = timestamp, parts(2) = valor IR.

La separación del formato entre estados elimina la necesidad de que MATLAB filtre etiquetas de texto durante la adquisición activa, reduciendo la carga de parseo en el host y minimizando la latencia del procesamiento en tiempo real.
## Configuración inicial y parámetros del sistema 
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

## Entrada de usuario y configuración del puerto serial
```
answer = inputdlg( ...
    "Ingrese la duración de captura en segundos:", ...
    "Tiempo de captura", [1 45], {defaultDuration});

if isempty(answer), return; end

duration = str2double(answer{1});
if isnan(duration) || duration <= 0
    error("Tiempo de captura inválido.");
end

clear s
s = serialport(port, baud);
configureTerminator(s, "LF");
flush(s);
s.Timeout = 0.05;
cleanupObj = onCleanup(@()clearSerialPort(s));

pause(2);
write(s, "S", "char");
pause(0.20);
flush(s);
```
Este bloque gestiona la interacción con el usuario y establece la comunicación serial con el microcontrolador.

inputdlg abre un cuadro de diálogo que solicita la duración total de la captura. Esto permite flexibilidad experimental sin necesidad de editar el código fuente. La validación posterior garantiza que el valor ingresado sea un número positivo.
La inicialización del puerto serial con serialport utiliza la API moderna de MATLAB (R2020a+), más robusta que el objeto serial legado. El terminador "LF" (salto de línea, carácter ASCII 10) coincide con el protocolo de transmisión de la ESP32, que envía cada muestra como una línea de texto terminada en \n.
s.Timeout = 0.05 establece un tiempo máximo de espera de 50 ms para operaciones de lectura. Esto impide que el bucle principal se bloquee indefinidamente si no hay datos disponibles.
onCleanup(@()clearSerialPort(s)) registra una función de limpieza que se ejecuta automáticamente cuando el script termina, ya sea normalmente o por error. Esto garantiza que el puerto serial siempre se libere correctamente.
El pause(2) inicial da tiempo al microcontrolador para estabilizarse después de la apertura del puerto (muchas placas ESP32 se reinician al detectar una conexión serial). La escritura del carácter "S" (Start) es la señal de protocolo que ordena al firmware comenzar la transmisión de datos.

## Inicialización de buffers y variables de estado
```
Nmax = max(6000, ceil(duration * fs * 2));
t_ms = zeros(Nmax,1);
irRaw = zeros(Nmax,1);
xProc = zeros(Nmax,1);
k = 0;

buffer = "";
t0_ms = [];

estado = 0;
hasValley = false;

lastValleyIdx = NaN;
lastValleyValue = NaN;
lastPeakTime = -inf;

peak_idx = [];
peak_times = [];
peak_values = [];

valley_idx = [];
valley_times = [];
valley_values = [];

ppa_values = [];
ppi_values = [];

currentBPM = NaN;

baselineReady = false;
ppaRef = NaN;
ppiRef = NaN;

spiValues = [];
spiTimes = [];
ppaRatioHist = [];
ppiRatioHist = [];

lastPrintedSPI = 0;
summary40Shown = false;
summary80Shown = false;
baselineMsgShown = false;
```
Este bloque reserva memoria y define el estado inicial de todas las variables del sistema.

La preasignación de t_ms, irRaw y xProc con zeros(Nmax, 1) es una práctica fundamental en MATLAB para evitar la reasignación dinámica de memoria en cada iteración del bucle, lo que degradaría severamente el rendimiento en tiempo real. El tamaño Nmax se calcula con un factor de seguridad de 2x sobre los datos esperados.
buffer acumula los fragmentos de texto recibidos por el puerto serial, ya que los datos pueden llegar parcialmente entre lecturas consecutivas. La lógica de parseo posterior separa las líneas completas de los fragmentos incompletos.
La variable estado implementa una máquina de estados de tres valores: 0 (neutro/inicio), 1 (señal en ascenso), -1 (señal en descenso). Este mecanismo, conocido como algoritmo del montañero o slope follower, detecta picos y valles sin necesidad de búsqueda en ventanas históricas.
hasValley actúa como condición de validez: un pico sistólico solo se registra si estuvo precedido por un valle diastólico en la misma onda, evitando falsos positivos al inicio de la adquisición.
Los vectores peak_times, peak_values, ppa_values y ppi_values crecen dinámicamente con cada latido detectado y constituyen la base de datos de características morfológicas de la señal PPG a lo largo del tiempo.
baselineReady actúa como bandera que separa el período de calibración basal del período de análisis activo. Hasta que no se hayan acumulado suficientes latidos en los primeros 40 s, el SPI no se calcula.

## Construcción de la interfaz gráfica en tiempo real
```
figRT = figure('Name','PPG en tiempo real','NumberTitle','off');
axRT = axes(figRT);
hold(axRT,'on');
grid(axRT,'on');

hSig = animatedline(axRT,'LineWidth',1.2);
hPeak = animatedline(axRT,'Color',[0.85 0.1 0.1],'Marker','o','LineStyle','none');
hVal = animatedline(axRT,'Color',[0.1 0.2 0.85],'Marker','v','LineStyle','none');

xlabel(axRT,'Tiempo (s)');
ylabel(axRT,'PPG procesada');
title(axRT,'Inicializando...');
xlim(axRT,[0 12]);

txtPhase = text(axRT,0.02,0.94,'Fase: Reposo', ...
    'Units','normalized','FontWeight','bold','BackgroundColor','w');

txtSPI = text(axRT,0.02,0.88,'SPI estimado: calibrando basal...', ...
    'Units','normalized','FontWeight','bold','BackgroundColor','w');

txtPeaks = text(axRT,0.02,0.82,'Picos detectados: 0', ...
    'Units','normalized','FontWeight','bold','BackgroundColor','w');

txtBPM = text(axRT,0.02,0.76,'BPM: --', ...
    'Units','normalized','FontWeight','bold','BackgroundColor','w');

lastDraw = tic;
tStart = tic;
```
Este bloque construye el panel de visualización en tiempo real que permite al operador monitorear la calidad de la señal y el progreso del análisis durante la sesión experimental.

animatedline es el objeto gráfico de MATLAB optimizado para visualización en tiempo real. A diferencia de plot, que redibuja toda la línea en cada actualización, animatedline solo añade los nuevos puntos al buffer interno, reduciendo drásticamente el costo computacional de la visualización.
Se crean tres líneas animadas independientes: la señal PPG procesada (gris/negro), los picos sistólicos detectados (rojo, marcador circular) y los valles diastólicos (azul, marcador triangular invertido). Esta codificación visual facilita la verificación en tiempo real de la correcta detección de la morfología del pulso.
Los cuatro objetos de texto (txtPhase, txtSPI, txtPeaks, txtBPM) se actualizan en el bucle principal con set(..., 'String', ...), que es significativamente más rápido que eliminar y recrear objetos de texto.
tStart = tic inicia el cronómetro de alta resolución de MATLAB que controla la duración total de la sesión. lastDraw = tic controla la frecuencia máxima de actualización gráfica, limitada a aproximadamente 33 fps (cada 30 ms) para no saturar el hilo de renderizado.

## Bucle principal de adquisición y procesamiento
```
while toc(tStart) < duration && ishandle(figRT)

    nAvail = s.NumBytesAvailable;

    if nAvail > 0
        chunk = read(s, nAvail, "char");
        buffer = buffer + string(chunk);
        lines = splitlines(buffer);

        if ~endsWith(buffer, newline)
            buffer = lines(end);
            lines = lines(1:end-1);
        else
            buffer = "";
        end

        for ii = 1:numel(lines)
            line = strtrim(lines(ii));
            if line == "", continue; end

            if startsWith(line,"READY") || startsWith(line,"STREAM") || ...
               startsWith(line,"ERROR") || startsWith(line,"CAL") || ...
               startsWith(line,"TH")
                disp(line);
                continue
            end

            parts = split(line, ",");
            if numel(parts) < 2
                continue
            end

            t_aux = str2double(parts(1));
            ir_aux = str2double(parts(2));

            if isnan(t_aux) || isnan(ir_aux)
                continue
            end
```
Este bloque implementa el núcleo del sistema de adquisición de datos en tiempo real, combinando lectura serial no bloqueante con parseo robusto del protocolo de comunicación.

La condición ishandle(figRT) permite al usuario cerrar la ventana gráfica para terminar la sesión anticipadamente de forma controlada, sin forzar una interrupción abrupta del script.
s.NumBytesAvailable consulta cuántos bytes están disponibles en el buffer de entrada del puerto serial sin bloquear la ejecución. Solo si hay datos disponibles se procede a la lectura, lo que convierte el bucle en no bloqueante (non-blocking I/O). Esto es fundamental para mantener la responsividad del sistema y la actualización continua de la interfaz gráfica.
La estrategia de acumulación en buffer y separación por splitlines maneja correctamente la fragmentación de paquetes: es frecuente que una lectura serial entregue una línea incompleta al final, que debe guardarse y completarse con el siguiente chunk. La lógica ~endsWith(buffer, newline) detecta este caso y preserva el fragmento incompleto.
Las líneas que comienzan con "READY", "STREAM", "ERROR", "CAL" o "TH" son mensajes de protocolo del firmware de la ESP32 (diagnóstico, calibración, umbrales) y se imprimen en consola sin procesamiento numérico.
El formato de datos esperado es timestamp_ms,valor_IR, separado por coma. La validación con isnan descarta cualquier línea malformada o corrupta sin interrumpir la captura.

## Procesamiento digital de la señal PPG
```
k = k + 1;
            if k > length(t_ms)
                t_ms = [t_ms; zeros(Nmax,1)];
                irRaw = [irRaw; zeros(Nmax,1)];
                xProc = [xProc; zeros(Nmax,1)];
            end

            t_ms(k) = t_aux;
            irRaw(k) = ir_aux;

            if isempty(t0_ms)
                t0_ms = t_aux;
            end

            t_now = (t_aux - t0_ms)/1000;

            winDC = max(1, round(dcWindowSec * fs));
            iDC = max(1, k - winDC + 1);
            dc = mean(double(irRaw(iDC:k)));

            iSm = max(1, k - smoothSamples + 1);
            ySm = mean(double(irRaw(iSm:k)));

            y = ySm - dc;

            if invertSignal
                y = -y;
            end

            xProc(k) = y;
```
Este bloque implementa la cadena de procesamiento digital aplicada a cada muestra recibida, transformando la señal cruda del fotosensor en una señal PPG normalizada y lista para detección de características.
La cadena de procesamiento consta de tres etapas en cascada:

Eliminación de componente DC por media móvil causal: La señal cruda del sensor infrarrojo contiene una componente de corriente continua (DC) mucho mayor en amplitud que la componente pulsátil de interés. Se estima la DC como la media de las últimas dcWindowSec × fs muestras (120 muestras = 1.2 s a 100 Hz) y se sustrae de la señal. Este filtro es equivalente a un filtro paso-alto con frecuencia de corte aproximada de 1/(2π × dcWindowSec) ≈ 0.13 Hz, eliminando tendencias lentas y deriva de la línea base sin afectar los componentes cardíacos (≈ 1 Hz y armónicos).
Suavizado por media móvil causal: Se calcula la media de las últimas smoothSamples (5) muestras de la señal cruda antes de la sustracción DC. Esto implementa un filtro FIR de respuesta al impulso rectangular que actúa como paso-bajo con frecuencia de corte aproximada de fs / smoothSamples = 20 Hz, atenuando el ruido de cuantización y las interferencias de alta frecuencia del ADC.
Inversión de señal: Dependiendo del diseño óptico del sensor (reflexión vs. transmisión) y de la orientación del fotodetector, la componente pulsátil puede aparecer como una depresión (mínimo local) en lugar de una elevación (máximo local) durante la sístole. La inversión garantiza que la detección de picos basada en máximos locales coincida correctamente con los picos sistólicos fisiológicos.

El resultado xProc(k) es la señal PPG procesada, centrada en cero, con amplitud proporcional a la variación del volumen sanguíneo en el lecho capilar del dedo.

## Detección de picos y valles (Algoritmo del Alpinista)
```
if k >= 3

                if xProc(k) > xProc(k-1)

                    if estado == -1
                        lastValleyIdx = k-1;
                        lastValleyValue = xProc(k-1);
                        hasValley = true;

                        valley_idx(end+1,1) = lastValleyIdx;
                        valley_times(end+1,1) = t_now - 1/fs;
                        valley_values(end+1,1) = lastValleyValue;

                        addpoints(hVal, valley_times(end), valley_values(end));
                    end

                    estado = 1;

                elseif xProc(k) < xProc(k-1)

                    if estado == 1 && hasValley
                        pk_idx = k-1;
                        pk_time = t_now - 1/fs;
                        pk_value = xProc(k-1);

                        if (pk_time - lastPeakTime) > refractoryTime
                            lastPeakTime = pk_time;

                            peak_idx(end+1,1) = pk_idx;
                            peak_times(end+1,1) = pk_time;
                            peak_values(end+1,1) = pk_value;

                            addpoints(hPeak, pk_time, pk_value);
```
Este bloque implementa el algoritmo del montañero (slope follower o peak-valley detector), un método de detección de extremos locales en tiempo real basado en el seguimiento de la pendiente de la señal.
El algoritmo mantiene una máquina de estados con dos estados activos:

Estado +1 (ascenso): La señal aumentó en la última muestra (xProc(k) > xProc(k-1)). El sistema está escalando hacia un máximo potencial. Si venía de estado -1 (bajada), el punto de inflexión es un valle (mínimo local, diástole), que se registra en k-1.
Estado -1 (descenso): La señal disminuyó en la última muestra (xProc(k) < xProc(k-1)). Si venía de estado +1 (subida), el punto de inflexión es un pico (máximo local, sístole), que se registra en k-1.

La condición hasValley exige que cada pico sistólico esté precedido por un valle diastólico validado en la misma onda, lo que elimina falsas detecciones al inicio de la captura o tras artefactos de movimiento que reinician el estado.
El período refractario (refractoryTime = 0.4 s) descarta cualquier pico detectado dentro de los 400 ms posteriores al último pico válido. Este umbral corresponde a una frecuencia cardíaca máxima de 150 BPM, por encima de la cual se considera que la detección es un artefacto. Esta técnica es análoga al período refractario absoluto del potencial de acción cardíaco, donde el tejido es inexcitable independientemente del estímulo.
La ventaja de este algoritmo respecto a métodos basados en ventanas deslizantes (Pan-Tompkins, umbral adaptativo) es su latencia mínima: el pico se detecta en la muestra inmediatamente posterior al máximo, con un retardo de exactamente 1/fs = 10 ms.

##  Extracción de características morfológicas del pulso
```
if ~isnan(lastValleyValue)
                                ppa_now = pk_value - lastValleyValue;
                            else
                                ppa_now = NaN;
                            end

                            if numel(peak_times) >= 2
                                ppi_now = peak_times(end) - peak_times(end-1);
                                currentBPM = 60 / ppi_now;
                            else
                                ppi_now = NaN;
                                currentBPM = NaN;
                            end

                            ppa_values(end+1,1) = ppa_now;
                            ppi_values(end+1,1) = ppi_now;

                            set(txtPeaks,'String',sprintf('Picos detectados: %d', numel(peak_times)));
                            if ~isnan(currentBPM)
                                set(txtBPM,'String',sprintf('BPM: %.1f', currentBPM));
                            end
```
Este bloque extrae las dos características morfológicas fundamentales de la señal PPG que constituyen los descriptores fisiológicos del SPI: el PPA y el PPI.
PPA — Pulse Peak Amplitude (Amplitud de Pulso):
El PPA se define como la diferencia de amplitud entre el pico sistólico y el valle diastólico inmediatamente precedente en la misma onda de pulso:
PPAi=xpico,i−xvalle,iPPA_i = x_{pico,i} - x_{valle,i}PPAi​=xpico,i​−xvalle,i​
El PPA es un indicador del tono vasomotor periférico. Cuando el sistema nervioso simpático se activa (respuesta al estrés), induce vasoconstricción en los lechos capilares periféricos, reduciendo el volumen de sangre que llega al dedo con cada latido. Esto se manifiesta como una reducción en el PPA. El CPT activa esta respuesta de forma reproducible y cuantificable.
PPI — Pulse-to-Pulse Interval (Intervalo Interpulso):
El PPI es el tiempo transcurrido entre dos picos sistólicos consecutivos:
PPIi=tpico,i−tpico,i−1PPI_i = t_{pico,i} - t_{pico,i-1}PPIi​=tpico,i​−tpico,i−1​
El PPI es el equivalente fotopletismográfico del intervalo RR del ECG. A partir de él se calcula la frecuencia cardíaca instantánea: BPM = 60 / PPI. El PPI es el reflejo más directo de la modulación autonómica del nódulo sinusal: la activación simpática acorta el PPI (taquicardia) mientras que el tono parasimpático lo prolonga (bradicardia). En el contexto del CPT, se espera una reducción del PPI (aumento de BPM) como respuesta simpática al estrés por frío.
Ambas variables son complementarias: el PPA refleja principalmente la rama vasomotora del sistema simpático, mientras que el PPI refleja la rama cronótropa (regulación de la frecuencia cardíaca). Su combinación ponderada en el SPI proporciona una estimación más robusta del estado autonómico que cualquiera de las dos por separado.

## Cálculo de la referencia basal y activación del SPI
```
if ~baselineReady && pk_time >= baselineDuration
                                [ppaRef, ppiRef, okRef] = estimateBaselineRefs( ...
                                    peak_times, ppa_values, ppi_values, baselineDuration);

                                if okRef
                                    baselineReady = true;

                                    [spiValues, spiTimes, ppaRatioHist, ppiRatioHist] = ...
                                        computeSPIClinicalScaled( ...
                                        peak_times, ppa_values, ppi_values, ...
                                        ppaRef, ppiRef, spiWinBeats, ...
                                        wPPA, wPPI, ratioMin, ratioMax, alphaSPI);

                                    if ~baselineMsgShown
                                        fprintf('\nReferencia basal lista:\n');
                                        fprintf('PPA_ref = %.6f\n', ppaRef);
                                        fprintf('PPI_ref = %.6f s\n\n', ppiRef);
                                        baselineMsgShown = true;
                                    end
                                end

                            elseif baselineReady
                                [spiValues, spiTimes, ppaRatioHist, ppiRatioHist] = ...
                                    computeSPIClinicalScaled( ...
                                    peak_times, ppa_values, ppi_values, ...
                                    ppaRef, ppiRef, spiWinBeats, ...
                                    wPPA, wPPI, ratioMin, ratioMax, alphaSPI);
                            end
```
Este bloque implementa la calibración individual del sistema, que es el mecanismo que hace al SPI independiente de las diferencias anatómicas y fisiológicas entre sujetos.

El enfoque de normalización intra-sujeto es fundamental. Dado que el PPA y el PPI tienen valores absolutos que varían considerablemente entre individuos, debido a diferencias en el grosor del dedo, pigmentación, temperatura basal, tono vascular en reposo y frecuencia cardíaca en reposo, no es posible establecer umbrales fijos de estrés aplicables universalmente. La solución es normalizar cada variable respecto a sus propios valores de referencia en reposo, de manera que el índice resultante mida cambios relativos respecto al estado basal del propio sujeto.

La función estimateBaselineRefs calcula las referencias como la mediana de los valores válidos durante los primeros 40 segundos. Para ello, toma únicamente los latidos cuyo tiempo sea menor o igual a 40 s, con valores de PPA mayores que cero y valores de PPI dentro del rango de 0.3 a 2.0 segundos.

Se utiliza la mediana en lugar de la media porque es un estimador robusto frente a valores atípicos, los cuales en señales PPG pueden provenir de artefactos de movimiento, latidos ectópicos o interferencias electromagnéticas durante la fase de reposo.

El criterio de validez okRef exige un mínimo de 8 latidos válidos, lo que corresponde aproximadamente a entre 8 y 10 segundos en una frecuencia cardíaca normal. Esto garantiza que la referencia tenga suficiente representatividad estadística. Si no se alcanza este mínimo, el sistema espera y no calcula el SPI hasta contar con una referencia confiable.

## Actualización de la interfaz y control de fases experimentales
```
elapsed = toc(tStart);

    if elapsed < baselineDuration
        set(txtPhase,'String','Fase: Reposo');
    elseif elapsed < baselineDuration + cptDuration
        set(txtPhase,'String','Fase: CPT');
    else
        set(txtPhase,'String','Fase: Recuperación');
    end

    if baselineReady && ~summary40Shown
        idx1 = spiTimes <= baselineDuration;
        if any(idx1)
            fprintf('SPI medio reposo (0-40 s): %.2f\n', mean(spiValues(idx1),'omitnan'));
        end
        summary40Shown = true;
    end

    if baselineReady && ~summary80Shown && elapsed >= baselineDuration + cptDuration
        idx2 = spiTimes > baselineDuration & spiTimes <= baselineDuration + cptDuration;
        if any(idx2)
            fprintf('SPI medio CPT (40-80 s): %.2f\n\n', mean(spiValues(idx2),'omitnan'));
        end
        summary80Shown = true;
    end

    if toc(lastDraw) > 0.03 && k > 5
        tLast = (t_ms(k) - t_ms(1))/1000;
        xlim(axRT, [max(0,tLast-12), max(12,tLast)]);

        nWin = min(k, round(8*fs));
        yWin = xProc(k-nWin+1:k);
        yCenter = median(yWin);
        yMad = median(abs(yWin - yCenter));
        yHalf = max(0.2, 8*yMad);

        ylim(axRT, [yCenter-yHalf, yCenter+yHalf]);
        drawnow limitrate nocallbacks
        lastDraw = tic;
    end
end
```
Este bloque gestiona la visualización dinámica y el reporte de estadísticas por fase durante la adquisición.

El protocolo experimental sigue tres fases secuenciales: Reposo (0 a 40 s, calibración basal), CPT (40 a 80 s, estímulo estresante) y Recuperación (80 s en adelante, retorno al estado basal). La indicación visual de la fase activa en pantalla orienta al operador durante la sesión sin necesidad de consultar un cronómetro externo.

Las banderas summary40Shown y summary80Shown garantizan que los resúmenes estadísticos se imprimen exactamente una vez al completarse cada fase, evitando salidas redundantes en consola.

El escalado automático del eje Y utiliza la desviación absoluta mediana, o MAD, de la señal en una ventana de 8 segundos. La MAD se calcula como la mediana del valor absoluto de la diferencia entre cada muestra y la mediana de la señal en esa ventana.

La MAD es un estimador de dispersión robusto frente a artefactos. A diferencia de la desviación estándar, no se ve distorsionada por valores atípicos grandes causados por movimientos súbitos del dedo. El rango del eje se fija en 8 veces la MAD alrededor de la mediana, lo que garantiza que la señal ocupe apropiadamente la ventana visual incluso ante cambios en la amplitud del pulso.

La instrucción drawnow limitrate nocallbacks actualiza la figura de forma eficiente. La opción limitrate limita la frecuencia de renderizado a la tasa máxima que el sistema gráfico puede manejar sin saturarse, mientras que nocallbacks evita que los eventos de interfaz, como clics o redimensionados, interrumpan el procesamiento numérico durante la actualización.

## Resumen final y generación de figuras de análisis
```
fprintf('\n===== RESUMEN FINAL =====\n');
fprintf('Muestras recibidas: %d\n', numel(irRaw));
fprintf('Picos detectados : %d\n', numel(peak_times));
fprintf('PPA_ref          : %.6f\n', ppaRef);
fprintf('PPI_ref          : %.6f s\n', ppiRef);

idxRest = spiTimes <= baselineDuration;
idxCPT = spiTimes > baselineDuration & spiTimes <= baselineDuration + cptDuration;
idxRec = spiTimes > baselineDuration + cptDuration;

if any(idxRest)
    fprintf('SPI medio reposo (0-40 s)   : %.2f\n', mean(spiValues(idxRest),'omitnan'));
end
if any(idxCPT)
    fprintf('SPI medio CPT (40-80 s)     : %.2f\n', mean(spiValues(idxCPT),'omitnan'));
end
if any(idxRec)
    fprintf('SPI medio recuperación      : %.2f\n', mean(spiValues(idxRec),'omitnan'));
end
```
Al finalizar la sesión de captura, este bloque consolida los resultados numéricos por fase experimental. El SPI medio de cada fase es la métrica primaria de comparación:

SPI reposo ≈ 50: Por diseño del escalado, el basal centrado en 50 significa que el sujeto está en un estado autonómico de referencia.
SPI CPT > 50: Un incremento del SPI durante el CPT indica activación simpática, consistente con la respuesta esperada al estrés por frío: vasoconstricción periférica (reducción de PPA) y taquicardia relativa (reducción de PPI).
SPI recuperación → 50: La vuelta progresiva del SPI hacia 50 refleja la recuperación del tono autonómico basal tras retirar el estímulo estresante.

Las figuras generadas a continuación permiten una validación visual completa de la sesión, mostrando la señal cruda, la señal procesada, la detección de picos y valles, la evolución temporal del SPI y las razones normalizadas de PPA y PPI.

##  Función estimateBaselineRefs
```
function [ppaRef, ppiRef, okRef] = estimateBaselineRefs(peak_times, ppa_values, ppi_values, baselineDuration)

    idx = peak_times <= baselineDuration & ...
          ~isnan(ppa_values) & ppa_values > 0 & ...
          ~isnan(ppi_values) & ppi_values >= 0.3 & ppi_values <= 2.0;

    if nnz(idx) < 8
        ppaRef = NaN;
        ppiRef = NaN;
        okRef = false;
        return
    end

    ppaRef = median(ppa_values(idx), 'omitnan');
    ppiRef = median(ppi_values(idx), 'omitnan');

    okRef = isfinite(ppaRef) && isfinite(ppiRef) && ppaRef > 0 && ppiRef > 0;
end
```
Esta función implementa la estimación de las referencias basales con múltiples capas de validación fisiológica.
Los criterios de filtrado aplicados sobre los latidos de la fase de reposo son:

ppa_values > 0: El PPA debe ser estrictamente positivo, garantizando que la amplitud del pico supere a la del valle (morfología fisiológicamente normal).
ppi_values >= 0.3 && ppi_values <= 2.0: El PPI debe estar en el rango de 300 ms a 2000 ms, correspondiente a frecuencias cardíacas entre 30 y 200 BPM. Valores fuera de este rango indican artefactos de detección (dobles detecciones, latidos perdidos) que contaminarían la referencia.
nnz(idx) >= 8: Exige al menos 8 latidos válidos para que la mediana tenga significado estadístico. Con 8 puntos, la mediana está determinada por los dos valores centrales y es relativamente estable frente a 1–2 outliers.

La elección de la mediana como estimador de centralidad es deliberada: es resistente a la presencia de hasta el 50% de valores atípicos (punto de ruptura del 50%), lo que la hace significativamente más robusta que la media aritmética en señales biológicas con artefactos esporádicos.

## Función computeSPIClinicalScaled
```
function [spiValues, spiTimes, ppaRatioHist, ppiRatioHist] = ...
    computeSPIClinicalScaled(peak_times, ppa_values, ppi_values, ...
    ppaRef, ppiRef, winBeats, wPPA, wPPI, ratioMin, ratioMax, alphaSPI)

    n = numel(peak_times);
    spiFull = nan(n,1);
    ppaRatioHist = nan(n,1);
    ppiRatioHist = nan(n,1);

    for i = 1:n
        ppa = ppa_values(i);
        ppi = ppi_values(i);

        if isnan(ppa) || isnan(ppi) || ppa <= 0 || ppi < 0.3 || ppi > 2.0
            continue
        end

        ppaRatioHist(i) = min(max(ppa / ppaRef, ratioMin), ratioMax);
        ppiRatioHist(i) = min(max(ppi / ppiRef, ratioMin), ratioMax);

        valid = find(~isnan(ppaRatioHist(1:i)) & ~isnan(ppiRatioHist(1:i)));
        if numel(valid) < 3
            continue
        end

        valid = valid(max(1, numel(valid)-winBeats+1):end);

        ppaBar = mean(ppaRatioHist(valid), 'omitnan');
        ppiBar = mean(ppiRatioHist(valid), 'omitnan');

        stressScore = wPPA * (1 - ppaBar) + wPPI * (1 - ppiBar);

        spiInst = 50 + 50 * stressScore;
        spiInst = max(0, min(100, spiInst));

        lastValidIdx = find(~isnan(spiFull), 1, 'last');
        if ~isempty(lastValidIdx)
            prev = spiFull(lastValidIdx);
            spiInst = alphaSPI * spiInst + (1 - alphaSPI) * prev;
        end

        spiFull(i) = spiInst;
    end

    idxValid = ~isnan(spiFull);
    spiValues = spiFull(idxValid);
    spiTimes = peak_times(idxValid);
end
```
Esta es la función central del sistema: implementa el cálculo del Stress Photoplethysmography Index (SPI) a partir de las razones normalizadas de PPA y PPI.

El algoritmo procede en cuatro etapas por cada latido:

1. Normalización y limitación de rango

Primero se calcula la razón entre el valor actual y su valor de referencia basal tanto para el PPA como para el PPI. Luego, estas razones se limitan al intervalo entre 0.20 y 2.00 mediante una función de recorte o clip.

La función clip evita que artefactos extremos, por ejemplo un PPA casi nulo debido a movimiento, generen valores de SPI fuera del rango interpretable. En condiciones normales, estas razones oscilan alrededor de 1.0 durante el reposo.

2. Suavizado por ventana de latidos

Después, se promedian las últimas winBeats = 5 razones válidas de PPA y PPI. Este promedio reduce la variabilidad natural latido a latido presente en las señales fisiológicas, como la variabilidad normal de la frecuencia cardíaca, sin introducir una latencia excesiva en la detección de cambios asociados al estrés.

3. Cálculo del puntaje de estrés y escalado al rango de 0 a 100

A partir de esos promedios suavizados se calcula un puntaje de estrés ponderado, usando los pesos asignados a PPA y PPI. La lógica del índice es la siguiente: en reposo, los valores normalizados promediados de PPA y PPI son cercanos a 1, por lo que el puntaje de estrés es cercano a 0 y el SPI se ubica alrededor de 50.

Bajo estrés, la vasoconstricción reduce el PPA y la taquicardia reduce el PPI. Como ambos valores promediados pasan a ser menores que 1, los términos del puntaje de estrés se vuelven positivos y el SPI aumenta por encima de 50. El escalado aplicado permite que el índice se exprese en un rango teórico de 0 a 100.

4. Suavizado exponencial temporal

Finalmente, el SPI instantáneo se filtra mediante un suavizado exponencial, combinando una fracción del valor actual con una fracción del valor anterior. Con alpha = 0.25, este filtro tiene una constante de tiempo aproximada de 3.5 latidos, lo que equivale a unos 3 a 4 segundos para una frecuencia cardíaca normal.

Este suavizado proporciona mayor estabilidad visual al índice, evitando oscilaciones bruscas, pero sin ocultar tendencias sostenidas como las inducidas por el CPT.

##  Función de limpieza del puerto serial
```
function clearSerialPort(s)
    try
        write(s, "P", "char");
    catch
    end
    try
        flush(s);
    catch
    end
    try
        clear s
    catch
    end
end
```
Esta función de limpieza se ejecuta automáticamente al finalizar el script gracias al objeto onCleanup registrado al inicio. Su propósito es garantizar la liberación segura de recursos de hardware independientemente de cómo termine la ejecución del programa.

La escritura del carácter "P" (Pause/Stop) envía la señal de protocolo que ordena al firmware de la ESP32 detener la transmisión de datos, evitando que el microcontrolador continúe transmitiendo en modo streaming hacia un puerto que ya no está siendo leído.
El uso de bloques try-catch independientes para cada operación garantiza que si una falla (por ejemplo, el puerto ya fue desconectado físicamente), las siguientes operaciones de limpieza se ejecuten de todas formas.
Liberar correctamente el objeto serialport es crítico en MATLAB: si el objeto no se destruye apropiadamente, el puerto COM queda bloqueado y no puede ser abierto nuevamente hasta reiniciar MATLAB o desconectar y reconectar el dispositivo USB.

[![spi1.jpg](https://i.postimg.cc/kM0r8GRC/spi1.jpg)](https://postimg.cc/F1VnX9xC)
[![spi2.jpg](https://i.postimg.cc/Gmb5Jycx/spi2.jpg)](https://postimg.cc/1fYBy4V8)
[![spi3.jpg](https://i.postimg.cc/pd6cHcKq/spi3.jpg)](https://postimg.cc/1gDr0HCq)
[![spi4.jpg](https://i.postimg.cc/k4R1XNc8/spi4.jpg)](https://postimg.cc/1nPcvqb5)

### Datos obtenidos
===== RESUMEN FINAL =====
Muestras recibidas: 3006

Picos detectados : 175

PPA_ref          : 766.958333

PPI_ref          : 0.720000 s

SPI medio reposo (0-40 s)   : 49.89

SPI medio CPT (40-80 s)     : 60.35

SPI medio recuperación      : 50.56



## Análisis
• Análisis 1: Compare los valores del SPI obtenidos durante la práctica con
los que frecuentemente se observan durante una cirugía para proporcionar
el nivel óptimo de anestesia.
Los resultados obtenidos muestran un comportamiento coherente del SPI a lo largo de las tres fases del experimento. Durante la etapa de reposo (0–40 s), el SPI presentó un valor medio de 49.89, ubicándose dentro del rango clínico recomendado (20–50) para una adecuada analgesia intraoperatoria.

Al aplicar el Cold Pressor Test (40–80 s), el SPI aumentó a un valor medio de 60.35, superando el rango óptimo. Este incremento es consistente con una mayor activación del sistema nervioso simpático ante un estímulo nociceptivo, lo que se traduce en vasoconstricción periférica y aumento de la frecuencia cardíaca.

Finalmente, en la etapa de recuperación, el SPI descendió a 50.56, aproximándose nuevamente al valor basal, lo que indica una disminución de la respuesta al estrés una vez retirado el estímulo.

Al comparar estos resultados con el contexto clínico, se observa que, aunque el sujeto no se encontraba bajo anestesia, el comportamiento del SPI sigue la misma lógica fisiológica: valores más altos reflejan mayor nocicepción. Sin embargo, los valores en reposo se encuentran cerca del límite superior del rango clínico, lo cual puede deberse a que el sujeto estaba consciente y a factores como estrés basal o condiciones experimentales.

En conjunto, los resultados confirman que el sistema desarrollado es capaz de detectar cambios fisiológicos asociados a la nocicepción de manera consistente con lo reportado en la literatura.

• Análisis 2: Evalúe el alcance y las posibles limitaciones de emplear el
sistema desarrollado para cuantificar el dolor que percibe una persona.

El sistema desarrollado, basado en la señal PPG y el cálculo del SPI, permite evaluar de forma objetiva la respuesta fisiológica asociada a la nocicepción, a partir de cambios en la amplitud del pulso (PPGA) y el intervalo entre latidos (HBI). Esto lo convierte en una herramienta útil para monitorear el estrés y la activación del sistema nervioso autónomo de manera continua y no invasiva, especialmente en contextos donde el paciente no puede comunicar dolor.

Sin embargo, su principal limitación es que el SPI no mide el dolor directamente, sino la nocicepción. El dolor es una experiencia subjetiva influenciada por factores emocionales y psicológicos que no se reflejan completamente en señales fisiológicas.

Además, la señal PPG puede verse afectada por factores como el movimiento, la presión del sensor o la perfusión periférica, lo que impacta la precisión del SPI. También, respuestas fisiológicas similares pueden generarse por estrés o ansiedad, lo que puede llevar a interpretaciones erróneas.

En conclusión, el sistema es útil para evaluar cambios fisiológicos asociados al estrés o nocicepción, pero debe considerarse como un indicador complementario, no como una medida directa del dolor.

## Conclusiones
Con los resultados obtenidos, se puede decir que la práctica se desarrolló de forma exitosa y que el sistema respondió como se esperaba frente a cada etapa del protocolo. En la fase de reposo, el SPI medio fue de 49.89, un valor muy cercano a 50, lo cual es coherente con la lógica de calibración del algoritmo, ya que el estado basal del sujeto se centra alrededor de ese valor. Durante la fase del Cold Pressor Test (CPT), el SPI aumentó hasta un promedio de 60.35, mostrando que el sistema sí fue sensible a los cambios fisiológicos producidos por el estímulo estresante, especialmente a la disminución del PPA y del PPI asociada a la respuesta simpática. Posteriormente, en la fase de recuperación, el SPI descendió a 50.56, acercándose nuevamente al nivel basal, lo que indica que el sujeto presentó una tendencia de retorno al estado inicial y que el índice logró reflejar correctamente esa transición.

Además, la adquisición de la señal fue adecuada, ya que se recibieron 3006 muestras y se detectaron 175 picos, lo que confirma que tanto la lectura del sensor como el procesamiento de la señal y el algoritmo de detección de máximos y mínimos funcionaron correctamente. En general, los resultados muestran que el sistema implementado no solo permitió calcular el SPI de manera estable, sino que también logró diferenciar claramente las fases de reposo, estrés y recuperación. Por tanto, se puede concluir que se cumplió satisfactoriamente con el objetivo de la guía, obteniendo un comportamiento experimental consistente con la respuesta fisiológica esperada ante el CPT y validando el uso del SPI como una medida útil para el seguimiento de cambios autonómicos en tiempo real.
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

