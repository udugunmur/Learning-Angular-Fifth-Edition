# Parte 2: Reactividad y Gestión de Estado

## Capítulo 7: Seguimiento del estado de la aplicación con Signals

Angular permite a los desarrolladores utilizar la reactividad integrada en sus aplicaciones mediante **signals** (señales). Las señales de Angular son un enfoque sincrónico de la programación reactiva que mejora eficientemente el rendimiento de la aplicación y gestiona el estado de la misma.

Ya nos encontramos con las señales en capítulos anteriores, donde usamos el método `input` para intercambiar datos entre componentes y el método `viewChild` para consultar componentes hijos. La API de Signals se puede utilizar en diferentes partes de una aplicación Angular, por lo que su uso está distribuido a lo largo de los capítulos de este libro.

En este capítulo, cubriremos los siguientes temas:

- Comprendiendo las señales
- Lectura y escritura de señales
- Señales computadas
- Cooperación con RxJS

---

### Sección 7.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través del concepto de las señales de Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch07` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch07](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch07)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 7.2: Comprendiendo las señales

Como aprendimos en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, **Zone.js** juega un papel importante en el rendimiento de una aplicación Angular. Activa el mecanismo de detección de cambios de Angular cuando ocurren eventos particulares dentro de la aplicación. El framework verifica cada componente de la aplicación en cada ciclo de detección y evalúa sus enlaces, lo que degrada el rendimiento de la aplicación.

La lógica de la detección de cambios con Zone.js se basa en el hecho de que Angular no puede saber cuándo o dónde ha ocurrido un cambio dentro de la aplicación. Inevitablemente, los desarrolladores de Angular intentan limitar los ciclos de detección de cambios mediante las siguientes técnicas:

- Configurar componentes con la estrategia de detección de cambios `OnPush`
- Interactuar manualmente con el mecanismo de detección de cambios mediante el servicio `ChangeDetectorRef`

Las señales mejoran la forma en que los desarrolladores interactúan con el mecanismo de detección de cambios de Angular simplificando y mejorando las técnicas anteriores según las necesidades de la aplicación.

Las señales de Angular proporcionan una gestión más robusta y ergonómica del ciclo de detección de cambios basada en la reactividad. Observan cómo cambia el estado de la aplicación y permiten que el framework reaccione activando la detección de cambios solo en las partes afectadas por el cambio.

> Las señales son una característica innovadora del framework Angular que permitirá mejoras adicionales en el rendimiento de la aplicación al introducir aplicaciones sin zonas (*zoneless applications*) y componentes basados en señales en el futuro.

Las señales también actúan como contenedores de valores que el mecanismo de detección de cambios debe verificar. Cuando un valor cambia, las señales notifican al framework sobre ese cambio. El framework es responsable de activar la detección de cambios y actualizar a los consumidores de la señal. El valor de una señal puede cambiar directamente mediante señales de escritura (*writable signals*) o indirectamente mediante señales de solo lectura o computadas (*computed signals*).

En la siguiente sección, aprenderemos cómo funcionan las señales de escritura.

---

### Sección 7.3: Lectura y escritura de señales

Una señal de escritura se indica mediante el tipo `signal` del paquete npm `@angular/core`.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 6: Patrones reactivos en Angular* para seguir el resto del capítulo. Después de obtener el código, te sugerimos eliminar la carpeta `key-logger` por simplicidad.

Comencemos y aprendamos cómo podemos escribir un valor en una señal:

1. Abre el archivo `app.component.ts` e importa el artefacto `signal` del paquete npm `@angular/core`:

```typescript
import { Component, inject, signal } from '@angular/core';
```

2. Declara la siguiente propiedad en la clase `AppComponent` como una señal e inicialízala:

```typescript
currentDate = signal(new Date());
```

3. Reemplaza la variable `timestamp` en la propiedad `setTitle` con el siguiente fragmento:

```typescript
this.currentDate.set(new Date());
```

En el fragmento anterior, usamos el método `set` para escribir un nuevo valor en la señal. El método notifica al framework Angular que el valor ha cambiado y que debe ejecutar el mecanismo de detección de cambios.

4. Modifica la propiedad `title` para usar el valor de la señal `currentDate`:

```typescript
this.title = `${this.settings.title} (${this.currentDate()})`;
```

En el fragmento anterior, llamamos al método getter `currentDate()` para leer el valor de la señal.

Las señales son una excelente opción en los casos en que la velocidad y el rendimiento de una aplicación importan, como por ejemplo:

- Una página de panel de control (*dashboard*) con widgets y datos en vivo que deben actualizarse con regularidad, como una aplicación de bolsa de valores.
- Un componente que necesita mostrar propiedades de un objeto grande o complejo, como el siguiente:

```typescript
const order = {
  no: '1',
  date: new Date(),
  products: [
    { id: 1, title: 'Keyboard', price: 100 },
    { id: 2, title: 'Microphone', price: 35 }
  ],
  customerCode: '0002',
  isCompleted: false
};
```

En este caso, podemos extraer las propiedades del objeto que deseamos en una señal sin involucrar a todo el objeto en el ciclo de detección de cambios, como:

```typescript
const orderDetails = signal({
  no: '1',
  customerCode: '0002',
  isCompleted: false
});
```

Un método similar de las señales que también desencadena la detección de cambios es el método `update`. Se utiliza cuando queremos establecer un nuevo valor en una señal en función de su valor actual:

```typescript
this.currentDate.update(d => {
  return new Date(d.getFullYear(), d.getMonth(), d.getDate(), 0, 0);
});
```

El fragmento anterior obtendrá el valor de la señal `currentDate` en la variable `d` y lo usará para devolver un nuevo objeto `Date`.

En la siguiente sección, exploraremos cómo se comportan las señales computadas en una aplicación Angular.

---

### Sección 7.4: Señales computadas

Una señal computada o de solo lectura depende de otras señales, ya sean de escritura o computadas. El valor de una señal computada no puede cambiar directamente usando el método `set` o `update`; solo puede cambiar indirectamente cuando cambia el valor de cualquiera de las otras señales de las que deriva.

Veamos cómo funciona:

1. Abre el archivo `app.component.ts` e importa los artefactos `computed` y `Signal` del paquete npm `@angular/core`:

```typescript
import { Component, inject, Signal, computed, signal } from '@angular/core';
```

2. Cambia el tipo de la propiedad del componente `title` a `Signal`:

```typescript
title: Signal<string> = signal('');
```

El tipo `Signal` indica que la señal es computada.

3. Elimina la asignación de `title` del método `setTitle` y agrégala dentro del constructor de la siguiente manera:

```typescript
constructor() {
  this.title$.subscribe(this.setTitle);
  this.title = computed(() => {
    return `${this.settings.title} (${this.currentDate()})`;
  });
}
```

En el fragmento anterior, usamos la función `computed` para establecer el valor de la señal `title`. El valor de la señal `title` depende de la señal `currentDate`. Se actualiza cada 2 segundos cuando cambia el valor de la señal `currentDate`.

4. Abre el archivo `app.component.html` y modifica el elemento HTML `<header>` de la siguiente manera:

```html
<header>{{ title() }}</header>
```

5. Ejecuta la aplicación usando `ng serve` y verifica que el título se actualice correctamente.

Las señales computadas tienen un gran rendimiento cuando se trata de cálculos más complicados que el anterior debido a las siguientes razones:

- La función `computed` se ejecuta cuando el valor de la señal se lee por primera vez en la plantilla.
- Un nuevo valor de señal se calcula únicamente cuando cambian las señales derivadas.
- Las señales computadas utilizan un mecanismo de caché para memorizar (*memoize*) los valores y devolverlos sin recalcularlos.

Aunque las señales son un enfoque reactivo moderno para Angular, son relativamente nuevas en el ecosistema de Angular en comparación con RxJS. En la siguiente sección, aprenderemos cómo pueden cooperar con RxJS en una aplicación Angular.

---

### Sección 7.5: Cooperación con RxJS

Las señales y RxJS potencian las aplicaciones Angular con capacidades reactivas. Estas librerías pueden complementarse entre sí para proporcionar reactividad aprovechando los beneficios del framework Angular. Las señales no se crearon para reemplazar a RxJS, sino para proporcionar un enfoque reactivo alternativo a los desarrolladores con las siguientes características adicionales:

- Reactividad de grano fino (*fine-grained reactivity*)
- Programación imperativa
- Uso mejorado del mecanismo de detección de cambios

Sin embargo, hay partes centrales en el framework Angular que todavía usan RxJS y observables, como el cliente HTTP y el enrutador. Además, muchos desarrolladores prefieren el enfoque declarativo que la librería RxJS proporciona de forma predeterminada.

Al momento de escribir este texto, el equipo de Angular está investigando y experimentando para hacer que RxJS sea opcional para las aplicaciones Angular en un futuro previsible. También están trabajando para convertir las API integradas, como el cliente HTTP y el enrutador, en señales.

Angular Signals proporciona una API integrada para cooperar con RxJS y observables. La API de Signals proporciona una función que puede convertir un observable en una señal:

1. Abre el archivo `product-list.component.ts` e importa los artefactos `inject` y `toSignal`:

```typescript
import { Component, inject } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';
```

El paquete npm `@angular/core/rxjs-interop` incluye todos los métodos de utilidad para manejar la cooperación entre señales y observables. La función `toSignal` puede convertir un observable en una señal.

> El paquete `rxjs-interop` también contiene métodos de utilidad para convertir una señal en un observable. Puedes leer más en *Reactive Patterns with RxJS and Angular Signals* de Lamis Chebbi (Packt Publishing).

2. Crea la siguiente señal en la clase `ProductListComponent`:

```typescript
products = toSignal(inject(ProductsService).getProducts(), { initialValue: [] });
```

Pasamos dos parámetros en la función `toSignal`: el observable que queremos convertir y opcionalmente un valor inicial. En este caso, pasamos el método `getProducts` de la clase `ProductService` que devuelve un observable, y también establecemos el valor inicial de la señal como un array vacío.

3. Abre el archivo `product-list.component.html` y modifica su contenido de la siguiente manera:

```html
@if (products().length > 0) {
  <h1>Products ({{products().length}})</h1>
}
<ul class="pill-group">
  @for (product of products() | sort; track product.id) {
    <li class="pill" (click)="selectedProduct = product">
      @switch (product.title) {
        @case ('Keyboard') {
        }
        @case ('Microphone') {
        }
        @default {
        }
      }
      {{product.title}}
    </li>
  } @empty {
    <p>No products found!</p>
  }
</ul>
<app-product-detail
  [product]="selectedProduct"
  (added)="onAdded()"
></app-product-detail>
```

En la plantilla anterior, eliminamos el bloque `@if` superior y convertimos la propiedad `products` en una señal. No necesitamos el pipe `async` porque las señales se suscriben automáticamente a un observable.

Para limpiar aún más nuestro componente, podemos eliminar cualquier código relacionado con el pipe `async` y los observables, ya que ya no es necesario. El archivo `product-list.component.ts` resultante debería ser el siguiente:

```typescript
import { Component, inject } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';
import { SortPipe } from '../sort.pipe';
import { ProductsService } from '../products.service';

@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent, SortPipe],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent {
  selectedProduct: Product | undefined;
  products = toSignal(inject(ProductsService).getProducts(), { initialValue: [] });

  onAdded() {
    alert(`${this.selectedProduct?.title} added to the cart!`);
  }
}
```

4. Ejecuta la aplicación usando `ng serve` y observa que la salida de la aplicación muestra la lista de productos.

El fragmento anterior parece mucho más simple. Las señales de Angular mejoran la experiencia del desarrollador y la ergonomía, además del rendimiento de nuestras aplicaciones.

---

### Sección 7.6: Resumen

En este capítulo, exploramos las señales (*signals*), que son un nuevo patrón reactivo en Angular utilizado para gestionar el estado de la aplicación. Aprendimos su justificación y cómo se comparan con Zone.js. Exploramos ejemplos de cómo leer y escribir valores en señales. También aprendimos cómo crear señales computadas que dependen de valores de otras señales.

En el próximo capítulo, aprenderemos cómo utilizar el cliente HTTP de Angular y consumir datos desde un punto de conexión (*endpoint*) remoto.
