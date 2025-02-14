# lab6

Starter files for the week 6 lab, intro to Packer.

See lab instructions on D2L for details.


Web Front Packer Template
This repository contains a Packer template (web-front.pkr.hcl) that builds an Amazon Machine Image (AMI) for an Nginx-based web server.

The built image meets the following requirements:  
 Runs Ubuntu 24.04
 Installs and configures Nginx
 Serves the included HTML document
 Uses the provided Nginx configuration

Once the AMI is created, you can launch an EC2 instance and access the hosted web page in a browser.

 Cloning the Repository

git clone https://github.com/Sabbasnia/lab6.git
cd lab6
  Changes & Implementations
  Updated web-front.pkr.hcl
We made the following modifications to the Packer template:

Selected Ubuntu 24.04 as the base image:
name = "ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-20250115"
Defined a source block for Amazon EC2 (amazon-ebs).
Created necessary directories and assigned correct permissions:

provisioner "shell" {
  inline = [
    "sudo mkdir -p /web/html",
    "sudo mkdir -p /tmp/web",
    "sudo mkdir -p /etc/nginx/sites-available",
    "sudo mkdir -p /etc/nginx/sites-enabled",
    "sudo chown -R ubuntu:ubuntu /tmp/web",
    "sudo chown -R www-data:www-data /web/html",
    "sudo chmod -R 755 /web/html"
  ]
}
Uploaded the HTML file to a temporary location:

provisioner "file" {
  source      = "files/index.html"
  destination = "/tmp/web/index.html"
}
Uploaded the Nginx configuration file:
provisioner "file" {
  source      = "files/nginx.conf"
  destination = "/tmp/web/nginx.conf"
}
Installed Nginx and configured it using shell scripts:
provisioner "shell" {
  script = "scripts/install-nginx"
}

provisioner "shell" {
  script = "scripts/setup-nginx"
}
Moved index.html to the correct web root (/web/html):
provisioner "shell" {
  inline = [
    "sudo mv /tmp/web/index.html /web/html/index.html"
  ]
}
Running the Packer Build
To generate the AMI, follow these steps:

1️Install Packer
If you haven't installed Packer, install it using:

For Linux/macOS we have already repository from the Terraform install just now sudo apt install Packer will work:

sudo apt update && sudo apt install -y packer

I also installed aws cli too and adding the access keys and ids to connect to my own aws account 
Configure AWS Credentials
Ensure AWS credentials are set up:

aws configure
Enter:
AWS Access Key ID
AWS Secret Access Key
Default Region (e.g., us-west-2)
Default Output Format (press Enter for json)
Validate the Packer Template
Before running, check if the HCL file is correctly formatted:

packer validate web-front.pkr.hcl
4️Initialize Packer
Initialize Packer and install dependencies:

packer init web-front.pkr.hcl
Build the AMI
Run the Packer build process:

packer build web-front.pkr.hcl
This will: 
  Launch a temporary EC2 instance
  Install Nginx and apply configurations
  Create an AMI and store it in AWS EC2
  Terminate the temporary instance

Verifying the AMI
Go to AWS Console → EC2 Dashboard.
Click AMIs in the left menu.
Find your AMI named "web-nginx-aws".
Launch an EC2 instance using this AMI.
Get Public IP and Test
Once the instance is running, open a web browser and visit:


![image](https://github.com/user-attachments/assets/1a4d480c-63a6-47b6-94e3-1d25992068d1)


to verify please check this 
http://54.68.40.52/ or this <a href="ec2-54-68-40-52.us-west-2.compute.amazonaws.com">ec2-54-68-40-52.us-west-2.compute.amazonaws.com</a>
![image](https://github.com/user-attachments/assets/4aad4c2d-e333-4328-aed1-855a2814fc8b)



now you can see my html page.
