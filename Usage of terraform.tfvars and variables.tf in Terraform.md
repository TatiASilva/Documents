# **Usage of `terraform.tfvars` and `variables.tf` in Terraform**

### **Introduction**

Terraform allows the definition of infrastructure as code through configuration files, where resources, variables, and values are declared. Within this ecosystem, it is important to understand how variables are handled and how we can provide values for these variables. Two common approaches are using the **`terraform.tfvars`** and **`variables.tf`** files.

This document explores the roles of these two files, when to use them, and when they can be replaced or combined, providing a clear understanding of how to structure your Terraform code efficiently.

### **What are `terraform.tfvars` and `variables.tf`?**

1. **`variables.tf`**:
   - A file used to **define variables** in Terraform. It contains the type, description, and default values (if necessary) of the variables that will be used in the Terraform code.
   - **Example**:
     ```hcl
     variable "resource_group_name" {
       description = "Resource Group Name"
       type        = string
     }
     ```

2. **`terraform.tfvars`**:
   - A file where you provide **concrete values** for the variables defined in `variables.tf`. It allows you to override the default values and define environment-specific configurations.
   - **Example**:
     ```hcl
     resource_group_name = "rg-sitecore-dev"
     resource_group_location = "East US"
     ```

### **How do they work together?**

- The **`variables.tf`** file contains the definition of variables, including their types and descriptions.
- The **`terraform.tfvars`** file is used to **assign values** to those variables. Terraform will automatically load the values from `terraform.tfvars` when running `terraform plan` or `terraform apply`.
- Using **`terraform.tfvars`** is optional, but it makes it easier to customize values without modifying the code directly.

### **When to use `variables.tf`?**

**`variables.tf`** is used to **define and document variables**. You should use this file when:
- **You need to document your variables**: Descriptions, types, and default values help provide context about what the variables do.
- **You need type and value validations**: You can set rules to ensure that the variables accept only certain types of data or specific values.
- **You need reusable variables across multiple modules**: By defining variables centrally in `variables.tf`, you can use them in different Terraform modules.

**Example of `variables.tf`:**

```hcl
variable "resource_group_name" {
  description = "Resource Group Name"
  type        = string
}

variable "resource_group_location" {
  description = "Region where the Resource Group will be created"
  type        = string
  default     = "East US"
}
```

### **When to use `terraform.tfvars`?**

**`terraform.tfvars`** is used when you want to **assign values** to the variables defined in `variables.tf`. You should use this file when:
- **You need to customize values** for different environments (development, production, etc.).
- **You don't want to store sensitive values directly in the code** (although `terraform.tfvars` is not ideal for storing sensitive information in public environments, you can use secret variables in CI/CD systems).
- **You need to pass values without modifying the Terraform code directly**.

**Example of `terraform.tfvars`:**

```hcl
resource_group_name = "rg-sitecore-dev"
resource_group_location = "East US"
```

### **When to use both (`variables.tf` and `terraform.tfvars`)?**

You should use both files when you want **good control over variables** and need a clear separation between **variable definitions** and their **specific values** for different environments.

**Advantages of using both**:
- **Clarity**: `variables.tf` defines the behavior of the variable (type, description, default values), while `terraform.tfvars` provides the specific values.
- **Easy environment configuration**: You can maintain a different `terraform.tfvars` file for each environment (e.g., `dev.tfvars`, `prod.tfvars`), ensuring customization without modifying the Terraform code.

**Example of using both**:

- **`variables.tf`**:
  ```hcl
  variable "resource_group_name" {
    description = "Resource Group Name"
    type        = string
  }

  variable "resource_group_location" {
    description = "Azure Region"
    type        = string
    default     = "East US"
  }
  ```

- **`terraform.tfvars`** (for the development environment):
  ```hcl
  resource_group_name = "rg-sitecore-dev"
  resource_group_location = "East US"
  ```

- **`prod.tfvars`** (for the production environment):
  ```hcl
  resource_group_name = "rg-sitecore-prod"
  resource_group_location = "West US"
  ```

You can then use the specific file when running Terraform:
```bash
terraform apply -var-file="prod.tfvars"
```

### **Alternatives and Considerations**

1. **Passing values via command line**:
   - You can pass variables directly on the command line without the need for a `.tfvars` file:
     ```bash
     terraform apply -var="resource_group_name=rg-sitecore-prod" -var="resource_group_location=West US"
     ```
   - This approach can be useful for automation and CI/CD pipelines, where you can pass variables without storing them in files.

2. **Using environment variables**:
   - In CI/CD environments or infrastructure servers, you can also use environment variables to pass values to Terraform, like:
     ```bash
     export TF_VAR_resource_group_name="rg-sitecore-prod"
     export TF_VAR_resource_group_location="West US"
     terraform apply
     ```

### **Conclusion**

- **`variables.tf`** is used to **define** variables, specifying their type, description, and default values.
- **`terraform.tfvars`** is used to **assign values** to these variables in a separate file, making it easy to customize the environment without changing the Terraform code.
- For simpler projects, you can opt to use just the **`terraform.tfvars`** file to provide values and skip the `variables.tf` file. However, as your project grows, separating the responsibilities between both files will bring more clarity, flexibility, and scalability to your Terraform code.

Choosing between using or not using both files depends on the **complexity of your project** and how you want to manage the variables and their values. For smaller and simpler projects, using just `terraform.tfvars` may be sufficient. For larger, more structured projects, using both `variables.tf` and `terraform.tfvars` is the best practice.


---

# **Uso de `terraform.tfvars` e `variables.tf` no Terraform**

### **Introdução**

O Terraform permite a definição de infraestrutura como código através de arquivos de configuração, onde recursos, variáveis e valores são declarados. Dentro deste ecossistema, é importante entender como as variáveis são tratadas e como podemos fornecer valores para essas variáveis. Duas abordagens comuns são o uso dos arquivos **`terraform.tfvars`** e **`variables.tf`**.

Este documento explora os papéis desses dois arquivos, quando utilizá-los e quando podem ser substituídos ou combinados, fornecendo uma visão clara de como organizar seu código Terraform de maneira eficiente.

### **O que são `terraform.tfvars` e `variables.tf`?**

1. **`variables.tf`**:
   - Arquivo utilizado para **definir variáveis** no Terraform. Ele contém o tipo, descrição e valores padrão (caso necessário) das variáveis que serão utilizadas no código Terraform.
   - **Exemplo de uso**:
     ```hcl
     variable "resource_group_name" {
       description = "Nome do Resource Group"
       type        = string
     }
     ```

2. **`terraform.tfvars`**:
   - Arquivo onde você fornece os **valores concretos** para as variáveis definidas em `variables.tf`. Ele permite que você substitua os valores padrão e defina configurações específicas para o ambiente.
   - **Exemplo de uso**:
     ```hcl
     resource_group_name = "rg-sitecore-dev"
     resource_group_location = "East US"
     ```

### **Como funcionam juntos?**

- O arquivo **`variables.tf`** contém a definição das variáveis, incluindo seus tipos e descrições.
- O arquivo **`terraform.tfvars`** é utilizado para **atribuir valores** a essas variáveis. O Terraform carrega as variáveis do `terraform.tfvars` automaticamente ao executar os comandos `terraform plan` ou `terraform apply`.
- O uso do **`terraform.tfvars`** é opcional, mas facilita a personalização de valores sem a necessidade de alterar diretamente o código.

### **Quando usar `variables.tf`?**

**`variables.tf`** é usado para **definir e documentar variáveis**. Você deve usar este arquivo quando:
- **Precisa documentar suas variáveis**: Descrições, tipos e valores padrão ajudam a fornecer contexto sobre o que as variáveis fazem.
- **Precisa de validações de tipo e valores**: Você pode definir regras para garantir que as variáveis aceitem apenas certos tipos de dados ou valores específicos.
- **Precisa de variáveis reutilizáveis em múltiplos módulos**: Ao definir variáveis centralmente em `variables.tf`, você pode usá-las em diferentes módulos Terraform.

**Exemplo de `variables.tf`:**

```hcl
variable "resource_group_name" {
  description = "Nome do Resource Group"
  type        = string
}

variable "resource_group_location" {
  description = "A região onde o Resource Group será criado"
  type        = string
  default     = "East US"
}
```

### **Quando usar `terraform.tfvars`?**

**`terraform.tfvars`** é utilizado quando você deseja **atribuir valores** às variáveis definidas no `variables.tf`. O arquivo `terraform.tfvars` deve ser usado quando:
- **Você precisa personalizar os valores** para diferentes ambientes (desenvolvimento, produção, etc.).
- **Você não quer armazenar valores sensíveis diretamente no código** (embora o `terraform.tfvars` não seja ideal para informações sensíveis em ambientes públicos, você pode usar variáveis secretas em sistemas CI/CD).
- **Você precisa passar valores sem alterar diretamente o código do Terraform**.

**Exemplo de `terraform.tfvars`:**

```hcl
resource_group_name = "rg-sitecore-dev"
resource_group_location = "East US"
```

### **Quando usar ambos (`variables.tf` e `terraform.tfvars`)?**

Você deve usar ambos os arquivos quando desejar **um bom controle sobre as variáveis** e precisa de uma separação clara entre a **definição das variáveis** e seus **valores específicos** para diferentes ambientes.

**Vantagens de usar ambos**:
- **Clareza**: O `variables.tf` define o comportamento da variável (tipo, descrição, valores padrão), enquanto o `terraform.tfvars` fornece os valores específicos.
- **Facilidade de configuração em ambientes diferentes**: Você pode manter um arquivo `terraform.tfvars` diferente para cada ambiente (como `dev.tfvars`, `prod.tfvars`), garantindo a personalização sem alterar o código do Terraform.

**Exemplo de uso combinado**:

- **`variables.tf`**:
  ```hcl
  variable "resource_group_name" {
    description = "Nome do Resource Group"
    type        = string
  }

  variable "resource_group_location" {
    description = "Região do Azure"
    type        = string
    default     = "East US"
  }
  ```

- **`terraform.tfvars`** (para o ambiente de desenvolvimento):
  ```hcl
  resource_group_name = "rg-sitecore-dev"
  resource_group_location = "East US"
  ```

- **`prod.tfvars`** (para o ambiente de produção):
  ```hcl
  resource_group_name = "rg-sitecore-prod"
  resource_group_location = "West US"
  ```

Você pode então usar o arquivo específico ao executar o Terraform:
```bash
terraform apply -var-file="prod.tfvars"
```

### **Alternativas e Considerações**

1. **Passar valores via linha de comando**:
   - Você pode passar variáveis diretamente na linha de comando, sem a necessidade de um arquivo `.tfvars`:
     ```bash
     terraform apply -var="resource_group_name=rg-sitecore-prod" -var="resource_group_location=West US"
     ```
   - Essa abordagem pode ser útil para automação e pipelines CI/CD, onde você pode passar variáveis sem armazená-las em arquivos.

2. **Uso de variáveis de ambiente**:
   - Em ambientes CI/CD ou servidores de infraestrutura, as variáveis de ambiente também podem ser usadas para passar valores ao Terraform, como:
     ```bash
     export TF_VAR_resource_group_name="rg-sitecore-prod"
     export TF_VAR_resource_group_location="West US"
     terraform apply
     ```

### **Conclusão**

- **`variables.tf`** é usado para **definir** as variáveis, especificando o tipo, a descrição e os valores padrão.
- **`terraform.tfvars`** é usado para **atribuir valores** a essas variáveis em um arquivo separado, facilitando a personalização do ambiente sem alterar o código do Terraform.
- Em projetos mais simples, você pode optar por usar apenas o **`terraform.tfvars`** para fornecer valores e dispensar o `variables.tf`. No entanto, à medida que o projeto cresce, a separação das responsabilidades entre os dois arquivos traz mais clareza, flexibilidade e escalabilidade ao seu código Terraform.

Escolher entre usar ou não ambos os arquivos depende da **complexidade do seu projeto** e de como você deseja gerenciar as variáveis e seus valores. Para projetos pequenos e simples, o uso apenas de `terraform.tfvars` pode ser suficiente. Para projetos maiores e mais estruturados, usar tanto `variables.tf` quanto `terraform.tfvars` é a melhor prática.
