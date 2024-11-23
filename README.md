# Terraform-Notes
### **Structured Terraform Notes**

---

#### **Core Commands**
1. **`terraform init`**  
   - Initializes the Terraform project by downloading required plugins (e.g., AWS plugin).

2. **`terraform plan`**  
   - Previews the changes that will be applied to the infrastructure.

3. **`terraform apply`**  
   - Applies the changes and provisions the infrastructure.

4. **`terraform fmt`**  
   - Formats the code to improve readability.

5. **`terraform destroy`**  
   - Destroys all resources created by the code.

6. **`terraform force-unlock [options] LOCK_ID`**  
   - Releases a lock in case someone else is applying changes simultaneously.

7. **`terraform apply --var-file <file_name>.tfvars`**  
   - Loads variable values from a specified file.

---

#### **Workspaces**
1. **`terraform workspace show`**  
   - Displays the current workspace name.

2. **`terraform workspace new <WORKSPACE_NAME>`**  
   - Creates a new workspace.

3. **`terraform workspace list`**  
   - Lists all available workspaces in the project.

4. **`terraform workspace select <WORKSPACE_NAME>`**  
   - Selects a specific workspace.

---

#### **Variables and Outputs**
1. **Defining Variables (`variables.tf`)**
   ```hcl
   variable "cidr" {
     type = string
   }
   ```

2. **Using Variables in Resources (`vpc.tf`)**
   ```hcl
   resource "aws_vpc" "main" {
     cidr_block         = var.cidr
     enable_dns_support = false
   }
   ```

3. **Setting Variables in Files (`terraform.tfvars`)**
   ```hcl
   cidr = "10.0.0.0/16"
   ```
   - Common practice: use different variable files like `dev.tfvars` or `prod.tfvars` for multiple environments.

4. **Output Variables (`output.tf`)**
   ```hcl
   output "vpc_id" {
     value = aws_vpc.myVPC.id
   }
   ```

---

#### **Backend Configuration (`backend.tf`)**
- Example:
  ```hcl
  terraform {
    backend "s3" {
      bucket         = "myS3"
      key            = "path/in/s3"
      region         = "us-east-1"
      dynamodb_table = "myDynamoDB"
    }
  }
  ```

- **Purpose**:  
  - **S3**: Stores the state file to share between collaborators.  
  - **DynamoDB**: Enables state locking and consistency to prevent simultaneous `apply` operations.

- **DynamoDB Table Requirements**:
  - Must have a partition key named `LockID` with a type of `String`.

---

#### **Provider Configuration (`provider.tf`)**
- **Method 1: Define keys directly in the provider file**:
  ```hcl
  provider "aws" {
    version    = "~> 5.0"
    region     = "us-east-1"
    access_key = "my-access-key"
    secret_key = "my-secret-key"
  }
  ```

- **Method 2: Export environment variables in Linux**:
  ```bash
  export AWS_ACCESS_KEY_ID="my-access-key"
  export AWS_SECRET_ACCESS_KEY="my-secret-key"
  ```

- **Method 3: Use AWS CLI credentials file (recommended)**:
  ```plaintext
  vim ~/.aws/credentials

  [default]
  aws_access_key_id = ASIAIOSFODNN7EXAMPLE
  aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
  ```

---

#### **Provisioners**
- Provisioners allow you to execute scripts or commands during resource creation.

- Example: Using a `local-exec` provisioner to run a Linux CLI command:
  ```hcl
  resource "aws_vpc" "myVPC" {
    cidr_block         = var.cidr
    enable_dns_support = false

    provisioner "local-exec" {
      command = "touch ${self.id}"
    }
  }
  ```

---

This structured approach should help make your notes easier to follow and practical for daily Terraform use. Let me know if you need further adjustments!
