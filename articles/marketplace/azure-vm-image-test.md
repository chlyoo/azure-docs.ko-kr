---
title: Azure Marketplace에 대 한 Azure 가상 머신 이미지 테스트
description: Azure Marketplace에서 Azure virtual machine 제품을 테스트 하 고 제출 하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 10/15/2020
ms.openlocfilehash: a9698981b1a658664bfc14886628bbfd0a4a64d2
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976931"
---
# <a name="test-a-virtual-machine-image"></a>가상 컴퓨터 이미지 테스트

이 문서에서는 상업용 Marketplace에서 VM(가상 머신) 이미지를 테스트하고 제출하여 최신 Azure Marketplace 게시 요구 사항을 충족하도록 하는 방법을 설명합니다.

VM 제품을 제출하기 전에 다음 단계를 완료합니다.

- 일반화 된 이미지를 사용 하 여 Azure VM을 배포 합니다. [승인 된 기본을 사용 하 여 가상 머신 만들기](azure-vm-create-using-approved-base.md) 또는 [고유한 이미지를 사용 하 여 가상 머신 만들기를](azure-vm-create-using-own-image.md)참조 하세요.
- 유효성 검사를 실행합니다.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>일반화된 이미지를 사용하여 Azure VM 배포

이 섹션에서는 일반화 된 VHD (가상 하드 디스크) 이미지를 배포 하 여 새 Azure VM 리소스를 만드는 방법을 설명 합니다. 이 프로세스의 경우 제공 된 Azure Resource Manager 템플릿과 Azure PowerShell 스크립트를 사용 합니다.

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 준비

이 섹션에서는 사용자 제공 VM(가상 머신) 이미지를 만들고 배포하는 방법을 설명합니다. Azure에서 배포 된 가상 하드 디스크에서 운영 체제 및 데이터 디스크 VHD 이미지를 제공 하 여이 작업을 수행할 수 있습니다. 이러한 단계는 일반화 된 VHD를 사용 하 여 VM을 배포 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 일반화 된 운영 체제 VHD 및 데이터 디스크 Vhd를 Azure Storage 계정에 업로드 합니다.
3. 홈 페이지에서 **리소스 만들기** 를 선택 하 고 "템플릿 배포"를 검색 한 후 **만들기** 를 선택 합니다.
4. **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="템플릿 선택을 표시 합니다.":::

5. 다음 JSON 템플릿을 편집기에 붙여넣고 **저장** 을 선택 합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

<br>

6. 표시된 사용자 지정 배포 속성 페이지에 대한 매개 변수 값을 제공합니다.

    | ResourceGroupName | 기존 Azure 리소스 그룹 이름입니다. 일반적으로 Key Vault과 동일한 RG를 사용합니다. |
    | --- | --- |
    | TemplateFile | VHDtoImage.json 파일에 대한 전체 경로 이름입니다. |
    | userStorageAccountName | 스토리지 계정 이름 |
    | dnsNameForPublicIP | 퍼블릭 IP의 DNS 이름입니다. 소문자여야 합니다. |
    | subscriptionId | Azure 구독 식별자입니다. |
    | 위치 | 리소스 그룹의 표준 Azure 지리적 위치입니다. |
    | vmName | 가상 머신의 이름입니다. |
    | vhdUrl | 가상 하드 디스크의 웹 주소입니다. |
    | vmSize | 가상 머신 인스턴스의 크기입니다. |
    | publicIPAddressName | 퍼블릭 IP 주소의 이름입니다. |
    | virtualNetworkName | 가상 네트워크의 이름입니다. |
    | nicName | 가상 네트워크의 네트워크 인터페이스 카드 이름입니다. |
    | adminUserName | 관리자 계정의 사용자 이름입니다. |
    | adminPassword | 관리자 암호입니다. |


7. 이러한 값을 입력한 후 **구매** 를 선택합니다.

Azure에서 배포가 시작됩니다. 지정된 관리되지 않는 VHD가 있는 새 VM을 지정된 스토리지 계정 경로에 만듭니다. 포털의 왼쪽에 있는 **Virtual Machines** 를 선택하여 Azure Portal에서 진행 상황을 추적할 수 있습니다. VM을 만들면 상태가 시작 중에서 실행 중으로 변경됩니다.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>2 세대 VM 배포의 경우 다음 템플릿을 사용 합니다.

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>PowerShell을 사용 하 여 Azure VM 배포

다음 스크립트를 복사 및 편집하여 `$storageaccount` 및 `$vhdUrl` 변수의 값을 제공합니다. 이 스크립트를 실행하여 일반화된 기존 VHD에서 Azure VM 리소스를 만듭니다.

```PowerShell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
```

## <a name="run-validations"></a>유효성 검사 실행

배포 된 이미지에 대해 유효성 검사를 실행 하는 방법에는 두 가지가 있습니다.

### <a name="use-certification-test-tool-for-azure-certified"></a>Azure Certified용 인증 테스트 도구 사용

#### <a name="download-and-run-the-certification-test-tool"></a>인증 테스트 도구 다운로드 및 실행

Azure 인증 테스트 도구는 로컬 Windows 머신에서 실행되지만 Azure 기반 Windows 또는 Linux VM을 테스트합니다. 사용자 VM 이미지를 Microsoft Azure에서 사용할 수 있는지, 즉 VHD 준비와 관련된 지침과 요구 사항이 충족되었는지를 확인합니다.

1. 가장 최근의 [Azure Certified용 인증 테스트 도구](https://www.microsoft.com/download/details.aspx?id=44299)를 다운로드하여 설치합니다.
2. 인증 도구를 연 다음, **새 테스트 시작** 을 선택합니다.
3. 테스트 정보 화면에서 테스트 실행에 대한 **테스트 이름** 이름을 입력합니다.
4. VM에 대 한 플랫폼을 **Windows Server** 또는 **Linux** 중에서 선택 합니다. 플랫폼 선택은 나머지 옵션에 영향을 줍니다.
5. VM에서 이 데이터베이스 서비스를 사용하는 경우 **Azure SQL Database 테스트** 확인란을 선택합니다.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>VM 이미지에 인증 도구 연결

1. SSH 인증 모드로 암호 인증 또는 키 파일 인증을 선택합니다.
2. 암호 기반 인증을 사용 하는 경우 **VM DNS 이름**, **사용자 이름** 및 **암호** 에 대 한 값을 입력 합니다. 기본 SSH 포트 번호를 변경할 수도 있습니다.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="VM 테스트 정보를 선택 하는 방법을 보여 줍니다.":::

3. 키 파일 기반 인증을 사용하는 경우 VM DNS Name, 사용자 이름 및 프라이빗 키 위치에 대한 값을 입력합니다. 암호를 제공하거나 기본 SSH 포트 번호를 변경할 수도 있습니다.
4. 정규화된 VM DNS 이름을 입력합니다(예: MyVMName.Cloudapp.net).
5. **사용자 이름** 및 **암호** 를 입력 합니다.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="VM 사용자 이름 및 암호를 선택 하는 방법을 보여 줍니다.":::

6. **다음** 을 선택합니다.

#### <a name="run-a-certification-test"></a>인증 테스트 실행

인증 도구에서 VM 이미지에 대 한 매개 변수 값을 지정한 후 연결 테스트를 선택 하 여 VM에 대 한 올바른 연결을 만듭니다. 연결이 확인되면 **다음** 을 선택하여 테스트를 시작합니다. 테스트가 완료 되 면 결과는 테이블에 표시 됩니다. 상태 열에는 각 테스트에 대한 (통과/실패/경고)가 표시됩니다. 테스트 중 하나라도 실패하면 이미지가 인증되지 않습니다. 이 경우 요구 사항과 오류 메시지를 검토하고, 제안된 변경을 수행한 다음, 테스트를 다시 실행합니다.

자동화된 테스트가 완료되면 질문서 화면의 두 탭인 일반 평가 및 커널 사용자 지정에서 VM 이미지에 대한 추가 정보를 제공하고 다음을 선택합니다.

마지막 화면에서 Linux VM 이미지에 대 한 SSH 액세스 정보와 같은 추가 정보를 제공 하 고, 예외를 찾고 있는 경우 실패 한 평가에 대 한 설명을 제공할 수 있습니다.

마지막으로, 보고서 생성을 선택하여 실행된 테스트 사례에 대한 테스트 결과 및 로그 파일과 질문서에 대한 답변을 다운로드합니다. 
> [!Note]
> VM에 설치 된 방화벽과 같은 소프트웨어가 있는 경우 vm을 잠가야 하는 시나리오가 소수의 게시자에 게 있습니다. 이 경우 [인증 된 테스트 도구](https://aka.ms/AzureCertificationTestTool) 를 여기에서 다운로드 하 고 파트너 센터 [지원](https://aka.ms/marketplacepublishersupport)에서 보고서를 제출 합니다.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>PowerShell을 사용 하 여 Self-Test API를 사용 하는 방법

### <a name="on-linux-os"></a>Linux OS

PowerShell에서 API를 호출 합니다.

1. Invoke-WebRequest 명령을 사용 하 여 API를 호출 합니다.
2. 다음 코드 예제와 화면 캡처와 같이 메서드는 Post이고 콘텐츠 형식은 JSON입니다.
3. JSON 형식으로 본문 매개 변수를 지정합니다.

다음 예제에서는 API에 대 한 PowerShell 호출을 보여 줍니다.

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>PowerShell에서 API를 호출 하는 예제는 다음과 같습니다.

[![PowerShell에서 API를 호출 하기 위한 화면 예제입니다.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>앞의 예제를 사용하여 JSON을 검색하고 이를 구문 분석하여 다음 세부 정보를 얻습니다.

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>표시 되는이 샘플 화면은 `$res.Content` JSON 형식의 테스트 결과에 대 한 세부 정보를 표시 합니다.

[![테스트 결과의 세부 정보를 사용 하 여 PowerShell에서 API를 호출 하기 위한 화면 예제입니다.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>다음은 온라인 JSON 뷰어에서 볼 때 표시 되는 JSON 테스트 결과의 예입니다 (예: [Code Beautify](https://codebeautify.org/jsonviewer) 또는 [json 뷰어](https://jsonformatter.org/json-viewer)).

![온라인 JSON 뷰어에서 테스트 결과가 더 많이 있습니다.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows OS

PowerShell에서 API를 호출 합니다.

1. Invoke-WebRequest 명령을 사용 하 여 API를 호출 합니다.
2. 다음 코드 예제 및 샘플 화면에 표시 된 것 처럼 메서드는 Post이 고 콘텐츠 형식은 JSON입니다.
3. JSON 형식으로 본문 매개 변수를 만듭니다.

이 코드 샘플은 API에 대 한 PowerShell 호출을 보여 줍니다.

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

다음 샘플 화면은 PowerShell에서 API를 호출 하는 예제를 보여 줍니다.

**SSH 키 사용**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="SSH 키를 사용 하 여 PowerShell에서 API 호출":::

**암호 사용**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="PowerShell에서 암호를 사용 하 여 API 호출":::

<br>앞의 예제를 사용하여 JSON을 검색하고 이를 구문 분석하여 다음 세부 정보를 얻습니다.

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>이 화면에는 `$res.Content` JSON 형식의 테스트 결과에 대 한 세부 정보가 표시 됩니다.

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="JSON 형식의 테스트 결과에 대 한 세부 정보입니다.":::

<br>다음은 온라인 JSON 뷰어에서 표시 되는 테스트 결과의 예입니다 (예: [Code Beautify](https://codebeautify.org/jsonviewer) 또는 [JSON 뷰어](https://jsonformatter.org/json-viewer)).

![온라인 JSON 뷰어에서 테스트 결과](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Linux OS에서 Self-Test API를 사용 하기 위해 말아 넘기기를 사용 하는 방법

이 예제에서 말아 넘기기는 Azure Active Directory 및 Self-Host VM에 대 한 POST API 호출을 수행 하는 데 사용 됩니다.

1. Azure AD 토큰을 요청 하 여 자체 호스트 VM에 인증

   올바른 값이 말아 넘기기 요청에서 대체 되는지 확인 합니다.

   ```
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```
   요청의 응답 예는 다음과 같습니다.
   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. 자체 테스트 VM에 대 한 요청 제출

   전달자 토큰 및 매개 변수가 올바른 값으로 대체 되는지 확인 합니다.

   ```
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   자체 테스트 VM api 호출의 예제 응답
   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>다음 단계

- [SAS URI 가져오기](azure-vm-get-sas-uri.md)
