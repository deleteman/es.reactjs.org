---
title: Importar y exportar componentes
---

<Intro>

La magia de los componentes reside en su reusabilidad: puedes crear componentes que se componen a su vez de otros componentes. Pero mientras anidas más y más componentes, a menudo tiene sentido comenzar a separarlos en diferentes archivos. Esto te permite que tus archivos se mantengan fáciles de buscar/scan????? y reutilizar componentes en más lugares.

</Intro>

<YouWillLearn>

* Qué es un archivo de componente raíz
* Cómo importar y exportar un componente
* Cuándo usar imports y exports *defaults* o nombrados
* Cómo importar o exportar múltiples componentes de un archivo
* Cómo separar componentes en múltiples archivos

</YouWillLearn>

## El archivo de componente raíz {/*the-root-component-file*/}

En [Tu primer componente](/learn/your-first-component), hiciste un componente `Profile` y un componente `Gallery` que lo renderiza:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Estos viven actualmente en este ejemplo en un **archivo de componente raíz,** llamado `App.js`. En [Create React App](https://create-react-app.dev/), tu aplicación vive en `src/App.js`. No obstante, en dependencia de tu configuración, tu componente raíz podría estar en otro archivo. Si utilizas un framework con enrutamiento basado en archivos, como Next.js, tu componente raíz será diferente para cada página.

## Exportar e importar un componente {/*exporting-and-importing-a-component*/}

¿Y si quisieras cambiar la pantalla de inicio en el futuro y poner allí una lista de libros científicos? ¿O ubicar todos los perfiles en otro lugar? Tiene sentido mover `Gallery` y `Profile` fuera del componente raíz. Esto los haría más modulares y reutilizables en otros archivos. Puedes mover un componente en tres pasos:

1. **Crea** un nuevo archivo JS para poner los componentes dentro.
2. **Exporta** tu componente de función desde ese archivo (ya sea usando exports [por defecto](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/export#usando_el_export_por_defecto) o [con nombre](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/export#syntax)).
3. **Impórtalo** en el archivo en el que usarás el componente (usando la técnica correspondiente de importar exports [por defecto](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/import#importaci%C3%B3n_de_elementos_por_defecto) o [con nombre](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/import#importa_un_solo_miembro_de_un_m%C3%B3dulo.)).

Aquí tanto `Profile` y `Gallery` se han movido fuera de `App.js` en un nuevo archivo llamado `Gallery.js`. Ahora puedes cambiar `App.js` para importar `Gallery` desde `Gallery.js`:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Nota cómo este ejemplo está ahora descompuesto en dos archivos:

1. `Gallery.js`:
     - Define el componente `Profile` que se usa solo dentro del mismo archivo y no se exporta.
     - Define el componente `Gallery` como un *export por defecto*.
2. `App.js`:
     - Importa `Gallery` como un **import por defecto** desde `Gallery.js`.
     - Exporta el componente raíz `App` como un **export por defecto**.


<Note>

Puede que te encuentres archivos que omiten la extensión de archivo `.js` de esta forma:

```js 
import Gallery from './Gallery';
```

Tanto `'./Gallery.js'` como `'./Gallery'` funcionarán con React, aunque la primera forma es más cercana a cómo lo hacen los [módulos nativos de ES](https://developer.mozilla.org/es/docs/Web/JavaScript/Guide/Modules).

</Note>

<DeepDive title="Default vs Named Exports">

There are two primary ways to export values with JavaScript: default exports and named exports. So far, our examples have only used default exports. But you can use one or both of them in the same file. **A file can have no more than one _default_ export, but it can have as many _named_ exports as you like.**

![Default and named exports](/images/docs/illustrations/i_import-export.svg)

How you export your component dictates how you must import it. You will get an error if you try to import a default export the same way you would a named export! This chart can help you keep track:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './button.js';` |

When you write a _default_ import, you can put any name you want after `import`. For example, you could write `import Banana from './button.js'` instead and it would still provide you with the same default export. In contrast, with named imports, the name has to match on both sides. That's why they are called _named_ imports!

**People often use default exports if the file exports only one component, and use named exports if it exports multiple components and values.** Regardless of which coding style you prefer, always give meaningful names to your component functions and the files that contain them. Components without names, like `export default () => {}`, are discouraged because they make debugging harder.

</DeepDive>

## Exporting and importing multiple components from the same file {/*exporting-and-importing-multiple-components-from-the-same-file*/}

What if you want to show just one `Profile` instead of a gallery? You can export the `Profile` component, too. But `Gallery.js` already has a *default* export, and you can't have _two_ default exports. You could create a new file with a default export, or you could add a *named* export for `Profile`. **A file can only have one default export, but it can have numerous named exports!**

> To reduce the potential confusion between default and named exports, some teams choose to only stick to one style (default or named), or avoid mixing them in a single file. It's a matter of preference. Do what works best for you!

First, **export** `Profile` from `Gallery.js` using a named export (no `default` keyword):

```js
export function Profile() {
  // ...
}
```

Then, **import** `Profile` from `Gallery.js` to `App.js` using a named import (with the curly braces):

```js
import { Profile } from './Gallery.js';
```

Finally, **render** `<Profile />` from the `App` component:

```js
export default function App() {
  return <Profile />;
}
```

Now `Gallery.js` contains two exports: a default `Gallery` export, and a named `Profile` export. `App.js` imports both of them. Try editing `<Profile />` to `<Gallery />` and back in this example:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <Profile />
  );
}
```

```js Gallery.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Now you're using a mix of default and named exports:

* `Gallery.js`:
  - Exports the `Profile` component as a **named export called `Profile`**.
  - Exports the `Gallery` component as a **default export**.
* `App.js`:
  - Imports `Profile` as a **named import called `Profile`** from `Gallery.js`.
  - Imports `Gallery` as a **default import** from `Gallery.js`.
  - Exports the root `App` component as a **default export**.

<Recap>

On this page you learned:

* What a root component file is
* How to import and export a component
* When and how to use default and named imports and exports
* How to export multiple components from the same file

</Recap>



<Challenges>

### Split the components further {/*split-the-components-further*/}

Currently, `Gallery.js` exports both `Profile` and `Gallery`, which is a bit confusing.

Move the `Profile` component to its own `Profile.js`, and then change the `App` component to render both `<Profile />` and `<Gallery />` one after another.

You may use either a default or a named export for `Profile`, but make sure that you use the corresponding import syntax in both `App.js` and `Gallery.js`! You can refer to the table from the deep dive above:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './button.js';` |

<Hint>

Don't forget to import your components where they are called. Doesn't `Gallery` use `Profile`, too?

</Hint>

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <div>
      <Profile />
    </div>
  );
}
```

```js Gallery.js active
// Move me to Profile.js!
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

After you get it working with one kind of exports, make it work with the other kind.

<Solution>

This is the solution with named exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import { Profile } from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

This is the solution with default exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import Profile from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

</Solution>

</Challenges>