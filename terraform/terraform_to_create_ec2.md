```hcl
# Define the provider (AWS)
provider "aws" {
  region = "us-east-1" # Change this to your desired region
}

# Define a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.0.0.0/16"
}

# Define a subnet within the VPC
resource "aws_subnet" "example" {
  vpc_id                  = aws_vpc.example.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a" # Change this to your desired availability zone
}

# Define a security group for the instance
resource "aws_security_group" "example" {
  name        = "example"
  description = "Example security group for EC2 instance"
  
  # Define ingress rules (allowing incoming traffic)
  ingress {
    from_port   = 22 # SSH
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # Allow SSH from anywhere (for demonstration purposes)
  }
  
  # You can add more ingress rules as needed for your application
}

# Define the EC2 instance
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI, change to your desired AMI
  instance_type = "t2.micro" # Change to your desired instance type
  subnet_id     = aws_subnet.example.id
  key_name      = "your-key-pair-name" # Replace with your EC2 key pair name

  # Attach the security group to the instance
  vpc_security_group_ids = [aws_security_group.example.id]

  # Define user data if needed
  user_data = <<-EOF
              #!/bin/bash
              echo "Hello, World!" > /var/www/html/index.html
              EOF
}

# Output the instance's public IP address
output "instance_public_ip" {
  value = aws_instance.example.public_ip
}
