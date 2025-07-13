Descripción de la problemática: 
La gestión de una colección personal de libros, tanto fisicos como digitales, en ocasiones sigue siendo un desafio para muchos lectores. Muchos olvidan que libros tienen, sobre todo cuando la colección crece. En ocasiones también muchos lectores desean tener un seguimiento 
de su lectura, cuales han terminado, cuales planean leer o cuales tienen inconclusos. Mantener este conocimiento de una forma manual es complicado debido a que son propensas a errores o dificiles de actualizar. 

Solución Propuesta:
La solución propuesta para abordar esta problemática es, desarrollar una página web que sea SPA en donde se pueda tener una biblioteca personal digital, en donde la navegación sea sencilla e intuitiva que permita al usuario alternar de una manera rapida
entre la busqueda de libros así como la gestión de su colección, permitiendole al usuario poder añadir, actualizar o eliminar sus libros de una forma sencilla y rápida. Todo esto utilizando Vue.JS como lenguaje principal. 

Instrucciones de uso:
1.- Lo primero que se observa es la página inicial en donde está el campo de busqueda, aquí es donde uno debe poner ya sea el titulo del libro o un autor en especifico para ver los libros que ha escrito.
2.- Una vez realizada la busqueda, se visualizaran los resultados en forma de tarjetas, se mostrarán con portada (si está disponible) para guiarte mejor en caso de que hayan libros con nombres similares.
3.- Debajo de la tarjeta saldra la opción "Añadir a mi biblioteca". Saltará una alerta de confirmación y el libro se añadirá con el estado "Pendiente".
4.- Una vez agregado en la zona superior está el boton "Mi Biblioteca" para poder acceder donde están los libros guardados. 
5.- Aquí es donde se pueden filtrar los libros que has añadido en los diferentes estados ("Todos", "Pendientes", "En Lectura", "Finalizados").
6.- Si deseas cambiar el estado de un libro simplemente basta con hacer click en el estado y mostrará las opciones.
7.- Para eliminar un libro, sencillamente haz click en "Eliminar".

Todos los libros que se añadan a "Mi Biblioteca" y sus respectivos estados, automaticamente se guardan en "localStorage" del navegador.
