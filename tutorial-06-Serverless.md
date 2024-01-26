
# Tutorial 06 Tutorial -Serverless with AWSCli

## file_permission

Here explain Linux file permissions and the chmod command.

In Linux, file permissions control who can read, write, or execute files. They are typically shown as a set of three numbers, with each number representing different access levels for the user, the group, and others, respectively.

The chmod command (which stands for "change mode") is used to change these permissions. The number following chmod represents the permissions you wish to set.

The number is calculated using a simple system:

- 4 stands for "read",
- 2 stands for "write",
- 1 stands for "execute",
- 0 stands for "no permissions".

You can add these numbers together to get different permissions. For example, 7 (4+2+1) would give read, write, and execute permissions.

In your command, `chmod 400 ~/.ssh/key.pem`, 

- 4 (read) for the user (you),
- 0 (no permissions) for the group,
- 0 (no permissions) for others.

That means only the file owner will have the "read" permission, while the members of the file's group and others will have no permissions to read, write, or execute the file.

This command is often used for files that contain sensitive information - in this case, `key.pem` likely being a private key. This level of restriction (400) ensures that this sensitive information can only be read by the user/owner, and cannot be accidentally written to or executed, minimizing the risk of accidental damage or exposure.

## for_each
In Terraform, the `for_each` loop is a powerful construct that allows you to create multiple instances of a resource, based on a set of given values. It is often combined with variables to iterate over complex data structures, and can be used in several ways.

Here are a few examples of how you can use `for_each` with multiple variables:

1. Using `for_each` with a map of objects:
   
   Here, `for_each` iterates over a map of objects, where each object contains multiple properties.

   ```terraform
   variable "user_map" {
     default = {
       alice = { age = 24, city = "New York" }
       bob   = { age = 27, city = "San Francisco" }
     }
   }

   resource "aws_instance" "example" {
     for_each = var.user_map

     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = "t2.micro"

     tags = {
       Name = each.key
       Age  = each.value.age
       City = each.value.city
     }
   }
   ```

   In this example, the `for_each` loop creates an AWS instance for each user in `user_map` with tags that include each user's name, age, and city.

2. Combining `for_each` with `setproduct`:

   The `setproduct` function is used in combination with `for_each` to generate a cartesian product of multiple sets, which can be useful when you need to create resources for every combination of certain values.

   ```terraform
   variable "regions" {
     default = ["us-west-1", "us-west-2", "us-east-1"]
   }

   variable "instances" {
     default = ["t2.micro", "t2.small", "t2.medium"]
   }

   locals {
     region_instance_pairs = { for pair in setproduct(var.regions, var.instances) :
       "${pair[0]}-${pair[1]}" => {
         region  = pair[0]
         instance_type = pair[1]
       }
     }
   }

   resource "aws_instance" "example" {
     for_each = local.region_instance_pairs

     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = each.value.instance_type
     availability_zone = each.value.region

     tags = {
       Name = each.key}
   }
   ```

In this example, an AWS instance is created for every combination of regions and instance types.

3. Using `for_each` with a list of maps:

   If you have a list of maps and you want to create a resource for each map in the list, you can use `for_each` with `toset` and `element`.

   ```terraform
   variable "users" {
     default = [
       {
         name = "Alice"
         age  = 24
         city = "New York"
       },
       {
         name = "Bob"
         age  = 27
         city = "San Francisco"
       },
     ]
   }

   resource "aws_instance" "example" {
     for_each = { for user in var.users : user.name => user }

     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = "t2.micro"

     tags = {
       Name = each.key
       Age  = each.value.age
       City = each.value.city
     }
   }
   ```

In this example, an AWS instance is created for each user specified in the `users` variable.

Each of these examples demonstrates different ways to utilize the `for_each` loop in Terraform with multiple variables. This construct can significantly simplify your code when you need to create multiple similar resources, but with different parameters. Remember that the `for_each` argument expects a map or a set of strings, so you need to structure your variables accordingly.

## how_to_setup_awscli


# Setting up AWS CLI and Creating a CLI Account in AWS on Ubuntu

## Prerequisites:
- Ubuntu 16.04 or higher
- Basic understanding of the Linux command line interface

## Step 1: Installing AWS CLI

1. Update your Ubuntu package list.

```bash
sudo apt-get update
```

2. Install the AWS CLI using `apt-get`.

```bash
sudo apt-get install awscli
```

3. Check the AWS CLI version to confirm that it was installed correctly.

```bash
aws --version
```

## Step 2: Creating a CLI User in AWS

1. Sign into the AWS Management Console.

2. Navigate to the IAM service.

3. Select "Users" from the navigation menu and click "Add User".

4. Enter a name for the user, select "Programmatic Access" as the access type, and click "Next".

5. Select "Attach existing policies directly" and choose the policy that matches the permissions you want to grant to the user. For full access, select "AdministratorAccess".

6. Click "Next: Tags" to add metadata to the user (optional), then "Next: Review" to review the ```markdown
details.

7. Click "Create user" to finalize the creation. 

8. On the final screen, you'll see your access key ID and secret access key. Save these in a secure place. You won't be able to see these again.

**Note:** Never share your secret access key. Anyone who has your access key has the same level of access to your AWS resources as you do.

## Step 3: Configuring AWS CLI

1. Open your terminal and run the following command to start the configuration process:

```bash
aws configure
```
Check the setting by
```bash
aws sts get-caller-identity
```
2. Enter your AWS Access Key ID and Secret Access Key that you saved from Step 2.

3. Specify `json` as the default output format (or another if preferred).

4. Specify your preferred AWS region. You can find region codes in the [AWS Documentation](https://docs.aws.amazon.com/general/latest/gr/rande.html).

```bash
AWS Access Key ID [****************]: YOUR_ACCESS_KEY
AWS Secret Access Key [****************]: YOUR_SECRET_KEY
Default region name [us-east-1]: YOUR_PREFERRED_REGION
Default output format [json]: json
```

Congratulations! You have now set up AWS CLI and created a CLI account in AWS. You can now ```markdown
use AWS services from your Ubuntu terminal.

## Step 4: Test Your Configuration

To confirm that your AWS CLI is configured correctly, you can try running a command using the AWS CLI.

For example, to list all of your S3 buckets, you can use the following command:

```bash
aws s3 ls
```

If your setup is correct, you'll see a list of your S3 buckets. If you haven't created any, the output will be empty.

**Note:** The AWS CLI sends requests to AWS services on your behalf, so you will be charged for any services that the AWS CLI command line tools interact with, such as Amazon EC2 or Amazon S3.
```
Remember to replace `YOUR_ACCESS_KEY`, `YOUR_SECRET_KEY`, and `YOUR_PREFERRED_REGION` with your actual access key, secret key, and region.


## how_to_setup_ssh_for_ansible_manage_ec2


# Step-by-step guide on setting up SSH, creating a key pair in AWS and using the key pair in Ubuntu to connect EC2 via SSH for Ansible 

## Setting up SSH
1. Open up a terminal and install OpenSSH server with the following command:
```
sudo apt-get install openssh-server
```
2. Verify that SSH is running by using:
```
sudo service ssh status
```

## Creating a Keypair in AWS
1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2. In the navigation pane, under NETWORK & SECURITY, choose Key Pairs.
3. Choose Create key pair.
4. For Name, enter the name that you want to use for the key pair, and choose Create Key Pair.

## Using the Keypair in Ubuntu to connect to EC2
1. After the key pair is created, the private key is automatically downloaded by your browser. The base file name is the name you specified as the name of your key pair, and the filename extension is `.pem`. Save the private key file in `.ssh` directory.
2. Change the permission of the key pair file to read only for your user 
```
chmod 400 /path/my-key-pair.pem
```
3. To connect to your instance, use the ssh command.
```
ssh -i /path/my-key-pair.pem my-instance-user-name@my-instance-public-dns-name
```

## Use the keypair in Ansible
1. Install ansible with the following command:
```
sudo apt-get install ansible
```
2. Add the private key to ssh-agent using ssh-add (so that you don't have to use the -i flag with ansible command):
```
ssh-add /path/my-key-pair.pem
```
3. Now, create a hosts file in /etc/ansible (or any directory where you'll run ansible commands from), and add your EC2 instances:
```
[mygroup]
my-instance-public-dns-name
```
4. Test ansible ping:
```
ansible -m ping mygroup
```

## Note
In the ssh command and ansible hosts file, replace `/path/my-key-pair.pem` with the path where you saved your private key, and replace `my-instance-public-dns-name` with the public DNS or public IP of your EC2 instance.

Also, the `my-instance-user-name` will be dependent on the AMI that you have launched on your EC2 instance. For example, it is 'ubuntu' for Ubuntu AMIs and 'ec2-user' for most AWS and Centos AMIs.

These steps should get you started with setting up SSH, creating a keypair in AWS, and using the keypair in Ubuntu to connect to EC2 via SSH for use with Ansible.


## terraform_local

You can practice with Terraform on your local machine without any external dependencies. Terraform has a built-in provisioner called "local-exec" that allows you to execute a local command. You can use it to understand Terraform's core concepts like providers, resources, variables, outputs, etc.

Here is a simple example:

1. Create a new directory for your Terraform project.

```bash
mkdir terraform-local
cd terraform-local
```

2. Create a new file called `main.tf` in the directory you just created.

```bash
touch main.tf
```

3. Open `main.tf` in a text editor, and add the following code:

```hcl
variable "my_var" {
  type    = string
  default = "Hello, World!"
}

resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo ${var.my_var}"
  }
}

output "my_output" {
  value = var.my_var
}
```
This configuration defines a variable, uses it in a local command, and outputs it.

4. Initialize your Terraform working directory:

```bash
terraform init
```

5. Apply your Terraform configuration:

```bash
terraform apply
```

This command creates your resource as defined in `main.tf`. Terraform will display what actions will be taken before asking for confirmation. If everything looks good, answer `yes`.

6. You should see the output of the command `echo ${var.my_var}` executed, and also the value of `my_output` with the same `Hello, World!` message.

Remember to delete the resources when you're done testing:

```bash
terraform destroy
```

This is a very basic example of how to use Terraform's local-exec provisioner, variables, and outputs. Depending on what you want to practice, you might need to adjust your Terraform configuration accordingly.

## tf_basic_demo


# Terraform: Introduction, Demo and Exercise

Terraform, developed by HashiCorp, is an open-source infrastructure as code software tool that allows you to safely and predictably create, change, and improve infrastructure. This guide provides a quick introduction to Terraform, focusing on resources, locals, variables, and loops using null_resource.

## Introduction

### Resources

In Terraform, a Resource represents an item that can be created, updated, or destroyed, such as a physical server or a database. 

```terraform
resource "null_resource" "example" {
  # Configuration details go here
}
```

### Local Values

Local values assign a name to an expression so that it can be used multiple times within a module. 

```terraform
locals {
  # Configuration details go here
  common_tags = {
    Owner = "DevOps Team"
  }
}
```

### Variables

Variables in Terraform are a great way to define centrally controlled reusable values. The configuration looks like this:


```terraform
variable "image_id" {
  description = "The id of the machine image (AMI) to use for the server."
  type        = string
}
```

### Loops

Using `count`, `for_each`, and `for`, you can create multiple instances of resources and data sources, conditionally create or omit resources, and iterate over collections to generate multiple instances of resources and data sources.

```terraform
resource "null_resource" "cluster" {
  count = 5 # creates 5 null resources
}
```

## Demo

Let's see how we can use a null_resource with a local and a variable in a loop.

```terraform
variable "instance_count" {
  description = "The number of instances to create."
  type        = number
  default     = 5
}

locals {
  common_tags = {
    Owner = "DevOps Team"
  }
}

resource "null_resource" "example" {
  count = var.instance_count

  triggers = {
    tag_value = local.common_tags.Owner
  }
}
```

In this example, we create a number of null resources specified by the "instance_count" variable. Each null resource gets a tag from a local value.

## Exercise

1. Create a variable "student_name" with your name as the default value.
2. Define a local value "common_tags" with a tag "Student" set to the "student_name" variable.
3. Create a null_resource "student" with count 3, each having a trigger with the "Student" tag value.

Save your code in a ".tf" file and initialize Terraform by running `terraform init`. Then, apply the configuration using `terraform apply`.

## Solution

You can verify your solution with the code below:

```terraform
variable "student_name" {
  description = "The student's name."
  type        = string
  default     = "Your Name"
}

locals {
  common_tags = {
    Student = var.student_name
  }
}

resource "null_resource" "student" {
  count = 3

  triggers = {
    tag_value = local.common_tags.Student
  }
}
```

If you initialized and applied this configuration successfully, Terraform would create three null resources with a trigger tag "Student" set to your name.


## tf_create_lambda

Here is some answer to https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK6_Terraform/hands_on/4%20-%20Homework.md

Same code saved in repo https://github.com/AutomationLover/tflambda

First, let's begin with the folder structure:
```
terraform_lambda/
│
├── lambda_function/
│   └── helloword.py
│
├── main.tf
├── variables.tf
└── outputs.tf
```

Now let's go through each file:

1. `lambda_function/helloword.py`: This is a simple Python lambda function.
    ```python
    def lambda_handler(event, context):
        print("Hello, World!")
    ```



2. `main.tf`: Use the variable in your S3 bucket and policy definition.

Here is the updated `main.tf`:

```markdown
provider "aws" {
  region  = "us-west-2"
}

resource "aws_s3_bucket" "bucket" {
  bucket = var.bucket_name
  acl    = "private"

  tags = {
    Name        = "My bucket"
    Environment = "Dev"
  }
}


resource "aws_lambda_function" "example" {
  function_name    = "lambda_function_name"
  s3_bucket        = aws_s3_bucket.bucket.bucket
  s3_key           = "lambda_function_payload.zip"
  handler          = "helloworld.lambda_handler"
  runtime          = "python3.8"
  role             = aws_iam_role.role.arn

  depends_on = [
    aws_s3_bucket_object.object,
  ]
}

data "archive_file" "example_zip" {
  type        = "zip"
  source_dir  = "lambda_function"
  output_path = "lambda_function_payload.zip"
}

resource "aws_s3_bucket_object" "object" {
  bucket = aws_s3_bucket.bucket.bucket
  key    = "lambda_function_payload.zip"
  source = "lambda_function_payload.zip" 

  depends_on = [
    data.archive_file.example_zip,
  ]
}

resource "aws_iam_role" "role" {
  name = "lambda_s3_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      },
    ]
  })
}

resource "aws_iam_role_policy_attachment" "attach_s3_policy" {
  role       = aws_iam_role.role.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}
```


3. `variables.tf`: This file will define any variables we are going to use.

    ```markdown
    variable "region" {
      description = "AWS region"
      default     = "us-west-2"
    }
    variable "bucket_name" {
      description = "S3 bucket name"
      default     = "my-unique-bucket-name"
    }
    ```

4. `outputs.tf`: This file will define any output we might require.

    ```markdown
    output "lambda_function_name" {
      description = "The name of the Lambda Function"
      value       = aws_lambda_function.example.function_name
    }

    output "s3_bucket_id" {
      description = "The ID of the S3 bucket"
      value       = aws_s3_bucket.bucket.id
    }
    ```
Make sure to replace `"my-unique-bucket-name"` in `variables.tf` with your desired unique bucket name and `"lambda_function_name"` in `main.tf` with your desired lambda function name. 

Once done,you can initialize Terraform with the following command:

```markdown
terraform init
```

This will set up the necessary providers for your project.

Next, you can apply your configuration with the following command:

```markdown
terraform apply
```

This will prompt you to confirm that you want to create the resources defined in your `.tf` files. Confirm by typing `yes`. 

When the command completes, you should see output similar to the following:

```markdown
Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

Outputs:

lambda_function_name = "lambda_function_name"
s3_bucket_id = "bucket_name"
```

This output indicates that your Lambda function and S3 bucket were created successfully.

One thing to remember, you need to upload your Python code to the S3 bucket before referencing it in your Lambda function. The Terraform configuration provided above takes care of that by creating a `.zip` file of your code and uploading it to the S3 bucket before creating the Lambda function.


## tf_example_answer

BEFORE


```hcl
variable "regions" {
  type = map(any)
  default = {
    "region1" = {
      draft = false
      latency = 50
    },
    "region2" = {
      draft = true
      latency = 100
    }
  }
}

resource "null_resource" "example" {
  for_each = var.regions

  provisioner "local-exec" {
    command = "echo Creating resource for region: ${each.key} with latency ${each.value.latency} and draft status ${each.value.draft}"
  }
}
```



AFTER

```hcl
variable "regions" {
  description = "Regions map"
  type        = map(any)
  default     = {
    "region1" = {
      draft   = false
      latency = 50
    },
    "region2" = {
      draft   = true
      latency = 100
    }
  }
}

variable "aws_services" {
  description = "AWS Services"
  type        = list(string)
  default     = ["EC2", "S3"]
}

locals {
  combined = { for pair in setproduct(keys(var.regions), var.aws_services) :
    "${pair[0]}-${pair[1]}" => {
      region  = pair[0]
      service = pair[1]
      draft   = var.regions[pair[0]].draft
      latency = var.regions[pair[0]].latency
    }
  }
}


resource "null_resource" "example" {
  for_each = local.combined

  provisioner "local-exec" {
    command = "echo Creating resource for region: ${each.value.region}, service: ${each.value.service}, with latency: ${each.value.latency} and draft status: ${each.value.draft}"
  }
}
```

Some update

```hcl
variable "regions" {
  description = "Regions map"
  type        = map(any)
  default     = {
    "region1" = {
      draft   = false
      latency = 50
    },
    "region2" = {
      draft   = true
      latency = 100
    }
  }
}

variable "aws_services" {
  description = "AWS Services"
  type        = list(string)
  default     = ["EC2", "S3"]
}

locals {
  combined = { for pair in setproduct(keys(var.regions), var.aws_services) :
    "${pair[0]}-${pair[1]}" => {
      region  = pair[0]
      service = pair[1]
      region_var   = var.regions[pair[0]]
    }
  }
}


resource "null_resource" "example" {
  for_each = local.combined

  provisioner "local-exec" {
    command = "echo Creating resource for region: ${each.value.region}, service: ${each.value.service}, with latency: ${each.value.region_var.latency} and draft status: ${each.value.region_var.draft}"
  }
}

```
In the context of Terraform, `pair[0]` and `var.regions[pair[0]]` differently reference elements of a data structure. Here's an explanation:

- `pair[0]`: This syntax is used when you have a list of items, and you want to access an item in the list by its index. In Terraform, list indices start at 0, so `pair[0]` gives you the first item in the list named `pair`. 

- `var.regions[pair[0]]`: This syntax is used to access a map's values using its keys. Here, `var.regions` is a map, and `pair[0]` is being used as a key to this map. `var.regions[pair[0]]` will return the value that is associated with the key `pair[0]` in the `regions` map.

So, if `pair[0]` is 'region1', `var.regions[pair[0]]` will return the value associated with the key 'region1' in the `regions` map. This value is itself another map with keys like 'draft' and 'latency'. To access these inner values, you would use something like `var.regions[pair[0]].latency`, which will return the value associated with the 'latency' key in the map that is associated with the 'region1' key in the `regions` map.

## tf_homework

**Terraform Locals Homework**

Objective: 
This assignment aims to help you understand the usage of Terraform locals and how to use the `null_resource` with the `local-exec` provisioner. 

Tasks:
1. Install Terraform on your local machine if you haven't done so already.

2. Create a new directory named `terraform-local` and navigate into it.

3. Create a new Terraform configuration file named `main.tf`.

4. In `main.tf`, define two variables: `regions` and `aws_services`. 

    - `regions`: a map variable that contains different regions with properties like 'draft' (boolean) and 'latency' (integer).
    - `aws_services`: a list variable that contains different AWS service names as strings.

5. Define a `locals` block that creates a combination of regions and services using the `setproduct` function. This will be a map where each key is a combination of a region and a service, and the value is another map with details about the region and the service.

6. Define a `null_resource` with a `local-exec` provisioner that uses the `locals` block you created earlier.
   
   - The `null_resource` should use the `for_each` argument to iterate over each item in the `locals` block.
   - The `local-exec` provisioner should echo a message containing the region, service, latency, and draft status.

7. Initialize your Terraform working directory by running `terraform init`.

8. Apply your Terraform configuration by running `terraform apply`.

   - Observe the echo messages for each combination of region and service.

9. Remember to destroy the resources when you're done testing by running `terraform destroy`.

Deliverables:
- Submit your `main.tf` file.
- Provide screenshots of the terminal output from running `terraform apply` and `terraform destroy`.

Note: 
- Be sure to understand how the `setproduct` function and `for_each` argument work.
- Familiarize yourself with the structure and usage of Terraform `locals`.
- Understand the purpose and usage of the `null_resource` and `local-exec` provisioner.
- This assignment does not require any cloud resources. All tasks are performed locally.
