### 1.- Crear credenciales de autorizacion

Para poder hacer uso de ***YouTube Data API (v3)*** el primer paso es crear las credenciales (OAuth 2.0) desde [console developers google](https://console.developers.google.com).

Desde el ***administrador de APIs*** creamos un nuevo proyecto:
![capute-01](/img/capture-01.png)

![capture-02](/img/capture-02.png)

![capture-03](/img/capture-03.png)

Posteriormente, en el menu lateral izquierdo accedemos a la opcion ***Credenciales*** y creamos las nuevas credenciales dando clic en **Crear credenciales/ID de cliente OAuth**:

![capture-04](/img/capture-04.png)

Se nos solicitara definir un nombre de producto en la pantalla de autorizacion, esto es simplemente para dar mas detalles de nuestra Aplicacion,

![capture-05](/img/capture-05.png)

Los campos requeridos son:
	* Direccion de correo electrónico
	* Nombre de producto mostrado a los usuarios

![capture-06](/img/capture-06.png)

Una vez guardados los cambios nos mostrara nuevamente la siguiente pantalla, y nuevamente damos clic en **Crear credenciales/ID de cliente OAuth**

![capture-04](/img/capture-04.png)

Para finalizalmente solicitarnos el tipo de aplicacion en la que usaremos las claves autorizacion,

![capture-07](/img/capture-07.png)

En el apartado de ***Restricciones*** es **IMPORTANTE** que se indique la URL donde estara haciendo response nuestra aplicacion para la aprobacion de las credenciales.
	* **Origenes de JavaScript autorizados:** En este apartado se añade la URL, siempre y cuando la API que estemos usando este desarrollada en JavaScript del lado del cliente.
	* **URIs de redireccionamiento autorizados:** Para aplicaciones en las que se este usando una API de desarrollo del lado del servidor, se debe indicar la(s) URIs de redireccionamiento.


**NOTA: en este ejemplo se realizo de manera local, por lo que en produccion es importante agregar la URL del host publico**

Una vez creada las credenciales de autorizacion, se nos mostrara el ***ID de cliente*** y el ***secreto de cliente***, las cuales las usaremos posteriormente para añadirlos en el codigo de nuestra APP.

![capture-08](/img/capture-078.png)

### ISSUES
#### Server Side
![capture-09](/img/capture-09.png)

Es muy comun encontrarse con este tipo de errores al querer ejecutar nuestra aplicacion, y este error es debido a que en el apartado de ***Restricciones*** no se añadio una URI de manera correcta, o no se tiene acceso a nuestra APP desde esa URI, por lo que se deben añadir todas las posibles URIs de acceso a nuestra APP, example:

	*https://localhost/yt/index.php
	*http://localhost/yt/index.php
	*http://localhost:8080/yt/index.php
	*ETC.

Aun así el propio mensaje de error indica la URI a la cual esta intentando realizar la peticion correspondiente, y esa URI tal cual, debera ser indicada en el apartado de **URIs de redireccionamiento autorizados:**.
![capture-10](/img/capture-10.png)

#### Client Side
![capture-11](/img/capture-11.png)

De igual manera al configurar nuestra aplicacion desarrolldad en JavaScript, es muy comun encontrarse con ese error debido a que no se añadio de manera correcta la URL en el apartado **Origenes de JavaScript autorizados:**, es por ello que se debe añadir la URL de acceso a nuestra APP, example:

	*https://localhost
	*https://localhost:8080
	*ETC.