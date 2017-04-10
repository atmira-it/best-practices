# Guia general de errores

> Esta guia ha sido desarrollada para tener reunidos los errores que se han ido dando a lo largo del desarrollo

## Tabla de contenidos

[Conceptos basicos] (# Conceptos basicos "Conceptos basicos")

#  EL1051E:(pos 34): Unexpected ran out of arguments

Este error es muy comun en el copy/paste, simplemente nos indica que se nos ha olvidado cerrar un parentesis en el xml.

```java
<transition on="doedittesttype" to="restart">
	<evaluate expression="testTypeViewService.updateTestType(vwNewTestType, sessionParameters.businessId"
	result="flowScope.vwNewTestType" />				
</transition>
```

Para mas informacion consultar el siguiente enlace:

* http://stackoverflow.com/questions/32509109/thymeleaf-webflow-spelparseexception-el1051epos-34-unexpected-ran-out-of-ar

# EL1008E:(pos 0)

Si vemos alguno de estos errores: 

```java
2017-03-23 11:48:06,517|SEV:ERROR|com.bankia.arq.ioi.flows.core.actions.FlowExceptionAction|MSG:Exception in flow execution
org.springframework.expression.spel.SpelEvaluationException: EL1008E:(pos 0): Field or property 'anularContratacionCarteraGestionadaSBPAction' cannot be found on object of type 'org.springframework.webflow.engine.impl.RequestControlContextImpl'


2017-03-14 11:42:14,966|SEV:ERROR|com.bankia.arq.ioi.flows.core.actions.FlowExceptionAction|MSG:Exception in flow execution
org.springframework.expression.spel.SpelEvaluationException: EL1008E:(pos 0): Field or property 'GestionarAportacionesCarteraGestionadaSBPAction' cannot be found on object of type 'org.springframework.webflow.engine.impl.RequestControlContextImpl'

```

Debemos de comprobar que en el archivo *.flow.xml, no haya ningun nombre de bean que empiece por mayuscula o que en el archivo app-context, el componente "context:component-scan" tenga incluida la ruta para todos los archivos *.java.

# El tipo es.cajamadrid.servicios.ARQ.Fecha no está soportado por ArqMapper

Si nos encontramos este error:

```java

Código Error:            #ARQM_002#
Servicio:                ##
Versión:                 ##
Módulo:                  ##
Sistema:                 #ARQMAPPER#
Descripción:             #El tipo es.cajamadrid.servicios.ARQ.Fecha no está soportado por ArqMapper#
Alias:                   ##
Máquina Ejecución:       ##
Tipo Error:              #TIPO DE ERROR NO ESTABLECIDO#
Id. Operación:           #-1#
Proveedor:               ##

```
La solucion pasa por implementar este metodo para realizar el casting entre los diferentes tipos de Fechas.

```java
    public static es.cm.arq.tda.tiposdedatosbase.Fecha getFechaOpcional(es.cajamadrid.servicios.ARQ.Fecha fechaBISA) throws WIException {
    
                String stringFecha = fechaBISA.getValor();
                int any = Integer.valueOf(stringFecha.substring(0, 4));
                int mes = Integer.valueOf(stringFecha.substring(5, 7));
                int dia = Integer.valueOf(stringFecha.substring(8, 10));
                return new Fecha(any, mes, dia);
    }
```

# The channel code is not configure in system

Si vemos el siguiente error:

```java
com.bankia.arq.ioi.commons.execution.context.ExecutionContextException: The channel code is not configure in system
```

Debemos de asegurarnos que la variable 'x-j_gid_cod_app' va rellena y con una aplicacion valida para el servicio que estamos probando en un entorno especifico, por ejemplo se tuvo problemas probando 'ObtenerContratosIntervinienteGACFSAP' y se identifico este problama y todo era porque estabamos tirando contra ioi con la app 'E2' y para nuestro sistema necesitabamos pasarle 'o2'.

# The session token is not present in the request

Cuando veamos el siguiente error:

```java
{
"operationResult":"EG0001",
"operationMessage":"The session token is not present in the request"
}
```

Debemos de asegurarnos que la url le estamos pasando el campo 'TGT='

# The channel code is not present in the request

Cuando veamos el siguiente error:

```java
{
"operationResult":"EG0001",
"operationMessage":"The channel code is not present in the request"
}
```

Debemos de asegurarnos que la url le estamos pasando el campo x-j_gid_cod_app=


