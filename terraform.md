Verify that Terraform is available 
`terraform` 

Inititialization run a new configuration 
`terraform init`

Create an execution plan
`terraform plan` 

Apply changes in the same directory as .tf file
`terraform apply`

Inspect current state
`terraform show`

Import module 
`terraform import module.instances-PROJECT-ID ./modules/instances`

Recreate the instance
`terraform taint NAME_INSTANCE`

Destroy infrastructure 
`terraform destroy`

Refresh the state
`terraform refresh`

Modules registry, provision an example environment
https://registry.terraform.io/modules/terraform-google-modules/network/google/3.3.0 

Create resource in file with extention .tf example instance.tf 
`resource "google_compute_instance" "terraform" {
  project      = "<PROJECT_ID>"
  name         = "terraform"
  machine_type = "n1-standard-1"
  zone         = "us-central1-a"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }
  network_interface {
    network = "default"
    access_config {
    }
  }
}

#EXAMPLE BUCKET CONFIG -> Storage module
resource "google_storage_bucket" "static-site" {
  name          = "IMAGE_NAME"
  location      = "US"
  force_destroy = true
  uniform_bucket_level_access = true
}


Example main.tf file 
#TERRAFORM BLOCK
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
    }
  }
}
#PROVIDER BLOCK
provider "google" {
  version = "3.5.0"
}

terraform {
  backend "gcs" {
    bucket  = "BUCKET_NAME"
    prefix  = "terraform/state"
  }
}


#MODULES
module "instances" {
  source = "./modules/instances"
  project_id = var.project_id
  lifecycle_rules = [{
    action = {
      type = "Delete"
    }
    condition = {
      age        = 365
      with_state = "ANY"
    }
  }]
}

module "storage" {
  source = "./modules/storage"
  project_id = var.project_id
  lifecycle_rules = [{
    action = {
      type = "Delete"
    }
    condition = {
      age        = 365
      with_state = "ANY"
    }
  }]
}

#RESOURCE INSTANCE EXAMPLE
resource "google_compute_instance" "default" {
  name         = "tf-instance-1"
  machine_type = "e2-medium"
  network_interface {
    network = "default"

    access_config {
      // Ephemeral public IP
    }
  metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT
  allow_stopping_for_update = true
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }
  
  resource "google_compute_instance" "default2" {
  name         = "tf-instance-2"
  machine_type = "e2-medium"
  network_interface {
    network = "default"

    access_config {
      // Ephemeral public IP
    }
  metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT
  allow_stopping_for_update = true
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }

#Variables 
variable "region" {
  description = "The region of instances"
  default     = "us-east1"
}

variable "zone" {
  description = "The zone of instances"
  default     = "us-east1-c"

variable "project_id" {
  description = "The project ID to host the network in"
  default     = "FILL IN YOUR PROJECT ID HERE"
}`
