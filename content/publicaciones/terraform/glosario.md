+++ 
date = '2025-10-25T11:26:15-03:00'
draft = false
title = 'Glosario'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Glosario"] 
+++

# Glosario

## Perfiles de Terraform
Uno de los problemas principales que se tuvo administrando con Terraform los recursos de AWS, es que cuando se quiere ejecutar cualquier comando de `terraform` se debe utilizar un PERFIL. Para crear un perfil nuevo, se deben seguir los siguientes pasos: 
1. Para esto, necesitaremos la cuenta root de AWS e iremos al servicio de IAM para poder crear un nuevo usuario:
    1. Nos logueamos a nuestra cuenta de AWS con el usuario root
    2. Vamos al servicio de IAM > Users > Create User
    3. **User Name**: Terraform
    4. Permission > Attach policies directly
    5. **Y seleccionamos el siguiente permiso**: Administrator Access
    6. Create User
    7. Una vez que nuestro nuevo usuario se encuentra creado, lo que deberemos de hacer es:
        * Lo seleccionamos de la lista de IAM > Users
        * En la pestaña Security Credentials > Create Access Key > CLI
        * Agregamos alguna descripción que querramos 
        * Create Access Key
        * Por temas de seguridad, deberemos de bajar el csv donde se tienen las llaves en texto plano
        * Luego de esto, copiamos tanto el Access Key como el valor del Secret Access Key. 
2. Luego lo que hacemos es seguir el paso nº 4 de la siguiente documentación: https://www.thegeekstuff.com/2019/03/aws-configure-examples/
3. Luego, deberemos de invocar el comando `export AWS_PROFILE=terraform` antes de invocar a comandos tales como `terraform plan`. Recordar que la invocación al comando `export` se debe realizar cada vez que se abra una nueva sesión en la consola. 
5. Luego de esta configuración, podemos revisarla invocando a `aws configure list`. 

### Provider Maintainers
Los "provider maintainers" en Terraform son individuos o equipos responsables del desarrollo y mantenimiento de los proveedores de Terraform. Un proveedor en Terraform es un complemento que permite a Terraform interactuar con servicios en la nube, proveedores de infraestructura local u otros sistemas externos para gestionar recursos.

Los proveedores mantienen la integración de Terraform con servicios específicos y se encargan de asegurar que los proveedores sean compatibles, estables y estén actualizados con las últimas características y cambios de los servicios que representan. 

### Proveedor de Registro (Provider Registry)
El proveedor de registros (Provider Registry en inglés) en Terraform es un servicio proporcionado por HashiCorp que actúa como un repositorio centralizado de proveedores de Terraform y módulos. Este registro permite a los usuarios buscar, descubrir y acceder fácilmente a los proveedores de Terraform y módulos creados y mantenidos por la comunidad y por HashiCorp.

### Provider Tiers
Los *providers* de Terraform, que son los plugins que permiten a Terraform interactuar con APIs externas (como AWS, Azure, Google Cloud, Kubernetes, etc.), se clasifican oficialmente en función de quién los mantiene y su nivel de confianza, lo cual podría verse como distintos "tiers". 

#### 1. Providers Oficiales (Official Providers)
Estos son los providers de mayor confianza y calidad, mantenidos directamente por HashiCorp (la empresa que desarrolla Terraform) o por el vendedor de la plataforma a la que se conectan (por ejemplo, AWS, Microsoft, Google, etc.), en estrecha colaboración con HashiCorp.

1. **Características**: Máximo soporte, actualizaciones más frecuentes, integración profunda con Terraform y con la plataforma subyacente.
2. **Ejemplos**: `aws`, `azurerm`, `google`, `kubernetes`.

#### 2. Providers Verificados (Verified Providers)
Son mantenidos por terceros que son *partners* tecnológicos o proveedores de software reconocidos y que han pasado por un proceso de verificación con HashiCorp.

1. **Características**: Son de alta calidad y están respaldados por una organización comercial establecida que colabora con HashiCorp.
2. **Ejemplos**: Providers de empresas como Cloudflare, Datadog, Splunk, o VMWare.

#### 3. Providers Comunitarios (Community Providers)
Son mantenidos por miembros individuales de la comunidad *open-source*.

1. **Características**: La calidad y el soporte varían. Dependen de la dedicación de sus mantenedores. Son esenciales para interactuar con herramientas más nicho o APIs menos populares.
2. **Ejemplos**: Providers para algunas herramientas *self-hosted* o APIs específicas.

### Namespaces
En el contexto del registro de proveedores de Terraform, el término "namespaces" se refiere a una forma de organizar y categorizar los proveedores de Terraform y los módulos asociados dentro del registro. Los "namespaces" actúan como contenedores lógicos que agrupan los recursos relacionados según el autor o el mantenimiento del proveedor.

Cada "namespace" en el registro de proveedores de Terraform está asociado con un nombre único, que generalmente corresponde al nombre del autor o del mantenedor del proveedor o módulo. Estos nombres pueden ser nombres de organizaciones, nombres de usuarios o cualquier otro identificador único.

Los "namespaces" permiten a los usuarios del registro de proveedores de Terraform identificar fácilmente los proveedores y módulos mantenidos por una determinada entidad. Esto facilita la búsqueda y el descubrimiento de recursos relevantes para sus necesidades específicas.

### File State
En Terraform, los "state files" (archivos de estado) son archivos que contienen información sobre la infraestructura gestionada por Terraform. Estos archivos almacenan el estado actual de los recursos que Terraform administra, incluyendo los recursos creados, sus atributos y cualquier otra información relevante.

Aquí hay algunos puntos clave sobre los archivos de estado en Terraform:

1. **Registro del estado de la infraestructura**: Los archivos de estado registran el estado actual de la infraestructura gestionada por Terraform. Esto incluye información sobre los recursos que Terraform ha creado, modificado o eliminado, así como sus atributos y configuraciones actuales.

2. **Sincronización con la infraestructura real**: Los archivos de estado se utilizan para mantener un registro sincronizado de la infraestructura real y la definición de infraestructura en tus archivos de configuración de Terraform. Esto permite a Terraform determinar qué recursos están gestionados por Terraform y qué cambios han sido aplicados a esos recursos.

3. **Prevención de conflictos**: Los archivos de estado evitan conflictos al permitir que Terraform realice cambios de manera controlada y predecible. Terraform utiliza el archivo de estado como fuente de verdad para determinar qué cambios son necesarios para alcanzar el estado deseado de la infraestructura.

4. **Almacenamiento local o remoto**: Los archivos de estado pueden almacenarse localmente en tu máquina de desarrollo o en un sistema de control de versiones como Git, pero también es común almacenarlos de forma remota en un servicio de almacenamiento específico, como Terraform Cloud, Amazon S3, Azure Blob Storage, o Google Cloud Storage. Almacenar los archivos de estado de forma remota proporciona una mayor seguridad y colaboración entre equipos.

### Desired & Current state
En Terraform, el término "desired state" (estado deseado) se refiere al estado en el que deseas que se encuentre tu infraestructura según lo definido en tus archivos de configuración de Terraform. Este estado deseado se describe mediante la especificación de recursos y sus atributos en los archivos de configuración.

Puntos importantes:

1. **Definición de recursos**: En tus archivos de configuración de Terraform, defines los recursos que deseas que Terraform administre. Estos recursos pueden ser instancias de máquinas virtuales, bases de datos, redes, balanceadores de carga, entre otros, dependiendo del proveedor y los servicios que estés utilizando.

2. **Atributos y configuraciones**: Junto con la definición de recursos, también especificas los atributos y configuraciones de esos recursos que deseas que Terraform aplique. Esto incluye detalles como el tipo de instancia, la región, el tamaño del disco, las reglas de firewall, etc.

3. **Gestión de cambios**: Terraform se encarga de llevar la infraestructura al estado deseado, comparando el estado actual de los recursos con el estado deseado definido en los archivos de configuración. Si hay diferencias entre el estado actual y el estado deseado, Terraform realiza los cambios necesarios para converger hacia el estado deseado.

4. **Planificación y ejecución**: Antes de aplicar los cambios, Terraform genera un plan detallado que describe los pasos necesarios para alcanzar el estado deseado. Este plan muestra qué recursos serán creados, modificados o eliminados para alcanzar el estado deseado.  Luego, Terraform ejecuta estos cambios de manera segura y controlada.

### Provider Versioning
La versión de proveedor (provider versioning en inglés) en Terraform se refiere al proceso de gestionar y controlar las versiones de los proveedores de Terraform utilizados en tu configuración. Cada proveedor de Terraform es mantenido y actualizado por un equipo específico, y regularmente se lanzan nuevas versiones para incluir nuevas funcionalidades, correcciones de errores y mejoras de rendimiento.

Aquí hay algunas cosas importantes que debes saber sobre la versión de proveedor en Terraform:

1. **Compatibilidad de versión**: Cada versión de Terraform es compatible con una serie de versiones de proveedor. Esto significa que si estás utilizando una versión específica de Terraform, debes asegurarte de utilizar una versión compatible del proveedor. Puedes consultar la documentación oficial de Terraform o del proveedor para encontrar información sobre la compatibilidad de versiones.

2. **Especificación de versiones**: En tus archivos de configuración de Terraform, puedes especificar la versión del proveedor que deseas utilizar. Esto te permite controlar qué versión del proveedor se utiliza en tu infraestructura y evitar actualizaciones no deseadas que podrían introducir cambios inesperados.

3. **Actualización de versiones**: Es importante mantener tus proveedores de Terraform actualizados para aprovechar las últimas funcionalidades y correcciones de errores. Puedes utilizar herramientas como `terraform init -upgrade` para actualizar automáticamente los proveedores a las últimas versiones compatibles.

4. **Administración de versiones**: Si necesitas utilizar una versión específica del proveedor por razones de compatibilidad o estabilidad, puedes administrar las versiones de los proveedores utilizando herramientas de administración de dependencias, como `terraform init -backend-config`.

### .terraform.lock.hcl
El archivo `terraform.lock.hcl` es un archivo generado por Terraform que se utiliza para bloquear las versiones específicas de los módulos de Terraform y los proveedores de infraestructura utilizados en tu configuración. Este archivo ayuda a garantizar que las mismas versiones de los módulos y proveedores se utilicen de manera consistente en diferentes entornos y durante diferentes ejecuciones de Terraform.

Algunos puntos importantes:

1. **Versiones específicas**: El archivo `terraform.lock.hcl` contiene una lista de las versiones específicas de los módulos de Terraform y los proveedores de infraestructura que se han utilizado en la configuración. Estas versiones son determinadas por Terraform durante la ejecución de `terraform init` y se basan en las restricciones de versión especificadas en los archivos de configuración, como `required_providers` y `required_version`.

2. **Reproducibilidad**: Al incluir versiones específicas en el archivo `terraform.lock.hcl`, se garantiza que las mismas versiones de los módulos y proveedores se utilizarán cada vez que se ejecute Terraform en el mismo directorio de trabajo. Esto proporciona una mayor reproducibilidad y evita que las actualizaciones automáticas de los módulos y proveedores afecten la consistencia de la infraestructura.

3. **Control de versiones**: El archivo `terraform.lock.hcl` puede y debe ser incluido en tu sistema de control de versiones (por ejemplo, Git) junto con tus otros archivos de configuración de Terraform. Esto asegura que todos los miembros del equipo utilicen las mismas versiones de los módulos y proveedores y facilita la colaboración en el desarrollo de infraestructura.

4. **Actualización manual**: Aunque Terraform gestiona automáticamente la generación y actualización del archivo `terraform.lock.hcl`, no se recomienda modificar manualmente este archivo. Terraform lo gestionará por ti durante `terraform init` y garantizará que refleje de manera precisa las versiones utilizadas en tu configuración.

### Debuging Terraform
`TF_LOG` es una variable de entorno que puedes configurar para controlar el nivel de detalle de los registros (logs) generados por Terraform durante la ejecución de los comandos. Esta variable de entorno es útil para depurar problemas, entender lo que está sucediendo bajo el capó de Terraform y obtener más información sobre cómo se están ejecutando tus comandos.

Puedes establecer `TF_LOG` en uno de los siguientes niveles de detalle:

1. **`TRACE`**: Este es el nivel de detalle más alto y proporciona información detallada sobre cada acción que Terraform está tomando, incluyendo todas las solicitudes a la API del proveedor de la nube, la planificación detallada y más. Este nivel es útil para depurar problemas específicos y entender el flujo de trabajo interno de Terraform, pero la cantidad de registros generados puede ser muy grande.
      
2. **`DEBUG`**: Este nivel proporciona información detallada sobre lo que está haciendo Terraform, incluyendo la creación y actualización de recursos, la resolución de dependencias, la evaluación de expresiones y más. Es menos detallado que `TRACE`, pero aún así puede generar muchos registros.

3. **`INFO`**: Este es el nivel predeterminado y proporciona información de nivel de información sobre el progreso de los comandos de Terraform, como los recursos que se están creando, actualizando o eliminando, y cualquier problema que ocurra durante la ejecución.  Este nivel es útil para obtener una visión general de lo que está sucediendo sin inundar la salida con demasiada información.

4. **`WARN`**: Este nivel solo muestra advertencias y errores. Es útil cuando solo estás interesado en problemas que puedan ocurrir durante la ejecución y no necesitas ver detalles sobre el progreso.

5. **`ERROR`**: Este nivel solo muestra mensajes de error. Es útil cuando solo necesitas ser notificado sobre errores críticos y no estás interesado en ningún otro tipo de mensaje.

## Comandos
### terraform console
Para poder consultar el funcionamiento de las funciones built-in de Terraform, tenemos la posibilidad de invocar al comando `terraform console`. 

El comando `terraform console` es una herramienta interactiva que te permite evaluar expresiones y funciones de Terraform en tiempo real dentro de una consola interactiva. Esto te permite probar y experimentar con código Terraform sin necesidad de aplicar los cambios a tu infraestructura.

Cuando ejecutas el comando `terraform console`, Terraform inicia una sesión interactiva donde puedes ingresar y evaluar expresiones de Terraform. Por ejemplo, puedes realizar cálculos simples, manipulaciones de cadenas, operaciones matemáticas, y mucho más.

Por ejemplo, podrías ejecutar el siguiente comando: 

```bash
terraform console
```

Una vez que ingreses a la consola interactiva, podrías ingresar expresiones como estas: 

```hcl
> "Hello, " + "World!"
```

El cual te daría como resultado: 

```
"Hello, World!"
```

Otras operaciones más complejas también son posibles, por ejemplo: 

```hcl
> upper("hello")
```

Dará como resultado: 

```
"HELLO"
```

Esta herramienta es útil para probar rápidamente cómo se comportan las expresiones y las funciones de Terraform antes de integrarlas en tus archivos de configuración. Además, puede ser útil para depurar y comprender mejor cómo funcionan ciertas funciones o expresiones en Terraform. Sin embargo, ten en cuenta que `terraform console` no tiene acceso a ningún estado de Terraform o recursos reales, por lo que algunas operaciones que dependen del estado de la infraestructura real no serán posibles de evaluar.

### terraform init
El comando `terraform init` se utiliza para inicializar un directorio de trabajo de Terraform. Cuando trabajas con Terraform en un nuevo directorio o cuando clonas un repositorio que contiene archivos de configuración de Terraform, es necesario ejecutar `terraform init` antes de utilizar otros comandos de Terraform como `terraform plan` o `terraform apply`. Aquí hay más detalles sobre qué hace este comando:

1. **Instalación de proveedores y módulos**: Una de las funciones principales de `terraform init` es descargar e instalar los proveedores de infraestructura y módulos de Terraform especificados en tus archivos de configuración. Los proveedores son los complementos que Terraform utiliza para interactuar con API de servicios en la nube, como AWS, Azure, Google Cloud, etc. Los módulos son paquetes reutilizables de configuración de Terraform.
2. **Inicialización del backend**: Terraform utiliza un "backend" para almacenar el estado de la infraestructura. El backend puede ser local, en la nube o en un servicio de almacenamiento remoto. Durante la ejecución de `terraform init`, Terraform configura y establece el backend que se especifica en tu archivo de configuración. Esto es crucial para que Terraform mantenga y sincronice el estado de la infraestructura de manera adecuada.
3. **Inicialización de complementos**: Además de los proveedores y módulos, Terraform puede requerir complementos adicionales para realizar ciertas operaciones, como la generación de gráficos de dependencia o la ejecución de validaciones de sintaxis. `terraform init` también se encarga de descargar e instalar estos complementos si es necesario.

En resumen, `terraform init` es el primer comando que debes ejecutar al comenzar a trabajar en un nuevo directorio de Terraform. Prepara el entorno de trabajo descargando proveedores, módulos y complementos necesarios, y configura el backend para el almacenamiento del estado de la infraestructura. Es un paso fundamental para empezar a utilizar Terraform de manera efectiva en tu proyecto de infraestructura como código.

### terraform init -upgrade
El comando `terraform init -upgrade` se utiliza en Terraform para actualizar automáticamente los módulos de Terraform y los proveedores de infraestructura a las últimas versiones compatibles. Este comando es útil cuando quieres asegurarte de que estás utilizando las últimas funcionalidades y correcciones de errores proporcionadas por los proveedores de Terraform.

Cuando ejecutas `terraform init -upgrade`, Terraform realiza las siguientes acciones:

1. **Actualización de módulos y proveedores**: Terraform verifica si hay nuevas versiones de los módulos de Terraform y los proveedores de infraestructura especificados en tus archivos de configuración. Si se encuentran nuevas versiones, Terraform las descarga automáticamente y las instala en tu entorno de trabajo.

2. **Actualización del archivo `terraform.lock.hcl`**: Después de actualizar los módulos y los proveedores, Terraform actualiza el archivo `terraform.lock.hcl` para reflejar las versiones específicas que se utilizarán en tu configuración. Esto garantiza que las mismas versiones sean utilizadas de manera consistente cada vez que se ejecute Terraform en el mismo directorio de trabajo.

3. **Advertencias y confirmaciones**: Durante el proceso de actualización, Terraform puede mostrar advertencias o solicitar confirmaciones si se detectan cambios significativos en las versiones de los módulos o proveedores. Esto te permite revisar y confirmar los cambios antes de que se apliquen.

En cuanto a la relación con las versiones de los proveedores, `terraform init -upgrade` asegura que estés utilizando las últimas versiones de los proveedores de infraestructura compatibles con tu configuración. Esto es importante porque las nuevas versiones pueden incluir nuevas funcionalidades, mejoras de rendimiento, correcciones de errores y parches de seguridad que pueden ser beneficiosos para tu infraestructura.

Es importante tener en cuenta que aunque `terraform init -upgrade` actualiza los proveedores de infraestructura a las últimas versiones compatibles, no actualiza automáticamente la versión de Terraform en sí misma. Para actualizar la versión de Terraform, debes hacerlo manualmente instalando la nueva versión y ejecutando `terraform init` sin la opción `-upgrade`.

Por ejemplo, en una primera instancia, teníamos:  

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = "= 2.50"
        }
    }
}
```

Y luego actualizamos el código a:  

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = "<= 3.0"
        }
    }
}
```

Ingresamos `terraform init -upgrade` y lo que hará Terraform es buscar la nueva versión compatible con lo que especificamos en nuestro código, por ejemplo, podrá descargar la versión `3.0`. De lo contrario, de no ingresar `-upgrade`, el comando lanzará un error. 

### terraform fmt
El comando `terraform fmt` es ideal para dar formato a nuestros archivos de terraform. Esto posibilita la estandarización y la legibilidad de forma simple, a través de un simple comando. 

### terraform validate
El comando `terraform validate` en Terraform se utiliza para verificar la validez de los archivos de configuración de Terraform. Básicamente, realiza dos tipos de validaciones:

1. **Sintaxis del archivo**: Verifica si la estructura y sintaxis de los archivos de configuración de Terraform son correctas. Esto incluye comprobar si hay errores de sintaxis, como errores de formato, errores de puntuación, errores de indentación, etc. Si hay algún error de sintaxis en tus archivos de configuración, el comando `terraform validate` te lo indicará para que puedas corregirlo antes de continuar.
2. **Referencias a recursos y proveedores**: Terraform también verifica si las referencias a recursos y proveedores en tus archivos de configuración son válidas. Esto significa que comprueba si los recursos y proveedores a los que haces referencia realmente existen y están disponibles en las versiones especificadas de los proveedores de Terraform. Si intentas utilizar un recurso o proveedor que no existe o no está disponible en tu configuración, Terraform te lo hará saber durante la validación.  En resumen, `terraform validate` es una herramienta útil para verificar la precisión y validez de tus archivos de configuración de Terraform antes de aplicar los cambios en tu infraestructura. Esto te ayuda a detectar y corregir errores de sintaxis y referencias inválidas antes de ejecutar comandos que puedan afectar a tu infraestructura en la nube.

### terraform plan
El comando `terraform plan` es uno de los comandos más importantes en Terraform. Se utiliza para generar un plan de ejecución que describe qué acciones Terraform llevará a cabo para alcanzar el estado deseado de la infraestructura según la configuración definida en los archivos de Terraform.  Aquí hay algunos aspectos clave sobre el comando `terraform plan`:

1. **Generación de un plan**: Cuando ejecutas `terraform plan`, Terraform analiza tus archivos de configuración y consulta el estado actual de la infraestructura para determinar qué cambios son necesarios para alcanzar el estado deseado. Luego, genera un plan detallado que describe qué recursos se crearán, modificarán o eliminarán para lograr ese estado deseado.
2. **Visualización de cambios**: El plan generado por `terraform plan` te muestra una lista de acciones que Terraform tomará, incluyendo la creación, modificación o eliminación de recursos. También te muestra cualquier cambio planeado en los atributos de los recursos existentes.
3. **Validación previa a la aplicación**: Antes de aplicar cualquier cambio a tu infraestructura, es una buena práctica ejecutar `terraform plan` para revisar los cambios propuestos. Esto te permite entender completamente qué cambios se van a realizar y te da la oportunidad de detectar posibles problemas antes de que se apliquen los cambios.
4. **No aplica los cambios**: Es importante tener en cuenta que `terraform plan` solo genera un plan de ejecución y no aplica ningún cambio a la infraestructura. Esto significa que puedes usar este comando de forma segura para revisar los cambios sin afectar a tu infraestructura en producción.  En resumen, `terraform plan` es una herramienta fundamental para la gestión de la infraestructura con Terraform. Te permite visualizar y validar los cambios propuestos antes de aplicarlos, lo que ayuda a prevenir errores y asegura que tus modificaciones se realicen de manera controlada y predecible.

### terraform plan -target=<FILE.tf>
El comando `terraform plan -target` se utiliza para generar un plan de ejecución de Terraform limitado a un conjunto específico de recursos dentro de tu configuración. Aquí tienes una explicación breve sobre lo que hace:

1. **Genera un plan de ejecución limitado**: El comando `terraform plan` por sí solo genera un plan de ejecución completo para todos los recursos definidos en tus archivos de configuración de Terraform. Sin embargo, al agregar `-target`, puedes restringir este plan solo a los recursos que especifiques, junto con sus dependencias.
2. **Especifica el objetivo**: Con `-target`, puedes indicar a Terraform qué recursos deseas incluir en el plan. Esto es útil cuando solo quieres ver qué cambios se aplicarán en un subconjunto específico de recursos en lugar de en toda tu infraestructura.
3. **Evalúa dependencias**: Terraform analiza los recursos especificados junto con sus dependencias para determinar qué cambios deben aplicarse. Esto puede incluir la creación, actualización o eliminación de recursos, así como las dependencias necesarias para mantener la integridad de la infraestructura.
4. **Proporciona una vista previa de los cambios**: Una vez que ejecutas `terraform plan -target`, Terraform genera un plan detallado que muestra qué acciones tomará para los recursos especificados. Esto te permite revisar y validar los cambios propuestos antes de aplicarlos.

### terraform apply
El comando `terraform apply` es utilizado para aplicar los cambios definidos en tus archivos de configuración de Terraform a tu infraestructura. Este comando lleva a cabo las siguientes acciones:

1. **Planificación y validación**: Antes de aplicar los cambios, Terraform ejecuta implícitamente `terraform plan` para generar un plan detallado de los cambios propuestos. Esto te permite revisar los cambios que se van a realizar y validarlos antes de que tengan efecto en tu infraestructura.
2. **Interactividad opcional**: Terraform solicitará confirmación antes de aplicar los cambios si estás ejecutando `terraform apply` de forma interactiva. Esto te da la oportunidad de revisar el plan y cancelar la operación si es necesario.
3. **Aplicación de los cambios**: Una vez que confirmas la aplicación de los cambios, Terraform procede a ejecutar las acciones necesarias para alcanzar el estado deseado de la infraestructura. Esto puede incluir la creación, modificación o eliminación de recursos en tu entorno de nube.
4. **Actualización del estado**: Después de aplicar los cambios con éxito, Terraform actualiza el archivo de estado para reflejar el estado actual de la infraestructura. Este archivo de estado se utiliza para realizar un seguimiento de los recursos gestionados por Terraform y garantizar que cualquier cambio futuro se aplique de manera coherente.

Es importante tener en cuenta que `terraform apply` realiza cambios en tu infraestructura en la nube y debe utilizarse con precaución, especialmente en entornos de producción. Es recomendable revisar cuidadosamente el plan generado por `terraform plan` antes de aplicar los cambios y asegurarse de entender completamente el impacto de los mismos en tu infraestructura. Además, es una buena práctica hacer uso de entornos de desarrollo o pruebas para probar los cambios antes de aplicarlos en entornos de producción.

### terraform apply -target=<FILE.tf>
Aquí tienes una explicación breve sobre lo que hace el comando `terraform apply -target=<FILE.tf>`:

1. **Aplica cambios limitados a recursos específicos**: El comando `terraform apply` se utiliza para aplicar los cambios definidos en tu configuración de Terraform a tu infraestructura. Al agregar `-target=<file.tf>`, puedes limitar la aplicación de estos cambios solo a los recursos especificados en el archivo `<file.tf>` y sus dependencias.
2. **Identifica el objetivo de la aplicación**: Mediante `-target=<file.tf>`, le indicas a Terraform qué recursos deseas que sean el objetivo de la aplicación de cambios. Esto te permite controlar exactamente qué recursos serán modificados, creados o eliminados durante la ejecución de `terraform apply`.
3. **Evalúa las dependencias necesarias**: Terraform analiza los recursos especificados junto con sus dependencias para determinar qué cambios se deben aplicar y en qué orden. Esto garantiza que se respeten las dependencias entre los recursos y que la infraestructura se mantenga en un estado coherente.
4. **Aplica los cambios de manera selectiva**: Una vez que ejecutas `terraform apply -target=<file.tf>`, Terraform aplica los cambios propuestos solo a los recursos especificados, lo que te permite realizar modificaciones de manera selectiva y controlada en tu infraestructura.

### terraform apply -auto-approve
El comando `terraform apply -auto-approve` se utiliza en Terraform para aplicar los cambios en tu infraestructura sin necesidad de confirmación manual. Cuando ejecutas este comando, Terraform realiza los siguientes pasos:

1. **Generación del plan**: Terraform analiza tus archivos de configuración y determina qué cambios deben aplicarse a tu infraestructura. Luego, genera un plan detallado que describe los recursos que serán creados, modificados o eliminados para alcanzar el estado deseado.
2. **Visualización del plan**: Antes de aplicar los cambios, Terraform muestra el plan generado en la salida estándar para que puedas revisarlo y confirmar que los cambios propuestos son los esperados y deseables.
3. **Aplicación automática**: Después de mostrar el plan, si ejecutas `terraform apply -auto-approve`, Terraform aplicará automáticamente los cambios descritos en el plan sin solicitar confirmación adicional.  Esto incluye la creación, modificación o eliminación de recursos según lo especificado en el plan.
4. **Seguimiento de la ejecución**: Mientras Terraform aplica los cambios, muestra mensajes de progreso en la salida estándar para indicar qué recursos están siendo creados, modificados o eliminados, así como cualquier error o advertencia que pueda ocurrir durante el proceso.

Es importante tener en cuenta que el uso de `-auto-approve` en `terraform apply` suprime la solicitud de confirmación, lo que significa que los cambios se aplicarán inmediatamente sin la oportunidad de revisarlos manualmente. Por lo tanto, debes usar esta opción con precaución, especialmente en entornos de producción, para evitar cambios no deseados o accidentales en tu infraestructura.

### terraform apply -var-file=<file.tfvars>
El comando `terraform apply -var-file="[nombre_archivo]"` se utiliza principalmente en situaciones donde necesitas separar los valores de las variables de la configuración de tu infraestructura y aplicarlos de una manera organizada, segura y repetible.

Las situaciones clave donde se usa este comando son:

#### 1. Gestión de Múltiples Entornos (Dev, Staging, Prod)
Esta es la razón más común. Utilizar archivos de variables (`.tfvars`) permite aplicar el mismo código base de Terraform a diferentes entornos, simplemente cambiando el archivo de variables.

* **Situación**: Tienes la misma infraestructura (por ejemplo, una red, servidores web y una base de datos), pero necesitas diferentes tamaños, nombres o configuraciones según el entorno.
* **Archivos**:
    * **`dev.tfvars`**: Contiene valores pequeños (ej: `server_count = 1`, `db_size = "small"`).
    * **`prod.tfvars`**: Contiene valores grandes (ej: `server_count = 10`, `db_size = "large"`).
* **Comando**:
    * **Para desarrollo**: `terraform apply -var-file="dev.tfvars"`
    * **Para producción**: `terraform apply -var-file="prod.tfvars"`

#### 2. Manejo de Datos Sensibles (Secretos)
Aunque la mejor práctica es usar herramientas como HashiCorp Vault, AWS Secrets Manager o Azure Key Vault, el uso de archivos de variables proporciona una capa de seguridad para datos menos críticos o durante el desarrollo local, especialmente si se añade el archivo a la lista de ignorados (`.gitignore`).

* **Situación**: Necesitas pasar claves de API o contraseñas a la configuración de Terraform sin codificarlas directamente en los archivos `.tf`.
* **Práctica**: Creas un archivo (ej: `secrets.tfvars`) que no se sube a tu repositorio de código (Git).
* **Comando**: `terraform apply -var-file="secrets.tfvars"`

#### 3. Sobrescribir Valores por Defecto (Defaults)
Si tienes variables con valores por defecto en tu archivo `variables.tf`, puedes usar un archivo de variables para sobrescribir solo aquellas que necesitas cambiar.

* **Situación**: Quieres cambiar rápidamente uno o dos parámetros de una configuración compleja sin tener que pasar cada variable individualmente en la línea de comandos (`-var`).
* **Archivo**: `custom.tfvars` solo incluye las variables que difieren del valor por defecto.
* **Comando**: `terraform apply -var-file="custom.tfvars"`

#### 4. Flujos de Trabajo CI/CD (Integración y Despliegue Continuo)
En sistemas de automatización, el uso de archivos de variables es crucial para mantener la configuración de un entorno separada del código de despliegue.

* **Situación**: Estás utilizando una herramienta de CI/CD (como Jenkins, GitLab CI o GitHub Actions) para desplegar tu infraestructura.
* **Beneficio**: El *pipeline* de CI/CD puede cargar el archivo de variables apropiado para el entorno de destino (por ejemplo, el archivo `staging.tfvars` cuando se ejecuta en la rama de *staging*).

#### ¿Cuál es la diferencia con `terraform.tfvars`?
Si un archivo se llama `terraform.tfvars`, Terraform lo carga y lo aplica automáticamente sin necesidad de usar el *flag* `-var-file`.

El uso explícito de `-var-file="nombre_archivo.tfvars"` es necesario cuando:

1.  El nombre del archivo no es `terraform.tfvars` o `*.auto.tfvars`.
2.  Necesitas cargar múltiples archivos de variables en una sola ejecución.
* **Ejemplo**: `terraform apply -var-file="base.tfvars" -var-file="prod.tfvars"`

### terraform refresh
NOTA: Este comando se encuentra deshabilitado en versiones actuales de Terraform y la única posibilidad de utilizarlo es mediante los comandos `terraform plan` y `terraform apply`. 

### terraform plan -destroy
El comando `terraform plan -destroy` es una variación del comando `terraform plan` que se utiliza específicamente para generar un plan detallado de destrucción de recursos. Este comando es útil cuando estás preparando la eliminación de la infraestructura gestionada por Terraform y quieres ver qué recursos serán eliminados antes de ejecutar `terraform destroy`.

Aquí hay más información sobre `terraform plan -destroy`:
1. **Generación de un plan de destrucción**: Al ejecutar `terraform plan -destroy`, Terraform analiza tus archivos de configuración y el estado actual de la infraestructura para determinar qué recursos serán eliminados si ejecutas `terraform destroy`. Terraform genera un plan detallado que describe los recursos que serán eliminados y cualquier cambio asociado, como la eliminación de dependencias.
2. **Visualización de cambios a ser eliminados**: El plan de destrucción generado por `terraform plan -destroy` te mostrará una lista detallada de los recursos que serán eliminados. Esto incluye información sobre los recursos específicos, como sus nombres, tipos, y cualquier otra información relevante.
3. **Validación previa a la destrucción**: Al revisar el plan de destrucción, puedes asegurarte de que comprendes completamente qué recursos serán eliminados y qué impacto tendrá en tu infraestructura. Esto te permite tomar decisiones informadas y verificar que estás eliminando los recursos deseados antes de ejecutar `terraform destroy`.

### terraform destroy
El comando para destruir o eliminar toda la infraestructura que se ha creado con Terraform es `terraform destroy`. Este comando desmantela y elimina todos los recursos que fueron creados por Terraform según la configuración definida en tus archivos.

Es importante tener en cuenta que `terraform destroy` eliminará todos los recursos definidos en tu configuración, por lo que debes tener cuidado al usarlo en entornos de producción para evitar la pérdida de datos o recursos importantes.

Antes de ejecutar `terraform destroy`, es una buena práctica revisar el plan de destrucción generado por Terraform para asegurarte de que comprendes qué recursos se eliminarán. Puedes obtener este plan ejecutando `terraform plan -destroy` para ver una lista detallada de los recursos que serán eliminados.

Una vez estés seguro de que deseas continuar con la eliminación de los recursos, simplemente ejecuta `terraform destroy` y confirma la acción cuando se te solicite. Terraform comenzará a eliminar los recursos de manera segura y controlada de acuerdo con la configuración definida en tus archivos.

El comando `terraform destroy` se utiliza para eliminar de forma segura y controlada todos los recursos que Terraform ha creado y administra según la configuración definida en tus archivos de Terraform. Aquí hay más información sobre este comando:

1. **Eliminación de recursos**: Cuando ejecutas `terraform destroy`, Terraform desmantela y elimina todos los recursos que ha creado según la configuración en tus archivos. Esto incluye la eliminación de instancias de máquinas virtuales, grupos de seguridad, redes, bases de datos, y cualquier otro recurso gestionado por Terraform.

2. **Confirmación de eliminación**: Antes de llevar a cabo la eliminación de los recursos, Terraform solicitará confirmación para asegurarse de que estás seguro de que deseas proceder con la acción. Debes confirmar explícitamente la eliminación escribiendo "yes" en la línea de comandos para evitar la eliminación accidental de recursos importantes.

3. **Actualización del estado**: Después de eliminar los recursos, Terraform actualizará el archivo de estado para reflejar los cambios realizados. Esto asegura que Terraform tenga un registro actualizado del estado de tu infraestructura y evita conflictos futuros al aplicar cambios.

4. **Control de recursos**: Es importante tener en cuenta que `terraform destroy` eliminará todos los recursos definidos en tu configuración, por lo que debes tener cuidado al usarlo en entornos de producción para evitar la pérdida de datos o recursos importantes. Se recomienda realizar una copia de seguridad o una revisión cuidadosa de los recursos antes de ejecutar este comando en un entorno de producción.

### terraform destroy -target
Algunas veces, no vamos a necesitar eliminar todos los recursos que se encuentran dentro de nuestro repositorio. En el caso actual, si nosotros aplicaríamos `terraform destroy`, se eliminarían los recursos de: 
* `first_ec2.tf`
* `github.tf`

Pero supongamos que nosotros queremos mantener las instancias generadas en AWS pero queremos eliminar los recursos gestionados en GitHub ¿Cómo podemos hacer esto?. 

Esto se puede hacer mediante el comando `terraform destroy -target <TARGET>`. 

El comando `terraform destroy -target <TARGET>` es una variación del comando `terraform destroy` que te permite especificar un recurso específico que deseas destruir en lugar de eliminar todos los recursos gestionados por Terraform. Esto puede ser útil cuando solo quieres eliminar un recurso particular en lugar de toda la infraestructura, lo que te permite controlar de manera más precisa qué recursos se eliminarán.

Detalles: 

1. **Especificación del objetivo**: Con la opción `-target`, puedes especificar el recurso específico que deseas destruir. Esto se hace proporcionando la dirección del recurso como argumento. La dirección del recurso es la ruta completa al recurso en el árbol de recursos de Terraform, como `aws_instance.example` o `google_compute_instance.example`.

2. **Destrucción del recurso objetivo**: Cuando ejecutas `terraform destroy -target <TARGET>`, Terraform desmantela y elimina únicamente el recurso especificado y cualquier recurso que dependa directa o indirectamente de él. Terraform analiza la dependencia del recurso objetivo y elimina los recursos en el orden adecuado para evitar conflictos o errores.

3. **Precaución**: Es importante tener en cuenta que `terraform destroy -target <TARGET>` elimina solo el recurso especificado y sus dependencias, pero no elimina otros recursos no relacionados que puedan estar presentes en tu configuración de Terraform. Por lo tanto, debes tener cuidado al usar este comando para evitar la eliminación accidental de recursos importantes o infraestructura dependiente.



