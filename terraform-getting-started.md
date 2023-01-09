# Getting Started With Terraform

Terraform is an infrastructure as code tool you can use to define and manage infrastructure with human-readable configuration files. Managing infrastructure as code helps you save time and reduce human error.

In this guide, you will:

- Install Terraform
- Create a configuration file
- Initialize a Terraform project
- Apply the configuration that you defined
- Tear down the infrastructure that you provisioned 

## Prerequisites

To follow this tutorial, you will need to:

- [Install Terraform](https://developer.hashicorp.com/terraform/downloads)
- [Install Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Use a terminal on a Mac

## Create a Configuration File

In this section, you will create a [configuration file](https://developer.hashicorp.com/terraform/language) to provision an NGINX server.

Create a directory named `terraform-demo`:

```
$ mkdir terraform-demo
```

Navigate to your new directory:

```
$ cd terraform-demo
```

Create a file called `main.tf` and paste in the following configuration code:

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.13.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 8000
  }
}
```

## Initialize a Terraform Project

In this section, you will initialize your Terraform project. Initializing downloads the plugin that Terraform needs to use Docker.

Use the [`init`](https://developer.hashicorp.com/terraform/cli/commands/init) command to initialize your project:

```
$ terraform init
```

Here is the output:

```
Initializing the backend...

Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 2.13.0"...
- Installing kreuzwerker/docker v2.13.0...
- Installed kreuzwerker/docker v2.13.0 (self-signed, key ID 24E54F214569A8A5)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## Apply Your Configuration

In this section, you will apply the configuration you created. This will provision the NGINX server you defined.

Use the [`apply`](https://developer.hashicorp.com/terraform/cli/commands/apply) command to provision your infrastructure:

```
$ terraform apply
```

Here is the output:

```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = "json-file"
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 8000
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id           = (known after apply)
      + keep_locally = false
      + latest       = (known after apply)
      + name         = "nginx:latest"
      + output       = (known after apply)
      + repo_digest  = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```

To confirm, type `yes` and press `Enter`.

Here is the output:

```
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 6s [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 3s [id=f12ed94154c1101c0f3e4c6b8da0482c50303d1028ed320052327393dd0a6d77]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Check if your Docker container is running by listing the processes that are running:

```
$ docker ps
```

Here is the output:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
ee93e78ab85c        1403e55ab369        "/docker-entrypoint.…"   30 seconds ago      Up 27 seconds       0.0.0.0:8000->80/tcp   training
```

## Tear Down Your Infrastructure

In this section, you will tear down the infrastructure that you provisioned. This will destroy the NGINX server.

Use the [`destroy`](https://developer.hashicorp.com/terraform/cli/commands/destroy) command to stop your server:

```
$ terraform destroy
```

Here is the output:

```
docker_image.nginx: Refreshing state... [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_container.nginx: Refreshing state... [id=ee93e78ab85cad8a7aa70ed06f894f8ab63a4ff41f778b5aff999df2fad23493]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # docker_container.nginx has been changed
  ~ resource "docker_container" "nginx" {
      + dns               = []
      + dns_opts          = []
      + dns_search        = []
      + group_add         = []
        id                = "ee93e78ab85cad8a7aa70ed06f894f8ab63a4ff41f778b5aff999df2fad23493"
      + links             = []
      + log_opts          = {}
        name              = "training"
      + sysctls           = {}
      + tmpfs             = {}
        # (31 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }

Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes,
the following plan may include actions to undo or respond to these changes.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "887bcd218107" -> null
      - id                = "887bcd2181073b27bea8ef5a755effed2f2e19548d9e550eaa85c552f38e23f4" -> null
      - image             = "sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 8000 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id           = "sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest" -> null
      - keep_locally = false -> null
      - latest       = "sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52" -> null
      - name         = "nginx:latest" -> null
      - repo_digest  = "nginx@sha256:0047b729188a15da49380d9506d65959cce6d40291ccfb4e039f5dc7efd33286" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:  
```

To confirm, type `yes` and press `Enter`.

Here is the output:

```
docker_container.nginx: Destroying... [id=ee93e78ab85cad8a7aa70ed06f894f8ab63a4ff41f778b5aff999df2fad23493]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:1403e55ab369cd1c8039c34e6b4d47ca40bbde39c371254c7cba14756f472f52nginx:latest]
docker_image.nginx: Destruction complete after 0s
```

## Next steps

You now know how to use Terraform to create a configuration, use it to provision resources, and tear them down. You can manage your infrastructure as code to save time and reduce human error.

Next, you can learn about:

- [Uses cases for infrastructure as code](https://developer.hashicorp.com/terraform/intro/use-cases)
- [The Terraform configuration language](https://developer.hashicorp.com/terraform/language)
- [The providers and tools you can use with Terraform](https://registry.terraform.io/browse/providers)
