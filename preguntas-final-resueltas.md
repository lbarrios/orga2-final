MICROARQUITECTURA
=================

1) Definir el concepto de ISA vs Microarquitectura. ¿Para qué sirve esta distinción?
    - ¿Pueden haber dos procesadores con la misma ISA y distinta microarquitectura? Dar ejemplo.
    - ¿Pueden haber dos procesadores con la misma microarquitectura y distinta ISA? Dar ejemplo.

```

La Instruction Set Architecture es una especificación que provee una interfaz
del procesador, para ser utilizada por el programador, el compilador, el
sistema operativo, etcétera. Dicha interfaz establece el conjunto de
instrucciones soportado por esta arquitectura, la forma de organización y
direccionamiento de la memoria, los privilegios de ejecución, los registros,
así como cualquier otro detalle relevante a la hora de implementar algo que
funcione sobre un procesador que cumpla con esta interfaz.

La microarquitectura, en cambio, es una implementación particular de la ISA.
Incluye, a grandes rasgos, la organización y detalles específicos de los
componentes internos del procesador (caché, buses, unidades de ejecución,
etc).

Esta distinción permite que se generen familias de procesadores de una misma
arquitectura, que aseguren la compatibilidad entre distintos modelos. Esto
significa que un código corriendo en un modelo de procesador de una
determinada arquitectura debería de poder correr un procesador de esa misma
arquitectura, pero de otro modelo. Sin contar con esta ventaja, un binario
compilado para una determinada arquitectura, debería ser recompilado cada vez
que se quisiera usar en un procesador distinto.

Un ejemplo puntual de procesadores con la misma ISA y distintas
microarquitecturas se da en la arquitectura x86, en donde se tiene por un lado
al modelo Intel Pentium 4, que está implementado con microarquitectura
Netburst, propietaria de Intel, y al AMD Athlon, que está implementado con una
microarquitectura distinta, propietaria de AMD.

Dado que la microarquitectura es una implementación particular de la ISA, no
tiene sentido hablar de dos procesadores con la misma microarquitectura y
distinta ISA.

```

2) ¿Dentro de una microarquitectura, cuál es la diferencia entre la organización y el hardware? 
    - ¿Pueden haber dos procesadores con la misma organización y distinto hardware? Dar ejemplo.
    - ¿Pueden haber dos procesadores con el mismo hardware y distinta organización? Dar ejemplo.

```

La organización es todo lo que respecta a los distintos componentes que
conforman la microarquitectura, cómo funcionan y cómo se relacionan entre sí
los distintos bloques funcionales internos de la CPU. El hardware son detalles
implementativos, de nivel lógico, electrónico y de fabricación.

Es posible encontrar procesadores con la misma ISA y la misma organización,
pero distinto hardware, por ejemplo un procesador orientado a uso de
escritorio, vs el mismo modelo de procesador pero orientado a su uso en
dispositivos móviles. Se puede considerar, por ejemplo, dentro de la
microarquitectura Netburst, a los procesadores Pentium 4 vs los procesadores
Pentium 4-M. Siendo muy similares en cuanto a los detalles
microarquitecturales, pero con diferencias importantes en cuanto a su diseño
electrónico, por ejemplo incluyendo el segundo una mayor optimización y lógica
adicional de control de consumo energético.

```


INSTRUCTION LEVEL PARALELISM
============================

1) Distinguir entre microarquitecturas monociclo vs multiciclo, mencionando ventajas y desventajas de cada tipo.

```

Las microarquitecturas monociclo son aquellas en donde cada instrucción es
ejecutada en un ciclo de máquina, y en donde se considera que no existen
cambios intermedios dentro del contexto de la ejecución de una misma
instrucción. Es decir, si la instrucción transforma el estado arquitectural AS
en AS', entonces la trancisión AS -> AS' se realiza de forma atómica, en un
ciclo de clock, sea cual sea la instrucción que se esté ejecutando. Es una
arquitectura muy simple. A nivel electrónico, se puede representar como una
máquina de estados con dos bloques, uno siendo la lógica secuencial, que
preserva el estado de la CPU, y el otro la lógica combinatoria, que resuelve
la ejecución de la instrucción. Presenta como desventaja que la duración de un
ciclo generalmente es larga, ya que está supeditada al tiempo que tarde en
ejecutarse la más lenta de las instrucciones definidas por el ISA,
considerando el peor caso posible. Ejemplo, una instrucción de punto flotante,
que además realiza acceso a memoria. Esto significa que hay mucho "tiempo
desperdiciado", ya que aquellas instrucciones simples y cuya ejecución podría
realizarse en mucho menor tiempo, ejemplo incrementar un registro, se ejecutan
en la primera parte de ese ciclo, y el resto es tiempo muerto.

Una posible mejora sobre esto último, es una arquitectura multiciclo, en donde
se divide la ejecución en varias etapas, y cada una de ellas corre en un ciclo
de máquina. De forma similar, la duración de un ciclo de máquina está
supeditada al tiempo que pueda tardar la más lenta de las etapas. Esto permite
paralelizar la ejecución de instrucciones, por ejemplo a través de un
pipeline, ya que si la cantidad de etapas es N, en el caso ideal se pueden
tener N instrucciones corriendo al mismo tiempo (una sobre cada etapa).

Finalmente, existen las arquitecturas basadas en microcódigo, en donde el
tiempo de clock puede ser muy pequeño, y cada etapa está subdivida en
microinstrucciones. La duración de las etapas no siempre es la misma, ya que
depende de las microinstrucciones que requiera la ejecución de cada
instrucción. Esto permite optimizar la ejecución a muy bajo nivel, por ejemplo
aplicando técnicas como Out-Of-Order Execution.

```

2) Explicar qué es el pipelining, y cómo ayuda a mejorar el rendimiento del procesador.
    - Diagramar un ejemplo de un flujo de ejecución sin pipelining vs. la misma ejecución con pipelining.

```

El pipelining es una técnica que permite paralelizar la ejecución de
instrucciones, utilizando para ello las distintas etapas del procesador, de
forma similar a una cinta de producción en una fábrica, o en una tubería, en
donde el líquido ingresa de un lado, y sale del otro; en este último ejemplo
la tubería inicialmente tarda un tiempo en llenarse (imaginemos que es una
tubería de cientos de metros), pero una vez que lo hace se establece un flujo
de transmisión contínuo. Lo mismo sucede con una arquitectura con pipeline, en
donde se busca generar un flujo de instrucciones constante. De este modo, en
un pipeline ideal, el tiempo por instrucción resulta inversamente proporcional
a la cantidad de etapas del pipeline.

Supongamos el flujo de ejecución de las instruciones A, B, C.

Sin pipelining:

--------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Execute     |   Retire
--------------------------------------------------------------
    1   |     A     |           |               |       
--------------------------------------------------------------
    2   |           |     A     |               |           
--------------------------------------------------------------
    3   |           |           |     A         |           
--------------------------------------------------------------
    4   |           |           |               |      A
--------------------------------------------------------------
    5   |     B     |           |               |       
--------------------------------------------------------------
    6   |           |     B     |               |           
--------------------------------------------------------------
    7   |           |           |     B         |           
--------------------------------------------------------------
    8   |           |           |               |      B
--------------------------------------------------------------
    9   |     C     |           |               |       
--------------------------------------------------------------
    10  |           |     C     |               |           
--------------------------------------------------------------
    11  |           |           |     C         |           
--------------------------------------------------------------
    12  |           |           |               |      C
--------------------------------------------------------------
    13  |     D     |           |               |       
--------------------------------------------------------------
    14  |           |     D     |               |           
--------------------------------------------------------------
    15  |           |           |     D         |           
--------------------------------------------------------------
    16  |           |           |               |      D
--------------------------------------------------------------

Con pipelining:

--------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Execute     |   Retire
--------------------------------------------------------------
    1   |     A     |           |               |       
--------------------------------------------------------------
    2   |     B     |     A     |               |           
--------------------------------------------------------------
    3   |     C     |     B     |     A         |           
--------------------------------------------------------------
    4   |     D     |     C     |     B         |      A
--------------------------------------------------------------
    5   |           |     D     |     C         |      B
--------------------------------------------------------------
    6   |           |           |     D         |      C        
--------------------------------------------------------------
    7   |           |           |               |      D        
--------------------------------------------------------------

Como se puede ver, hay una mejora notable de rendimiento. En el ciclo 4 se
alcanza una condición de estabilidad del pipeline, en donde todas las etapas
se encuentran haciendo algo al mismo tiempo. En este contexto, si se mantienen
las condiciones óptimas, y continúan ingresando instrucciones al pipeline de
forma ininterrumpida, el throughput se maximiza, ya que el TPI se divide según
la cantidad de etapas del pipeline. 
```


3) ¿Qué son los obstáculos/riesgos, y qué fenómeno pueden ocasionar en relación al pipeline?
    - Diagramar un ejemplo sencillo de un flujo de ejecución en donde se pueda observar este fenómeno.

```

Los obstáculos son dependencias entre etapas del pipeline, que hacen que una o
más etapas no puedan ejecutarse. Esto genera un fenómeno denominado pipeline
stall, que consiste en la detención parcial o total de las distintas etapas
del pipeline. Dado que cada etapa debe ejecutarse, y pasar la instrucción a la
siguiente etapa, las etapas anteriores a la que no puede ejecutarse terminan
deteniéndose también, al generarse una reacción en cadena en la que las
instrucciones no pueden avanzar de etapa. Las etapas posteriores, por otro
lado, siguen trabajando con las instrucciones que ya tenían en ejecución, pero
al no recibir más instrucciones, se van deteniendo.


Pipeline con stall de 5 ciclos en la instrucción E en la etapa 2.

-------------------------------------------------
 Ciclo  |   E1  |   E2  |   E3  |   E4  |   E5  |
-------------------------------------------------
   1    |   A   |       |       |       |       | <- LLENANDO
-------------------------------------------------
   2    |   B   |   A   |       |       |       | <- LLENANDO
-------------------------------------------------
   3    |   C   |   B   |   A   |       |       | <- LLENANDO
-------------------------------------------------
   4    |   D   |   C   |   B   |   A   |       | <- LLENANDO
-------------------------------------------------
   5    |   E   |   D   |   C   |   B   |   A   | <- ESTABLE
-------------------------------------------------
   6    |   F   |   E   |   D   |   C   |   B   | <- ESTABLE
-------------------------------------------------
   7    |   F   |   E   |   -   |   D   |   C   | <- STALL
-------------------------------------------------
   8    |   F   |   E   |   -   |   -   |   D   | <- STALL
-------------------------------------------------
   9    |   F   |   E   |   -   |   -   |   -   | <- STALL
-------------------------------------------------
   10   |   F   |   E   |   -   |   -   |   -   | <- STALL
-------------------------------------------------
   11   |   F   |   E   |   -   |   -   |   -   | <- STALL
-------------------------------------------------
   12   |   G   |   F   |   E   |   -   |   -   | <- RECUPERANDO
-------------------------------------------------
   13   |   H   |   G   |   F   |   E   |   -   | <- RECUPERANDO
-------------------------------------------------
   14   |   I   |   H   |   G   |   F   |   E   | <- ESTABLE
-------------------------------------------------


El mismo pipeline sin stall.

-------------------------------------------------
 Ciclo  |   E1  |   E2  |   E3  |   E4  |   E5  |
-------------------------------------------------
   1    |   A   |       |       |       |       | <- LLENANDO
-------------------------------------------------
   2    |   B   |   A   |       |       |       | <- LLENANDO
-------------------------------------------------
   3    |   C   |   B   |   A   |       |       | <- LLENANDO
-------------------------------------------------
   4    |   D   |   C   |   B   |   A   |       | <- LLENANDO
-------------------------------------------------
   5    |   E   |   D   |   C   |   B   |   A   | <- ESTABLE
-------------------------------------------------
   6    |   F   |   E   |   D   |   C   |   B   | <- ESTABLE
-------------------------------------------------
   7    |   G   |   F   |   E   |   D   |   C   | <- ESTABLE
-------------------------------------------------
   8    |   H   |   G   |   F   |   E   |   D   | <- ESTABLE
-------------------------------------------------
   9    |   I   |   H   |   G   |   F   |   E   | <- ESTABLE
-------------------------------------------------
   10   |   J   |   I   |   H   |   G   |   F   | <- ESTABLE
-------------------------------------------------
   11   |   K   |   J   |   I   |   H   |   G   | <- ESTABLE
-------------------------------------------------
   12   |   L   |   K   |   J   |   I   |   H   | <- ESTABLE
-------------------------------------------------
   13   |   M   |   L   |   K   |   J   |   I   | <- ESTABLE
-------------------------------------------------
   14   |   N   |   M   |   L   |   K   |   J   | <- ESTABLE
-------------------------------------------------
```


4) Explicar qué son los obstáculos estructurales. Dar un ejemplo. ¿De qué modo se pueden prevenir?

```

Los obstáculos estructurales son aquellos que surgen por dependencias entre
etapas que se encuentran en el propio diseño de la microarquitectura. Esto
puede sugerir que las etapas no fueron diseñadas con la suficiente
granularidad o independencia entre sí, aunque muchas veces la solución no es
tan sencilla como "dividir la etapa en más pequeñas".

Un ejemplo típico es cuando hay una etapa que hace el fetch de las
instrucciones (para ello requiere realizar un acceso a memoria), y una etapa
distinta que hace el fetch de los operandos (para ello requiere también
realizar un acceso a memoria).

Lo anterior se puede ver en un diagrama, suponiendo que la instrucción A
necesita sacar operandos de memoria. Esto hace que el fetch de la instrucción
C se tenga que pausar, generalmente insertando en el pipeline una operación
NOP (también llamado burbuja).

-----------------------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
-----------------------------------------------------------------------------
    1   |     A     |           |               |               |     
-----------------------------------------------------------------------------
    2   |     B     |     A     |               |               |     
-----------------------------------------------------------------------------
    3   |     C     |     B     |     A         |               |     
-----------------------------------------------------------------------------
    4   |     C     |     -     |     B         |      A        |     
-----------------------------------------------------------------------------
    5   |     D     |     C     |     -         |      B        |     A
-----------------------------------------------------------------------------
    6   |     E     |     D     |     C         |      -        |     B
-----------------------------------------------------------------------------

La mayoría de las veces prevenir estos obstáculos implica agregar o
reorganizar el hardware. Siguiendo con el ejemplo anterior, una solución sería
desdoblar la caché L1 en caché L1 de instrucciones (usada por el Fetch) y
caché L1 de datos (usada por el Fetch de Operandos).

```


5) Explicar qué son los obstáculos de datos. 
    - Explicar qué tipo de dependencias de datos definen las siglas WAR, RAW, WAW.

```

Las dependencias de datos son aquellas que pueden surgir debido al contexto y
la naturaleza de las instrucciones contenidas en el pipeline. Existen tres
tipos de dependencias:

- Read-After-Write (RAW): También llamada dependencia de flujo, o dependencia
  real. Sucede cuando una instrucción escribe un dato como resultado, y una
  instrucción posterior necesita utilizar ese mismo dato como entrada. Si la
  dependencia no es detectada, la instrucción posterior termina leyendo un dato
  inválido. Cuando el riesgo es detectado, si la instrucción que provee el dato
  tarda mucho en ejecutarse, esto termina ocasionando un pipeline stall.

  Supongamos que la instrucción A produce como salida el el dato que la
  instrucción B va a usar como operando.

  En un flujo de ejecución normal (sin detección de dependencias), termina
  sucediendo que en el ciclo 4 la instrucción B termina leyendo un operando
  que contiene información inconsistente con el orden de ejecución del
  programa. Es decir, a partir de este punto, el programa no sirve más.

  -----------------------------------------------------------------------------
   Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
  -----------------------------------------------------------------------------
      1   |     A     |           |               |               |     
  -----------------------------------------------------------------------------
      2   |     B     |     A     |               |               |     
  -----------------------------------------------------------------------------
      3   |     C     |     B     |     A         |               |     
  -----------------------------------------------------------------------------
      4   |     D     |     C     |     B         |      A        |     
  -----------------------------------------------------------------------------
      5   |     E     |     D     |     C         |      B        |     A
  -----------------------------------------------------------------------------
      6   |     F     |     E     |     D         |      C        |     B
  -----------------------------------------------------------------------------

  Si agregamos detección de dependencias, la solución es detener el fetch de
  los operandos de B, hasta que A haya guardado el resultado en el registro o
  memoria correspondiente.

  -----------------------------------------------------------------------------
   Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
  -----------------------------------------------------------------------------
      1   |     A     |           |               |               |     
  -----------------------------------------------------------------------------
      2   |     B     |     A     |               |               |     
  -----------------------------------------------------------------------------
      3   |     C     |     B     |     A         |               |     
  -----------------------------------------------------------------------------
      4   |     D     |     C     |     B         |      A        |     
  -----------------------------------------------------------------------------
      5   |     D     |     C     |     B         |      -        |     A
  -----------------------------------------------------------------------------
      6   |     D     |     C     |     B         |      -        |     -
  -----------------------------------------------------------------------------
      7   |     E     |     D     |     C         |      B        |     -     
  -----------------------------------------------------------------------------
      8   |     F     |     E     |     D         |      C        |     B
  -----------------------------------------------------------------------------

- Write-After-Read (WAR): También llamada anti-dependencia. Sucede cuando una
  instrucción lee un dato, y luego otra escribe ese mismo dato. El riesgo es que
  la instrucción que escribe se termine ejecutando antes de la instrucción que
  lee, afectando la consistencia del programa. Esto puede llegar a suceder en un
  contexto de Ejecución Fuera de Orden.

- Write-After-Write (WAW): También llamada dependencia de output. Sucede cuando
  una instrucción escribe un dato, y luego otra instrucción escribe ese mismo
  dato. En un flujo de ejecución normal, el dato debería quedar con el valor que
  escribió la segunda instrucción. El riesgo es que la escritura de la segunda
  instrucción repercuta primero que la de la primera instrucción, por lo que el
  dato terminaría siendo el que definió la primera instrucción, haciendo que el
  programa llegue a un estado de inconsistencia. Esto puede llegar a suceder en
  un contexto de Ejecución Fuera de Orden.

```

6) Explicar los conceptos de scheduling estático vs scheduling dinámico.
    - Dar ejemplos de scheduling estático.
    - ¿Qué es el interlocking, y cómo se relaciona con el scheduling estático?

```

El scheduling estático son aquellas soluciones que buscan maximizar el ILP, y
solucionar los problemas de dependencias, pero que son implementados en nivel
del compilador, mientras que en el scheduling dinámico el procesador
interviene de forma dinámica para mejorar el ILP.

Ejemplos de scheduling estático pueden ser la inserción de burbujas NOP entre
las instrucciones que tengan dependencias de datos, como forma de eliminar
estas dependencias en tiempo de compilación, el loop unrolling que consiste en
transformar los loops en una secuencia de instrucciones sin saltos, el
ordenamiento de instrucciones de un código de forma tal que corran en el
procesador de la forma más eficiente posible, etcétera.

El interlocking es una técnica que permite que cuando una determinada etapa se
encuentra ocupada, o cuando existe una dependencia de algún tipo, las etapas
afectadas puedan detener su ejecución (stall), o insertar burbujas (NOP).
Básicamente, es un mecanismo de detección y protección ante dependencias,
aunque no sirve para solucionar la pérdida de rendimiento ocasionada. El
interlocking es una forma de scheduling dinámico, y requiere contar con lógica
adicional, específica para realizar esta tarea. Se relaciona con el scheduling
dinámico, ya una posibilidad es minimizar las capacidades de interlocking de
un procesador, o directamente no hacer interlocking (como en el caso de los
MIPS en sus orígenes), y delegar esta tarea al compilador, que se debe
encargar de generar un código apropiado que evite las dependencias (por
ejemplo, insertando NOPs entre dos instrucciones conflictivas cuando el
reordenamiento no es suficiente).

```

7) ¿Cómo funciona el forwarding en el contexto de los obstáculos de datos, y para qué sirve?
    - ¿Qué dependencias de datos se ven afectadas por su utilización?

```

El forwarding es una técnica que consiste en enviar el resultado de la
ejecución de una instrucción, directo desde la unidad de ejecución, hacia la
instrucción que lo necesite como dependencia (en el caso de RAW).

Manteniendo el mismo ejemplo que se utilizó en la explicación de RAW,
supongamos que la ejecución B requiere como entrada el dato emitido por la
ejecución de A.

Con Forwarding

-----------------------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
-----------------------------------------------------------------------------
    1   |     A     |           |               |               |     
-----------------------------------------------------------------------------
    2   |     B     |     A     |               |               |     
-----------------------------------------------------------------------------
    3   |     C     |     B     |     A         |               |     
-----------------------------------------------------------------------------
    4   |     D     |     C     |     B         |    A (->B)    |     
-----------------------------------------------------------------------------
    5   |     D     |     C     |     B (<-A)   |      -        |     A
-----------------------------------------------------------------------------
    6   |     E     |     D     |     C         |      B        |     -     
-----------------------------------------------------------------------------
    7   |     F     |     E     |     D         |      C        |     B
-----------------------------------------------------------------------------
    8   |     G     |     F     |     E         |      D        |     C     
-----------------------------------------------------------------------------

```

8) Explicar el concepto de ejecución superescalar. Relacionarlo con el concepto de Hyper-Threading (Intel SMT).

```

La ejecución escalar consiste en tener múltiples unidades de ejecución,
normalmente en una arquitectura con pipeline. Esto permite que una mayor
cantidad de instrucciones sean ejecutadas al mismo tiempo.

Supongamos un pipeline con tres etapas (Fetch, Decode, Execute), los diagramas
de ejecución serían los siguientes.

Pipelining normal

---------------------------------
 Ciclo  |   F   |   D   |   E   |
---------------------------------
    1   |   A   |       |       |
---------------------------------
    2   |   B   |   A   |       |
---------------------------------
    3   |   C   |   B   |   A   |
---------------------------------
    4   |   D   |   C   |   B   |
---------------------------------
    5   |   E   |   D   |   C   |
---------------------------------
    6   |   F   |   E   |   D   |
---------------------------------


Pipelining + Superescalar

---------------------------------------------------------
 Ciclo  |   F1  |   F2  |   D1  |   D2  |   E1  |   E2  |
---------------------------------------------------------
    1   |   A   |   B   |       |       |       |       |
---------------------------------------------------------
    2   |   C   |   D   |   A   |   B   |       |       |
---------------------------------------------------------
    3   |   E   |   F   |   C   |   D   |   A   |   B   |
---------------------------------------------------------

En una microarquitectura superescalar, es posible que terminen existiendo
unidades de ejecución o etapas que se desperdicien, por ejemplo por
dependencias. Una de las formas de solucionar esto es Hyper-Treading (que es
el nombre comercial de Intel para el Simultaneous Multithreading), que
básicamente consiste en simular la existencia de un segundo procesador, con un
pequeño incremento de hardware (para mantener el estado arquitectural de cada
procesador, o para la lógica de arbitraje necesaria), pero un gran incremento
en el rendimiento, bajo la idea de que las etapas del pipeline van a ser más
utilizado.

```

9) Explicar la idea detrás del concepto de Out-of-Order Execution. 
    - Mencionar ventajas y desventajas de su utilización. 
    - Ejemplificar el diagrama de un flujo de ejecución In-Order vs la misma ejecución Out-of-Order.

```

La idea detrás del concepto de OoOE es que ante dependencias que generen un
pipeline stall, se desperdician etapas, y por lo general hay otras
instrucciones en cola que se podrían estar ejecutando, ya que no tienen
dependencias.

Supongamos que la instrucción B depende del output de A, pero que las
siguientes no. Entonces, si pudieramos tendríamos lo siguiente.

Flujo de ejecución in-order (con stall)
-----------------------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
-----------------------------------------------------------------------------
    1   |     A     |           |               |               |     
-----------------------------------------------------------------------------
    2   |     B     |     A     |               |               |     
-----------------------------------------------------------------------------
    3   |     C     |     B     |     A         |               |     
-----------------------------------------------------------------------------
    4   |     D     |     C     |     B         |      A        |     
-----------------------------------------------------------------------------
    5   |     D     |     C     |     B         |      -        |     A
-----------------------------------------------------------------------------
    6   |     D     |     C     |     B         |      -        |     -
-----------------------------------------------------------------------------
    7   |     E     |     D     |     C         |      B        |     -     
-----------------------------------------------------------------------------
    8   |     F     |     E     |     D         |      C        |     B
-----------------------------------------------------------------------------

Flujo de ejecución out-of-order
-----------------------------------------------------------------------------
 Ciclo  |   Fetch   |   Decode  |   Fetch Oper  |   Execute     |   Retire
-----------------------------------------------------------------------------
    1   |     A     |           |               |               |     
-----------------------------------------------------------------------------
    2   |     B     |     A     |               |               |     
-----------------------------------------------------------------------------
    3   |     C     |     B     |     A         |               |     
-----------------------------------------------------------------------------
    4   |     D     |     C     |     B         |      A        |     
-----------------------------------------------------------------------------
    5   |     E     |     D     |     (B) C     |      -        |     A
-----------------------------------------------------------------------------
    6   |     E     |     D     |     B         |      C        |     -
-----------------------------------------------------------------------------
    6   |     F     |     E     |     D         |      B        |     C
-----------------------------------------------------------------------------
    6   |     G     |     F     |     E         |      D        |     B
-----------------------------------------------------------------------------

La mejora puede llegar a ser sustancialmente mejor si se cuenta con ejecución
superescalar.

```

10) ¿Qué son las excepciones imprecisas, y cuales son sus posibles causas?

```

Las excepciones imprecisas surgen al utilizar Out-of-Order Execution. Son
excepciones en donde el estado arquitectural del procesador no es el mismo que
el que tendría si la ejecución hubiera sido in-order. Esto hace que el
programa pueda tomar estados inválidos.

Las causas pueden ser dos, que una instrucción que debería haberse ejecutado
no se haya ejecutado, o que una instrucción que todavía no se debería haber
ejecutado ya se haya ejecutado.

Tomando el ejemplo anterior, se puede interpretar que puede haber una
excepción imprecisa si:

  - C levanta una excepción, pero B todavía no se commiteó, por lo que el
    estado del procesador no es válido.

  - B levanta una excepción, pero C ya se ejecutó, por lo que el estado del
    procesador no es válido.

```

11) ¿Qué es el scoreboarding, y para qué sirve?
    - Explicar cuáles son las etapas que lo componen, y qué se realiza en cada una de ellas. 
    - Dibujar un diagrama que permita entender cómo se relaciona cada etapa con la siguiente, junto con los demás componentes que fuesen necesarios para implementar el método. 
    - ¿Qué dependencias de datos se ven afectadas por su utilización, en qué etapa y de qué modo? 
    - Mencionar ventajas y desventajas generales.

```

El Scoreboarding es una técnica que permite implementar Out-of-Order
execution, previniendo que la ejecución se vea afectada por las falsas
dependencias (WAW y WAR) surgidas de implementar OoOE. Su función es la de
lograr que cuando hay dependencias de datos (también RAW) o estructurales se
generen stalls, ya sea deteniendo las etapas o unidades funcionales
necesarias, o insertando burbujas. Para ello, divide la etapa de decode en dos
etapas, Issue y Read Operands. La ejecución de una instrucción comprendería
las etapas, todas ellas conectadas a una unidad de scoreboard (marcador,
pizarra):

Fetch -> Issue -> Read Operands -> Execute -> Write Results
   |       |          |               |            |
   ---------------------- Scoreboard ---------------
   
```


# TODO



1) Fetch: se lee la siguiente instrucción
2) Issue: se 
3) Read Operands: se leen
4) Execute: 
5) Write Results:

12) ¿Qué es el algoritmo de Tomasulo, y para qué sirve?
    - Explicar cuáles son las etapas que lo componen, y qué se realiza en cada una de ellas. 
    - Dibujar un diagrama que relacione las etapas con el resto de los componentes (ejemplo, Reservation Station, Common Data Bus, Reorder Buffer).
    - ¿Qué dependencias busca solucionar en comparación con Scoreboarding, y de qué forma lo hace?
    - Mencionar ventajas y desventajas generales.

13) Explicar qué son los obstáculos de control. 
    - Caracterizar los tipos de discontinuidad que se pueden encontrar en el flujo de una ejecución.

14) ¿Qué sucede con el pipeline cuando el procesador encuentra un salto? Explicar el concepto de Branch Prediction, y relacionarlo con los distintos tipos de branches que pueden encontrarse.

15) Diferenciar los conceptos de predicción estática vs predicción dinámica. Ventajas y desventajas de cada una.

16) Explicar las siguientes técnicas. Mencionar ventajas, desventajas y requerimientos de cada una:
    - Branch Always Not Taken
    - Branch Always Taken
    - Backward Taken Forward Not Taken
    - Profile-Driven Prediction
    - Delayed Branch Slot
    - Loop Unrolling
    - Predicated Execution

17) ¿Qué es el Branch Target Buffer, para qué sirve, cómo funciona y cómo está compuesto?

18) ¿Qué es el Branch Prediction Buffer (o Branch History Table, one-level predictor), para qué sirve, cómo funciona y cómo está compuesto?
    - Explicar cómo funciona un predictor de 1 bit. Explicar su funcionamiento mediante un diagrama de flujo y/o máquina de estados.
    - Explicar cómo funciona un predictor de 2 bits. Explicar su funcionamiento mediante un diagrama de flujo y/o máquina de estados.
    - Explicar cómo funcionaría en el caso general un one-level predictor que utilice un contador de n bits. ¿Presentan alguna ventaja respecto a los de menor cantidad de bits?

19) ¿Qué son los Correlation-Based Branch Predictor?
    - ¿En qué se diferencian de los one-level predictor de n bits?
    - Explicar los conceptos de local y global branch correlation.



-------
MEMORIA
-------

1) ¿Cuáles son las principales diferencias entre las memorias ROM y las RAM? Dar ejemplos de memorias de cada tipo.

2) Distinguir entre memoria ram estática y dinámica, mencionando ventajas y desventajas de cada una. ¿Cuál de ellas es usada para memoria caché, y cuál es usada para memoria principal? Comparar según:
    - cómo es el circuito (tamaño, cantidad de componentes electrónicos),
    - consumo eléctrico,
    - tiempo de acceso,
    - capacidad de almacenamiento por chip,
    - costo de fabricación

3) ¿Cuál es la función de la memoria caché, y cuáles son los dos principios sobre los cuales se fundamenta su utilización?

4) Describir los algoritmos de reemplazo más comunes, y relacionarlos con los principios mencionados anteriormente.

5) Diferenciar entre caché de mapeo directo, totalmente asociativa, y asociativa por conjuntos. Dar un ejemplo de cada una, y mencionar ventajas y desventajas.

6) Describir las políticas de escritura más comunes, y las ventajas y desventajas de cada una.

7) ¿Qué características generales debe cumplir un sistema para ser considerado SMP? Describir en qué consiste, en un sistema SMP de bus compartido, el problema de la coherencia de caché.

8) ¿Qué condiciones debe cumplir un sistema de memoria para ser considerado coherente? Explicar las propiedades de coherencia y consistencia, y relacionarlas con los conceptos de propagación y serialización.

9) ¿Qué soluciones existen para los problemas de coherencia de caché en sistemas monoprocesador? Ejemplo, cuando un dispositivo DMA escribe a memoria. 
    - ¿Qué ventajas y desventajas traen estas soluciones? 
    - ¿Requieren algún tipo de soporte de hardware o software especial? 
    - ¿Por qué no resulta práctico implementar esto mismo en sistemas SMP?

10) Describir la idea detrás de los protocolos de directorio y los protocolos de snooping, comparando ventajas y desventajas de cada uno de ellos.

11) Describir las diferencias entre los protocolos de invalidación en escritura, vs los de actualización en escritura. Ejemplificar, ¿qué ocurre en cada caso si un procesador desea leer de su caché una posición de memoria que fue previamente modificada por otro procesador?

11) Explicar cómo funciona un protocolo de snooping, de dos estados (válido, inválido).
    - ¿Cumple con las condiciones de coherencia y consistencia?
    - ¿Qué políticas de escritura soporta?
    - ¿Qué ventajas y desventajas trae usar este protocolo?

12) Explicar en qué consiste y cómo funciona el protocolo MSI, detallando cada estado.
    - Explicar la diferencia con el protocolo de snooping de dos estados.
    - ¿Cumple con las condiciones de coherencia y consistencia?
    - ¿Qué políticas de escritura soporta?
    - ¿Qué ventajas y desventajas trae usar este protocolo?

13) Explicar cómo funciona el protocolo MESI, detallando cada estado. Hacer un diagrama.
    - Explicar la diferencia diferencia con MSI, ¿qué ventajas presenta?
    - ¿Cumple con las condiciones de coherencia y consistencia?
    - ¿Qué políticas de escritura soporta?
    - Dibujar un diagrama de estados.
    - ¿Qué desventajas presenta este protocolo?


------------------------
CASOS PRÁCTICOS / PAPERS
------------------------

1) Explicar la microarquitectura P6 (Three Cores Engine).

2) Explicar la arquitectura Netburst, y las ventajas frente a P6.