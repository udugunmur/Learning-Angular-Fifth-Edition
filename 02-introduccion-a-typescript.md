# Parte 1: Fundamentos de Angular

## Capítulo 2: Introducción a TypeScript

Como aprendimos en el capítulo anterior, cuando creamos nuestra primera aplicación Angular, el código de un proyecto de Angular está escrito en **TypeScript**. Escribir en TypeScript y aprovechar su tipado estático nos brinda una ventaja notable sobre otros lenguajes de scripting. Este capítulo no es una descripción exhaustiva del lenguaje TypeScript. En cambio, nos centraremos en los elementos centrales que serán útiles para este libro. Como veremos muy pronto, tener un conocimiento sólido de estos mecanismos es fundamental para comprender cómo funciona la inyección de dependencias en Angular.

En este capítulo, vamos a cubrir los siguientes temas principales:

- Fundamentos de JavaScript
- ¿Qué es TypeScript?
- Primeros pasos con TypeScript

Primero refrescaremos nuestro conocimiento de JavaScript revisando algunas características esenciales relacionadas con TypeScript, como funciones y clases. Luego investigaremos los antecedentes de TypeScript y la razón fundamental detrás de su creación. También aprenderemos cómo codificar y ejecutar código TypeScript. Haremos hincapié en el sistema de tipos, que es la principal ventaja de TypeScript, y aprenderemos a usarlo para crear tipos básicos e interfaces.

---

### Sección 2.1: Requisitos técnicos

- **GitHub:** [https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch02](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch02)
- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 2.2: Fundamentos de JavaScript

JavaScript es un lenguaje de programación que contiene muchas características para crear aplicaciones web. En esta sección, revisaremos y refrescaremos nuestro conocimiento de algunas de las más básicas, ya que están directamente correlacionadas con el desarrollo en TypeScript y Angular. TypeScript es un superconjunto sintáctico de JavaScript, lo que significa que agrega características como tipos, interfaces y genéricos. Analizaremos las siguientes características de JavaScript en mayor detalle:

- Declaración de variables
- Parámetros de función
- Funciones flecha
- Encadenamiento opcional (*Optional chaining*)
- Fusión nula (*Nullish coalescing*)
- Clases
- Módulos

Puedes ejecutar todos los ejemplos de código de esta sección de las siguientes maneras:

- Ingresando el código en la consola del navegador.
- Escribiendo el código en un archivo JavaScript y usando Node.js para ejecutarlo.

> Si te sientes cómodo con estas características, puedes saltar directamente a la sección *¿Qué es TypeScript?*.

#### Declaración de variables
Tradicionalmente, los desarrolladores de JavaScript han utilizado la palabra clave `var` para declarar objetos, variables y otros artefactos. La razón era que la semántica antigua del lenguaje solo tenía un alcance de función (*function scope*) donde las variables eran únicas dentro de su contexto:

```javascript
function myFunc() {
  var x = 0;
}
```

En la función anterior, no se puede declarar ninguna otra variable como `x` dentro de su cuerpo. Si declaras una, efectivamente la estás redefiniendo. Sin embargo, hay casos en los que no se aplica el alcance de bloque, como en los bucles:

```javascript
var x = 20;
for (var x = 0; x < 10; x++) {
}
```

En el fragmento anterior, la variable `x` fuera del bucle no afectará a la variable `x` dentro porque tienen un alcance diferente. Para superar la limitación del alcance, JavaScript introdujo la palabra clave `let`:

```javascript
function myFunc() {
  let x = 0;
  x = 10;
}
```

La palabra clave `let` nos permite cambiar la referencia de una variable varias veces en el código.

Otra forma de definir variables en JavaScript es la palabra clave `const`, que indica que una variable nunca debe cambiar. A medida que crece una base de código, los cambios pueden ocurrir por error, lo cual puede ser costoso. La palabra clave `const` puede prevenir este tipo de errores. Considera el siguiente fragmento de código:

```javascript
const price = 100;
price = 50;
```

Si intentamos ejecutarlo, arrojará el siguiente mensaje de error:

```text
TypeError: Assignment to constant variable.
```

El error anterior solo aparecerá en el nivel superior. Debes tener esto en cuenta si declaras objetos como constantes, de la siguiente manera:

```javascript
const product = { price: 100 };
product.price = 50;
```

Declarar la variable `product` como una constante no impide que se edite el objeto en sí, sino su referencia. Por lo tanto, el código anterior es válido. Si intentamos cambiar la referencia de la variable, obtendremos el mismo tipo de error que antes:

```javascript
const product = { price: 100 };
product = { price: 50 };
```

Es preferible utilizar la palabra clave `const` cuando estamos seguros de que las propiedades de un objeto no cambiarán durante su vida útil, ya que evita que el objeto cambie accidentalmente.

Cuando queremos combinar variables, podemos usar la sintaxis de parámetro de propagación (*spread parameter*). Un parámetro de propagación utiliza los puntos suspensivos (`...`) para expandir los valores de una variable:

```javascript
const category = 'Computing';
const categories = ['Gaming', 'Multimedia'];
const productCategories = [...categories, category];
```

En el fragmento anterior, combinamos el array `categories` y el elemento `category` para crear un nuevo array. El array `categories` todavía contiene dos elementos, mientras que el nuevo array contiene tres. El comportamiento actual se denomina **inmutabilidad**, lo que significa no cambiar una variable sino crear una nueva a partir de la original.

> Un objeto no es inmutable si sus propiedades se pueden cambiar o si sus propiedades son un objeto cuyas propiedades se pueden cambiar.

También podemos usar un parámetro de propagación en objetos:

```javascript
const product = { name: 'Keyboard', price: 75 };
const newProduct = { ...product, price: 100, category: 'Computing' };
```

En el fragmento anterior, no cambiamos el objeto `product` original, sino que creamos una fusión entre los dos. El valor del objeto `newProduct` será:

```javascript
{ name: 'Keyboard', price: 100, category: 'Computing' }
```

El objeto `newProduct` toma las propiedades del objeto `product`, agrega nuevos valores sobre él y reemplaza los existentes.

#### Parámetros de función
Las funciones en JavaScript son las máquinas de procesamiento que usamos para analizar entradas, asimilar información y aplicar las transformaciones necesarias a los datos. Utilizan parámetros para proporcionar datos para transformar el estado de nuestra aplicación o devolver una salida que se utilizará para dar forma a la lógica empresarial de nuestra aplicación o a la interactividad del usuario.

Podemos declarar una función para que acepte parámetros por defecto, de modo que la función asuma un valor predeterminado cuando no se pase explícitamente durante la ejecución:

```javascript
function addtoCart(productId, quantity = 1) {
  const product = { id: productId, qty: quantity };
}
```

Si no pasamos un valor para el parámetro `quantity` al llamar a la función, obtendremos un objeto `product` con `qty` establecido en `1`.

> Los parámetros por defecto deben definirse después de todos los parámetros obligatorios en la firma de la función.

Una ventaja significativa de la flexibilidad de JavaScript al definir funciones es aceptar un array ilimitado y no declarado de parámetros llamados **parámetros rest** (*rest parameters*). Básicamente, podemos definir un parámetro adicional al final de la lista de argumentos con el prefijo de puntos suspensivos (`...`):

```javascript
function addProduct(name, ...categories) {
  const product = { name, categories: categories.join(',') };
}
```

En la función anterior, usamos el método `join` para crear una cadena separada por comas a partir del parámetro `categories`. Pasamos cada parámetro por separado al llamar a la función:

```javascript
addProduct('Keyboard', 'Computing', 'Peripherals');
```

Los parámetros rest son beneficiosos cuando no sabemos cuántos argumentos se pasarán como parámetros. La propiedad `name` también se establece utilizando otra característica útil del lenguaje JavaScript. En lugar de establecer la propiedad en el objeto `product` explícitamente, usamos el nombre de la propiedad directamente. El siguiente fragmento es equivalente a la declaración inicial de la función `addProduct`:

```javascript
function addProduct(name, ...categories) {
  const product = { name: name, categories: categories.join(',') };
}
```

> La sintaxis abreviada para asignar valores de propiedad solo se puede utilizar cuando el nombre del parámetro coincide con el nombre de la propiedad de un objeto.

#### Funciones flecha
En JavaScript, podemos crear funciones de una manera alternativa llamada **funciones flecha** (*arrow functions*). El propósito de una función flecha es simplificar la sintaxis general de la función y proporcionar una forma infalible de manejar el alcance de la función, que tradicionalmente es manejado por el objeto `this`. Considera el siguiente ejemplo, que calcula el descuento de un producto dado su precio:

```javascript
const discount = (price) => {
  return (price / 100) * 10;
};
```

El código anterior no tiene la palabra clave `function`, y el cuerpo de la función se define mediante una flecha (`=>`). Las funciones flecha se pueden simplificar aún más siguiendo estas mejores prácticas:

- Omitir los paréntesis en los parámetros de la función cuando la firma contiene un solo parámetro.
- Omitir las llaves en el cuerpo de la función y la palabra clave `return` si la función tiene una sola sentencia.

La función resultante se verá mucho más simple y fácil de leer:

```javascript
const discount = price => (price / 100) * 10;
```

Expliquemos ahora cómo se relacionan las funciones flecha con el manejo del alcance (*scope*). El valor del objeto `this` puede apuntar a un contexto diferente, dependiendo de dónde ejecutemos una función. Cuando lo usamos dentro de una devolución de llamada (*callback*), perdemos el rastro del contexto superior, lo que generalmente nos lleva a usar convenciones como asignar su valor a una variable externa. Considera la siguiente función, que registra el nombre de un producto utilizando la función nativa `setTimeout`:

```javascript
function createProduct(name) {
  this.name = name;
  this.getName = function() {
    setTimeout(function() {
      console.log('Product name is:', this.name);
    });
  }
}
```

Ejecuta la función `getName` usando el siguiente fragmento y observa la salida de la consola:

```javascript
const product = new createProduct('Monitor');
product.getName();
```

El fragmento anterior no imprimirá el nombre del producto `Monitor` como se esperaba porque nuestro código modifica el alcance del objeto `this` al evaluar la función dentro del callback de `setTimeout`. Para solucionarlo, convierte la función `setTimeout` para usar una función flecha en su lugar:

```javascript
setTimeout(() => {
  console.log('Product name is:', this.name);
});
```

Nuestro código ahora es más simple y podemos usar el alcance de la función de manera segura.

#### Encadenamiento opcional (Optional chaining)
El encadenamiento opcional es una característica poderosa que puede ayudarnos a refactorizar y simplificar nuestro código. En pocas palabras, puede guiar a nuestro código para que ignore la ejecución de una sentencia a menos que se haya proporcionado un valor en algún punto de esa sentencia. Veamos el encadenamiento opcional con un ejemplo:

```javascript
const getOrder = () => {
  return { product: { name: 'Keyboard' } };
};
```

En el fragmento anterior, definimos una función `getOrder` que devuelve el producto de un pedido en particular. A continuación, obtengamos el valor de la propiedad `product`, asegurándonos de que exista un pedido antes de leerlo:

```javascript
const order = getOrder();
if (order !== undefined) {
  const product = order.product;
}
```

El fragmento anterior es un paso de precaución en caso de que nuestro objeto haya sido modificado. Si no comprobamos el objeto y se vuelve `undefined`, JavaScript arrojará un error. Sin embargo, podemos usar el encadenamiento opcional para mejorar la sentencia anterior:

```javascript
const order = getOrder();
const product = order?.product;
```

El carácter `?` después del objeto `order` asegura que se accederá a la propiedad `product` solo si el objeto tiene un valor. El encadenamiento opcional también funciona en escenarios más complicados, tales como:

```javascript
const name = order?.product?.name;
```

En el fragmento anterior, también comprobamos si el objeto `product` tiene un valor antes de acceder a su propiedad `name`.

#### Fusión nula (Nullish coalescing)
La fusión nula está relacionada con proporcionar un valor por defecto cuando una variable no está establecida. Considera el siguiente ejemplo, que asigna un valor a la variable `quantity` solo si la variable `qty` existe:

```javascript
const quantity = qty ? qty : 1;
```

La sentencia anterior se llama operador ternario y funciona como una sentencia condicional. Si la variable `qty` no tiene un valor, la variable `quantity` se inicializará con el valor predeterminado de `1`. Podemos reescribir la expresión anterior usando fusión nula como:

```javascript
const quantity = qty ?? 1;
```

La fusión nula nos ayuda a que nuestro código sea más legible y conciso.

#### Clases
Las clases de JavaScript nos permiten estructurar el código de nuestra aplicación y crear instancias de cada clase. Una clase puede tener miembros de propiedad, un constructor, métodos y descriptores de acceso a propiedades (*accessors*). El siguiente fragmento de código ilustra cómo es una clase:

```javascript
class User {
  firstName = '';
  lastName = '';
  #isActive = false;

  constructor(firstName, lastName, isActive = true) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.#isActive = isActive;
  }

  getFullname() {
    return `${this.firstName} ${this.lastName}`;
  }

  get active() {
    return this.#isActive;
  }
}
```

La sentencia `class` envuelve varios elementos que podemos desglosar:

- **Miembro:** La clase `User` contiene los miembros `firstName`, `lastName` y `#isActive`. Los miembros de la clase solo serán accesibles desde dentro de la propia clase. Las instancias de la clase `User` solo tendrán acceso a las propiedades públicas `firstName` y `lastName`. La propiedad `#isActive` no estará disponible porque es privada, como lo denota el carácter `#` delante del nombre de la propiedad.
- **Constructor:** El constructor se ejecuta cuando creamos una instancia de la clase. Por lo general, se usa para inicializar los miembros de la clase en su interior con los parámetros proporcionados en la firma. También podemos proporcionar valores predeterminados para parámetros como el parámetro `isActive`.
- **Método:** Un método representa una función y puede devolver un valor, como el método `getFullname`, que construye el nombre completo de un usuario. También se puede definir como privado, similar a los miembros de la clase.
- **Descriptor de acceso a propiedades (Accessor):** Se define anteponiendo a un método la palabra clave `set` para hacerlo escribible y la palabra clave `get` para hacerlo legible, seguido del nombre de la propiedad que queremos exponer. El método `active` es un descriptor de acceso que devuelve el valor del miembro `#isActive`.

Una clase también puede extender los miembros y la funcionalidad de otras clases. Podemos hacer que una clase herede de otra añadiendo la palabra clave `extends` a la definición de la clase seguida de la clase que queremos heredar:

```javascript
class Customer extends User {
  taxNumber = '';

  constructor(firstName, lastName) {
    super(firstName, lastName);
  }
}
```

En el fragmento anterior, la clase `Customer` extiende la clase `User`, que expone las propiedades `firstName` y `lastName`. Cualquier instancia de la clase `Customer` puede usar esas propiedades por defecto. También podemos sobrescribir métodos de la clase `User` agregando un método con el mismo nombre. El constructor debe llamar al método `super`, que apunta al constructor de la clase `User`.

#### Módulos
A medida que nuestras aplicaciones escalan y crecen, llegará un momento en que necesitaremos organizar mejor nuestro código y hacerlo sostenible y reutilizable. Los módulos son una excelente manera de lograr estas tareas, así que veamos cómo funcionan y cómo podemos implementarlos en nuestra aplicación.

En la sección anterior aprendimos a trabajar con clases. Tener ambas clases en el mismo archivo no es escalable y mantenerlo no será fácil. Imagina cuánto código debes procesar para realizar un cambio simple en una de las clases. Los módulos nos permiten separar el código de nuestra aplicación en archivos individuales, aplicando el Principio de Responsabilidad Única (**SRP**). Cada archivo es un módulo diferente que se ocupa de una característica o funcionalidad específica.

> Un buen indicio para dividir un módulo en varios archivos es cuando el módulo comienza a ocupar diferentes dominios. Por ejemplo, un módulo de productos no puede contener lógica para clientes.

Refactoricemos el código descrito en la sección anterior para que las clases `User` y `Customer` pertenezcan a módulos separados:

1. Abre VSCode y crea un nuevo archivo JavaScript llamado `user.js`.
2. Introduce el contenido de la clase `User` y agrega la palabra clave `export` en la definición de la clase. La palabra clave `export` hace que el módulo esté disponible para otros módulos y forma la API pública del módulo.
3. Crea un nuevo archivo JavaScript llamado `customer.js` y agrega el contenido de la clase `Customer`. La clase `Customer` no puede reconocer la clase `User` porque están en archivos diferentes.
4. Importa la clase `User` en el archivo `customer.js` agregando la siguiente sentencia en la parte superior del archivo:

```javascript
import { User } from './user';
```

Usamos la palabra clave `import` y la ruta relativa del archivo del módulo sin la extensión para importar la clase `User`. Si un módulo exporta más de un artefacto, los colocamos dentro de llaves separados por una coma, como por ejemplo:

```javascript
import { User, UserPreferences } from './user';
```

Explorar los módulos concluye nuestro recorrido por los fundamentos de JavaScript. En la siguiente sección, aprenderemos sobre TypeScript y cómo nos ayuda a crear aplicaciones web.

---

### Sección 2.3: ¿Qué es TypeScript?

Transformar pequeñas aplicaciones web en clientes monolíticos robustos era imposible debido a las limitaciones de las versiones anteriores de JavaScript. En resumen, las aplicaciones de JavaScript a gran escala sufrían graves problemas de mantenibilidad y escalabilidad tan pronto como crecían en tamaño y complejidad. Este problema se volvió más relevante a medida que las nuevas librerías y módulos requerían una integración perfecta en nuestras aplicaciones. La falta de mecanismos adecuados para la interoperabilidad condujo a soluciones complejas e incómodas.

Para superar esas dificultades, **Microsoft** creó un superconjunto del lenguaje JavaScript que ayudaría a crear aplicaciones empresariales con una menor tasa de errores mediante la comprobación estática de tipos, mejores herramientas y análisis de código. **TypeScript 1.0** se introdujo en 2014. Se adelantó a JavaScript, implementó las mismas características y proporcionó un entorno estable para crear aplicaciones a gran escala. Introdujo el tipado estático opcional a través de anotaciones de tipo, lo que garantiza la comprobación de tipos en tiempo de compilación y detecta errores en una etapa temprana del proceso de desarrollo. Su soporte para archivos de declaración también permitió a los desarrolladores describir la interfaz de sus módulos para que otros desarrolladores pudieran integrarlos mejor en su flujo de trabajo y herramientas de código.

El sitio web oficial de TypeScript se encuentra en [https://www.typescriptlang.org](https://www.typescriptlang.org/). Contiene una extensa documentación del lenguaje y un *playground* que nos brinda acceso a un tutorial rápido para ponernos al día con el lenguaje en poco tiempo. Incluye algunos ejemplos de código listos para usar que cubren algunos de los rasgos más comunes del lenguaje.

Como superconjunto de JavaScript, una de las principales ventajas de adoptar TypeScript en tu próximo proyecto es la baja barrera de entrada. Si conoces JavaScript, prácticamente estás listo, ya que todas las funciones adicionales de TypeScript son opcionales. Puedes elegir e introducir cualquiera de ellas para lograr tu objetivo. En general, hay una larga lista de argumentos sólidos para usar TypeScript en tu próximo proyecto, y todos se aplican a Angular.

Aquí tienes un breve resumen de algunas de las ventajas:

- Anotar tu código con tipos asegura la integración consistente de tus diferentes unidades de código y mejora la legibilidad y comprensión del código.
- El verificador de tipos integrado analiza tu código en tiempo de compilación y te ayuda a prevenir errores antes de ejecutar el código.
- El uso de tipos garantiza la coherencia en toda la aplicación. Combinado con los dos puntos anteriores, la tasa general de errores de código se minimiza a largo plazo.
- Las interfaces aseguran la integración fluida y sin problemas de tus librerías en otros sistemas y bases de código.
- El soporte del lenguaje en diferentes IDEs es extraordinario, y puedes beneficiarte de funciones como resaltado de código, verificación de tipos en tiempo real y compilación automática sin costo adicional.
- La sintaxis es familiar para los desarrolladores de otros entornos orientados a objetos (**OOP**), como Java, C# y C++.

En la siguiente sección, aprenderemos a desarrollar y ejecutar una aplicación TypeScript. En las aplicaciones Angular, no necesitamos ejecutar el código TypeScript manualmente porque el CLI de Angular lo maneja automáticamente; sin embargo, es bueno saber cómo funciona internamente.

---

### Sección 2.4: Primeros pasos con TypeScript

El lenguaje TypeScript es un paquete npm que se puede instalar desde el registro de npm mediante el siguiente comando:

```bash
npm install -g typescript
```

En el comando anterior, elegimos instalar TypeScript globalmente en nuestro sistema para poder usarlo desde cualquier ruta en nuestro entorno de desarrollo. Veamos cómo podemos usar TypeScript a través de un ejemplo simple:

1. Abre VSCode y selecciona **File | New File…** en las opciones del menú principal.
2. Escribe `app.ts` en el cuadro de diálogo *New File…* y presiona *Enter*.

*(Figura 2.1: Cuadro de diálogo New File…)*

> Como ya hemos aprendido, los archivos TypeScript tienen una extensión `.ts`.

3. Selecciona la ruta donde deseas crear el nuevo archivo. Luego, VSCode abrirá ese archivo dentro del editor.
4. Escribe el siguiente fragmento en el archivo `app.ts`:

```typescript
const title = 'Hello TypeScript!';
```

Aunque hemos creado un archivo TypeScript, el fragmento anterior es código JavaScript válido. Recuerda que TypeScript es un superconjunto de JavaScript que proporciona azúcar sintáctico a través de su sistema de tipos. Sin embargo, escribir código JavaScript plano con TypeScript no nos aporta ningún beneficio claro.

5. Abre una ventana de terminal y ejecuta el siguiente comando para compilar el archivo TypeScript a JavaScript:

```bash
tsc app.ts
```

El comando anterior inicia un proceso llamado **transpilación** realizado por el ejecutable `tsc`, un compilador que se encuentra en el núcleo del lenguaje TypeScript. Necesitamos compilar el código TypeScript a JavaScript porque los navegadores actualmente no admiten TypeScript de forma nativa.

> Angular utiliza un compilador que emplea el compilador de TypeScript internamente para compilar aplicaciones Angular.

El compilador de TypeScript admite opciones de configuración adicionales que podemos pasar al ejecutable `tsc` a través de la ventana de terminal o un archivo de configuración. La lista completa de opciones de compilador disponibles se puede encontrar en [https://www.typescriptlang.org/docs/handbook/compiler-options.html](https://www.typescriptlang.org/docs/handbook/compiler-options.html).

El proceso de transpilación creará un archivo `app.js` en la misma carpeta que el archivo TypeScript. El nuevo archivo contendrá el siguiente código:

```javascript
var title = 'Hello TypeScript!';
```

Dado que aún no hemos utilizado ninguna función específica de TypeScript, el fragmento anterior se ve casi idéntico al original, excepto por la declaración de la variable.

El proceso de transpilación reemplazó la palabra clave `const` por la palabra clave `var` porque el compilador de TypeScript usa una versión antigua de JavaScript por defecto. Podemos cambiar eso especificando un objetivo (*target*) en el comando `tsc`:

```bash
tsc app.ts --target es2022
```

En el comando anterior, especificamos `es2022`, que representa la versión más reciente del lenguaje JavaScript en el momento de escribir este texto. Las aplicaciones de Angular que construiremos a lo largo de este libro también apuntan a la misma versión de JavaScript por defecto.

Dado que usaremos la última versión de JavaScript en el resto de este capítulo, definamos la opción de destino utilizando un archivo de configuración de TypeScript. Crea un archivo llamado `tsconfig.json` en la carpeta actual y agrega el siguiente contenido:

```json
{
  "compilerOptions": {
    "target": "ES2022"
  }
}
```

Puedes encontrar más opciones para el archivo de configuración de TypeScript en [https://www.typescriptlang.org/tsconfig](https://www.typescriptlang.org/tsconfig).

6. Ejecuta el comando `tsc` en una ventana de terminal para verificar que el archivo JavaScript de salida permanezca sin cambios.

> Cuando ejecutamos el comando `tsc` sin opciones, compilará todos los archivos TypeScript en la carpeta actual utilizando las opciones del archivo de configuración.

El código TypeScript que hemos escrito hasta ahora no utiliza características específicas de TypeScript. En la siguiente sección, aprenderemos a utilizar el sistema de tipos, que es la característica más potente y esencial del lenguaje TypeScript.

#### Tipos
Trabajar con TypeScript o cualquier otro lenguaje de programación significa trabajar con datos, y estos datos pueden representar diferentes tipos de contenido, llamados **tipos**. Los tipos se utilizan para representar el hecho de que los datos pueden ser texto, un valor entero o un array de estos tipos de valores, entre otros.

> Los tipos desaparecen durante la transpilación y no se incluyen en el código JavaScript final.

Es posible que ya hayas encontrado tipos en JavaScript, ya que siempre hemos trabajado implícitamente con ellos. En JavaScript, cualquier variable dada podría asumir (o devolver, en el caso de las funciones) cualquier valor. A veces, esto genera errores y excepciones en nuestro código debido a colisiones de tipos entre lo que nuestro código devolvió y lo que esperábamos que devolviera en cuanto a tipos. Sin embargo, tipar estáticamente nuestras variables nos da a nosotros y a nuestro IDE una buena imagen de qué tipo de datos debemos encontrar en cada instancia de código. Se convierte en una forma invaluable de ayudar a depurar nuestras aplicaciones en tiempo de compilación antes de que se ejecute el código.

##### String
Uno de los tipos primitivos más utilizados es `string`, que llena una variable con texto:

```typescript
const product: string = 'Keyboard';
```

El tipo se define agregando dos puntos y el nombre del tipo al lado de la variable.

##### Boolean
El tipo `boolean` define una variable que puede tener un valor de `true` o `false`:

```typescript
const isActive: boolean = true;
```

El resultado de una variable booleana representa el cumplimiento de una sentencia condicional.

##### Number
El tipo `number` es probablemente el otro tipo de datos primitivo más utilizado, junto con `string` y `boolean`:

```typescript
const price: number = 100;
```

Podemos usar el tipo `number` para definir un número de punto flotante y literales hexadecimales, decimales, binarios y octales.

##### Array
El tipo `array` define una lista de elementos que contienen únicamente un determinado tipo. Manejar las excepciones que surgen de errores, como asignar tipos de miembros incorrectos en una lista, ahora se puede evitar fácilmente con este tipo. Podemos definir arrays usando la sintaxis de corchetes o la palabra clave `Array`:

```typescript
const categories: string[] = ['Computing', 'Multimedia'];
const categories: Array<string> = ['Computing', 'Multimedia'];
```

> Es aconsejable acordar con tu equipo cualquiera de las dos sintaxis y mantenerla durante el desarrollo de la aplicación.

Si intentamos agregar un nuevo elemento al array `categories` con un tipo diferente a `string`, TypeScript arrojará un error, asegurando que nuestros miembros tipados permanezcan consistentes y que nuestro código esté libre de errores.

##### any
En todos los casos anteriores, el tipado es opcional porque TypeScript es lo suficientemente inteligente como para inferir los tipos de datos de las variables a partir de sus valores con un cierto nivel de precisión.

> Permitir que el sistema de tipos infiera los tipos es muy importante, en lugar de escribirlos manualmente. El sistema de tipos nunca se equivoca, pero el desarrollador sí puede hacerlo.

Sin embargo, si no es posible, el sistema de tipos asignará automáticamente el tipo dinámico `any` a los datos con tipos poco definidos, a costa de reducir la comprobación de tipos a un mínimo indispensable. Además, podemos agregar el tipo `any` en nuestro código manualmente cuando sea difícil inferir el tipo de datos a partir de la información que tenemos en un momento determinado. El tipo `any` incluye todos los demás tipos existentes, por lo que podemos tipar cualquier valor de datos con él y asignarle cualquier valor más adelante:

```typescript
let order: any;
function setOrderNo() {
  order = '0001';
}
```

TypeScript contiene otro tipo, similar al tipo `any`, llamado `unknown`. Una variable del tipo `unknown` puede tener un valor de cualquier tipo. La principal diferencia es que TypeScript no nos permitirá aplicar operaciones arbitrarias a valores `unknown`, como llamar a un método, a menos que realicemos una comprobación de tipo primero.

Sin embargo, un gran poder conlleva una gran responsabilidad. Si eludimos la conveniencia de la comprobación estática de tipos, abrimos la puerta a errores de tipo al canalizar datos a través de nuestra aplicación. Depende de nosotros garantizar la seguridad de tipos en toda nuestra aplicación.

##### Tipos personalizados
En TypeScript, puedes crear tu propio tipo si lo necesitas utilizando la palabra clave `type` de la siguiente manera:

```typescript
type Categories = 'computing' | 'multimedia';
```

Luego podemos crear una variable de un tipo específico de la siguiente manera:

```typescript
const category: Categories = 'computing';
```

El código anterior es perfectamente válido ya que `'computing'` es uno de los valores permitidos y funciona según lo previsto. Los tipos personalizados son una excelente manera de agregar tipos con un número finito de valores permitidos.

Cuando queremos crear un tipo personalizado a partir de un objeto, podemos usar el operador `keyof`. El operador `keyof` nos permite iterar sobre las propiedades de un objeto y extraerlas en un nuevo tipo:

```typescript
type Category = {
  computing: string;
  multimedia: string;
};
type CategoryType = keyof Category;
```

En el fragmento anterior, `CategoryType` produjo el mismo resultado que el tipo `Categories`. Aprenderemos cómo podemos usar el operador `keyof` para iterar sobre las propiedades de los objetos de forma dinámica en el *Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas*.

El sistema de tipos de TypeScript se utiliza principalmente para anotar código JavaScript con tipos. Mejora la experiencia del desarrollador al proporcionar IntelliSense y prevenir errores de forma temprana en el desarrollo. En la siguiente sección, aprenderemos más sobre cómo agregar anotaciones de tipo en funciones.

#### Funciones
Las funciones en TypeScript no son tan diferentes de las de JavaScript estándar, excepto que, como todo lo demás en TypeScript, se pueden anotar con tipos estáticos. De este modo, mejoran el compilador al proporcionarle la información que espera en su firma y el tipo de datos que pretende devolver, si lo hay.

El siguiente ejemplo muestra cómo se anota una función normal en TypeScript:

```typescript
function getProduct(): string {
  return 'Keyboard';
}
```

En el fragmento anterior, anotamos el valor devuelto de la función agregando el tipo `string` a la declaración de la función. También podemos agregar tipos en los parámetros de la función, como por ejemplo:

```typescript
function getFullname(firstName: string, lastName: string): string {
  return `${this.firstName} ${this.lastName}`;
}
```

En el fragmento anterior, anotamos los parámetros declarados en la firma de la función, lo cual tiene sentido ya que el compilador querrá verificar si los datos proporcionados contienen el tipo correcto.

Como se mencionó en la sección anterior, el compilador de TypeScript es lo suficientemente inteligente como para inferir tipos cuando no se proporciona ninguna anotación. En las dos funciones anteriores, podríamos omitir el tipo porque el compilador podría inferirlo a partir de los argumentos proporcionados y las sentencias devueltas.

Cuando una función no devuelve un tipo, podemos anotarla usando el tipo `void`:

```typescript
function printFullname(firstName: string, lastName: string): void {
  console.log(`${this.firstName} ${this.lastName}`);
}
```

Ya hemos aprendido a usar parámetros por defecto y rest en funciones de JavaScript. TypeScript amplía las capacidades de las funciones al introducir parámetros opcionales. Los parámetros se definen como opcionales agregando el carácter `?` después del nombre del parámetro:

```typescript
function addtoCart(productId: number, quantity?: number) {
  const product = { id: productId, qty: quantity ?? 1 };
}
```

En la función anterior, hemos definido `quantity` como un parámetro opcional. También hemos utilizado la sintaxis de fusión nula para establecer la propiedad `qty` del objeto `product` si no se pasa `quantity`.

Podemos invocar la función `addToCart` pasando solo el parámetro `productId` o ambos.

> Los parámetros opcionales deben colocarse al final en la firma de una función.

Ya hemos aprendido cómo las clases de JavaScript pueden ayudarnos a estructurar el código de nuestra aplicación. En la siguiente sección, veremos cómo usarlas en TypeScript para mejorar aún más nuestra aplicación.

#### Clases
Considera la clase `User` que definimos en el archivo `user.js`:

```javascript
export class User {
  firstName = '';
  lastName = '';
  #isActive = false;

  constructor(firstName, lastName, isActive = true) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.#isActive = isActive;
  }

  getFullname() {
    return `${this.firstName} ${this.lastName}`;
  }

  get active() {
    return this.#isActive;
  }
}
```

Daremos pasos sencillos y pequeños para agregar tipos en toda la clase:

1. Convierte el archivo a TypeScript renombrándolo a `user.ts`.
2. Agrega los siguientes tipos a todas las propiedades de la clase:

```typescript
firstName: string = '';
lastName: string = '';
private isActive: boolean = false;
```

En el fragmento anterior, también usamos el modificador `private` para definir la propiedad `isActive` como privada.

3. Modifica el constructor agregando tipos a los parámetros:

```typescript
constructor(firstName: string, lastName: string, isActive: boolean = true) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.isActive = isActive;
}
```

Alternativamente, podríamos omitir las propiedades de la clase y hacer que el constructor las cree automáticamente declarando los parámetros como privados:

```typescript
constructor(private firstName: string, private lastName: string, private isActive: boolean = true) {}
```

4. Finalmente, agrega tipos en el descriptor de acceso a la propiedad `active` y en el método `getFullname`:

```typescript
getFullname(): string {
  return `${this.firstName} ${this.lastName}`;
}

get active(): boolean {
  return this.isActive;
}
```

Convertir una clase de JavaScript a TypeScript y agregar tipos es un paso importante para aprovechar la característica de tipado en TypeScript.

Otra gran característica de TypeScript relacionada con las clases es la palabra clave `instanceof`. Nos permite verificar el tipo de instancia de la clase y proporciona las propiedades correctas de acuerdo con la clase relacionada. Explorémoslo con la clase `Customer` definida en el archivo `customer.js`:

1. Convierte el archivo a TypeScript renombrándolo a `customer.ts`.
2. Reescribe la clase `Customer` de la siguiente manera para agregar tipos:

```typescript
class Customer extends User {
  taxNumber: number;

  constructor(firstName: string, lastName: string) {
    super(firstName, lastName);
  }
}
```

3. Crea un objeto fuera de la clase que pueda ser tanto del tipo `User` como `Customer`:

```typescript
const account: User | Customer = undefined;
```

4. Ahora podemos usar la palabra clave `instanceof` para acceder a diferentes propiedades del objeto `account` de acuerdo con la clase subyacente:

```typescript
if (account instanceof Customer) {
  const taxNo = account.taxNumber;
} else {
  const name = account.getFullname();
}
```

TypeScript es lo suficientemente inteligente como para entender que el objeto `account` en la sentencia `else` no tiene una propiedad `taxNumber` porque es del tipo `User`. Incluso si intentamos acceder a ella por error, VSCode arrojará un error:

*(Figura 2.2: Error de acceso a propiedad)*

Las clases de TypeScript nos ayudan a escribir código bien estructurado, se pueden instanciar, contienen lógica de negocio y proporcionan tipado estático en nuestra aplicación. A medida que las aplicaciones escalan y se crean más clases, necesitamos encontrar formas de garantizar la coherencia y el cumplimiento de las reglas en nuestro código. Como aprenderemos en la siguiente sección, una de las mejores formas de abordar la coherencia y la validación de tipos es crear interfaces.

#### Interfaces
Una **interfaz** es un contrato de código que define un esquema particular. Cualquier artefacto como clases y funciones que implementen una interfaz debe cumplir con este esquema. Las interfaces son beneficiosas cuando queremos imponer un tipado estricto en clases generadas por fábricas (*factories*) o cuando definimos firmas de funciones para garantizar que se encuentre una propiedad tipada particular en la carga útil (*payload*).

> Las interfaces desaparecen durante la transpilación y no se incluyen en el código JavaScript final.

En el siguiente fragmento, definimos una interfaz para gestionar productos:

```typescript
interface Product {
  name: string;
  price: number;
  getCategories: () => string[];
}
```

> Las interfaces son el enfoque recomendado cuando se trabaja con datos de una API backend u otra fuente.

Una interfaz puede contener propiedades y métodos. En el fragmento anterior, la interfaz `Product` contenía las propiedades `name` y `price`. También definió el método `getCategories`. Una clase puede usar una interfaz agregando la palabra clave `implements` y el nombre de la interfaz en la declaración de la clase:

```typescript
class Keyboard implements Product {
  name: string = 'Keyboard';
  price: number = 20;

  getCategories(): string[] {
    return ['Computing', 'Peripherals'];
  }
}
```

En el fragmento anterior, la clase `Keyboard` debe implementar todos los miembros de la interfaz `Product`; de lo contrario, TypeScript arrojará un error. Si no queremos implementar un miembro de la interfaz, podemos definirlo como opcional usando el carácter `?`:

```typescript
interface Product {
  name: string;
  price: number;
  getCategories: () => string[];
  description?: string;
}
```

También podemos usar interfaces para cambiar el tipo de una variable de un tipo a otro, lo que se denomina **conversión de tipos** (*type casting*). La conversión de tipos es útil cuando se trabaja con datos dinámicos o cuando TypeScript no puede inferir el tipo de una variable automáticamente. En el siguiente código, le indicamos a TypeScript que trate el objeto `product` como un tipo `Product`:

```typescript
const product = { name: 'Keyboard', price: 20 } as Product;
```

Sin embargo, la conversión de tipos debe usarse con precaución. En el fragmento anterior, omitimos intencionalmente agregar el método `getCategories`, pero TypeScript no arrojó ningún error. Cuando usamos la conversión de tipos, le decimos a TypeScript que una variable simula ser de un tipo específico.

> Se recomienda evitar la conversión de tipos si es posible y definir los tipos explícitamente.

Las interfaces se pueden combinar con genéricos para proporcionar un comportamiento de código general independientemente del tipo de datos, como aprenderemos en la siguiente sección.

#### Genéricos
Los **genéricos** se utilizan cuando queremos usar tipos dinámicos en otros artefactos de TypeScript, como los métodos.

Supongamos que queremos crear una función para guardar un objeto `Product` en el almacenamiento local (*local storage*) del navegador:

```typescript
function save(data: Product) {
  localStorage.setItem('Product', JSON.stringify(data));
}
```

En el código anterior, definimos explícitamente el parámetro `data` como un `Product`. Si también queremos guardar objetos `Keyboard`, deberíamos modificar el método `save` de la siguiente manera:

```typescript
function save(data: Product | Keyboard) {
  localStorage.setItem('Product', JSON.stringify(data));
}
```

Sin embargo, el enfoque anterior no escala bien si quisiéramos agregar otros tipos en el futuro. En su lugar, podemos usar genéricos para que el consumidor del método `save` decida sobre el tipo de datos pasado:

```typescript
function save<T>(data: T) {
  localStorage.setItem('Product', JSON.stringify(data));
}
```

En el ejemplo anterior, el tipo de `T` no se evalúa hasta que usamos el método. Usamos `T` por convención para definir genéricos, pero también puedes usar otras letras. Podemos ejecutar el método `save` para un objeto `Product` de la siguiente manera:

```typescript
save<Product>({
  name: 'Microphone',
  price: 45,
  getCategories: () => ['Peripherals', 'Multimedia']
});
```

Como puedes ver, su tipo varía según cómo lo llames. También asegura que estás pasando el tipo de datos correcto. Supongamos que el método anterior se llama de esta manera:

```typescript
save<Product>('Microphone');
```

Especificamos que `T` debe ser un `Product`, pero insistimos en pasar su valor como un `string`. El compilador indica claramente que esto no es correcto. Si quisiéramos usar más genéricos en nuestro método `save`, podríamos usar letras diferentes, como por ejemplo:

```typescript
function save<T, P>(data: T, obj: P) {
  localStorage.setItem('Product', JSON.stringify(data));
}
```

Los genéricos se utilizan a menudo en colecciones porque tienen un comportamiento similar, independientemente del tipo. Sin embargo, se pueden usar en otras construcciones, como métodos. La idea es que los genéricos deben indicar si estás a punto de mezclar tipos de una manera que no está permitida.

> Los genéricos son muy potentes para usar si tienes un comportamiento típico con muchos tipos de datos diferentes. Es probable que no escribas genéricos personalizados, al menos no inicialmente, pero es bueno saber qué está sucediendo.

En la siguiente sección, veremos algunos tipos de utilidad relacionados con interfaces que nos ayudarán durante el desarrollo en Angular.

#### Tipos de utilidad (Utility types)
Los **tipos de utilidad** son tipos que nos ayudan a derivar nuevos tipos a partir de los existentes.

El tipo `Partial` se utiliza cuando queremos crear un objeto a partir de una interfaz donde todas sus propiedades son opcionales. En el siguiente fragmento, usamos la interfaz `Product` para declarar una versión reducida de un producto:

```typescript
const mic: Partial<Product> = { name: 'Microphone', price: 67 };
```

En el fragmento anterior, podemos ver que el objeto `mic` no contiene el método `getCategories`. Alternativamente, podríamos usar el tipo `Pick`, que nos permite crear un objeto a partir de un subconjunto de propiedades de la interfaz:

```typescript
type Microphone = Pick<Product, 'name' | 'price'>;
const microphone: Microphone = { name: 'Microphone', price: 67 };
```

Algunos lenguajes, como C#, tienen un tipo reservado al definir un objeto de pares clave-valor o diccionario, como se le conoce. En TypeScript, si queremos definir dicho tipo, podemos usar un tipo `Record`:

```typescript
interface Order {
  products: Record<string, number>;
}
```

El fragmento anterior define el nombre del producto como un `string` y la cantidad como un `number`.

Puedes encontrar más tipos de utilidad en [https://www.typescriptlang.org/docs/handbook/utility-types.html](https://www.typescriptlang.org/docs/handbook/utility-types.html).

---

### Sección 2.5: Resumen

Fue una lectura extensa, pero esta introducción a TypeScript fue necesaria para comprender la lógica detrás de muchas de las partes más brillantes de Angular. Nos permitió introducir la sintaxis del lenguaje y explicar la razón de su éxito como la sintaxis preferida para construir el framework Angular.

Revisamos la arquitectura de tipos y cómo podemos crear lógica de negocios avanzada al diseñar funciones con varias alternativas para firmas parametrizadas. Incluso descubrimos cómo evitar problemas relacionados con el alcance utilizando las potentes funciones flecha. Mejoramos nuestro conocimiento de TypeScript explorando algunas de las características más comunes utilizadas en las aplicaciones Angular.

Probablemente la parte más relevante de este capítulo abarcó nuestra descripción general de clases, métodos, propiedades y descriptores de acceso, y cómo podemos manejar la herencia y un mejor diseño de aplicaciones a través de interfaces.

Con todo este conocimiento, podemos comenzar a aprender a aplicarlo creando aplicaciones Angular. En el próximo capítulo, aprenderemos a usar componentes de Angular para crear interfaces de usuario componibles que permitan mantener el código de nuestra aplicación y hacerlo más escalable.
