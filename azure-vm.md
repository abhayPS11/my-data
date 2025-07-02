---
title: Convert AArch64 ISO to VHD and Deploy on Azure VM
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---


### Convert AArch64 ISO to VHD and Deploy Azure Linux Arm64 VM

This guide walks through the steps to convert the **Azure Linux 3.0 AArch64 ISO** into a **VHD**, upload it to Azure, and deploy a custom **Linux Arm64 virtual machine** using Azure Shared Image Gallery.

---

**Step A: Download and Prepare the Raw Disk Image**

**Download Azure Linux ISO (AArch64)**
```bash
wget https://aka.ms/azurelinux-3.0-aarch64.iso
```
**2. Create a 32 GB raw disk image**
```bash
qemu-img create -f raw azurelinux-arm64.raw 34359738368
```
Creates a 32 GB empty raw disk image to install the OS.

**Step B: Install Azure Linux Using QEMU**

**3. Boot the ISO and install the OS to the raw image**
```bash
qemu-system-aarch64 \
  -machine virt \
  -cpu cortex-a72 \
  -m 4096 \
  -nographic \
  -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd \
  -drive if=none,file=azurelinux-arm64.raw,format=raw,id=hd0 \
  -device virtio-blk-device,drive=hd0 \
  -cdrom azurelinux-3.0-aarch64.iso \
  -netdev user,id=net0 \
  -device virtio-net-device,netdev=net0
```
Boots the Azure Linux ISO on an emulated ARM64 VM and installs the OS onto the raw disk.

**4. Inside the VM — Install Azure Agent and Shut Down**
```bash
sudo dnf install WALinuxAgent -y
sudo systemctl enable waagent
sudo systemctl start waagent
sudo poweroff
```
Installs and enables the Azure Linux Agent required for VM provisioning.

**Step C: Convert Raw Disk to VHD Format**

**5. Convert the raw disk image to fixed-size VHD**
```bash
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc azurelinux-arm64.raw azurelinux-arm64.vhd
```
Converts the raw disk image to a fixed-size VHD format compatible with Azure.

**Step D: Upload VHD and Create Azure Image Gallery**

**Set environment variables:**

```bash
RESOURCE_GROUP="MyCustomARM64Group"
LOCATION="centralindia"
STORAGE_ACCOUNT="mycustomarm64storage"
CONTAINER_NAME="mycustomarm64container"
VHD_NAME="azurelinux-arm64.vhd"
GALLERY_NAME="MyCustomARM64Gallery"
IMAGE_DEF_NAME="MyAzureLinuxARM64Def"
IMAGE_VERSION="1.0.0"
PUBLISHER="custom"
OFFER="custom-offer"
SKU="custom-sku"
OS_TYPE="Linux"
ARCHITECTURE="Arm64"
HYPERV_GEN="V2"
STORAGE_ACCOUNT_TYPE="Standard_LRS"
VM_NAME="MyAzureLinuxARMVM"
ADMIN_USER="azureuser"
VM_SIZE="Standard_D4ps_v6"
```
{{% notice Note %}}
You can modify the values of these environment variables—such as RESOURCE_GROUP, VM_NAME, LOCATION, and others—based on your naming preferences, region, and resource requirements.
{{% /notice %}}

**6. Create Resource Group and Storage Account**
```bash
az group create --name "$RESOURCE_GROUP" --location "$LOCATION"
```
```bash
az storage account create \
  --name "$STORAGE_ACCOUNT" \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --sku Standard_LRS \
  --kind StorageV2
```
Creates a resource group and a general-purpose storage account.

**7. Create Blob Container and Upload VHD**
```bash
az storage container create \
  --name "$CONTAINER_NAME" \
  --account-name "$STORAGE_ACCOUNT"

az storage blob upload \
  --account-name "$STORAGE_ACCOUNT" \
  --container-name "$CONTAINER_NAME" \
  --name "$VHD_NAME" \
  --file ./azurelinux-arm64.vhd
```
Uploads the VHD to your storage container.

**Step E: Create Azure Shared Image Gallery Resources**

**8. Create Shared Image Gallery and Image Definition**
```bash

az sig create \
  --resource-group "$RESOURCE_GROUP" \
  --gallery-name "$GALLERY_NAME" \
  --location "$LOCATION"

az sig image-definition create \
  --resource-group "$RESOURCE_GROUP" \
  --gallery-name "$GALLERY_NAME" \
  --gallery-image-definition "$IMAGE_DEF_NAME" \
  --publisher "$PUBLISHER" \
  --offer "$OFFER" \
  --sku "$SKU" \
  --os-type "$OS_TYPE" \
  --architecture "$ARCHITECTURE" \
  --hyper-v-generation "$HYPERV_GEN"
```
Sets up an image gallery and defines the custom Arm64 Linux image.

**9. Create Image Version Using Uploaded VHD**
```bash

az sig image-version create \
  --resource-group "$RESOURCE_GROUP" \
  --gallery-name "$GALLERY_NAME" \
  --gallery-image-definition "$IMAGE_DEF_NAME" \
  --gallery-image-version "$IMAGE_VERSION" \
  --location "$LOCATION" \
  --os-vhd-uri "https://${STORAGE_ACCOUNT}.blob.core.windows.net/${CONTAINER_NAME}/${VHD_NAME}" \
  --os-vhd-storage-account "$STORAGE_ACCOUNT" \
  --storage-account-type "$STORAGE_ACCOUNT_TYPE"
```
Registers the VHD as a version of your custom image.

**Step F: Deploy Azure Linux Arm64 VM**

**10. (Optional) Retrieve the Image ID**
```bash

IMAGE_ID=$(az sig image-version show \
  --resource-group "$RESOURCE_GROUP" \
  --gallery-name "$GALLERY_NAME" \
  --gallery-image-definition "$IMAGE_DEF_NAME" \
  --gallery-image-version "$IMAGE_VERSION" \
  --query "id" -o tsv)
```
Retrieves the unique ID for use in VM creation.

**11. Create the VM Using the Custom Image**
```bash

az vm create \
  --resource-group "$RESOURCE_GROUP" \
  --name "$VM_NAME" \
  --image "$IMAGE_ID" \
  --size "$VM_SIZE" \
  --admin-username "$ADMIN_USER" \
  --generate-ssh-keys \
  --public-ip-sku Standard
```
Deploys a Linux Arm64 VM from your custom image.

**Step G: Access the Created Azure Linux Arm64 VM**

After successfully creating the VM, you can access it using SSH.

**12. Get the Public IP of the VM**
```bash
az vm show \
  --resource-group "$RESOURCE_GROUP" \
  --name "$VM_NAME" \
  --show-details \
  --query "publicIps" \
  -o tsv
```

Retrieves the public IP address of the newly created VM.

**13. Connect via SSH**

```bash
ssh azureuser@<public-ip-address>
```

Replace **public-ip-address** with the IP returned in the previous command.
This uses the SSH key that was automatically generated during VM creation (or your existing one, if specified).

You can now log into your custom Azure Linux Arm64 VM and start using it!
