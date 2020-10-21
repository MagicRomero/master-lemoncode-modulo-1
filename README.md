# Master Front End IX - Laboratorio - Módulo 1 - Layout

### Antes de empezar

Se ha importado `normalize.css` para renderizar los elementos html de una manera mas estándar y consistente entre navegadores.
Los archivos `.scss` ya han sido transpilados a `css` en cada ejercicio, si necesitas volver a recompilarlos, debes tener instalado `node + npm` en tu máquina y **ejecutar el siguiente comando** en la raíz del proyecto:

```sh
npm install
```

Esto instalara la dependencia de desarrollo `node-sass`, el proyecto ya contiene los scripts para los ejercicios 1 y 2 que es donde únicamente se utiliza sass:

```json
{
  "scripts": {
    "compile-first-exercise": "node-sass --include-path sass ./1-Paleta-De-Colores/index.scss ./1-Paleta-De-Colores/index.css ",
    "compile-second-exercise": "node-sass --include-path sass ./2-Temas-Distintos/index.scss ./2-Temas-Distintos/index.css ",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

```sh
npm run compile-first-exercise
npm run compile-second-exercise
```

# Tabla de contenidos

A continuación se enumeran los ejercicios propuestos a modo de índice para la práctica de **_HTML, CSS y SASS (.scss)_**

1.  [Paleta de colores](#paleta-de-colores)
2.  [Aplicar temas en web](#aplicar-temas-en-web)
3.  [Barra de navegación responsive](#barra-de-navegacion-responsive)
4.  [Card layout con CSS grid](#card-layout-con-css-grid)

---

## Paleta de colores

El objetivo es usar el **preprocesador SASS** para crear paletas de colores dinámicas y utilizarlos en nuestro html con una simple clase.

El html final es sencillo e intuitivo, ya que las propias clases nos indican que color base se esta utilizando en la paleta:

```html
<main>
  <div class="palette-container">
    <div class="color-box blue-extra-dark">extra dark</div>
    <div class="color-box blue-darker">darker</div>
    <div class="color-box blue-dark">dark</div>
    <div class="color-box blue-base">base</div>
    <div class="color-box blue-light">light</div>
    <div class="color-box blue-lighter">lighter</div>
    <div class="color-box blue-extra-light">extra-light</div>
  </div>
  <div class="palette-container">
    <div class="color-box red-extra-dark">extra-dark</div>
    <div class="color-box red-darker">darker</div>
    <div class="color-box red-dark">dark</div>
    <div class="color-box red-base">base</div>
    <div class="color-box red-light">light</div>
    <div class="color-box red-lighter">lighter</div>
    <div class="color-box red-extra-light">extra-light</div>
  </div>
  <div class="palette-container">
    <div class="color-box green-extra-dark">extra-dark</div>
    <div class="color-box green-darker">darker</div>
    <div class="color-box green-dark">dark</div>
    <div class="color-box green-base">base</div>
    <div class="color-box green-light">light</div>
    <div class="color-box green-lighter">lighter</div>
    <div class="color-box green-extra-light">extra-light</div>
  </div>
</main>
```

**En el lado de SASS**, se ha creado una variable global donde guardamos las paletas de colores usando el propio color como llave **_(lo ideal seria generarlas dinámicas pasandole un color base)_**

```scss
$palettes: (
  blue: (
    extra-dark: hsl(201, 75%, 22%),
    darker: hsl(201, 61%, 34%),
    dark: hsl(198, 57%, 39%),
    base: hsl(196, 59%, 42%),
    light: hsl(194, 56%, 51%),
    lighter: hsl(192, 61%, 53%),
    extra-light: hsl(192, 100%, 74%),
  ),
  ...
 }
```

Es a través de la funcion `@palette-color` y el mixin `@create-palete-classes` donde sucede la magia, la primera función se encarga de obtener el color segun el tono usando nuestra **_variable global_** `$palettes`:

```scss
@function palette-color($color, $tone) {
  @warn map-get($palettes,$color);

  @if map-has-key($palettes, $color) {
    $color: map-get($palettes, $color);

    @if map-has-key($color, $tone) {
      $tone: map-get($color, $tone);
      @return $tone;
    }

    @warn "unknown tone `#{$tone}` in color";
    @return null;
  }

  @warn "unknown color `#{$color}` in palette";
  @return null;
}
```

Esta función la utilizamos dentro de nuestro mixin el cual su único objetivo es crear las clases pasandole un color y usandolo como prefijo de la clase final:

```scss
$tones: "extra-dark" "darker" "dark" "base" "light" "lighter" "extra-light";

@mixin create-palette-classes($color) {
  @each $tone in $tones {
    .#{$color}-#{$tone} {
      background: palette-color($color, $tone);
    }
  }
}
// Resultado usando el mixin con el color 'blue'
@include create-palette-classes(blue);
--------------------------------------- .blue-extra-dark {
  background: #0e4562;
}
.blue-darker {
  background: #22678c;
}
.blue-dark {
  background: #2b7a9c;
}
.blue-base {
  background: #2c89aa;
}
.blue-light {
  background: #3ca7c8;
}
.blue-lighter {
  background: #3eb3d0;
}
.blue-extra-light {
  background: #7ae4ff;
}
```

## Aplicar temas en web

El objetivo de este ejercicio es cambiar propiedades comunes de los elementos segun el tema de color que tenga seteado en ese momento la aplicación, el ejemplo mas utilizado es el de cambiar el diseño de una web a través de un switch que represente un tema claro u oscuro, cambiando colores de fuente, bordes, color de fondo, etc.

Se ha aplicado una lógica similar al ejercicio anterior, disponemos de una variable global `$themes`, donde guardaremos la configuración de cada uno a través de una key que representara el theme en si.

```scss
$themes: (
  light: (
    primaryFont: Lucida Sans,
    secondaryFont: Georgia,
    backgroundColor: #fff,
    textColor: #408bbd,
    buttonTextColor: #408bbd,
    buttonTextTransform: none,
    buttonTextHoverColor: #61b0e7,
    buttonColor: #fff,
    defaultBorder: 2px solid #fff,
    defaultRadius: 0,
    boxShadow: 0 4px 8px 0 rgba(0, 0, 0, 0.911),
    boxShadowHover: 0 8px 16px 0 rgba(0, 0, 0, 0.911),
  ),
  dark: (
    primaryFont: Segoe UI,
    secondaryFont: Verdana,
    backgroundColor: #222,
    textColor: #ddd,
    buttonTextColor: #aaa,
    buttonTextTransform: uppercase,
    buttonTextHoverColor: #ddd,
    buttonColor: #333,
    defaultBorder: 1px solid #aaa,
    defaultRadius: 10px,
    boxShadow: 0 4px 8px 0 rgba(233, 232, 232, 0.911),
    boxShadowHover: 0 8px 16px 0 rgba(233, 232, 232, 0.911),
  ),
);
```

Con un simple mixin que llamaremos `@themify` y una función `@themed` podemos generar las clases necesarias que nos permitirán cambiar el estilo css de nuestros elementos html de manera muy fácil:

```scss
@mixin themify($themes: $themes) {
  @each $theme, $map in $themes {
    .theme-#{$theme} & {
      $theme-map: () !global;
      @each $key, $submap in $map {
        $value: map-get(map-get($themes, $theme), "#{$key}");
        $theme-map: map-merge(
          $theme-map,
          (
            $key: $value,
          )
        ) !global;
      }

      @content;
      $theme-map: null !global;
    }
  }
}

@function themed($key) {
  @return map-get($theme-map, $key);
}
```

El mixin nos permite generar nuestros temas dinámicamente y crear las clases necesarias segun los que hayamos configurado que en nuestro caso de ejemplo es **'light' y 'dark'**, en el siguiente ejemplo se puede entender mejor:

```scss
.app {
  display: flex;
  flex-direction: column;
  flex: 1;
  align-items: center;
  justify-content: center;

  @include themify($themes) {
    color: themed("textColor");
    background-color: themed("backgroundColor");
    font-family: themed("primaryFont");
  }
}

// El CSS que genera sería:
.app {
  display: flex;
  flex-direction: column;
  flex: 1;
  align-items: center;
  justify-content: center;
}
.theme-light .app {
  color: #408bbd;
  background-color: #fff;
  font-family: Lucida Sans;
}
.theme-dark .app {
  color: #ddd;
  background-color: #222;
  font-family: Segoe UI;
}
```

En el html simplemente debemos embeber nuestra aplicación en un contenedor general donde cambiaremos la clase que define el tema actual y donde queremos aplicar nuestros estilos:

```html
<main id="root">
  <div class="theme-dark">
    <!-- Si quisieramos otro tema sería: class="theme-light" -->
    <div class="app">...</div>
  </div>
</main>
```

## Barra de navegacion responsive

Este ejercicio al ser simple no he necesitado aplicar **SASS** ya que únicamente necesitaba unas cuantas propiedades **CSS** sin necesidad de dinamismos.
He utilizado `flexbox` junto a `media queries` para conseguir el resultado final:

```css
main {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  font-size: 16px;
}
ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
.navbar {
  width: 100%;
  display: block;
  font-size: inherit;
  color: white;
  text-transform: capitalize;
  background-color: rgb(23, 114, 119);
}
.navbar ul {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.navbar ul li {
  height: 100%;
  padding: 1.2em 1em;
}
.navbar ul li.active {
  background-color: rgb(12, 68, 72);
}
@media only screen and (max-width: 450px) {
  .navbar ul {
    flex-direction: column;
    align-content: center;
    align-items: initial;
  }
  .navbar ul li {
    flex: 1;
    text-align: center;
  }
}
```

## Card layout con CSS grid

En este ejercicio tampoco he utilizado **SASS** ya que no lo requería para llegar al resultado final, como antes, solo necesitaba unas cuantas propiedades **CSS** para conseguir el resultado final.

Las clases importantes donde he utilizado grid han sido el contenedor principal que representa el grid, el contenedor que contiene las cards y la card como tal, utilizando `media queries` en resoluciones menores para disminuir el número de elementos que hay en una fila:

```css
.card-grid {
  display: grid;
  grid-template-columns: 1fr repeat(12, minmax(auto, 350px)) 1fr;
  grid-gap: 30px;
}

.card-grid .cards {
  grid-column: 2 / span 12;
  display: grid;
  grid-template-columns: repeat(12, minmax(auto, 350px));
  grid-gap: 30px;
}

.card-grid .cards .card {
  background: #fafafa;
  border: 1px solid rgb(170, 167, 167);
  border-radius: 0.25rem;
  grid-column-end: span 4;
  display: flex;
  flex-direction: column;
  cursor: pointer;
  transition: all 0.3s ease 0s;
}

@media only screen and (max-width: 1000px) {
  .card {
    grid-column-end: span 6;
  }
}

@media only screen and (max-width: 700px) {
  main {
    grid-gap: 20px;
  }
  .card {
    grid-column-end: span 12;
  }
}

@media only screen and (max-width: 500px) {
  main {
    grid-template-columns: 10px repeat(6, 1fr) 10px;
    grid-gap: 10px;
  }
```
