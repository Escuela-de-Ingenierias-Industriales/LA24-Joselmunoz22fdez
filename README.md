#  Piero Indiviudal Jose Luis Muñoz
  Como objetivo  principal de este artículo es describir los conocimientos aprendidos y mi experiencia durante el proyecto del
"Piero". Este proyecto se centra en la creación y el progreso del Piero, así como la implementación de programas que le permitan
recorrer un camino ya preestablecido, asi como salir del aula. También se le ha implemnetado una navegación reactiva, es decir 
que mientras se realizando la trayectoria sea capaz de esquivar objetos.

# Montaje de Robot Piero 
  Los miembros que pertenecen al grupo 17, hemos montado individualmente un Piero propio. Siguinedo para nuestro caso, el montaje 
descrito en el siguinte documento. Además de seguir los enseñado en clase, como proyecto personal, he implementado un esquema 
propio  para los diferentes bloques usados. Ha este poryecto lo hemos denominado " Final Project" (siendo los archivos realaciona-
dos Fp_namefile)


# Hardware
A continuación vamos a contar el hardware que se ha usado para el montaje del Piero: 
  
  ## Arduino Mega 2560
  Como controlador del Piero vamos a utilizar una tarjeta de la compañia Arudino que nos va a permitir poder manejar todos la
  informacion captada por los sensores usados para poder realizar el control del Piero. Para poder usar todos los sensores es 
  necesario disponer de multiples pines tanto analogicos como digitales. Es por eso que se ha escogido el arduino Mega 2560:

  ### Puertos de Arduino Mega 2560
  ![Pines Arduino Mega 2560 Rev3](https://lab.bricogeek.com/assets/10/Pinout-Mega2560rev3_latest.png)

  ## Alimenación de circuitos 
  Para alimentar todo el sistema del Piero vamos a utilizar una fuente de tensión de 12 V y para ello usaremos tres pilas de
  4 V en serie. Para conocer la carga de la bateria usada vamos añadir un multimetro para ver por un display la tensión de 
  la bateria y un medidor de tensión que irá conectado al Arduino.

  ## Sonars 
  Para nuestro piero se han escogido dos sensores ultrasonidos US-016 , el cual cuanta con 5 pines: Vcc (alimentación 5V),
  Range (alimentacion 3V), Out (salida analógica) y Gnd (conectada a tierra).
  ![](https://github.com/user-attachments/assets/c5cc2bf4-af3b-4ca8-bf87-d0f26b45caae)

  ## Motores 
  Para el Piero se han usado dos motores con encoders incorporados mediante el módulo L298N. Dicho motores necesitan una  
  alimentación de 12 V que como hemos dicho antes. En la siguidnte imagen se pueden observar tanto el esquema de alimentación
  como el de los motores.
  (![](https://github.com/user-attachments/assets/e8439ba3-9fba-4890-902f-90ffcea61b91)


  
   
