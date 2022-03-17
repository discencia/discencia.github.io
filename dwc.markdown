---
layout: page
title: UT08 - Firebase
permalink: /dwcut08/
---

# FireBase

Es una plataforma de apoyo al desarrollo web en la nube creada por Google. Facilita el alojamiento de base de datos no relacional (que es lo que se usará en el ejemplo) y cuya característica más importante es que permite la sincronización en tiempo real. Además, _Firebase_ ofrece otras servicios de autentificación, estadísticas de uso, de mensajería y de almacenamiento entre otros. Más información [aquí](https://firebase.google.com/docs/web/setup?hl=es-419).

Uno de los servicios que incluye es el _Firestore_ _Database_ la cual permite disponer de una base de datos no relacional y reactiva que sincroniza datos con todos los clientes conectados en tiempo real. Se tratarán las acciones **CRUD** sobre esta base de datos a través del siguiente ejemplo. Para facilitar el seguimiento del mismo, se aconseja visitar [este enlace](https://firebase.google.com/docs/firestore/manage-data/add-data) donde se detallan más los pasos a seguir. Huelga decir que será necesario disponer de una cuenta en este servicio para poder trabajar con el ejemplo.

## Tipos de bases de datos

Existen dos tipos principales de bases de datos: las **SQL** o relacionales y las **NoSQL**. Las primeras son muy populares entre lenguajes como **PHP** y están orientadas a aplicaciones muy complejas (la mayoría de las veces). Las **NoSQL** funcionan genial con lenguajes como _JavaScript_ y permite puentear las tecnologías de servidor para el acceso a la información.

Las bases de datos **NoSQL** almacenan nuestros datos en un contenedor al que llama _database instance_. Dentro de ese contenedor los datos se separan en secciones llamadas _collections_. Así, es posible tener una _collection_ que almacene usuarios, otra que almacene artículos, otra que almacene comentarios, etcétera. Dentro de cada _collection_ se almacenan los _documents_, que disponen de un único valor. Cada _document_ tiene un identificador único.
Dentro de este documento, está su información con una disposición muy parecido a un objeto de _JavaScript_, ya que se compone de pares **clave-valor**.

![Figura 1](./resources/img/img1.png)

_Firebase_ ofrece varios servicios para la creación de webapps. Uno de ellos es el de almacenamiento de la información. Dispone de dos servicios de bases de datos: _Firestore_ _Database_ y _Realtime_ _Database_. _Realtime_ _Database_ es la primera base de datos que liberó _Firebase_ con una alta eficiencia, baja latencia y capacidades para consultas en tiempo real. _Firestore_ _Database_ es la nueva base de datos diseñada y desarrollada por Google la cual tiene una estructura más fácil de entender y mejoras en los **SDK** para la realización de consultas.

La estructura de datos de _Realtime_ _Database_ almacena los datos en **JSON** pero se organiza como un árbol. Esta forma es compleja cuando crece la información. _Firestore_ _Database_ tiene una estructura visible en la consola más sencilla y fácil de escalar cuando se tiene grandes cantidades de datos gracias a las subcolecciones.

Por lo que respecta a las consultas, _Realtime_ _Database_ presenta serias limitaciones a la hora del filtrado de datos, además, cuando se realiza una consulta retorna todo el subárbol del nodo. _Firestore_ _Database_ soporta consultas compuestas y puede combinar filtros con ordenamiento además de indexar todas las consultas por defecto. Al consultar sólo trae el documento sin traer subcolecciones o hijos, esta información se solicita según las necesidades de la aplicación.

Por estos motivos, en lo sucesivo se trabajará con _Firestore_ _Database_ quedando la exploración de _Realtime_ _Database_ en manos del discente.

## Creación de una base de datos

Será necesario el registro de una nueva cuenta [aquí](https://firebase.google.com/) para poder utilizar estos servicios. Una vez realizado este paso la creación de la base de datos es muy intuitiva y se realiza en tres pasos:

- crea el proyecto que contendrá, entre otras cosas, la base de datos
- configura el _Google Analytics_ si es necesario (en nuestros ejemplos no es buen idea ya que aumenta el tráfico entre la aplicación y el servidor)
- creación de una appweb para que consuma los datos de la nueva base de datos

Tras esto, será necesario crear la base de datos y se hará creando un dato de ejemplo. No existe una estructura como en una base de datos **SQL**, sino que cada colección, e incluso cada documento, puede tener su propia estructura diferente al resto.

![Figura 2](./img/img2.png)

Para crea las colecciones (así se llaman las divisiones en bases de datos **NoSQL**) está disponible el siguiente formulario. Si se tiene clara la estructura de las colecciones el manejo de esta interfaz resulta evidente. En la imagen muestra la colección feos que dispone de seis documentos (identificados por su id) y cada uno de esos documentos posee varias propiedades: aficiones (listado de aficiones), nombre y apellidos.

Desde esta ventana se puede acceder a la pestaña **Reglas** que indicará qué acciones se pueden hacer o no sobre la base de datos.

![Figura 3](./img/img3.png)

Será necesario revisar la documentación para ampliar estos permisos e incluso asignarlos de forma personalizada por usuario y colección. Además, se podrán crear índices para agilizar las consultas y comprobar el uso que se le está dando a la base de datos.

Tras agregar una webapp al proyecto, los datos de conexión estarán disponibles en su apartado de configuración. Se dispone de varios métodos de conexión. Para el proyecto de muestra se utilizará conexión a través de CDN (_Content Delivery Network_).

## Conectar con la base de datos

_Firestore_ utiliza la tecnología de módulos para optimizar la carga de sus bibliotecas, a cuya documentación se podrá acceder desde [aquí](https://firebase.google.com/docs/reference/js/firestore_.md).

Será necesario la creación de un fichero de configuración y acceso a la aplicación y para ello se utiliza module:

```js
"use strict";
// Importar las funciones necesarias desde su biblioteca.
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
// Configurar el objeto con los datos de acceso de *Firestore*.
const firebaseConfig = {
  apiKey: "APY_KEY",
  authDomain: "AUTH_DOMAIN",
  projectId: "ID",
  storageBucket: "STORAGE_BUCKET",
  messagingSenderId: "SENDER_ID",
  appId: "APP_ID",
};
// Crear el enlace a la aplicación.
const app = initializeApp(firebaseConfig);
// Exportar el objeto aplicación.
export { app };
```

Este es el contenido del fichero **datosFirestore.js** del que se importará desde el fichero principal:

```js
import { app } from "./datosFirebase.js";
```

De este modo se dispone del objeto app que apunta a la aplicación que se ha creado en los servidores de _Firebase_. A través de este objeto es posible, entre otras cosas, la conexión a la base de datos _Firestore_. Para ello será necesario la importación el módulo necesario desde la biblioteca firebase-firestore.js y se realizará desde el CDN de Google (enlaces a las bibliotecas de _Firebase_ [aquí](https://firebase.google.com/docs/web/learn-more?authuser=0#libraries-cdn)):

```js
import { getFirestore } from "https://www.gstatic.com/.../firebase-firestore.js";
```

Esta importación debe realizarse desde el fichero donde se requiera el uso de los datos (hay que recordar que debe especificarse **type=”module”** para que funcione de forma adecuada).Tras esto, ya se dispone de las herramientas para crear un acceso a la base de datos:

```js
const db = getFirestore(app);
```

El objeto **db** será una conexión directa con _Firestore_. Dispone de una serie de métodos para el manejo de los datos que serán analizados a continuación. No es ocioso encarecer que la consulta a la documentación oficial para ampliar la información es de obligado cumplimiento.

## Leer datos

Para la lectura de los datos a través del objeto **db** será necesario la importación de módulos extra:

```js
import {
  getFirestore,
  collection,
  getDocs,
  getDoc,
} from "https:/.../firebase-firestore.js";
```

Para simplificar el funcionamiento de la transmisión asíncrona es preferible la utilización del binomio _async/await_ ahora que ya se sabe cómo funciona. De este modo es posible crear una función que obtenga todos los documentos de una colección:

```js
// Se crea una función asíncrona.
const obtenerFeos = async () => {
	// Se obtiene la colección.
	const feosColeccion = collection(db, "feos");
	// Se obtienen los documentos de esa colección.
	const feosDocumentos = await getDocs(feosColeccion);
	// Se ejecuta una acción sobre ellos.
	feosDocumentos.docs.map((doc) => {
		console.log(doc);
		console.log(`${doc.data().nombre} tiene como aficiones ${doc.data().aficiones}`);
	};
)};
```

Para traer los datos tan sólo será necesario llamar a la función:

```js
obtenerFeos();
```

Esta es una forma tradicional de obtener datos; se consulta a la base de datos cuando se ejecuta la función y se obtienen los datos de ese instante. _Firestore_ ofrece otra forma de consultar datos creando un monitor (_listener_) que se encarga de realizar una actualización de los datos cada vez que se modifican (crean nuevos, se actualizan o eliminan). Esto lo realiza a través de un sistema de caché local y tan sólo se transfieren los datos modificados. Ademas, es posible limitar este monitor para que vigile un sólo documento, un conjunto de ellos o una colección entera.

Para realizar esta acción es necesario añadir otro módulo a la importación de _Firestore_:

```js
import { onSnapshot } from "https://www.gstatic.com/.../firebase-firestore.js";
```

En el siguiente ejemplo se coloca un monitor (listener) sobre toda la colección Feos:

```js
const obtenerFeosSnap = async () => {
	// Obtención de la colección “feos”.
	const feosColeccion = collection(db, "feos");
	// Se crea un monitor a la colección con onSnapshot.
	const feosDocumentos = await onSnapshot(feosColeccion, (col) => {
		// La función (manejador) recibe una colección como parámetro.
		col.docs.map((doc) => {
			console.log(doc);
			console.log(`${doc.data().nombre} tiene como aficiones ${doc.data().aficiones}`);
		}`);
	});
};
```

Para hacer una consulta a las colecciones se debe utilizar el módulo query que antes habrá que importar junto con otros para realizar búsquedas más completas:

```js
import {
  query,
  where,
} from "https://www.gstatic.com/.../firebase-firestore.js";
```

El método query recibirá como parámetros una colección de documentos y una sentencia where. Ésta, a su vez, recibirá una propiedad por la que filtrar, un operador y un valor como parámetros). Con esto se establecerá una consulta que deberá ser convertida en colección obteniendo los documentos de la base de datos que cumplan esos filtros:

```js
const consulta = query(feosColeccion, where("nombre", "==", "Juan"));
```

y se aplica la consulta a la base de datos:

```js
const feosFiltrados = await getDocs(consulta);
```

lo que devolverá una colección de documentos filtrados por la consulta.

El código completo a continuación desde una función asíncrona parametrizada:

```js
const filtrarFeos = async (campo, valor) => {
  // Se crea la colección completa.
  const feosColeccion = collection(db, "feos");
  // Se crea la consulta para el filtrado.
  const consulta = query(feosColeccion, where(campo, "==", valor));
  // Se filtran los documentos de la base de datos.
  const feosFiltrados = await getDocs(consulta);
  // Se hace algo con los documentos que se han obtenido.
  feosFiltrados.docs.map((doc) => {
    console.log(
      `${doc.data().nombre} tiene como aficiones ${doc.data().aficiones}`
    );
  });
};
```

Es posible filtrar en conjuntos de datos como Array con la consulta:

```js
const consulta = query(feosColeccion, where(campo, "array-contains", valor));
```

El listado completo de operadores lógicos que es posible utilizar es el siguiente:

| Operador           | Descripción                                              |
| ------------------ | -------------------------------------------------------- |
| <                  | menor que                                                |
| <=                 | menor o igual que                                        |
| ==                 | igual que                                                |
| >                  | mayor que                                                |
| >=                 | mayor o igual que                                        |
| !=                 | diferente a                                              |
| array-contains     | _true_ si el array contiene el valor especificado        |
| array-contains-any | _true_ si el array contiene algún valor especificado     |
| in                 | _true_ si existe el valor especificado en un conjunto    |
| not-in             | _true_ si no existe el valor especificado en un conjunto |

Para saber acerca de estos operadores consulta [aquí](https://firebase.google.com/docs/firestore/query-data/queries#query_operators).

También es posible realizar consultas compuestas de varios filtros encadenando los métodos where que sean necesarios. Todos ellos se comportarán como con un **AND** lógico:

```js
const consulta = query(
  feosColeccion,
  where(campo, "array-contains", valor),
  where("nombre", "==", "Juan")
);
```

Pero este tipo de combinaciones no pueden hacerse de cualquier forma. Se pueden realizar comparaciones de rango (<, <=, >, >=) o diferente (!=) sólo en un único campo

```js
const con1 = query(
  feosColeccion,
  where("edad", ">=", 18),
  where("edad", "<=", 40)
);
```

y se puede incluir como máximo una cláusula array-contains o array-contains-any en una consulta compuesta. Para ver el listado completo de limitaciones en las consultas pincha [aquí](https://firebase.google.com/docs/firestore/query-data/queries#query_limitations).

Para limitar y ordenar los resultados de una consulta, esté filtrada o no, hay que importar nuevos módulos:

```js
import {
  ordeBy,
  limit,
} from "https://www.gstatic.com/.../firebase-firestore.js";
```

Con ellos es posible crear una consulta de este tipo:

```js
const consulta = query(
  feosColeccion,
  where(campo, "!=", valor),
  orderBy(campo, "desc"),
  limit(10)
);
```

Hay que tener en cuenta que si se incluye una o varias clausulas where el campo de ordenación debe estar presente en ellas, de lo contrario se deberá crear un índice de forma manual para poder realizar este tipo de consultas.

## Escribir datos

Para añadir datos a una colección primero se genera un objeto con la estructura que espera la colección y los datos a introducir, por ejemplo, recorriendo un formulario. Después se decide cuál es la mejor manera de introducir el documento en la colección, si con un identificador nuevo, cambiar la estructura de un objeto existente o crear un documento nuevo vacío para usarlo más tarde (que se hace con doc). Para estas tareas es necesario importar varios métodos:

```js
import {
  addDoc,
  setDoc,
  doc,
} from "https://www.gstatic.com/.../firebase-firestore.js";
```

Una vez construido el objeto a guardar, como por ejemplo:

```js
const nuevoFeo = {
  nombre: "Albert",
  apellidos: "Einstein",
  aficiones: ["pesca", "relatividad", "peines"],
};
```

tan sólo será necesario usar el método addDoc para guardarlo en la base de datos. Este método recibe como parámetros una referencia a una colección y el objeto a almacenar, además, crea de forma automática un identificador:

```js
const guardarFeo = async (feo) => {
  const feoGuardado = await addDoc(feosColeccion, feo);
  console.log(`Feo guardado con el id ${feoGuardado.id}`);
};
```

Sólo resta ejecutar la función donde sea necesario (manejador de evento por ejemplo):

```js
guardarFeo(nuevoFeo);
```

Con setDoc es posible agregar datos a un documento ya existente si, además de los parámetros del addDoc, se añade el identificador del objeto a sobrescribir. Para elegir un documento a través de su identificador es necesario utilizar el módulo doc (que requiere como parámetro una colección de documentos y un identificador válido):

```js
const guardarFeoSet = async (id) => {
  // Se obtiene la referencia del documento.
  const pruebaRef = await doc(feosColeccion, id);
  // Se sobrescriben los datos del documento.
  await setDoc(pruebaRef, {
    nombre: "Juana",
    apellidos: "López",
    // Se añaden datos a la estructura.
    edad: 87,
    aficiones: ["ninguna"],
  });
};
```

Bastará con ejecutar la función pasándole un identificador de documento válido:

```js
guardarFeoSet("LCy8664CnKt93gzwQfkr");
```

Cuando se usa setDoc todos las propiedades del objeto que no sean definidas en la escritura serán eliminadas del objeto. Si , por el contrario, se especifica una propiedad nueva será añadida con su nuevo valor. Hay que tener en cuenta esta particularidad.

## Actualizar datos

Con el módulo setDoc es posible modificar la información de los documentos de una colección pero había que tener cuidado de especificar todos los datos del objeto ya que, de no ser así, se eliminaban los no declarados en la orden de modificación. _Firestore_ dispone de otro método para actualizar la información de un documento de forma parcial sin que se destruyan las propiedades no especificadas: updateDoc. Como es habitual, hay que importarlo antes de su uso:

```js
import { updateDoc } from "https://www.gstatic.com/.../firebase-firestore.js";
```

y su uso debe resultar ya intuitivo a estas alturas del manual:

```js
const actualizarFeo = async (id) => {
  const pruebaRef = await doc(feosColeccion, id);
  await updateDoc(pruebaRef, {
    nombre: "Juan",
    aficiones: ["carreras"],
  });
};
```

Este método tan sólo afectará a las propiedades indicadas en la actualización. Igual que ocurría en setDoc, si se especifica una nueva propiedad al documento, ésta será incluida como nueva propiedad en el documento.

En el ejemplo anterior el Array aficiones se ha reemplazado por uno nuevo. Quizás la idea era ampliar o eliminar uno de los valores que contiene. Para esa tarea es necesario importar dos nuevos módulos:

```js
import {
  arrayUnion,
  arrayRemove,
} from "https://www.gstatic.com/.../firebase-firestore.js";
```

Si se modifica la función anterior es posible hacer que carreras se añada al listado de aficiones del usuario en cuestión:

```js
const actualizarFeo = async (id) => {
  const pruebaRef = await doc(feosColeccion, id);
  await updateDoc(pruebaRef, {
    nombre: "Juan",
    aficiones: arrayUnion("pesca", "peines", "carreras"),
  });
};
```

La ventaja de este método es que si el valor ya está presente en el Array, no será introducido, por lo que no existirán duplicados en el listado.

```js
const eliminarAficion = async (id) => {
  const pruebaRef = await doc(feosColeccion, id);
  await updateDoc(pruebaRef, {
    aficiones: arrayRemove("feos", "guapos"),
  });
};
```

## Borrar datos

Por último, para eliminar documentos de la colección o propiedades de un objeto, será necesario importar nuevos módulos:

```js
import {
  deleteDoc,
  deleteFiled,
} from "https://www.gstatic.com/.../firebase-firestore.js";
```

Su uso resulta muy intuitivo ya que recibe como parámetro la referencia al documento que se necesita eliminar:

```js
const borrarDoc = async (id) => {
  await deleteDoc(doc(feosColeccion, id));
};
```

y sólo sería necesario ejecutar la función:

```js
borrarDoc("iwJMNrwqWUPFoUxJ7hQ3");
```

Es posible eliminar un campo específico del documento, aunque esto debe realizarse durante un proceso de actualización:

```js
const borrarApellido = async (id) => {
  const pruebaRef = await doc(feosColeccion, id);
  await updateDoc(pruebaRef, {
    apellidos: deleteField(),
  });
  console.log(`Apellido de ${pruebaRef.id} borrado.`);
};
```

Hay que tener muy presente que deleteField **elimina la propiedad del objeto**, no sólo su valor.
