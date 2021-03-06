---

 

copyright:

  years: 2016

 

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock:.codeblock}
{:screen:.screen}
{:pre: .pre}

# Detalles del sistema {{site.data.keyword.openwhisk_short}}
{: #openwhisk_reference}
Última actualización: 9 de septiembre de 2016
{: .last-updated}

En las secciones siguientes se proporcionan más detalles sobre el sistema {{site.data.keyword.openwhisk}}.
{: shortdesc}

## Entidades de {{site.data.keyword.openwhisk_short}}
{: #openwhisk_entities}

### Espacios de nombres y paquetes
{: #openwhisk_entities_namespaces}

Las acciones, desencadenantes y reglas de {{site.data.keyword.openwhisk_short}} pertenecen a un espacio de nombres y, opcionalmente, a un paquete.

Los paquetes pueden contener acciones e información de entrada. Un paquete no puede contener otro paquete, por lo que no se permite anidamiento de paquetes. Además, las entidades no tienen que estar obligatoriamente contenidas en un paquete.

En Bluemix, un par organización+espacio corresponde a un espacio de nombres de {{site.data.keyword.openwhisk_short}}. Por ejemplo,
la organización `BobsOrg` y el espacio `dev` corresponderían al espacio de nombres
`/BobsOrg_dev` de {{site.data.keyword.openwhisk_short}}.

Puede crear sus propios espacios de nombres si está autorizado a ello. El espacio de nombres `/whisk.system`
se reserva para entidades distribuidas con el sistema {{site.data.keyword.openwhisk_short}}.


### Nombres completos
{: #openwhisk_entities_fullyqual}

El nombre completo de una entidad es `/namespaceName[/packageName]/entityName`. Observe que
se utiliza `/` para delimitar espacios de nombres, paquetes y entidades. Además, los espacios de nombres deben
tener una `/` como prefijo.

Por comodidad, el espacio de nombres se puede dejar fuera si es el *espacio de nombre predeterminado* del usuario.

Por ejemplo, supongamos un usuario cuyo espacio de nombres predeterminado es `/myOrg`. A continuación hay ejemplos
de los nombres completos de una serie de entidades y sus alias.

| Nombre completo | Alias | Espacio de nombres | Paquete | Nombre |
| --- | --- | --- | --- | --- |
| `/whisk.system/cloudant/read` |  | `/whisk.system` | `cloudant` | `read` |
| `/myOrg/video/transcode` | `video/transcode` | `/myOrg` | `video` | `transcode` |
| `/myOrg/filter` | `filter` | `/myOrg` |  | `filter` |

Usará este esquema de denominación cuando usa la CLI de {{site.data.keyword.openwhisk_short}}, entre otros lugares.

### Nombres de entidad
{: #openwhisk_entities_names}

Los nombres de todas las entidades incluidas las acciones, desencadenantes, reglas, paquetes y los espacios de nombres están en una secuencia de caracteres que cumplen el formato siguiente:

* El primer carácter debe ser un carácter alfanumérico, un dígito o un subrayado.
* Los caracteres posteriores pueden ser alfanuméricos, dígitos y espacios, de cualquiera de lo siguiente: `_`, `@`, `.`, `-`.
* El último carácter no puede ser un espacio.

Concretamente, un nombre debe coincidir con la siguiente expresión regular (expresada con la sintaxis de metacaracteres de Java):
`\A([\w]|[\w][\w@ .-]*[\w@.-]+)\z`.


## Semánticas de acción
{: #openwhisk_semantics}

En las secciones siguientes se describen los detalles sobre las acciones de
{{site.data.keyword.openwhisk_short}}.

### Falta de estado
{: #openwhisk_semantics_stateless}

Las implementaciones de acciones deberían ser sin estado, o *idempotent*. Aunque el sistema no impone esta propiedad,
no hay garantía de que cualquier estado mantenido por una acción esté disponible entre invocaciones.

Además, se puede dar la creación de varias instancias de una acción, teniendo cada creación de instancias su propio estado. Una
invocación de acción se podría asignar a cualquiera de estas creaciones de instancias.

### Entrada y salida de invocación
{: #openwhisk_semantics_invocationio}

La entrada y salida de una acción es un diccionario de pares de clave/valor. La clave es una serie, y el valor un valor JSON válido.

### Ordenación de invocaciones de acciones
{: #openwhisk_ordering}

Las invocaciones de una acción no están ordenadas. Si el usuario invoca una acción dos veces desde la línea de mandatos
o la API REST, la segunda invocación podría ejecutarse antes que la primera. Si las acciones tienen efectos secundarios,
se podrían observar en cualquier orden.

Además, no existe ninguna garantía de que las acciones se ejecuten de forma atómica. Dos acciones se pueden ejecutar de forma simultánea
y tener efectos secundarios que se entrelacen. OpenWhisk no asegura ningún modelo de coherencia simultáneo concreto en cuanto a efectos secundarios. Los efectos secundarios de simultaneidad dependerán de la implementación.

### Garantías de ejecución de acción
{: #openwhisk_atmostonce}

Cuando se recibe una solicitud de invocación, el sistema registra la solicitud y asigna una activación.

El sistema devuelve un ID de activación (en el caso de una invocación sin bloqueo) para confirmar que
la invocación se ha recibido.
Tenga en cuenta que si se produce un error de red o de otro tipo antes de recibir una respuesta HTTP, es posible que {{site.data.keyword.openwhisk_short}} haya recibido y procesado la solicitud.

El sistema intenta invocar la acción una vez, lo que tiene uno de los cuatro resultados siguientes:
- *success*: la invocación de la acción se ha completado correctamente.
- *application error*: la invocación de la acción ha sido correcta, pero la acción ha devuelto un valor de error a propósito,
por ejemplo debido a una condición previa de los argumentos que no se cumpla.
- *action developer error*: la acción se ha invocado, pero se ha completado de forma anómala, por ejemplo, la acción
no ha detectado una excepción o se ha producido un error de sintaxis.
- *whisk internal error*: el sistema no ha podido invocar la acción.
El resultado se registra en el campo `status` del registro de activación,
como documento en la sección siguiente.

Cada invocación que se recibe correctamente, y que se impute al usuario, tendrá un registro de activación.

Tenga en cuenta que en el caso de *error de desarrollador de acción*, es posible que la acción haya ejecutado de forma parcial y generado efectos colaterales visibles externamente.
El usuario es responsable de comprobar si dichos efectos colaterales se han producido en realidad y de emitir lógica de reintento si se desea.
Tenga en cuenta también que determinados *errores internos de whisk* indicarán que se ha iniciado la ejecución de una acción, pero que el sistema ha fallado antes de que la acción registrara su finalización.


## Registro de activación
{: #openwhisk_ref_activation}

Cada invocación de acción y activación de desencadenante tiene como resultado un registro de activación.

Un registro de activación contiene los campos siguientes:

- *activationId*: el ID de activación.
- *start* y *end*: indicaciones de fecha y hora que registran el inicio y final de la activación. Los valores
están en [formato de hora UNIX](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap04.html#tag_04_15).
- *namespace* y `name`: el espacio de nombres y el nombre de la entidad.
- *logs*: una matriz de series con los registros generados por la acción durante su activación. Cada elemento de matriz corresponde
con una salida de línea de `stdout` o `stderr` para la acción, e incluye la hora y secuencia de la salida de registro. La estructura es la siguiente: `TIMESTAMP STREAM: LOG_OUTPUT`.
- *response*: un diccionario que define las claves `success`, `status` y `result`:
  - *status*: el resultado de activación, que puede ser uno de los valores siguientes: "success", "application error", "action developer error", "whisk internal error".
  - *success*: es `true` solo si el estado es `"success"`
- *result*: un diccionario que contiene el resultado de activación. Si la activación ha sido correcta, contiene el valor devuelto por
la acción. Si la activación no ha sido correcta, `result` contiene la clave `error`,
generalmente con una explicación del fallo.


## Acciones JavaScript
{: #openwhisk_ref_javascript}

### Prototipo de función
{: #openwhisk_ref_javascript_fnproto}

Las acciones JavaScript de {{site.data.keyword.openwhisk_short}} se ejecutan en un tiempo de ejecución Node.js, actualmente en la versión 6.2.0.

Las acciones escritas en JavaScript se deben confinar a un único archivo. El archivo puede contener varias funciones pero,
por convenio, debe existir una función llamada `main` y es la que se llama cuando se invoca la acción. Por ejemplo,
a continuación se muestra un ejemplo de una acción con varias funciones.

```
function main() {
    return { payload: helper() }
}

function helper() {
    return new Date();
}
```
{: codeblock}

Los parámetros de entrada de la acción se pasan como un objeto JSON como un parámetro a la función
`main`. El resultado de la activación correcta también es un objeto JSON pero se devuelve de forma distinta,
según si la acción es síncrona o asíncrona, según se describe en la sección siguiente.


### Comportamiento síncrono y asíncrono
{: #openwhisk_ref_javascript_synchasynch}

Es frecuente para funciones de JavaScript continuar la ejecución en una función de devolución de llamada incluso después de la devolución. Para ajustarse a esto, una activación de una acción de JavaScript puede ser *síncrona* o *asíncrona*.

Una activación de una acción de JavaScript es **síncrona** si la función main sale bajo una de las condiciones siguientes:

- La función main sale sin ejecutar una sentencia `return`.
- La función main sale al ejecutar una sentencia `return` que devuelve cualquier valor
*excepto* un Promise.

A continuación se muestra un ejemplo de acción síncrona.

```
// una acción en la que cada vía tiene como resultado una activación síncrona
function main(params) {
  if (params.payload == 0) {
     return;
  } else if (params.payload == 1) {
     return {payload: 'Hello, World!'};
  } else if (params.payload == 2) {
    return whisk.error();   // indicates abnormal completion
  }
}
```
{: codeblock}

La activación de una acción de JavaScript es **asíncrona** si la función main sale devolviendo un Promise.  En este caso, el sistema asume que la acción sigue en ejecución, hasta que se haya cumplimentado o rechazado el Promise.
Empiece por crear una instancia de un nuevo objeto Promise y pasarlo a una función de devolución de llamada. La devolución de llamada tiene dos argumentos, resolve y reject, ambos funciones. Todo el código asíncrono va dentro de una devolución de llamada.


A continuación se muestra un ejemplo de cómo rellenar un Promise llamando a la función resolve.

```
function main(args) {
     return new Promise(function(resolve, reject) {
       setTimeout(function() {
         resolve({ done: true });
       }, 100);
    })
 }
```
{: codeblock}

A continuación se muestra un ejemplo de cómo rechazar un Promise llamando a la función reject.

```
function main(args) {
     return new Promise(function(resolve, reject) {
       setTimeout(function() {
         reject({ done: true });
       }, 100);
    })
 }
```
{: codeblock}

Es posible que una acción sea síncrona en varias entradas y asíncrona en otras. A continuación se muestra un ejemplo.

```
  function main(params) {
      if (params.payload) {
         // activación asíncrona
         return new Promise(function(resolve, reject) {
                setTimeout(function() {
                  resolve({ done: true });
                }, 100);
             })
      } else {
         // activación síncrona
         return {done: true};
      }
  }
```
{: codeblock}

Observe que, independientemente de si la activación es síncrona o asíncrona, la invocación de la acción puede ser o no de bloqueo (blocking o non-blocking).

### Métodos SDK adicionales

La función `whisk.invoke()` invoca otra acción y devuelve un Promise de la activación resultante. Acepta como argumento un diccionario que define los parámetros siguientes:

- *name*: nombre completo de la acción a invocar,
- *parameters*: un objeto JSON que representa la entrada a la acción invocada. Si se omite, tiene como valor predeterminado un objeto vacío.
- *apiKey*: la tecla de autorización con la que invocar la acción. Tiene como valor predeterminado `whisk.getAuthKey()`.
- *blocking*: si la acción se debe invocar en modalidad de bloque o no bloqueo. Cuando el `bloqueo` es true, la invocación esperará el resultado de la acción invocada antes de resolver el Promise devuelto. Tiene como valor predeterminado
`false`, indicando una invocación que no es de bloqueo.

`whisk.invoke()` devuelve un Promise. Para hacer que el sistema OpenWhisk espere a que termine la invocación, debe devolver este Promise desde la función `principal` de la acción.
- Si la invocación falla, el promise enviará un rechazo con un objeto indicando que la invocación ha fallado. Puede tener dos campos:
  - *error*: un objeto, normalmente una cadena, que describe el error.
  - *activation*: un diccionario opcional que puede estar o no presente en función de la naturaleza del error de la invocación. En caso de estar presente, tendrá los siguientes campos:
    - *activationId*: el ID de activación:
    - *result*: si la acción se ha invocado en modalidad de bloqueo: el resultado de la acción como un objeto JSON, o
bien `undefined`.
- Si la invocación se realiza correctamente, el promise se resolverá con un diccionario que describirá la activación con los campos *activationId* y *result* descritos previamente.

A continuación, se muestra un ejemplo de una invocación de bloqueo que utiliza el promise devuelto:
```javascript
return whisk.invoke({
  name: 'myAction',
  blocking: true
})
.then(function (activation) {
    // activation completed successfully, activation contains the result
    console.log('Activation ' + activation.activationId + ' completed successfully and here is the result ' + activation.result);
})
.catch(function (reason) {
    console.log('An error has occured ' + reason.error);

    if(reason.activation) {
      console.log('Please check activation ' + reason.activation.activationId + ' for details.');
    } else {
      console.log('Failed to create activation.');
    }
});
```
{: codeblock}

La función `whisk.trigger()` activa un desencadenante y devuelve un Promise para la activación resultante. Acepta como argumento un objeto JSON con los parámetros siguientes:

- *name*: nombre completo del desencadenante a invocar.
- *parameters*: un objeto JSON que representa la entrada al desencadenante. Si se omite, tiene como valor predeterminado un objeto vacío.
- *apiKey*: la tecla de autorización con la que activar el desencadenante. Tiene como valor predeterminado `whisk.getAuthKey()`.

`whisk.trigger()` devuelve un Promise. Si necesita que el sistema OpenWhisk espere a que termine el desencadenante, debe devolver este Promise desde la función `principal` de la acción.
- Si el desencadenante falla, el promise enviará un rechazo con un objeto describiendo el error.
- Si el desencadenante se completa correctamente, el promise se resolverá con un diccionario con el campo `activationId` que incluirá el ID de activación.

La función `whisk.getAuthKey()` devuelve la clave de autorización bajo la que se ejecuta la acción. Normalmente,
no necesita invocar esta función directamente, ya que se utiliza implícitamente por parte de las funciones
`whisk.invoke()` y `whisk.trigger()`.

### Entornos de ejecución JavaScript
{: #openwhisk_ref_javascript_environments}

Las acciones de JavaScript se ejecutan de forma predeterminada en un entorno de Node.js versión 6.2.0.  El entorno 6.2.0 se utilizará también para una acción si se especifica de forma explícita el distintivo `--kind` con un valor de 'nodejs:6' al crear/actualizar la acción.
Los paquetes siguientes están disponibles para su uso en el entorno de Node.js 6.2.0:

- apn v1.7.5
- async v1.5.2
- body-parser v1.15.1
- btoa v1.1.2
- cheerio v0.20.0
- cloudant v1.4.1
- commander v2.9.0
- consul v0.25.0
- cookie-parser v1.4.2
- cradle v0.7.1
- errorhandler v1.4.3
- express v4.13.4
- express-session v1.12.1
- gm v1.22.0
- log4js v0.6.36
- iconv-lite v0.4.13
- merge v1.2.0
- moment v2.13.0
- mustache v2.2.1
- nano v6.2.0
- node-uuid v1.4.7
- nodemailer v2.5.0
- oauth2-server v2.4.1
- pkgcloud v1.3.0
- process v0.11.3
- pug v2.0.0
- request v2.72.0
- rimraf v2.5.2
- semver v5.1.0
- sendgrid v3.0.11
- serve-favicon v2.3.0
- socket.io v1.4.6
- socket.io-client v1.4.6
- superagent v1.8.3
- swagger-tools v0.10.1
- tmp v0.0.28
- twilio v2.9.1
- watson-developer-cloud v1.12.4
- when v3.7.7
- ws v1.1.0
- xml2js v0.4.16
- xmlhttprequest v1.8.0
- yauzl v2.4.2

El entorno Node.js versión 0.12.14 se utilizará para una acción si se especifica de forma explícita el distintivo `--kind` con un valor de 'nodejs' al crear/actualizar la acción.
Los paquetes siguientes están disponibles para su uso en el entorno de Node.js 0.12.14:

- apn v1.7.4
- async v1.5.2
- body-parser v1.12.0
- btoa v1.1.2
- cheerio v0.20.0
- cloudant v1.4.1
- commander v2.7.0
- consul v0.18.1
- cookie-parser v1.3.4
- cradle v0.6.7
- errorhandler v1.3.5
- express v4.12.2
- express-session v1.11.1
- gm v1.20.0
- jade v1.9.2
- log4js v0.6.25
- merge v1.2.0
- moment v2.8.1
- mustache v2.1.3
- nano v5.10.0
- node-uuid v1.4.2
- oauth2-server v2.4.0
- process v0.11.0
- request v2.60.0
- rimraf v2.5.1
- semver v4.3.6
- serve-favicon v2.2.0
- socket.io v1.3.5
- socket.io-client v1.3.5
- superagent v1.3.0
- swagger-tools v0.8.7
- tmp v0.0.28
- watson-developer-cloud v1.4.1
- when v3.7.3
- ws v1.1.0
- xml2js v0.4.15
- xmlhttprequest v1.7.0
- yauzl v2.3.1

## Acciones Python

Las acciones Python se ejecutan de forma predeterminada utilizando Python 2.7.12.
Además de la biblioteca estándar Python, las acciones Python también pueden utilizar los siguientes paquetes.

- attrs v16.1.0
- beautifulsoup4 v4.5.1
- cffi v1.7.0
- click v6.6
- cryptography v1.5
- cssselect v0.9.2
- enum34 v1.1.6
- flask v0.11.1
- gevent v1.1.2
- greenlet v0.4.10
- httplib2 v0.9.2
- idna v2.1
- ipaddress v1.0.16
- itsdangerous v0.24
- jinja2 v2.8
- lxml v3.6.4
- markupsafe v0.23
- parsel v1.0.3
- pyasn1 v0.1.9
- pyasn1-modules v0.0.8
- pycparser v2.14
- pydispatcher v2.0.5
- pyopenssl v16.1.0
- python-dateutil v2.5.3
- queuelib v1.4.2
- requests v2.11.1
- scrapy v1.1.2
- service-identity v16.0.0
- simplejson v3.8.2
- six v1.10.0
- twisted v16.4.0
- w3lib v1.15.0
- werkzeug v0.11.10
- zope.interface v4.3.1

## Acciones de Docker
{: #openwhisk_ref_docker}

Las acciones Docker ejecutan un binario proporcionado por el usuario en un contenedor Docker. El binario se ejecuta en una imagen Docker basada en [python:2.7.12-alpine](https://hub.docker.com/r/library/python), por lo que el binario debe ser compatible con dicha distribución.

El esqueleto de Docker es una forma cómoda de crear imágenes Docker compatibles con OpenWhisk. Puede instalar el esqueleto con el mandato de CLI de `wsk sdk install docker`.

El programa binario principal debe estar en `/action/exec` dentro del contenedor. El ejecutable recibe los argumentos de entrada a través de `stdin` y debe devolver un resultado a través de `stdout`.

Puede incluir los pasos de compilación o dependencias modificando el `archivo de Docker` incluido en `dockerSkeleton`.

## API REST
{: #openwhisk_ref_restapi}

Todas las funciones del sistema están disponibles a través de la API REST. Hay una colección y puntos finales de entidad para acciones, activadores, reglas, paquetes, activaciones y espacios de nombres.

Los puntos finales de colección son:

- `https://openwhisk.{DomainName}/api/v1/namespaces`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/actions`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/triggers`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/rules`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/packages`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/activations`

`openwhisk.{DomainName}` es el nombre de host de la API de OpenWhisk (por ejemplo, openwhisk.ng.bluemix.net, 172.17.0.1, etc.).

Para `{namespace}`, se puede utilizar el carácter `_` para especificar el *espacio de nombre predeterminado* del usuario (es decir, la dirección de correo electrónico).

Puede realizar una solicitud GET en los puntos finales de colección para obtener una lista de todas las entidades de la colección.

Hay puntos finales de entidad para cada tipo de entidad:

- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/actions/[{packageName}/]{actionName}`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/triggers/{triggerName}`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/rules/{ruleName}`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/packages/{packageName}`
- `https://openwhisk.{DomainName}/api/v1/namespaces/{namespace}/activations/{activationName}`

Los puntos finales de espacio de nombres y activación solo admiten solicitudes GET. Los puntos finales de acciones, desencadenantes, reglas y paquetes admiten solicitudes GET, PUT y DELETE. Los puntos finales de acciones, activadores y reglas también admiten solicitudes POST, que se utilizan para invocar acciones y activadores, y para habilitar o inhabilitar reglas. Consulte la [Referencia de API](https://new-console.{DomainName}/apidocs/98) para obtener información detallada.

Todas las API están protegidas con autenticación HTTP básica. Las credenciales de autenticación básica se encuentran en la propiedad `AUTH` del archivo `~/.wskprops`, delimitadas por dos puntos. También puede recuperar estas credenciales en los [pasos de configuración de la CLI](../README.md#setup-cli).

A continuación se muestra un ejemplo en el que se utiliza el mandato cURL para obtener la lista de todos los paquetes del espacio de nombres `whisk.system`:

```
curl -u USERNAME:PASSWORD https://openwhisk.ng.bluemix.net/api/v1/namespaces/whisk.system/packages
```
{: pre}
```
[
  {
    "name": "slack",
    "binding": false,
    "publish": true,
    "annotations": [
      {
        "key": "description",
        "value": "Package that contains actions to interact with the Slack messaging service"
      }
    ],
    "version": "0.0.9",
    "namespace": "whisk.system"
  },
  ...
]
```
{: screen}

La API de OpenWhisk admite llamadas solicitud-respuesta de clientes web. OpenWhisk responde a las solicitudes de `OPTIONS` con cabeceras de uso compartido de recursos de distintos orígenes. Actualmente, estos orígenes están permitidos (es decir, Access-Control-Allow-Origin es "`*`") y Access-Control-Allow-Headers produce Authorization y Content-Type.

**Atención:** debido a que OpenWhisk admite en este momento únicamente una clave por cuenta, no se recomienda utilizar CORS si no es para experimentar. La clave debería incorporarse en el código del lado del cliente, haciéndola visible al público. Utilícelo con precaución.

## Límites del sistema
{: #openwhisk_syslimits}

### Acciones
{{site.data.keyword.openwhisk_short}} tiene algunos límites del sistema, incluyendo la cantidad de memoria que utiliza una acción y
cuántas invocaciones de acción se permiten por hora. En la tabla siguiente se proporciona una lista con los límites predeterminados de las acciones. 

| límite | descripción | configurable | unidad | predeterminado |
| ----- | ----------- | ------------ | -----| ------- |
| timeout | un contenedor no tiene permiso para ejecutarse más de N milisegundos | por acción |  milisegundos | 60000 |
| memory | un contenedor no tiene permiso para asignar más de n MB de memoria | por acción | MB | 256 |
| logs | un contenedor no tiene permiso para escribir más de N MB en stdout | por acción | MB | 10 |
| concurrent | no se permiten más de N activaciones simultáneas por espacio de nombres | por espacio de nombres | número | 100 |
| minuteRate | un usuario no puede invocar más de este número de acciones por minuto | por usuario | número | 120 |
| hourRate | un usuario no puede invocar más de este número de acciones por hora | por usuario | número | 3600 |
| codeSize | tamaño máximo de actioncode | no configurable, límite por acción | MB | 48 |
| parameters | tamaño máximo de los parámetros que se pueden adjuntar | no configurable, límite por acción/paquete/desencadenante | MB | 1 |

### Tiempo de espera por acción (ms) (Predeterminado: 60s)
{: #openwhisk_syslimits_timeout}
* El límite de tiempo de espera N está en el intervalo [100ms..300000ms] y se establece por acción, en milisegundos.
* Un usuario puede cambiar el límite cuando crea la acción.
* Un contenedor que se ejecuta más de N milisegundos, se finaliza.

### Memoria por acción (MB) (Predeterminado: 256MB)
{: #openwhisk_syslimits_memory}
* El límite de memoria M está en el intervalo [128MB..512MB] y se establece por acción en MB.
* Un usuario puede cambiar el límite cuando crea la acción.
* Un contenedor no puede tener más memoria asignada del límite.

### Registros por acción (MB) (Predeterminado: 10MB)
{: #openwhisk_syslimits_logs}
* El límite de registro N está en el intervalo [0MB..10MB] y se establece por acción.
* Un usuario puede cambiar el límite cuando crea o actualiza la acción.
* Los registros que superen el límite establecido se truncan y se añade un aviso como última salida de la activación para indicar que la activación ha sobrepasado el límite de registro establecido.

### Artefacto por acción (MB) (Fijo: 48MB)
{: #openwhisk_syslimits_artifact}
* El tamaño máximo de código de la acción es de 48MB.
* Se recomienda que, para una acción de JavaScript, se utilice una herramienta para concatenar todo el código fuente incluyendo las dependencias en un único archivo empaquetado.

### Tamaño de carga útil por activación (MB) (fijo: 1MB)
{: #openwhisk_syslimits_activationsize}
* El tamaño de contenido POST máximo más los posibles parámetros para una invocación de acción o activación de desencadenante es de 1 MB.

### Invocación simultánea por espacio de nombres (Predeterminado: 100)
{: #openwhisk_syslimits_concur}
* El número de activaciones que hay actualmente en proceso para un espacio de nombres no pueden ser más de 100.
* El límite predeterminado se puede configurar de forma estática por medio de whisk en consul kvstore.
* Un usuario no puede actualmente cambiar los límites.

### Invocaciones por minuto/hora (Fijo: 120/3600)
{: #openwhisk_syslimits_invocations}
* El límite de tasa N se establece en 120/3600, y limita el número de invocaciones de acción en espacios de tiempo de un minuto/hora.
* Un usuario no puede cambiar este límite cuando crea la acción.
* Una llamada de la CLI o API que sobrepase este límite recibe un código de error correspondiente al código de estado de HTTP `429: DEMASIADAS SOLICITUDES`.

### Tamaño de los parámetros (Fijo: 1MB)
{: #openwhisk_syslimits_parameters}
* El límite de tamaño de los parámetros al crear o actualizar una acción, paquete o desencadenante es de 1MB.
* El usuario no puede cambiar el límite.
* Se rechazará la creación o actualización de una entidad con parámetros demasiado grandes.

### Límite (ulimit) de archivos abiertos de acción por acoplador (Fijo: 64:64)
{: #openwhisk_syslimits_openulimit}
* El número máximo de archivos abiertos es de 64 (tanto para el límite absoluto como el flexible).
* El mandato docker run utiliza el argumento `--ulimit nofile=64:64`.
* Para obtener más información sobre el límite (ulimit) de archivos abiertos, consulte la documentación de
[docker run](https://docs.docker.com/engine/reference/commandline/run).

### Límite (ulimit) de procesos de acción por acoplador (Fijo: 512:512)
{: #openwhisk_syslimits_proculimit}
* El número máximo de procesos disponibles para un usuario es de 512 (tanto para el límite absoluto como el flexible).
* El mandato docker run utiliza el argumento `--ulimit nproc=512:512`.
* Para obtener más información sobre el límite (ulimit) del número máximo de procesos, consulte la documentación de
[docker run](https://docs.docker.com/engine/reference/commandline/run).

### Desencadenantes

Los desencadenantes están sujetos a una tasa de activación por minuto y por hora tal como se indica en la tabla siguiente.

| límite | descripción | configurable | unidad | predeterminado |
| ----- | ----------- | ------------ | -----| ------- |
| minuteRate | un usuario no puede activar más de este número de desencadenantes por minuto | por usuario | número | 60 |
| hourRate | un usuario no puede activar más de este número de desencadenantes por hora | por usuario | número | 720 |

### Desencadenantes por minuto/hora (fijo: 60/720)
{: #openwhisk_syslimits_triggerratelimit}
* El límite de tasa N se establece en 60/720 y limita el número de desencadenantes que se pueden activar en intervalos de un minuto/hora. 
* Un usuario no puede cambiar este límite cuando crea el desencadenante. 
* Una llamada de la CLI o API que sobrepase este límite recibe un código de error correspondiente al código de estado de HTTP `429: DEMASIADAS SOLICITUDES`.
