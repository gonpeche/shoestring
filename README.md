# Shoestring

## Introducción

### Overview & Objetivos

Muchas compañías utilizan un framework CSS para desarrollo rápido y conveniencia. Frameworks populares son diseñados cuidadosamente, compatible a través de muchos browsers, y generoso en características. Sin embargo, hay un contingente de desarrolladores que creen que frameworks como Bootstrap son muy agresivos u opinionados en lo que proveen, y que es mejor construir tu propio framework o escribir estilos propios para cada proyecto. 

En este workshop, vamos a tratar de recrear la vista de un [Template de Bootstrap](http://blackrockdigital.github.io/startbootstrap-modern-business/) sin realmente usar Bootstrap. Para lograr esto, vamos a crear nuestro propio CSS framework - un subconjunto de Bootstrap el cual vamos a llamar 'Shoestring'. Shoestring va a tener tres componentes claves:

- Tipografía
- Grids
- Forms
- También estas alentado a implementar otro componente importante de Bootstrap, el navbar.

A lo largo del camino vamos a aprender sobre construir CSS moderno y semántico usando herramientas como Sass (una extensión del lenguaje CSS de alta-calidad).

## Setup

## Obtén el Repo

Para empezar, forkeá y cloná el repo de Shoestring. Este repo es un esqueleto de una app de Node; en adición de servir como un servidor básico de archivos estáticos, usa Sass (a través de `node-sass`) para compilar archivos SCSS a CSS. 


## Comenzá la App

Abrí la carpeta del proyecto de Shoestring, `npm install` y corre el app:

```sh
$ cd shoestring.js
$ npm install
$ npm start
```

si visitás [http://localhost:3000](http://localhost:3000) deberías ver una versión que parece sin terminar del sitio. Esto es porque nuestra app esta solo parcialmente estilada; necesitamos que termines de darle estilo por nosotros.


### Compilar SASS

Vas a estar escribiendo todo de tu CSS dentro de `assets/stylesheets/shoestring.css`. Un archivo `scss` es específicamente para código de estilo  SASS -- la cual no corre realmente en tu browser! Lo que necesitamos hacer es usar un compilador SASS en orden de transformar este código `scss` a CSS real!

Si mirás en tu `package.json`, vas a ver una propiedad del script `build-css`. Abrí otra terminal (SEPARADA DE donde corrés `npm start`) y corré el comando `npm run build-css`. Este proceso va a ahora a mirar tu archivo `shoestring.css` por cambios y compilar a `public/stylesheets/shoestring.css` el cual esta conectado a nuestro HTML (`layout.html`).  

---

#### Dev vs. Build vs. Serve

Esto resalta una distinción entre el comportamiento en producción y en desarrollo. En nuestra app en producción, queremos solo servir archivos CSS compilados y minificados que ya existen. No esperamos, de hecho, que esos archivos cambien para nada.

Si nosotros como programadores decidimos hacer alteraciones al diseño, eso es realmente un paso del desarrollo. Una vez que editamos nuestros archivos `.scss`, nuestra configuración ideal detecta automáticamente eso y construye nuestro archivo CSS fina por nosotros. Algún tipo de watcher puede luego reiniciar nuestro servidor par aque todos los nuevos requests muestren el diseño actual.   
En general, un proceso *build* es un paso del desarrollo en el cual los archivos siendo trabajados son  construidos / combinados / editados / auditados / cualquier paso hacía producir una app final en producción. Existen muchas herramientas para lograr builds automatizados - Grunt, Gulp, Webpack etc. En este workshop, tomamos a pasos de bebe y solo usamos nuestro gran amigo NPM para hacer el truco con este script `npm run build-css`.

## Sass y Grids

### Intro a Sass

CSS por si mismo no es un lenguaje de programación completo. No soporta conceptos dinámicos como variables, funciones, loops, operadores, etc. Esto hace que crear CSS reusable un verdadero desafío.

Sass (Syntactically Awesome StyleSheets) da a CSS esas capacidades adicionales. Vamos a escribir nuestro Sass usando una sintaxis llamada SCSS, un superset de CSS que es pre-procesado - El script pre-procesador de Sass toma un archivo `.scss` y lo convierte en un archivo `.css` ordinario.     

Sass es un simple add-on pero hace a CSS mucho más poderoso. Repasa las bases de la guía [aquí](http://sass-lang.com/guide)

---

#### Recursos Útiles

Toma un rápido vistazo de estas guías, solo para familiarizarte con donde está la información en cada uno. Podes llegar a necesitar referirte a alguna de la documentación a través de este workshop.

- [Bootstrap CSS](https://getbootstrap.com/docs/3.3/css/)
- [The Bootstrap Modern Theme](https://startbootstrap.com/template-overviews/modern-business/)
- [Normalize.css](http://necolas.github.io/normalize.css/)
- [SCSS Guide](http://sass-lang.com/guide)


### Usando Sass en Nuestra App

Recorre la aplicación y entendé un poco como funciona.

Nuestro servidor de Express usa static routing para enviar páginas HTML comunes (no hay templates hoy!), y también están ya parcialmente estiladas - hay estilos globales definidos en `assets/stylesheets/modern-bussiness.scss`, los estilos del navbar están localizados en `navbar.scss`, y estamos usando `normalize.scss` para dar nuestros estilos compatibilidad cross-browser. Finalmente, todas estas hojas de estilo están siendo importadas en shoestring.scss.

Sin embargo, nuestra app carece de muchos otros estilos, incluyendo disposición o estilo responsivo. No vamos a escribir un archivo `.css` directamente; en cambio, vamos a escribir archivos `.scss` (Sass). Nuestro proceso de build (npm run build-css) va a automáticamente compilar el Sass a vanilla CSS y colocarlo en la carpeta estática `/public/stylesheets` para que este disponible a los clientes.

En consecuencia deberías hacer la mayor parte de tu trabajo `/assets/stylesheets/shoestring.scss`. También podes usar `@import` para separar tus definiciones de archivos en módulos distintos (archivos). Al final, `shoestring.scss` puede comenzar con una serie de `@imports` como:

```css
@import "normalize";
@import "variables";
@import "grid";
@import "form";
```

Cualquier variable que definas en `variables.scss` va a estar disponible en hojas de estilo que lo importen.

**ADVERTENCIA**: No edites ninguno de los archivos CSS que aparecen en `/public/stylesheets`. Dado que estos archivos están siendo generados por el compilador de Sass, son sobrescritos cada vez que el servidor reinicia! Deberías hacer todo tu trabajo en los archivos Sass.


### Grid Systems

Esperamos que para ahora ya hayas visto que un grid system (como el de Bootstrap) hace al diseño de layouts fácil. Aprender como implementar un sistema así por nosotros mismos nos va a ayudar solidificar nuestro conocimiento de CSS. 

Las primeras tres clases que vas a notar en el archivo `home.html` son `container`, `row` y `col-lg-12`. 


| `**container**`                         | `**row**`                                                                   | `**col-lg-12**`                                                                  |
|-----------------------------------------|-----------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| Centrado en la página; borra los floats | envuelve una serie de columnas; la altura se expande a la columna mas larga | Una columna (col) para una pantalla grande (lg) que cubre un ancho de 12 grillas |

Las columnas tienen un par de propiedades:

- Un `padding` definido por el tamaño de el gutter(el espacio entre las columnas) en tu grid layout.
- Un `width` definido por el porcentaje de unidades del grid que toman. En el caso de una columna de 12 unidades de ancho, es facil: 100%. De otra forma, es el número provisto por el nombre de la clase dividido por el numero de unidades del grid, escrito en porcentaje. Debajo hay un estilo básico que completa estos requerimientos. Agregá lo siguiente en tu archivo Sass. No vamos a ver ninguna mejora visual aún, pero empezamos a construir un solido grid framework.

```js
.container {
  margin-left: auto;
  margin-right: auto;
  clear: both;
}

.row {
  clear: both;
}

.col-lg-12 { 
  width: 100%;
}
```


+++SCSS y columnas 
Usar un loop @for nos puede ayudar hacer todas las clases de las columnas (1-12) dinámicamente sin tener que escribir cada una individualmente.  
+++


### Construilo!

Tu trabajo es rellenar el SCSS/CSS requerido para hacer tus páginas HTML principal y contacto se vean como estos dos screenshots. Haciendo eso va a completar este workshop.   

##### NO DEBERÍAS CAMBIAR LOS DOCUMENTOS HTML A MENOS QUE LO DIGA EN EL WORKSHOP

También puedas encontrar [éste articulo](https://css-tricks.com/centering-css-complete-guide/) útil cuando tratás de centrar algo/todo.

Recomendamos primero construir tu propio grid system como fue descripto en el paso anterior. Este grid system debería permitir para un layout correcto en las clases `container`, `row`, `lg` encontradas en `main.html` y `contact.html`. Las clases `md` y `sm` van a ser usadas más adelante. 

Trabaja una `<section>` a la vez y tratá lo mejor que puedas de hacer que el sitio se vea similar a la imagen debajo. Usa padding y margin para hacer pequeños ajustes.

+++Workflow
¿Teniendo problemas con donde empezar?

##### CÓMO PENSAR A TRAVÉS DE ESTE WORKSHOP

- Toma nota de las clases de un elemento
- Basado en como debería terminar visualmente escribí, en español, lo que querés hacer
- Determiná que estilo mejor quedaría en qué clase
- Notá si otros elementos tienen la misma clase y si todos estos estilos tienen sentido para todos esos elementos
- Crea el estilo que refleje tu descripción en ingles


##### AUNQUE QUE TIPO DE WORKFLOW DE ELEMENTOS

- Hace el grid básico
- Continuá con el resto de la primera página (ignorando el carrusel)
- Continuá a la segunda página con el mapa, form, breadcrumbs, etc.
- Hace pequeñas ediciones para acercarte, pero no te estreses mucho sobre la perfección
- Crea un diseño responsivo (siguiendo las acciones del workshop)
- Incorporá código SCSS específico (e.g. loops, mixins, etc)
- Hace el carrusel funcional, perfeccioná el sitio o agrega tu propia onda! 
+++

---

#### Home Page

![homepage](homepage.png)


#### Página de Contacto

![contact](contact.png)
