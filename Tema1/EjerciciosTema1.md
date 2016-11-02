#Ejercicios1: Buscar una aplicación de ejemplo, preferiblemente propia, y deducir qué patrón es el que usa. ¿Qué habría que hacer para evolucionar a un patrón tipo microservicios?

La aplicación que he usado es una propia realizada con el framework Django Python, que se encarga de la gestión de distintos bares de Quesada (Jaén) y sus variadas tapas. 

Esta aplicación usa un patrón de arquitectura MVC (Modelo-Vista-Controlador). Por lo que tiene bien definidas las partes de negocio, vistas del usuario y base de datos. 

Para evolucionar este patrón a un patrón tipo microservicios, lo primero que haría sería dividir mi aplicación en distintos módulos. Una vez hecho esto procedería a descentralizar la base de datos. Con esto quiero decir que, en vez de que cada módulo tenga que acudir a la misma base datos, cada modulo contará con su propia base de datos. De esta forma evitariamos sobrecargarla y que un fallo en la base de datos provocara la caida de todo el sistema.
Así dividiría mi aplicación en distintos módulos multifuncionales, adaptnado así un módulo común a todos para que ofrezca un servicio determinado.

#Ejercicios2: En la aplicación que se ha usado como ejemplo en el ejercicio anterior, ¿podría usar diferentes lenguajes? ¿Qué almacenes de datos serían los más convenientes?

Sí podría usar diferentes lenguajes. Una vez realizada la aplicación mencionada anteriormente, al cambiar a una arquitectura de microservicios, se podría usar un lenguaje distinto para cada módulo o servicio del sistema.

El almacén de datos más conveniente sería MongoDB ya que es una base de datos NoSQL y ayudaría a esta debido a su lenguaje no tipado.
