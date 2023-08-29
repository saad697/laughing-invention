# laughing-invention
provider "aws" {
  region = "us-east-1"  # Update with your desired region
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_subnet" "private_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.2.0/24"
}

resource "aws_security_group" "instance_security_group" {
  name        = "instance-sg"
  description = "Security group for the EC2 instance"

  // Inbound rule for SSH
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  // Outbound rule allowing all traffic
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  vpc_id = aws_vpc.my_vpc.id
}

resource "aws_instance" "ec2_instance" {
  ami           = "ami-12345678"  # Replace with your desired AMI ID
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  root_block_device {
    volume_size = 5
    volume_type = "gp2"
    delete_on_termination = true
  }

  tags = {
    "purpose" = "Assignment"
  }

  security_groups = [aws_security_group.instance_security_group.name]
}
