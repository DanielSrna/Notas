Dentro del desarrollo backend con [[Node.js]], los endpoint son cosas que vamos a ver mucho por todos lados. Básicamente, como hemos visto anteriormente, las API son programas que sirven como un intercomunicador entre el cliente, y el servidor. Pues, los endpoint, son los canales de ese intercomunicador.
Podría decirse que un endpoint es un camino por el cual el cliente envía su petición, y el servidor le devuelve una respuesta. 
## ¿Cómo está construido un endpoint?
Los endpoint están construidos bajo una estructura tradicional que no solo garantiza la rápida respuesta a las peticiones del cliente, sino, otros factores importantes como:
1. La seguridad del servidor.
2. Asegurarse que de hecho el usuario que hace la petición tenga permisos para recibir esa respuesta.
3. Que la petición hecha por el usuario sea correcta.
4. Que los formatos de los datos de la petición coincidan con los planificados para la DB.
Si retomamos nuestro ejemplo anterior sobre que un endpoint es un camino. Podría decirse que en ese camino hay diferentes retenes policiales en donde se verifica que la petición enviada sea correcta, no busque dañar la integridad del servidor, y que el usuario que la envió tenga los permisos para recibir una respuesta.
## ¿Cómo se ofrecen al cliente?
Los endpoints están **expuestos** para que el cliente pueda encontrarlos. Sin embargo, que estén disponibles no significa que cualquiera pueda obtener datos. Si el 'camino' está protegido, el servidor rechazará a quien no tenga las llaves (tokens o permisos), aunque conozca la dirección.
Más adelante, vamos a ver como se usan estos caminos cuando veamos Peticiones HTTP.