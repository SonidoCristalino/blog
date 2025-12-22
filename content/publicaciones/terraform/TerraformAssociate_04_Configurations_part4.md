
+++ 
date = '2025-12-05T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 4'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
series = ["Curso Terraform"]
+++

## Revisión general de las variables de Terraform

### Problema

Como bien se sabe, el generar variables **ESTÁTICAS** (o bien denominadas "variables hardcodeadas") tiene el problema que no son una solución optima a la hora de
funcionar en entornos dinámicos. Por ejemplo, podemos pensar en un requerimiento donde se pide generar una VPN con reglas de tipo *inbound rule* (denominadas
*whitelist* o lista blanca). Como se puede pensar, ya es un problema tener que lidiar con una sola *inbound rule*, imagine el problema que sería ingresando todas
estas direcciones a mano, por lo tanto, en organizaciones muy grandes esto puede escalar mucho más allá de cientos de reglas. 

Por lo tanto no es óptimo tener que manejar todo esto mediante valores expresados en el código, sino más bien que es mucho más óptimo y fácil generar **una variable**. 

### Solución

Un mejor acercamiento para dar solución a esto es tener definida una variable global que permita ser alcanzada por todos los recursos cuyo atributo esté definida con ella y de esta manera en caso de tener que modificarse, se modifica en un sólo lugar y replicado en todos los demás lugares. 

Otra posibilidad es tener definas varias variables en un sólo lugar (como puede ser un archivo) y que a partir de ahí sea visible para todos los recursos que se
quieran gestionar con Terraform. 

### Práctica

Archivos a emplear: 

1. `42_variables.tf`
1. `central_variables.tf`

En el archivo `central_variables.tf` podemos observar el siguiente código: 

```terraform
    variable "vpn_ip" { 
        default = "101.0.62.210/32" 
    }

    variable "vpn_port" { 
        default = "80" 
    }
```

Para luego ser utilizado de la siguiente forma en el archivo `42_variables.tf`: 

```terraform
    resource "aws_security_group" "terraform-firewall" { 
        name        = "terraform-firewall" 
        description = "Managed from Terraform"
        tags = { 
            Name = "Terraform-SG" 
        } 
    }

    resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
#       Notar que el id se compone del recurso del SG + "id" 
        security_group_id = aws_security_group.terraform-firewall.id 
        cidr_ipv4         = "${var.vpn_ip}" 
        from_port         = var.vpn_port 
        ip_protocol       = "tcp" 
        to_port           = var.vpn_port
        tags   = { 
            Name = "Terraform-InboundRule" 
        } 
    }
```

Es importante notar que para indicarle a Terraform que debe de ir a leer una variable global, se debe comenzar con el prefijo `var` seguido del nombre local, en
nuestro caso tenemos `var.vpn_ip` y luego `var.vpn_port`. De esta manera Terraform sabrá que deberá de buscar la definición de estas variables en algún archivo por
fuera de los scripts. 

Por lo tanto si invocamos al comando `terraform plan` vamos a poder observar que efectivamente toma los valores correctos: 

```bash
    # aws_vpc_security_group_ingress_rule.allow_80_ipv4 will be created 
    + resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    + arn                    = (known after apply) 
    + cidr_ipv4              = "101.0.62.210/32" 
    + from_port              = 80 
    + id                     = (known after apply) 
    + ip_protocol            = "tcp" 
    + security_group_id      = (known after apply) 
    + security_group_rule_id = (known after apply) 
    + tags                   = { 
    + "Name" = "Terraform-InboundRule" } 
    + tags_all               = { 
    + "Name" = "Terraform-InboundRule" } 
    + to_port                = 80 }

    Plan: 2 to add, 0 to change, 0 to destroy.
```

Podemos ver que en el atributo `cidr_ipv4` como en `from_port` y `to_port` se encuentran gestionados con los valores provenientes del archivo `central-variables.tf`

### Importante
Cabe notar que NO importa el nombre del archivo en el que coloquemos las variables generales (en nuestro caso es `central-variables.tf`). Lo importante es la
extensión `.tf` y la nomenclatura que se utiliza para hacer referencia a ellas dentro del código. Comos se dijo anteriormente, se debe utilizar
`var.<VARIABLE.NAME>`

### Modificación de variable
Lo que podemos hacer ahora, es modificar el archivo `central-variables.tf` y visualizar nuevamente si el cambio ha surgido efecto cuando invocamos `terraform plan`. 

Por lo tanto ahora el archivo `central-variables.tf` queda de la siguiente manera: 

```terraform

    variable "vpn_ip" { default = "255.255.62.210/32" }
    variable "vpn_port" { default = "22" }
```

Invocamos el comando `terraform plan` y vemos la salida: 

```bash
    aws_vpc_security_group_ingress_rule.allow_80_ipv4 will be created 
    + resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
    + arn                    = (known after apply) 
    + cidr_ipv4              = "255.255.62.210/32" 
    + from_port              = 22 
    + id                     = (known after apply) 
    + ip_protocol            = "tcp" 
    + security_group_id      = (known after apply) 
    + security_group_rule_id = (known after apply) 
    + tags                   = { 
    + "Name" = "Terraform-InboundRule" } 
    + tags_all               = { 
    + "Name" = "Terraform-InboundRule" } 
    + to_port                = 22 }

    Plan: 2 to add, 0 to change, 0 to destroy.
```

Podemos ver nuevamente que en el atributo `cidr_ipv4` como en `from_port` y `to_port` se encuentran modificados a los nuevos valores provenientes del archivo
`central-variables.tf`. 

Los beneficios de utilizar variables globales o definidas en un archivo central son: 
1. Tenemos un único lugar donde modificar los valores asociados a la variable y por lo tanto podemos salvar tiempo de errores potenciales. 
2. Acorta el error humano por tener que modificar un solo lugar en vez de andar realizando las modificaciones a lo largo de los archivos de configuración de los
   recursos gestionados a través de los archivos `.tf`

## Terraform Variables - Practica 1

Archivos a utilizar: 

1. `43_GlobalVariables.tf`
1. `43_central_variables.tf`

### Análisis de variables
Es la misma práctica que se hizo en la lección anterior. Simplemente se agregan más variables para poder tener más ejemplos para mostrar al final del comando
`terraform plan`. 

En este ejemplo lo que podemos ver es lo siguiente: 

```terraform
    resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
        security_group_id = aws_security_group.terraform-firewall.id 
        cidr_ipv4         = "${var.vpn_ip}/16" 
        from_port         = var.vpn_from_port 
        ip_protocol       = "tcp" 
        to_port           = var.vpn_to_port
        tags   = { 
            Name = "Terraform-InboundRule" } }

    resource "aws_vpc_security_group_egress_rule" "allow_all_traffic_ipv4" { 
        security_group_id = aws_security_group.terraform-firewall.id 
        cidr_ipv4         = var.vpn_ip ip_protocol       = "-1" 
       tags   = { Name = "Terraform-OutboundRule" } }

```
Podemos ver que el atributo `cidr_ipv4` se encuentra asociado tanto a una variable directa como a un string y que ambos valores son aceptados de forma correcta. Lo interesante para notar es que `terraform plan` también permite realizar una evaluación sobre los valores reemplazados por las variables globales. Por ejemplo, si tenemos el siguiente valor en el archivo `central-variables.tf`: 

```terraform
    variable "vpn_ip" { 
        default = "100.32.62.210/32" 
    }
```

Y luego tengo el siguiente código en el archivo `43_GlobalVariables.tf`: 

```terraform
    resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" { 
        security_group_id = aws_security_group.terraform-firewall.id
        cidr_ipv4         = "${var.vpn_ip}/16"   # -> notar que el reemplazo daría 100.32.62.210/32/16"
        from_port         = var.vpn_from_port
        ip_protocol       = "tcp"
        to_port           = var.vpn_to_port
        tags              = {
            Name          = "Terraform-InboundRule" }
        }
```

La salida de `terraform plan` es la siguiente: 

```bash
    │ Error: Invalid Attribute Value
    │ 
    │   with aws_vpc_security_group_ingress_rule.allow_80_ipv4,
    │   on 43_GlobalVariables.tf line 18, in resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4":
    │   18:   cidr_ipv4         = "${var.vpn_ip}/16"
    │ 
    │ Attribute cidr_ipv4 value must be a valid IPv4 CIDR that represents a network address, got:
    | 255.255.62.210/32/16
```

Y esto es porque realiza primero una interpolación de variables y luego un breve análisis sintáctico para evaluar si los valores reemplazados tienen la estructura gramatical para ser asignados. 

Para corregir los rangos CIDR podemos utilizar la siguiente aplicación: https://www.ipaddressguide.com/cidr

### Interpolación de variables
Luego de corregir los errores del apartado anterior, invocamos el comando `terraform plan`, obtenemos lo siguiente: 

```bash
  + resource "aws_vpc_security_group_egress_rule" "allow_all_traffic_ipv4" {
      + arn                    = (known after apply)
      + cidr_ipv4              = "54.164.75.253/32"
      + id                     = (known after apply)
      + ip_protocol            = "-1"
      + security_group_id      = (known after apply)
      + security_group_rule_id = (known after apply)
      + tags                   = {
          + "Name" = "Terraform-OutboundRule"
        }
      + tags_all               = {
          + "Name" = "Terraform-OutboundRule"
        }
    }

  + resource "aws_vpc_security_group_ingress_rule" "allow_80_ipv4" {
      + arn                    = (known after apply)
      + cidr_ipv4              = "54.164.75.253/32"
      + from_port              = 22
      + id                     = (known after apply)
      + ip_protocol            = "tcp"
      + security_group_id      = (known after apply)
      + security_group_rule_id = (known after apply)
      + tags                   = {
          + "Name" = "Terraform-InboundRule"
        }
      + tags_all               = {
          + "Name" = "Terraform-InboundRule"
        }
      + to_port                = 80
    }

Plan: 3 to add, 0 to change, 0 to destroy.
```

Por lo tanto vemos que la interpolación de variables ha sido exitosa. 

### Importante
Algo a tener en cuenta es que cuando tengamos que afrontar código de producción en Terraform, el código será muy largo, siendo muchos los recursos administrados. Es por eso que lo que conviene en estos casos es tratar de evitar las modificaciones de estos archivos lo más que se pueda. 

Siempre es importante generalizar todo a variables y con ello evitamos de tener que andar navegando y modificando valores a lo largo de archivos enormes donde podemos generar errores con facilidad. 

### Importante 2
Lo que recomiendan las buenas prácticas de Terraform declaradas en el siguiente [link](https://www.terraform-best-practices.com), es utilizar una convención de nombres para los archivos de configuración, permitiendo de esta manera encontrar con mayor certeza y claridad los recursos que estamos buscando. Esto es aplicable tanto para los archivos de configuración como para el de las variables globales. 

Como también es buena práctica en estas `input variables` utilizar el argumento `description` el cual, sólo funciona como una descripción de la variable para poder entender mejor a lo que hace referencia. Es para orientar de forma más eficiente al desarrollador. Por ejemplo de la siguiente manera: 

```terraform
    variable "vpn_ip" {
      default     = "54.164.75.253"
      description = "This is an VNP example implement through Terraform"
    }
```
### Importante 3
Podemos ver que en la definición de una `input variable` no sólo existe el argumento `default`, sino un montón de otros argumentos. Esto se puede encontrar en la [documentación de Hashicorp](https://developer.hashicorp.com/terraform/language/values/variables) para tener una idea de todos los argumentos que soporta. 

## Continuará... 

En las siguientes secciones veremos cómo se definen los archivos de variables (`tfvars`), la importancia de ello en el buen manejo de nuestra infraestructura y
también pondré a disposición en mi repositorio personal los archivos `.tf` utilizados a lo largo de todas las secciones previas y posteriores. 

¡Abrazo de gol para todos!
