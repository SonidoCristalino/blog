+++ 
date = '2025-12-02T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 2'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
+++

## Creando una IP Elástica con Terraform
### Advertencia sobre el costo
Es importante tener en cuenta que si una IP elástica está asociada a una instancia en ejecución, **no se incurre en cargos adicionales** por la IP elástica mientras la instancia **esté en ejecución**. Sin embargo, si una instancia se detiene y la IP elástica sigue desplegada (es decir, que no se ha eliminado) pero no se encuentra asociada a ninguna instancia en ejecución, se pueden aplicar cargos por la **IP elástica no utilizada**.

Es recomendable revisar la documentación oficial de AWS o consultar directamente la página de precios de AWS para obtener información actualizada sobre los costos de las IP elásticas en la región específica en la que estés utilizando los servicios de AWS.

### ¿Qué es una IP Elástica en AWS?
Una IP elástica en Amazon Web Services (AWS) es una dirección **IP estática** que puedes asignar a instancias en ejecución en la nube de AWS. A diferencia de las direcciones IP estáticas tradicionales, las IP elásticas de AWS se pueden asociar y desasociar de instancias en ejecución de forma dinámica, lo que proporciona flexibilidad en la gestión de la infraestructura de red.

Las IP elásticas son útiles en situaciones donde necesitas que una instancia tenga una dirección IP fija y predecible, por ejemplo, para alojar un sitio web o una aplicación con acceso público. Además, las IP elásticas pueden ser migradas entre diferentes instancias, lo que facilita la re asignación de direcciones IP en caso de fallas o actualizaciones de instancias.

Es importante destacar que **AWS cobra por el uso de IP elásticas que no estén asociadas a instancias en ejecución**, pero no cobra por las IP elásticas asociadas a instancias en ejecución. Esto las convierte en una herramienta flexible para la gestión de la infraestructura en la nube de AWS.

### Práctica: nueva Elastic IP
Como siempre, nos basamos en la documentación para poder construir los [recursos necesarios](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip)

El código de muestra es: 

```terraform
resource "aws_eip" "lb" { 
    instance = aws_instance.web.id 
    domain   = "vpc" 
}
```

Como vemos, el código requiere de una instancia para ser adosada. Por lo tanto copiamos el código de la primera instancia creada de EC2, por lo que el código quedaría de la siguiente manera: 

```terraform
provider "aws"{ 
    region     = "us-east-1" 
    access_key = "my-access-key"  # Modificar con tu propia access-key
    secret_key = "my-secret-key"  # Modificar con tu propia secret-key
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
        Name      = "TerraformElasticIp"
    } 
}

resource "aws_eip" "lb" { 
    instance = aws_instance.terraformec2.id 
    domain   = "vpc" 
}
```

### Comando: `terraform destroy`
Luego de haber generado los recursos, debemos de eliminarlos por completo haciendo uso del comando `terraform destroy -auto-approbe`

Esto lo realizamos para que no haya ninguna probabilidad de que nos quede desplegada una IP elástica sin asociar a ninguna instancia. 

## Atributos básicos
### ¿Qué son los atributos?

En Terraform, un atributo para un recurso se refiere a una propiedad específica o un valor dentro de un recurso definido en un archivo de configuración de Terraform. Los recursos en Terraform representan componentes de infraestructura, como instancias de máquinas virtuales, redes, bases de datos, entre otros.

Cuando defines un recurso en un archivo de configuración de Terraform, como por ejemplo un recurso de instancia de máquina virtual en un proveedor de nube, ese recurso tendrá una serie de atributos que puedes configurar. Estos atributos son propiedades específicas que controlan cómo se crea y se gestiona el recurso. Por ejemplo, para una instancia de máquina virtual, algunos atributos comunes podrían ser el tamaño de la instancia, la imagen de sistema operativo a utilizar, las direcciones IP, la configuración de red, etc.

Aquí hay un ejemplo simplificado de un recurso de instancia de máquina virtual en Terraform con algunos atributos configurados: 

```terraform
resource "aws_instance" "example" {
    ami           = "ami-0c55b159cbfafe1f0" 
    instance_type = "t2.micro"
    subnet_id     = "subnet-12345678"
    key_name      = "my_key"
}
```

En este ejemplo:

- `ami`, `instance_type`, `subnet_id`, y `key_name` son atributos del recurso `aws_instance`.
- `ami` define la Amazon Machine Image (AMI) que se utilizará para lanzar la instancia.
- `instance_type` especifica el tipo de instancia.
- `subnet_id` indica en qué subred se lanzará la instancia.
- `key_name` especifica el par de claves SSH que se utilizará para acceder a la instancia.

Los atributos pueden variar según el proveedor de la nube y el tipo de recurso que estemos utilizando en nuestra configuración de Terraform. Cada recurso tiene su propio conjunto de atributos que se pueden configurar para ajustarse a nuestras necesidades específicas de infraestructura.

### Documentación de los atributos
En todo recurso gestionado por Terraform, tendremos la sección correspondiente en la documentación, por ejemplo, para las instancias de AWS se tiene el [siguiente link](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#attribute-reference)

### Práctica

Seguimos utilizando el archivo de la lección nº 38. Una vez que aplicamos el plan de terraform, vamos a ver que en el directorio se crea un archivo denominado `terraform.tfstate`. 

Si lo analizamos, este archivo lo que tiene es toda la información que recolecta Terraform de los recursos creados. Podremos observar que hay una sección destinada a ambos recursos (EIP y EC2) denominado como `attributes`: 

```JSON
  "instances": [ 
    { 
    "schema_version": 1, 
        "attributes": { 
            "ami": "ami-0d7a109bf30624c99", 
            "arn": "arn:aws:ec2:us-east-1:657164278704:instance/i-05a77de4d83cc5438", 
            "associate_public_ip_address": true, 
            "availability_zone": "us-east-1a", 
            "capacity_reservation_specification": [ 
                { 
                    "capacity_reservation_preference": "open", 
                    "capacity_reservation_target": [] 
                } 
            ],
         ...        # Muchas cosas más
    ], 
    "ephemeral_block_device": [], 
    "get_password_data": false, 
    "hibernation": false, 
    "host_id": "", 
    "host_resource_group_arn": null, 
    "iam_instance_profile": "", 
    "id": "i-05a77de4d83cc5438", 
    "instance_initiated_shutdown_behavior": "stop", 
    "instance_lifecycle": "", 
    "instance_market_options": [], 
    "instance_state": "running", 
    "instance_type": "t2.micro", 
    "ipv6_address_count": 0, 
    "ipv6_addresses": [], 
    "key_name": "", 
    "launch_template": [], 
    "maintenance_options": [ ...
```

Como vemos, la información desplegada en este archivo se obtiene de lo generado por Terraform en el proveedor de AWS. La información obtenida es la que se va actualizando cada vez que se invoca el comando `terraform plan` o `terraform apply`. 

Lo que podemos hacer con este archivo, es ir a la documentación de Terraform del recurso que hayamos aplicado los cambios, buscar el atributo deseado y luego buscarlo en el archivo `terraform.tfstate`. Por ejemplo, desde la documentación relacionada a la instancia, podemos buscar lo siguiente: `host_resource_group` y nos encontraremos con [este link](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/resourcegroups_group). 

Para entender cada uno de los atributos listados en el archivo `terraform.tfstate` es conveniente buscarlos en la documentación relacionada al recurso de esos atributos. Todos los atributos de algún recurso generados mediante Terraform se generarán en el archivo `terraform.tfstate`, por más nosotros no hayamos declarado el atributo de manera formal dentro del script, algunos valores se generarán de manera automática y se guardarán dentro de este archivo. 

## Referencias cruzadas para los atributos de un Recurso

En los ambientes productivos, lo que se tienen son muchos recursos generados a partir de otros recursos, o quizás de atributos que dependan de una variable que se encuentra en otro recurso, por lo tanto es muy difícil poder relacionar cada atributo con cada recurso. 

Pensemos que podemos tener: 
1. Por un lado la creación de una Elastic IP (EIP) en AWS
2. Por el otro, un Security Group el cual tenga una inbound rule que permita el ingreso de todo el tráfico proveniente de la EIP. 

Como vemos, el segundo recurso, es decir, el Security Group, depende de un valor del primer recurso (EIP). 

### Requerimiento
El siguiente será el flujo de trabajo que se debe realizar para que cada recurso se genere de forma, en orden correcto y sin generar
error alguno: 
1. Primero deberá de crearse el recurso de la Elastic IP y de esta forma obtener su dirección IP. 
2. Luego deberá de crearse el recurso de SG el cual tendrá una inbound rule asociada para que todo el tráfico proveniente de la dirección IP del recurso EIP no se vea eliminado por el firewall. 

Entonces la pregunta es ¿cómo lograr esto? ¿cómo lograr asegurarnos que un recurso se creará primero que otro para que las dependencias
se satisfasgan de manera efectiva?. 

### Práctica

Como parte de la solución, lo que debemos hacer como primera medida es investigar los atributos relacionados a la EIP y para ello vamos a la [documentación de Terraform](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip#attribute-reference)

Aquí podremos ver un montón de atributos de los que nos podemos servir para lograr nuestro cometido. Como por ejemplo, podemos ver que un atributo que Terraform obtendrá será el de la `public_ip` que es la dirección IP que nosotros buscamos para poder generar la inbound rule de nuestro recurso SG.

Hasta ahora podemos notar que ya hemos completado parte de la solución, ya que sabemos que un atributo de nuestro recurso EIP será el que debamos utilizar en la definición de nuestra inbound rule. 

Terraform tiene una característica que nos permite referenciar un atributo de un recurso y utilizarlo como atributo en otro recurso. 

Y la forma de referenciar ese atributo es mediante la siguiente nomenclatura:  `<RESOURCE-TYPE>.<LOCAL-NAME>.<ATTRIBUTE>`

Por lo tanto, si obtenemos el código del archivo `SecurityGroup.tf` e implementamos lo que se sugiere en esta lección, podemos obtener lo siguiente: 

```terraform
resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id
    cidr_ipv4         = aws_eip.lb.public_ip    # -> Acá vemos cómo es que se referencia al EIP
    from_port         = 80
    ip_protocol       = "tcp"
    to_port           = 80
    tags              = {
        Name          = "Terraform-InboundRule"
    } 
}
 resource "aws_eip" "lb" { 
    instance = aws_instance.terraformec2.id
    domain   = "vpc"
    tags     = {
        Name = "ElasticIP from Terraform"
    } 
}
```

Una de las características importantes que implementa Terraform es que va a entender cómo proceder en el orden de creación de los recursos para poder generarlos sin que haya problemas de obtener los atributos correctos entre dependencias. 

Pero así como está el código, NO va a funcionar, porque debemos ver que al atributo que le asignamos la dirección pública del EIP, es decir `cidr_ipv4` espera una cadena, además de un rango de direcciones IP, algo como "132.35.55.142/16" y para ello utilizamos una suplantación de variables como se utiliza en bash scripting (con `${}`) denominada como **interpolación de variables**, quedando de la siguiente manera:

```terraform
resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id 
    cidr_ipv4         = "${aws_eip.lb.public_ip}/16" 
    from_port         = 80 
    ip_protocol       = "tcp" 
    to_port           = 80
    tags   = { 
        Name = "Terraform-InboundRule" 
    } 
}
resource "aws_eip" "lb" { 
    instance = aws_instance.terraformec2.id 
    domain   = "vpc"
    tags   = { 
        Name = "ElasticIP from Terraform" 
    } 
}
```

De esta forma, la variable  `aws_eip.lb.public_ip` será reemplazada por un string y convertida a un rango de direcciones junto con el `/16` final. 

#### Salida del comando `terraform apply`
Una vez que invoquemos el comando `terraform apply`, en la salida podremos visualizar el orden en el que es construido cada uno de los recursos que estamos generando. Como dijimos anteriormente Terraform es lo bastante inteligente como para saber crear el orden de dependencias sin que nosotros se lo especifiquemos. 

## Cross Resource Attribute References 
### Advertencia
Para esta práctica construiremos los siguientes recursos: 
1. Instancia EC2. 
2. Security Group. 
3. Elastic IP asociada a la instancia. 
4. Inbound Rule asociada a SG y cuyo rango se obtiene de Elastic IP. 

Como se comentó anteriormente, el problema que existe, es que si el recurso Elastic IP no se encuentra relacionada a una instancia de AWS, se nos cobrará mucho más que el tenerla asociada. Por lo tanto, habrá que estar atentos de invocar el comando `terraform destroy` luego de haber finalizado esta práctica. 

### Práctica

Lo que haremos es como se dijo en la lección anterior, el utilizar el siguiente código para poder generar 4 recursos para poder demostrar cómo es que se gestionan los atributos cruzados. Por lo tanto parte del código es el siguiente: 

```terraform
## Instancia 
resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99" 
    instance_type = "t2.micro"
    tags   = { 
        Name = "TerraformEC2Instance" 
    } 
}

## Security Group 
resource "aws_security_group" "terraform-firewall" { 
    name        = "terraform-firewall"
    description = "Managed from Terraform"
    tags        = {
        Name    = "Terraform-SG"
    } 
}

## Inbound Rule 
resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    security_group_id = aws_security_group.terraform-firewall.id
    cidr_ipv4         = "${aws_eip.lb.public_ip}/32"
    from_port         = 443
    ip_protocol       = "tcp"
    to_port           = 443
    tags              = {
        Name          = "Terraform-InboundRule"
    } 
}

## Elastic IP 
resource "aws_eip" "lb" { 
    instance = aws_instance.terraformec2.id
    domain   = "vpc"
    tags     = {
        Name = "ElasticIP from Terraform"
    } 
}
```

Luego de esto, invocamos el comando `terraform plan` y vamos a poder notar que el plan es crear 4 recursos: 

```bash
Plan: 4 to add, 0 to change, 0 to destroy.
```

#### Aplicamos los cambios
Si luego ingresamos `terraform apply` entonces podremos obtener el orden de creación de cada uno de los recursos planificados: 

```bash
aws_security_group.terraform-firewall: Creating... 
aws_instance.terraformec2: Creating... 
aws_security_group.terraform-firewall: 
Creation complete after 4s [id=sg-08b9710ca6334833c] 
aws_instance.terraformec2: Still creating... [10s elapsed] 
aws_instance.terraformec2: Still creating... [20s elapsed] 
aws_instance.terraformec2: Still creating... [30s elapsed] 
aws_instance.terraformec2: Creation complete after 35s [id=i-04efe94814c26bcdc] 
aws_eip.lb: Creating... 
aws_eip.lb: Creation complete after 3s [id=eipalloc-07b7caf93ff4e22f5] 
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Creating... 
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Creation complete after 1s [id=sgr-00fe1a384b40435a6]
```

En esta salida podemos ver cómo fueron creados cada uno de los recursos y en el orden correcto para poder permitir que los valores que tienen referencia cruzada puedan ser invocados y creados sin ningún problema. 

Luego, lo que podemos hacer es verificar mediante el archivo `terraform.tfstate` los atributos creados para cada uno de los recursos, así como también podemos observarlo desde el lado de AWS. 

En esta instancia, podemos ver que el valor asignado al atributo `public_ip` es el mismo para el recurso Elastic IP: 

```JSON
"instances": [ 
    { 
        "schema_version": 0, 
        "attributes": { 
        ... 
        "private_ip": "172.31.91.244", 
        "public_dns": "ec2-54-157-225-150.compute-1.amazonaws.com", 
        "public_ip": "54.157.225.150", 
        ...

## Elastic IP 
    "type": "aws_eip", 
    "name": "lb", 
    "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]", 
    "instances": [ 
        { 
            ... 
            "private_ip": "172.31.91.244", 
            "public_dns": "ec2-54-157-225-150.compute-1.amazonaws.com", 
            "public_ip": "54.157.225.150", 
            "public_ipv4_pool": "amazon", 
            "tags": 
                {
                    ...

## Inbound Rule 
    "name": "allow_80_ipv4", 
    "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]", 
    "instances": [ 
        { 
            "schema_version": 0, 
            "attributes": { "arn": "arn:aws:ec2:us-east-1:657164278704:security-group-rule/sgr-00fe1a384b40435a6", 
            "cidr_ipv4": "54.157.225.150/32",
            ...
```

Por lo tanto, podemos ver que se han creado con satisfacción y de forma correcta todos los valores cruzados de los recursos que teníamos planificados. 

### Comando `Terraform destroy`
Cuando aplicamos el comando `terraform destroy` podemos observar que Terraform automáticamente sabe cómo destruir los recursos de forma correcta para que no haya errores: 

```bash
Plan: 0 to add, 0 to change, 4 to destroy.
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Destroying... [id=sgr-00fe1a384b40435a6]
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Destruction complete after 1s
aws_eip.lb: Destroying... [id=eipalloc-07b7caf93ff4e22f5]
aws_security_group.terraform-firewall: Destroying... [id=sg-08b9710ca6334833c]
aws_security_group.terraform-firewall: Destruction complete after 2s
aws_eip.lb: Destruction complete after 2s
aws_instance.terraformec2: Destroying... [id=i-04efe94814c26bcdc]
aws_instance.terraformec2: Still destroying... [id=i-04efe94814c26bcdc, 10s elapsed]
aws_instance.terraformec2: Still destroying... [id=i-04efe94814c26bcdc, 20s elapsed]
aws_instance.terraformec2: Still destroying... [id=i-04efe94814c26bcdc, 30s elapsed]
aws_instance.terraformec2: Still destroying... [id=i-04efe94814c26bcdc, 40s elapsed]
aws_instance.terraformec2: Destruction complete after 42s
```

### Creación a la mitad
También podría suceder la siguiente secuencia de acciones: 
1. Una vez que finalizamos de generar nuestro código en Terraform, aplicamos el comando `terraform validate` para saber si nuestro código escrito es correcto. 
2. Luego aplicamos el comando `terraform plan` para que nos cree un plan de forma correcta
3. Luego aplicamos el comando `terraform apply`. Pero en este punto puede suceder que parte de los recursos se crearon de forma correcta y luego por algún error, el comando `terraform apply` sale con un **error inesperado**. 

#### Solución a la creación parcial
¿Qué sucede con esta situación? Podemos optar por dos opciones: 
1. Resolver las cosas de manera manual (**NO ES LO RECOMENDADO**). En esta opción, lo que nos puede quedar por crear quizás es la regla inbound rule y quizás pueda generarse mediante la consola de AWS. En este caso quizás lo que pueda suceder es que si lo solucionamos a mano y luego modificamos el código agregando la solución y otros recursos, y luego el comando `terraform plan` y `terraform apply`, puede que quede sanjado el problema. 
2. La segunda solución (y **la recomendada**) es destruir todo y comenzar de nuevo. Por lo tanto, primero llamar a `terraform destroy` para que se elimine parte de los recursos creados, solucionar el error en el código y luego volver a invocar al comando `terraform apply`. De esta forma tenemos el estado desired state igual que el current state y todo sigue funcionando sin problema alguno mediante Terraform. 

### Advertencia sobre plan y apply
Si bien el comando `terraform plan` nos puede servir para diagramar el plan a implementar, esto no significa que una vez que invoquemos el comando `terraform apply` este vaya a funcionar correctamente. Por lo tanto hay que tener en mente cómo proceder en caso de que el comando `terraform apply` no funcione como esperábamos. 

# Continuará...

En la próxima parte estaremos viendo lo que son los **outputs values**, su utilidad y porqué son tan importantes para nuestro día a día. 

¡Hasta pronto!
