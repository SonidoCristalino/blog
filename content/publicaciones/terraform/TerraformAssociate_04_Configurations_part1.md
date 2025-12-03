+++ 
date = '2025-12-02T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 1'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
+++

# Sección nº 4
## Vista general sobre la sección actual

En esta sección iremos viendo distintas configuraciones permitidas en Terraform. Para ello es preferible mantener ordenadas las carpetas que nosotros vayamos a crear para mantener dentro de ellas todos los scripts relacionadas a las distintas secciones de aprendizaje de la herramienta, como pueden ser: 
- Creación de las instancias de EC2
- Recursos de tutoría
- Expresiones condicionales

Uno de los repositorios donde se tienen varios ejemplos de lo que hablaremos en esta sección, es el siguiente: 
* **GitHub Repository**: https://github.com/zealvora/terraform-beginner-to-advanced-resource

### Importante: utilizar `terraform destroy`
Siempre **debemos destruir** los recursos luego de haber finalizado la práctica mediante el comando `terraform destroy`. Esto nos permitirá evitar gastar dinero con recursos que quedarán generados en el proveedor de AWS de manera obsoleta. 

## Alcance del tutorial
Debemos de comprender que este tutorial está pensado para poner de manifiesto las ventajas de utilizar Terraform para orquestar y
gestionar nuestra infraestructura. Es importante notar que de ninguna manera se hará uso de casi los 200 servicios que tiene Amazon
desplegados en su nube. Simplemente se utilizarán aquellos servicios básicos que generen menos gastos al usuario. 

Por lo tanto, se utilizarán pocos servicios de AWS que sean soporte para que podamos entender los conceptos fundamentales de Terraform como herramienta de IaC. Algunos de los los servicios a utilizar de AWS son: 
* EC2
* Security Groups (firewalls)
* IAM Users
* Elastic IP (Public IP address)
* Quizás algún otro servicio para demostrar alguna particularidad de Terraform. 

Cuando haga falta, se hará una introducción a cada uno de los servicios de AWS que iremos viendo para que se comprenda mejor el uso que
le daremos. 

En caso de que se tenga el conocimiento de lo que es un **Firewall** o un **Security groups** por ejemplo, entonces daremos una
introducción sobre ellos. 

## Conceptos básicos de Firewall en AWS
### Introducción a los puertos

En Internet, la comunicación entre dispositivos se realiza a través de protocolos de red, como el *Protocolo de Control de Transmisión* (TCP) o el *Protocolo de Datagramas de Usuario* (UDP). Los puertos son números de identificación asociados con direcciones IP que permiten que múltiples servicios o aplicaciones se comuniquen simultáneamente en un mismo dispositivo.

Aquí hay algunas funciones clave de los puertos en Internet:

1. **Identificación de servicios**: Los puertos permiten que múltiples servicios se ejecuten en una sola máquina. Cada servicio tiene asignado un número de puerto único. Por ejemplo, el servicio web generalmente utiliza el puerto 80 para HTTP y el puerto 443 para HTTPS.

2. **Multiplexación**: Los puertos permiten que una única dirección IP pueda admitir múltiples conexiones simultáneas. Esto se logra asignando diferentes números de puerto a cada conexión. Cuando un paquete de datos llega a una máquina, el sistema operativo lo dirige al proceso correspondiente en función del puerto al que está dirigido.

3. **Comunicación cliente-servidor**: Los puertos facilitan la comunicación entre clientes y servidores.  Cuando un cliente solicita un servicio a un servidor (por ejemplo, una página web), el cliente envía la solicitud al puerto específico asociado con ese servicio en el servidor. El servidor luego responde al cliente a través del mismo puerto.

4. **Seguridad**: Los puertos también pueden utilizarse para controlar el acceso a servicios específicos. Por ejemplo, un firewall puede configurarse para permitir o bloquear el tráfico en ciertos puertos, lo que ayuda a proteger la red contra ataques maliciosos.

Por lo tanto, los puertos en Internet son herramientas fundamentales que permiten la comunicación entre dispositivos, la identificación de servicios y la seguridad en la red. Son una parte esencial de cómo funcionan las redes informáticas y son utilizados por ingenieros de software y administradores de redes para garantizar un funcionamiento eficiente y seguro de los sistemas.

Hay que entender que cada aplicación que esté corriendo el servidor, escuchará en un puerto determinado. Estos puertos pueden ser conocidos o no, pero siempre una aplicación deberá de tener asociada un puerto determinado. 

### AWS: Apertura de puertos
Si queremos saber los puertos que se encuentran abiertos en alguna instancia de AWS que estemos corriendo (siempre que sea de tipo Linux), deberemos de conectarnos a ella tanto por SSH o por CloudShell e ingresar: 
1. Para listar los puertos: `netstat -ntlp`
2. Si tenemos una instancia levantada en AWS, podremos ver que los puertos que se encuentran posiblemente abiertos sean los los puertos 22 (SSH) y el puerto 80 (HTTP)

### Conceptos básicos de firewall

Un firewall es un componente de seguridad fundamental en redes informáticas que controla y regula el tráfico de red basado en un conjunto de reglas predefinidas. Su función principal es proteger una red privada o un dispositivo de accesos no autorizados, ataques maliciosos y otros riesgos de seguridad en Internet.

Aquí hay algunos conceptos clave relacionados con los firewalls:

1. **Filtrado de paquetes**: Un firewall puede inspeccionar los paquetes de datos que entran y salen de una red. Puede permitir o bloquear paquetes basándose en criterios como la dirección IP de origen o destino, el número de puerto, el tipo de protocolo, entre otros.

2. **Control de acceso**: Los firewalls pueden configurarse para permitir o bloquear el tráfico en función de reglas específicas. Por ejemplo, se pueden establecer reglas para permitir el acceso solo a ciertos servicios o para bloquear ciertos tipos de tráfico conocidos por ser maliciosos.

3. **NAT (Traducción de Dirección de Red)**: Algunos firewalls también pueden realizar funciones de NAT, que permiten que múltiples dispositivos en una red privada compartan una única dirección IP pública. Esto ayuda a proteger la identidad y la seguridad de los dispositivos internos.

4. **Inspección de estado**: Los firewalls de inspección de estado pueden monitorear el estado de las conexiones de red y permitir el tráfico de datos asociado con conexiones establecidas legítimamente. Esto ayuda a prevenir ataques como los de denegación de servicio (DDoS) y otros intentos de explotar conexiones no autorizadas.

5. **Seguridad perimetral**: Los firewalls suelen ubicarse en la frontera entre una red privada y una red pública, como Internet. Esta posición estratégica les permite proteger la red interna de amenazas externas.

Por lo tanto, un firewall es una barrera de seguridad muy importante que protege las redes informáticas al controlar el tráfico de red según reglas predefinidas. Ayuda a prevenir accesos no autorizados, ataques maliciosos y otras amenazas, y es una parte fundamental de la infraestructura de seguridad de cualquier red, ya sea a nivel de red doméstica, empresarial o de proveedores de servicios de Internet.

### AWS: Security Groups

En el contexto de AWS (Amazon Web Services), un Security Group es un mecanismo de seguridad que actúa como un firewall virtual para controlar el tráfico de red hacia y desde las instancias de EC2 (Elastic Compute Cloud), así como también para otras instancias de servicios de AWS que pueden estar asociadas.

Aquí hay algunos puntos clave sobre los Security Groups en AWS:

1. **Control de acceso**: Al igual que un firewall tradicional, un Security Group permite definir reglas para permitir o bloquear el tráfico de red basado en direcciones IP de origen, direcciones IP de destino, números de puerto y protocolos.

2. **Asociación con instancias**: Cada instancia de EC2 en AWS debe estar asociada con uno o más Security Groups. Estos grupos controlan el tráfico de red hacia y desde la instancia.

3. **Reglas de seguridad**: Las reglas de un Security Group pueden ser configuradas para permitir el acceso desde ciertas direcciones IP, rangos de direcciones IP, o desde otras instancias de AWS que estén en el mismo grupo o en grupos específicos.

4. **Implementación dinámica**: Los cambios en la configuración de los Security Groups se aplican de manera casi inmediata, lo que permite una gestión ágil y flexible de la seguridad de la red en la nube.

5. **Defensa en profundidad**: Los Security Groups se pueden utilizar en conjunto con otras medidas de seguridad de AWS, como listas de control de acceso (ACL) de red y reglas de enrutamiento, para proporcionar una defensa en profundidad y una seguridad robusta para las aplicaciones alojadas en la nube.

Por lo tanto un Security Group en AWS es una forma de controlar y gestionar la seguridad del tráfico de red hacia y desde las instancias de EC2 y otros servicios de AWS. Funciona de manera similar a un firewall tradicional, pero está diseñado específicamente para entornos de nube y se integra estrechamente con otros servicios de AWS para proporcionar una seguridad integral en la nube.

También debemos recordar que un Security Group permite establecer tanto reglas para **la entrada de datos** (inbound rules) hacia la instancia como también los datos que pueden **salir desde la instancia hacia internet** (outbound rules). 

## Práctica de Security Groups
Si el usuario sabe cómo abordar la creación de una instancia de EC2 mediante la consola de AWS, lo animo a que juegue con los Security
Groups para evidenciar cómo pueden ser configurados. En caso de que el estudiante no sepa cómo realizarlo, posteriormente publicaré un
extenso tutorial sobre la certificación de **AWS Developer** donde se abordarán estos temas con sumo cuidado. 

## Creando reglas de Firewall (Firewalls Rules) usando Terraform
A continuación lo que haremos será crear los siguientes recursos en AWS utilizando Terraform: 
1. Un Security Group (firewall de AWS) llamado localmente `terraform-firewall`
2. **Una regla de entrada (inbound rule)**: allow 80 from `0.0.0.0/0`
3. **Otra regla de salida (outbound rule)**: allow `all`

Terraform siempre va a tener en su documentación los distintos servicios a los que dará soporte. En este caso como vamos a generar un Security Group, lo que podríamos buscar es la documentación de Terraform basado en el servicio de Security Groups (SG) para AWS. Por lo tanto podremos buscar en internet algo como "Terraform Security Group". 

* El link es el [siguiente](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group)

A la hora de haber sido generado este tutorial, podemos observar en el link, que hubieron cambios en la definición del recurso con respecto a las reglas de entrada y de salida, por lo tanto a los nuevos recursos definidos, hay que llamarlos como `aws_vpc_security_group_egress_rule` y `aws_vpc_security_group_ingress_rule`.

### Práctica: create SG
Lo que haremos es copiar el ejemplo que nos provee la documentación de Terraform e ir realizando los cambios según nuestro requerimiento: 

```terraform
resource "aws_security_group" "allow_tls" { 
    name        = "allow_tls"
    description = "Allow TLS inbound traffic and all outbound traffic"
    vpc_id      = aws_vpc.main.id
    tags        = {
        Name    = "allow_tls"
    } 
}
```

Para esta primera parte, lo que hay que tener en cuenta es que tiene las mismas características que si generaríamos el recurso mediante la consola web de AWS, ya que tendríamos que ingresar para ello un nombre, una descripción, y una VPC asociada. Por lo tanto, si sabemos cómo generar el recurso de forma manual, mediante Terraform nos será una tarea transparente. 

Por lo tanto, vamos completando nuestro script según el requerimiento expuesto al comienzo de la lección y lo llamamos `terraform-securityGroup.tf`: 

```terraform
resource "aws_security_group" "terraform-firewall" { 
    name        = "terraform-firewall"
    description = "Managed from Terraform"
    #vpc_id     = aws_vpc.main.id
    tags        = {
        Name    = "Terraform-SG"
    } 
}
```

Podemos buscar a lo largo de la documentación y ver que el parámetro `description` como `vpc_id` son OPCIONALES, por lo tanto se pueden quitar o comentar (que es la opción que implementamos aquí). 

Luego de haber modificado el archivo, invocamos `terraform init` para descargar el provider y luego lo que hacemos es invocar el comando `terraform plan`. Si estamos de acuerdo con el plan, procedemos a invocar el comando `terraform apply -auto-approve`. 

Si vamos a la consola de AWS > EC2 > Security Group vamos a poder notar si el nuevo recurso se generó de forma satisfactoria. Pero veremos que no tiene ninguna regla de entrada ni de salida, por lo tanto deberemos de actualizar el script para poder generarlas. 

### Práctica: creando reglas de SG
Acto seguido, a nuestro script le agregamos los siguientes bloques que conforman los **inbound y outbound** rules. Hay que notar lo siguiente: 
1. Que la nueva nomenclatura de Terraform sobre estas reglas son denominadas recursos, por lo tanto, en la documentación tienen entradas aparte, así como lo tiene el recurso de security group. Por lo tanto tenemos dos páginas distintas: 
    - https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc_security_group_ingress_rule 
    - https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc_security_group_egress_rule
2. El ejemplo que proporciona la documentación son para IPv4 e IPv6. Por lo tanto, lo que haremos es copiar la que se relaciona a **IPv4**. 

El código finalizado es el siguiente: 

```terraform
resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id 
    cidr_ipv4         = "0.0.0.0/0" 
    from_port         = 80 ip_protocol       = "tcp" 
    to_port           = 80 
}

resource "aws_vpc_security_group_egress_rule" "allow_all_traffic_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id 
    cidr_ipv4         = "0.0.0.0/0" 
    ip_protocol       = "-1" # semantically equivalent to all ports 
}
```

Para el recurso `aws_vpc_security_group_ingress_rule` hay que saber que: 
1. Lo que debemos de notar es que el ID de cada variable para `security group id` se compone del recurso del SG (`terraform-firewall`) +
   `.id` (esta nomenclatura la veremos más adelante)
2. Por otra parte, las opciones de `from_port` y `to_port` son para indicar un rango de puertos, por ejemplo 80-100. Esto se puede hacer configurando `from_port = 80` y `to_port = 100`

Es importante también ver que la opción `ip_protocol` del recurso `aws_vpc_security_group_egress_rule` se puede configurar para que, o bien acepte todos los puertos y esto es lo que permite la opción `-1` o especificarle qué puerto debe filtrar. 

### Práctica: modificando la opción to_port
Ahora cambiemos la opción `to_port `del recurso `aws_vpc_security_group_ingress_rule` a 100 para que veamos lo dicho anteriormente con respecto a los rangos de los puertos. Por lo tanto, el código quedaría de la siguiente manera: 

```terraform
resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id 
    cidr_ipv4         = "0.0.0.0/0" 
    from_port         = 80 
    ip_protocol       = "tcp" 
    to_port           = 100 
}
```

Generamos un plan con el comando `terraform plan` y luego aplicamos los cambios `terraform apply` para poder observar estos cambios en la consola de AWS. 

### Práctica: agregar una inbound rule al Security Group por defecto
Otra característica interesante es que el recurso `aws_vpc_security_group_ingress_rule` por ejemplo, cuya opción es `security_group_id` no sólo soporta variables sino también el ID directamente obtenido del SG del Security Group ID. Por ejemplo, el ID de un SG por default que tiene mi cuenta personal de AWS es el siguiente: `sg-08548269db38a916f`. Por lo tanto, se lo asignaremos también a mi SG `default`. 

El código queda de la siguiente manera: 

```terraform
resource "aws_vpc_security_group_ingress_rule" "tempRule" { 
    security_group_id = "sg-08548269db38a916f"
    cidr_ipv4         = "0.0.0.0/0"
    from_port         = 80
    ip_protocol       = "tcp"
    to_port           = 100
    tags              = {
        Name          = "Terraform-tmpRule"
    } 
}
```

Luego podemos ir a la consola de AWS y ver cómo ha impactado en el Security Group. 

## Actualizaciones de código en la documentación

Muchas veces Hashicorp actualiza la documentación referida a cada uno de los providers con los que trabaja ya que del lado del proveedor se agregan nuevas funcionalidades o se modifican otras para mejor funcionamiento.  

Lo importante es entender que siempre vamos a necesitar la documentación a la hora de codificar en Terraform, ya que es imposible codificar de memoria cada uno de los recursos y cada una de las opciones. 

### Retro-compatibilidad
Por lo anteriormente dicho, Terraform permite que cada una de los lanzamientos de las nuevas versiones de los providers sean *retro compatibles* con versiones anteriores, por lo que muchas nuevas viejas funcionalidades funcionarán con las nuevas versiones del plugin del provider. 

Otra cosa interesante es que en la documentación de Terraform, es posible visualizar que debajo de banner de Terraform, tengamos una referencia similar a *Providers / hashicorp / aws / Version 5.42.0*.  Nótese que la versión es posible ingresar a ella con el mouse y listar las versiones viejas para poder tener a mano la documentación referida a esa versión en específica. 

También podemos basarnos en la versión que se encuentra explicitada en la URL, como por ejemplo: 

1. **Esta es la versión actual**: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group
2. **Esta es una versión vieja**: https://registry.terraform.io/providers/hashicorp/aws/5.23.1/docs/resources/security_group

Podemos ver que el tag `latest` se modifica por el número de versión vieja `5.23.1`. Guiándonos de esta forma, también podemos abordar la documentación antigua. 

### Puntos a tener en cuenta

Hashicorp recomienda siempre tener actualizados las nuevas funcionalidades de Terraform pero esto **no significa** que las viejas funcionalidades no funcionen con las nuevas versiones de los proveedores. 

Por lo dicho anteriormente, las organizaciones pueden estar tranquilas que su código no necesitará actualizarse constantemente y por lo tanto el código ya testeado no será necesario modificarlo en cada nueva versión. 

Para empresas muy grandes es mejor quedarse con una versión determinada del proveedor y por lo dicho anteriormente notamos que de ser así, no habrá inconvenientes acerca de que sigan funcionando los recursos codificados con el código antiguo. 

# Continuará... 

En la próxima parte, estaremos viendo qué es una **IP Elástica en AWS**, y cómo crear este recurso de una manera sencilla. Además estaremos respasando los atributos de
los recursos y cómo configurarlos. 


