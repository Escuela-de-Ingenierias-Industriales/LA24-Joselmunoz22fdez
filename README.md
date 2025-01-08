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

Para probar si funciona el digrama generado en simulik vamos a generar un archivo de Test:
![](https://github.com/user-attachments/assets/2021d2cf-0189-49cf-831d-476ab99e48c3)

## Encoders
  El sibsistema de encoders será el encargado de darnos los valores de velocidad de cada uno de las ruedas y asi poder realizar unas serie de estimaciones que sean necesarias. Para ello usaremos los siguintes bloques:
  - Encoders : lee la posición y la velocidad rotacional del motor desde un encoder conectado al motor. Este bloque como salida otorga el número de flancos medidos desde que se inicio el sistema.
  - Data Type conversion : Permite la conversión de valores.
  - Gain : Permite convertir el número de flancos en la distancia  ( necesitaremos conocer los valores especificos de las ruedas.
  - Discrete Derivative : Realiza una derivación en tiempo discreto y asi obtener la derivada de la posición, definida como la velocidad.

1. Se crea un subsistema vacíom y se añaden los bloques de enconders, indicando los pines de lectura 18,19 para la rueda derecha y 2 y 3 para la  rueda izquierda. Ambas señales las multiplexamos y la multiplicamos por la ganancia para concer la distancia.
2. Se calcula la ganancia de los enconders, para eso realizamos un giro de los motores de manera manual y asi conocemos el número de flancos. Para pasar del movimiento angular a distancia, la multiplicamos por la circunferencia de la rueda, es decir $\pi$ x diámetro. Eso lo dividiremos entre el número de flancos medidos en 10 vueltas y asi normalizamos. PAra obtener la velocidad añadimos el bloque de Discrete Dereivative para poder obtener la velocidad en  $\frac{m}{s}$.
   ![image](https://github.com/user-attachments/assets/6c011bdd-d0c9-4b8f-8367-9b7c7d94ae5f)


## Leds Sensores
  Generaremos la lógica de control que nos permitirá ver mediante un Led elestado de la bateria del Piero para ello haremos usos de:
  - Digital Output: bloque de salida digital que pondrá a 1 o a 0 el pin digital seleccionado. Lo usamos para encender o apagar el led RGB.
  - Puertas Logicas : Puertas de logica digital tales como las  Puertas AND, OR , NAND y NOR.
  - Compare to constant: Si el valor entrante es igual a una constante definia, el bloque devolverá  un 1 logico, en caso, de no ser este devuelve un 0 lógico.

# Navegación Reactiva Báscica
  Con los bloques subsistemas generados ya podremos realizar una navegación que nos permitá evitar los obstaculos. Como entrada de los motores colocaremos la señal obtenida por los sensores y los multiplicaremos por una ganancia. Cuando se detecte un objeto por un lateral, la distancia se reducirá y producirá una disminución de los motores generando un giro evitando el obstaculo. 

# Simulación del sistema
  Para realizar las pruebas mediante simulaciones, primerdo deberemos obtener la función de trasnferencia del sistema. Eso lo podremos realizar mediante la respuesta del sistema mediante una señalde tipo escalos y una señal rampa. Para generar dichas funciones generaremos el bloque "Signal Builder" Dichas señales tendrán valores unitarios y luego se multiplicarán por una ganancia. 
![](https://github.com/user-attachments/assets/a9805cee-087e-48a4-b77f-d550cad2b354)

Para poder obtener los valores del sistema, generaremos un bloque de sistema formado por el Hardware del Piero que lo conforman los subsistema de control de motores y el subsistema de encoders. Como entrada al sistema tendremos las señales que hemos definido anteiormente y como sistema de salida tendremos los datos obtenidos por los encoders. Todo estos datos los exportaremos al workspace para poder el estudio del sistema y poder obtener las funciones que modelen el sistema.
![](https://github.com/user-attachments/assets/232bda7f-26cb-4348-bf07-47e5df763710)

Siendo el sistema de estudio:

![](https://github.com/user-attachments/assets/765387e0-368b-424d-9ece-8e540c6e783d)

#Ident 
Con los datos que se han obtenido que se han obtenido de la simulación frente a la entrada escalon, podemos obtener la función de trasnferencia que imiten al comportamiento de nuestro Piero especificamente. Para poder obtener dicha función se puede usar la herramienta de Matlab *"System Identification"*,donde cargaremos tantos los datos que se han obtenido de la rueda izquierda como la de la derecha. UNa vez cargado los datos que nos interes,con las señales se procederá a cear las funciones dedeadas. 

De estos datos sacaremos diversas funciones, variaremos el número de polos y de ceros y escogeremos la función que más se acerque al comportamiento deseados. 
![](https://github.com/user-attachments/assets/698f8849-bcfb-433a-ab78-7e3e117e3a2a)

Escogida la funcion que queremos generaremos un subsistema que nos permitirá realizar simulaciones del robot. Este subsistema lo denominaremos Piero Model que contendrádos Discrete Trnasfer Fcn, las cuales incorporaremos los datos de las funciones obtenidas anteriormente.
![image](https://github.com/user-attachments/assets/3fa5b6d4-f526-4724-88db-59010a912d6d)

Para facilitar el desarrollo del Piero y poder realizar tanto simulaciones como poder probar el hardware, crearemos un subsistema, el cual simula a nuestro Piero en ambos casos "Piero.slx". Para que esto se realize de manera automática usaremos el bloque "Variant Source", el cual tiene dos entradas, y según si estamos en simulación o en hardware, selecciona el bloque pertinente.
![image](https://github.com/user-attachments/assets/03443706-8221-41da-b312-ecef15992557)

## Control Bucle Cerrado 
Para realizar un control ante perturbaciones, realizaremos un control mediante Bucle cerrado. Para ello, creamos un subsistema "Control_BC". Este contará con controladores PID (uno para cada motor) a la salida de estos se encontrará el subsistema de referencia Piero el cual se encuentra en realimentación con la entrada de los controladores. De esta manera podremos realizar un control para los motores del Piero. 

Para el diseño de los controladores, usaremos el PID tuning implementado en Matlab y buscamos un comportamiento deseado.
![image](https://github.com/user-attachments/assets/0dceda79-dd31-466c-a49c-77484b866345)

Siendo el subsistema:
![image](https://github.com/user-attachments/assets/8744ac42-0d49-4114-8799-817631a7c143)

# Cinemática y Control de Orientación
## Cinemática 
Siguinedo con el estudio del comportamiento del Piero, estudiaremos la cinemática diferencial, esto nos permitirá entender como se mueve el robot y como varían las velocidades y orientaciones según la consigna de control en dicho instante. Para ver como se despalza por espacio, consideraremos este como un espacio de dos dimensiones (Plano XY) 

A este modelo cinemático directo le deberemos indicar las velocidades articulares (de cada una de las ruedas) y obtendremos las velocidades cartesianas (Velocidad lineal y Velocidad angular). Para obtener estos valores utilizaremos la cinemática directa. En este bloque tendremos una matriz que lleve el calculo de las operaciones necesarias para pasar de velocidades articulares a cartesianas . 

![image](https://github.com/user-attachments/assets/80c7bdbe-3bce-4baf-9efb-feff00555beb)

El modelo cinématico inverso realizará la funcion contraria a la descrita antes. A unas velocidades cartesianas devuelve sus correspondiente velocidades articulares. También generaremos otro bloque que encargue de realizar estos calcilos. El mismo proceso que antes hemos hecho. 

![image](https://github.com/user-attachments/assets/b28fb2e6-5f4d-46d9-87c3-bac5466a6d1c)

Unicamnete ya nos queda obtener la odometría y así conocer la posición dle robot en un plano xy. Para conocer la estimación de la posición y la orientación usaremos las velocidades cartesianas. Pimero debemos serparar ambas velocidades en las dos direcciones Vx y Vy usando senos y cosenos. Tras esto podemos integrar las velocidades y obtenemos la posición x e y y el ángulo theta. Finalmente las magnitudes se pondrán represetar en un gráfico Xy. 
![image](https://github.com/user-attachments/assets/3543c2c4-6486-4737-b51d-27f191debad0)

Una vez hemos creado todos los subsistemas pertinentes, podemos proceder a crear un modelo para ver el comportamiento del sistema. En el la consigna de entrada será una velocidad linear y una angular, por lo que deberemos aplicar el MCI para pasarlo a velocidades articulares y que nuestro Piero pueda leerlas y realizar los movmientos pertinentes. Tras esto se aplicará el MCD con el fín ed volver a obtener velocidades cartesianas y poedr tanto ver la salida más clara como poder representar correctamente el movimiento en el XY graph.
![image](https://github.com/user-attachments/assets/a90cfdf3-773e-48e0-92e1-923074d2f326)

Si realizamos una simulación donde asignamos unas velociades lineal  y angular podemos ver el siguinte comportamiento.

![image](https://github.com/user-attachments/assets/29acfe74-c835-4794-b80e-d50fd0d2681e)

## Control por orientación
Al no poseer ninguna realimentación, tendremos los mismos fallos que un sistema en bucle abierto por lo que el Piero no podrá realizar una orientación  y por ende no podrá seguir la trayectoria deseada, deberemos realizar un blucle realimentado para el ángulo de rotación theta.

## Control de Trayectorias 
  Para poder implementar las diferentes trayectorias y que nuestro Piero sea capaz de seguirlas, se modificarán las consignas de entradas que serán más complejas ya que estas varian su valor según convenga. Para nuestro caso marcaremos una trayectoria para salir de calse.
1. 4 metros en línea recta
2. Giro antihorario 90º
3. 12 metros en línea recta
4. Giro horario 90º
5. 0.8 metros en línea recta
6. Giro antihorario 90º
7. 2 metros en linea recta

Para ello podremos hacerlo usamos distintas generamos diferentes señalesque simulan las velocidades lineales y angulares con un signal builder.
![image](https://github.com/user-attachments/assets/aa4411f9-396a-46b4-b6dd-377e9d658215)

## Diagramas de estado 
Otra manera en la que podemos realizar la trayectoria es mediante un diagrama de estados, los cuales aporta mejor visibilidad de la trayectoria  y mejor manejo intuitivo del Piero. Este diagrama d estado la podemos realizar mediante "Stateflow", mediante el bloque Chart. Según la posición en la que nos encontremosm el Piero tomará una acción u otra.
![image](https://github.com/user-attachments/assets/06ca7c1e-10f5-4f93-986f-6b232f185c50)
Siendo el sistema igual a: 
![image](https://github.com/user-attachments/assets/c428f9e8-5b26-4a09-992c-cbe95a5cc97c)

#Mfunction
El bloque que vamos a usar es el Mfuction, el cual se trata de un bloque especifico de Simulink, en el cual podemos escibir una función de Matlab. Esta función va a atener como salida la velocidad, el ángulo tehta. PAra poder calcular esta salida, es necesario tener el camino a seguir (waypoints), la posición (x e y) y una variable i, que indique que waypoint. 
![image](https://github.com/user-attachments/assets/6f3e58c8-ad86-459d-92a8-5e7bc89ce5de)

La función tiene el siguiente funcionamiento:
 - Definimos las variables de velocidad y angulo inicial, el índice i y una distancia L (que determinará como de cerca debe estar nuestro robot de un waypoint para pasar al siguiente).
 - Comprobamos que todavía nos quedan waypoints por recorrer, si la respuesta es afirmativa, calculamos la distancia entre nuestra posición actual y nuestro siguiente punto.
 - Si la distancia es menor que L y sigue habiendo waypoints, nos pasamos al siguiente waypoint, y volvemos a calcular la distancia y el ángulo entre la posición actual y el siguiente waypoint y ponemos una velocidad linear de 0.30 para que el robot se desplace al siguiente waypoint. Cuando esta condicion se deje de cumplir y cambiemos de waypoint al que dirigirnos, se calculará el ángulo entre la posición actual y el siguiente punto al que tenemos que desplazarnos.
 - Si no quedan waypoints por recorrer, se ponen ambas velocidades a 0, ya que hemos llegado al destino.
 - Finalmente comprobamos si el indice i es menor al número de waypoints que tenemos, es caso de ser así, el indice il de la salida será el valor de i-1, ya que hemos recorrido un waypoint más. Pero si i es mayor, significa que todavía no hemos pasado un waypoint, por lo que i=il

 - ## Aceleración limitada con evitación de obstáculos
Finalmente, si queremos añadir a nuestro Piero una navegacón reactiva, es decir, que esquive obstaculos, añadiremos al sistema un Diagrama de estados que se encarge de esquivar los obstáculos. La lógica de control es fácil, si se detecta algún objeto, el switch hará que se cambie de modo navegación a modo reactivo. Cuando se haya esquivado el objeto, se seguirá con la trayectoria normal volviendo a ir al waypoint al que inicialmente ibamos a ir antes de detectar el obstáculo.

El diagrama de estados tendrá como estado incial la no detección de obstaculos, la cual mandará un 0 como señal de control y por lo tanto, el comportamiento reactivo estará desactivado. En cuanto detecte algún obstaculo por la izquierda, se irá al estado *"ObstaculoIzq"*, en donde se procede a girar a la derecha. Cada tres segndos se comprueba si ya se ha esquivado o no dicho obstáculo, si además de detectarlo por la izquierda lo detecta por la derecha tambien, quiere decir que el obstáculo está de frente, por lo que habrá que girar más. Si se detecta por la derecha, el razonamiento es el mismo, salvo que esta vez estaremos en el estado *"ObstaculoDcha"* y el giro se hará a la izquierda.
![image](https://github.com/user-attachments/assets/cafa66ef-3efb-455b-821f-72fe8318783b)
Siendo el esquema del bloque final 
![image](https://github.com/user-attachments/assets/a1c14f50-99cc-4335-ac52-1e6c1022e594)



