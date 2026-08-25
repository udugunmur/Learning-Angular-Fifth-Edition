# Parte 1: Fundamentos de Angular

## Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas

En el capítulo anterior, creamos varios componentes que renderizaban datos en la pantalla con la ayuda de propiedades de entrada y salida. Aprovecharemos ese conocimiento en este capítulo para llevar nuestros componentes al siguiente nivel utilizando **pipes** y **directivas** de Angular. Los pipes nos permiten procesar y transformar la información que vinculamos en nuestras plantillas. Las directivas permiten funcionalidades más ambiciosas, como manipular el DOM o alterar la apariencia y el comportamiento de los elementos HTML.

En este capítulo, aprenderemos sobre los siguientes conceptos:

- Manipulación de datos con pipes
- Creación de pipes
- Creación de directivas

---

### Sección 4.1: Requisitos técnicos

Este capítulo contiene ejemplos de código para guiarte a través de los pipes y las directivas de Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch04` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch04](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch04)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 4.2: Manipulación de datos con pipes

Los **pipes** nos permiten transformar el resultado de nuestras expresiones a nivel de vista. Toman datos como entrada, los transforman al formato deseado y muestran la salida en la plantilla.

La sintaxis de un pipe consiste en el nombre del pipe colocado después de la expresión que queremos transformar, separado por un símbolo de barra vertical (`|`):

```html
expression | pipe
```

Cualquier parámetro se añade después del nombre del pipe, separados por dos puntos (`:`):

```html
expression | pipe:param
```

Los pipes se pueden utilizar con interpolación y enlace de propiedades en plantillas de Angular y se pueden encadenar entre sí.

Angular tiene una amplia gama de tipos de pipes integrados de fábrica:

- **`uppercase`/`lowercase`:** Transforma una cadena de texto en letras mayúsculas o minúsculas.
- **`percent`:** Da formato a un número como porcentaje.
- **`date`:** Da formato a una fecha o cadena en un formato de fecha específico. El uso predeterminado del pipe muestra la fecha de acuerdo con la configuración regional del equipo del usuario. Sin embargo, podemos pasar formatos adicionales integrados en Angular como parámetros.
- **`currency`:** Da formato a un número como moneda local. Podemos anular la configuración local y cambiar el símbolo de moneda pasando el código de moneda como parámetro al pipe.
- **`json`:** Toma un objeto como entrada y lo muestra en formato JSON, reemplazando las comillas simples por comillas dobles. El uso principal del pipe `json` es la depuración. Es una excelente manera de ver qué contiene un objeto complejo e imprimirlo claramente en la pantalla.
- **`keyvalue`:** Convierte un objeto en una colección de pares clave-valor, donde la clave de cada elemento representa la propiedad del objeto y el valor es su valor real.
- **`slice`:** Extrae un subconjunto (*slice*) de una colección o cadena. Acepta como parámetros un índice inicial, donde comenzará a cortar los datos de entrada, y opcionalmente un índice final. Cuando se especifica el índice final, el elemento en ese índice no se incluye en el array resultante. Si se omite el índice final, se extiende hasta el último índice de los datos.

> El pipe `slice` transforma datos inmutables. La lista transformada es siempre una copia de los datos originales, incluso cuando devuelve todos los elementos.

- **`async`:** Se utiliza cuando gestionamos datos manejados de forma asíncrona por nuestra clase de componente y necesitamos asegurarnos de que nuestras vistas reflejen puntualmente los cambios. Aprenderemos más sobre este pipe más adelante en el *Capítulo 8: Comunicación con servicios de datos a través de HTTP*, donde lo utilizaremos para obtener y mostrar datos de forma asíncrona.

Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 3: Estructuración de interfaces de usuario con componentes* para seguir el resto del capítulo.

Cubriremos los pipes `lowercase`, `currency` y `keyvalue` en más detalle, pero te recomendamos explorar el resto en la referencia de la API en [https://angular.dev/api](https://angular.dev/api):

1. Abre el archivo `product-detail.component.ts` e importa la clase `CommonModule`:

```typescript
import { CommonModule } from '@angular/common';
import { Component, input, output } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [CommonModule],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

> **Consejo rápido:** Mejora tu experiencia de programación con las funciones AI Code Explainer y Quick Copy en el lector de nueva generación de Packt: [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

La clase `CommonModule` exporta los pipes integrados de Angular. Un componente de Angular debe importar `CommonModule` antes de usar pipes integrados en la plantilla del componente.

2. Abre el archivo `product.ts` y agrega los siguientes campos a la interfaz `Product` que describen propiedades adicionales para un producto:

```typescript
export interface Product {
  id: number;
  title: string;
  price: number;
  categories: Record<number, string>;
}
```

La propiedad `categories` es un objeto donde la clave representa el identificador de la categoría y el valor representa la descripción de la categoría.

3. Abre el archivo `product-list.component.ts` y modifica el array `products` para establecer valores para las nuevas propiedades:

```typescript
products: Product[] = [
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

4. Abre el archivo `product-detail.component.html` y agrega un elemento de párrafo para mostrar el precio del producto seleccionado en euros:

```html
@if (product()) {
  <p>
    You selected: <strong>{{product()!.title}}</strong>
  </p>
  <p>{{product()!.price | currency:'EUR'}}</p>
  <button (click)="addToCart()">Add to cart</button>
}
```

5. Ejecuta `ng serve` para iniciar la aplicación y selecciona el `Microphone` de la lista de productos:

*(Figura 4.1: Detalles del producto)*

En la imagen anterior, el precio del producto se muestra en formato de moneda.

6. Agrega el siguiente fragmento debajo del precio del producto para mostrar las categorías del producto:

```html
<div class="pill-group">
  @for (cat of product()!.categories | keyvalue; track cat.key) {
    <p class="pill">{{cat.value | lowercase}}</p>
  }
</div>
```

En el fragmento anterior, usamos el bloque `@for` para iterar sobre la propiedad `categories` de la variable `product`. La propiedad `categories` no es iterable porque es un objeto simple, por lo que usamos el pipe `keyvalue` para convertirlo en un array que contiene propiedades `key` y `value`. La propiedad `key` representa el ID de categoría, un identificador único que podemos usar con la variable `track`. La propiedad `value` almacena la descripción de la categoría.

Además, usamos el pipe `lowercase` para convertir la descripción de la categoría en texto en minúsculas.

7. Agrega los siguientes estilos CSS al archivo `product-detail.component.css`:

```css
.pill-group {
  display: flex;
  flex-direction: row;
  align-items: start;
  flex-wrap: wrap;
  gap: 1.25rem;
}

.pill {
  display: flex;
  align-items: center;
  --pill-accent: var(--gray-900);
  background: color-mix(in srgb, var(--pill-accent) 5%, transparent);
  color: var(--pill-accent);
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border-radius: 2.75rem;
  border: 0;
  transition: background 0.3s ease;
  font-family: var(--inter-font);
  font-size: 0.875rem;
  font-style: normal;
  font-weight: 500;
  line-height: 1.4rem;
  letter-spacing: -0.00875rem;
  text-decoration: none;
}
```

8. Con la aplicación en ejecución, selecciona el producto `Web camera` de la lista:

*(Figura 4.2: Detalles del producto con categorías)*

Como alternativa al uso de `CommonModule`, podríamos haber importado cada clase de pipe por separado del paquete npm `@angular/common`:

```typescript
import { CurrencyPipe, KeyValuePipe, LowerCasePipe } from '@angular/common';
import { Component, input, output } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [KeyValuePipe, CurrencyPipe, LowerCasePipe],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

En el archivo `product-detail.component.html` final, usamos el fragmento `product()!` muchas veces para leer el valor de la propiedad `product`. Alternativamente, podríamos crear un alias usando la sintaxis `@let` de la siguiente manera:

```html
@let selectedProduct = product()!;
```

La palabra clave `@let` es similar a la palabra clave `let` en JavaScript y se usa para declarar variables que están disponibles solo en la plantilla del componente. En el fragmento anterior, declaramos la variable `selectedProduct`, que se puede usar en el resto del código HTML de la siguiente manera:

```html
@if (selectedProduct) {
  <p>
    You selected: <strong>{{selectedProduct.title}}</strong>
  </p>
  <p>{{selectedProduct.price | currency:'EUR'}}</p>
  <div class="pill-group">
    @for (cat of selectedProduct.categories | keyvalue; track cat.key) {
      <p class="pill">{{cat.value | lowercase}}</p>
    }
  </div>
  <button (click)="addToCart()">Add to cart</button>
}
```

La palabra clave `@let` nos ayuda en los casos en que queremos usar expresiones complejas en plantillas como:

- Operadores ternarios
- Propiedades de objetos anidados
- El pipe `async`

Los pipes integrados son suficientes para la mayoría de los casos de uso, pero debemos aplicar transformaciones complejas a nuestros datos en otros casos. El framework Angular proporciona un mecanismo para crear pipes personalizados únicos, como veremos en la siguiente sección.

---

### Sección 4.3: Creación de pipes

Ya hemos visto qué son los pipes y cuál es su propósito en el ecosistema de Angular. A continuación, profundizaremos en cómo podemos construir un pipe para proporcionar transformaciones personalizadas a los enlaces de datos. En la siguiente sección, crearemos un pipe que ordene nuestra lista de productos por título.

#### Ordenar datos utilizando pipes
Para crear un nuevo pipe, usamos el comando `ng generate` de Angular CLI, pasando su nombre como parámetro:

```bash
ng generate pipe sort
```

El comando anterior generará todos los archivos necesarios del pipe `sort` dentro de la carpeta donde ejecutamos el comando `ng generate`. La clase TypeScript del pipe se define en el archivo `sort.pipe.ts`:

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sort'
})
export class SortPipe implements PipeTransform {
  transform(value: unknown, ...args: unknown[]): unknown {
    return null;
  }
}
```

`@Pipe` es un decorador de Angular que define el nombre del pipe de Angular.

La clase TypeScript de un pipe implementa el método `transform` de la interfaz `PipeTransform` y acepta dos parámetros:

- **`value`:** Los datos de entrada que queremos transformar.
- **`args`:** Una lista opcional de argumentos que podemos proporcionar al método de transformación, cada uno separado por dos puntos.

Angular CLI nos ayudó generando un método `transform` vacío. Ahora necesitamos modificarlo para satisfacer nuestras necesidades comerciales. El pipe operará sobre una lista de objetos `Product`, por lo que debemos realizar los ajustes necesarios en los tipos proporcionados:

1. Agrega la siguiente sentencia para importar la interfaz `Product`:

```typescript
import { Product } from './product';
```

2. Cambia el tipo del parámetro `value` a `Product[]` ya que queremos ordenar una lista de objetos `Product`.
3. Cambia el tipo de retorno del método a `Product[]` ya que la lista ordenada solo contendrá objetos `Product`, y modifícalo para que devuelva un array vacío por defecto.

El archivo `sort.pipe.ts` resultante debería verse ahora de la siguiente manera:

```typescript
import { Pipe, PipeTransform } from '@angular/core';
import { Product } from './product';

@Pipe({
  name: 'sort'
})
export class SortPipe implements PipeTransform {
  transform(value: Product[], ...args: unknown[]): Product[] {
    return [];
  }
}
```

Ahora estamos listos para implementar el algoritmo de ordenación de nuestro método. Usaremos el método nativo `sort`, que ordena los elementos alfabéticamente por defecto. Proporcionaremos una función de comparación personalizada al método `sort` que anula la funcionalidad predeterminada y realiza la lógica de ordenación que queremos lograr:

```typescript
transform(value: Product[], ...args: unknown[]): Product[] {
  if (value) {
    return value.sort((a: Product, b: Product) => {
      if (a.title < b.title) {
        return -1;
      } else if (b.title < a.title) {
        return 1;
      }
      return 0;
    });
  }
  return [];
}
```

Vale la pena señalar que el método `transform` comprueba si hay datos de entrada primero antes de continuar con el proceso de ordenación. De lo contrario, devuelve un array vacío. Esto mitiga los casos en los que la colección se establece de forma asíncrona o el componente que consume el pipe no establece la colección en absoluto.

> Para obtener más información sobre el método `sort`, consulta [https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/sort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/sort).

¡Eso es todo! Hemos creado con éxito nuestro primer pipe. Necesitamos llamarlo desde la plantilla de nuestro componente para verlo en acción:

1. Abre el archivo `product-list.component.ts` e importa la clase `SortPipe`:

```typescript
import { Component } from '@angular/core';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';
import { SortPipe } from '../sort.pipe';

@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent, SortPipe],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

2. Abre el archivo `product-list.component.html` y agrega el pipe en el bloque `@for`:

```html
<ul class="pill-group">
  @for (product of products | sort; track product.id) {
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
```

Si ejecutamos la aplicación con el comando `ng serve`, notaremos que la lista de productos ahora está ordenada por título alfabéticamente:

*(Figura 4.3: Lista de productos ordenada por título alfabéticamente)*

El pipe `sort` solo puede ordenar datos de productos por título. En la siguiente sección, aprenderemos a configurar el pipe para que también pueda ordenar por otras propiedades del producto.

#### Paso de parámetros a pipes
Como aprendimos en la sección *Manipulación de datos con pipes*, podemos pasar parámetros adicionales a un pipe usando dos puntos. Usamos el parámetro `args` en el método `transform` de un pipe para obtener el valor de cada parámetro separado por dos puntos. Aprendimos que Angular CLI crea el parámetro `args` por defecto y usa el operador de propagación (*spread operator*) para expandir sus valores en el método:

```typescript
transform(value: Product[], ...args: unknown[]): Product[] {
  if (value) {
    return value.sort((a: Product, b: Product) => {
      if (a.title < b.title) {
        return -1;
      } else if (b.title < a.title) {
        return 1;
      }
      return 0;
    });
  }
  return [];
}
```

El método `transform` actualmente solo puede funcionar con la propiedad `title` de un producto. Podríamos aprovechar el parámetro `args` para hacerlo dinámico y permitir que el consumidor del pipe defina la propiedad por la que desea ordenar los datos, como el precio del producto:

1. Elimina el operador de propagación del parámetro `args` porque pasaremos una sola propiedad de un producto cada vez y cambia su tipo de la siguiente manera:

```typescript
transform(value: Product[], args: keyof Product): Product[] {
  if (value) {
    return value.sort((a: Product, b: Product) => {
      if (a.title < b.title) {
        return -1;
      } else if (b.title < a.title) {
        return 1;
      }
      return 0;
    });
  }
  return [];
}
```

En el método anterior, usamos el operador de tipo `keyof` de TypeScript para definir que el parámetro `args` puede ser cualquier propiedad de un objeto `Product`.

2. Reemplaza la propiedad `title` con el parámetro `args` dentro de la sentencia `if`:

```typescript
if (value) {
  return value.sort((a: Product, b: Product) => {
    if (a[args][args] < b) {
      return -1;
    } else if (b[args][args] < a) {
      return 1;
    }
    return 0;
  });
}
```

Observa que en el fragmento anterior, accedemos a los objetos `a` y `b` usando la sintaxis de corchetes en lugar de la sintaxis de puntos como antes.

3. Modifica el parámetro `args` en la firma del método para que use la propiedad `title` por defecto, si el consumidor no pasa ningún parámetro en el pipe:

```typescript
transform(value: Product[], args: keyof Product = 'title')
```

El comportamiento anterior asegura que el componente de lista de productos funcionará sin ningún cambio en el uso del pipe.

4. Ejecuta el comando `ng serve` y verifica que la lista de productos esté ordenada inicialmente por título.
5. Abre el archivo `product-list.component.html` y pasa la propiedad `price` como parámetro del pipe:

```html
@for (product of products | sort:'price'; track product.id) {
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
}
```

Guarda el archivo y espera a que la aplicación se recargue. Deberías ver que la lista de productos ahora está ordenada por precio:

*(Figura 4.4: Lista de productos ordenada por precio)*

El decorador `@Pipe` contiene otra propiedad significativa que podemos establecer, la cual está directamente relacionada con la forma en que los pipes reaccionan en el mecanismo de detección de cambios del framework Angular.

#### Detección de cambios con pipes
Hay dos categorías de pipes: **puros** (*pure*) e **impuros** (*impure*). Todos los pipes se consideran puros por defecto a menos que configuremos la propiedad `pure` explícitamente en `false` en el decorador `@Pipe`:

```typescript
@Pipe({
  name: 'sort',
  pure: false
})
```

Angular ejecuta pipes puros cuando hay un cambio en la referencia de la variable de entrada. Por ejemplo, si el array `products` en la clase `ProductListComponent` se asigna a un nuevo valor, el pipe reflejará correctamente ese cambio. Sin embargo, si agregamos un nuevo producto al array usando el método nativo `Array.push`, el pipe no se activará porque la referencia del objeto del array no cambia.

Otro ejemplo es cuando creamos un pipe puro que opera sobre un solo objeto. Del mismo modo, si la referencia del valor cambia, el pipe se ejecuta correctamente. Si cambia una propiedad del objeto, el pipe no puede detectar el cambio.

Sin embargo, una advertencia: los pipes impuros llaman al método `transform` cada vez que se activa el ciclo de detección de cambios. Por lo tanto, esto puede no ser bueno para el rendimiento. Alternativamente, puedes dejar la propiedad `pure` sin establecer e intentar almacenar en caché el valor o trabajar con reductores y datos inmutables para resolver esto de una mejor manera, como la siguiente:

```typescript
this.products = [
  ...this.products,
  {
    id: 5,
    title: 'Headphones',
    price: 55,
    categories: {
      3: 'Multimedia'
    }
  }
];
```

En el fragmento anterior, usamos la sintaxis de parámetro de propagación para crear una nueva referencia del array `products` agregando un nuevo elemento a la referencia del array existente.

Como alternativa a un pipe puro, podemos usar una señal computada (*computed signal*), que es más efectiva y ergonómica debido a las siguientes razones:

- Podemos acceder al valor de la señal en la clase del componente, a diferencia de los pipes, donde sus valores solo se pueden leer en la plantilla.
- Una señal computada es una función simple y directa, por lo que no necesitamos usar una clase TypeScript como en los pipes.

> Aprenderemos más sobre las señales en el *Capítulo 7: Seguimiento del estado de la aplicación con Signals*.

Crear pipes personalizados nos permite transformar nuestros datos de una manera particular según nuestras necesidades. Debemos crear directivas personalizadas si también queremos transformar elementos de plantilla.

---

### Sección 4.4: Creación de directivas

Las **directivas de Angular** son atributos HTML que extienden el comportamiento o la apariencia de un elemento HTML estándar. Cuando aplicamos una directiva a un elemento HTML o incluso a un componente de Angular, podemos agregar un comportamiento personalizado o alterar su apariencia. Hay tres tipos de directivas:

- **Componentes:** Los componentes son directivas que contienen una plantilla HTML asociada.
- **Directivas estructurales:** Estas agregan o eliminan elementos del DOM.
- **Directivas de atributo:** Estas modifican la apariencia de un elemento DOM o definen un comportamiento personalizado. Nos encontramos con directivas de atributo en los enlaces de clases y estilos en el capítulo anterior.

> Si una directiva tiene una plantilla adjunta, se convierte en un componente. En otras palabras, los componentes son directivas de Angular con una vista. Esta regla es muy útil al decidir si crear un componente o una directiva para tus necesidades. Si necesitas una plantilla, crea un componente; de lo contrario, conviértelo en una directiva.

Las directivas personalizadas nos permiten asociar comportamientos avanzados a elementos en el DOM o modificar su apariencia. En las siguientes secciones, exploraremos cómo crear directivas de atributo.

#### Visualización de datos dinámicos
Las directivas de atributo se usan comúnmente para alterar la apariencia de un elemento HTML. Probablemente todos nos hayamos encontrado en una situación en la que queremos agregar información de derechos de autor (*copyright*) a nuestras aplicaciones. Idealmente, queremos usar esta información en varias partes de nuestra aplicación, en un panel de control o en una página de contacto. El contenido de la información también debe ser dinámico. El año o rango de años (depende de cómo quieras usarlo) debe actualizarse dinámicamente según la fecha actual.

Nuestra primera intención probablemente sea crear un componente, pero ¿qué tal si lo convertimos en una directiva en su lugar? De esta manera, podríamos adjuntar la directiva a cualquier elemento que queramos y no preocuparnos por una plantilla en particular. ¡Así que comencemos!

Usaremos el comando `ng generate` de Angular CLI, pasando el nombre de la directiva como parámetro:

```bash
ng generate directive copyright
```

El comando anterior generará todos los archivos necesarios de la directiva `copyright` dentro de la carpeta donde ejecutamos el comando `ng generate`. La clase TypeScript de la directiva se define en el archivo `copyright.directive.ts`:

```typescript
import { Directive } from '@angular/core';

@Directive({
  selector: '[appCopyright]'
})
export class CopyrightDirective {
  constructor() { }
}
```

`@Directive` es un decorador de Angular que define las propiedades de la directiva de Angular. Configura una clase TypeScript como una directiva de Angular utilizando la propiedad `selector`. Es un selector CSS que indica a Angular que cargue la directiva en la ubicación donde encuentre el atributo correspondiente en una plantilla HTML. Angular CLI agrega el prefijo `app` de forma predeterminada, pero puedes personalizarlo usando la opción `--prefix` al crear el proyecto de Angular.

> Cuando usamos el selector en una plantilla HTML, no agregamos los corchetes.

Usemos la directiva recién creada para agregar información de copyright a nuestra aplicación:

1. Abre el archivo `styles.css` y agrega los siguientes estilos CSS:

```css
.copyright {
  font-family: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  width: 100%;
  min-height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem;
  box-sizing: inherit;
  position: relative;
}
```

En el fragmento anterior, agregamos los estilos CSS para nuestra directiva de copyright en la hoja de estilos CSS global. Las directivas no tienen un archivo CSS adjunto que podamos usar, como los componentes.

2. Abre el archivo `copyright.directive.ts` e importa la clase `ElementRef` del paquete npm `@angular/core`:

```typescript
import { Directive, ElementRef } from '@angular/core';
```

3. Modifica el constructor de la directiva de la siguiente manera:

```typescript
constructor(el: ElementRef) {
  const currentYear = new Date().getFullYear();
  const targetEl: HTMLElement = el.nativeElement;
  targetEl.classList.add('copyright');
  targetEl.textContent = `Copyright ©${currentYear} All RightsReserved`;
}
```

En el fragmento anterior, usamos la clase `ElementRef` para acceder y manipular el elemento HTML subyacente adjunto a la directiva. La propiedad `nativeElement` contiene el elemento HTML nativo real. También agregamos la clase `copyright` usando el método `add` de la propiedad `classList`. Finalmente, cambiamos el texto del elemento modificando la propiedad `textContent`.

> `ElementRef` es un servicio integrado de Angular. Para usar un servicio en un componente o directiva, debemos inyectarlo en el constructor, como aprenderemos en el *Capítulo 5: Gestión de tareas complejas con Servicios*.

4. Abre el archivo `app.component.ts` e importa la clase `CopyrightDirective`:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';
import { CopyrightDirective } from './copyright.directive';

@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

5. Abre el archivo `app.component.html` y agrega un elemento `<footer>` para mostrar información de derechos de autor:

```html
<main class="main">
  <div class="content">
    <app-product-list></app-product-list>
  </div>
</main>
<footer appCopyright></footer>
<router-outlet />
```

6. Ejecuta la aplicación usando el comando `ng serve` y observa la salida de la aplicación:

*(Figura 4.5: Salida de la aplicación)*

Al crear directivas, es importante considerar una funcionalidad reutilizable que no se relacione necesariamente con una característica en particular. El tema que analizamos fue la información de derechos de autor, pero podríamos crear otras funcionalidades, como información sobre herramientas (*tooltips*) y funciones de desplazamiento infinito o colapsable, con relativa facilidad. En la siguiente sección, crearemos otra directiva de atributo que explora más a fondo las opciones disponibles.

#### Enlace de propiedades y respuesta a eventos
Las directivas de atributo también se ocupan del comportamiento de un elemento HTML. Pueden extender la funcionalidad del elemento y agregar nuevas características. El framework Angular proporciona dos decoradores útiles que podemos usar en nuestras directivas para mejorar la funcionalidad de un elemento HTML:

- **`@HostBinding`:** Vincula un valor a la propiedad del elemento anfitrión nativo.
- **`@HostListener`:** Se vincula a un evento del elemento anfitrión nativo.

> El elemento anfitrión nativo (*host element*) es el elemento donde actúa nuestra directiva.

El elemento HTML nativo `<input>` puede admitir diferentes tipos de entrada, incluidos texto simple, botones de opción y valores numéricos. Cuando usamos este último, la entrada agrega dos flechas en línea, hacia arriba y hacia abajo, para controlar su valor. Es esta característica del elemento de entrada la que hace que parezca incompleto. Si escribimos un carácter no numérico, la entrada aún lo representa.

Crearemos una directiva de atributo que rechace los valores no numéricos ingresados por el teclado:

1. Ejecuta el siguiente comando de Angular CLI para crear una nueva directiva llamada `numeric`:

```bash
ng generate directive numeric
```

2. Abre el archivo `numeric.directive.ts` e importa los dos decoradores que vamos a utilizar:

```typescript
import { Directive, HostBinding, HostListener } from '@angular/core';
```

3. Define una propiedad `currentClass` usando el decorador `@HostBinding` que se vinculará a la propiedad `class` del elemento `<input>`:

```typescript
@HostBinding('class') currentClass = '';
```

4. Define un método `onKeyPress` usando el decorador `@HostListener` que se vinculará al evento nativo `keypress` del elemento `<input>`:

```typescript
@HostListener('keypress', ['$event']) onKeyPress(event: KeyboardEvent) {
  const charCode = event.key.charCodeAt(0);
  if (charCode > 31 && (charCode < 48 || charCode > 57)) {
    this.currentClass = 'invalid';
    event.preventDefault();
  } else {
    this.currentClass = 'valid';
  }
}
```

5. Abre el archivo `styles.css` y agrega los siguientes estilos CSS que se aplicarán cuando un componente use la directiva:

```css
input.valid {
  border: solid green;
}
input.invalid {
  border: solid red;
}
```

El método `onKeyPress` contiene la lógica de cómo funciona nuestra directiva internamente.

Cuando el usuario presiona una tecla dentro de un elemento `<input>`, Angular sabe que debe llamar al método `onKeyPress` porque lo hemos registrado con el decorador `@HostListener`. El decorador `@HostListener` acepta el nombre del evento y una lista de argumentos como parámetros. En nuestro caso, pasamos el nombre del evento `keypress` y el argumento `$event`, respectivamente. `$event` es el objeto actual que desencadenó el evento, que es del tipo `KeyboardEvent` y contiene las pulsaciones de teclas introducidas por el usuario.

Cada vez que el usuario presiona una tecla, la extraemos del objeto `$event`, la convertimos en un carácter Unicode utilizando el método `charCodeAt` y la comparamos con un código no numérico. Si el carácter no es numérico, llamamos al método `preventDefault` del objeto `$event` para cancelar la acción del usuario y revertir el elemento `<input>` a su estado anterior. Al mismo tiempo, establecemos la clase respectiva en `valid` si la tecla es numérica y en `invalid` si no lo es. Podemos aplicar la directiva en una etiqueta `<input>` de la siguiente manera:

```html
<input appNumeric />
```

Veremos un uso real de la directiva en el *Capítulo 10: Recopilación de datos del usuario con Formularios*. Mientras tanto, si deseas probarlo tú mismo, recuerda importar la clase `NumericDirective` en tu componente antes de usarla.

---

### Sección 4.5: Resumen

Ahora que hemos llegado a este punto, es justo decir que has conocido casi todos los artefactos de Angular para crear componentes de Angular, que son de hecho las ruedas y el motor de todas las aplicaciones de Angular. En los próximos capítulos, veremos cómo podemos diseñar mejor la arquitectura de nuestra aplicación, gestionar la inyección de dependencias en todo nuestro árbol de componentes, consumir servicios de datos y aprovechar el nuevo enrutador de Angular para mostrar y ocultar componentes cuando sea necesario.

Ahora, prepárate para asumir nuevos desafíos: en el próximo capítulo, descubriremos cómo usar los servicios de datos para gestionar tareas complejas en nuestros componentes.
