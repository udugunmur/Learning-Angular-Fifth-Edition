# Parte 4: Calidad, Rendimiento y Producción

## Capítulo 13: Pruebas unitarias en aplicaciones Angular

En los capítulos anteriores, analizamos muchos aspectos sobre cómo crear una aplicación empresarial de Angular desde cero. Pero, ¿cómo podemos asegurarnos de que una aplicación pueda mantenerse en el futuro sin mayores complicaciones? Una capa integral de pruebas automatizadas puede convertirse en nuestro salvavidas una vez que nuestra aplicación comienza a escalar y tenemos que mitigar el impacto de los errores (*bugs*).

Las pruebas, específicamente las pruebas unitarias (*unit tests*), están diseñadas para ser realizadas por el desarrollador a medida que se desarrolla el proyecto. Ahora que nuestro conocimiento del framework es maduro, cubriremos brevemente todas las complejidades de las pruebas unitarias de una aplicación Angular en este capítulo, incluido el uso de herramientas de prueba.

> Por simplicidad, los ejemplos de este capítulo no están relacionados con la aplicación de tienda online que hemos creado a lo largo del libro.

En más detalle, aprenderemos sobre lo siguiente:

- ¿Por qué necesitamos pruebas unitarias?
- La anatomía de una prueba unitaria
- Introducción a las pruebas unitarias en Angular
- Pruebas de componentes
- Pruebas de servicios
- Pruebas de pipes
- Pruebas de directivas
- Pruebas de formularios
- Pruebas del enrutador

---

### Sección 13.1: Requisitos técnicos

El capítulo contiene varios ejemplos de código para guiarte a través del concepto de pruebas unitarias en Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch13` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch13](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch13)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 13.2: ¿Por qué necesitamos pruebas unitarias?

En esta sección, aprenderemos qué son las pruebas unitarias y por qué son útiles en el desarrollo web.

> Puedes pasar a la siguiente sección si ya estás familiarizado con las pruebas unitarias y el desarrollo guiado por pruebas (*Test-Driven Development* o TDD).

Las pruebas unitarias son parte de una filosofía de ingeniería para procesos de desarrollo ágiles y eficientes. Añaden una capa de pruebas automatizadas al código de la aplicación antes de que se desarrolle. El concepto fundamental es que un fragmento de código va acompañado de su prueba, y ambos son construidos por el desarrollador que trabaja en ese código. Primero, diseñamos la prueba frente a la funcionalidad que queremos ofrecer, verificando la precisión de su salida y comportamiento. Dado que la funcionalidad aún no está implementada, la prueba fallará, por lo que el trabajo del desarrollador es crear la funcionalidad para pasar la prueba.

Las pruebas unitarias son bastante controvertidas. Si bien el desarrollo guiado por pruebas es beneficioso para garantizar la calidad del código y el mantenimiento a lo largo del tiempo, no todo el mundo realiza pruebas unitarias en su flujo de trabajo de desarrollo diario.

Crear pruebas a medida que desarrollamos nuestro código a veces puede parecer una carga, especialmente cuando los resultados de las pruebas se vuelven más grandes que la funcionalidad que pretenden probar. Sin embargo, los argumentos a favor de las pruebas superan con creces los argumentos en contra:

- **La creación de pruebas contribuye a un mejor diseño del código:** Nuestro código debe ajustarse a los requisitos de la prueba y no al revés. Si intentamos probar un fragmento de código existente y nos encontramos bloqueados en algún momento, es probable que el código no esté bien diseñado y requiera replantearse. Por otro lado, la creación de funciones comprobables puede ayudar con la detección temprana de efectos secundarios.
- **Refactorizar código probado es un salvavidas contra la introducción de errores en etapas posteriores:** El desarrollo está destinado a evolucionar con el tiempo y el riesgo de introducir un error con cada refactorización es alto. Las pruebas unitarias son una excelente manera de garantizar que detectemos los errores a tiempo, ya sea al introducir nuevas funciones o al actualizar las existentes.
- **Crear pruebas es una excelente manera de documentar nuestro código:** Se convierte en un recurso invaluable cuando alguien que no está familiarizado con la base del código se hace cargo de las labores de desarrollo.

Estos son solo algunos argumentos, pero puedes encontrar innumerables recursos en la web sobre los beneficios de probar tu código. Si aún no te sientes convencido, pruébalo; de lo contrario, continuemos con nuestro viaje y veamos la estructura general de una prueba unitaria.

---

### Sección 13.3: La anatomía de una prueba unitaria

Hay muchas formas diferentes de probar un fragmento de código. En esta sección, veremos la anatomía de una prueba unitaria: las partes individuales que la componen.

Para probar cualquier código, necesitamos un framework para escribir la prueba y un ejecutor (*test runner*) para ejecutarla. En esta sección, nos centraremos en el framework de pruebas. El framework de pruebas debe proporcionar funciones de utilidad para crear suites de pruebas (*test suites*) que contengan una o varias especificaciones de prueba (*test specs*). Como resultado, las pruebas unitarias involucran los siguientes conceptos:

- **Suite de pruebas (*Test suite*):** Una suite que crea una agrupación lógica para muchas pruebas. Una suite, por ejemplo, puede contener todas las pruebas para una funcionalidad específica.
- **Especificación de prueba (*Test spec*):** La prueba unitaria real.

Usaremos Jasmine en este capítulo, un popular framework de pruebas que también se usa de forma predeterminada en los proyectos de Angular CLI. Así es como se ve una prueba unitaria en Jasmine:

```typescript
describe('Calculator', () => {
  it('should add two numbers', () => {
    expect(1+1).toBe(2);
  });
});
```

El método `describe` define la suite de pruebas y acepta un nombre y una función de flecha como parámetros. La función de flecha es el cuerpo de la suite de pruebas y contiene varias pruebas unitarias. El método `it` define una única prueba unitaria. Acepta un nombre y una función de flecha como parámetros.

Cada especificación de prueba valida una funcionalidad específica de la característica descrita en el nombre de la suite y declara una o varias expectativas (*expectations*) en su cuerpo. Cada expectativa toma un valor, llamado el valor esperado, que se compara con un valor real utilizando una función de comparación (*matcher*). La función comprueba si los valores esperado y real coinciden en consecuencia, lo que se denomina una aserción (*assertion*). El framework de pruebas aprueba o no la especificación según el resultado de dichas aserciones. En el ejemplo anterior, `1+1` devolverá el valor real que se supone debe coincidir con el valor esperado, `2`, declarado en la función de comparación `toBe`.

El framework Jasmine contiene varias funciones de comparación según las necesidades específicas del usuario, como veremos más adelante en el capítulo.

Supongamos que el código anterior contiene otra operación matemática que debe probarse. Tendría sentido agrupar ambas operaciones bajo la suite `Calculator`, de la siguiente manera:

```typescript
describe('Calculator', () => {
  it('should add two numbers', () => {
    expect(1+1).toBe(2);
  });
  it('should subtract two numbers', () => {
    expect(1-1).toBe(0);
  });
});
```

Hasta ahora, hemos aprendido sobre las suites de pruebas y cómo usarlas para agrupar pruebas según su funcionalidad. Además, hemos aprendido a invocar el código que queremos probar y a afirmar que hace lo que debe hacer. Sin embargo, en las pruebas unitarias intervienen más conceptos que vale la pena conocer, a saber, las funcionalidades de configuración (*setup*) y desmontaje (*teardown*).

Una funcionalidad de configuración prepara tu código antes de comenzar a ejecutar las pruebas. Es una forma de mantener tu código limpio concentrándote en invocar el código y verificar las aserciones. Una funcionalidad de desmontaje es lo opuesto. Es responsable de derribar lo que configuramos inicialmente, involucrando actividades como la limpieza de recursos. Veamos cómo se ve esto en la práctica con un ejemplo de código:

```typescript
describe('Calculator', () => {
  let total: number;
  beforeEach(() => total = 1);
  it('should add two numbers', () => {
    total = total + 1;
    expect(total).toBe(2);
  });
  it('should subtract two numbers', () => {
    total = total - 1;
    expect(total).toBe(0);
  });
  afterEach(() => total = 0);
});
```

El método `beforeEach` se utiliza para la funcionalidad de configuración y se ejecuta antes de cada prueba unitaria. En este ejemplo, establecemos el valor de la variable `total` en `1` antes de cada prueba. El método `afterEach` se utiliza para ejecutar la lógica de desmontaje. Después de cada prueba, restablecemos el valor de la variable `total` a `0`.

Es evidente que la prueba solo tiene que ocuparse de invocar el código de la aplicación y afirmar el resultado, lo que hace que las pruebas sean más limpias; sin embargo, las pruebas tienden a tener una configuración mucho mayor en una aplicación del mundo real. Lo más importante es que el método `beforeEach` tiende a facilitar la adición de nuevas pruebas, lo cual es excelente. Queremos código bien probado; cuanto más fácil sea escribir y mantener dicho código, mejor será para nuestro software.

Ahora que hemos cubierto los conceptos básicos de una prueba unitaria, veamos cómo podemos implementarlas en el contexto del framework Angular.

---

### Sección 13.4: Introducción a las pruebas unitarias en Angular

En la sección anterior, nos familiarizamos con las pruebas unitarias y sus conceptos generales, como suites de pruebas, especificaciones de pruebas y aserciones. Es hora de aventurarse en las pruebas unitarias con Angular, armados con ese conocimiento. Sin embargo, antes de comenzar a escribir pruebas para Angular, echemos un vistazo a las herramientas que nos proporcionan el framework Angular y Angular CLI:

- **`Jasmine`:** Ya aprendimos que este es el framework de pruebas.
- **`Karma`:** El ejecutor de pruebas (*test runner*) para ejecutar nuestras pruebas unitarias.
- **Utilidades de prueba de Angular (*Angular testing utilities*):** Un conjunto de métodos auxiliares que nos ayudan a configurar nuestras pruebas unitarias y escribir nuestras aserciones en el contexto del framework Angular.

Cuando usamos Angular CLI, no tenemos que hacer nada para configurar Jasmine y Karma en una aplicación Angular. Las pruebas unitarias funcionan de fábrica cuando creamos un nuevo proyecto de Angular CLI. La mayor parte del tiempo, interactuaremos con las utilidades de prueba de Angular.

Las utilidades de prueba de Angular nos ayudan a crear un entorno de pruebas que facilita la escritura de pruebas para nuestros artefactos de Angular. Consiste en la clase `TestBed` y varios métodos auxiliares en el espacio de nombres `@angular/core/testing`. A medida que avance este capítulo, aprenderemos cuáles son y cómo pueden ayudarnos a probar varios artefactos. Por ahora, echemos un vistazo a los conceptos más utilizados para que estés familiarizado con ellos cuando los analicemos con más detalle más adelante:

- **`TestBed`:** Una clase que crea un módulo de prueba. Adjuntamos un artefacto de Angular a este módulo de prueba cuando lo probamos. La clase `TestBed` contiene el método `configureTestingModule` que usamos para configurar el módulo de prueba según sea necesario.
- **`ComponentFixture`:** Una clase contenedora alrededor de una instancia de componente de Angular. Nos permite interactuar con el componente y su elemento HTML correspondiente.
- **`DebugElement`:** Un contenedor alrededor del elemento DOM del componente. Es una abstracción que opera entre plataformas para que nuestras pruebas sean independientes de la plataforma.

Ahora que conocemos nuestro entorno de pruebas y los frameworks y librerías utilizados, podemos comenzar a escribir nuestras primeras pruebas unitarias en Angular.

> Todos los ejemplos descritos en este capítulo se han creado en un nuevo proyecto de Angular CLI.

Nos embarcaremos en este gran viaje desde el bloque de construcción más fundamental de Angular: el componente.

---

### Sección 13.5: Pruebas de componentes

Es posible que hayas notado que cada vez que usamos Angular CLI para estructurar una nueva aplicación Angular o generar un artefacto de Angular, creó algunos archivos de prueba para nosotros.

Los archivos de prueba en Angular CLI contienen la palabra `spec` en su nombre de archivo. El nombre de archivo de una prueba es el mismo que el artefacto de Angular que está probando, seguido del sufijo `.spec.ts`. Por ejemplo, el archivo de prueba para el componente principal de una aplicación Angular es `app.component.spec.ts` y reside en la misma ruta que el archivo del componente.

Debemos considerar un artefacto de Angular y su prueba correspondiente como una sola entidad. Cuando cambiamos la lógica del artefacto, es posible que también debamos modificar la prueba unitaria. Colocar archivos de prueba unitaria junto con sus artefactos de Angular nos facilita recordarlos y editarlos. También nos ayuda cuando necesitamos refactorizar nuestro código, como al mover artefactos (sin olvidar mover la prueba unitaria).

Cuando estructuramos una nueva aplicación de Angular, Angular CLI crea automáticamente una prueba para el componente principal, `AppComponent`. Al principio del archivo, hay una sentencia `beforeEach` que se utiliza para fines de configuración:

```typescript
beforeEach(async () => {
  await TestBed.configureTestingModule({
    imports: [AppComponent],
  }).compileComponents();
});
```

Utiliza el método `configureTestingModule` de la clase `TestBed` y pasa un objeto como parámetro.

Podemos especificar un array `imports` que contiene el componente que queremos probar. Además, podemos definir opciones de desmontaje utilizando la propiedad `teardown`.

La propiedad `teardown` contiene un objeto del tipo `ModuleTeardownOptions` que puede establecer las siguientes propiedades:

- **`destroyAfterEach`:** Crea una nueva instancia del módulo en cada prueba para eliminar errores causados por la limpieza incompleta de elementos HTML.
- **`rethrowErrors`:** Lanza cualquier error que ocurra cuando se destruye el módulo.

Finalmente, llamamos al método `compileComponents` para compilar la clase TypeScript y la plantilla HTML de nuestro componente.

La primera prueba unitaria verifica si podemos crear una nueva instancia de `AppComponent` usando el método `createComponent`:

```typescript
it('should create the app', () => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.componentInstance;
  expect(app).toBeTruthy();
});
```

El resultado del método `createComponent` es una instancia de `ComponentFixture` del tipo `AppComponent` que puede darnos la instancia del componente utilizando la propiedad `componentInstance`. También usamos la función de comparación `toBeTruthy` para verificar si la instancia resultante es válida.

Tan pronto como tengamos acceso a la instancia del componente, podemos consultar cualquiera de sus propiedades y métodos públicos:

```typescript
it(`should have the 'my-app' title`, () => {
  const fixture = TestBed.createComponent(AppComponent);
  const app = fixture.componentInstance;
  expect(app.title).toEqual('my-app');
});
```

En la prueba anterior, verificamos si la propiedad del componente `title` está establecida en `my-app` usando otra función de comparación, `toEqual`.

> El valor de la propiedad del componente `title` en una nueva aplicación Angular será el nombre que pasaste en el comando `ng new` al crear la aplicación.

Como hemos aprendido, un componente consta de una clase TypeScript y un archivo de plantilla. Por lo tanto, probarlo solo desde la perspectiva de la clase, como en la prueba anterior, no es suficiente. También debemos probar si la clase interactúa correctamente con el DOM:

```typescript
it('should render title', () => {
  const fixture = TestBed.createComponent(AppComponent);
  fixture.detectChanges();
  const compiled = fixture.nativeElement as HTMLElement;
  expect(compiled.querySelector('h1')?.textContent).toContain('Hello, my-app');
});
```

> Muchos desarrolladores prefieren las pruebas de clases a las pruebas de DOM y confían en las pruebas de extremo a extremo (*End-to-End* o E2E), que son más lentas y tienen un rendimiento inferior. Las pruebas E2E a menudo validan la integración de una aplicación con una API backend y son fáciles de romper. Por lo tanto, se recomienda realizar pruebas unitarias del DOM en tus aplicaciones Angular.

En la prueba anterior, creamos un componente y llamamos al método `detectChanges` de `ComponentFixture`. El método `detectChanges` desencadena el mecanismo de detección de cambios de Angular, forzando la actualización de los enlaces de datos. Ejecuta el evento de ciclo de vida `ngOnInit` del componente la primera vez que se llama y el `ngOnChanges` en llamadas posteriores para que podamos consultar el elemento DOM del componente utilizando la propiedad `nativeElement`. En este ejemplo, comprobamos el `textContent` del elemento HTML correspondiente a la propiedad `title`.

Para ejecutar pruebas, usamos el comando `ng test` de Angular CLI. Iniciará el ejecutor de pruebas Karma, buscará todos los archivos de pruebas unitarias, los ejecutará y abrirá un navegador para mostrar los resultados de cada prueba. Angular CLI utiliza el navegador Google Chrome de forma predeterminada. La salida se verá así:

*(Figura 13.1: Salida de ejecución de pruebas)*

En la figura anterior, podemos ver el resultado de cada prueba en la parte superior de la página. También podemos ver cómo Karma agrupa visualmente cada prueba por suite. En nuestro caso, la única suite de pruebas es `AppComponent`.

Ahora, hagamos que una de nuestras pruebas falle. Abre el archivo `app.component.ts`, cambia el valor de la propiedad `title` a `my-new-app` y guarda el archivo. Karma volverá a ejecutar nuestras pruebas y mostrará los resultados en la página:

*(Figura 13.2: Fallo de prueba)*

Karma se ejecuta en modo de observación (*watch mode*), por lo que no necesitamos ejecutar el comando de prueba de Angular CLI cada vez que realizamos un cambio.

A veces, leer la salida de las pruebas en el navegador no es muy conveniente. Alternativamente, podemos inspeccionar la ventana de la consola que usamos para ejecutar el comando `ng test`, que contiene una versión recortada de los resultados de las pruebas:

```text
Executed 3 of 3 SUCCESS (0.117 secs / 0.044 secs)
TOTAL: 3 SUCCESS
```

Hemos obtenido una gran cantidad de información simplemente mirando la prueba de `AppComponent` que Angular CLI creó automáticamente para nosotros. En la siguiente sección, veremos un escenario más avanzado para probar un componente con dependencias.

#### Pruebas con dependencias
En un escenario del mundo real, los componentes no suelen ser tan simples como el componente principal. Es casi seguro que dependerán de uno o más servicios. También contendrán posiblemente otros componentes secundarios en su plantilla.

Tenemos diferentes formas de abordar las pruebas en tales situaciones. Una cosa está clara: si estamos probando el componente, no debemos probar el servicio ni sus componentes secundarios. Entonces, cuando configuramos dicha prueba, la dependencia no debe ser la clase real. Hay diferentes formas de lidiar con eso cuando se trata de pruebas unitarias; ninguna solución es estrictamente mejor que otra:

- **Creación de stubs (*Stubbing*):** Un método que le indica al inyector de dependencias que inyecte un *stub* (objeto simulado) de la dependencia que proporcionamos en lugar de la clase real.
- **Espionaje (*Spying*):** Un método que inyecta la dependencia real pero adjunta un espía (*spy*) al método al que llamamos en nuestro componente. Luego podemos devolver datos simulados (*mock data*) o permitir que se realice la llamada al método real.

> Es preferible utilizar stubs en lugar de espías cuando una dependencia es complicada. Algunos servicios inyectan otros servicios, por lo que el uso de la dependencia real en una prueba requiere que compenses otras dependencias. También es el método preferido cuando el componente que queremos probar contiene componentes secundarios en su plantilla.

Independientemente del enfoque, nos aseguramos de que la prueba no realice acciones no deseadas, como acceder al sistema de archivos o intentar comunicarse a través de HTTP; estamos probando el componente en completo aislamiento.

##### Reemplazar la dependencia con un stub
Reemplazar una dependencia con un *stub* significa que reemplazamos completamente la dependencia con una falsa.

Podemos crear una dependencia falsa de las siguientes maneras:

1. Crear una variable o clase constante que contenga propiedades y métodos de la dependencia real.
2. Crear una definición simulada (*mock*) de la clase real de la dependencia.

Los enfoques no son tan diferentes. En esta sección, veremos el primero, ya que es el más común en el desarrollo de Angular. Siéntete libre de explorar el segundo a tu propio ritmo.

Considera el siguiente archivo de componente `stub.component.ts`:

```typescript
import { Component, OnInit } from '@angular/core';
import { StubService } from '../stub.service';

@Component({
  selector: 'app-stub',
  template: '<span>{{ msg }}</span>'
})
export class StubComponent implements OnInit {
  msg = '';
  constructor(private stubService: StubService) {}

  ngOnInit(): void {
    this.msg = this.stubService.isBusy ?
      this.stubService.name + ' is on mission' :
      this.stubService.name + ' is available';
  }
}
```

Inyecta `StubService`, que contiene dos propiedades públicas. Proporcionar un stub para este servicio en las pruebas es bastante sencillo, como se muestra en el siguiente ejemplo:

```typescript
const serviceStub: Partial<StubService> = {
  name: 'Boothstomper'
};
```

Hemos declarado el servicio como `Partial` porque solo queremos establecer la propiedad `name` inicialmente. Ahora podemos usar la sintaxis de objeto literal para inyectar el servicio stub en nuestro módulo de pruebas:

```typescript
await TestBed.configureTestingModule({
  imports: [StubComponent],
  providers: [
    { provide: StubService, useValue: serviceStub }
  ]
})
.compileComponents();
```

La propiedad del componente `msg` depende del valor de la propiedad del servicio `isBusy`. Por lo tanto, necesitamos obtener una referencia al servicio en la suite de pruebas y proporcionar valores alternativos para esta propiedad en cada prueba. Podemos obtener la instancia inyectada de `StubService` utilizando el método `inject` de la clase `TestBed`:

```typescript
describe('status', () => {
  let service: StubService;

  beforeEach(() => {
    service = TestBed.inject(StubService);
  })
});
```

Pasamos el `StubService` real como parámetro al método `inject`, no la versión con stub que creamos. Modificar el valor del stub no afectará al servicio inyectado ya que nuestro componente utiliza una instancia del servicio real. El método `inject` le pide al inyector raíz de la aplicación el servicio solicitado. Si el servicio se proporcionó desde el inyector del componente, deberíamos obtenerlo del inyector del componente usando `fixture.debugElement.injector.get(StubService)`.

Ahora podemos escribir nuestras pruebas para verificar si la propiedad del componente `msg` se comporta correctamente durante el enlace de datos:

```typescript
describe('status', () => {
  let service: StubService;
  let msgDisplay: HTMLElement;

  beforeEach(() => {
    service = TestBed.inject(StubService);
    msgDisplay = fixture.nativeElement.querySelector('span');
  })

  it('should be on a mission', () => {
    service.isBusy = true;
    fixture.detectChanges();
    expect(msgDisplay.textContent).toContain('is on mission');
  });

  it('should be available', () => {
    service.isBusy = false;
    fixture.detectChanges();
    expect(msgDisplay.textContent).toContain('is available');
  });
});
```

Hemos eliminado la línea `fixture.detectChanges` de la sentencia `beforeEach` porque queremos activar la detección de cambios en nuestras pruebas por separado.

La creación de stubs para una dependencia no siempre es viable, especialmente cuando el inyector raíz no la proporciona. Se puede proporcionar un servicio en el nivel del inyector del componente. Proporcionar un stub mediante el proceso que analizamos anteriormente no tiene ningún efecto. Para abordar tal escenario, podemos usar el método `overrideComponent` de la clase `TestBed`:

```typescript
await TestBed.configureTestingModule({
  imports: [StubComponent],
  providers: [
    { provide: StubService, useValue: serviceStub }
  ]
})
.overrideComponent(StubComponent, {
  set: {
    providers: [
      { provide: StubService, useValue: serviceStub }
    ]
  }
})
.compileComponents();
```

El método `overrideComponent` acepta dos parámetros: el tipo de componente que proporciona el servicio y un objeto de metadatos de anulación. El objeto de metadatos contiene la propiedad `set`, que proporciona servicios al componente.

Supongamos que el componente que queremos probar contiene un componente secundario en su plantilla, como:

```typescript
@Component({
  selector: 'app-stub',
  template: `
    <span>{{ msg }}</span>
    <app-child></app-child>
  `
})
```

En el caso anterior, cuando probamos el `StubComponent`, también necesitábamos importar la clase TypeScript del componente `<app-child>` al configurar el módulo de prueba:

```typescript
await TestBed.configureTestingModule({
  imports: [StubComponent],
  providers: [
    { provide: StubService, useValue: serviceStub }
  ],
  imports: [ChildComponent]
})
```

La clase `ChildComponent` también puede tener otras dependencias. Proporcionar stubs para esas dependencias no es viable porque no es responsabilidad del componente bajo prueba. En su lugar, podemos crear una clase TypeScript stub para el componente e importarla al configurar el módulo de prueba:

```typescript
@Component({
  selector: 'app-child',
  template: ''
})
class ChildStubComponent {}
```

En el fragmento anterior, pasamos un string vacío en la propiedad `template` del componente porque no estamos interesados en la implementación interna del componente secundario.

> Si el componente secundario contiene propiedades y métodos que se utilizan al probar el componente principal, también debemos definirlos en el `ChildStubComponent`.

Alternativamente, para proporcionar un stub del componente, podemos pasar `NO_ERRORS_SCHEMA` del paquete npm `@angular/core` mientras configuramos el módulo de prueba:

```typescript
await TestBed.configureTestingModule({
  imports: [StubComponent],
  providers: [
    { provide: StubService, useValue: serviceStub },
  ],
  schemas: [NO_ERRORS_SCHEMA]
})
```

El fragmento anterior le indica a Angular que ignore cualquier componente que no se haya importado al módulo de prueba.

Hacer un stub de una dependencia es muy simple, pero no siempre es posible, como veremos en la siguiente sección.

##### Espiar el método de la dependencia
El uso de un stub no es la única forma de aislar la lógica en una prueba unitaria. No tenemos que reemplazar toda la dependencia, solo las partes que usa nuestro componente. Reemplazar ciertas partes significa que señalamos métodos específicos en la dependencia y les asignamos un espía (*spy*). Un espía puede responder lo que quieras, pero también puedes ver cuántas veces se llamó y con qué argumentos. Por lo tanto, un espía te brinda mucha más información sobre lo que está sucediendo.

Hay dos formas de configurar un espía en una dependencia:

1. Inyectar la dependencia real y espiar sus métodos.
2. Usar el método `createSpyObj` de Jasmine para crear una instancia de dependencia falsa. Luego podemos espiar los métodos de esta dependencia como lo haríamos con la real.

El primer caso es el más común en el desarrollo de Angular. Veamos cómo configurarlo. Considera el siguiente archivo `spy.component.ts`, que utiliza el servicio `Title` del framework Angular:

```typescript
import { Component, OnInit } from '@angular/core';
import { Title } from '@angular/platform-browser';

@Component({
  selector: 'app-spy',
  template: '{{ caption }}'
})
export class SpyComponent implements OnInit {
  caption = '';
  constructor(private title: Title) {}

  ngOnInit(): void {
    this.title.setTitle('My Angular app');
    this.caption = this.title.getTitle();
  }
}
```

El servicio `Title` interactúa con el título del documento HTML principal en una aplicación Angular.

No tenemos ningún control sobre el servicio `Title` ya que está integrado en el framework. Puede tener dependencias que desconocemos. Espiar sus métodos es la forma más fácil y segura de usarlo en nuestras pruebas. Lo inyectamos en el módulo de prueba usando el array `providers` y luego lo usamos en nuestra prueba, por ejemplo:

```typescript
it('should set the title', () => {
  const title = TestBed.inject(Title);
  const spy = spyOn(title, 'setTitle');
  component.ngOnInit();
  expect(spy).toHaveBeenCalledWith('My Angular app');
});
```

Usamos el método `spyOn` de Jasmine, que acepta dos parámetros: el objeto y su método específico a espiar. Lo usamos antes de llamar al método del componente `ngOnInit` para adjuntar el espía antes de activar el mecanismo de detección de cambios. La sentencia `expect` valida que el método `setTitle` se haya llamado con los argumentos correctos.

Nuestro componente también usa el método `getTitle` para obtener el título del documento. Podemos espiar directamente ese método y devolver datos simulados:

1. Primero, debemos definir el servicio `Title` como un objeto espía e inicializarlo pasando dos parámetros: el nombre del servicio y un array de los nombres de los métodos que utiliza actualmente el componente:

```typescript
const titleSpy = jasmine.createSpyObj('Title', [
  'getTitle',
  'setTitle'
]);
```

2. Luego adjuntamos un espía al método `getTitle` y devolvemos un título personalizado usando el método `returnValue` de Jasmine:

```typescript
titleSpy.getTitle.and.returnValue('My title');
```

3. Finalmente, agregamos la variable `titleSpy` en el array `providers` del módulo de prueba:

```typescript
await TestBed.configureTestingModule({
  imports: [SpyComponent],
  providers: [
    { provide: Title, useValue: titleSpy }
  ]
})
.compileComponents();
```

La prueba resultante debería verse como la siguiente:

```typescript
it('should get the title', async () => {
  const titleSpy = jasmine.createSpyObj('Title', [
    'getTitle',
    'setTitle'
  ]);
  titleSpy.getTitle.and.returnValue('My title');
  await TestBed.configureTestingModule({
    imports: [SpyComponent],
    providers: [
      { provide: Title, useValue: titleSpy }
    ]
  })
  .compileComponents();
  const fixture = TestBed.createComponent(SpyComponent);
  fixture.detectChanges();
  expect(fixture.nativeElement.textContent).toContain('My title');
});
```

Muy pocos servicios se comportan de forma tan sencilla y directa como el servicio `Title`, en el sentido de que son síncronos. La mayoría de las veces, son asíncronos y pueden devolver observables o promesas. En la siguiente sección, aprenderemos a probar dependencias asíncronas.

##### Pruebas de servicios asíncronos
Las utilidades de prueba de Angular proporcionan dos artefactos para abordar escenarios de pruebas asíncronas:

- **`waitForAsync`:** Un enfoque asíncrono para servicios de pruebas unitarias. Se combina con el método `whenStable` de la clase `ComponentFixture`.
- **`fakeAsync`:** Un enfoque síncrono para servicios de pruebas unitarias. Se utiliza en combinación con la función `tick`.

Ambos enfoques proporcionan aproximadamente la misma funcionalidad; solo difieren en cómo los usamos. Veamos cómo podemos usar cada uno mirando un ejemplo.

Considera el siguiente archivo `async.component.ts`:

```typescript
import { AsyncPipe } from '@angular/common';
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';
import { AsyncService } from '../async.service';

@Component({
  selector: 'app-async',
  imports: [AsyncPipe],
  template: `
    @for(item of items$ | async; track item) {
      <p>{{ item }}</p>
    }
  `
})
export class AsyncComponent implements OnInit {
  items$: Observable<string[]> | undefined;
  constructor(private asyncService: AsyncService) {}

  ngOnInit(): void {
    this.items$ = this.asyncService.getItems();
  }
}
```

Inyecta el `AsyncService` del archivo `async.service.ts` y llama a su método `getItems` dentro del método `ngOnInit`. Como podemos ver, el método `getItems` devuelve un observable de cadenas de texto. También introduce un ligero retraso para que el escenario parezca asíncrono:

```typescript
getItems(): Observable<string[]> {
  return of(items).pipe(delay(500));
}
```

La prueba unitaria consulta el elemento nativo del componente y comprueba si el valor del observable `items$` se muestra correctamente:

```typescript
it('should get data with waitForAsync', waitForAsync(async() => {
  fixture.detectChanges();
  await fixture.whenStable();
  fixture.detectChanges();
  const itemDisplay: HTMLElement[] = fixture.nativeElement.querySelectorAll('p');
  expect(itemDisplay.length).toBe(2);
}));
```

Envolvemos el cuerpo de la prueba dentro del método `waitForAsync` y llamamos al método `detectChanges` para activar la detección de cambios. Además, llamamos al método `whenStable`, que devuelve una promesa que se resuelve inmediatamente cuando se completa el observable `items$`. Cuando se resuelve la promesa, llamamos al método `detectChanges` nuevamente para activar el enlace de datos y consultar el DOM en consecuencia.

> El método `whenStable` también se utiliza cuando queremos probar un componente que contiene un formulario controlado por plantillas (*template-driven form*). La naturaleza asíncrona de este método hace que sea preferible utilizar formularios reactivos en nuestras aplicaciones Angular.

Un enfoque síncrono alternativo sería utilizar el método `fakeAsync` y escribir la misma prueba unitaria de la siguiente manera:

```typescript
it('should get items with fakeAsync', fakeAsync(() => {
  fixture.detectChanges();
  tick(500);
  fixture.detectChanges();
  const itemDisplay: HTMLElement[] = fixture.nativeElement.querySelectorAll('p');
  expect(itemDisplay.length).toBe(2);
}));
```

En el fragmento anterior, envolvimos el cuerpo de la prueba en un método `fakeAsync` y reemplazamos el método `whenStable` con la función `tick`. La función `tick` avanza el tiempo en 500 ms, que es el retraso virtual que introdujimos en el método `getItems` del `AsyncService`.

Probar componentes con servicios asíncronos a veces puede convertirse en una pesadilla. Aun así, cada uno de los enfoques descritos puede ayudarnos significativamente en esta tarea. Sin embargo, los componentes no solo tienen que ver con servicios, sino también con enlaces de entrada y salida. En la siguiente sección, aprenderemos a probar la API pública de un componente.

#### Pruebas con entradas y salidas (inputs y outputs)
Hasta ahora, hemos aprendido a probar componentes con propiedades simples y a abordar dependencias síncronas y asíncronas. Pero hay más en un componente que eso. Como aprendimos en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, un componente tiene una API pública que consta de entradas y salidas que también deben probarse.

Dado que queremos probar la API pública de un componente, tiene sentido probar cómo interactúa cuando se aloja desde otro componente. Probar un componente de este tipo se puede hacer de dos maneras:

1. Podemos verificar que nuestro enlace de entrada (*input binding*) esté configurado correctamente.
2. Podemos verificar que nuestro enlace de salida (*output binding*) se active correctamente y que lo que emite sea recibido.

Supongamos que tenemos el siguiente archivo `bindings.component.ts` con un enlace de entrada y salida:

```typescript
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-bindings',
  template: `
    <p>{{ title() }}</p>
    <button (click)="liked.emit()">Like!</button>
  `
})
export class BindingsComponent {
  title = input('');
  liked = output();
}
```

Antes de comenzar a escribir nuestras pruebas, debemos crear un componente anfitrión de prueba (*test host component*) dentro del archivo `bindings.component.spec.ts` que va a utilizar el componente bajo prueba:

```typescript
@Component({
  imports: [BindingsComponent],
  template: `
    <app-bindings [title]="testTitle" (liked)="isFavorite = true"></app-bindings>
  `
})
export class TestHostComponent {
  testTitle = 'My title';
  isFavorite = false;
}
```

En la fase de configuración, observa que el `ComponentFixture` es del tipo `TestHostComponent`:

```typescript
describe('BindingsComponent', () => {
  let component: TestHostComponent;
  let fixture: ComponentFixture<TestHostComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [TestHostComponent]
    })
    .compileComponents();
    fixture = TestBed.createComponent(TestHostComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

Nuestras pruebas unitarias validarán el comportamiento de `BindingsComponent` al interactuar con `TestHostComponent`.

La primera prueba verifica si el enlace de entrada a la propiedad `title` se ha aplicado correctamente:

```typescript
it('should display the title', () => {
  const titleDisplay: HTMLElement = fixture.nativeElement.querySelector('p');
  expect(titleDisplay.textContent).toEqual(component.testTitle);
});
```

La segunda prueba valida si la propiedad `isFavorite` está conectada correctamente con el evento de salida `liked`:

```typescript
it('should emit the liked event', () => {
  const button: HTMLButtonElement = fixture.nativeElement.querySelector('button');
  button.click();
  expect(component.isFavorite).toBeTrue();
});
```

En la prueba anterior, consultamos el DOM para el elemento `<button>` usando la propiedad `nativeElement` de la clase `ComponentFixture`. Luego, hacemos clic en él para que se emita el evento de salida. Alternativamente, podríamos haber usado la propiedad `debugElement` para encontrar el botón y usar su método `triggerEventHandler` para hacer clic en él:

```typescript
it('should emit the liked event using debugElement', () => {
  const buttonDe = fixture.debugElement.query(By.css('button'));
  buttonDe.triggerEventHandler('click');
  expect(component.isFavorite).toBeTrue();
});
```

En la prueba anterior, usamos el método `query`, que acepta una función de predicado como parámetro. El predicado utiliza el método `css` de la clase `By` para ubicar un elemento por su selector CSS.

> Como aprendimos en la sección *Introducción a las pruebas unitarias en Angular*, el `debugElement` es agnóstico al framework. Si estás seguro de que tus pruebas solo se ejecutarán en un navegador, debes optar por la propiedad `nativeElement`.

El método `triggerEventHandler` acepta el nombre del evento que queremos activar como parámetro; en este caso, es el evento `click`.

Podríamos haber evitado mucho código si solo hubiéramos probado el `BindingsComponent`, lo cual aún habría sido válido. Pero habríamos perdido la oportunidad de probarlo como un escenario del mundo real. La API pública de un componente está diseñada para ser utilizada por otros componentes, por lo que debemos probarla de esta manera.

Actualmente, el botón que usamos en la plantilla de `BindingsComponent` es un elemento HTML nativo `<button>`. Si el botón fuera un componente de botón de Angular Material, podríamos usar un enfoque alternativo para interactuar con él, que es el tema de la siguiente sección.

#### Pruebas con un Component Harness
La librería Angular CDK, el núcleo de Angular Material, contiene un conjunto de utilidades que permiten que una prueba interactúe con un componente a través de una API de prueba pública. Las utilidades de prueba de Angular CDK nos permiten acceder a los componentes de Angular Material sin depender de su implementación interna utilizando un arnés de componentes (*component harness*).

El proceso de probar un componente de Angular mediante un arnés consta de las siguientes partes:

- **`@angular/cdk/testing`:** El paquete npm que contiene la infraestructura para interactuar con un arnés de componentes.
- **Entorno de pruebas (*Testing environment*):** El entorno en el que se cargará la prueba del arnés de componentes. Angular CDK contiene un entorno de pruebas integrado para pruebas unitarias con Karma. También proporciona un amplio conjunto de herramientas que permiten a los desarrolladores crear entornos de pruebas personalizados.
- **Arnés de componente (*Component harness*):** Una clase que le da al desarrollador acceso a la instancia de un componente en el DOM del navegador.

Para aprender a usar los arneses de componentes, convertiremos el elemento `<button>` de `BindingsComponent` en un botón de Angular Material:

```typescript
import { Component, input, output } from '@angular/core';
import { MatButton } from '@angular/material/button';

@Component({
  selector: 'app-bindings',
  imports: [MatButton],
  template: `
    <p>{{ title() }}</p>
    <button mat-button (click)="liked.emit()">Like!</button>
  `
})
```

> El fragmento anterior asume que has agregado la librería Angular Material al proyecto en el que estás trabajando.

Para comenzar a usar un arnés de componente de Angular CDK, debemos importar los siguientes artefactos del espacio de nombres `@angular/cdk/testing`:

```typescript
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
import { MatButtonHarness } from '@angular/material/button/testing';
```

En el fragmento anterior, hemos agregado las siguientes clases:

- **`TestbedHarnessEnvironment`:** Representa el entorno de prueba para ejecutar pruebas unitarias con Karma.
- **`MatButtonHarness`:** El arnés de componente para el componente de botón de Angular Material. Casi todos los componentes de la librería Angular Material tienen un arnés de componente correspondiente que podemos usar.

> Si eres autor de una librería de componentes, Angular CDK proporciona todas las herramientas necesarias para crear arneses para tus componentes de interfaz de usuario.

Una vez que hayamos terminado de importar todos los artefactos necesarios, podemos escribir nuestra prueba:

```typescript
it('should emit the liked event using harness', async () => {
  const loader = TestbedHarnessEnvironment.loader(fixture);
  const buttonHarness = await loader.getHarness(MatButtonHarness);
  await buttonHarness.click();
  expect(component.isFavorite).toBeTrue();
});
```

En la prueba anterior, el método `loader` del entorno de prueba acepta la instancia `ComponentFixture` del componente actual como parámetro y devuelve un objeto `HarnessLoader`. La abstracción que proporciona un arnés de Angular CDK se basa en el concepto de que opera sobre el fixture del componente, que es una capa de abstracción sobre el elemento DOM real.

Envolvemos el cuerpo de la prueba dentro de una función asíncrona (`async`) porque los arneses de componentes se basan en promesas. Usamos el método `getHarness` del cargador de arneses para cargar el arnés específico para el componente de botón. Finalmente, llamamos al método `click` del arnés del componente de botón para activar el evento de clic del botón.

> No necesitamos llamar al método `detectChanges` porque el arnés de componentes de Angular CDK activa la detección de cambios automáticamente.

El arnés de componentes es una potente herramienta de Angular CDK que garantiza que interactuemos con los componentes de forma abstracta y segura durante las pruebas.

Hemos discutido muchas formas de probar un componente con una dependencia. Ahora, es el momento de aprender cómo probar la dependencia en sí.

---

### Sección 13.6: Pruebas de servicios

Como aprendimos en el *Capítulo 5: Gestión de tareas complejas con servicios*, un servicio puede inyectar otros servicios. Probar un servicio independiente es bastante sencillo: obtenemos una instancia del inyector y luego comenzamos a consultar sus propiedades y métodos públicos.

> Solo nos interesa probar la API pública de un servicio, que es la interfaz que utilizan los componentes y otros artefactos. Las propiedades y métodos privados no tienen ningún valor cuando se prueban porque representan la implementación interna del servicio.

Hay dos tipos diferentes de pruebas que podemos realizar en un servicio:

- Probar operaciones síncronas y asíncronas, como un método que devuelve un array simple o uno que devuelve un observable.
- Probar servicios con dependencias, como un método que realiza solicitudes HTTP.

En las siguientes secciones, profundizaremos en cada uno de ellos.

#### Pruebas de métodos síncronos/asíncronos
Cuando creamos un servicio de Angular mediante Angular CLI, también crea un archivo de prueba correspondiente. Considera el siguiente archivo `async.service.spec.ts`, que es el archivo de prueba para el `AsyncService` que usamos anteriormente:

```typescript
import { TestBed } from '@angular/core/testing';
import { AsyncService } from './async.service';

describe('AsyncService', () => {
  let service: AsyncService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(AsyncService);
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });
});
```

El `AsyncService` no depende de nada. También se proporciona con el inyector raíz de la aplicación Angular, por lo que pasa un objeto vacío al método `configureTestingModule`. Podemos obtener una instancia del servicio que probamos utilizando el método `inject` de la clase `TestBed`.

La primera prueba que podemos escribir es bastante sencilla ya que llama al método `setItems` e inspecciona su resultado:

```typescript
it('should set items', () => {
  const result = service.setItems('Camera');
  expect(result.length).toBe(3);
});
```

Escribir una prueba para métodos síncronos, como en el caso anterior, suele ser relativamente fácil; sin embargo, las cosas son diferentes cuando queremos probar un método asíncrono como el siguiente.

Esta segunda prueba es un poco complicada porque involucra un observable. Necesitamos suscribirnos al método `getItems` e inspeccionar el valor tan pronto como se complete el observable:

```typescript
it('should get items', (done: DoneFn) => {
  service.getItems().subscribe(items => {
    expect(items.length).toBe(2);
    done();
  });
});
```

El ejecutor de pruebas Karma no sabe cuándo se completará un observable, por lo que proporcionamos la función `done` para señalar que el observable se ha completado y ahora podemos verificar la sentencia `expect`.

#### Pruebas de servicios con dependencias
Probar servicios con dependencias es similar a probar componentes con dependencias. Cada método que vimos en la sección *Pruebas de componentes* se puede aplicar de manera similar; sin embargo, seguimos un enfoque diferente al probar un servicio que inyecta el servicio `HttpClient`.

Considera el siguiente archivo `deps.service.ts` que utiliza el cliente HTTP:

```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class DepsService {
  constructor(private http: HttpClient) { }

  getItems() {
    return this.http.get('http://some.url');
  }

  addItem(item: string) {
    return this.http.post('http://some.url', { name: item });
  }
}
```

Las utilidades de prueba de Angular proporcionan dos artefactos para simular solicitudes HTTP en pruebas unitarias: la función `provideHttpClientTesting`, que proporciona un cliente HTTP para pruebas, y el `HttpTestingController`, que simula el servicio `HttpClient`. Podemos importar ambos desde el espacio de nombres `@angular/common/http/testing`:

```typescript
import { TestBed } from '@angular/core/testing';
import { provideHttpClient } from '@angular/common/http';
import { HttpTestingController, provideHttpClientTesting } from '@angular/common/http/testing';
import { DepsService } from './deps.service';

describe('DepsService', () => {
  let service: DepsService;
  let httpTestingController: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        provideHttpClient(),
        provideHttpClientTesting()
      ]
    });
    service = TestBed.inject(DepsService);
    httpTestingController = TestBed.inject(HttpTestingController);
  });
});
```

Nuestras pruebas no deben realizar una solicitud HTTP real. Solo necesitan validar que se realizará con las opciones correctas. La siguiente es la primera prueba que valida el método `getItems`:

```typescript
it('should get items', () => {
  service.getItems().subscribe();
  const req = httpTestingController.expectOne('http://some.url');
  expect(req.request.method).toBe('GET');
});
```

En la prueba anterior, creamos una solicitud falsa usando el método `expectOne` de `HttpTestingController` que toma una URL como argumento. El método `expectOne` crea un objeto de solicitud simulada y afirma que solo se realiza una solicitud a la URL específica. Después de haber creado nuestra solicitud, podemos validar que su método sea `GET`.

Seguimos un enfoque similar al probar el método `addItem`, excepto que debemos asegurarnos de que el cuerpo de la solicitud contenga los datos correctos:

```typescript
it('should add an item', () => {
  service.addItem('Camera').subscribe();
  const req = httpTestingController.expectOne('http://some.url');
  expect(req.request.method).toBe('POST');
  expect(req.request.body).toEqual({ name: 'Camera' });
});
```

Después de cada prueba, nos aseguramos de que no haya solicitudes no coincidentes pendientes utilizando el método `verify` dentro de un bloque `afterEach`:

```typescript
afterEach(() => {
  httpTestingController.verify();
});
```

En la siguiente sección, continuaremos nuestro viaje por el mundo de las pruebas aprendiendo a probar un pipe.

---

### Sección 13.7: Pruebas de pipes

Como aprendimos en el *Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas*, un pipe es una clase TypeScript que implementa la interfaz `PipeTransform`. Expone un método `transform`, que suele ser síncrono, lo que significa que es muy fácil de probar.

Considera el archivo `list.pipe.ts` que contiene un pipe que convierte una cadena separada por comas en una lista:

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'list'
})
export class ListPipe implements PipeTransform {
  transform(value: string): string[] {
    return value.split(',');
  }
}
```

Escribir una prueba es simple. Lo único que tenemos que hacer es crear una instancia de la clase `ListPipe` y verificar el resultado del método `transform` con algunos datos simulados:

```typescript
it('should return an array', () => {
  const pipe = new ListPipe();
  expect(pipe.transform('A,B,C')).toEqual(['A', 'B', 'C']);
});
```

Las utilidades de prueba de Angular no participan al probar un pipe. Creamos una instancia de la clase de pipe y podemos comenzar a llamar al método `transform`.

Las directivas de Angular son artefactos que tal vez no creemos con mucha frecuencia ya que la colección incorporada que proporciona el framework es más que suficiente; sin embargo, si creamos directivas personalizadas, también debemos probarlas. En la siguiente sección, aprenderemos cómo lograr esto.

---

### Sección 13.8: Pruebas de directivas

Las directivas suelen ser bastante sencillas en su forma general, siendo componentes sin una vista adjunta. El hecho de que las directivas suelan funcionar con componentes nos da una buena idea de cómo proceder al probarlas.

Considera el archivo `copyright.directive.ts` que creamos en el *Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas*:

```typescript
import { Directive, ElementRef } from '@angular/core';

@Directive({
  selector: '[appCopyright]'
})
export class CopyrightDirective {
  constructor(el: ElementRef) {
    const currentYear = new Date().getFullYear();
    const targetEl: HTMLElement = el.nativeElement;
    targetEl.classList.add('copyright');
    targetEl.textContent = `Copyright ©${currentYear} All Rights Reserved`;
  }
}
```

Una directiva generalmente se usa con un componente, por lo que tiene sentido realizar una prueba unitaria mientras se usa en un componente. Creemos un componente anfitrión de prueba y agreguémoslo al array `imports` del módulo de prueba:

```typescript
@Component({
  imports: [CopyrightDirective],
  template: '<span appCopyright></span>'
})
class TestHostComponent { }
```

Ahora podemos escribir nuestras pruebas que verifican si el elemento `<span>` contiene la clase `copyright` y muestra el año actual en su propiedad `textContent`:

```typescript
describe('CopyrightDirective', () => {
  let container: HTMLElement;

  beforeEach(() => {
    const fixture = TestBed.configureTestingModule({
      imports: [TestHostComponent]
    })
    .createComponent(TestHostComponent);
    container = fixture.nativeElement.querySelector('span');
  });

  it('should have copyright class', () => {
    expect(container.classList).toContain('copyright');
  });

  it('should display copyright details', () => {
    expect(container.textContent).toContain(new Date().getFullYear().toString());
  });
});
```

Así de simple puede ser probar una directiva. Las conclusiones clave son que necesitas un componente para colocar la directiva y que implícitamente pruebas la directiva utilizando el componente.

En la siguiente sección, aprenderemos a probar formularios reactivos.

---

### Sección 13.9: Pruebas de formularios

Como vimos en el *Capítulo 10: Recopilación de datos del usuario con Formularios*, los formularios son parte integral de una aplicación Angular. Es raro que una aplicación Angular no tenga al menos un formulario simple, como un formulario de búsqueda. En este capítulo, nos centraremos en los formularios reactivos porque son más fáciles de probar que los formularios controlados por plantillas.

Considera el siguiente archivo `search.component.ts`:

```typescript
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators, ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-search',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="searchForm" (ngSubmit)="search()">
      <input type="text" formControlName="searchText">
      <button type="submit" [disabled]="searchForm.invalid">Search</button>
    </form>
  `
})
export class SearchComponent {
  searchForm = new FormGroup({
    searchText: new FormControl('', Validators.required)
  });

  search() {
    if(this.searchForm.valid) {
      console.log('You searched for: ' + this.searchForm.controls.searchText.value);
    }
  }
}
```

En el componente anterior, podemos escribir nuestras pruebas unitarias para verificar que:

- El valor del control de formulario `searchText` se pueda establecer correctamente.
- El botón de búsqueda (*Search*) esté deshabilitado cuando el formulario no sea válido.
- Se llame al método `console.log` cuando el formulario sea válido y el usuario haga clic en el botón de búsqueda.

Para probar un formulario reactivo, primero debemos importar `ReactiveFormsModule` en el módulo de prueba:

```typescript
await TestBed.configureTestingModule({
  imports: [SearchComponent, ReactiveFormsModule]
})
.compileComponents();
```

Para la primera prueba, debemos afirmar si el valor se propaga al control de formulario `searchText` cuando escribimos algo en el control de entrada:

```typescript
it('should set the searchText', () => {
  const input: HTMLInputElement = fixture.nativeElement.querySelector('input');
  input.value = 'Angular';
  input.dispatchEvent(new CustomEvent('input'));
  expect(component.searchForm.controls.searchText.value).toBe('Angular');
});
```

En la prueba anterior, usamos el método `querySelector` de la propiedad `nativeElement` para encontrar el elemento HTML `<input>` y establecer su valor. Pero esto por sí solo no será suficiente para que el valor se propague al control del formulario. El framework Angular no sabrá si el valor del elemento HTML `<input>` ha cambiado hasta que activemos el evento DOM `input` en ese elemento. Estamos utilizando el método `dispatchEvent` para desencadenar el evento, que acepta un solo método como parámetro que apunta a una instancia de la clase `CustomEvent`.

Ahora que estamos seguros de que el control de formulario `searchText` está conectado correctamente, podemos usarlo para escribir las pruebas restantes:

```typescript
it('should disable search button', () => {
  const button: HTMLButtonElement = fixture.nativeElement.querySelector('button');
  component.searchForm.controls.searchText.setValue('');
  expect(button.disabled).toBeTrue();
});

it('should log to the console', () => {
  const button: HTMLButtonElement = fixture.nativeElement.querySelector('button');
  const spy = spyOn(console, 'log');
  component.searchForm.controls.searchText.setValue('Angular');
  fixture.detectChanges();
  button.click();
  expect(spy).toHaveBeenCalledWith('You searched for: Angular');
});
```

Ten en cuenta que en la segunda prueba, establecemos el valor del control de formulario `searchText` y luego llamamos al método `detectChanges` para que el botón esté habilitado. Al hacer clic en el botón, se activa el evento `submit` del formulario y finalmente podemos afirmar la expectativa de nuestra prueba.

En los casos en que un formulario tiene muchos controles, no es conveniente consultarlos dentro de nuestras pruebas. Alternativamente, podemos crear un objeto de página (*Page Object*) que se encargue de consultar elementos HTML y espiar servicios:

```typescript
class Page {
  get searchText() {
    return this.query<HTMLInputElement>('input');
  }
  get submitButton() {
    return this.query<HTMLButtonElement>('button');
  }
  private query<T>(selector: string): T {
    return fixture.nativeElement.querySelector(selector);
  }
}
```

Luego podemos crear una instancia del objeto `Page` en la sentencia `beforeEach` y acceder a sus propiedades y métodos en nuestras pruebas.

Como hemos visto, los formularios reactivos son muy fáciles de probar ya que el modelo de formulario es la única fuente de verdad. En la siguiente sección, aprenderemos cómo probar partes de una aplicación Angular que usan el enrutador.

---

### Sección 13.10: Pruebas del enrutador

Probar el código que interactúa con el enrutador de Angular podría ser fácilmente un capítulo separado. En esta sección, nos centraremos en los siguientes conceptos del enrutador:

- Componentes enrutados y de enrutamiento
- Guards
- Resolvers

Veamos primero cómo probar componentes enrutados y de enrutamiento.

#### Componentes enrutados y de enrutamiento
Un componente enrutado (*routed component*) es un componente que se activa cuando navegamos a una ruta de aplicación específica. Considera el siguiente archivo `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';
import { RoutedComponent } from './routed/routed.component';

export const routes: Routes = [
  {
    path: 'routed',
    component: RoutedComponent
  }
];
```

La clase `RoutedComponent` se define en el siguiente archivo `routed.component.ts`:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-routed',
  template: '<span>{{ title }}</span>'
})
export class RoutedComponent {
  title = 'My routed component';
}
```

El componente anterior vincula el valor de la propiedad del componente `title` a un elemento HTML `<span>`. La prueba que escribiremos afirmará si el enlace funciona correctamente.

Las pruebas del enrutador de Angular se basan en el enfoque de arnés de componentes que conocimos en la sección *Pruebas de componentes*. Expone la clase `RouterTestingHarness`, que contiene varios métodos de utilidad para trabajar con componentes enrutados en pruebas:

```typescript
import { RouterTestingHarness } from '@angular/router/testing';
```

Antes de que podamos comenzar a probar un componente enrutado, debemos registrar la configuración de enrutamiento de la aplicación en el módulo de prueba:

```typescript
beforeEach(async () => {
  await TestBed.configureTestingModule({
    providers: [provideRouter(routes)]
  })
  .compileComponents();
  fixture = TestBed.createComponent(RoutedComponent);
  component = fixture.componentInstance;
  fixture.detectChanges();
});
```

En el proceso de configuración anterior, proporcionamos la configuración de enrutamiento de la aplicación como en el archivo `app.config.ts`.

Ya hemos aprendido que podemos consultar el DOM del elemento HTML nativo desde la clase `ComponentFixture`. Cuando se carga un componente utilizando el enrutador, utilizamos la propiedad `routeNativeElement` de la clase `RouterTestingHarness`:

```typescript
it('should display a span element', async () => {
  const harness = await RouterTestingHarness.create();
  await harness.navigateByUrl('/routed');
  expect(harness.routeNativeElement?.querySelector('span')?.textContent).toBe('My routed component');
});
```

La prueba anterior se divide en los siguientes pasos:

1. Usamos el método `create` de `RouterTestingHarness` para crear un nuevo arnés de enrutamiento para nuestro componente.
2. Navegamos a la ruta registrada utilizando el método `navigateByUrl`. De acuerdo con la configuración de enrutamiento de la aplicación, la URL `/routed` activará el componente bajo prueba.
3. Usamos métodos de consulta estándar de la propiedad `routeNativeElement` para verificar que el elemento HTML `<span>` muestre el texto correcto.

> La clase `RouterTestingHarness` también contiene la propiedad `routeDebugElement`, que funciona entre plataformas de manera similar a la propiedad `debugElement` de la clase `ComponentFixture`.

Un componente de enrutamiento (*routing component*) es un componente que se utiliza para navegar a otro componente en una aplicación Angular. Por lo general, implica llamar al método `navigate` del servicio `Router` de la siguiente manera:

```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-routed',
  template: '<span>{{ title }}</span>'
})
export class RoutedComponent {
  title = 'My routed component';
  constructor(private router: Router) {}

  goBack() {
    this.router.navigate(['/']);
  }
}
```

De acuerdo con el fragmento anterior, nuestra prueba debe verificar que el enrutador navegará a la ruta raíz cuando llamemos al método `goBack`:

```typescript
it('should navigate to the root path', () => {
  component.goBack();
  expect(TestBed.inject(Router).url).toBe('/');
});
```

En la prueba anterior, usamos el método `inject` de la clase `TestBed` para obtener una referencia al servicio `Router`. Luego accedemos a la propiedad `url` para verificar que el proceso de navegación se haya completado correctamente.

En la siguiente sección, aprenderemos a probar guards del enrutador.

#### Guards
Aprendimos en el *Capítulo 9: Navegación a través de aplicaciones con enrutamiento* que los guards del enrutador son funciones simples.

Considera el siguiente guard que verifica el estado de autenticación de un usuario:

```typescript
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = () => {
  const authService = inject(AuthService);
  const router = inject(Router);
  if (authService.isLoggedIn) {
    return true;
  }
  return router.parseUrl('/');
};
```

En el guard anterior, verificamos la propiedad `isLoggedIn` de la siguiente clase `AuthService`:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  isLoggedIn = false;
}
```

Decidimos mantener la clase `AuthService` simple y concentrarnos en la lógica del guard de autenticación.

Si la propiedad `isLoggedIn` es `true`, el guard también devuelve `true`. De lo contrario, ejecuta el método `parseUrl` del servicio `Router` para redirigir a los usuarios a la ruta raíz.

Angular CLI ha creado la siguiente prueba unitaria para el guard:

```typescript
import { TestBed } from '@angular/core/testing';
import { CanActivateFn } from '@angular/router';
import { authGuard } from './auth.guard';

describe('authGuard', () => {
  const executeGuard: CanActivateFn = (...guardParameters) =>
    TestBed.runInInjectionContext(() => authGuard(...guardParameters));

  beforeEach(() => {
    TestBed.configureTestingModule({});
  });

  it('should be created', () => {
    expect(executeGuard).toBeTruthy();
  });
});
```

En el fragmento anterior, la variable `executeGuard` encapsula la creación de la función `authGuard`. Utiliza el método `runInInjectionContext` de la clase `TestBed` para permitir la inyección de los servicios requeridos mediante el método `inject`.

Para crear pruebas unitarias que validen el uso del guard de autenticación, debemos ejecutar los siguientes pasos:

1. Modifica la sentencia de importación del paquete npm `@angular/router` de la siguiente manera:

```typescript
import { ActivatedRouteSnapshot, CanActivateFn, Router, RouterStateSnapshot } from '@angular/router';
```

2. Agrega la siguiente sentencia de importación:

```typescript
import { AuthService } from './auth.service';
```

3. Crea las siguientes variables que corresponden a los servicios inyectados:

```typescript
let authService: AuthService;
let routerSpy: jasmine.SpyObj<Router>;
```

4. Inicializa las variables anteriores en la sentencia `beforeEach` de la suite de pruebas:

```typescript
beforeEach(() => {
  routerSpy = jasmine.createSpyObj('Router', ['parseUrl']);
  TestBed.configureTestingModule({
    providers: [
      { provide: Router, useValue: routerSpy }
    ]
  });
  authService = TestBed.inject(AuthService);
});
```

En el fragmento anterior, usamos el método `createSpyObj` para crear un objeto espía para el servicio `Router` y proporcionarlo al módulo de prueba. Además, obtenemos la instancia de la clase real `AuthService` utilizando el método `inject` de la clase `TestBed` porque es un servicio simple sin dependencias.

5. La primera prueba unitaria debe afirmar que la ejecución del guard devuelve `true` cuando el usuario está autenticado:

```typescript
it('should return true', () => {
  authService.isLoggedIn = true;
  expect(executeGuard({} as ActivatedRouteSnapshot, {} as RouterStateSnapshot)).toBeTrue();
});
```

Pasamos un objeto vacío para los parámetros `ActivatedRouteSnapshot` y `RouterStateSnapshot` porque son innecesarios en el guard.

6. La segunda prueba unitaria debe verificar que la ejecución del guard provoque una redirección a la ruta raíz:

```typescript
it('should redirect', () => {
  authService.isLoggedIn = false;
  executeGuard({} as ActivatedRouteSnapshot, {} as RouterStateSnapshot);
  expect(routerSpy.parseUrl).toHaveBeenCalledWith('/');
});
```

En la siguiente sección, aprenderemos a probar resolvers de guards.

#### Resolvers
Los resolvers del enrutador son funciones simples de un tipo específico similar a los guards. El escenario más común al probar resolvers es verificar que los datos devueltos sean correctos.

Considera el siguiente resolver, que devuelve una lista de elementos:

```typescript
import { ResolveFn } from '@angular/router';
import { AsyncService } from './async.service';
import { inject } from '@angular/core';

export const itemsResolver: ResolveFn<string[]> = () => {
  const asyncService = inject(AsyncService);
  return asyncService.getItems();
};
```

El resolver utiliza el `AsyncService` que vimos anteriormente, que devuelve un observable de elementos utilizando el método `getItems`.

Angular CLI creará inicialmente el siguiente archivo de prueba unitaria al estructurar el resolver:

```typescript
import { TestBed } from '@angular/core/testing';
import { ResolveFn } from '@angular/router';
import { itemsResolver } from './items.resolver';

describe('itemsResolver', () => {
  const executeResolver: ResolveFn<boolean> = (...resolverParameters) =>
    TestBed.runInInjectionContext(() => itemsResolver(...resolverParameters));

  beforeEach(() => {
    TestBed.configureTestingModule({});
  });

  it('should be created', () => {
    expect(executeResolver).toBeTruthy();
  });
});
```

En el fragmento anterior, la variable `executeResolver` encapsula la creación de la función `itemsResolver`, de forma similar a como lo hace con los guards. También utiliza el método `runInInjectionContext` de la clase `TestBed` para permitir la inyección de los servicios requeridos.

La lógica de nuestro resolver es muy simple, por lo que debemos escribir una sola prueba unitaria:

1. Modifica la sentencia de importación del paquete npm `@angular/router` de la siguiente manera:

```typescript
import { ActivatedRouteSnapshot, ResolveFn, RouterStateSnapshot } from '@angular/router';
```

2. Agrega la siguiente sentencia de importación:

```typescript
import { Observable } from 'rxjs';
```

3. Cambia el tipo de la variable `executeResolver` a `ResolveFn<string[]>` para que coincida con la firma de la función `itemsResolver`:

```typescript
const executeResolver: ResolveFn<string[]> = (...resolverParameters) =>
  TestBed.runInInjectionContext(() => itemsResolver(...resolverParameters));
```

4. Escribe la siguiente prueba unitaria:

```typescript
it('should return items', () => {
  (executeResolver({} as ActivatedRouteSnapshot, {} as RouterStateSnapshot) as Observable<string[]>).subscribe(items => {
    expect(items).toEqual(['Microphone', 'Keyboard']);
  })
});
```

Para verificar que el resolver devuelva los datos correctos, debemos suscribirnos a la función `executeResolver`.

En esta sección, aprendimos cómo realizar pruebas unitarias de algunas características importantes del enrutador de Angular.

---

### Sección 13.11: Resumen

Estamos al final de nuestro viaje de pruebas, y ha sido largo pero emocionante. En este capítulo, vimos la importancia de introducir pruebas unitarias en nuestras aplicaciones Angular, la estructura básica de una prueba unitaria y el proceso de configuración de Jasmine para nuestras pruebas.

También aprendimos a escribir pruebas sólidas para nuestros componentes, directivas, pipes y servicios. También analizamos cómo probar los formularios reactivos de Angular y el enrutador.

Este capítulo de pruebas unitarias casi ha completado el rompecabezas de la creación de una aplicación Angular completa. Solo queda la última pieza, que es importante porque las aplicaciones web están destinadas en última instancia a la web. Por lo tanto, en el próximo capítulo, aprenderemos cómo generar una compilación de producción para una aplicación Angular y desplegarla para compartirla con el resto del mundo.
