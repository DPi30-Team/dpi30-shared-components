# DPi30 Shared Components: Styles and Conventions

## Table of contents
1. [Usage](#Usage)
2. [Branching](#Branching)
3. [Resource naming and parameter metadata description properties](#Resource-naming-and-parameter-metadata-description-properties)
4. [Property name formatting](#Property-name-formatting)

### Usage

1. In the DPi30 application repository, create a new directory structure named `code\arm\templates`

2. Within the directory create a new template named `template.json`


2. Set the root 'Raw' path of the Azure Resource templates as a top-level variable as follows:

    ```json
    "variables": {
            "deploymentUrlBase": "https://raw.githubusercontent.com/DPi30-Team/dpi30-shared-components/master/templates/"
    }
    ```

3. Select the Azure Resource template you wish to use, (eg. https://raw.githubusercontent.com/DPi30-Team/dpi30-shared-components/master/templates/sql-server.json) by setting the concatenation of the deploymentUrlBase variable and the file name as the uri of the templateLink property as follows:

    ```json
    {
        "apiVersion": "2017-05-10",
        "name": "app-service",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "Incremental", //or Complete
            "templateLink": {
                "uri": "[concat(variables('deploymentUrlBase'),'sql-server.json')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "parameter1": {
                    "value": "<Your value>"
                },
                "parameter2": {
                    "value": "<Your value>"
                }
            }
        },
        "dependsOn":[
            "<Any dependencies>"
        ]
    }
    ```

## Branching

1. Always do your work in a new branch.

2. Do not ever commit straight to master.

3. Submit a pull request to the team when you are ready for review.

4. After the pull request is approved and the branch merged to master, delete the branch you made.

## Resource naming and parameter metadata description properties

Reduce the number of top-level parameters needed by setting the Azure resource names with variables:

1. Set the top-level ```resourceEnvironmentName``` and ```serviceName``` parameters as follows, adding a metadata description property to all top-level parameters:

    ```json
"parameters": {
    "Organization_Name": {
      "defaultValue": "Contoso",
      "type": "string",
      "metadata": {
        "description": "Short name of the organization. Used for the name of global resources created."
      }
    },
    "projectname": {
      "defaultValue": "adap",
      "type": "string",
      "metadata": {
        "description": "Short name of the ADAP project. Used for the name of resources created. (default: adap)"
      }
    },
    "projectversion": {
      "type": "string",
      "metadata": {
        "description": "Version number of the project."
      }
    },
    "chargebackid": {
      "defaultValue": "8675309",
      "type": "string",
      "metadata": {
        "description": "Chargeback Id for the resource. Used in Tags"
      }
    },
    "dri": {
      "defaultValue": "admin@contoso.com",
      "type": "string",
      "metadata": {
        "description": "The email address of the directly responsible individual. used in Tags"
      }
    },
    "environmenttag": {
      "defaultValue": "dev",
      "type": "string",
      "metadata": {
        "description": "The tag name of the environment. (smk, dev, uat, prod, sbx)"
      }
    },
    "locationtag": {
      "defaultValue": "eus",
      "type": "string",
      "metadata": {
        "description": "The tag name of the location or region. Should align with the resource location. (eus, eus2, wus, wus2, scus, ncus, cus)"
      }
    },
            ...
    }
    ```

2. Set the top-level variable of the ```resourceNamePrefix``` and Azure resource names as follows:

    ```json
    "variables": {
        ...
        "resourceNamePrefix": "[toLower(concat(parameters('projectname'),'-',parameters('environmenttag'),'-',parameters('locationtag')))]",
		"sqlServerName": "[toLower(concat(parameters('projectname'),'-',parameters('environmenttag'),'-',parameters('locationtag'),'-sql'))]",
        "storageAccountName": "[concat(parameters('projectname'),parameters('environmenttag'),parameters('locationtag'),'stg')]",
        ...
    }
    ```
3. Within the properties section of the resource deployment section, use the resource name variable for the name parameter of the resource as follows:

    ```json
    "parameters": {
        "sqlServerName": {
            "value": "[variables('sqlServerName')]"
        },
        ...
    }
    ```
4. Set the the top-level output of the generated variable that will be used in the release pipeline as follows:

    ```json
    "outputs": {
        "sqlServerName": {
            "type": "string",
            "value": "[variables('sqlServerName')]"
        },
        ...
    }
    ```
## Property name formatting

The convention of property name formatting, as used in the examples above:

1. Parameters: camelCase (matching the release pipeline override template parameters)
2. Variables: camelCase
3. Resource Deployments: lowercase-with-hyphens
4. Outputs: PascalCase (matching the release pipeline variables)