* temas
** intro
ventaja de virtual memory: desfragmentas y el programa no se da cuenta
ventaja: 40.50 (shared C library)
ventaja: 43.30: el compilador puede asumir adonde va a poner el stack, etc.
ver el diagrama que sigue, puede estructurar todos los programas iguales

"Para una tarea fija, reducir la frecuencia de clock disminuye la
potencia disipada pero no tiene efecto con la energı́a consumida."

"La mayorı́a de los procesadores actuales contiene bloques de hardware para
control de consumo, que se encargan de mantener alimentados solo los bloques
funcionales que se necesitan en cada momento. Por ejemplo: Si el procesador
no está ejecutando operaciones de Punto Flotante, entonces la Unidad de Punto
Flotante se mantiene apagada.

A pesar de que un transistor esté al corte, existe una corriente de fuga (likage)
que circula de todos modos

A medida que aumenta la cantidad de transistores esta corriente se hace mas
significativa.
En 2006 los principales diseñadores establecieron como meta que esta
corriente represente solo el 25 % del consumo total del chip. Aún ası́ los
modelos de mas alto rendimiento no lograron esta marca."

"Arquitectura
Es el conjunto de recursos accesibles para el programador, que por
lo general se mantienen a lo largo de los diferentes modelos de
procesadores de esa arquitectura

Microarquitectura: Es la implementación en el silicio de la arquitectura.
Puede ser muy simple o sumamente robusta y poderosa.

Microarquitectura = Organización + Hardware

Organización
Se refiere a los detalles de implementación de la ISA. Es decir
Organización e interconexión de la memoria.
Diseño de los diferentes bloques de la CPU y para implementar el set de
instrucciones.
Implementación de paralelismo a nivel de Instrucciones, y/o de datos."
** paralelismo a nivel de instruccion
pipeline: aumenta el throughput, aumenta un poco la latencia (hay delay entre etapas)
a veces hay varios pipelines, uno para integer operations, otro para flops (tardan distinto)

an ideal pipeline (seguir viendo que explican)
   - repetition of identical operations:
     the same operation is repeated on a large number of different inputs
   - repetition of independent operations:
     no dependencies between repeated operations
   - uniformly partitionable suboperations
     processing can be evenly divided into uniform-latency suboperations
     (that do not share resources)

causes of pipeline stalls
   resource contention
   dependencies (between instructions): data, control
   long-latency (multi-cycle) operations (e.g. division)

obstaculos estructurales:
=========================
etapa o recurso no suficientemente atomizado (requiere mas de un ciclo de clock)
las instrucciones que vienen atras esperan
solucion: agregar hardware (y aumentar la profundidad del pipeline). por ejemplo:
- desdoblar L1 en cache de datos y cache de instrucciones
buffers de instrucciones como colas FIFO

obstaculos de datos
===================
una instruccion espera que este disponible un dato

mejora: forwarding
se pasa un valor desde la salida de la ALU etc. a la entrada de la otra etapa,
sin esperar que se retire (escriba el resultado en el operando destino)
no ayuda mucho si hay un load, por ejemplo, que tarda mucho

obstaculos de control
=====================
branch penalty: se vacia el pipeline (mas pior para deep pipelines)
solucion: loop unrolling
solucion: delayed branch (creo que es ejecutar la que vendria en caso branch not taken)
solucion: branch prediction
fijas: predicted-non-taken, predicted-taken
no fijas:
  estaticas:
  - comparar si es hacia atras o adelante (se hace con las direcciones en el momento, no hacen falta bits)
  - el compilador predice usando heuristicas y setea un bit + se pueden aceptar hints del programador
  dinamicas:
  usa branch prediction buffer, que indexa usando parte de los bits de la direccion
  (guarda bits de confianza y direccion de la branch)
  podes usar x cantidad de bits para representar tu confianza, 2 suelen andar bien
  
  tambien esta branch target buffer que solo guarda direccion de salto y no bits
  (creo que funciona como tener un bit (te fijas si esta o no esta))

superscalar
==========
dos pipelines: duplicar los recursos
cada vez mas necesario:

ejecucion fuera de orden
========================
instrucciones con latencia indeterminada: loads, por ejemplo
instructions dispatched in dataflow (not control-flow) order
latency tolerance: allows independent operations to execute and complete in the
presence of a long latency operation

riesgos:
- WAR
- WAW
- RAW

excepciones imprecisas: para evitarlo, antes de levantar una excepcion, terminar
instrucciones que queden, y anular las que siguieron. algunos procesadores solo
tienen imprecisas.

se divide la etapa de decodificacion en:
- envio: trabaja en orden, decodificando instrucciones. la unidad de prebusqueda
trae instrucciones desde la cache L1 especifica, hasta que se llene el buffer
- lectura de operandos: inicia fuera de orden. envia a ejecutar las operaciones
cuyos operandos pueden ser accedidos, y monitorea el resto para cuando se
resulven los obstaculos de datos.

scoreboarding
=============
el scoreboard se va fijando que instrucciones pueden enviarse a ejecutar
evita WAR y WAW (esperando, no salteandoselas)
ver diagrama (slide 68/111)
obstaculos estructurales ya que la cantidad de buses es limitada para
paralelizar las transferencias entre el scoreboard y los registros
los operandos de leen de los registros, no utiliza forwarding

tomasulo
========
a traves de Register Renaming, se saltea los riesgos WAR y WAW
TODO: ver diagrama (slide 77/111)

Etapa 1 - Envio:
- Get next instruction from instruction queue.
- Find a free reservation station for it (if none are free, stall until one is)
– Read operands that are in the registers
– If the operand is not in the register, find which reservation station will produce it
– In effect, this step renames registers (reservation station IDs are “temporary” names)

Etapa 2 - Ejecucion:
– Monitor results as they are produced
– Put a result into all reservation stations waiting for it
– When all operands available for an instruction, it is ready
– Several ready instrs for one functional unit? Pick one.

Etapa 3 - Escritura de resultado:
– When result is computed, make it available on the “common data bus” (CDB),
where waiting reservation stations can pick it up
– Stores write to memory
– Result stored in the register file
– This step frees the reservation station
WAW hazards are handled since only the last instruction (in program order)
actually writes to the registers.

loads/stores se hacen en orden, para evitar riesgos de memoria
In Tomasulo's algorithm, the control logic is distributed among the reservation
stations, whereas in scoreboarding, the scoreboard keeps track of everything.

se hace ejecucion especulativa, por ejemplo prediciendo branches. para evitar
problemas como excepciones, etc, y para facilmente volver para atras en branch
mispredictions, se agrega a Tomasulo un ReOrder Buffer (ROB). Solo se escribe
en una etapa adicional llamada commmit, una vez que todo es seguro.
TODO: ver diagrama slide 81/111
ROB guarda: tipo de instruccion, destino, valor, ready
** caches
1:12:47 - cache de mapeo directo: cada bloque puede ir a un solo lugar de la cache
notar que hay tag store (mapea de index en tag) y data store
1:20:32 - asociatividad
1:24:10 - full associativity
1:26:36 - trade-offs
(higher hit rate,
slower cache access time (hit latency and data access latency),
more expensive hardware)
diminishing returns from higher associativity
random, fifo
1:38:11 - approximations of LRU
not mru, hierarchichal LRU (MRU group and MRU block)
1:44:29 - victim - next victim (facil de explicar en final)
V, NV, O

coherencia de cache
===================
al escribir un bloque de cache, tres formas de actuar:
write through, write through buffered, copy back

no soluciones:
- que se encargue el programador
- tener todas las caches compartidas => esta se convierte en memory bottleneck

formas de mantener coherencia, update vs invalidate

formas de coordinar: snoop bus (distribuido) vs directorio (centralizado)

solucion simple:
dos estados, valido e invalido, snoop, write-through, si un procesador escribe
un bloque, los otros lo invalidan

protocolo MSI
=============
M: unica copia y dirty
S: hay mas copias
I: no esta presente

cuando lees, te pones en Shared. cuando escribis, te pones en modified y avisas
que el resto invalide.
el problema es que cuando lee pone en shared aunque sea el unico. entonces, al
querer escribir, tiene que hacer el broadcast a todos innecesariamente

protocolo MESI
==============
idea: agrega un estado E (Exclusive)
al leer, si nadie lo tiene, pasa a exclusive. al escribir desde exclusive, no
avisa y pasa a modified
ambos MSI y MESI usan write-back de los modified antes que otro lo quiera leer

Request For Ownership (RFO): cuando estas en Shared, o Invalid, y queres escribir

ver diagrama (slide 47/48)

"(Such Cache to Cache transfers can reduce the read miss latency if the latency
to bring the block from the main memory is more than from Cache to Cache
transfers which is generally the case in bus based systems. But in multicore
architectures, where the coherence is maintained at the level of L2 caches,
there is on chip L3 cache, it may be faster to fetch the missed block from the
L3 cache rather than from another L2)"
* tips facebook/CUBA
Los finales de Alejandro Furfaro suelen ser orales (si no
hay mucha gente). Duran 15-20 minutos y hace preguntas de microarquitectura,
específicamente sobre coherencia de Caché, predicción de saltos,
microarquitectura Netburst y Pentium III.

Al comenzar, Furfaro cuenta cuantos se presentaron y multiplica por 15 minutos,
la duración de un oral. Si en menos de 2 horas y media no le puede tomar a
todos, toma escrito. Es decir, si se presentan mas de ~10 esperen escribir.
A mi me tomo coherencia de cache, y las diferentes políticas que se pueden
implementar. Sepan mas de una, no solo MESI. Furfaro en el oral te va a dar una
tiza, usala! Hace todos los dibujos que puedas. No te olvides los controladores!

A otros les tomo ejecución fuera de orden. Sepan Scoreboarding y Tomasulo, con
sus respectivos diagramas.

No pierdan tiempo con los manuales. Con un repaso de lo que hicieron para el
TP3 alcanza. Los resúmenes de CUBA Wiki sirven para revisar estos temas
rápido. Denle mucha pelota a las filminas Arch1 a 3 y a los papers de Netburst
y Hyperthreading. Con eso alcanza tranquilamente para pasar.

La gente que se saco 10 en general estudio mas de lo que se había visto en la
materia, para tirar cancha. Con el siguiente material (curso de
Microarquitectura de Carnegie Mellon) te puede ir muy bien:
https://www.youtube.com/playlist...
http://www.ece.cmu.edu/~ece447/s13/doku.php?id=schedule

Me sumo a la recomendación de estudiar del curso de Microarquitectura de
carnagie mellon. El material es genial y aprendes bocha, además furfi se da
cuenta que estudiaste de ahí y te pone muy buena nota.

Carnegie Mellon - Computer Architecture 2015 - Onur Mutlu: Videos sobre los
temas del final (MUY recomendado). Las clases importantes son la
9, 10, 11, 12, 13, 17 y 18.

ver el resto de los threads en fb..

* papers
** art2-theUArchofpentium4
"A high-bandwidth IA-32 decoder,
that is capable of decoding several instructions per clock
cycle, takes several pipeline stages to do its work. When
a branch is mispredicted, the recovery time is much
shorter if the machine does not have to re-decode the IA-
32 instructions needed to resume execution at the
corrected branch target location. By caching the uops of
the previously decoded instructions in the Trace Cache,
the NetBurst microarchitecture bypasses the instruction
decoder most of the time thereby reducing misprediction
latency and allowing the decoder to be simplified: it only
needs to decode one IA-32 instruction per clock cycle."

"The simple, very frequent ALU operations go to the high-
speed integer ALU execution units described above.
Integer operations that are more complex go to separate
hardware for completion. Most integer shift or rotate
operations go to the complex integer dispatch port. These
shift operations have a latency of four clocks. Integer
multiply and divide operations also have a long latency.
Typical forms of multiply and divide have a latency of
about 14 and 60 clocks, respectively."

"The uop schedulers dispatch dependent operations before
the parent load has finished executing. In most cases, the
scheduler assumes that the load will hit the L1 data cache.
If the load misses the L1 data cache, there will be
dependent operations in flight in the pipeline. These
dependent operations that have left the scheduler will get
temporarily incorrect data. This is a form of data
speculation. Using a mechanism known as replay, logic
tracks and re-executes instructions that use incorrect data.
Only the dependent operations are replayed: the
independent ones are allowed to complete."

"In an out-of-order-execution processor, stores are not
allowed to be committed to permanent machine state (the
L1 data cache, etc.) until after the store has retired.
Waiting until retirement means that all other preceding
operations have completely finished.
All faults,
interrupts, mispredicted branches, etc. must have been
signaled beforehand to make sure this store is safe to
perform. With the very deep pipeline of the Pentium 4
processor it takes many clock cycles for a store to make it
to retirement. Also, stores that are at retirement often
have to wait for previous stores to complete their update
of the data cache. This machine can have up to 24 stores
in the pipeline at a time. Sometimes many of them have
retired but have not yet committed their state into the L1
data cache. Other stores may have completed, but have
not yet retired, so their results are also not yet in the L1
data cache. Often loads must use the result of one of
these pending stores, especially for IA-32 programs, due
to the limited number of registers available. To enable
this use of pending stores, modern out-of-order execution
processors have a pending store buffer that allows loads to
use the pending store results before the stores have been
written into the L1 data cache. This process is called
store-to-load forwarding."

"To make this store-to-load-forwarding process efficient,
this pending store buffer is optimized to allow efficient
and quick forwarding of data to dependent loads from the
pending stores. The Pentium 4 processor has a 24-entry
store-forwarding buffer to match the number of stores that
can be in flight at once. This forwarding is allowed if a
load hits the same address as a proceeding, completed,
pending store that is still in the store-forwarding buffer.
The load must also be the same size or smaller than the
pending store and have the same beginning physical
address as the store, for the forwarding to take place. This
is by far the most common forwarding case. If the bytes
requested by a load only partially overlap a pending store
or need to have some bytes come simultaneously from
more than one pending store, this store-to-load forwarding
is not allowed. The load must get its data from the cache
and cannot complete until the store has committed its state
to the cache."

"The FP divider executes all divide, square root, and
remainder uops. It is based on a double-pumped SRT
radix-2 algorithm, producing two bits of quotient (or
square root) every clock cycle."

"Achieving significantly higher floating-point and multi-
media performance requires much more than just fast
execution units. It requires a balanced set of capabilities
that work together. These programs often have many
long latency operations in their inner loops. The very
deep buffering of the Pentium 4 processor (126 uops and
48 loads in flight) allows the machine to examine a large
section of the program at once. The out-of-order-
execution hardware often unrolls the inner execution loop
of these programs numerous times in its execution
window. This dynamic unrolling allows the Pentium 4
processor to overlap the long-latency FP/SSE and
memory instructions by finding many independent
instructions to work on simultaneously. This deep
window buys a lot more performance for most FP/multi-
media applications than more execution units would."

"FP/multi-media applications usually need a very high
bandwidth memory subsystem. Sometimes FP and multi-
media applications do not fit well in the L1 data cache but
do fit in the L2 cache. To optimize these applications the
Pentium 4 processor has a high bandwidth path from the
L2 data cache to the L1 data. Some FP/multi-media
applications stream data from memory–no practical cache
size will hold the data. They need a high bandwidth path
to main memory to perform well. The long 128-byte L2
cache lines together with the hardware prefetcher
described below help to prefetch the data that the
application will soon need, effectively hiding the long
memory latency. The high bandwidth system bus of the
Pentium 4 processor allows this prefetching to help keep
the execution engine well fed with streaming data."

"Associated with the L2 cache is a hardware prefetcher that
monitors data access patterns and prefetches data
automatically into the L2 cache. It attempts to stay 256
bytes ahead of the current data access locations. This
prefetcher remembers the history of cache misses to
detect concurrent, independent streams of data that it tries
to prefetch ahead of use in the program. The prefetcher
also tries to minimize prefetching unwanted data that can
cause over utilization of the memory system and delay the
real accesses the program needs."
** Hyper-Threading Technology Architecture and Microarchitecture
"The amazing growth of the Internet and
telecommunications is powered by ever-faster systems
demanding increasingly higher levels of processor
performance. To keep up with this demand we cannot
rely entirely on traditional approaches to processor
design. Microarchitecture techniques used to achieve
past processor performance improvement–super-
pipelining, branch prediction, super-scalar execution,
out-of-order execution, caches–have made
microprocessors increasingly more complex, have more
transistors, and consume more power. In fact, transistor
counts and power are increasing at rates greater than
processor performance.
Processor architects are
therefore looking for ways to improve performance at a
greater rate than transistor counts and power
dissipation. Intel’s Hyper-Threading Technology is one
solution."

"A look at today’s software trends reveals that server
applications consist of multiple threads or processes that
can be executed in parallel. On-line transaction
processing and Web services have an abundance of
software threads that can be executed simultaneously
for faster performance. Even desktop applications are
becoming increasingly parallel. Intel architects have
been trying to leverage this so-called thread-level
parallelism (TLP) to gain a better performance vs.
transistor count and power ratio."

"The branch prediction structures are either duplicated or
shared. The return stack buffer, which predicts the
target of return instructions, is duplicated because it is a
very small structure and the call/return pairs are better
predicted for software threads independently. The
branch history buffer used to look up the global history
array is also tracked independently for each logical
processor. However, the large global history array is a
shared structure with entries that are tagged with a
logical processor ID."

"If there are uops for both logical processors in the uop
queue, the allocator will alternate selecting uops from
the logical processors every clock cycle to assign
resources. If a logical processor has used its limit of a
needed resource, such as store buffer entries, the
allocator will signal “stall” for that logical processor and
continue to assign resources for the other logical
processor. In addition, if the uop queue only contains
uops for one logical processor, the allocator will try to
assign resources for that logical processor every cycle to
optimize allocation bandwidth, though the resource
limits would still be enforced.

By limiting the maximum resource usage of key buffers,
the machine helps enforce fairness and prevents
deadlocks."

"The execution core and memory hierarchy are also
largely oblivious to logical processors. Since the source
and destination registers were renamed earlier to
physical registers in a shared physical register pool,
uops merely access the physical register file to get their
destinations, and they write results back to the physical
register file. Comparing physical register numbers
enables the forwarding logic to forward results to other
executing uops without having to understand logical
processors."

"Because logical processors can share data in the cache,
there is the potential for cache conflicts, which can
result in lower observed performance. However, there
is also the possibility for sharing data in the cache. For
example, one logical processor may prefetch
instructions or data, needed by the other, into the cache;
this is common in server application code. In a
producer-consumer usage model, one logical processor
may produce data that the other logical processor wants
to use. In such cases, there is the potential for good
performance benefits."

"To optimize performance when there is one software
thread to execute, there are two modes of operation
referred to as single-task (ST) or multi-task (MT). In
MT-mode, there are two active logical processors and
some of the resources are partitioned as described
earlier. There are two flavors of ST-mode: single-task
logical processor 0 (ST0) and single-task logical
processor 1 (ST1). In ST0- or ST1-mode, only one
logical processor is active, and resources that were
partitioned in MT-mode are re-combined to give the
single active logical processor use of all of the
resources.
The IA-32 Intel Architecture has an
instruction called HALT that stops processor execution
and normally allows the processor to go into a lower-
power mode. HALT is a privileged instruction, meaning
that only the operating system or other ring-0 processes
may execute this instruction. User-level applications
cannot execute HALT.
On a processor with Hyper-Threading Technology,
executing HALT transitions the processor from MT-
mode to ST0- or ST1-mode, depending on which logical
processor executed the HALT. For example, if logical
processor 0 executes HALT, only logical processor 1
would be active; the physical processor would be in
ST1-mode and partitioned resources would be
recombined giving logical processor 1 full use of all
processor resources. If the remaining active logical
processor also executes HALT, the physical processor
would then be able to go to a lower-power mode."

"A system with processors that use Hyper-Threading
Technology appears to the operating system and
application software as having twice the number of
processors than it physically has. Operating systems
manage logical processors as they do physical
processors, scheduling runnable tasks or threads to
logical processors. However, for best performance, the
operating system should implement two optimizations.
The first is to use the HALT instruction if one logical
processor is active and the other is not. HALT will
allow the processor to transition to either the ST0- or
ST1-mode. An operating system that does not use this
optimization would execute on the idle logical processor
a sequence of instructions that repeatedly checks for
work to do. This so-called “idle loop” can consume
significant execution resources that could otherwise be
used to make faster progress on the other active logical
processor.
The second optimization is in scheduling software
threads to logical processors. In general, for best
performance, the operating system should schedule
threads to logical processors on different physical
processors before scheduling multiple threads to the
same physical processor. This optimization allows
software threads to use different physical execution
resources when possible."

"Intel’s Hyper-Threading Technology brings the concept
of simultaneous multi-threading to the Intel
Architecture. This is a significant new technology
direction for Intel’s future processors. It will become
increasingly important going forward as it adds a new
technique for obtaining additional performance for
lower transistor and power costs."
* videos
** lecture 8 - pipelining (min. 40)
*** 1h20m: an ideal pipeline (seguir viendo que explican)
   - repetition of identical operations:
     the same operation is repeated on a large number of different inputs
   - repetition of independent operations:
     no dependencies between repeated operations
   - uniformly partitionable suboperations
     processing can be evenly divided into uniform-latency suboperations
     (that do not share resources)
*** 1h26m: causes of pipeline stalls
   resource contention
   dependencies (between instructions): data, control
   long-latency (multi-cycle) operations (e.g. division)
*** 1h34m: types of data dependences. ver lo que sigue que esta bueno
** lecture 14 - out of order execution   (07.35)
38:00 - tomasulo's algorithm
** lecture 16 - virtual memory 1 (min. 4.40)
*** 13.30
*** 19.20: pregunta: should all caches be built the same way?
   luego contestan
*** 33: entender DMA
*** ventaja de virtual memory: desfragmentas y el programa no se da cuenta
*** ventaja: 40.50 (shared C library)
*** ventaja: 43.30: el compilador puede asumir adonde va a poner el stack, etc.
   ver el diagrama que sigue, puede estructurar todos los programas iguales
*** 52.05, 53.57, 55.15
** lecture 22 - memory hierarchy
1:12:47 - cache de mapeo directo: cada bloque puede ir a un solo lugar de la cache
1:20:32 - asociatividad
1:24:10 - full associativity
1:26:36 - trade-offs
1:38:11 - approximations of LRU
1:44:29 - victim - next victim (facil de explicar en final)

