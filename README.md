#  Piero Indiviudal Jose Luis Muñoz
  Como objetivo  principal de este artículo es describir los conocimientos aprendidos y mi experiencia durante el proyecto del
"Piero". Este proyecto se centra en la creación y el progreso del Piero, así como la implementación de programas que le permitan recorrer un camino ya preestablecido, asi como salir del aula. También se le ha implemnetado una navegación reactiva, es decir que mientras se realizando la trayectoria sea capaz de esquivar objetos.

# Montaje de Robot Piero 
  Los miembros que pertenecen al grupo 17, hemos montado individualmente un Piero propio. Siguinedo para nuestro caso, el montaje descrito en el siguinte documento. Además de seguir los enseñado en clase, como proyecto personal, he implementado un esquema  propio  para los diferentes bloques usados. Ha este poryecto lo hemos denominado " Final Project" (siendo los archivos realaciona dos Fp_namefile)


# Hardware
A continuación vamos a contar el hardware que se ha usado para el montaje del Piero: 
  
  ## Arduino Mega 2560
  Como controlador del Piero vamos a utilizar una tarjeta de la compañia Arudino que nos va a permitir poder manejar todos la
  informacion captada por los sensores usados para poder realizar el control del Piero. Para poder usar todos los sensores es  necesario disponer de multiples pines tanto analogicos como digitales. Es por eso que se ha escogido el arduino Mega 2560:

### Puertos de Arduino Mega 2560
![Pines Arduino Mega 2560 Rev3](https://lab.bricogeek.com/assets/10/Pinout-Mega2560rev3_latest.png)

## Alimenación de circuitos 
Para alimentar todo el sistema del Piero vamos a utilizar una fuente de tensión de 12 V y para ello usaremos tres pilas de 4 V en serie. Para conocer la carga de la bateria usada vamos añadir un multimetro para ver por un display la tensión de la bateria y un medidor de tensión que irá conectado al Arduino.

  ## Sonars 
  Para nuestro piero se han escogido dos sensores ultrasonidos US-016 , el cual cuanta con 5 pines: Vcc (alimentación 5V),
  Range (alimentacion 3V), Out (salida analógica) y Gnd (conectada a tierra).
  ![](https://github.com/user-attachments/assets/c5cc2bf4-af3b-4ca8-bf87-d0f26b45caae)

## Motores 
Para el Piero se han usado dos motores con encoders incorporados mediante el módulo L298N. Dicho motores necesitan una  alimentación de 12 V que como hemos dicho antes. En la siguidnte imagen se pueden observar tanto el esquema de alimentación como el de los motores.
![](https://github.com/user-attachments/assets/e8439ba3-9fba-4890-902f-90ffcea61b91)

## Led
Ahora mostraremos la conexion del led RGB usado en el Piero: 
  ![](https://github.com/user-attachments/assets/d6dd5988-c9a2-416b-8084-4a7be0a593ac)

# Arduino en Simulink 
Para realizar la programación del Piero se crearan distinos subsistemas de simulink para simular el hardware. Se usará la libreria *"Simulink Support Package for Arduino Hardware"*.

## Sonars 
Para poder gestionar la información que proporcionan los sensores de ultrasonidos se debe usar los siguintes bloques: 
- Analog Input: mide el voltaje del pin de la entrada analógica, que puede tomar el valor en el rango entre 0 y 1023. En nuestro caso, los pines que se usarán para realizar las lecturas son el puerto A0 (sensor izquierdo) y el puerto A1 (sensor derecho).
- Gain: Debido a que la información otorgada por el analog input es un tensión, usaremos este bloque para realizar una conversión de voltajes a cm.

Para generar el subsitema necesario para los sonars se ha seguido los iguintes pasos:
1. Primero se ha implementado la lógica necesaria para recoger los datos de entrada de los pines A0 y A1, dihos datos los uniremos bajo un multiplexor (colocaremos primero los datos de la izquierda). Posteriormente la información la multiplicaremos por una ganancia (conversión a cm).
2. Para calcular la ganancia, conocemos que las entradas analógicas van desde los valores 0 hasta 1023 (podemos verlo si miramos el datasheet del sensor). Sabiendo esto podemos dividir la información que nos da por $\frac{1}{1023}$ lo que nos dará un valor en metro, nos falta pues realizar la conversión a cm. Esto nos deja que la ganancia final para los sonars es  $\frac{100}{1023}$
   ![](https://github.com/user-attachments/assets/97b1d5d8-2967-4385-90a9-e6498016cacf)

Para poder realizar las pruebas vamos a crear un nuevo modelo en simulink al que llamaremos "Test_Sensores" y en este con el uso de de Subsystem reference podremos cargar el subsistema creado y vemos los datos de salida mediante un display.
![image](https://github.com/user-attachments/assets/ec701d87-a5e1-4efb-98e5-6fc43a455795)

## Motores
En el control de los motores vamos a utilizar los siguintes bloques de simulink:
- PWM:Se usará para indicar la potencia que se le suministrará al motor y así poder realizar un control de la velocidad del Piero. Los valores van desde el valor más bajo 0 a 255 más alto.
- Digital Output: Vamos a usar un puerto adicionalmente que nor permitirá activar el motor o no. Para los motores usado en nuestro Piero no disponemos de dicho control enable.
- Saturation : Permite que la señal quedé limitada entre un rango de valores limitantes. Si los valor es superior a alguno de los limites, el bloque devuelve el valor del limite idnicado.

  Comenzamos generando un subssistema de referencia que permita controlar los motores hacia adelante o hacia atras. Para ello cada uno de los motores es contorlado por 3 pines, 2 pines de pwm asociados a los desplazamiento hacia adelante o hacia atras y otro asociado al enable. Para los desplazamientos hacia atras lo indicaremos con valores negativos (0 y -255). Como el bloque PWM de simulink no lee valores negativos usaremos un bloque abs ( valor absoluto ) para los valores negativos.

  En el control del enable, usaremos un compare to constant para indicar por el puerto enable cuando el valor es diferente a 0 y asi poder activar el motor.

  Como entrada, el bloque dispondrá de una señal con dos valores, uno para cada motor y usaremos un multiplexor y asi podremos separar ambas señales para gestionar el control de los motores.

  ![](https://github.com/user-attachments/assets/68ba5336-f18f-4230-a404-aa528eef001f)




  
   
