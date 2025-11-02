+++ 
date = '2025-10-31T23:12:06-03:00'
draft = true
title = "Introducción a Terraform"
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Introducción"] 
+++

# Sección nº 1
## 1. Descripción general de Terraform y su certificación
Terraform es una herramienta que se utiliza para gestionar la infraestructura como código. En otras palabras, te permite definir tu infraestructura (como servidores, redes, bases de datos, etc.) utilizando un lenguaje de programación y luego desplegarla de manera automatizada.

La importancia de Terraform en el ámbito de DevOps radica en su capacidad para automatizar y estandarizar el proceso de aprovisionamiento de infraestructura. Esto significa que puedes definir tu infraestructura de forma coherente y repetible, lo que facilita la gestión y el despliegue de aplicaciones en diferentes entornos (desarrollo, pruebas, producción, etc.). Además, al utilizar Terraform, puedes mantener tu infraestructura bajo control de versiones, lo que facilita la colaboración y el seguimiento de cambios a lo largo del tiempo. En resumen, Terraform ayuda a acelerar el tiempo de entrega, mejorar la consistencia y reducir los errores en el ciclo de vida de desarrollo y despliegue de software.

Podemos observar la importancia en el mundo laboral que ha tomado Terraform, con sólo buscar algunos avisos laborales en Linkedin. 

Lo importante de aprender Terraform, es que una vez que aprendemos el corazón del lenguaje y a programar nuestra propia infraestructura, vamos a tener la posibilidad de implementarlo en cualquier proveedor de la nube, por lo tanto nos abstrae de andar aprendiendo las particularidades de cada una de las plataformas de los proveedores de la nube. 

## 2. Acerca del curso
Este curso sigue todas los puntos que aborda la [certificación de HashiCorp](https://www.hashicorp.com/certification/terraform-associate)

En caso de Hashicorp incorpore nuevos materiales de estudio, se irán agregando al curso. 

La idea de este curso es ir de manera progresiva con respecto al material de estudio para que el conocimiento y la lectura sean de dificultad gradual. 

Un punto importante para un entendimiento completo, es que todo el código que se vea dentro del curso, **estará subido dentro de algún repositorio en GitHub**.

### Proveedor utilizado: AWS
Si bien el curso es para obtener los conocimientos básicos de Terraform, el proveedor con el que se trabajará será AWS. La opción del proveedor es indistinto, ya que Terraform nos permite abstraernos de las particularidades de la API de cada uno de ellos y en contraposición, tener un lenguaje que los unifica; por lo que el estudiantado puede sentirse libre de elegir el proveedor con el que se sienta más cómodo. Cabe destacar que sólo se utilizarán los servicios básicos de AWS como es EC2, IAM y alguno más. Estos mismos servicios serán los que después terminarán impactando de igual manera en los demás proveedores (en caso de modificar el proveedor). 

Hay que tener en cuenta que estos servicios son los más básicos y los que menos costo en la nube de AWS tienen, pero esto no significa que el costo de usarlos sea $0. Siempre se incurrirá en algún costo aunque sea mínimo. 

**IMPORTANTE**: Es por eso que aconsejo tomar este curso con una cuenta nueva de AWS, ya que de este modo tenemos un año completo dentro de la *capa gratuita*; capa que nos permite utilizar varios servicios básicos con un costo reducido. 

## 3. Documentación abierta
Existen varios lugares de donde podemos basarnos para realizar ciertos ejemplos. Uno de ellos es el siguiente: https://github.com/zealvora/terraform-beginner-to-advanced-resource

## 4. Principios básicos de IaC
Aquí se comentan algunos de los beneficios que se tienen al aprender IaC (Infrastructure as Code). Es de suma importancia automatizar la generación de infraestructura, la cual ofrece una serie de beneficios bastante importantes, y aprender Infrastructure as Code (IaC) es fundamental para aprovechar al máximo estas ventajas, entra las cuales están:

1. ****Eficiencia y velocidad**:** Automatizar la generación de infraestructura permite desplegar recursos de manera rápida y consistente. Esto acelera el proceso de desarrollo y despliegue de aplicaciones, lo que resulta en un tiempo de entrega más corto y una respuesta más rápida a las necesidades del negocio.

2. ****Consistencia**:** Al definir la infraestructura como código, se garantiza que todos los entornos, desde desarrollo hasta producción, estén configurados de la misma manera. Esto reduce la posibilidad de errores causados por configuraciones inconsistentes y simplifica la resolución de problemas al tener entornos uniformes.

3. ****Escalabilidad**:** La automatización facilita la gestión de infraestructuras complejas y su escalabilidad. Puedes definir patrones de infraestructura que se repitan fácilmente y que se adapten a las necesidades cambiantes del negocio, sin necesidad de configurar manualmente cada recurso.

4. ****Control de versiones y trazabilidad**:** Al mantener la infraestructura como código, puedes gestionarla mediante sistemas de control de versiones como Git. Esto proporciona un historial completo de los cambios realizados en la infraestructura, facilitando la colaboración entre equipos, la auditoría y la reversión de cambios si es necesario.

5. ****Reutilización y modularidad**:** Puedes crear módulos reutilizables que representen componentes de infraestructura comunes, lo que facilita la construcción y mantenimiento de aplicaciones. Esto promueve las prácticas de desarrollo ágil y la reutilización de buenas prácticas en toda la organización.

En resumen, aprender Infrastructure as Code (IaC), especialmente mediante herramientas como Terraform, es crucial para aprovechar los beneficios de la automatización de la infraestructura. Ayuda a los equipos de desarrollo y operaciones a trabajar de manera más eficiente, consistente y escalable, mejorando así la velocidad y la calidad de la entrega de software.

### Automatización de tareas
Es sumamente importante resaltar la importancia de automatizar las tareas repetitivas y diarias para un profesional DevOps. Automatizar estas tareas repetitivas ofrece beneficios significativos:

1. **Ahorro de tiempo y recursos**: La automatización elimina la necesidad de que los equipos realicen tareas repetitivas manualmente, lo que ahorra tiempo y recursos valiosos. Esto permite que los equipos se enfoquen en actividades de mayor valor, como la innovación y la mejora continua.

2. **Reducción de errores**: Las tareas manuales están sujetas a errores humanos, mientras que la automatización proporciona consistencia y precisión en la ejecución de tareas. Esto ayuda a reducir los errores y minimiza los riesgos asociados con las operaciones manuales, lo que a su vez mejora la calidad y la fiabilidad del software.

3. **Escalabilidad**: A medida que las organizaciones crecen y las demandas de infraestructura y desarrollo aumentan, la automatización permite escalar de manera eficiente. Las tareas automatizadas pueden ejecutarse en múltiples entornos y plataformas de manera consistente y rápida, lo que facilita la gestión de entornos complejos y en constante cambio.

4. **Consistencia**: La automatización garantiza que las tareas se realicen de manera consistente en todos los entornos, lo que ayuda a mantener la coherencia en el desarrollo, las pruebas y la implementación de software. Esto reduce la posibilidad de discrepancias entre entornos y minimiza los problemas causados por configuraciones incorrectas.

5. **Mejora de la colaboración**: La automatización fomenta la colaboración entre equipos al proporcionar una forma estandarizada y reproducible de realizar tareas. Esto facilita la comunicación y la coordinación entre los equipos de desarrollo, operaciones y otros departamentos, lo que conduce a una mayor eficiencia y alineación en toda la organización.

## 5. Eligiendo la herramienta correcta de IaC
El mercado está lleno de herramientas que permiten implementar IaC, y algunas de ellas son las siguientes: 

1. ****Terraform****: Terraform es una herramienta de código abierto desarrollada por HashiCorp que permite definir y administrar la infraestructura como código. Utiliza un lenguaje declarativo para describir recursos de infraestructura y proporciona una amplia compatibilidad con proveedores de servicios en la nube y tecnologías de infraestructura.

2. ****AWS CloudFormation****: AWS CloudFormation es un servicio de AWS que facilita la creación y gestión de recursos en la nube de AWS mediante plantillas de texto JSON o YAML. Permite definir y aprovisionar de manera automatizada recursos como instancias EC2, grupos de Auto Scaling, bases de datos RDS y más.

3. ****Azure Resource Manager (ARM) Templates****: ARM Templates son archivos JSON utilizados para definir y desplegar recursos en Azure. Con ARM Templates, puedes describir todos los recursos necesarios para tu aplicación o solución en Azure, incluidos servicios como máquinas virtuales, redes, bases de datos y más.

4. ****Google Cloud Deployment Manager****: Google Cloud Deployment Manager es una herramienta que te permite definir y desplegar recursos en Google Cloud Platform (GCP) utilizando plantillas YAML o Jinja. Con Deployment Manager, puedes crear y gestionar recursos como máquinas virtuales, redes, bases de datos y más de forma programática.

5. ****Ansible****: Ansible es una herramienta de automatización de TI de código abierto que se utiliza para la provisión de infraestructura, la gestión de configuraciones y la orquestación de aplicaciones. Utiliza un lenguaje simple basado en YAML para describir tareas y configuraciones, lo que facilita la automatización de procesos en una variedad de entornos.

6. ****Chef****: Chef es una plataforma de automatización de infraestructura que utiliza un enfoque basado en la "infraestructura como código" para gestionar y configurar servidores. Utiliza recetas (recipes) y roles para definir configuraciones y políticas, lo que permite la gestión centralizada y la aplicación consistente de configuraciones en múltiples nodos.

7. ****Puppet****: Puppet es una herramienta de gestión de configuración que automatiza el aprovisionamiento, la configuración y la gestión de infraestructura de manera consistente y escalable. Utiliza un lenguaje declarativo para describir el estado deseado de los sistemas, lo que facilita la automatización y la gestión de configuraciones en diferentes plataformas.

### Orquestación de la infraestructura y gestión de su configuración
Para poder entender mejor cuál podría ser la mejor herramienta para nuestra organización, lo mejor será tener en claro los siguientes aspectos. 

1. **Orquestación de Infraestructura (Infrastructure Orchestration)**: La orquestación de infraestructura se refiere al proceso de coordinar y gestionar los recursos de infraestructura de manera automatizada y centralizada. Esto implica la gestión de la infraestructura a nivel macro, incluyendo la creación, configuración, escalado y eliminación de recursos en una infraestructura de TI. La orquestación de infraestructura a menudo se utiliza para gestionar entornos complejos y distribuidos, como clústeres de servidores, redes y almacenamiento en la nube.  Un ejemplo de la tarea a la que se aboca esta categoría sería la de crear 3 servidores con 4Gb de RAM con 2 vCPUs, y cada uno de los servidores deberá de tener un firewall que permita la conexión SSH desde la IP de las oficinas centrales. 

2. **Gestión de Configuración (Configuration Management)**: La gestión de configuración se centra en garantizar que los sistemas y recursos de infraestructura estén configurados de manera consistente y conforme a un estado deseado. Esto implica definir, mantener y aplicar configuraciones de software y sistemas operativos en los servidores y otros dispositivos de la infraestructura. La gestión de configuración ayuda a automatizar la implementación y el mantenimiento de configuraciones, lo que garantiza que los sistemas funcionen de manera confiable y coherente.  Un ejemplo de la tarea a la que se aboca esta categoría sería el tener instalado y configurado en todos los servidores un antivirus cuya versión sesa la 10.0.2

Podemos catalogar las herramientas mencionadas en el apartado anterior en: 

#### Orquestación de Infraestructura (Infrastructure Orchestration):

Algunas de las herramientas que permiten la Orquestación de Infraestructura, son: 

- Terraform
- AWS CloudFormation
- Azure Resource Manager (ARM) Templates
- Google Cloud Deployment Manager

#### Gestión de Configuración (Configuration Management):

Por otro lado, algunas de las herramientas que permiten la Gestión de Configuraciones son: 

- Ansible
- Chef
- Puppet

Si bien algunas herramientas pueden tener capacidades que se superponen entre ambas categorías, generalmente se pueden catalogar según su enfoque principal. Las herramientas de orquestación de infraestructura se centran en la provisión y gestión de recursos de infraestructura en un nivel más alto, mientras que las herramientas de gestión de configuración se utilizan para definir y mantener configuraciones de software y sistemas en los recursos aprovisionados.

### IaC & Gestión de configuraciones
Luego de esta introducción, debemos entender que ambas categorías se solapan en aplicaciones tales como Ansible. Pero cuando nuestra Organización requiera exclusivamente IaC, entonces deberemos de pensar en Terraform. 

Terraform también se puede conectar con la herramienta Ansible para poder dar soporte en la Gestión de configuraciones de los servidores creados mediante Terraform. Es por eso que es tan importante Terraform, ya que nos permite conectarnos a otras aplicaciones tales como Ansible para poder tener un poder total sobre nuestra infraestructura. 

### Pensando Casos de uso para IaC

Ahora pensemos casos de uso simples que nos permitan evaluar a grandes rasgos las distintas posibilidades de adoptar una herramienta de IaC. Hay que poner en
evidencia, que en no todos los casos, la respuesta será Terraform. 

#### Caso de uso nº 1
Pongamos un ejemplo de un caso de uso de una empresa: 
1. La organización basará toda su infraestructura en AWS por los próximos 25 años. 
2. Se requerirá un soporte oficial en caso de que se enfrente con algún tipo de inconveniente con la herramienta de IaC o con su código. 
3. Se requiere algún tipo de interface (GUI) que soporte la generación de código automático. 

Vayamos evaluando punto por punto el requisito de la empresa: 

1. Este punto ya nos indica que no hará falta mudar nuestra infraestructura a otro proveedor distinto de AWS, por lo que nos ahorra el pensar en alguna herramienta que permita la abstracción del código del proveedor. 

2. El servicio de CloudFormation (la herramienta de IaC propietaria de AWS) ofrece un soporte técnico pago que permite a la organización consultar en caso de que algo con respecto a la herramienta o con el código haya salido mal. También Terraform permite esto, dependiendo del nivel de suscripción que se haga a **Terraform Cloud** o **Terraform Enterprise**. Aún así, el soporte no está diseñado para escribir código; los ingenieros de HashiCorp a menudo brindan asistencia y orientación técnica profunda para diagnosticar fallas o problemas de state que se manifiestan debido a errores en la lógica del código HCL. 

3. Para este punto, debemos también hacer hincapié que *per se* Cloudformation no cuenta con una interface que permita la generación de código de manera automática, pero si existe un servicio denominado **AWS Designer** que permita visualizar la arquitectura existente, mostrando los recursos y sus conexiones mediante diagramas. También es posible con esta herramienta diseñar las plantillas en formato JSON o YAML, permitiendo así verificar la dependencia y la estructura del código.  Este servicio se integra de manera nativa con el de Cloudformation. 
Por otro lado, Terraform no cuenta con ninguna herramienta de estas características soportada por Hashicorp. 

**Posible Solución**: la posible solución será utilizar el servicio de Cloudformation junto con algún nivel de suscripción para soporte técnico. 

#### Caso de uso nº 2
En este ejemplo, los requisitos son: 
1. **La organización se basará en una solución híbrida**: se utiliza VMWare para los servidores on-premises y los proveedores AWS, Azure y GCP para la nube. 
2. Se requerirá un soporte oficial en caso de que se enfrente con algún tipo de inconveniente con la herramienta de IaC. Estos casos suceden cuando los errores que está teniendo la herramienta corresponden con lo que se encuentra documentado en la página oficial de la herramienta. 

Analicemos los puntos de nuestro nuevo requerimiento nuevamente: 

1. Con este punto podremos saber que la herramienta requerida será Terraform, ya que soporta múltiples proveedores de infraestructura, a diferencia de CloudFormation que es propietaria de AWS y pensada para funcionar sólo dentro de su ámbito. 
Terraform soporta tanto los proveedores de la nube como también a VMWare. Esto último se pude buscar en la página oficial de [proveedores de Terraform](https://registry.terraform.io/browse/providers). Se puede observar que VMWare se encuentra soportado mediante [VSphere](https://registry.terraform.io/providers/hashicorp/vsphere/latest)

2. La compañía que gestiona Terraform, se llama Hashicorp y si vamos a su página oficial, podremos ver que existe la posibilidad de obtener soporte para la licencia
   *Enterprise* por lo tanto deberemos de pensar en este gasto para poder obtener este tipo de beneficio. 

**Posible Solución**: la posible solución en este caso será utilizar Terraform. 
