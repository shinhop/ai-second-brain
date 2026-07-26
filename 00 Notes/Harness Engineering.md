Ecostistema para manejar la AI. Queremos controlar:
* Contexto
* Memoria
* Validación

Cuando más complejo es el harness menos eficiente es el agente
Cuanto mas tiempo utilizas la AI mas tonta se vuelve. Context corruption.

Que aprendemos:
* La gestion del contexto es de vital importancia.
* Para limpiar el contexto, la idea es poder extraer parte del contexto actual a una memoria exterior (persistente o no) para que la ventana de contexto actual sea menor y la AI trabaje mejor. 

Otra parte importante del diseño del harness es proveer al agente herramientas para que pueda verificar que lo que esta construyendo es correcto. En otras palabras, se debe añadir instrucciones precisas al modelo para que sepa como verificar su implementación.

Vamos a simplificar, tres pilares basicos:
* Utilizar el propio repositorio como el sistema.
* Orchestracion multi agente
* Verificación.