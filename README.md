# Terraform GCP Static Website

Cloud Infrastructure as Code: Provisioning a Static Website from a Storage Bucket.
 
<p align="center"><img src="images/browser.png" width="500"/></p>

## Features

<p align="center"><a href="https://cloud.google.com/"><img src="images/gcp-logo.jpeg" width="130"/></a><a href="https://www.terraform.io/"><img src="images/terraform-logo.svg" width="80"/></a></p>

 - Provisions a simple static website in Google Cloud Platform.
 - A domain name registrar A record points at an external IP entry point, allowing access from a domain name.
 - A forwarding rule then directs requests to an http proxy where urls are mapped to a backend bucket.
 - Using a load balancer provides many features but does incur costs - a cheaper alternative can be found in [infrastructure-terraform-gcp-static-website-low-cost](https://github.com/inventive-technology/infrastructure-terraform-gcp-static-website-low-cost).
 - The backend bucket serves up publicly readable files as web resources.
 - A small collection of files are provisioned as storage bucket objects:
    - index.html - a very basic landing page
    - 404.html - presented when web resources are not found
    - Background images, logo and favicon files.
 - This project builds upon other projects:
    - See [infrastructure-terraform-gcp-intro](https://github.com/inventive-technology/infrastructure-terraform-gcp-intro) for an overview of Terraform usage.
    - See [infrastructure-terraform-gcp-remote-backend](https://github.com/inventive-technology/infrastructure-terraform-gcp-remote-backend) which persists Terraform state data to a remote backend.

<p align="center"><img src="images/provisioning.png" width="800"/></p>

## Prerequisites

 - An installation of [Terraform CLI](https://learn.hashicorp.com/tutorials/terraform/install-cli)
 - A Google Cloud Platform account and installation of [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)
 - A deployment of [infrastructure-terraform-gcp-remote-backend](https://github.com/inventive-technology/infrastructure-terraform-gcp-remote-backend), which provides the following required by this project:
    - A GCP project ID for the remote backend.
    - A storage bucket name for the remote backend.
    - A json key for a service account with Editor priviledge on the remote backend project.

## Getting Started

<details>
  <summary>Set up GCP project - click to expand.</summary>

 - Log into Google Cloud Platform create a new project and **copy** the project ID. This project will be separate from the project that provides the remote backend.
 
 - Edit the [main.tf](main.tf) and **paste** your project ID into the *project* line:

```
  project     = "place-project-id-here"
```

 - Within your GCP account for the selected project, under *IAM & Admin* find the *Service Accounts* section and create a GCP service account with the "Editor" priviledge. Export a json key for the service account and save it in the 'keys' sub-directory. See the [infrastructure-terraform-gcp-intro](https://github.com/inventive-technology/infrastructure-terraform-gcp-intro) project for detailed instructions.
 
 - Edit the [main.tf](main.tf) file and insert the file name of the exported json file into the *credentials* line, for example:

```
  credentials = file("my-project-key.json")
```

 - Edit the [main.tf](main.tf) file and configure the remote backend:
     - The *bucket* must match the name of the remote backend bucket created in the [infrastructure-terraform-gcp-remote-backend](https://github.com/inventive-technology/infrastructure-terraform-gcp-remote-backend) project.
     - The *credentials* must reference the service account json key for the terraform-state-storage project. Save this in the 'keys' sub-directory.
     - Setting *prefix* to the name of *this* project will provide a distinct sub-directory within the Terraform remote backend in which Terraform can store all state information.
 
```
terraform {
  backend "gcs" {
    bucket      = "terraform-remote-backend-xxxxxxxxxxxxxxxx"
    prefix      = "storage"
    credentials = "keys/terraform-state-storage-xxxxxx-xxxxxxxxxxxx.json"
  }
}
```

</details>

<details>
  <summary>Set up Terraform - click to expand.</summary>

 - At the command line, in the root of this project you will need to initialise Terraform:

```
terraform init
```

 - You can then validate that the syntax of the Terraform configuration is correct by running:

```
terraform validate
```

 - To see what changes Terraform will need to apply, run:

```
terraform plan
```

 - Now we are ready to deploy the infrastructure, you will need to confirm with 'yes' when prompted:

```
terraform apply
```

</details>

 - The storage bucket and file resources should now exist within your GCP project. Additionally, the Terraform state will have been persisted to the remote backend in the terraform-state-storage GCP project.

 - You will need to point your domain name registrar's DNS to the external IP address. The 'show' command will list the IP resource's details. Take note of the IP address.

```
terraform show
```

 - For the domain name registrar *123-reg.co.uk* for example, the DNS settings are under *manage DNS*:

<p align="center"><img src="images/manage-dns.png" width="300"/></p>

 - The GCP external *ip_address* should be entered under *destination/target* for A records:

<p align="center"><img src="images/dns-settings.png" width="500"/></p>

 - DNS may take 24hrs to propagate. It should then be possible to access the static site from a browser using the domain name:
 
<p align="center"><img src="images/browser.png" width="500"/></p>

## Usage

```bash
terraform init
terraform validate
terraform plan
terraform apply
terraform show
terraform destroy
```

## Contributing
If you have any feedback/ideas open an issue and tell me what you think.

## References/Related Projects
 - [https://terraform.io](https://terraform.io)
 - [https://cloud.google.com](https://cloud.google.com)
 - [https://github.com/inventive-technology/infrastructure-terraform-gcp-intro](https://github.com/inventive-technology/infrastructure-terraform-gcp-intro) - a basic introduction to Terraform/GCP
 - [https://github.com/inventive-technology/infrastructure-terraform-gcp-remote-backend](https://github.com/inventive-technology/infrastructure-terraform-gcp-remote-backend) - a prerequisite of *this* project