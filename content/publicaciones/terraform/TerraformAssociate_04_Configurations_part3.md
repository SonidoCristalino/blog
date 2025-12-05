
+++ 
date = '2025-12-03T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 3'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
+++

## Output Values
### ¿Qué son los valores de salida?
En Terraform, los **output values** (valores de salida) son una forma de exponer información útil o resultados específicos después de que se ha aplicado la
configuración de infraestructura. Estos valores pueden ser variables, atributos o cualquier otra información generada como resultado de la ejecución de la
configuración de Terraform.

Cuando definimos outputs en la configuración de Terraform, estás especificando qué información deseas que Terraform muestre una vez que se ha aplicado la
configuración. Esto puede ser útil para mostrar direcciones IP, identificadores de recursos, URLs o cualquier otra información relevante para el usuario o para
otros sistemas que dependen de la infraestructura.

Aquí un ejemplo simple de cómo se definen los *output values* en Terraform: 

```hcl
output "instance_ip" {
    value = aws_instance.example.public_ip
}
```

En este ejemplo, `instance_ip` es el nombre del output. El valor de este output es la dirección IP pública de la instancia de AWS definida con el nombre `example`.
Cuando aplicamos esta configuración y luego ejecutas `terraform output`, Terraform mostrará la dirección IP pública de la instancia.

Podemos utilizar los "output values" de varias maneras, como integrarlos con otros sistemas, pasarlos como entrada a otras herramientas o scripts, o simplemente
para proporcionar información a los usuarios sobre la infraestructura creada. Son una forma útil de comunicar información relevante generada por Terraform después
de aplicar la configuración.

Hay que tener en cuenta que esta información se nos informará por pantalla luego de que el comando `terraform apply` haya finalizado. 

Si nosotros generamos una instancia de EC2, luego de haber ingresado el comando `terraform apply` lo que vamos a ver es una lista de atributos relacionados a
nuestra instancia pero no se informa, por ejemplo nuestra dirección IP. Para que esto suceda, podremos ingresar a nuestra cuenta de AWS y ver la IP desde la
consola. 

Pero la realidad es que es muy trabajoso hacer esto y por lo tanto lo que podemos hacer es que al finalizar el comando `terraform apply`, automáticamente Terraform
nos muestre la dirección en pantalla. 

### 41. Práctica
Hay que tener en cuenta que para este ejemplo se agregaron 2 componentes más que no modifican en nada lo anteriormente mencionado, como ser un *Security Group* y una
*Inbound Rule* asociada. 

Para poder poner esto en práctica, lo que vamos a generar es simplemente una instancia de EC2 con el siguiente código y la variable de salida: 

```terraform
resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
        Name      = "TerraformEC2Instance"
    } 
}

output "EC2_public_ip" { 
    value = aws_instance.terraformec2.public_ip 
}
```

Esto generará a la hora de finalizar con el comando, la siguiente salida (siendo N un número natural que conforma la dirección IP pública): 

```bash
EC2_public_ip: NNN.NNN.NNN.NNN
```
Luego de aplicar el comando `terraform apply` podemos ver la salida del comando donde se nos indica el orden de creación de cada recurso, como también la variable
de salida deseada: 

```bash

Changes to Outputs:
  + EC2_public_ip = (known after apply)
aws_security_group.terraform-firewall: Creating...
aws_instance.terraformec2: Creating...
aws_security_group.terraform-firewall: Creation complete after 4s [id=sg-0d31d2401a575ae5a]
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Creating...
aws_vpc_security_group_ingress_rule.allow_80_ipv4: Creation complete after 1s [id=sgr-0ac63f4b227856ae6]
aws_instance.terraformec2: Still creating... [10s elapsed]
aws_instance.terraformec2: Still creating... [20s elapsed]
aws_instance.terraformec2: Creation complete after 25s [id=i-0b06b34e6d78c33e3]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

Outputs:

EC2_public_ip = "54.164.75.253"
```

#### Concatenación de salidas
También podemos utilizar la interpolación de variables para poder agregar más información a la salida deseada, como por ejemplo: 

```terraform
output "URL Server" { 
    value = "https://${aws_instance.terraformec2.public_ip}/80" 
}
```

Esto se puede aplicar directamente haciendo `terraform plan` y luego `terraform apply`. Es decir, que no hace falta eliminar la infraestructura y volver a
implementar para que se muestren las variables de salida. 

Por lo tanto, la salida es la siguiente: 

```bash
Changes to Outputs:
  ~ EC2_public_ip = "54.164.75.253" -> "https//54.164.75.253/8080"

You can apply this plan to save these new output values to the Terraform state, without changing any real
infrastructure.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

EC2_public_ip = "https://54.164.75.253/8080"
```

#### Visualizar todos los atributos
También existe la posibilidad de que queramos visualizar todos los atributos creados por el recurso y en ese caso lo único que hacemos es llamar al recurso en el
que estamos interesados, sin solicitar el atributo: 

```terraform
resource "aws_instance" "terraformec2" {
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
    Name          = "TerraformEC2Instance"
    }
}

# Listamos todos los atributos asociados al recurso. 
output "EC2_public_ip" {
    value = aws_instance.terraformec2
}
```

De esta forma, cuando se implementen los cambios mediante `terraform apply` se nos listaran todos los atributos de la instancia creada o modificada. La salida del
comando será: 

```JSON
EC2_public_ip = {
  "ami" = "ami-0d7a109bf30624c99"
  "arn" = "arn:aws:ec2:us-east-1:657164278704:instance/i-0b06b34e6d78c33e3"
  "associate_public_ip_address" = true
  "availability_zone" = "us-east-1a"
  "capacity_reservation_specification" = tolist([
    {
      "capacity_reservation_preference" = "open"
      "capacity_reservation_target" = tolist([])
    },
  ])
  "cpu_core_count" = 1
  "cpu_options" = tolist([
    {
      "amd_sev_snp" = ""
      "core_count" = 1
      "threads_per_core" = 1
    },
  ])
  "cpu_threads_per_core" = 1
  "credit_specification" = tolist([
    {
      "cpu_credits" = "standard"
    },
  ])
  "disable_api_stop" = false
  "disable_api_termination" = false
  "ebs_block_device" = toset([])
  "ebs_optimized" = false
  "enclave_options" = tolist([
    {
      "enabled" = false
    },
  ])
  "ephemeral_block_device" = toset([])
  "get_password_data" = false
  "hibernation" = false
  "host_id" = ""
  "host_resource_group_arn" = tostring(null)
  "iam_instance_profile" = ""
  "id" = "i-0b06b34e6d78c33e3"
  "instance_initiated_shutdown_behavior" = "stop"
  "instance_lifecycle" = ""
  "instance_market_options" = tolist([])
  "instance_state" = "running"
  "instance_type" = "t2.micro"
  "ipv6_address_count" = 0
  "ipv6_addresses" = tolist([])
  "key_name" = ""
  "launch_template" = tolist([])
  "maintenance_options" = tolist([
    {
      "auto_recovery" = "default"
    },
  ])
  "metadata_options" = tolist([
    {
      "http_endpoint" = "enabled"
      "http_protocol_ipv6" = "disabled"
      "http_put_response_hop_limit" = 2
      "http_tokens" = "required"
      "instance_metadata_tags" = "disabled"
    },
  ])
  "monitoring" = false
  "network_interface" = toset([])
  "outpost_arn" = ""
  "password_data" = ""
  "placement_group" = ""
  "placement_partition_number" = 0
  "primary_network_interface_id" = "eni-068b4fadf95051f06"
  "private_dns" = "ip-172-31-86-101.ec2.internal"
  "private_dns_name_options" = tolist([
    {
      "enable_resource_name_dns_a_record" = false
      "enable_resource_name_dns_aaaa_record" = false
      "hostname_type" = "ip-name"
    },
  ])
  "private_ip" = "172.31.86.101"
  "public_dns" = "ec2-54-164-75-253.compute-1.amazonaws.com"
  "public_ip" = "54.164.75.253"
  "root_block_device" = tolist([
    {
      "delete_on_termination" = true
      "device_name" = "/dev/xvda"
      "encrypted" = false
      "iops" = 3000
      "kms_key_id" = ""
      "tags" = tomap({})
      "tags_all" = tomap({})
      "throughput" = 125
      "volume_id" = "vol-0f459e140ba7ca838"
      "volume_size" = 8
      "volume_type" = "gp3"
    },
  ])
  "secondary_private_ips" = toset([])
  "security_groups" = toset([
    "default",
  ])
  "source_dest_check" = true
  "spot_instance_request_id" = ""
  "subnet_id" = "subnet-09096255cb789eefc"
  "tags" = tomap({
    "Name" = "TerraformEC2Instance"
  })
  "tags_all" = tomap({
    "Name" = "TerraformEC2Instance"
  })
  "tenancy" = "default"
  "timeouts" = null /* object */
  "user_data" = tostring(null)
  "user_data_base64" = tostring(null)
  "user_data_replace_on_change" = false
  "volume_tags" = tomap(null) /* of string */
  "vpc_security_group_ids" = toset([
    "sg-08548269db38a916f",
  ])
}*

```

### Valores referenciados a través de otros proyectos

Estas variables de salida, pueden ser compartidas mediante otros scripts de Terraform que no necesariamente deban estar o situarse dentro del mismo proyecto. Esto
es así ya que como se mencionó anteriormente, Terraform luego de aplicar el comando `terraform plan` o el comando `terraform apply` crea o modifica el archivo
`terraform.tfstate` donde se mantiene el estado actual de todos los recursos y atributos creados. 

Es posible que un **Proyecto B** acceda a la variable de salida de un **Proyecto A** para poder incorporarlo en la infraestructura a desplegar por éste último. 

Si bien esto se verá en lecciones posteriores, se hace mención para que se sepa que estas variables de salida son de un uso mucho más extenso que simplemente
imprimirse por pantalla. 

## Continuará ...

En la próxima parte, estaremos viendo las variables en Terraform. 

¡Abrazo de gol!

