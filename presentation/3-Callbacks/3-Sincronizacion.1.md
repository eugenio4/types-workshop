## Sincronización de callbacks

Situación: "Para cargar nuestra página de inicio tenemos que llamar a 3 servicios,
pero no queremos pintar la página hasta tener la respuesta de los tres servicios"

--
Condiciones de carrera
````javascript
function getData1 () { request("http://api.com/data1") }

function getData2 () { request("http://api.com/data2") }

function getData3 () { request("http://api.com/data2") }

function pintar(){
  //pintamos nuestra página
}

getData1();
getData2();
getData3();
pintar();
````
--
Problema de rendimiento

````javascript
function getData1 () { request("http://api.com/data1", getData2) }
function getData2 () { request("http://api.com/data2", getData3) }
function getData3 () { request("http://api.com/data2", pintar) }
function pintar() {
//pintamos nuestra página
}
getData1();
````

--

Una solución

````javascript
let numSuccessRequest = 0;
function success () {
  numSuccessRequest++;
  pintar();
}
function fail () {}
function getData1 () { request("http://example.com/data1", success, fail) }
function getData2 () { request("http://example.com/data2", success, fail}
function getData3 () { request("http://example.com/data2", success, fail}

function pintar(){
  if (pintar === 3) {
    //pintamos nuestra página
  }
}
function init(){
  getData1();
  getData2();
  getData3();
}

init();
````

--
* ¿Qué pasa si falla alguna de las llamadas?
* ¿Si se lanza alguna excepción en otra parte del código asíncrono?
* Sí alguna llamada asíncrona nunca termina.

![Scared](/img/scared.png)

--

Hay que aumentar la lógica del código, todo ese control de llamadas asíncronas es lo que se llama "Callback hell".