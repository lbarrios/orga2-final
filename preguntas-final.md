-----------------
MICROARQUITECTURA
-----------------

1) Definir el concepto de ISA vs Microarquitectura. ¿Para qué sirve esta distinción?
	- ¿Pueden haber dos procesadores con la misma ISA y distinta microarquitectura? Dar ejemplo.
	- ¿Pueden haber dos procesadores con la misma microarquitectura y distinta ISA? Dar ejemplo.

2) ¿Dentro de una microarquitectura, cuál es la diferencia entre la organización y el hardware? 
	- ¿Pueden haber dos procesadores con la misma organización y distinto hardware? Dar ejemplo.
	- ¿Pueden haber dos procesadores con el mismo hardware y distinta organización? Dar ejemplo.


----------------------------
INSTRUCTION LEVEL PARALELISM
----------------------------

1) Distinguir entre microarquitecturas monociclo vs multiciclo, mencionando ventajas y desventajas de cada tipo.

2) Explicar qué es el pipelining, y cómo ayuda a mejorar el rendimiento del procesador.
	- Diagramar un ejemplo de un flujo de ejecución sin pipelining vs. la misma ejecución con pipelining.

3) ¿Qué son los obstáculos/riesgos, y qué fenómeno pueden ocasionar en relación al pipeline?

4) Explicar qué son los obstáculos estructurales. Dar un ejemplo. ¿De qué modo se pueden prevenir?

5) Explicar qué son los obstáculos de datos. 
	- Explicar qué tipo de dependencias de datos definen las siglas WAR, RAW, WAW.

6) Explicar los conceptos de scheduling estático vs scheduling dinámico.
	- Dar ejemplos de scheduling estático.
	- ¿Qué es el interlocking, y cómo se relaciona con el scheduling estático?

7) ¿Cómo funciona el forwarding en el contexto de los obstáculos de datos, y para qué sirve?
	- ¿Qué dependencias de datos se ven afectadas por su utilización?

8) Explicar el concepto de ejecución superescalar. Relacionarlo con el concepto de Hyper-Threading (Intel SMT).

9) Explicar la idea detrás del concepto de Out-of-Order Execution. 
	- Mencionar ventajas y desventajas de su utilización. 
	- Ejemplificar el diagrama de un flujo de ejecución In-Order vs la misma ejecución Out-of-Order.

10) ¿Qué son las excepciones imprecisas, y cuales son sus posibles causas?

11) ¿Qué es el scoreboarding, y para qué sirve?
	- Explicar cuáles son las etapas que lo componen, y qué se realiza en cada una de ellas. 
	- Dibujar un diagrama que permita entender cómo se relaciona cada etapa con la siguiente, junto con los demás componentes que fuesen necesarios para implementar el método. 
	- ¿Qué dependencias de datos se ven afectadas por su utilización, en qué etapa y de qué modo? 
	- Mencionar ventajas y desventajas generales.

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