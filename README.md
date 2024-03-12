# Proyecto Assesment Pragma 2024/03: Integración

## Descripción general

El **microservicio** llamado ***Integración (intgration)*** es el encargado
de funcionar como orquestador entre el consumidor final y los servicio back-end (customer, finance_product y decision_engine).

### url de consumo: https://localhost:443/api/development/v1/finance-service/integration/calculate-best-finance-products/customers/$(customerId)

## Aspectos técnicos

### Framework
Este es un servicio desarrollado con **apache NIFI**.
Requiere Java 21 para funcionar.

### Seguridad
Se realizó una implementación de seguridad a través de certificados digitales. Se tiene configurado un keystore y un truststore. Para poder consumir este servicio es vital configurar el keystore del lado del cliente.

Para realizar pruebas en postman se debe incluir el certificado keystore.p12, el cual se encuentra disponible en la carpeta /conf, en la configuración de certificados de postman. El password del keystore es: 75e0fba420318162cfd38ca676f18777.

Importante tener en cuenta que el consumo se debe hacer a través de https y no sólo http.

## Instalación y ejecución

#### <a href="https://github.com/fcordonezo/AP202403_documentation/blob/develop/collections/FinanceService_collection.json" download>Descargar colección Postman</a>

Primero que nada, es indispensable [descargar Apache NIFI](https://nifi.apache.org/download/) desde la página oficial.

Una vez descargado, se debe extraer el contenido y, acto seguido, se debe copiar la carpeta /conf que está en este repositodio, como reemplazo a la que se encuentra presente en el directorio extraído del proyecto.  

Una vez hecho esto sólo resta dirigirse al directorio /bin y, desde una línea de comandos, ejecutar el archivo ```run-nifi.bat``` (o ```nifi.sh``` en linux). Esto levantará el servicio en el puerto 8443.

La ruta para ingresar a NIFI es: https://localhost:8443/nifi

Para iniciar sesión, las credenciales dispuestas son las siguentes:
Usuario: b24353dd-eb26-4149-b20e-143491e5015f
Contraseña: CI3o8svONwp6hhrCGYWZhkUkaQ/t/ju5

Una vez iniciada la sesión, podrá encontrar el flujo propuesto para el desarrollo del ejercicio ejercicio. 

Antes de iniciar los nodos, deberá hacer clic derecho en cualquier parte del lienzo y selecionar la opción **configure**. Una vez allí, en la pestaña **CONTROLLER SERVICES** habrán dos controladores: Uno es el encargado de toda la parte de recibir y entregar las peticiones HTTP, y el otro es el responsable de entregar la información necesaria a Prometheus. Si alguno de los dos o ambos están en estado **Disabled**, entonces harbá que hacer clic en el ícono de rayo en el controlador correspondiente y, acto seguido, clic en el botón **ENABLE**. Con esto el controlador estará habilitado para trabajar. 

Es importe habilitar estos controladores porque algunos nodos los necesitan para trabajar correctamente.

Ahora sí, para activar los nodos, bastará con hacer clic derecho sobre cualquier parte del lienzo y seleccionar la opción de **►Start**, todos los nodos empezaran a funcionar y el servicio será entonces consumible.

### Contrato Openapi 3.0.1
La firma puede ser encontrada en **[esta página](https://fcordonezo.github.io/AP202403_integration_nifi/)**
``` yaml
openapi: 3.0.1
info:
  title: Integración
  version: 1.0.0
servers:
- url: https://localhost:443/api/development/v1/finance-service/integration/calculate-best-finance-products/customers
  description: Integración de componentes para servicio financiero
paths:
  /{customerId}:
    get:
      tags:
      - calculate-best-finance-products
      summary: Obtener una lsita con los productos financieros aplicables al cliente
      operationId: getById
      parameters:
      - name: customerId
        in: path
        required: true
        schema:
          type: integer
          format: int64
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BestFinanceProductsResponseDto'
components:
  schemas:
    BestFinanceProductsResponseDto:
      type: object
      properties:
        customer:
          type: object
          properties:
            customerId:
              type: integer
              format: int64
            fullName:
              type: string
            income:
              type: number
              format: float
            city:
              type: string
            countryCode:
              type: string
            age:
              type: integer
              format: int32
        financeProducts:
          type: array
          items:
            type: object
            properties:
              financeProductId:
                type: integer
                format: int64
              code:
                type: string
              description:
                type: string
              ruleSet:
                type: string
```


## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
- [Getting Help](#getting-help)
- [Requirements](#requirements)
- [License](#license)
- [Export Control](#export-control)

## Features

Apache NiFi was made for dataflow. It supports highly configurable directed graphs of data routing, transformation, and system mediation logic. Some of its key features include:

- Web-based user interface
  - Seamless experience for design, control, and monitoring
- Highly configurable
  - Loss tolerant vs guaranteed delivery
  - Low latency vs high throughput
  - Dynamic prioritization
  - Flows can be modified at runtime
  - Back pressure
- Data Provenance
  - Track dataflow from beginning to end
- Designed for extension
  - Build your own processors and more
  - Enables rapid development and effective testing
- Secure
  - SSL, SSH, HTTPS, encrypted content, etc...
  - Pluggable role-based authentication/authorization
