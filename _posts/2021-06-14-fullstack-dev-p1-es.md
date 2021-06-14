---
layout: post
title:  "Desarrollo de aplicaciones Full Stack con Next.js y Spring Boot I"
summary: Desarrolla aplicaciones Full Stack rápidamente utilizando Next.js y Spring Boot
author: alberto
date: '2021-06-14 07:00:00'
categories: fullstack next.js spring-boot
thumbnail: /assets/img/posts/code.jpg
keywords: fullstack, next.js, spring boot, fullstack development, fullstack tutorial, next.js tutorial, spring boot tutorial
permalink: /blog/fullstack-dev-p1-es/
---

## Introducción

En esta serie de tutoriales demostraremos el proceso de desarrollo de una aplicación Full Stack. Desarrollaremos la interfaz de usuario con Next.js y consumiremos los datos necesarios a través de una API REST hecha con Spring Boot.

Para esta demostración simularemos el feed de una red social.

Lo primero que haremos será crear el proyecto de Frontend. Para eso abre una terminal y ejecuta el siguiente comando:

`yarn create next-app`

Si aun no tienes yarn puedes instalarlo con el siguiente comando:

`npm install -g yarn`

Tendremos que introducir el nombre del proyecto. En este ejemplo usaré Nissus, tú puedes usar el que quieras.

Una vez que el proyecto se haya creado, abriremos nuestro editor de texto, importaremos el código e iniciaremos el servidor:

`cd nissus`

`yarn dev`

La aplicación ya está disponible en la siguiente ruta.

[http://localhost:3000/](http://localhost:3000/)

Por default Next.js utiliza el puerto 3000. Si no tienes disponible este puerto puedes cambiarlo modificando el archivo `package.json`. Dentro del nodo `scripts` ubica la siguiente línea:

`"dev": "next dev"`

Y modifícala con el puerto que quieres. En este ejemplo usamos el 3001:

`"dev": "next dev -p 3001"`

> **Realiza este cambio solo si el puerto default no está disponible.**

Next.js es un framework de React que permite crear una aplicación lista para producción sin necesidad de agregar configuraciones extra. Esto permite enfocarnos únicamente en el código de nuestra app.

Al crear una nueva aplicación, Next.js genera por default el archivo `./pages/index.js`. Este archivo es el que muestra la pantalla de bienvenida.

Al abrir este archivo podemos ver el código generado por default. Ubica la siguiente linea:

`Welcome to <a href="https://nextjs.org">Next.js!</a>`

Borrála y pon el siguiente texto:

`This is my Next app!`

La página se recargará automáticamente al detectar los cambios y podremos verlos en pantalla.

![Next.js app](/assets/img/posts/next-index.png){:height="50%" width="50%"}

Dale un vistazo al archivo `./pages/index.js`, si conoces React esto te parecerá familiar. Esto es JSX.

JSX es una extension sintáctica de Javascript utilizada para desarrollar componentes en React. Con JSX puedes hacer cosas como:

`const hello = <h1>Hello!</h1>`

Esto no es una cadena de caracteres ni un HTML, JSX permite combinar lógica de renderización junto con otros elementos de la interfaz de usuario.

En este ejemplo asignamos a la variable `hello` el valor de un elemento HTML. Este elemento puede ser combinado y rehusado por otros componentes:

```js
<div>
  {hello}
</div>
```

Renombra el archivo `./pages/index.js` a `./pages/index.jsx`, esto ayudara a diferenciar los archivos Javascript estándares de nuestros componentes. React no requiere JSX, pero lo usaremos en este proyecto.

## Ruteo

Next.js tiene un sistema de ruteo basado en directorios. Esto quiere decir que las rutas de nuestra aplicación se habilitarán de acuerdo a las carpetas y archivos que creemos. 

El directorio habilitado para está función es `./pages`. Este directorio se genera por default al crear una aplicación de Next.js.

Para ver cómo esto funciona, crea el archivo hello.jsx en el directorio `./pages` con el siguiente código:

`./pages/hello.jsx`

```jsx
import React, { Component } from 'react'

export default class Hello extends Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
      </div>
    )
  }
}
```

Al crear el archivo en `./pages`, y nombrarlo `hello.jsx`, Next.js habilita la ruta `/hello` sirviendo el contenido de este archivo.

[http://localhost:3000/hello](http://localhost:3000/hello)

![Hello JSX](/assets/img/posts/hello-jsx.png){:height="50%" width="50%"}

Ahora crea el subdirectorio `./pages/hello`, mueve el archivo `hello.jsx` a esta carpeta, renombralo a `index.jsx` y recarga la página.

`./pages/hello/index.jsx` (previously `./pages/hello.jsx`)

```jsx
import React, { Component } from 'react'

export default class Hello extends Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
      </div>
    )
  }
}
```

Next.js mapea los archivos `index.js*` como `/`, por lo que al cambiar `./pages/hello.jsx` por `./pages/hello/index.jsx`, la ruta habilitada sigue siendo la misma.

En caso de requerir parametros, podemos definirlos utilizando el nombre del archivo.

Crea el archivo `[value].jsx` dentro de `./pages/hello/` con el siguiente contenido:

`./pages/hello/[value].jsx`

```jsx
import { useRouter } from 'next/router'

export default function Home() {
  const router = useRouter();
  const { value } = router.query;

  return (
    <div>
      <h1>{"Hello, " + value + "!"}</h1>
    </div>
  );
}
```

La ruta /hello ahora acepta un valor y lo muestra en pantalla. Haz una prueba, en este caso el valor sera la palabra friend.

[http://localhost:3000/hello/friend](http://localhost:3000/hello/friend)

![Hello, friend](/assets/img/posts/hello-friend.png){:height="75%" width="75%"}

Compara el código de `hello.jsx` y `[value].jsx`.

La principal diferencia es la declaración de la clase. `export default class` indica que `hello.jsx` es un componente de clase, mientras que `export default function` declara a `[value].jsx` como un componente funcional. Esto es importante, ya que la funcion useRouter() es un hook. Los hooks nos permiten acceder a funcionalidades de React desde componentes funcionales, sin declarar componentes de clase.

La aplicación que estamos desarrollando está conformada por un Frontend, hecho con Next.js, y un API REST, desarrollada con Spring Boot. Pospondremos la parte de Spring Boot por el momento y en su lugar utilizaremos las API Routes de Next.js.

## Next.js API Routes

Al crear un proyecto de Next.js, otra de las carpetas generadas por default es `./pages/api`.

Este subdirectorio funciona de manera similar a `./pages`, la única diferencia es que solo soporta respuestas en JSON (si intentas crear un componente como los que creamos arriba, Next.js retornará un Internal Server Error), los archivos que crees acá serán mapeados a `http://localhost:3000/api`.

Abre el archivo `./pages/api/hello.js`:

`./pages/api/hello.js`

```js
// Next.js API route support: https://nextjs.org/docs/api-routes/introduction

export default (req, res) => {
  res.status(200).json({ name: 'Hello, world!' })
}
```

Y navega hacía:
[http://localhost:3000/api/hello](http://localhost:3000/api/hello)

`./pages/api/hello.js` contiene una función que recibe dos parámetros, `req` y `res`. `req` encapsula los datos contenidos en la petición que hacemos al servicio, como los parametros, headers, etc.  `res` encapsula los datos de la repuesta del servicio. La respuesta de este servicio es un estatus `HTTP 200 OK` y un JSON con un parametro `name`.

![Hello REST](/assets/img/posts/hello-rest.png){:height="75%" width="75%"}

Nosotros usaremos esta funcionalidad para simular los servicios que desarrollaremos más adelante con Spring Boot. Ya que es una simulación usaremos datos de prueba.

El servicio que desarrollaremos retornará una lista de posts, cada uno de estos está formado por tres parámetros:

`Post`

```json
{
  id: 1,
  author: "Alberto",
  text: "Hello everybody!"
}
```

`id` representa el identificador único del post a nivel base de datos, `author` es el nombre de la persona que escribió el post, y `text` es el contenido del post. Más adelante explicaremos con mas detalle el modelado de la aplicación.

Vamos a crear los datos de prueba. Crea la carpeta `./pages/api/mocks` y dentro de ella el archivo `data.js`. Este archivo exporta una lista de Posts de ejemplo:

`./pages/api/mocks/data.js`

```js
export const POSTS = [
  {
    id: 1,
    author: "Albert H.",
    text: "Look at this! 👁"
  },
  {
    id: 2,
    author: "M. Sabina",
    text: "I really like it!"
  },
  {
    id: 3,
    author: "Terence M.",
    text: "Take it easy"
  },
  {
    id: 4,
    author: "Alberto A.",
    text: "Coming soon 🛫"
  }
];
```

Estos serán los datos que consumiremos a través de nuestra API, para exponerlos crea el archivo `./pages/api/posts.js` con el siguiente código:

`./pages/api/posts.js`

```js
import {POSTS} from "./mocks/data"

export default (req, res) => {
  res.status(200).json(POSTS)
}
```

Ahora revisa la respuesta de la API:

[http://localhost:3000/api/posts](http://localhost:3000/api/posts)

![Mock posts](/assets/img/posts/mock-posts.png){:height="75%" width="75%"}

Usaremos la librería `axios` para comunicarnos con la API:

`yarn add axios`

Crea el directorio `./lib` y el archivo `./lib/api.jsx` con el siguiente contenido:

`./lib/api.jsx`

```js
import axios from "axios";

export default axios.create({
  baseURL: "http://localhost:3000/api/",
  responseType: "json"
});
```

Este componente exporta una instancia de axios que permite comunicarnos con nuestra API. Solo se define la URL y el tipo de respuesta.

## Consumiendo el API

Una vez definidos los datos que estaremos consumiendo, estaremos listos para mostrarlos en pantalla.

Para eso es necesario agregar la librería `react-bootstrap`. Bootstrap nos brinda varios componentes por default para utilizar en nuestra aplicación:

`yarn add react-bootstrap bootstrap`

Borra el contenido del archivo `./pages/index.jsx` y reemplazalo por lo siguiente:

`./pages/index.jsx`

```jsx
import Head from 'next/head'
import styles from '../styles/Home.module.css'

import Card from 'react-bootstrap/Card';

import api from "../lib/api"

export async function getStaticProps(context) {
  const posts = await api.get("/posts");

  return {
    props: {
      posts: posts.data
    },
  }
}

export default function Home( {posts} ) {
  const hello = <h1>Hello!</h1>
  const style = {
    width: '30rem',
    marginTop: '20px'
  }

  const postsView = posts.map(post => (
    <Card key={post.id} style={style}>
      <Card.Body>
        <Card.Title>{post.author}</Card.Title>
        <Card.Text>{post.text}</Card.Text>
      </Card.Body>
    </Card>
  ));

  return (
    <div className={styles.container}>
      <Head>
        <title>Nissus</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main className="container">
      <h1>NISSUS</h1>
      {postsView}
      </main>

      <footer className={styles.footer}>
        Footer
      </footer>
    </div>
  )
}
```

Primero importamos el Header y los estilos, después los componentes que usaremos de Bootstrap y por último, la utilidad que creamos para comunicarnos con nuestra API.

Observa la función `getStaticProps(context)`:

```jsx
export async function getStaticProps(context) {
  const posts = await api.get("/posts");

  return {
    props: {
      posts: posts.data
    },
  }
}
```

Una de las características de Next es su pre renderización. Next soporta dos formas de pre renderización, Generación estática y Renderización del lado del servidor.

* Generación estática genera el HTML al compilar nuestra aplicación. Este HTML pre renderizado es rehusado en cada petición al servidor. Si necesitamos datos para la pre renderización de la pagina, el HTML se genera hasta después de conseguirlos.

* Renderización del lado del servidor genera el HTML en cada petición. Los datos se obtienen en cada una de estas peticiones.

Podemos usar ambos tipos en nuestra aplicación dependiendo de nuestras necesidades. Usaremos la Generación estática para este componente, para eso definimos `getStaticProps`.

En nuestra implementación primero nos comunicamos con nuestra API y guardamos el resultado en una constante, después retornamos la propiedad `props` con un parámetro llamado `posts` y los datos que recuperamos de la API. Este parámetro es requerido por el componente `Home`:

```jsx
export default function Home( {posts} )
```

Dentro de `Home` mapeamos cada uno de los posts que recibimos a un componente `Card`:

```jsx
const postsView = posts.map(post => (
  <Card key={post.id} style={style}>
    <Card.Body>
      <Card.Title>{post.author}</Card.Title>
      <Card.Text>{post.text}</Card.Text>
    </Card.Body>
  </Card>
));
```

El parámetro `key` es importante debido a que renderizaremos varios posts. Definimos el cuerpo de la tarjeta y renderizamos el nombre del autor en el título, el contenido de la tarjeta mostrará el texto del post.

Guardamos esto en la constante `postsView` y para utilizarlo al retornar la vista de la función. Esta vista contiene únicamente el Header, el contenido de la API y el Footer.

Ahora podemos ver nuestra aplicación corriendo:

[http://localhost:3000/](http://localhost:3000/)

![Posts](/assets/img/posts/posts.png){:height="75%" width="75%"}

En este post vimos que fácil y rápido es crear una aplicación Full Stack utilizando Next.js. En el siguiente post veremos como construir un API utilizando Spring Boot.

## Recursos

Por favor, lee el material listado abajo para aprender mas acerca de las herramientas que utilizamos arriba.

[https://reactjs.org/docs/hooks-intro.html](https://reactjs.org/docs/hooks-intro.html)

[https://nextjs.org/docs/api-routes/introduction](https://nextjs.org/docs/api-routes/introduction)

[https://react-bootstrap.github.io/](https://react-bootstrap.github.io/)

[https://react-bootstrap.github.io/components/cards/](https://react-bootstrap.github.io/components/cards/)
