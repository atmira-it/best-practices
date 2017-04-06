# Buenas prácticas para Java 

> Esta guia ha sido desarrollada para cualquier versión de Java

## Tabla de contenidos

[Conceptos basicos] (# Conceptos basicos "Conceptos basicos")

# Conceptos basicos

Al tratarse de proyectos maven generados a traves de arquetipos tiene una estructura de directorios reconocida, esto significa que debemos situar nuestras clases, tests o recursos en un lugar en concreto, 
para que luego Maven sea capaz de tratarlos correctamente.

Maven tiene cuatro carpetas fuente por defecto:

* src/main/java : donde guardaremos nuestras clases java fuente. Debajo de esta carpeta situaremos nuestras clases en distintos paquetes.
* src/main/resources : aquí almacenaremos los recursos (ficheros xml, ficheros de propiedades, imagenes, …) que pueda necesitar las clases java de nuestro proyecto. Igualmente aquí tienen que ir los ficheros de configuración de Spring o Hibernate por ejemplo.
* src/test/java : en dicha carpeta se guardan las clases de test que se encargarán de probar el correcto funcionamiento de nuestra aplicación. Aquí por ejemplo podemos guardar nuestros test unitarios de JUnit.
* src/test/resources : en esta carpeta guardamos los recursos que usan los test.

Pongamos por ejemplo como sería la estructura de un proyecto maven (simplificada).

```
src/
├── main/
│   ├── java/
│   │   └── com.bankia.ioi.activarContrato.carteraGestionada.v1_0.flow/
│   │   	├── action/
│   │   	│   └── ...
│   │   	├── assamble/
│   │   	│   └── ...
│   │   	├── constantes/
│   │   	│   └── ...
│   │   	├── dto/
│   │   	│   └── ...
│   │   	├── enrichment/
│   │   	│   └── ...
│   │   	└── utilidades/
│   │   	    └── ...
│   ├── resources/
│   │   └── META-INF/
│   │   	├── spring/
│   │   	│   └── ...
│   │   	└── views/
│   │   	    └── ...
│   └─ webapp/
│       ├── WEB-INF/
│       │   └── ...
│       └── WEB-INF-LOCAL/
│           └── ...
│   
└── test/
    ├── java/
    │   └── ...
    └── resources/
        └── ...
 
```

## Lazy Initialitation

* Inicializar los objetos solo cuando sea necesario, de esa manera evitaremos sobrecargar la memoria de java.

```java
public class Paises {
 
    private List paises;
 
    public List getPaises() {
        //se inicializa solo cuando es requerido
        if(null == paises) {
            paises = new ArrayList();
        }
        return paises;
    }
}

```

## Valores nulo

Para un mejor rendimiento de la aplicación y evitar un montón de tiempo cuando hagas pruebas para valores nulos, es mejor devolver las colecciones o arreglos vacíos en lugar de nulos, ojo, siempre hay que controlar las excepciones.

## Nomenclatura

Cosas a tener en cuenta cuando vayamos a nombrar algo en Java.

* Los nombres de paquetes debería estar totalmente en minúsculas.
* Los nombres de constantes de clases deberían escribirse todo en mayúsculas con las palabras separadas por subrayados ("_"). Todas serán declaradas como public static final
```java
	public static final String PROPERTY_URL_SERVICIO = "urlServicio";
```
	
* Los nombres que representan métodos deben ser verbos y escribirse con mayúsculas y minúsculas iniciando con minúscula.
* Utilizar JavaDoc para los métodos, además de ir insertando comentarios cada vez que creas que la lógica de un método puede llevar a confusión para los programadores que vengan detrás.
* Las declaraciones de clase deberían organizarse. Esto debería hacerse de la siguiente manera:
	** Documentación de la Clase/Interface.-**
	** Sentencia class o interface.**
	** Variables de clase (estáticas) en el orden public, protected, package (sin modificador de acceso), privadas.**
	** Variables de instancia en el orden public, protected, package (sin modificador de acceso), private.**
	** Constructores.**
	** Métodos (sin orden específico). **

## Declaracion de variables 

* Nunca declarar variables de instancia publica, por seguridad, mejor declararla como privada y crear getters y setters.

```java
/* Forma correcta y segura de declarar una variable */
public class vehiculo{

	private String marca;
	
	public String getMarca(){...}
	
	public void setMarca(String marca){...}
	
}

/* Forma incorrecta y poco segura, ya que se tiene acceso desde cualquier lado de la aplicación */
public class vehiculo{

	public String marca;
		
}

```
* Las variables booleanas negadas deben evitarse
``` java
boolean isError; // NOT: isNotError 
boolean isFound; // NOT: isNotFound
```
El problema surge cuando el operador lógico no se usa y surge una doble negación. No es muy claro inicialmente el significado de !isNotError.

# Reglas específicas de estilo

## Modules

### Nombres únicos

Pon nombres únicos a los Modules e incluye el nombre del padre en sub-módulos. Ie: 'twitter' como Module principal, 'twitter.profile' como Module de la aplicación.

> Evita colisión de nombres, facilita comprender la jerarquía y el ubicar los archivos en la estructura.

### Declaración

No asignes a una variable la declaración de un Module, y separa la declaración de dependencias en un array.

```javascript
// ...app.module.js
/* Evitar */
var app = angular.module('twitter', [
	'ngAnimate',
	'twitter.core'
	'twitter.shared',
	'twitter.modules'
]);
app.config(config);

function config() {
	...
}

/* OK */
var dependencies = [
	'ngAnimate',
	'twitter.core'
	'twitter.shared',
	'twitter.modules'
];
angular.module('twitter', dependencies);
angular.module('twitter').config(config);

function config() {
	
}
```

## Components

### controllerAs



## Controllers

### vm = this

Declara en el Controller una variable `var vm = this;` a la que asignar las diferentes funciones y variables públicas. Mientras se mantenga la regla de Controllers livianos será facil visualizar el Controller al debuggear sin necesidad de manejar un objeto con su mismo nombre.

```javascript
/* Evitar */
function ProfileController() {
	this.name = {};
	this.editAvatar = function() { };
}

/* OK */
function ProfileController() {
	var vm = this;
	vm.name = {};
	vm.editAvatar = function() { };
}
```

### Lógica del constructor/inicio en $onInit

Introducir la lógica a realizar en el inicio del Controller dentro de la función $onInit.

`Asegura que todos los bindings declarados en el Component están disponibles.`

```javascript
/* Evitar */
function TimelineController(timelineService) {
	var vm = this;
	// from binding: vm.profile
	vm.twitts = timelineService.getTwitts(vm.profile.id);
	...
}

/* OK */
function TimelineController(timelineService) {
	var vm = this;
	vm.twitts = [];
	// from binding: vm.profile
	
	vm.$onInit = function() {
		vm.twitts = timelineService.getTwitts(vm.profile.id);
	};
}
```

### Delega lógica a los Services

Los Controller deberían servir como punto de unión entre la vista y los Service, incluyendo sólo lógica que sea muy específica, no reutilizable.

`Poniendo la lógica en los Services, puede ser reutilizada por varios Controllers`
`Facilita la interpretación del Controller en su conjunto`
`Atomiza las operativas de manera que es mas facil realizar test unitarios y encontrar bugs`

```javascript
/* Evitar */
function ProfileController($http, $q) {
	var vm = this;
	vm.profile = undefined;
	...
	function getProfile() {
		var settings = { ... };
		return $http.get(settings)
			.then( ... )
			. catch ( ... );
	}
}

/* OK */
function ProfileController(profileService) {
	var vm = this;
	vm.profile = undefined;

	vm.$onInit = function() {
		vm.profile = profileService.getProfile();
	};
}
```

## Services

### Llamadas REST

Procura utilizar `$resource` antes que `$http`, que simplifica y hace más claro de interpretar el código de llamadas REST.
