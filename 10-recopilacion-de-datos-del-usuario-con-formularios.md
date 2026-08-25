# Parte 3: Enrutamiento, Datos y Formularios

## Capítulo 10: Recopilación de datos del usuario con Formularios

Las aplicaciones web utilizan formularios para recopilar datos de entrada de los usuarios. Los casos de uso varían: desde permitir a los usuarios iniciar sesión, completar información de pago, reservar un vuelo o incluso realizar una búsqueda. Posteriormente, los datos del formulario se pueden almacenar en el almacenamiento local (*local storage*) o enviarse a un servidor mediante una API backend.

En este capítulo, cubriremos los siguientes temas sobre formularios:

- Introducción a los formularios web
- Creación de formularios basados en plantillas (*template-driven*)
- Creación de formularios reactivos (*reactive forms*)
- Uso de un constructor de formularios (*FormBuilder*)
- Validación de entradas en formularios
- Manipulación del estado del formulario

---

### Sección 10.1: Requisitos técnicos

El capítulo contiene varios ejemplos de código para guiarte a través de la creación y gestión de formularios en Angular. Puedes encontrar el código fuente relacionado en la carpeta `ch10` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch10](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch10)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 10.2: Introducción a los formularios web

Un formulario suele tener las siguientes características que mejoran la experiencia de usuario de una aplicación web:

- Define diferentes tipos de campos de entrada.
- Configura diferentes tipos de validaciones y muestra errores de validación al usuario.
- Admite diferentes estrategias para manejar datos si el formulario se encuentra en un estado de error.

El framework Angular proporciona dos enfoques para manejar formularios: **basados en plantillas** (*template-driven*) y **reactivos** (*reactive*). Ninguno de los dos enfoques se considera mejor que el otro; debes elegir el que mejor se adapte a tu escenario. La principal diferencia entre ambos enfoques radica en cómo gestionan los datos:

- **Formularios basados en plantillas (*Template-driven forms*):** Son fáciles de configurar y agregar a una aplicación Angular. Operan únicamente en la plantilla del componente para crear elementos y configurar reglas de validación; por lo tanto, no son fáciles de probar. También dependen del mecanismo de detección de cambios del framework.
- **Formularios reactivos (*Reactive forms*):** Son más robustos al escalar y realizar pruebas. Operan en la clase del componente para gestionar los controles de entrada y establecer reglas de validación. También manipulan datos mediante un modelo de formulario intermedio, manteniendo su naturaleza inmutable. Esta técnica es para ti si utilizas ampliamente técnicas de programación reactiva o si tu aplicación Angular comprende muchos formularios.

Un formulario en una aplicación web consiste en un elemento HTML `<form>` que contiene elementos HTML para ingresar datos, como los elementos `<input>` y `<select>`, y elementos `<button>` para interactuar con los datos. El formulario puede recuperar y guardar datos localmente o enviarlos a un servidor para su posterior manipulación. El siguiente es un ejemplo de un formulario simple que se utiliza para que un usuario inicie sesión en una aplicación web:

```html
<form>
  <div>
    <input type="text" name="username" placeholder="Username" />
  </div>
  <div>
    <input type="password" name="password" placeholder="Password" />
  </div>
  <button type="submit">Login</button>
</form>
```

El formulario anterior tiene dos elementos `<input>`: uno para ingresar el nombre de usuario y otro para ingresar la contraseña. El tipo del campo de contraseña se establece en `password` para que el contenido del control de entrada no sea visible al escribir. El tipo del elemento `<button>` se establece en `submit` para que el formulario pueda recopilar datos mediante el clic de un usuario en el botón o al presionar Enter en cualquier control de entrada.

> Podríamos agregar otro botón con el tipo `reset` si quisiéramos restablecer los datos del formulario.

Ten en cuenta que un elemento HTML debe residir dentro del elemento `<form>` para ser parte de él. La siguiente captura de pantalla muestra cómo se ve el formulario cuando se representa en una página:

*(Figura 10.1: Formulario de inicio de sesión)*

Las aplicaciones web pueden mejorar significativamente la experiencia del usuario mediante el uso de formularios que proporcionan características como el autocompletado en los controles de entrada o la solicitud al usuario para guardar datos confidenciales. Ahora que hemos entendido cómo se ve un formulario web, aprendamos cómo encaja todo esto en el framework Angular.

---

### Sección 10.3: Creación de formularios basados en plantillas (template-driven)

Los formularios basados en plantillas son una de las dos formas diferentes de integrar formularios con Angular. Estos pueden ser muy potentes en los casos en que queramos crear formularios pequeños y sencillos para nuestra aplicación Angular.

Aprendimos sobre el enlace de datos (*data binding*) en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, y cómo podemos usar diferentes tipos para leer datos de un componente Angular y escribir datos en él. En ese caso, el enlace es en una dirección u otra, lo que se denomina enlace unidireccional (*one-way binding*). En los formularios basados en plantillas, podemos combinar ambos sentidos y crear un enlace bidireccional (*two-way binding*) que puede leer y escribir datos simultáneamente. Los formularios basados en plantillas proporcionan la directiva `ngModel`, que podemos usar en nuestros componentes para obtener este comportamiento. Para obtener más información sobre los formularios basados en plantillas, convertiremos la funcionalidad de cambio de precio de nuestro componente de detalles de producto para que funcione con formularios de Angular.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 9: Navegación a través de aplicaciones con Enrutamiento* para seguir el resto del capítulo.

Comencemos:

1. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { FormsModule } from '@angular/forms';
```

> Agregamos formularios basados en plantillas a una aplicación Angular utilizando la clase `FormsModule` del paquete npm `@angular/forms`.

2. Agrega `FormsModule` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-detail',
  imports: [CommonModule, FormsModule],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

3. Abre el archivo `product-detail.component.html` y modifica el elemento `<input>` de la siguiente manera:

```html
<input placeholder="New price" type="number" name="price" [(ngModel)]="product.price" />
```

En el fragmento anterior, vinculamos la propiedad `price` de la variable de plantilla `product` a la directiva `ngModel` del elemento `<input>`. El atributo `name` es necesario para que Angular pueda crear internamente un control de formulario único para distinguirlo.

> La sintaxis de la directiva `ngModel` se conoce como "banana en una caja" (*banana in a box*), y la creamos en dos pasos. Primero, hacemos la banana rodeando `ngModel` con paréntesis `()`. Luego, la ponemos en una caja rodeándola con corchetes `[()]`.

4. Modifica el elemento `<button>` de la siguiente manera:

```html
<button class="secondary" type="submit">Change</button>
```

En el fragmento anterior, eliminamos el evento `click` del elemento `<button>` porque enviar el formulario actualizará el precio. También agregamos el tipo `submit` para indicar que el envío del formulario puede ocurrir cuando un usuario hace clic en el botón.

5. Rodea los elementos `<input>` y `<button>` con el siguiente elemento `<form>`:

```html
<form (ngSubmit)="changePrice(product)">
  <input placeholder="New price" type="number" name="price" [(ngModel)]="product.price" />
  <button class="secondary" type="submit">Change</button>
</form>
```

En el fragmento anterior, vinculamos el método `changePrice` al evento `ngSubmit` del formulario. El enlace activará la ejecución del método si presionamos Enter dentro del cuadro de entrada o hacemos clic en el botón. El evento `ngSubmit` es parte del `FormsModule` de Angular y se conecta al evento nativo `submit` de un formulario HTML.

6. Abre el archivo `product-detail.component.ts` y modifica el método `changePrice` de la siguiente manera:

```typescript
changePrice(product: Product) {
  this.productService.updateProduct(
    product.id,
    product.price
  ).subscribe(() => this.router.navigate(['/products']));
}
```

7. Ejecuta la aplicación usando el comando `ng serve` y selecciona un producto de la lista.

Notarás que el precio actual del producto ya se muestra dentro del cuadro de entrada. Intenta cambiar el precio y notarás que el precio actual del producto también cambia mientras escribes:

*(Figura 10.2: Enlace bidireccional)*

El comportamiento de nuestra aplicación representado en la imagen anterior es la magia detrás del enlace bidireccional y `ngModel`.

> El enlace bidireccional fue el mayor atractivo de venta cuando se lanzó AngularJS en 2010. Era complejo lograr ese comportamiento en aquellos días con JavaScript puro y jQuery.

Mientras escribimos dentro del cuadro de entrada, la directiva `ngModel` actualiza el valor del precio del producto. El nuevo precio se refleja directamente en la plantilla porque usamos la sintaxis de interpolación de Angular para mostrar su valor.

En nuestro caso, actualizar el precio actual del producto mientras se ingresa uno nuevo es una mala experiencia de usuario. El usuario debe poder ver el precio actual del producto en todo momento. Modificaremos el componente de detalles del producto para que el precio se muestre correctamente:

8. Abre el archivo `product-detail.component.ts` y crea una propiedad `price` dentro de la clase `ProductDetailComponent`:

```typescript
price: number | undefined;
```

9. Modifica el método `changePrice` para usar la propiedad de componente `price`:

```typescript
changePrice(product: Product) {
  this.productService.updateProduct(
    product.id,
    this.price!
  ).subscribe(() => this.router.navigate(['/products']));
}
```

10. Abre el archivo `product-detail.component.html` y reemplaza el enlace en el elemento `<input>` para usar la nueva propiedad del componente:

```html
<input placeholder="New price" type="number" name="price" [(ngModel)]="price" />
```

Si ejecutamos la aplicación e intentamos ingresar un nuevo precio dentro del cuadro de entrada *New price*, notaremos que el precio actual mostrado no cambia. La funcionalidad de cambiar el precio también funciona correctamente como antes.

Hemos visto cómo los formularios basados en plantillas pueden ser útiles al crear formularios pequeños y simples. En la siguiente sección, profundizaremos en el enfoque alternativo que ofrece el framework Angular: los formularios reactivos.

---

### Sección 10.4: Creación de formularios reactivos (reactive forms)

Los formularios reactivos, como su nombre indica, proporcionan acceso a los formularios web de forma reactiva. Se construyen teniendo en cuenta la reactividad, donde los controles de entrada y sus valores se pueden manipular mediante flujos de observables. También mantienen un estado inmutable de los datos del formulario, lo que facilita las pruebas porque podemos estar seguros de que el estado del formulario se puede modificar de manera explícita y coherente.

Los formularios reactivos tienen un enfoque programático para crear elementos de formulario y configurar reglas de validación estableciendo todo en la clase del componente. Las clases clave de Angular involucradas en este enfoque son las siguientes:

- **`FormControl`:** Representa un control de formulario individual, como un elemento `<input>`.
- **`FormGroup`:** Representa una colección de controles de formulario. El elemento `<form>` es el `FormGroup` superior en la jerarquía de un formulario reactivo.
- **`FormArray`:** Representa una colección de controles de formulario, al igual que `FormGroup`, pero se puede modificar en tiempo de ejecución. Por ejemplo, podemos agregar o eliminar objetos `FormControl` dinámicamente según sea necesario.

Las clases anteriores están disponibles en el paquete npm `@angular/forms` y contienen propiedades que se pueden usar en los siguientes escenarios:

- Para renderizar la UI de manera diferente según el estado de un formulario o control.
- Para verificar si hemos interactuado con un formulario o control.

Exploraremos cada clase de formulario a través de un ejemplo en nuestra aplicación Angular. En la siguiente sección, introduciremos formularios reactivos en nuestra aplicación utilizando el componente de creación de productos.

#### Interacción con formularios reactivos
La aplicación Angular que hemos creado contiene un componente para agregar nuevos productos. El componente utiliza variables de referencia de plantilla para recopilar datos de entrada. Usaremos la API de formularios de Angular para realizar la misma tarea utilizando formularios reactivos:

1. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { FormControl, FormGroup, ReactiveFormsModule } from '@angular/forms';
```

2. Agrega la clase `ReactiveFormsModule` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-create',
  imports: [ReactiveFormsModule],
  templateUrl: './product-create.component.html',
  styleUrl: './product-create.component.css'
})
```

> La librería de formularios de Angular proporciona la clase `ReactiveFormsModule` para crear formularios reactivos en una aplicación Angular.

3. Define la siguiente propiedad `productForm` en la clase `ProductCreateComponent`:

```typescript
productForm = new FormGroup({
  title: new FormControl('', { nonNullable: true }),
  price: new FormControl<number | undefined>(undefined, { nonNullable: true }),
  category: new FormControl('', { nonNullable: true })
});
```

El constructor de `FormGroup` acepta un objeto que contiene pares clave-valor de controles de formulario. La clave es un nombre de control único y el valor es una instancia de `FormControl`. El constructor de `FormControl` acepta el valor predeterminado del control en el primer parámetro. Para los controles `title` y `category`, pasamos una cadena vacía para no establecer ningún valor inicialmente. Para el control `price`, que debe aceptar números como valores, lo establecemos inicialmente en `undefined`. El segundo parámetro pasado en `FormControl` es un objeto que establece la propiedad `nonNullable` para indicar que el control no acepta valores nulos.

Después de haber creado el grupo de formularios y sus controles, debemos asociarlos con los elementos HTML respectivos en la plantilla:

4. Abre el archivo `product-create.component.html` y rodea los elementos HTML `<input>`, `<select>` y `<button>` con el siguiente elemento `<form>`:

```html
<form [formGroup]="productForm">
  <div>
    <label for="title">Title</label>
    <input id="title" #title />
  </div>
  <div>
    <label for="price">Price</label>
    <input id="price" #price type="number" />
  </div>
  <div>
    <label for="category">Category</label>
    <select id="category" #category>
      <option>Select a category</option>
      <option value="electronics">Electronics</option>
      <option value="jewelery">Jewelery</option>
      <option>Other</option>
    </select>
  </div>
  <div>
    <button (click)="createProduct(title.value, price.value, category.value)">Create</button>
  </div>
</form>
```

En la plantilla anterior, usamos la directiva `formGroup`, exportada de la clase `ReactiveFormsModule`, para conectar una instancia de `FormGroup` a un elemento `<form>`.

5. La clase `ReactiveFormsModule` también exporta la directiva `formControlName`, que usamos para conectar una instancia de `FormControl` a un elemento HTML. Modifica los elementos HTML del formulario de la siguiente manera:

```html
<div>
  <label for="title">Title</label>
  <input id="title" formControlName="title" />
</div>
<div>
  <label for="price">Price</label>
  <input id="price" formControlName="price" type="number" />
</div>
<div>
  <label for="category">Category</label>
  <select id="category" formControlName="category">
    <option>Select a category</option>
    <option value="electronics">Electronics</option>
    <option value="jewelery">Jewelery</option>
    <option>Other</option>
  </select>
</div>
```

En el fragmento anterior, establecemos el valor de la directiva `formControlName` con el nombre de la instancia respectiva de `FormControl`. También eliminamos las variables de referencia de plantilla porque podemos obtener sus valores directamente de la instancia de `FormGroup`.

6. Modifica el método `createProduct` en el archivo `product-create.component.ts` en consecuencia:

```typescript
createProduct() {
  this.productsService.addProduct(this.productForm.value).subscribe(() => {
    this.router.navigate(['/products']);
  });
}
```

En el método anterior, usamos la propiedad `value` de la clase `FormGroup` para obtener el valor del formulario.

> Ten en cuenta que la propiedad `value` no incluye valores de campos deshabilitados de un formulario. En su lugar, podemos usar el método `getRawValue` para devolver valores de todos los campos.

En este caso, podemos usar el valor del formulario porque el modelo del formulario es idéntico a la interfaz `Product`. Si fuera diferente, podríamos usar la propiedad `controls` de la clase `FormGroup` para obtener los valores de control del formulario individualmente de la siguiente manera:

```typescript
createProduct() {
  this.productsService.addProduct({
    title: this.productForm.controls.title.value,
    price: this.productForm.controls.price.value,
    category: this.productForm.controls.category.value
  }).subscribe(() => {
    this.router.navigate(['/products']);
  });
}
```

> La clase `FormControl` contiene una propiedad `value` que devuelve el valor de un control de formulario.

7. Modifica el elemento `<form>` en el archivo `product-create.component.html` para que creemos un nuevo producto al enviar el formulario:

```html
<form [formGroup]="productForm" (ngSubmit)="createProduct()">
  <div>
    <label for="title">Title</label>
    <input id="title" formControlName="title" />
  </div>
  <div>
    <label for="price">Price</label>
    <input id="price" formControlName="price" type="number" />
  </div>
  <div>
    <label for="category">Category</label>
    <select id="category" formControlName="category">
      <option>Select a category</option>
      <option value="electronics">Electronics</option>
      <option value="jewelery">Jewelery</option>
      <option>Other</option>
    </select>
  </div>
  <div>
    <button type="submit">Create</button>
  </div>
</form>
```

8. Abre el archivo global `styles.css` y agrega el siguiente estilo CSS:

```css
label {
  margin-bottom: 4px;
  display: block;
}
```

Queremos que los estilos anteriores estén disponibles globalmente porque los usaremos en el componente del carrito más adelante en el capítulo.

9. Abre el archivo `product-create.component.css` y elimina el estilo para la etiqueta `<label>`.

Si ejecutamos la aplicación, veremos que la funcionalidad de agregar un nuevo producto sigue funcionando como se esperaba.

Aprendimos que la clase `FormGroup` agrupa una colección de controles de formulario. Un control de formulario puede ser un único control de formulario u otro grupo de formularios, como veremos en la siguiente sección.

#### Creación de jerarquías de formularios anidados
El componente de creación de productos consta de un solo grupo de formularios con tres controles de formulario. Algunos casos de uso en aplicaciones empresariales requieren formularios más avanzados que implican la creación de jerarquías anidadas de grupos de formularios. Considera el siguiente formulario, que se utiliza para agregar un nuevo producto junto con detalles adicionales:

*(Figura 10.3: Formulario de nuevo producto con información adicional)*

El formulario anterior puede parecer un solo grupo de formularios, pero si miramos más a fondo en la clase del componente, veremos que `productForm` consta de dos instancias de `FormGroup`, una anidada dentro de la otra:

```typescript
productForm = new FormGroup({
  title: new FormControl('', { nonNullable: true }),
  price: new FormControl<number | undefined>(undefined, { nonNullable: true }),
  category: new FormControl('', { nonNullable: true }),
  extra: new FormGroup({
    image: new FormControl(''),
    description: new FormControl('')
  })
});
```

La propiedad `productForm` es el grupo de formularios principal, mientras que `extra` es su elemento secundario. Un grupo de formularios principal puede tener tantos grupos de formularios secundarios como necesite. Si echamos un vistazo a la plantilla del componente, veremos que el grupo de formularios secundario se define de manera diferente al principal:

```html
<form [formGroup]="productForm" (ngSubmit)="createProduct()">
  <div>
    <label for="title">Title</label>
    <input id="title" formControlName="title" />
  </div>
  <div>
    <label for="price">Price</label>
    <input id="price" formControlName="price" type="number" />
  </div>
  <div>
    <label for="category">Category</label>
    <select id="category" formControlName="category">
      <option>Select a category</option>
      <option value="electronics">Electronics</option>
      <option value="jewelery">Jewelery</option>
      <option>Other</option>
    </select>
  </div>
  <h2>Additional details</h2>
  <form formGroupName="extra">
    <div>
      <label for="descr">Description</label>
      <input id="descr" formControlName="description" />
    </div>
    <div>
      <label for="photo">Photo URL</label>
      <input id="photo" formControlName="image" />
    </div>
  </form>
  <div>
    <button type="submit">Create</button>
  </div>
</form>
```

En la plantilla HTML anterior, usamos la directiva `formGroupName` para vincular el elemento de formulario interno a la propiedad `extra`.

> Es posible que hayas esperado vincularlo directamente a la propiedad `productForm.extra`, pero Angular es lo suficientemente inteligente porque comprende que `extra` es un grupo de formularios secundario de `productForm`. Puede deducir esta información porque el elemento de formulario relacionado con `extra` está dentro del elemento de formulario que se vincula a la propiedad `productForm`.

El valor de un grupo de formularios secundario se comparte con su principal en una jerarquía de formularios anidados. En nuestro caso, el valor del grupo de formularios `extra` se incluirá en el grupo `productForm`, manteniendo así un modelo de formulario coherente.

Ya hemos cubierto las clases `FormGroup` y `FormControl`. En la siguiente sección, aprenderemos a utilizar la clase `FormArray` para interactuar con formularios dinámicos.

#### Modificación dinámica de formularios
Considera el escenario en el que hemos agregado algunos productos al carrito de compras de nuestra aplicación de tienda online y deseamos actualizar sus cantidades antes de finalizar la compra.

Actualmente, nuestra aplicación no tiene ninguna funcionalidad para un carrito de compras, por lo que ahora agregaremos una:

1. Ejecuta el siguiente comando para crear una interfaz `Cart`:

```bash
ng generate interface Cart
```

2. Abre el archivo `cart.ts` y modifica la interfaz `Cart` de la siguiente manera:

```typescript
export interface Cart {
  id: number;
  products: { productId :number }[];
}
```

En el fragmento anterior, la propiedad `products` contendrá los ID de los productos que pertenecen al carrito actual.

3. Crea un nuevo servicio para administrar el carrito de compras ejecutando el siguiente comando de Angular CLI:

```bash
ng generate service cart
```

4. Abre el archivo `cart.service.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, defer, map } from 'rxjs';
import { Cart } from './cart';
import { APP_SETTINGS } from './app.settings';
```

5. Crea las siguientes propiedades en la clase `CartService`:

```typescript
cart: Cart | undefined;
private cartUrl = inject(APP_SETTINGS).apiUrl + '/carts';
```

La propiedad `cartUrl` se utiliza para el punto de conexión del carrito de la Fake Store API y la propiedad `cart` para mantener una caché local del carrito del usuario.

6. Inyecta el servicio `HttpClient` en el constructor:

```typescript
constructor(private http: HttpClient) { }
```

7. Agrega el siguiente método para agregar un producto al carrito:

```typescript
addProduct(id: number): Observable<Cart> {
  const cartProduct = { productId: id, quantity: 1 };
  return defer(() => !this.cart ?
    this.http.post<Cart>(this.cartUrl, { products: [cartProduct] }) :
    this.http.put<Cart>(`${this.cartUrl}/${this.cart.id}`, {
      products: [
        ...this.cart.products,
        cartProduct
      ]
    })
  ).pipe(map(cart => this.cart = cart));
}
```

En el método anterior, usamos un nuevo operador de RxJS llamado `defer`. El operador `defer` funciona como una sentencia `if/else` para observables.

Si la propiedad `cart` no se ha inicializado, lo que significa que nuestro carrito está actualmente vacío, iniciamos una solicitud POST a la API pasando la variable `cartProduct` como parámetro. De lo contrario, iniciamos una solicitud PUT pasando `cartProduct` junto con los productos existentes del carrito.

Hemos completado la configuración de nuestro servicio para que pueda comunicarse con la Fake Store API. Ahora, necesitamos conectar el servicio con el componente respectivo:

8. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { CartService } from '../cart.service';
```

9. Inyecta `CartService` en la clase `ProductDetailComponent`:

```typescript
constructor(
  private productService: ProductsService,
  public authService: AuthService,
  private route: ActivatedRoute,
  private router: Router,
  private cartService: CartService
) { }
```

10. Modifica el método `addToCart` para que llame al método `addProduct` de la clase `CartService`:

```typescript
addToCart(id: number) {
  this.cartService.addProduct(id).subscribe();
}
```

11. Finalmente, abre el archivo `product-detail.component.html` y modifica el evento de clic del botón *Add to cart*:

```html
<button (click)="addToCart(product.id)">Add to cart</button>
```

Hemos implementado la funcionalidad básica para almacenar los productos seleccionados que los usuarios desean comprar. Ahora, debemos modificar el componente del carrito para mostrar los artículos del carrito:

12. Abre el archivo `cart.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Component, OnInit } from '@angular/core';
import { FormArray, FormControl, FormGroup, ReactiveFormsModule } from '@angular/forms';
import { Product } from '../product';
import { CartService } from '../cart.service';
import { ProductsService } from '../products.service';
```

13. Agrega la clase `ReactiveFormsModule` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-cart',
  imports: [ReactiveFormsModule],
  templateUrl: './cart.component.html',
  styleUrl: './cart.component.css'
})
```

14. Agrega la interfaz `OnInit` a la lista de interfaces implementadas de la clase `CartComponent`:

```typescript
export class CartComponent implements OnInit
```

15. Crea las siguientes propiedades en la clase TypeScript:

```typescript
cartForm = new FormGroup({
  products: new FormArray<FormControl<number>>([])
});
products: Product[] = [];
```

En el fragmento anterior, creamos un objeto `FormGroup` que contiene una propiedad `products`. Establecemos el valor de la propiedad `products` en una instancia de la clase `FormArray`. El constructor de la clase `FormArray` acepta una lista de instancias de `FormControl` con el tipo `number` como parámetro. La lista está vacía por ahora ya que el carrito no tiene productos. La propiedad `products` fuera de la instancia de `FormGroup` se utilizará por razones de búsqueda para mostrar el título de cada producto en el carrito.

16. Agrega un constructor para inyectar los siguientes servicios:

```typescript
constructor(
  private cartService: CartService,
  private productsService: ProductsService
) {}
```

17. Crea el siguiente método para obtener productos del carrito:

```typescript
private getProducts() {
  this.productsService.getProducts().subscribe(products => {
    this.cartService.cart?.products.forEach(item => {
      const product = products.find(p => p.id === item.productId);
      if (product) {
        this.products.push(product);
      }
    });
  });
}
```

En el método anterior, inicialmente nos suscribimos al método `getProducts` de la clase `ProductsService` para obtener los productos disponibles. Luego, para cada producto en el carrito, extraemos la propiedad `productId` y verificamos si existe dentro del carrito. Si se encuentra el producto, lo agregamos a la propiedad de componente `products`.

18. Crea otro método para construir nuestro formulario:

```typescript
private buildForm() {
  this.products.forEach(() => {
    this.cartForm.controls.products.push(
      new FormControl(1, { nonNullable: true })
    );
  });
}
```

En el método anterior, iteramos sobre la propiedad `products` y agregamos una instancia de `FormControl` para cada uno dentro del array de formularios `products`. Establecemos el valor de cada control de formulario en 1 para indicar que el carrito contiene un artículo de cada producto de forma predeterminada.

19. Crea el siguiente método `ngOnInit` que combine ambos métodos de los pasos anteriores:

```typescript
ngOnInit(): void {
  this.getProducts();
  this.buildForm();
}
```

20. Abre el archivo `cart.component.html` y reemplaza su plantilla HTML con el siguiente contenido:

```html
<div [formGroup]="cartForm">
  <div formArrayName="products">
    @for(product of cartForm.controls.products.controls; track $index) {
      <label>{{products[$index].title}}</label>
      <input [formControlName]="$index" type="number" />
    }
  </div>
</div>
```

En la plantilla anterior, usamos un bloque `@for` para iterar sobre la propiedad `controls` del array de formularios `products` y crear un elemento `<input>` para cada uno. Usamos la palabra clave `$index` del bloque `@for` para dar un nombre creado dinámicamente a cada control de formulario mediante el enlace `formControlName`. También hemos agregado una etiqueta `<label>` que muestra el título del producto de la propiedad de componente `products`. El título del producto se obtiene utilizando el `$index` del producto actual en el array.

21. Finalmente, abre el archivo `cart.component.css` y agrega los siguientes estilos CSS:

```css
:host {
  width: 500px;
}

input {
  width: 50px;
}
```

Para ver el componente del carrito en acción, ejecuta la aplicación usando el comando `ng serve` y agrega algunos productos al carrito.

> No olvides iniciar sesión primero porque la funcionalidad que agrega un producto al carrito solo está disponible para usuarios autenticados.

Después de agregar algunos productos al carrito, haz clic en el enlace *My Cart* para ver tu carrito de compras. Debería verse de la siguiente manera:

*(Figura 10.4: Carrito de compras)*

Dado que hemos establecido la lógica de negocio para administrar un carrito de compras, también podemos actualizar el guard de checkout que creamos en el capítulo anterior:

22. Abre el archivo `checkout.guard.ts` y agrega las siguientes sentencias de importación:

```typescript
import { inject } from '@angular/core';
import { CartService } from './cart.service';
```

23. Inyecta la clase `CartService` en la función `checkoutGuard` mediante la siguiente sentencia:

```typescript
const cartService = inject(CartService);
```

24. Modifica el cuerpo restante de la función flecha `checkoutGuard` para que mostremos el cuadro de diálogo de confirmación solo cuando el carrito no esté vacío:

```typescript
if (cartService.cart) {
  const confirmation = confirm(
    'You have pending items in your cart. Do you want to continue?'
  );
  return confirmation;
}
return true;
```

Con `FormArray`, hemos completado nuestra exploración de los componentes más básicos de un formulario de Angular. Aprendimos a utilizar las clases de formularios de Angular para crear formularios web estructurados y recopilar la entrada del usuario. En la siguiente sección, aprenderemos a crear formularios de Angular utilizando el servicio `FormBuilder`.

#### Uso de un constructor de formularios (FormBuilder)
El uso de clases de formularios para crear formularios de Angular puede volverse repetitivo y tedioso para escenarios complejos. El framework Angular proporciona `FormBuilder`, un servicio integrado para formularios de Angular que contiene métodos auxiliares para construir formularios. Veamos cómo podríamos usarlo para construir un formulario para crear nuevos productos:

1. Abre el archivo `product-create.component.ts` e importa los artefactos `OnInit` y `FormBuilder`:

```typescript
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup, ReactiveFormsModule, FormBuilder } from '@angular/forms';
```

2. Agrega `OnInit` a la lista de interfaces implementadas en la clase `ProductCreateComponent`:

```typescript
export class ProductCreateComponent implements OnInit
```

3. Inyecta la clase `FormBuilder` en el constructor:

```typescript
constructor(
  private productsService: ProductsService,
  private router: Router,
  private builder: FormBuilder
) {}
```

4. Modifica la propiedad `productForm` de la siguiente manera:

```typescript
productForm: FormGroup<{
  title: FormControl<string>,
  price: FormControl<number | undefined>,
  category: FormControl<string>
}> | undefined;
```

En el fragmento anterior, definimos solo la estructura del formulario porque ahora se creará utilizando el servicio `FormBuilder`.

5. Crea el siguiente método para construir el formulario:

```typescript
private buildForm() {
  this.productForm = this.builder.nonNullable.group({
    title: [''],
    price: this.builder.nonNullable.control<number | undefined>(undefined),
    category: ['']
  });
}
```

En el método anterior, usamos la propiedad `nonNullable` de la clase `FormBuilder` para crear un grupo de formularios que no puede ser nulo. El método `group` se utiliza para agrupar controles de formulario. Los controles de formulario `title` y `category` se crean utilizando una cadena vacía como valor predeterminado. El control de formulario `price` sigue un enfoque diferente al resto porque no podemos asignar un valor predeterminado de `undefined` debido a las limitaciones del lenguaje TypeScript. En este caso, usamos el método `control` de la propiedad `nonNullable` para definir el control de formulario.

6. Agrega el ciclo de vida `ngOnInit` para ejecutar el método `buildForm`:

```typescript
ngOnInit(): void {
  this.buildForm();
}
```

7. Agrega el operador de aserción no nula (`!`) al acceder a la propiedad `productForm` en el método `createProduct`:

```typescript
createProduct() {
  this.productsService.addProduct(this.productForm!.value).subscribe(() => {
    this.router.navigate(['/products']);
  });
}
```

8. Abre el archivo `product-create.component.html` y agrega el operador de aserción no nula en el elemento HTML `<form>` también:

```html
<form [formGroup]="productForm!" (ngSubmit)="createProduct()">
  <div>
    <label for="title">Title</label>
    <input id="title" formControlName="title" />
  </div>
  <div>
    <label for="price">Price</label>
    <input id="price" formControlName="price" type="number" />
  </div>
  <div>
    <label for="category">Category</label>
    <select id="category" formControlName="category">
      <option>Select a category</option>
      <option value="electronics">Electronics</option>
      <option value="jewelery">Jewelery</option>
      <option>Other</option>
    </select>
  </div>
  <div>
    <button type="submit">Create</button>
  </div>
</form>
```

> Al usar el servicio `FormBuilder` para crear formularios de Angular, no tenemos que lidiar explícitamente con los tipos de datos `FormGroup` y `FormControl`, aunque eso es lo que se crea entre bastidores.

Ejecuta la aplicación usando el comando `ng serve` y verifica que el proceso de creación de nuevos productos funcione correctamente. Intenta hacer clic en el botón *Create* sin ingresar ningún valor en los controles del formulario y observa qué sucede en la lista de productos. La aplicación crea un producto con un título vacío. Es una situación que debemos evitar en un escenario del mundo real. Debemos ser conscientes del estado de un control de formulario y tomar medidas en consecuencia.

> El código de ejemplo en el resto del capítulo no utiliza el servicio `FormBuilder` cuando trabaja con formularios reactivos.

En la siguiente sección, investigaremos diferentes propiedades que podemos verificar para obtener el estado del formulario y proporcionar comentarios al usuario.

---

### Sección 10.5: Validación de entradas en formularios

Un formulario de Angular debe validar la entrada y proporcionar retroalimentación visual para mejorar la UX y guiar a los usuarios a completar el formulario con éxito. Investigaremos las siguientes formas de validar formularios en aplicaciones Angular:

- Validación global con CSS
- Validación en la clase del componente
- Validación en la plantilla del componente
- Creación de validadores personalizados

En la siguiente sección, aprenderemos a aplicar reglas de validación globalmente en una aplicación Angular utilizando estilos CSS.

#### Validación global con CSS
El framework Angular establece automáticamente las siguientes clases CSS en un formulario (basado en plantillas o reactivo) que podemos usar para proporcionar retroalimentación al usuario:

- **`ng-untouched`:** Indica que aún no hemos interactuado con un formulario.
- **`ng-touched`:** Indica que hemos interactuado con un formulario.
- **`ng-dirty`:** Indica que hemos establecido un valor en un formulario.
- **`ng-pristine`:** Indica que aún no hemos modificado un formulario.

Además, Angular agrega las siguientes clases en el elemento HTML de un control de formulario:

- **`ng-valid`:** Indica que el valor de un formulario es válido.
- **`ng-invalid`:** Indica que el valor de un formulario no es válido.

Angular establece las clases CSS anteriores en el formulario y sus controles según su estado. El estado del formulario se evalúa según el estado de sus controles. Por ejemplo, si al menos un control de formulario no es válido, Angular establecerá la clase CSS `ng-invalid` en el formulario y en el control correspondiente.

> En el caso de jerarquías de formularios anidados, el estado de un grupo de formularios secundario se propaga hacia arriba en la jerarquía y se comparte con su formulario principal.

Podemos usar las clases CSS integradas y dar estilo a los formularios de Angular usando solo CSS. Por ejemplo, para mostrar un borde resaltado en azul claro en un control de entrada cuando se interactúa con ese control por primera vez, debemos agregar el siguiente estilo:

```css
input.ng-touched {
  border: 3px solid lightblue;
}
```

También podemos combinar clases CSS según las necesidades de nuestra aplicación:

1. Abre el archivo global `styles.css` y modifica el estilo `input.valid` de la siguiente manera:

```css
input.valid, input.ng-dirty.ng-valid {
  border: solid green;
}
```

El estilo anterior mostrará un borde verde cuando un control de entrada tenga un valor válido ingresado por el usuario.

2. Modifica el estilo `input.invalid` en consecuencia:

```css
input.invalid, input.ng-dirty.ng-invalid {
  border: solid red;
}
```

El estilo anterior mostrará un borde rojo cuando un control de entrada tenga un valor no válido ingresado por el usuario.

3. Abre el archivo `product-create.component.html` y agrega el atributo `required` en los controles de formulario `<input>`:

```html
<div>
  <label for="title">Title</label>
  <input id="title" formControlName="title" required />
</div>
<div>
  <label for="price">Price</label>
  <input id="price" formControlName="price" type="number" required />
</div>
```

4. Ejecuta la aplicación usando el comando `ng serve` y navega a `http://localhost:4200/products/new`.
5. Ingresa algún texto en el campo *Title* y haz clic fuera del control de entrada. Observa que tiene un borde verde.
6. Elimina el texto del campo *Title* y haz clic fuera del control de entrada. El borde ahora debería ponerse rojo.

Aprendimos cómo definir reglas de validación en la plantilla utilizando estilos CSS. En la siguiente sección, aprenderemos cómo definirlas en formularios basados en plantillas y brindar retroalimentación visual mediante mensajes apropiados.

#### Validación en formularios basados en plantillas
En la sección anterior, aprendimos que Angular agrega una colección de clases CSS integradas al validar formularios de Angular. Cada clase tiene una propiedad booleana correspondiente en el modelo de formulario respectivo, tanto en formularios basados en plantillas como en reactivos:

- **`untouched`:** Indica que aún no hemos interactuado con un formulario.
- **`touched`:** Indica que hemos interactuado con un formulario.
- **`dirty`:** Indica que hemos establecido un valor en un formulario.
- **`pristine`:** Indica que aún no hemos modificado un formulario.
- **`valid`:** Indica que el valor de un formulario es válido.
- **`invalid`:** Indica que el valor de un formulario no es válido.

Podemos aprovechar las clases anteriores e informar al usuario sobre el estado actual del formulario. Primero, investiguemos el comportamiento del proceso de cambio de precio en el componente de detalles del producto:

1. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200`.
2. Selecciona un producto de la lista.
3. Agrega un valor de 0 en el cuadro de entrada *New price* y haz clic en el botón *Change*.
4. Selecciona el mismo producto de la lista y observa el resultado:

*(Figura 10.5: Detalles del producto)*

La lógica de presentación del componente no detecta que el usuario puede ingresar 0 para el precio del producto. Un producto siempre debe tener un precio.

El componente de detalles del producto necesita validar la entrada del valor del precio y, si se determina que la entrada no es válida, deshabilitar el botón *Change* y mostrar un mensaje informativo al usuario.

> El manejo de la validación es una cuestión de preferencia personal o especificación de la lógica de negocio. En este escenario, decidimos mostrar un enfoque de validación común deshabilitando el botón y mostrando un mensaje apropiado.

La validación basada en plantillas se realiza en la plantilla del componente. Abre el archivo `product-detail.component.html` y ejecuta los siguientes pasos:

1. Crea la variable de referencia de plantilla `priceCtrl` y vincúlala a la propiedad `ngModel`:

```html
<input placeholder="New price" type="number" name="price" #priceCtrl="ngModel" [(ngModel)]="price" />
```

> La propiedad `ngModel` nos da acceso al modelo de control de formulario subyacente.

2. Agrega los atributos de validación `required` y `min` al elemento HTML:

```html
<input placeholder="New price" type="number" name="price" required min="1" #priceCtrl="ngModel" [(ngModel)]="price" />
```

> El atributo de validación `min` se puede usar solo con elementos HTML `<input>` de tipo numérico. Se utiliza para definir el valor mínimo al usar las flechas del control numérico.

3. Agrega el siguiente elemento HTML `<span>` debajo del elemento `<button>` del formulario:

```html
@if (priceCtrl.dirty && (priceCtrl.invalid || priceCtrl.hasError('min'))) {
  <span class="help-text">Please enter a valid price</span>
}
```

El elemento HTML anterior se mostrará cuando ingresemos un valor de precio y luego lo dejemos en blanco o ingresemos un cero. Usamos el método `hasError` del modelo de control de formulario para verificar si la validación `min` arroja un error.

> Todos los atributos de validación se pueden verificar mediante el método `hasError`. El estado de validez de un control se evalúa en función del estado de todos los atributos de validación que adjuntamos al elemento HTML.

4. Agrega una variable de referencia de plantilla `priceForm` en el elemento HTML `<form>` y vincúlala a la propiedad `ngForm`:

```html
<form (ngSubmit)="changePrice(product)" #priceForm="ngForm">
  <input placeholder="New price" type="number" name="price" required min="1" #priceCtrl="ngModel" [(ngModel)]="price" />
  <button class="secondary" type="submit">Change</button>
  @if (priceCtrl.dirty && (priceCtrl.invalid || priceCtrl.hasError('min'))) {
    <span class="help-text">Please enter a valid price</span>
  }
</form>
```

> La propiedad `ngForm` nos da acceso al modelo de formulario subyacente.

5. Vincula la propiedad `disabled` del elemento HTML `<button>` al estado `invalid` del modelo de formulario:

```html
<button class="secondary" type="submit" [disabled]="priceForm.invalid">
  Change
</button>
```

En la plantilla anterior, podríamos vincular directamente al estado `priceCtrl.invalid` ya que el formulario tiene un solo control. Elegimos el formulario para fines de demostración.

6. Abre el archivo `styles.css` y agrega los siguientes estilos CSS para la etiqueta `<span>` y el botón deshabilitado:

```css
.help-text {
  display: flex;
  color: var(--hot-red);
  font-size: 0.875rem;
}

button:disabled {
  background-color: lightgrey;
  cursor: not-allowed;
}
```

Para verificar que la validación funciona según lo previsto, ejecuta los siguientes pasos:

1. Ejecuta el comando `ng serve` para iniciar la aplicación y selecciona un producto de la lista.
2. Ingresa 0 en el cuadro de entrada *New price* y observa la salida:

*(Figura 10.6: Error de validación)*

3. Ingresa un valor válido y verifica que el mensaje de error haya desaparecido y que el botón *Change* esté habilitado.
4. Deja el cuadro de entrada *New price* en blanco y verifica que el mensaje de error se muestre nuevamente y que el botón *Change* esté deshabilitado.

Ahora que hemos aprendido cómo lograr la validación en formularios basados en plantillas, veamos cómo validar datos de entrada en formularios reactivos.

#### Validación en formularios reactivos
Los formularios basados en plantillas dependen únicamente de la plantilla del componente para realizar validaciones. En los formularios reactivos, la fuente de la verdad es nuestro modelo de formulario que reside en la clase TypeScript del componente. Definimos reglas de validación en formularios reactivos al construir la instancia de `FormGroup` mediante programación.

Para demostrar la validación en formularios reactivos, agregaremos reglas de validación en el componente de creación de productos:

1. Abre el archivo `product-create.component.ts` e importa la clase `Validators` del paquete npm `@angular/forms`:

```typescript
import { FormControl, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
```

2. Modifica la declaración de la propiedad `productForm` para que los controles de formulario `title` y `price` pasen una propiedad `validators` en la instancia de `FormControl`:

```typescript
productForm = new FormGroup({
  title: new FormControl('', {
    nonNullable: true,
    validators: Validators.required
  }),
  price: new FormControl<number | undefined>(undefined, {
    nonNullable: true,
    validators: [Validators.required, Validators.min(1)]
  }),
  category: new FormControl('', { nonNullable: true })
});
```

La clase `Validators` contiene un campo estático para cada regla de validación disponible. Contiene casi las mismas reglas de validación que están disponibles para los formularios basados en plantillas. Podemos combinar múltiples validadores agregándolos a un array, como lo indica la propiedad `validators` en el control de formulario `price`.

> Cuando agregamos un validador mediante la clase `FormControl`, podemos eliminar el atributo HTML respectivo de la plantilla HTML. Sin embargo, se recomienda mantenerlo para fines de accesibilidad, de modo que las aplicaciones de lectura de pantalla puedan utilizarlo.

3. Abre el archivo `product-create.component.html` y usa la propiedad `invalid` de la propiedad `productForm` para deshabilitar el botón *Create*:

```html
<button type="submit" [disabled]="productForm.invalid">Create</button>
```

4. Agrega un elemento HTML `<span>` en cada control de formulario `<input>` para mostrar un mensaje de error cuando se haya interactuado con el control y la validación requerida arroje un error:

```html
<div>
  <label for="title">Title</label>
  <input id="title" formControlName="title" required />
  @if (productForm.controls.title.touched && productForm.controls.title.invalid) {
    <span class="help-text">Title is required</span>
  }
</div>
<div>
  <label for="price">Price</label>
  <input id="price" formControlName="price" type="number" required />
  @if (productForm.controls.price.touched && productForm.controls.price.invalid) {
    <span class="help-text">Price is required</span>
  }
</div>
```

En el fragmento anterior, usamos la propiedad `controls` de la propiedad `productForm` para obtener acceso a los modelos de control de formulario individuales y obtener sus estados.

Sería conveniente mostrar diferentes mensajes según la regla de validación. Podríamos mostrar un mensaje más específico cuando la validación `min` del control de precio arroje un error, por ejemplo. Podemos usar el método `hasError` que vimos en la sección anterior para mostrar dicho mensaje:

```html
<div>
  <label for="price">Price</label>
  <input id="price" formControlName="price" type="number" required />
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('required')) {
    <span class="help-text">Price is required</span>
  }
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('min')) {
    <span class="help-text">Price should be greater than 0</span>
  }
</div>
```

El framework Angular proporciona un conjunto de validadores integrados que aprendimos a usar en nuestros formularios. En la siguiente sección, aprenderemos a crear un validador personalizado para formularios basados en plantillas y reactivos para satisfacer necesidades empresariales particulares.

#### Creación de validadores personalizados
Los validadores integrados no cubrirán todos los escenarios que podríamos encontrar en una aplicación Angular; sin embargo, escribir un validador personalizado y usarlo en un formulario de Angular es fácil. En nuestro caso, crearemos un validador para verificar que el precio de un producto no pueda exceder un umbral específico.

> Podríamos usar el validador integrado `max` para realizar la misma tarea. Sin embargo, crearemos la función validadora con fines de aprendizaje.
>
> Los validadores personalizados se utilizan cuando queremos validar un formulario o un control con código personalizado. Por ejemplo, para comunicarse con una API para validar un valor o para realizar un cálculo complejo para validar un valor.

1. Crea un archivo llamado `price-maximum.validator.ts` en la carpeta `src/app` y agrega el siguiente contenido:

```typescript
import { ValidatorFn, AbstractControl, ValidationErrors } from '@angular/forms';

export function priceMaximumValidator(price: number): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const isMax = control.value <= price;
    return isMax ? null : { priceMaximum: true };
  };
}
```

Un validador de formulario es una función que devuelve un objeto `ValidationErrors` con el error especificado o un valor `null`. Acepta el control de formulario al que se aplicará como parámetro. En el fragmento anterior, si el valor del control es mayor que un umbral específico pasado en el parámetro `price` de la función exportada, devuelve un objeto de error de validación. De lo contrario, devuelve `null`.

> La clave del objeto de error de validación especifica un nombre descriptivo para el error del validador. Es un nombre que luego podemos verificar con el método `hasError` del control para averiguar si tiene algún error. El valor del objeto de error de validación puede ser cualquier valor arbitrario que podamos pasar en el mensaje de error.

2. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { priceMaximumValidator } from '../price-maximum.validator';
```

3. Agrega el validador en el array `validators` del control de formulario `price` y establece el umbral en 1000:

```typescript
price: new FormControl<number | undefined>(undefined, {
  nonNullable: true,
  validators: [
    Validators.required,
    Validators.min(1),
    priceMaximumValidator(1000)
  ]
})
```

4. Agrega un nuevo elemento HTML `<span>` para el control de formulario `price` en el archivo `product-create.component.html`:

```html
<div>
  <label for="price">Price</label>
  <input id="price" formControlName="price" type="number" required />
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('required')) {
    <span class="help-text">Price is required</span>
  }
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('min')) {
    <span class="help-text">Price should be greater than 0</span>
  }
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('priceMaximum')) {
    <span class="help-text">Price must be smaller or equal to 1000</span>
  }
</div>
```

5. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200/products/new`.
6. Ingresa un valor de 1200 en el campo *Price*, haz clic fuera del cuadro de entrada y observa el resultado:

*(Figura 10.7: Validación en formularios reactivos)*

Para usar el validador de precio máximo en un formulario basado en plantillas, debemos seguir un enfoque diferente que implica la creación de una directiva de Angular:

7. Ejecuta el siguiente comando para crear una directiva de Angular:

```bash
ng generate directive price-maximum
```

La directiva anterior actuará como un contenedor (*wrapper*) sobre la función `priceMaximumValidator` que ya hemos creado.

8. Abre el archivo `price-maximum.directive.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Directive, input, numberAttribute } from '@angular/core';
import { AbstractControl, NG_VALIDATORS, ValidationErrors, Validator} from '@angular/forms';
import { priceMaximumValidator } from './price-maximum.validator';
```

9. Agrega el proveedor `NG_VALIDATORS` en el decorador `@Directive`:

```typescript
@Directive({
  selector: '[appPriceMaximum]',
  providers: [
    {
      provide: NG_VALIDATORS,
      useExisting: PriceMaximumDirective,
      multi: true
    }
  ]
})
```

El token `NG_VALIDATORS` es un token integrado de los formularios de Angular que nos ayuda a registrar una directiva de Angular como validador de formularios. En el fragmento anterior, usamos la propiedad `multi` en la configuración del proveedor porque podemos registrar múltiples directivas con el token `NG_VALIDATORS`.

10. Agrega la interfaz `Validator` en las interfaces implementadas de la clase `PriceMaximumDirective`:

```typescript
export class PriceMaximumDirective implements Validator
```

11. Agrega la siguiente propiedad de entrada que se utilizará para pasar un valor para el umbral máximo:

```typescript
appPriceMaximum = input(undefined, {
  alias: 'threshold',
  transform: numberAttribute
});
```

En la propiedad anterior, pasamos un objeto de configuración con dos propiedades como parámetro en la función `input`. La propiedad `alias` define el nombre de la propiedad de entrada que usaremos para el enlace. La propiedad `transform` se utiliza para convertir el valor de la propiedad de entrada a un tipo diferente. `numberAttribute` es una función integrada del framework Angular que convierte el valor de la propiedad de entrada en un número.

> Angular también contiene la función `booleanAttribute`, que analiza el valor de una propiedad de entrada como un booleano.

12. Implementa el método `validate` de la interfaz `Validator` de la siguiente manera:

```typescript
validate(control: AbstractControl): ValidationErrors | null {
  return this.appPriceMaximum ?
    priceMaximumValidator(this.appPriceMaximum()!)(control) : null;
}
```

La firma del método `validate` es la misma que la función devuelta por la función `priceMaximumValidator`. Comprueba la propiedad de entrada `appPriceMaximum` y, en consecuencia, delega su valor a la función `priceMaximumValidator`.

Usaremos la nueva directiva que creamos en el componente de detalles del producto:

13. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { PriceMaximumDirective } from '../price-maximum.directive';
```

14. Agrega la clase `PriceMaximumDirective` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-detail',
  imports: [
    CommonModule,
    FormsModule,
    PriceMaximumDirective
  ],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

15. Abre el archivo `product-detail.component.html` y agrega el nuevo validador en el elemento HTML `<input>`:

```html
<input placeholder="New price" type="number" name="price" required min="1" appPriceMaximum threshold="500" #priceCtrl="ngModel" [(ngModel)]="price" />
```

16. Agrega un nuevo elemento HTML `<span>` para mostrar un mensaje diferente cuando el validador arroje un error:

```html
@if (priceCtrl.dirty && priceCtrl.hasError('priceMaximum')) {
  <span class="help-text">Price must be smaller or equal to 500</span>
}
```

17. Ejecuta el comando `ng serve` para iniciar la aplicación y selecciona un producto de la lista.
18. Ingresa el valor 600 en el cuadro de entrada *New price* y observa la salida:

*(Figura 10.8: Validación en formularios basados en plantillas)*

> Las validaciones personalizadas de Angular pueden funcionar de forma sincrónica o asincrónica. En esta sección, aprendimos a trabajar con las primeras. Las validaciones asincrónicas son un tema avanzado que no cubriremos en este libro. Sin embargo, puedes obtener más información en [https://angular.dev/guide/forms/form-validation#creating-asynchronous-validators](https://angular.dev/guide/forms/form-validation#creating-asynchronous-validators).

En la siguiente sección, exploraremos la manipulación del estado de un formulario de Angular.

---

### Sección 10.6: Manipulación del estado del formulario

El estado de un formulario de Angular difiere entre los formularios basados en plantillas y los reactivos. En los primeros, el estado es un objeto simple, mientras que en los segundos, se mantiene en el modelo del formulario. En esta sección, aprenderemos sobre los siguientes conceptos:

- Actualización del estado del formulario
- Reaccionar a los cambios de estado

Comenzaremos explorando cómo podemos cambiar el estado del formulario.

#### Actualización del estado del formulario
Trabajar con el estado del formulario en formularios basados en plantillas es relativamente fácil. Debemos interactuar con la propiedad del componente vinculada a la directiva `ngModel` de un control de formulario.

En los formularios reactivos, podemos usar la propiedad `value` de una instancia de `FormControl` o los siguientes métodos de la clase `FormGroup` para cambiar valores en todo el formulario:

- **`setValue`:** Reemplaza valores en todos los controles del formulario.
- **`patchValue`:** Actualiza valores en controles específicos del formulario.

El método `setValue` acepta un objeto como parámetro que contiene pares clave-valor para todos los controles del formulario. Si queremos completar los detalles de un producto en el componente de creación de productos mediante programación, el siguiente fragmento sirve como ejemplo:

```typescript
this.productForm.setValue({
  title: 'TV monitor',
  price: 600,
  category: 'electronics'
});
```

En el fragmento anterior, cada clave del objeto pasado en el método `setValue` debe coincidir con el nombre de cada control de formulario. Si omitimos uno, Angular arrojará un error.

Si queremos completar algunos de los detalles de un producto, podemos usar el método `patchValue`:

```typescript
this.productForm.patchValue({
  title: 'TV monitor',
  category: 'electronics'
});
```

Los métodos `setValue` y `patchValue` de la clase `FormGroup` nos ayudan a establecer datos en un formulario.

Otro aspecto interesante de los formularios es que podemos recibir notificaciones cuando estos valores cambian, como veremos en la siguiente sección.

#### Reaccionar a los cambios de estado
Un escenario común cuando se trabaja con formularios de Angular es que queremos desencadenar un efecto secundario cuando cambia el valor de un control de formulario. Un efecto secundario puede ser cualquiera de los siguientes:

- Alterar el valor de un control de formulario.
- Iniciar una solicitud HTTP para filtrar el valor de un control de formulario.
- Habilitar/deshabilitar ciertas partes de la plantilla del componente.

En los formularios basados en plantillas, podemos usar una versión extendida de la directiva `ngModel` para recibir una notificación cuando cambie su valor. La directiva `ngModel` contiene las siguientes propiedades vinculables:

- **`ngModel`:** Una propiedad de entrada para pasar valores al control.
- **`ngModelChange`:** Una propiedad de salida para recibir notificaciones cuando cambia el valor del control.

Podemos escribir el enlace `ngModel` en el elemento HTML `<input>` del componente de detalles del producto de la siguiente manera alternativa:

```html
<input placeholder="New price" type="number" name="price" required min="1" appPriceMaximum threshold="500" #priceCtrl="ngModel" [ngModel]="price" (ngModelChange)="price = $event" />
```

En el fragmento anterior, establecemos el valor de la propiedad de entrada `ngModel` usando el enlace de propiedades y el valor de la propiedad de componente `price` usando el enlace de eventos. Angular desencadena el evento `ngModelChange` automáticamente e incluye el nuevo valor del elemento HTML `<input>` en la propiedad `$event`. Podemos usar el evento `ngModelChange` para cualquier efecto secundario en nuestro componente cuando cambia el valor del control de formulario `price`.

En los formularios reactivos, utilizamos una API basada en observables para reaccionar a los cambios de estado. Las clases `FormGroup` y `FormControl` contienen el observable `valueChanges`, al que podemos suscribirnos y recibir notificaciones cuando cambia el valor del formulario o control.

Lo usaremos para restablecer el valor del control de formulario `price` en el componente de creación de productos cuando cambie la categoría:

1. Abre el archivo `product-create.component.ts` e importa el artefacto `OnInit` del paquete npm `@angular/core`:

```typescript
import { Component, OnInit } from '@angular/core';
```

2. Agrega la interfaz `OnInit` a la lista de interfaces implementadas de la clase `ProductCreateComponent`:

```typescript
export class ProductCreateComponent implements OnInit
```

3. Crea el siguiente método `ngOnInit` para suscribirte a la propiedad `valueChanges` del control de formulario `category`:

```typescript
ngOnInit(): void {
  this.productForm.controls.category.valueChanges.subscribe(() => {
    this.productForm.controls.price.reset();
  });
}
```

En el método anterior, restablecemos el valor del control de formulario `price` mediante el método `reset` de la clase `FormControl`.

> La propiedad `valueChanges` de la clase `FormControl` es un flujo observable estándar. No olvides cancelar la suscripción cuando se destruya el componente.

Por supuesto, hay más cosas que podemos hacer con el observable `valueChanges`; por ejemplo, podríamos verificar si el título del producto ya está reservado enviándolo a una API backend. Con suerte, sin embargo, los ejemplos anteriores han transmitido cómo puedes aprovechar la naturaleza reactiva de los formularios y responder en consecuencia.

---

### Sección 10.7: Resumen

En este capítulo, aprendimos que Angular proporciona dos tipos diferentes para crear formularios —basados en plantillas y reactivos— y ninguno de los enfoques es mejor que el otro. Exploramos cómo construir cada tipo de formulario y realizar validaciones en los datos de entrada, y cubrimos validaciones personalizadas para implementar escenarios de validación adicionales. También aprendimos cómo actualizar el estado de un formulario y cómo reaccionar cuando los valores en el estado cambian.

En el siguiente capítulo, exploraremos varias formas de manejar errores de aplicaciones. El manejo de errores es una característica muy importante de una aplicación Angular y puede tener diferentes fuentes y razones, como veremos.
