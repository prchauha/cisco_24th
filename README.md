# cisco_24th

```

Welcome to OpenDev Etherpad!

This pad text is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents!

OpenDev: https://opendev.org
Etherpad on Github: https://github.com/ether/etherpad-lite

my name is raman khanna ...


terraform diff types of blocks :
    provider block ... :  will give info on which platform and region (authentication)
    resource block ..... which resoucr it tf needs to create 
    variable block : which centralizes a value from  all the places i ur code 
    dynamic block
    data block .....
    output 
    local block
    backend block
    provisoner block : imp part as per exam...
    module block 
    import block
    
    

terraform  open source 

teraform (creating the resources ) /ansible ( configuring this resources) 


purpose : ?

deploying objects  / provisioning resources :: creation of resources ....

automation / template (iac file)   ,,  destroy all resources wetver 

scalibility ...  time saving

managemnt of state of ur infrastrcutuire 

[ sg , rds , vm , two or three resources ]  >>>  iac file  >>> create all those at once ..
maintain state of of all resources 


=======================================


aws cloud : 2006  ?

application ? small business 
code (software ) >>>>  compilation /build  >>> artefact (.exe)  >>>>>  qa ( deploy on servers ) >>>>> prod( deploy on serversw ) server 



buy : for my datacentere : hardware ( servers , switch , routers , firewalls , disks , ups , etc ) datacenter : a lot of money



https://registry.terraform.io/

https://registry.terraform.io/browse/providers


============================================


any platform : 
    
    gui
    cli
    iac script , terraform script , template , hcl script ...........................
    


=========================

aws credentials :
    
    
                 cisco-user  
                 
                 Ct!S2{)B
                 
                 https://685421549691.signin.aws.amazon.com/console   
                 
                 ========================================
                 
                 cisco user 
                 acces key : AKIAZ7FSO3B5YEMSBYYG
                 secret access key : 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
                 
                 
                 
                 =========================================
                 
                 --region of aws : us-east-1 (north virginia )
                 
                 
                 
-- installation terraform : ( u dont have to do it )
    
    https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli
    
    -- go to north virgina ..
    
                 instance details >>>> connect >> ec2 instance connect ....
                 
                 -- sudo -i
                 
                 -- mkdir raman
                 
                 
                 ===========================================
                 
             ---     auth aws account :  left it for now ...
                     
                 username/password 
                 iam role 
                 aws cli based authentication : aws configure 
                 directly mention the creds in the provider block itself
                 
                 
                 
                 =========
                 
                 -- using terraform , we are tryiong to create a machine 
                 
                 --- first iac script .....
                 
                 --- cd raman
                 
                 -- vi res.tf
                 

provider "aws" {
  region     = "us-east-1"
}

resource "aws_instance" "ec2" {
  ami                     = "ami-04aa00acb1165b32a"
  instance_type           = "t2.micro"
  availability_zone = "us-east-1a"
}
                 
                 
                 
                 
                 terraform init : provide plugin installation ( aws )
                 terraform validate : to validate hcl code 
                 terrafom plan : before creating it gives a plan of what will be created 
                 terraform apply : it will send an api call to my aws account to create that re source ....
                 terraform destroy : destroy all the resources 
                 
                 
                 
                 
--- authentication :
    
    iam role access :
        directly provding creds of account in provider block




root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

resource "aws_instance" "ec2" {
  ami                     = "ami-04aa00acb1165b32a"
  instance_type           = "t2.micro"
  availability_zone = "us-east-1a"
  tags={ 
Name= "raman-first-server"
project="Devops"
purpose="training"
}
}


terraform validate
  217  vi res.tf 
  218  ls
  219  vi res.tf 
  220  alias t=terraform
  221  t validate
  222  t plan
  223  t apply
  t destroy
  
  
  ==============================
  
  vpc ~ network implementation :
  
  #######################################
  
  
  root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

resource "aws_vpc" "vpcrk" {
  cidr_block       = "10.0.0.0/16"
  enable_dns_support= true
  enable_dns_hostnames=true

  tags = {
    Name = "manual-vpc-raman"
  }
}


resource "aws_subnet" "rksub" {
  vpc_id     = aws_vpc.vpcrk.id
  cidr_block = "10.0.0.0/24"
  map_public_ip_on_launch=false  

  tags = {
    Name = "private-raman-subnet"
  }
}




resource "aws_instance" "ec2" {
  ami                     = "ami-04aa00acb1165b32a"
  instance_type           = "t2.micro"
  subnet_id= aws_subnet.rksub.id
  tags={ 
Name= "raman-first-server"
project="Devops"
purpose="training"
network="manual"
}
}
  
  ###############################################
  
  vi res.tf 
  268  t plan
  269  t validate
  270  t apply
  271  t state list
  272  t show
  273  ls
  274  clear
  275  cat res.tf 
  
  
  
########################################################
  
  provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "vpcrk" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "manual-vpc-raman"
  }
}

resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.vpcrk.id

  tags = {
    Name = "manual-vpc-raman-gw"
  }
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.vpcrk.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }

  tags = {
    Name = "public-route-table"
  }
}

resource "aws_subnet" "public_rksub" {
  vpc_id                  = aws_vpc.vpcrk.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true  

  tags = {
    Name = "public-raman-subnet"
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_rksub.id
  route_table_id = aws_route_table.public_rt.id
}

resource "aws_instance" "public_ec2" {
  ami           = "ami-04aa00acb1165b32a"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_rksub.id

  tags = { 
    Name    = "raman-public-server"
    project = "Devops"
    purpose = "training"
    network = "manual"
  }
}

  ##########################################################################
  
  ===================================================================
  
  
  versioning : constraints !!
  
  https://github.com/hashicorp/terraform-provider-aws/releases
  
  
  
  root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  version    = "5.92.0"
  region     = "us-east-1"
  access_key = "A
  ...
  
  
  5.92 ~~~~
  
  version     = “~> 5.x”
  
  5.93.0 >>>>> some additional features , on some partucular services ...
  
  terraform init >>> 5.93
  
  
  5.99 >>>
  terra form init >>> 5.99
  
  
  6.0.1 
  terraform init >>>
  
  
  
  ========================
  
  5.91.0 : current version constraint .....
  
  5.92.0 
  
  
  
  
  LAB : PROVIDER VERSIONING        :

--- check the current version first by

cat .terraform.lock.hcl
 

provider "aws" {
  region = "us-east-2"
  version= ">=2.10,<=2.30"
}

resource "aws_instance" "myawsserver" {
  ami = "ami-064ff912f78e3e561"
  instance_type = "t2.micro"

  tags = {
    Name = "Techlanders-aws-ec2-instance"
  }
}

--- try to initialise new plugin

--delete the .terraform.lock.hcl file or try

terraform init -upgrade 

-- terrafform init again


  
  ================================================================
  
 LAB : Variables :

--- take the ec2 instance from the prev iac code and add a security group to it :

root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

variable "rk-cidr" {
    type=string
  default = "116.50.30.70/32"
#   default="0.0.0.0/0"
}


resource "aws_security_group" "rksg" {
  name        = "raman-variables"
  vpc_id      = "vpc-01948378f1e13345b"

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 53
    to_port     = 53
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 3389
    to_port     = 3389
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }
}


---- try changing the value in variables.tf file to see the changes .

  ===================================================
  
  ui : when u dont mention the defaukt value
  default value  : 
  
  string , number , boolean , list , map type variables ...
  
  
  how we can implement variable assignmnts :
      
  default 
  dont mention mention a degfault value : ui 
  custom.tfvars file .... : when we r working on multiole env
  
  
  ========================================================
  
  
  ---LAB :Different Approaches for Variable Assignment :

--- Default File used in Demo

root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

resource "aws_instance" "myawsserver" {
  ami = "ami-04aa00acb1165b32a"
  instance_type = var.typ
  vpc_security_group_ids=[aws_security_group.rksg.id]
  tags = {
    Name = "raman-aws-instance"
  }
}



resource "aws_security_group" "rksg" {
  name        = "raman-variables"
  vpc_id      = "vpc-01948378f1e13345b"

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 53
    to_port     = 53
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 3389
    to_port     = 3389
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }
}



root@ip-172-31-30-85:~/raman# ls
ramanprod.tfvars  res.tf  terraform.tfstate  terraform.tfstate.backup  variable.tf
root@ip-172-31-30-85:~/raman# cat variable.tf 
variable "typ" {
default = "t2.micro"
}

variable "rk-cidr" {
    type=string
  default = "116.50.30.70/32"
#   default="0.0.0.0/0"
}


-- custom.tfvars file :

root@ip-172-31-30-85:~/raman# cat ramanprod.tfvars 
typ="t2.large"
rk-cidr="0.0.0.0/0"


-- terraform.tfvars file: takes the value from this file if default value is nt specified.

type="t2.nano"

--CLI Commands

terraform plan -var="type=t2.small"
terraform plan -var-file="ramanprod.tfvars"

***************************************************************************
================================================================




LAB : count parameter and indexes :

root@ip-172-31-30-85:~/raman# cat res.tf 
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

resource "aws_instance" "myawsserver" {
  ami = "ami-04aa00acb1165b32a"
  instance_type = var.typ[count.index]
  count=3
  vpc_security_group_ids=[aws_security_group.rksg.id]
  tags = {
#    Name = var.serv-names[count.index]
     Name= "raman-${var.serv-names[count.index]}-server"
  }
}



resource "aws_security_group" "rksg" {
  name        = "raman-variables"
  vpc_id      = "vpc-01948378f1e13345b"

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 53
    to_port     = 53
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }

  ingress {
    from_port   = 3389
    to_port     = 3389
    protocol    = "tcp"
    cidr_blocks = [var.rk-cidr]
  }
}



root@ip-172-31-30-85:~/raman# cat variable.tf 
variable "typ" {
type=list
default = ["t2.micro","t2.medium","t2.large"]
}

variable "rk-cidr" {
    type=string
  default = "116.50.30.70/32"
#   default="0.0.0.0/0"
}

variable "serv-names" {
  type = list
  default = ["dev","stage","prod"]
}


================================================


root@ip-172-31-30-85:~/raman# cat res2.tf 
provider "aws" {
  region = "us-east-1"
  access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

variable "env" {
  description = "Deployment environment"
  type        = string
  default     = "dev"
}

variable "instances" {
  type = map(string)
  default = {
    "web"    = "t2.micro"
    "db"     = "t3.medium"
    "worker" = "t2.small"
  }
}

variable "cidr_blocks" {
  type    = list(string)
  default = ["0.0.0.0/0"]
}

variable "sg_ports" {
  type        = list(number)
  description = "list of ingress ports"
  default     = [8200, 8201,8300, 9200, 9500]
}


variable "use_premium_storage" {
  description = "Enable premium storage for prod instances"
  type        = bool
  default     = false
}

resource "aws_security_group" "rksg" {
  name   = "raman-secgroup"
  vpc_id = "vpc-01948378f1e13345b"

  dynamic "ingress" {
    for_each = var.sg_ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = var.cidr_blocks
    }
  }
}

resource "aws_instance" "myawsserver" {
  for_each      = var.instances
  ami           = "ami-04aa00acb1165b32a"
  instance_type = var.env == "prod" ? "t3.large" : each.value

  root_block_device {
    volume_type = var.use_premium_storage ? "io1" : "gp2"
  }

  vpc_security_group_ids = [aws_security_group.rksg.id]

  tags = {
    Name = "raman-${each.key}-server"
    Env  = var.env
  }
}


=========================================================

ami-04aa00acb1165b32a : us-east-1 : ami : amazon linux2 : amzn2-ami-kernel-5.10-hvm-2.0.20250305.0-x86_64-gp2

ami-05716d7e60b53d380 : us-east-2 : ami: amazon linux2 :  amzn2-ami-kernel-5.10-hvm-2.0.20250305.0-x86_64-gp2



---   Datsources :
    
     
    
    root@ip-172-31-30-85:~/raman/datasources# cat ec2.tf 
provider "aws" {
region="us-east-1"
 access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

data "aws_instance" "foo" {
  instance_id = "i-050a18394790a1cc8"
}

data "aws_ami" "app_ami" {
  most_recent = true
  owners = ["amazon"]


  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}


resource "aws_instance" "ec2" {
  ami=data.aws_ami.app_ami.id 
#  ami                     = data.aws_instance.foo.id
 instance_type=data.aws_instance.foo.instance_type
 availability_zone=data.aws_instance.foo.vailability_zone
  tags = {
    Name = "raman-ec2"
  }
}

#output "values-inst" {
#value=data.aws_instance.foo
#}

================================================


root@ip-172-31-30-85:~/raman/datasources# cat ec2.tf 
provider "aws" {
region="ap-south-1"
 access_key = "AKIAZ7FSO3B5YEMSBYYG"
  secret_key = "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"
}

data "aws_ami" "app_ami" {
  most_recent = true
  owners = ["amazon"]


  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}


resource "aws_instance" "ec2" {
  ami=data.aws_ami.app_ami.id 
 instance_type="t2.micro"
  tags = {
    Name = "raman-ec2"
  }
}


===========================================================



Resource Meta-Argument lifecycle :
The lifecycle meta-argument allows you to define behaviors that control the creation, updating, and deletion of resources.

Example 3.1: create_before_destroy - Default Behavior
Use Case: Ensuring zero downtime while replacing an EC2 instance.

resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
  }

  tags = {
    Name = "example-instance"
  }
}




Example 3.2: prevent_destroy - Implementation and Testing
Use Case: Preventing accidental deletion of an S3 bucket.

resource "aws_s3_bucket" "critical" {
  bucket = "critical-bucket"

  lifecycle {
    prevent_destroy = true
  }

  tags = {
    Purpose = "Critical data storage"
  }
}



Example 3.3: ignore_changes - Implementation and Testing
Use Case: Ignore changes to specific tags during future updates.

resource "aws_s3_bucket" "example" {
  bucket = "mutable-bucket"

  tags = {
    Environment = "Dev"
    Owner       = "DevOps"
  }

  lifecycle {
    ignore_changes = [
      tags["Owner"]
    ]
  }
}


#Helps avoid unintended resource updates.

===================================================

Functions :
    
    
    
    root@ip-172-31-30-85:~/raman/function# cat func.tf 
provider "aws" {
  region     = var.region
}

locals {
  time = formatdate("DD MMM YYYY hh:mm ZZZ", timestamp())
}

variable "region" {
  default = "us-east-1"
}

variable "tags" {
  type = list
  default = ["raman-firstec2","raman-secondec2"]
}

variable "ami" {
  type = map
  default = {
    "us-east-1" = "ami-08b5b3a93ed654d19"
    "us-west-2" = "ami-0b6d6dacf350ebc82"
    "ap-south-1" = "ami-05c179eced2eb9b5b"
  }
}


resource "aws_instance" "app-dev" {
   ami = lookup(var.ami,var.region)  #in terraform console: lookup({us-east-1="ami1",us-east-2="ami2",ap-south-1="ami3"},"ap-south-1","NA") #
   instance_type = "t2.micro"
   count = 2

   tags = {
     Name = element(var.tags,count.index)    # element(["firstec2","secondec2"],1) #
   }
}


output "instance_details" {
  description = "Consolidated details of all instances"
  value = {
    ids       = aws_instance.app-dev[*].id
    publicip         = aws_instance.app-dev[*].public_ip
    privatIps        = aws_instance.app-dev[*].private_ip
    amis           = aws_instance.app-dev[*].ami
    instance_names     = aws_instance.app-dev[*].tags.Name
    timestamp          = local.time
  }
}


history for reference :
    
    
    terraform console
 2011  clear
 2012  ls
 2013  vi func.tf 
 2014  t plan
 2015  alias t=terraform
 2016  t plan
 2017  cat func.tf 
 2018  vi func.tf 
 2019  t plan
 2020  vi func.tf 
 2021  t apply 
 2022  t outputs
 2023  t output
 2024  t output > export.json
 2025  ls
 2026  cat export.json 
 2027  t destroy
 2028  t state list
 
 ====================================================
 
 State lock :
     
     •Terraform acquires a state lock to protect the state from being written by multiple users at the same time.
     
     u can create two sessions and try to do apply on both together and see state lock in action...
     
     ------------------------------
     
     Remote backend :
         
         s3 >>> create a bucket raman-remote-backend-bucket ....
         
         --------------------------------------------------------------
         
         
         -- added the bakcend to my working directory  ......
         
         root@ip-172-31-30-85:~/raman# cat backend.tf 
terraform {
  backend "s3" {
    bucket = "backend-bucket"
    key    = "terraform/state"
    region = "us-east-1"
  }
}
     
     terraform init
     
     t apply ...
     
     ---- to enable versioning of ur remote tf state :
         -- u can enable versioning on ur s3 bucket
         
         ====================================================
         
         
         STATE LOCKING ON REMOTE BACKEND :
             
        -- create a dynamodb table ... raman-tf-table 
        -- set partition key "LockID" with type as string ...
        
        
     root@ip-172-31-30-85:~/raman# cat backend.tf 
terraform {
  backend "s3" {
    bucket = "backend-bucket"
    key    = "terraform/state"
    region = "us-east-1"
    dynamodb_table = "tf-table"
  }
}


-- go ahead and try to test the lock with2 sessions ...

=====================
ind best pratise :
   terraform / configuration of infra handled ansible ... 
    
ansible : configuration management  : manging the state of configurations 
terraform : creation of resources .... manages the state of those resources .., cannot mange the state of the configurations 

Provisioners : remote nd  local exec

creation of resources on any platform that supports
configuration of resources : terraform itself doesnt using provisoners 


RemoteProvisoner :

root@ip-172-31-30-85:~/raman/remote# cat remote.tf 
provider "aws" {
region= "us-east-1"
}

resource "aws_instance" "myec2" {
   ami = "ami-04aa00acb1165b32a"                               #amazon linux2 ami
   instance_type = "t2.micro"
   key_name = "raman-virginia-key"                                 # Create a keypair in the region #
   vpc_security_group_ids  = [aws_security_group.allow_ssh.id]
   tags= {
   Name= "raman-web-server"
   }

   provisioner "remote-exec" {
     inline = [
       "sudo amazon-linux-extras install -y nginx1.12",
       "sudo systemctl start nginx"
     ]

   connection {
     type = "ssh"
     user = "ec2-user"
     private_key = file("./raman.pem")    #copy the content of the private keyfile ; create a file in /root/app1 named nvirginia.pem and then paste the content into this file #
     host = self.public_ip
#    host= aws_instance.myec2.public_ip
   }
   }
}

### NOTE - Adding a new security group resource to allow the terraform provisioner from laptop to connect to EC2 Instance via SSH.

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"
  vpc_id      = "vpc-01948378f1e13345b"

  ingress {
    description = "SSH into VPC"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
 ingress {
    description = "SSH into VPC"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    description = "Outbound Allowed"
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}


==============================================




LAB : LOCAL EXEC PROVISIONER :

provider "aws" {
region= "us-east-1"
}

resource "aws_instance" "myec2" {
   ami = "ami-04aa00acb1165b32a"
   instance_type = "t2.micro"

   provisioner "local-exec" {
    command = "echo ${aws_instance.myec2.private_ip} >> private_ips.txt"
#   command = "echo ${self.private_ip} >> private_ips.txt"
}
}


=============================================

Multiple provider configuration :
    

root@ip-172-31-30-85:~/raman/remote# cat local.tf 
provider "aws" {
region= "us-east-1"
alias= "us1"
}

resource "aws_instance" "myec22" {
   ami = "ami-04aa00acb1165b32a"
   provider = aws.us1
   instance_type = "t2.micro"

   provisioner "local-exec" {
    command = "echo ${aws_instance.myec2.private_ip} >> private_ips.txt"
#   command = "echo ${self.private_ip} >> private_ips.txt"
}
}

====================================================================================================


























===============================================================




1. Introduction to Terraform depends_on (Meta-Argument)What is depends_on in Terraform?
In Terraform, the depends_on meta-argument is used to explicitly specify dependency relationships between resources, data sources, or modules when Terraform cannot automatically infer them.

Normally, Terraform automatically determines the order of resource creation using implicit dependency based on the references used inside the resource block.
However, in some complex cases, you may want to force Terraform to create or destroy resources in a specific order even when there is no direct reference between them — that's where depends_on is helpful.
✅ Syntax
hcl
CopyEdit
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  depends_on = [
    aws_key_pair.terraform_key
  ]
}
🚀 When to Use depends_on:

When a resource doesn't directly use another resource but still requires it to be created first.


When using provisioners (remote-exec, local-exec).


When working with external resources where the dependency chain is broken.


When working with null_resources and triggers.
2. Create Key Pair terraform-key and Review c1-versions.tf
Let me show you the two files you mentioned with explanation:
terraform-key.tf (Create Key Pair)
hcl
CopyEdit
resource "aws_key_pair" "terraform_key" {
  key_name   = "terraform-key"
  public_key = file("~/.ssh/id_rsa.pub")
}
✅ Explanation:

Resource Type: aws_key_pair


key_name: Name of the key pair in AWS.


public_key: Public key content which will be used to create the key pair. Typically, you use your local SSH public key (ensure ~/.ssh/id_rsa.pub exists).

You can then use this key in your EC2 instance:
hcl
CopyEdit
resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.terraform_key.key_name

  depends_on = [
    aws_key_pair.terraform_key
  ]
}
c1-versions.tf (Versioning Terraform Providers & Terraform Core)
A typical c1-versions.tf file contains version constraints to lock the versions of:


Terraform CLI


Terraform Providers

Sample:
hcl
CopyEdit
terraform {
  required_version = ">= 1.5.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
✅ Explanation:

required_version: Ensures compatibility of your Terraform code with specific Terraform CLI versions.


required_providers: Specifies the provider (AWS) and locks the version.


source: Always recommended to use hashicorp/aws to avoid ambiguity.


version: You can use pessimistic constraint ~> to allow patch updates but prevent major upgrades.
⭐️ Complete Working Example (Combined)
hcl
CopyEdit
terraform {
  required_version = ">= 1.5.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_key_pair" "terraform_key" {
  key_name   = "terraform-key"
  public_key = file("~/.ssh/id_rsa.pub")
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.terraform_key.key_name

  depends_on = [
    aws_key_pair.terraform_key
  ]
}
🎯 Important Note
depends_on should be used cautiously. Overusing it leads to unnecessary serialization of resources which can slow down the Terraform plan/apply and reduce parallelism.


==================================================================




✅ Directory Structure
cpp
CopyEdit
terraform-eip/
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars   (optional)
├── cleanup.sh         (optional cleanup script)
└── README.md
1️⃣ Terraform Configuration: Create Elastic IP with depends_on Meta-Argumentmain.tf
hcl
CopyEdit
provider "aws" {
  region = var.aws_region
}

resource "aws_vpc" "c4_vpc" {
  cidr_block = var.vpc_cidr
  tags = {
    Name = "c4-vpc"
  }
}

resource "aws_subnet" "c4_subnet" {
  vpc_id     = aws_vpc.c4_vpc.id
  cidr_block = var.subnet_cidr
  tags = {
    Name = "c4-subnet"
  }
}

resource "aws_instance" "c4_instance" {
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.c4_subnet.id

  tags = {
    Name = "c4-ec2"
  }
}

resource "aws_eip" "c4_elastic_ip" {
  instance = aws_instance.c4_instance.id
  depends_on = [aws_instance.c4_instance]
  tags = {
    Name = "c4-elastic-ip"
  }
}
2️⃣ Input Variables (Advanced Features)variables.tf
hcl
CopyEdit
variable "aws_region" {
  description = "AWS Region"
  type        = string
  default     = "us-east-1"
}

variable "vpc_cidr" {
  description = "VPC CIDR Block"
  type        = string
  default     = "10.0.0.0/16"
}

variable "subnet_cidr" {
  description = "Subnet CIDR Block"
  type        = string
  default     = "10.0.1.0/24"
}

variable "ami_id" {
  description = "AMI ID for EC2"
  type        = string
  default     = "ami-0c55b159cbfafe1f0"

  validation {
    condition     = can(regex("^ami-[a-zA-Z0-9]+$", var.ami_id))
    error_message = "The AMI ID must start with 'ami-' followed by alphanumeric characters."
  }
}

variable "instance_type" {
  description = "EC2 Instance Type"
  type        = string
  default     = "t2.micro"

  validation {
    condition     = contains(["t2.micro", "t2.small", "t3.micro"], var.instance_type)
    error_message = "Allowed values are t2.micro, t2.small, t3.micro"
  }
}

variable "access_key" {
  description = "AWS Access Key"
  type        = string
  sensitive   = true
}

variable "secret_key" {
  description = "AWS Secret Key"
  type        = string
  sensitive   = true
}
3️⃣ Override variables with Environment Variables
Terraform supports TF_VAR_ prefix:
bash
CopyEdit
export TF_VAR_aws_region="us-east-2"export TF_VAR_instance_type="t3.micro"export TF_VAR_access_key="YOUR_ACCESS_KEY"export TF_VAR_secret_key="YOUR_SECRET_KEY"

✅ These will automatically override default values at runtime without terraform.tfvars.
4️⃣ Outputsoutputs.tf
hcl
CopyEdit
output "instance_id" {
  value = aws_instance.c4_instance.id
}

output "elastic_ip" {
  value = aws_eip.c4_elastic_ip.public_ip
}
5️⃣ Initialize, Plan, Apply
bash
CopyEdit
terraform init
terraform plan
terraform apply -auto-approve
6️⃣ Verification
Once applied:
bash
CopyEdit
terraform show
terraform state list
aws ec2 describe-instances --instance-ids $(terraform output -raw instance_id)
aws ec2 describe-addresses
7️⃣ Taint & Untaint Commands🟢 Taint Resource
Manually mark a resource for recreation in next apply:

bash
CopyEdit
terraform taint aws_instance.c4_instance
terraform plan
terraform apply -auto-approve

Why?
Used when a resource is in a bad state (corrupt, needs recreation) without any config change.
🔵 Untaint Resource
Remove taint marking:
bash
CopyEdit
terraform untaint aws_instance.c4_instance
terraform plan
8️⃣ Clean-Up Resources
Option 1:
Using Terraform:
bash
CopyEdit
terraform destroy -auto-approve

Option 2:
Optional cleanup.sh (manual, safer in production)

bash
CopyEdit
#!/bin/bashecho "Releasing Elastic IP, Terminating EC2, Deleting VPC..."



====================================================


Modules :
    
child / root modules ::
    
    
    
    LAB : Modules :

---- create a directory modules and projects .

--- create ec2 directory in modules and project-A under projects directory.

--- under ec2 directory create the source module ec2.tf for global use .

resource "aws_instance" "s1" {
instance_type= "t2.micro"
ami= "ami-064ff912f78e3e561"
}


--- go to projects >> project-A directory and create the child module myec2.tf

-- call the source module from myec2.tf 

module "ec2module" {
source ="../../modules/ec2"
}


--- Being under projects-A ,do terraform plan 

--- terraform init

--- terraform apply

(if its giving an error , provide a provider block in project-A folder as well)

**************************************************************************



**************************************************************************

LAB : Using variables to solve the problem    


---- under /root/modules/ec2  , modify the ec2.tf file (source module) to use variables

resource "aws_instance" "s1" {
instance_type= var.instancetype
ami= "ami-064ff912f78e3e561"
}

----- variables.tf file

variable "instancetype" {
default = "t2.micro"
}


---- under /root/projects/project-A , create two env files which will use differenct instance types :

--- myec2.tf for dev env :

module "ec2module-dev" {
source ="../../modules/ec2"
instancetype="t2.nano"
}


---- myec2.tf-prod for prod env :

module "ec2module-prod" {
source ="../../modules/ec2"
}


---- terraform init and  apply  .


====================================


=======================================================

-- USING OUTPUT BLOCK IN MODULES TO TRANSFER THE VALUES FROM CHILD TO ROOT MODULES :

root@ip-172-31-80-106:~/app/projectB# cat /root/app/modules/compute/ec2.tf 
resource "aws_instance" "myec2" {
   ami = "ami-0e2c8caa4b6378d8c"
   instance_type = var.type
tags = {
    Name = var.nam
  }
}

output "type" {
  value = var.type
  description = "The instance type passed to the module"
}




root@ip-172-31-80-106:~/app/projectB# cat /root/app/modules/compute/variable.tf 
variable "type" {
}

variable "nam" {
}



root@ip-172-31-80-106:~/app/projectB# cat myec2.tf 
provider "aws" {
region="us-east-1"
}

module "ec2module" {
source ="/root/app/modules/compute"
type= var.type
nam="projectB-server"
}

variable "type" {
  description = "The instance type for the EC2 instance"
  type        = string
  default="t2.micro"
}


# Accessing the child's type variable output
output "ec2module_type" {
  value = module.ec2module.type
}

=====================================




============================================================

--- Terraform manifests to build a static website :


root@ip-172-31-91-158:~/test# ls
error.html  index.html  modules  static.tf  terraform.tfstate  terraform.tfstate.backup

-------------------

1. index.html (Index Document)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to My Static Website</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        h1 {
            color: #333;
        }
    </style>
</head>
<body>
    <h1>Welcome to My Static Website!</h1>
    <p>This is a simple static website hosted on Amazon S3. Enjoy exploring!</p>
</body>
</html>



2. error.html (Error Document)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>404 - Page Not Found</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        h1 {
            color: #f44336;
        }
        p {
            color: #555;
        }
    </style>
</head>
<body>
    <h1>404 - Page Not Found</h1>
    <p>Sorry, the page you're looking for doesn't exist.</p>
    <p>Please check the URL or <a href="/">go back to the homepage</a>.</p>
</body>
</html>


---------------------------------------


1. Directory Structure
Let's begin by structuring the Terraform files into a module-based format.


.
├── main.tf
├── modules
│   └── s3_website
│       ├── main.tf
│       └── outputs.tf
│       └── variables.tf
├── terraform.tfstate
-- index.tml
-- error.html
└── terraform.tfstate.backup


modules/s3_website: This will contain the code for creating the S3 website and associated configurations.

main.tf: This is your root Terraform configuration where you'll call the module.

variables.tf: Defines the input variables for the root module.

outputs.tf: Defines outputs that are aggregated from the module and root module.



2. Module (modules/s3_website)
a. modules/s3_website/main.tf
This is where we place the exact configuration from the existing code, but refactor it into a module that will receive variables.


# AWS Provider setup (this should be part of the root)
resource "aws_s3_bucket" "terraformbucket" {
  bucket = var.bucket_name
}

resource "aws_s3_bucket_ownership_controls" "ownership" {
  bucket = aws_s3_bucket.terraformbucket.id
  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}

resource "aws_s3_bucket_public_access_block" "publiceaccess" {
  bucket = aws_s3_bucket.terraformbucket.id
  block_public_acls       = var.block_public_acls
  block_public_policy     = var.block_public_policy
  ignore_public_acls      = var.ignore_public_acls
  restrict_public_buckets = var.restrict_public_buckets
}

resource "aws_s3_bucket_acl" "bucket_acl" {
  depends_on = [
    aws_s3_bucket_ownership_controls.ownership,
    aws_s3_bucket_public_access_block.publiceaccess,
  ]

  bucket = aws_s3_bucket.terraformbucket.id
  acl    = "public-read"
}

resource "aws_s3_object" "index" {
  bucket       = aws_s3_bucket.terraformbucket.id
  key          = "index.html"
  source       = var.index_html
  content_type = "text/html"
}

resource "aws_s3_object" "error" {
  bucket       = aws_s3_bucket.terraformbucket.id
  key          = "error.html"
  source       = var.error_html
  content_type = "text/html"
}

resource "aws_s3_bucket_website_configuration" "example" {
  bucket = aws_s3_bucket.terraformbucket.id

  index_document {
    suffix = "index.html"
  }

  error_document {
    key = "error.html"
  }
}

resource "aws_s3_bucket_policy" "public_read_access" {
  depends_on = [
    aws_s3_bucket_ownership_controls.ownership,
    aws_s3_bucket_public_access_block.publiceaccess,
  ]
  bucket = aws_s3_bucket.terraformbucket.id
  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [ "s3:GetObject" ],
      "Resource": [
        "${aws_s3_bucket.terraformbucket.arn}",
        "${aws_s3_bucket.terraformbucket.arn}/*"
      ]
    }
  ]
}
EOF
}





b. modules/s3_website/variables.tf
We define all variables used in the module here so the module can accept inputs.


variable "bucket_name" {
  description = "The name of the S3 bucket"
  type        = string
}

variable "index_html" {
  description = "Path to the index.html file to upload"
  type        = string
  default     = "index.html"
}

variable "error_html" {
  description = "Path to the error.html file to upload"
  type        = string
  default     = "error.html"
}

variable "block_public_acls" {
  description = "Whether to block public ACLs on the bucket"
  type        = bool
  default     = false
}

variable "block_public_policy" {
  description = "Whether to block public policy on the bucket"
  type        = bool
  default     = false
}

variable "ignore_public_acls" {
  description = "Whether to ignore public ACLs on the bucket"
  type        = bool
  default     = false
}

variable "restrict_public_buckets" {
  description = "Whether to restrict public buckets"
  type        = bool
  default     = false
}



c. modules/s3_website/outputs.tf
This is where we define the output values for the module.


output "website_endpoint" {
  description = "The endpoint URL for the static website"
  value       = aws_s3_bucket.terraformbucket.website_endpoint
}




3. Root (main.tf)
In the root directory, we will call the s3_website module. This is where you configure the input variables for the module and also use the module output.


module "s3_website" {
  source        = "./modules/s3_website"
  bucket_name   = "myteststaticcccccccccccccccc"
  index_html    = "index.html"
  error_html    = "error.html"
  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

output "website_endpoint" {
  value = module.s3_website.website_endpoint
}




4. Initialize and Apply Terraform Configuration
Now you can initialize Terraform in your working directory and apply the configuration.

Initialize Terraform:
Run the terraform init command to initialize the project.


terraform init


Plan:
Run terraform plan to review the changes that will be made.


terraform plan

Apply:
Run terraform apply to apply the configuration and create the resources.


terraform apply


6. Outputs
Once the configuration is applied, you can view the S3 static website endpoint with:


terraform output website_endpoint


------------


Repeat the initialization, planning, and application steps to verify the behavior.


===================================================================================


    LAB: WORKSPACE :

---Terraform Based Configuration File :

provider "aws" {
  region     = "us-east-2"

}

resource "aws_instance" "myec2" {
   ami = "ami-064ff912f78e3e561"
   instance_type = lookup(var.instance_type,terraform.workspace)
tags = {
    Name = lookup(var.name,terraform.workspace)
  }
}

variable "instance_type" {
  type = map

  default = {
    default = "t2.nano"
    dev     = "t2.micro"
    prod     = "t2.large"
  }
}

variable "name"{
type=map
default= {
default="IT server"
dev="dev-server"
prod="prod-server"
}
}



--- after creating the code , do terraform workspace show

--- in default wrkspace , do terraform plan 

-- create a workspace prod and dev as well

terraform workspace new dev

terraform workspace new prod

----  switch to dev

terraform workspace select dev

--- terraform plan

-- and same for prod and see the diff in instance types..
 
************************************

========================================================


********************************************************************************

LAB : DEBUGGING :

---- to start seeing terraform logs use

export TF_LOG=TRACE

--- to export it to a file

export TF_LOG_PATH=/tmp/crash.log

--- to remove logging 

unset TF_LOG=TRACE   / unset TF_LOG

unset TF_LOG_PATH

**************************************************************************************


Terraform import :

create a resource  first...

create import.tf file :

[root@tf-main terraform]# cat import.tf
import {
id="i-0430f7e44b388e61a"
to=aws_instance.ec23

}

  637  terraform plan -generate-config-out=server.tf
 # 638  t import aws_instance.web i-0c5b66a8f9d3b0053
---- t plan
  658  terraform apply
  659  terraform state list





-------------------


root@ip-172-31-91-158:~/test# cat import.tf 
import {
  to = aws_instance.web
  id = "i-0c5b66a8f9d3b0053"
}

import {
  to = aws_s3_bucket.bucket
  id = "pradeep-tf-test-bucket"

}



  641  terraform plan -target=aws_s3_bucket.bucket -generate-config-out=s3.tf
##  terraform import aws_s3_bucket.bucket pradeep-tf-test-bucket
t plan 
t apply
  644  t state list
 t state rm aws_instance.ec23
  652  t state rm aws_s3_bucket.bucket
or t state rm '*'


====================================


terraform associate :   1 week over here over this .....documantation of terraform
    
100 mcq 's >>  1 hour ~ 1.5 hrs 

documentation : not cramming just reading..
https://developer.hashicorp.com/terraform/language/resources/behavior


go through this questionbank :
https://www.examtopics.com/exams/hashicorp/terraform-associate/view/


===========================================================

```
