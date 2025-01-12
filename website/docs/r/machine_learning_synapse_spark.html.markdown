---
subcategory: "Machine Learning"
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_machine_learning_synapse_spark"
description: |-
  Manages a Machine Learning Synapse Spark.
---

# azurerm_machine_learning_synapse_spark

Manages a Machine Learning Synapse Spark.

## Example Usage

```hcl
data "azurerm_client_config" "current" {}

resource "azurerm_resource_group" "example" {
  name     = "example-rg"
  location = "west europe"
  tags = {
    "stage" = "example"
  }
}

resource "azurerm_application_insights" "example" {
  name                = "example-ai"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_key_vault" "example" {
  name                     = "example-kv"
  location                 = azurerm_resource_group.example.location
  resource_group_name      = azurerm_resource_group.example.name
  tenant_id                = data.azurerm_client_config.current.tenant_id
  sku_name                 = "standard"
  purge_protection_enabled = true
}

resource "azurerm_storage_account" "example" {
  name                     = "examplesa"
  location                 = azurerm_resource_group.example.location
  resource_group_name      = azurerm_resource_group.example.name
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_machine_learning_workspace" "example" {
  name                    = "example-mlw"
  location                = azurerm_resource_group.example.location
  resource_group_name     = azurerm_resource_group.example.name
  application_insights_id = azurerm_application_insights.example.id
  key_vault_id            = azurerm_key_vault.example.id
  storage_account_id      = azurerm_storage_account.example.id
  identity {
    type = "SystemAssigned"
  }
}

resource "azurerm_storage_data_lake_gen2_filesystem" "example" {
  name               = "example"
  storage_account_id = azurerm_storage_account.example.id
}

resource "azurerm_synapse_workspace" "example" {
  name                                 = "example"
  resource_group_name                  = azurerm_resource_group.example.name
  location                             = azurerm_resource_group.example.location
  storage_data_lake_gen2_filesystem_id = azurerm_storage_data_lake_gen2_filesystem.example.id
  sql_administrator_login              = "sqladminuser"
  sql_administrator_login_password     = "H@Sh1CoR3!"
}

resource "azurerm_synapse_spark_pool" "example" {
  name                 = "example"
  synapse_workspace_id = azurerm_synapse_workspace.example.id
  node_size_family     = "MemoryOptimized"
  node_size            = "Small"
  node_count           = 3
}

resource "azurerm_machine_learning_synapse_spark" "example" {
  name                          = "example"
  machine_learning_workspace_id = azurerm_machine_learning_workspace.example.id
  location                      = azurerm_resource_group.example.location
  synapse_spark_pool_id         = azurerm_synapse_spark_pool.example.id

  identity {
    type = "SystemAssigned"
  }
}
```

## Arguments Reference

The following arguments are supported:

* `name` - (Required) The name which should be used for this Machine Learning Synapse Spark. Changing this forces a new Machine Learning Synapse Spark to be created.

* `machine_learning_workspace_id` - (Required) The ID of the Machine Learning Workspace. Changing this forces a new Machine Learning Synapse Spark to be created.

* `location` - (Required) The Azure Region where the Machine Learning Synapse Spark should exist. Changing this forces a new Machine Learning Synapse Spark to be created.

* `synapse_spark_pool_id` - (Required) The ID of the linked Synapse Spark Pool. Changing this forces a new Machine Learning Synapse Spark to be created.

---

* `description` - (Optional) The description of the Machine Learning Synapse Spark. Changing this forces a new Machine Learning Synapse Spark to be created.

* `identity` - (Optional) A `identity` block as defined below. Changing this forces a new Machine Learning Synapse Spark to be created.

* `tags` - (Optional) A mapping of tags which should be assigned to the Machine Learning Synapse Spark. Changing this forces a new Machine Learning Synapse Spark to be created.

---

A `identity` block supports the following:

* `type` - (Required) The Type of Identity which should be used for this Machine Learning Synapse Spark. Possible values are `SystemAssigned`, `UserAssigned` and `SystemAssigned,UserAssigned`. Changing this forces a new Machine Learning Synapse Spark to be created.

* `identity_ids` - (Optional) A list of User Managed Identity ID's which should be assigned to the Machine Learning Synapse Spark. Changing this forces a new Machine Learning Synapse Spark to be created.

## Attributes Reference

In addition to the Arguments listed above - the following Attributes are exported: 

* `id` - The ID of the Machine Learning Synapse Spark.

* `identity` - An `identity` block as defined below, which contains the Managed Service Identity information for this Machine Learning Synapse Spark.

---

A `identity` block exports the following:

* `principal_id` - The Principal ID for the Service Principal associated with the Managed Service Identity of this Machine Learning Synapse Spark.

* `tenant_id` - The Tenant ID for the Service Principal associated with the Managed Service Identity of this Machine Learning Synapse Spark.

## Timeouts

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/docs/configuration/resources.html#timeouts) for certain actions:

* `create` - (Defaults to 30 minutes) Used when creating the Machine Learning Synapse Spark.
* `read` - (Defaults to 5 minutes) Used when retrieving the Machine Learning Synapse Spark.
* `delete` - (Defaults to 30 minutes) Used when deleting the Machine Learning Synapse Spark.

## Import

Machine Learning Synapse Sparks can be imported using the `resource id`, e.g.

```shell
terraform import azurerm_machine_learning_synapse_spark.example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resGroup1/providers/Microsoft.MachineLearningServices/workspaces/workspace1/computes/compute1
```
