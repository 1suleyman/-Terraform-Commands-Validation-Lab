# 🛠️ Terraform Commands & Validation Lab

In this lab, I learned how to **validate Terraform configurations, fix syntax and argument errors, understand the difference between `terraform validate` and `terraform apply`, format configuration files, inspect provider plugins, and use important Terraform commands like `terraform graph` and `terraform providers mirror`**.

---

## 📋 Lab Overview

**Goal:**

* Use Terraform commands to validate, format, preview, and apply infrastructure
* Identify syntax vs runtime (provider-level) errors
* Generate visual dependency graphs
* Inspect provider plugins without manually opening `.terraform` folders

**Learning Outcomes:**

* Understand how `terraform validate` works
* Fix incorrect arguments inside resource blocks
* See why `terraform apply` can fail even when validation passes
* Use `terraform graph`, `terraform fmt`, and `terraform providers` commands
* Inspect downloaded provider plugins from the CLI

---

## 🛠 Step-by-Step Journey

### Step 1: Identify Which Command Generates a Visual Graph

**Question:** Which command creates a visual representation of Terraform resources?
**Answer:** `terraform graph`

---

### Step 2: Validate a Faulty Configuration

**Directory:**

```
/root/terraform-projects/project-shazam
```

The configuration attempted to:

* Create a **RSA private key**
* Create a **certificate signing request (CSR)** using that key

But it contained argument errors.

**Commands Run:**

```bash
cd /root/terraform-projects/project-shazam
terraform validate
```

**Error Found:**

```
Unsupported argument: dsa_bits = 0485
```

This should’ve been:

```
rsa_bits
```

After fixing:

```hcl
rsa_bits = 0485
```

**Revalidated:**

```bash
terraform validate
# Success!
```

---

### Step 3: Generate an Execution Plan

```bash
terraform plan
# Plan generated successfully
```

---

### Step 4: Run `terraform apply` and Observe Failure

```bash
terraform apply
# Entered "yes"
```

**Result:**
Terraform failed due to a CSR creation error.

---

### Step 5: Understand Why `validate` Can Pass While `apply` Fails

**Key Concept:**
`terraform validate` checks **syntax and argument structure only**.

It **does NOT** check:

* whether the values are appropriate
* whether the provider can successfully create the resource
* whether required arguments are missing

`terraform apply`, however:

* Talks to the provider
* Attempts actual resource creation
* Fails when arguments or values are invalid

---

### Step 6: Fix the Root Cause of the `terraform apply` Failure

The error message mentioned:

```
message too long for RSA key size
```

The RSA key was too small to generate a valid CSR.

**Fix:** Increase the bit size to **2048** (minimum required).

```hcl
rsa_bits = 2048
```

Retested:

```bash
terraform plan
terraform apply
# Success
```

---

### Step 7: Format the Terraform File

To normalize the style of `main.tf`:

```bash
terraform fmt
```

This rewrote the configuration into canonical Terraform format.

---

### Step 8: Inspect a Different Configuration’s File Name Argument

**Directory:**

```
/root/terraform-projects/project-a
```

Task: Identify the value of a resource's `filename` argument.

Result:
`"quotes/quotes/quotes..."` (as seen in the resource block)

---

### Step 9: Understand What Providers Truly Are

**Providers = Plugins**

* They are **binary executables** downloaded by `terraform init`
* They allow Terraform to interact with external APIs (AWS, local, random, TLS, etc.)
* They live inside the `.terraform/providers` directory

---

### Step 10: Identify a Valid Subcommand of `terraform providers mirror`

Correct answer:
✔️ A recognized mirror subcommand (choice selected in the lab)

---

### Step 11: Inspect Installed Provider Plugins Using CLI (NOT filesystem)

**Directory:**

```
/root/terraform-projects/provider
```

Commands:

```bash
cd /root/terraform-projects/provider
terraform providers
```

**Providers Detected:**

* `local`
* `aws`

No need to manually open `.terraform` directories — Terraform prints provider information directly.

---

## ✅ Key Commands Summary

| Task                     | Command                                         |
| ------------------------ | ----------------------------------------------- |
| Create resource graph    | `terraform graph`                               |
| Validate config syntax   | `terraform validate`                            |
| Preview execution plan   | `terraform plan`                                |
| Apply changes            | `terraform apply`                               |
| Format Terraform files   | `terraform fmt`                                 |
| View installed providers | `terraform providers`                           |
| Check plugins via mirror | `terraform providers mirror`                    |
| Fix argument issues      | Update `rsa_bits`, remove unsupported arguments |

---

## 💡 Notes / Tips

* `terraform validate` **does NOT** guarantee resources will be created successfully.
* Provider interaction errors only appear during **`terraform plan`** or **`terraform apply`**.
* Use `terraform fmt` regularly — clean code prevents future errors.
* `terraform providers` is the safest way to inspect installed plugins.
* RSA keys smaller than **2048 bits** will often cause CSR creation errors.
* Always check error messages — Terraform is very descriptive.

---

## 📌 Lab Summary

| Step                            | Status | Key Takeaways                       |
| ------------------------------- | ------ | ----------------------------------- |
| Identify resource graph command | ✅      | `terraform graph`                   |
| Validate configuration          | ✅      | Found incorrect argument `dsa_bits` |
| Fix validation errors           | ✅      | Updated to `rsa_bits`               |
| Generate execution plan         | ✅      | Successful                          |
| Apply & observe failure         | ✅      | Runtime provider error              |
| Fix RSA key size                | ✅      | Minimum 2048 bits                   |
| Format config                   | ✅      | `terraform fmt` used                |
| Inspect provider plugins        | ✅      | Used `terraform providers`          |

---

## ✅ References

* [Terraform Validate](https://developer.hashicorp.com/terraform/cli/commands/validate)
* [Terraform Graph](https://developer.hashicorp.com/terraform/cli/commands/graph)
* [Terraform Providers](https://developer.hashicorp.com/terraform/language/providers)
* [Terraform Format Command](https://developer.hashicorp.com/terraform/cli/commands/fmt)
* [TLS Provider Documentation](https://registry.terraform.io/providers/hashicorp/tls/latest/docs)
