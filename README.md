# SOCIAL OPLESK
### 
<br/>

# como Instalar y ejecutar opentofus en Local y en AWS

<br/>

Para **instalar opentofu en Fedora, Amazon Linux con o sin WSl los pasos son:**

**Requisitos previos**

- Todo se hace mediante terminal de comandos.


### Instalación en Fedora e hijas de Red Hat

1. **Abrir Terminal CLI**
```
sudo dnf update -y
sudo dnf install opentofu -y
```

2. **Haz check para verificar si opentofu esta bien instalado, ejecutar el comando:** tofu --version

<br/>

### 🟢 Ejecutar opentofu para tener una infraestructura local, es decir modo offline.

```
resource "docker_image" "nginx"{
  name = "nginx:alpine"
  keep_locally = true 
} 

resource "docker_container" "web_server"{
  image =  docker_image.nginx.image_id
   name = "mi-nginx-server"

  ports {
   internal = 80
   external = 8080
  }

  restart = "unless-stopped"  
  
  labels {
    label = "environment"
    value = "dev"
  }
}
```

### 🟢 Ejecutar opentofu para tener una infraestructura en aws, es decir modo online.
```
provider "aws" {
  region = "us-east-1"  --> <tu region>
}

data "aws_ami" "al2023" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-2023.*-x86_64"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

resource "aws_instance" "web" {
  ami                         = data.aws_ami.al2023.id
  instance_type               = "t3.micro"
  subnet_id                   = "subnet-03da60e9fa9cc3b61"  --> <tu subnet>
  key_name                    = "my-key"  --> <tu key-pairs>
  associate_public_ip_address = true

  vpc_security_group_ids = ["sg-0e9bf3b2334aa1d81"]  --> <tu security gropu>

  user_data = <<-EOF
    #!/bin/bash
    dnf update -y
    dnf install -y nginx
    systemctl enable nginx
    systemctl start nginx
  EOF

  tags = {
    Name = "web-t3-micro"
  }
}
```

---
<h3 align="center">SOCIAL OPLESK</h3>
