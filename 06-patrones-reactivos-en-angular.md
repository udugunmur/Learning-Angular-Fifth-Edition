# Parte 2: Reactividad y Gestión de Estado

## Capítulo 6: Patrones reactivos en Angular

El manejo de información asíncrona es una tarea común en nuestro día a día como desarrolladores. La **programación reactiva** es un paradigma que nos ayuda a consumir, procesar y transformar información asíncrona utilizando flujos de datos (*data streams*). **RxJS** es una librería de JavaScript que proporciona métodos para manipular flujos de datos mediante **observables**.

Angular proporciona un conjunto de herramientas incomparable para ayudarnos al trabajar con datos asíncronos. Los flujos de observables están al frente de este conjunto de herramientas, brindando a los desarrolladores un amplio abanico de capacidades al crear aplicaciones en Angular. El núcleo del framework Angular depende ligeramente de RxJS. Otros paquetes de Angular, como el enrutador (*router*) y el cliente HTTP, están más estrechamente acoplados con los observables. Sin embargo, al momento de escribir este texto, el equipo de Angular está investigando cómo hacer que dichos paquetes dependan menos de los observables.

En este capítulo, aprenderemos sobre los siguientes conceptos:

- Estrategias para manejar información asíncrona
- Programación reactiva en Angular
- La librería RxJS
- Suscripción a observables
- Cancelación de suscripción de observables

---

### Sección 6.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través de los observables y RxJS. Puedes encontrar el código fuente correspondiente en la carpeta `ch06` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch06](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch06)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 6.2: Estrategias para manejar información asíncrona

Gestionamos datos de forma asíncrona en diferentes formas, como consumir datos de una API backend (una operación típica en nuestro flujo de trabajo diario de desarrollo) o leer contenidos del sistema de archivos local. Siempre consumimos datos a través de HTTP, como cuando autenticamos a los usuarios enviando credenciales a un servicio de autenticación. También usamos HTTP al obtener las últimas publicaciones en nuestra aplicación de red social favorita.

Los dispositivos móviles modernos han introducido una forma única de consumir servicios remotos: difieren las solicitudes y el consumo de respuestas hasta que haya conectividad móvil disponible. La capacidad de respuesta y la disponibilidad se han convertido en un aspecto primordial.

Aunque las conexiones a Internet son de alta velocidad hoy en día, el tiempo de respuesta siempre está involucrado al servir dicha información. Por lo tanto, como veremos en esta sección, implementamos mecanismos para gestionar el estado de nuestras aplicaciones de forma transparente para el usuario final.

#### De la pesadilla de los callbacks (callback hell) a las promesas
A veces, podríamos necesitar construir funcionalidades en nuestra aplicación que cambien su estado de forma asíncrona una vez que haya transcurrido el tiempo. En estos casos, debemos introducir patrones de código, como el patrón de *callback*, para manejar este cambio diferido en el estado de la aplicación.

En un callback, la función que desencadena la acción asíncrona acepta otra función como parámetro. La función se ejecuta cuando la operación asíncrona se ha completado.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 5: Gestión de tareas complejas con Servicios* para seguir el resto del capítulo. Después de obtener el código, te sugerimos realizar las siguientes acciones por simplicidad:
>
> - Eliminar la carpeta `favorites`
> - Eliminar el archivo `favorites.service.ts` y su archivo de pruebas unitarias
> - Eliminar el archivo `favorites.ts`
> - Eliminar el archivo `numeric.directive.ts` y su archivo de pruebas unitarias
> - Eliminar la carpeta `product-view`

Veamos cómo usar un callback a través de un ejemplo:

1. Abre el archivo `app.component.html` y agrega un elemento HTML `<header>` para mostrar la propiedad de componente `title` mediante interpolación:

```html
<header>{{ title }}</header>
<main class="main">
  <div class="content">
    <app-product-list></app-product-list>
  </div>
</main>
<footer appCopyright> - v{{ settings.version }}</footer>
<router-outlet />
```

2. Abre el archivo `app.component.ts` y crea la siguiente propiedad:

```typescript
private setTitle = () => {
  this.title = this.settings.title;
}
```

La propiedad `setTitle` se usa para cambiar la propiedad `title` del componente según la propiedad `title` de la configuración de la aplicación. Devuelve una función flecha porque la usaremos como un callback para otro método.

3. A continuación, crea un método `changeTitle` que llame a otro método llamado, por convención, `callback`, después de dos segundos:

```typescript
private changeTitle(callback: Function) {
  setTimeout(() => {
    callback();
  }, 2000);
}
```

4. Agrega un constructor para llamar al método `changeTitle`, pasando la propiedad `setTitle` como parámetro:

```typescript
constructor() {
  this.changeTitle(this.setTitle);
}
```

En el fragmento anterior, usamos la propiedad `setTitle` sin paréntesis porque pasamos firmas de funciones y no llamadas a funciones reales cuando usamos callbacks.

Si ejecutamos la aplicación Angular usando el comando `ng serve`, vemos que la propiedad `title` cambia después de dos segundos. El problema con el patrón que acabamos de describir es que el código puede volverse confuso y engorroso a medida que introducimos más callbacks anidados.

Considera el siguiente escenario en el que necesitamos profundizar en una jerarquía de carpetas para acceder a fotos en un dispositivo:

```typescript
getRootFolder(folder => {
  getAssetsFolder(folder, assets => {
    getPhotos(assets, photos => {});
  });
});
```

Dependemos de la llamada asíncrona anterior y de los datos que devuelve antes de poder realizar la siguiente llamada. Debemos ejecutar un método dentro de un callback que a su vez ejecuta otro método con un callback. El código rápidamente se vuelve complejo y difícil de leer, lo que lleva a una situación conocida como **callback hell** (el infierno de los callbacks).

Podemos evitar el *callback hell* usando **promesas** (*promises*). Las promesas introducen una nueva forma de visualizar la gestión de datos asíncronos al ajustarse a una interfaz más limpia y sólida. Se pueden encadenar diferentes operaciones asíncronas en el mismo nivel e incluso dividirse y devolverse desde otras funciones.

Para comprender mejor cómo funcionan las promesas, refactoricemos nuestro ejemplo de callback anterior:

1. Crea un nuevo método en la clase `AppComponent` llamado `onComplete` que devuelva un objeto `Promise`. Una promesa puede resolverse (*resolved*) o rechazarse (*rejected*). El parámetro `resolve` indica que la promesa se completó con éxito y opcionalmente devuelve un resultado:

```typescript
private onComplete() {
  return new Promise<void>(resolve => {
  });
}
```

2. Introduce un temporizador de dos segundos en la promesa para que se resuelva después de que haya transcurrido este tiempo:

```typescript
private onComplete() {
  return new Promise<void>(resolve => {
    setTimeout(() => {
      resolve();
    }, 2000);
  });
}
```

3. Ahora, reemplaza la llamada a `changeTitle` en el constructor con el método basado en promesas. Para ejecutar un método que devuelve una promesa, invocamos el método y lo encadenamos con el método `then`:

```typescript
constructor() {
  this.onComplete().then(this.setTitle);
}
```

No deberíamos notar ninguna diferencia significativa si volvemos a ejecutar la aplicación Angular. El verdadero valor de las promesas reside en la simplicidad y legibilidad que aportan a nuestro código. Ahora podríamos refactorizar el ejemplo de jerarquía de carpetas anterior en consecuencia:

```typescript
getRootFolder()
  .then(getAssetsFolder)
  .then(getPhotos);
```

El encadenamiento del método `then` en el código anterior muestra cómo podemos alinear una llamada asíncrona tras otra. Cada llamada asíncrona anterior pasa su resultado al siguiente método asíncrono.

Las promesas son atractivas, pero a veces podríamos necesitar producir una salida de respuesta que siga un proceso de transformación más complejo o incluso cancelar todo el proceso. No podemos lograr tal comportamiento con promesas porque se disparan tan pronto como se instancian. En otras palabras, las promesas no son perezosas (*not lazy*). Por otro lado, la posibilidad de cancelar una operación asíncrona después de haber sido disparada pero aún no completada puede ser muy útil en escenarios específicos. Las promesas nos permiten resolver o rechazar una operación asíncrona, pero a veces podríamos desear abortar todo antes de llegar a ese punto.

Además, las promesas se comportan como operaciones de una sola vez. Una vez resueltas, no podemos esperar recibir más información o notificaciones de cambio de estado a menos que ejecutemos todo desde cero. Para resumir las limitaciones de las promesas:

- No se pueden cancelar
- Se ejecutan inmediatamente
- Son operaciones de una sola vez; no hay una forma sencilla de reintentarlas
- Responden con un solo valor

Ilustremos algunas de estas limitaciones con un ejemplo:

1. Reemplaza `setTimeout` con `setInterval` en el método `onComplete`:

```typescript
private onComplete() {
  return new Promise<void>(resolve => {
    setInterval(() => {
      resolve();
    }, 2000);
  });
}
```

La promesa ahora se resolverá repetidamente cada dos segundos.

2. Modifica la propiedad `setTitle` para adjuntar la marca de tiempo actual en la propiedad `title` del componente:

```typescript
private setTitle = () => {
  const timestamp = new Date();
  this.title = `${this.settings.title} (${timestamp})`;
}
```

Ejecuta la aplicación Angular y notarás que la marca de tiempo se establece solo una vez después de dos segundos y nunca vuelve a cambiar. La promesa se resuelve a sí misma y todo el evento asíncrono termina en ese mismo instante.

Es posible que necesitemos una implementación más proactiva del manejo de datos asíncronos para corregir el comportamiento anterior, y aquí es donde los observables entran en escena.

#### Observables en pocas palabras
Un **observable** es un objeto que mantiene una lista de dependientes, llamados **observadores** (*observers*), y les informa sobre los cambios de estado y datos mediante la emisión de eventos de forma asíncrona. Para hacerlo, el observable implementa toda la maquinaria necesaria para producir y emitir dichos eventos. Se puede disparar y cancelar en cualquier momento, independientemente de si ya ha emitido los datos esperados.

Los observadores deben **suscribirse** a un observable para recibir notificaciones y reaccionar para reflejar el cambio de estado. Este patrón, conocido como el **patrón observer**, permite operaciones concurrentes y una lógica más avanzada. Estos observadores, también conocidos como **suscriptores** (*subscribers*), continúan escuchando lo que sucede en el observable hasta que se destruye. Podemos ver todo esto con más claridad en un ejemplo real:

1. Importa el artefacto `Observable` del paquete npm `rxjs`:

```typescript
import { Observable } from 'rxjs';
```

2. Crea una propiedad de componente llamada `title$` que cree un objeto `Observable`. El constructor de un observable acepta un objeto observador como parámetro. El observador es una función flecha que contiene la lógica de negocio que se ejecutará cuando alguien use el observable. Llama al método `next` del observador cada dos segundos para indicar un cambio de datos o de estado de la aplicación:

```typescript
title$ = new Observable(observer => {
  setInterval(() => {
    observer.next();
  }, 2000);
});
```

> Cuando definimos una variable observable, solemos añadir el signo `$` al final del nombre de la variable. Es una convención que seguimos para identificar observables en nuestro código de manera rápida y eficiente.

3. Modifica el constructor del componente para usar la propiedad `title$` recién creada:

```typescript
constructor() {
  this.title$.subscribe(this.setTitle);
}
```

Usamos el método `subscribe` para registrarnos en el observable `title$` y recibir notificaciones de cualquier cambio. Si no llamamos a este método, el método `setTitle` nunca se ejecutará.

> Un observable no hará nada a menos que un suscriptor se suscriba a él.

Si ejecutas la aplicación, notarás que la marca de tiempo cambia cada dos segundos. ¡Felicitaciones! ¡Has entrado en el mundo de los observables y la programación reactiva!

Los observables devuelven un flujo de eventos y nuestros suscriptores reciben notificaciones puntuales de esos eventos para que puedan actuar en consecuencia. No realizan una operación asíncrona y terminan (aunque podemos configurarlos para que lo hagan), sino que inician un flujo continuo de eventos a los que podemos suscribirnos.

Sin embargo, eso no es todo. Este flujo puede combinar muchas operaciones antes de llegar a los observadores suscritos a él. Así como podemos manipular arrays con métodos como `map` o `filter` para transformarlos, podemos hacer lo mismo con el flujo de eventos emitidos por observables. Es un patrón conocido como **programación reactiva**, y Angular aprovecha al máximo este paradigma para manejar información asíncrona.

---

### Sección 6.3: Programación reactiva en Angular

El patrón *observer* se encuentra en el núcleo de la programación reactiva. La implementación más básica de un script reactivo abarca varios conceptos con los que debemos familiarizarnos:

- Un **observable**
- Un **observador** (*observer*)
- Una **línea de tiempo** (*timeline*)
- Un **flujo de eventos** (*stream of events*)
- Un conjunto de **operadores componibles** (*composable operators*)

Puede sonar abrumador, pero no lo es. El gran desafío aquí es cambiar nuestra mentalidad y aprender a pensar de manera reactiva, que es el objetivo principal de esta sección.

> La programación reactiva implica aplicar suscripciones y transformaciones asíncronas a flujos observables de eventos.

Expliquémoslo a través de un ejemplo más descriptivo. Piensa en un dispositivo de interacción como un teclado. Tiene teclas que el usuario presiona. Cada una de esas pulsaciones de teclas desencadena un evento de teclado específico, como `keyUp`. El evento `keyUp` presenta una amplia gama de metadatos, incluido, entre otros, el código numérico de la tecla específica que el usuario presionó en un momento determinado. A medida que el usuario continúa presionando teclas, se activan más eventos `keyUp` que se canalizan a través de una línea de tiempo imaginaria. La línea de tiempo es un flujo continuo de datos donde el evento `keyUp` puede ocurrir en cualquier momento; después de todo, el usuario decide cuándo presionar esas teclas.

Recuerda el ejemplo con observables de la sección anterior. Ese código podía notificar a un observador que cada dos segundos se emitía otro valor. Sabemos con qué frecuencia se activa un intervalo de temporizador. En el caso de los eventos `keyUp`, no lo sabemos porque no están bajo nuestro control. Intentemos explicarlo más a fondo implementando un registrador de teclas (*key logger*) en nuestra aplicación:

1. Crea un nuevo componente de Angular llamado `key-logger`:

```bash
ng generate component key-logger
```

2. Abre el archivo `key-logger.component.html` y reemplaza su contenido con la siguiente plantilla HTML:

```html
<input type="text" #keyContainer />
You pressed: {{keys}}
```

En la plantilla anterior, agregamos un elemento HTML `<input>` y adjuntamos la variable de referencia de plantilla `keyContainer`.

> Se puede agregar una variable de referencia de plantilla a cualquier elemento HTML, no solo a componentes.

También mostramos una propiedad `keys` que representa todas las teclas del teclado que el usuario ha presionado.

3. Abre el archivo `key-logger.component.ts` e importa los artefactos `OnInit`, `viewChild` y `ElementRef` del paquete npm `@angular/core`:

```typescript
import { Component, ElementRef, OnInit, viewChild } from '@angular/core';
```

4. Crea las siguientes propiedades en la clase `KeyLoggerComponent`:

```typescript
input = viewChild<ElementRef>('keyContainer');
keys = '';
```

La propiedad `input` se usa para consultar el elemento HTML `<input>` mediante la variable de referencia de plantilla `keyContainer`.

5. Agrega la siguiente sentencia para importar el artefacto `fromEvent` del paquete npm `rxjs`:

```typescript
import { fromEvent } from 'rxjs';
```

La librería RxJS tiene varios artefactos útiles, llamados **operadores**, que podemos usar con observables. El operador `fromEvent` crea un observable a partir del evento del DOM de un elemento HTML nativo.

6. Implementa el método `ngOnInit` de la interfaz `OnInit` para escuchar los eventos `keyup` en el elemento `<input>` y guardar las teclas presionadas en la propiedad `keys`:

```typescript
export class KeyLoggerComponent implements OnInit {
  input = viewChild<ElementRef>('keyContainer');
  keys = '';

  ngOnInit(): void {
    const logger$ = fromEvent<KeyboardEvent>(this.input()!.nativeElement, 'keyup');
    logger$.subscribe(evt => this.keys += evt.key);
  }
}
```

Observa que obtenemos acceso al elemento de entrada HTML nativo a través de la propiedad `nativeElement` de la variable de referencia de plantilla. El resultado de consultar mediante la función `viewChild` es un objeto `ElementRef`, que es un contenedor sobre el elemento HTML real.

7. Abre el archivo `app.component.ts` e importa la clase `KeyLoggerComponent`:

```typescript
import { Component, inject } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';
import { CopyrightDirective } from './copyright.directive';
import { APP_SETTINGS, appSettings } from './app.settings';
import { Observable } from 'rxjs';
import { KeyLoggerComponent } from './key-logger/key-logger.component';

@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective,
    KeyLoggerComponent
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css',
  providers: [
    { provide: APP_SETTINGS, useValue: appSettings }
  ]
})
```

8. Abre el archivo `app.component.html` y agrega el selector `<app-key-logger>` en la plantilla:

```html
<header>{{ title }}</header>
<main class="main">
  <div class="content">
    <app-product-list></app-product-list>
  </div>
</main>
<footer appCopyright> - v{{ settings.version }}</footer>
<router-outlet />
<app-key-logger></app-key-logger>
```

9. Ejecuta la aplicación usando el comando `ng serve` y comienza a presionar teclas para verificar el uso del registrador de teclas que acabamos de crear:

*(Figura 6.1: Salida del registrador de teclas)*

Un aspecto esencial de los observables es el uso de operadores y el encadenamiento de observables entre sí, lo que permite una composición enriquecida. Los operadores observables se parecen a los métodos de arrays cuando queremos usarlos. Por ejemplo, un operador `map` para observables se usa de manera similar al método `map` de un array. En la siguiente sección, aprenderemos sobre la librería RxJS, que proporciona estos operadores, y veremos algunos de ellos a través de ejemplos.

---

### Sección 6.4: La librería RxJS

Como se mencionó anteriormente, Angular viene con una dependencia de pares con **RxJS**, la variante de JavaScript de la librería ReactiveX, que nos permite crear observables a partir de una gran variedad de escenarios, incluidos los siguientes:

- Eventos de interacción
- Promesas
- Funciones de callback
- Eventos

> La programación reactiva no pretende reemplazar patrones asíncronos como promesas o callbacks. Por el contrario, también puede aprovecharlos para crear secuencias observables.

RxJS tiene soporte integrado para varios operadores componibles para transformar, filtrar y combinar los flujos de eventos resultantes. Su API proporciona métodos convenientes para que los observadores se suscriban a estos flujos para que nuestros componentes puedan responder en consecuencia a los cambios de estado o la interacción de entrada. Veamos algunos de estos operadores en acción en las siguientes subsecciones.

#### Creación de observables
Ya hemos aprendido cómo crear un observable a partir de un evento del DOM usando el operador `fromEvent`. Otros dos operadores populares relacionados con la creación de observables son los operadores `of` y `from`.

El operador `of` se utiliza para crear un observable a partir de valores como números:

```typescript
const values = of(1, 2, 3);
values.subscribe(value => console.log(value));
```

El fragmento anterior imprime los números 1, 2 y 3 en la consola del navegador en orden.

El operador `from` se utiliza para convertir un array en un observable:

```typescript
const values = from([1, 2, 3]);
values.subscribe(value => console.log(value));
```

El operador `from` también es muy útil para convertir promesas o callbacks en observables. Podríamos envolver el método `onComplete` en el constructor de la clase `AppComponent` de la siguiente manera:

```typescript
constructor() {
  const complete$ = from(this.onComplete());
  complete$.subscribe(this.setTitle);
}
```

> ¡El operador `from` es una excelente manera de migrar a observables si usas promesas en una aplicación existente!

Además de crear observables, la librería RxJS también contiene un par de operadores prácticos para manipular y transformar los datos emitidos por los observables.

#### Transformación de observables
Ya aprendimos cómo crear una directiva exclusivamente numérica en el *Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas*. Ahora utilizaremos operadores de RxJS para lograr lo mismo en nuestro componente de registro de teclas:

1. Abre el archivo `key-logger.component.ts` e importa el operador `tap` del paquete npm `rxjs`:

```typescript
import { fromEvent, tap } from 'rxjs';
```

2. Refactoriza el método `ngOnInit` de la siguiente manera:

```typescript
ngOnInit(): void {
  const logger$ = fromEvent<KeyboardEvent>(this.input()!.nativeElement, 'keyup');
  logger$.pipe(
    tap(evt => this.keys += evt.key)
  ).subscribe();
}
```

El método `pipe` vincula y combina múltiples operadores separados por comas. Podemos pensar en él como una receta que define los operadores que se deben aplicar a un observable. Uno de ellos es el operador `tap`, que se usa cuando queremos hacer algo con los datos emitidos sin modificarlos.

Queremos excluir los valores no numéricos que emite el observable `logger$`. Ya obtenemos la tecla real presionada de la propiedad `evt`, pero devuelve valores alfanuméricos. No sería eficiente enumerar todos los valores no numéricos y excluirlos manualmente. En su lugar, utilizaremos el operador `map` para obtener el valor Unicode real de la tecla. Se comporta de manera similar al método `map` de un array, ya que devuelve un observable con una versión modificada de los datos iniciales.

3. Importa el operador `map` del paquete npm `rxjs`:

```typescript
import { fromEvent, tap, map } from 'rxjs';
```

4. Agrega el siguiente fragmento encima del operador `tap` en el método `ngOnInit`:

```typescript
map(evt => evt.key.charCodeAt(0))
```

Ahora podemos agregar el operador `filter`, que opera de manera similar al método `filter` de un array para excluir valores no numéricos.

5. Importa el operador `filter` del paquete npm `rxjs`:

```typescript
import { fromEvent, tap, map, filter } from 'rxjs';
```

6. Agrega el siguiente fragmento después del operador `map` en el método `ngOnInit`:

```typescript
filter(code => (code > 31 && (code < 48 || code > 57)) === false)
```

El observable actualmente emite códigos de caracteres Unicode. Debemos convertirlos nuevamente a caracteres de teclado para mostrarlos en la plantilla HTML.

7. Refactoriza el operador `tap` para acomodar este cambio:

```typescript
tap(digit => this.keys += String.fromCharCode(digit))
```

Como toque final, agregaremos un enlace de entrada en el componente para activar y desactivar condicionalmente la función de solo números:

8. Agrega la función `input` en la sentencia de importación del paquete npm `@angular/core`:

```typescript
import { Component, ElementRef, OnInit, viewChild, input } from '@angular/core';
```

9. Agrega una propiedad de entrada `numeric` en la clase `KeyLoggerComponent`:

```typescript
numeric = input(false);
```

10. Refactoriza el operador `filter` en el método `ngOnInit` para que tome en cuenta la propiedad `numeric`:

```typescript
filter(code => {
  if (this.numeric()) {
    return (code > 31 && (code < 48 || code > 57)) === false;
  }
  return true;
})
```

El observable `logger$` filtrará los valores no numéricos solo si la propiedad de entrada `numeric` es `true`.

El método `ngOnInit` finalmente debería verse así:

```typescript
ngOnInit(): void {
  const logger$ = fromEvent<KeyboardEvent>(this.input()!.nativeElement, 'keyup');
  logger$.pipe(
    map(evt => evt.key.charCodeAt(0)),
    filter(code => {
      if (this.numeric()) {
        return (code > 31 && (code < 48 || code > 57)) === false;
      }
      return true;
    }),
    tap(digit => this.keys += String.fromCharCode(digit))
  ).subscribe();
}
```

11. Abre el archivo `app.component.html` y agrega un enlace a la propiedad `numeric` en el selector `<app-key-logger>`:

```html
<app-key-logger [numeric]="true"></app-key-logger>
```

12. Ejecuta la aplicación mediante el comando `ng serve` e ingresa `Angular 19` dentro del cuadro de entrada:

*(Figura 6.2: Registrador de teclas numérico)*

Hemos visto operadores de RxJS que manipulan observables que devuelven tipos de datos primitivos como números, cadenas y arrays. En la siguiente sección, aprenderemos cómo usar observables en nuestra aplicación de comercio electrónico.

---

### Sección 6.5: Suscripción a observables

Ya hemos aprendido que un observador necesita suscribirse a un observable para obtener los datos emitidos. El observador en nuestro caso será el componente de lista de productos y el observable residirá dentro del archivo `products.service.ts`. Por lo tanto, primero debemos convertir la clase `ProductsService` para usar observables en lugar de arrays simples para que los componentes puedan suscribirse y obtener datos:

1. Abre el archivo `products.service.ts` y agrega la siguiente sentencia de importación:

```typescript
import { Observable, of } from 'rxjs';
```

2. Extrae los datos del producto utilizados en el método `getProducts` a una propiedad de servicio independiente para mejorar la legibilidad del código:

```typescript
private products: Product[] = [
  {
    id: 1,
    title: 'Keyboard',
    price: 100,
    categories: {
      1: 'Computing',
      2: 'Peripherals'
    }
  },
  {
    id: 2,
    title: 'Microphone',
    price: 35,
    categories: {
      3: 'Multimedia'
    }
  },
  {
    id: 3,
    title: 'Web camera',
    price: 79,
    categories: {
      1: 'Computing',
      3: 'Multimedia'
    }
  },
  {
    id: 4,
    title: 'Tablet',
    price: 500,
    categories: {
      4: 'Entertainment'
    }
  }
];
```

3. Modifica el método `getProducts` para que devuelva la propiedad `products` como un observable:

```typescript
getProducts(): Observable<Product[]> {
  return of(this.products);
}
```

En el fragmento anterior, usamos el operador `of` para crear un nuevo observable a partir del array `products`.

La clase `ProductsService` ahora emite datos de productos mediante observables. Debemos modificar el componente para suscribirse y obtener estos datos:

4. Abre el archivo `product-list.component.ts` y crea un método `getProducts` en la clase `ProductListComponent`:

```typescript
private getProducts() {
  this.productService.getProducts().subscribe(products => {
    this.products = products;
  });
}
```

En el método anterior, nos suscribimos al método `getProducts` de la clase `ProductsService` porque devuelve un observable en lugar de un array simple. El array `products` se devuelve dentro del método `subscribe`, donde establecemos la propiedad de componente `products` en el array emitido desde el observable.

5. Modifica el método `ngOnInit` para que llame al método `getProducts` recién creado:

```typescript
ngOnInit(): void {
  this.getProducts();
}
```

> Podríamos haber agregado el cuerpo del método `getProducts` directamente dentro del método `ngOnInit`. No lo hicimos ya que los métodos de eventos del ciclo de vida del componente deben ser lo más claros y concisos posible. Intenta siempre extraer su lógica en un método separado para mayor claridad.

6. Ejecuta la aplicación usando el comando `ng serve`, y deberías ver la lista de productos mostrada en la página con éxito:

*(Figura 6.3: Lista de productos)*

Como se muestra en la imagen anterior, hemos logrado el mismo resultado de mostrar la lista de productos que en el *Capítulo 5: Gestión de tareas complejas con Servicios*, pero usando observables. Puede que no sea evidente a primera vista, pero hemos establecido las bases para trabajar con el cliente HTTP de Angular, que se basa en observables. En el *Capítulo 8: Comunicación con servicios de datos a través de HTTP*, exploraremos el cliente HTTP con más detalle.

Cuando nos suscribimos a observables, somos propensos a posibles fugas de memoria (*memory leaks*) si no los limpiamos a tiempo. En la siguiente sección, aprenderemos sobre diferentes formas de lograrlo.

---

### Sección 6.6: Cancelación de suscripción de observables

Cuando nos suscribimos a un observable, creamos un observador que escucha los cambios en un flujo de datos. El observador observa el flujo continuamente mientras la suscripción permanece activa. Cuando una suscripción está activa, reserva memoria en el navegador y consume ciertos recursos. Si no le decimos al observador que cancele la suscripción en algún momento y libere los recursos, la suscripción al observable posiblemente provocará una fuga de memoria.

> Un observador generalmente necesita cancelar la suscripción cuando el componente de Angular que creó la suscripción debe destruirse.

Algunas de las técnicas más conocidas que se utilizan para cancelar la suscripción a observables son las siguientes:

- Cancelar la suscripción a un observable manualmente
- Usar el pipe `async` en una plantilla de componente

Veamos ambas técnicas en acción en las siguientes subsecciones.

#### Destrucción de un componente
Un componente tiene eventos de ciclo de vida que podemos usar para engancharnos y ejecutar lógica personalizada, como aprendimos en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*. Uno de ellos es el evento `ngOnDestroy`, que se llama cuando el componente se destruye y ya no existe.

Recuerda `ProductListComponent` y `ProductViewComponent`, que usamos anteriormente en nuestros ejemplos. Se suscriben a los métodos apropiados de `ProductsService` y `ProductViewService` al inicializarse el componente. Cuando los componentes se destruyen, la referencia de las suscripciones permanece activa, lo que puede provocar un comportamiento impredecible. Necesitamos cancelar la suscripción manualmente cuando los componentes se destruyen para liberar los recursos adecuadamente:

1. Abre el archivo `product-list.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { Subscription } from 'rxjs';
```

2. Crea la siguiente propiedad en la clase `ProductListComponent`:

```typescript
private productsSub: Subscription | undefined;
```

3. Asigna la propiedad `productsSub` al resultado de la suscripción en el método `getProducts`:

```typescript
private getProducts() {
  this.productsSub = this.productService.getProducts().subscribe(products => {
    this.products = products;
  });
}
```

4. Importa el hook del ciclo de vida `OnDestroy` del paquete npm `@angular/core`:

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
```

5. Agrega `OnDestroy` a la lista de interfaces implementadas de la clase `ProductListComponent`:

```typescript
export class ProductListComponent implements OnInit, OnDestroy
```

6. Implementa el método `ngOnDestroy` de la siguiente manera:

```typescript
ngOnDestroy(): void {
  this.productsSub?.unsubscribe();
}
```

El método `unsubscribe` elimina un observador de los oyentes activos de una suscripción y libera cualquier recurso reservado.

Eso es una gran cantidad de código repetitivo (*boilerplate*) para cancelar una sola suscripción. Puede volverse rápidamente ilegible e inmantenible si tenemos muchas suscripciones.

Alternativamente, podemos usar un tipo particular de operador llamado `takeUntilDestroyed`, que está disponible en el paquete `@angular/core/rxjs-interop`. Exploraremos la forma de cancelar la suscripción a observables utilizando este operador en el componente de lista de productos:

1. Abre el archivo `product-list.component.ts` e importa los artefactos `inject`, `DestroyRef` y `takeUntilDestroyed` de la siguiente manera:

```typescript
import { Component, DestroyRef, inject, OnInit } from '@angular/core';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';
```

El artefacto `takeUntilDestroyed` es un operador que cancela la suscripción a un observable cuando se destruye el componente.

2. Declara la siguiente propiedad para inyectar el servicio `DestroyRef`:

```typescript
private destroyRef = inject(DestroyRef);
```

3. Modifica el método `getProducts` de la siguiente manera:

```typescript
private getProducts() {
  this.productService.getProducts().pipe(
    takeUntilDestroyed(this.destroyRef)
  ).subscribe(products => {
    this.products = products;
  });
}
```

En el método anterior, usamos el operador `pipe` para encadenar el operador `takeUntilDestroyed` con la suscripción del método `getProducts` de la clase `ProductsService`. El operador `takeUntilDestroyed` acepta un parámetro del servicio `DestroyRef`.

4. Elimina cualquier código relacionado con el método `ngOnDestroy`.

¡Eso es todo! Ahora hemos convertido nuestra suscripción para que sea más declarativa y legible. Sin embargo, el problema del mantenimiento aún existe. Nuestros componentes ahora están cancelando la suscripción a sus observables manualmente. Podemos resolver eso usando un pipe de Angular de propósito especial: el **pipe `async`**, que nos permite cancelar la suscripción automáticamente con menos código.

#### Uso del pipe async
El pipe `async` es un pipe integrado de Angular que se usa junto con observables, y su función es doble: nos ayuda a escribir menos código y nos ahorra tener que configurar y desmantelar una suscripción. Se suscribe automáticamente a un observable y cancela la suscripción cuando se destruye el componente. Lo usaremos para simplificar el código del componente de lista de productos:

1. Abre el archivo `product-list.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { AsyncPipe } from '@angular/common';
import { Observable } from 'rxjs';
```

2. Agrega la clase `AsyncPipe` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent, SortPipe, AsyncPipe],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

3. Convierte la propiedad del componente `products` en un observable:

```typescript
products$: Observable<Product[]> | undefined;
```

4. Asigna el método `getProducts` de la clase `ProductsService` a la propiedad de componente `products$`:

```typescript
private getProducts() {
  this.products$ = this.productService.getProducts();
}
```

El cuerpo del método `getProducts` ahora se ha reducido a una línea y se ha vuelto más legible.

5. Abre el archivo `product-list.component.html` y agrega el siguiente fragmento al principio del archivo:

```html
@let products = (products$ | async)!;
```

En el fragmento anterior, nos suscribimos al observable `products$` usando el pipe `async` y creamos una variable de plantilla usando la palabra clave `@let`. La variable de plantilla tiene el mismo nombre que la propiedad de componente respectiva que teníamos anteriormente, por lo que no necesitamos cambiar más la plantilla del componente.

¡Eso es todo! ¡Ya no necesitamos suscribirnos ni cancelar la suscripción del observable manualmente! El pipe `async` se encarga de todo por nosotros.

Hemos aprendido que los observables reaccionan a los eventos de la aplicación y emiten valores de forma asíncrona a los observadores registrados. Podríamos visualizar los observables como objetos contenedores alrededor de los valores emitidos. Angular enriquece el campo de reactividad de las aplicaciones web al proporcionar un contenedor similar que funciona de forma sincrónica y reacciona a los cambios de estado de la aplicación.

---

### Sección 6.7: Resumen

Se necesita mucho más que un solo capítulo para cubrir en detalle todas las grandes cosas que podemos hacer con la reactividad en Angular. La buena noticia es que hemos cubierto todas las herramientas y clases que necesitamos para el desarrollo básico en Angular.

Aprendimos qué es la programación reactiva y cómo se puede usar en Angular. Vimos cómo aplicar técnicas reactivas como observables para interactuar con flujos de datos. Exploramos la librería RxJS y cómo usar algunos operadores para manipular observables. Aprendimos diferentes formas de suscribirse y cancelar suscripciones a observables en componentes de Angular.

El resto queda a tu imaginación, así que siéntete libre de ir un paso más allá y poner todo este conocimiento en práctica en tus aplicaciones de Angular. Las posibilidades son infinitas y tienes estrategias que van desde promesas hasta observables. Puedes aprovechar las increíbles funcionalidades de los patrones reactivos y crear experiencias reactivas asombrosas para tus aplicaciones de Angular.

Como ya hemos destacado, el cielo es el límite. Sin embargo, todavía tenemos un largo y emocionante camino por delante. En el próximo capítulo, exploraremos **signals**, un patrón reactivo alternativo integrado en el framework Angular. Aprenderemos a usar las señales de Angular para gestionar el estado de una aplicación Angular.
