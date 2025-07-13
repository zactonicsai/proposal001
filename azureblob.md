# Azure CLI Commands for Blob Storage

## Storage Account Management

### Create Storage Account
```bash
# Basic storage account creation
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

# Advanced storage account with specific options
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_GRS \
    --kind StorageV2 \
    --access-tier Hot \
    --https-only true \
    --allow-blob-public-access false
```

### List Storage Accounts
```bash
# List all storage accounts in subscription
az storage account list --output table

# List storage accounts in specific resource group
az storage account list --resource-group myResourceGroup --output table

# Get specific storage account details
az storage account show --name mystorageaccount --resource-group myResourceGroup
```

### Storage Account Keys
```bash
# Get storage account keys
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup

# Regenerate storage account key
az storage account keys renew --account-name mystorageaccount --resource-group myResourceGroup --key key1

# Get connection string
az storage account show-connection-string --name mystorageaccount --resource-group myResourceGroup
```

### Update Storage Account
```bash
# Update access tier
az storage account update --name mystorageaccount --resource-group myResourceGroup --access-tier Cool

# Enable/disable public blob access
az storage account update --name mystorageaccount --resource-group myResourceGroup --allow-blob-public-access false

# Update SKU (redundancy)
az storage account update --name mystorageaccount --resource-group myResourceGroup --sku Standard_GRS
```

### Delete Storage Account
```bash
# Delete storage account
az storage account delete --name mystorageaccount --resource-group myResourceGroup --yes
```

## Container Management

### Create Container
```bash
# Create private container
az storage container create --name mycontainer --account-name mystorageaccount

# Create container with public blob access
az storage container create --name mycontainer --account-name mystorageaccount --public-access blob

# Create container with connection string
az storage container create --name mycontainer --connection-string "DefaultEndpointsProtocol=https;AccountName=..."

# Create container with SAS token
az storage container create --name mycontainer --account-name mystorageaccount --sas-token "?sv=2021..."
```

### List Containers
```bash
# List all containers
az storage container list --account-name mystorageaccount --output table

# List containers with connection string
az storage container list --connection-string "DefaultEndpointsProtocol=https;AccountName=..." --output table
```

### Container Properties
```bash
# Show container properties
az storage container show --name mycontainer --account-name mystorageaccount

# Set container metadata
az storage container metadata update --name mycontainer --account-name mystorageaccount --metadata key1=value1 key2=value2

# Show container metadata
az storage container metadata show --name mycontainer --account-name mystorageaccount
```

### Delete Container
```bash
# Delete container
az storage container delete --name mycontainer --account-name mystorageaccount

# Delete container with confirmation prompt bypass
az storage container delete --name mycontainer --account-name mystorageaccount --yes
```

## Blob Operations

### Upload Blobs
```bash
# Upload single file
az storage blob upload --file ./myfile.txt --container-name mycontainer --name myfile.txt --account-name mystorageaccount

# Upload with specific content type
az storage blob upload --file ./image.jpg --container-name mycontainer --name images/image.jpg --account-name mystorageaccount --content-type "image/jpeg"

# Upload with metadata
az storage blob upload --file ./document.pdf --container-name mycontainer --name docs/document.pdf --account-name mystorageaccount --metadata author="John Doe" department="IT"

# Upload and overwrite existing blob
az storage blob upload --file ./myfile.txt --container-name mycontainer --name myfile.txt --account-name mystorageaccount --overwrite

# Upload directory (recursive)
az storage blob upload-batch --destination mycontainer --source ./local-folder --account-name mystorageaccount

# Upload with access tier
az storage blob upload --file ./archive.zip --container-name mycontainer --name archive.zip --account-name mystorageaccount --tier Archive
```

### Download Blobs
```bash
# Download single blob
az storage blob download --container-name mycontainer --name myfile.txt --file ./downloaded-file.txt --account-name mystorageaccount

# Download blob to stdout
az storage blob download --container-name mycontainer --name myfile.txt --account-name mystorageaccount --no-progress

# Download directory (recursive)
az storage blob download-batch --source mycontainer --destination ./downloads --account-name mystorageaccount

# Download with pattern matching
az storage blob download-batch --source mycontainer --destination ./downloads --pattern "images/*" --account-name mystorageaccount
```

### List Blobs
```bash
# List all blobs in container
az storage blob list --container-name mycontainer --account-name mystorageaccount --output table

# List blobs with prefix
az storage blob list --container-name mycontainer --prefix "images/" --account-name mystorageaccount --output table

# List blobs with detailed information
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[].{Name:name, Size:properties.contentLength, LastModified:properties.lastModified, Tier:properties.accessTier}" --output table

# List only blob names
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[].name" --output tsv
```

### Blob Properties and Metadata
```bash
# Show blob properties
az storage blob show --container-name mycontainer --name myfile.txt --account-name mystorageaccount

# Update blob metadata
az storage blob metadata update --container-name mycontainer --name myfile.txt --account-name mystorageaccount --metadata version="1.2" status="active"

# Show blob metadata
az storage blob metadata show --container-name mycontainer --name myfile.txt --account-name mystorageaccount

# Set blob properties
az storage blob update --container-name mycontainer --name myfile.txt --account-name mystorageaccount --content-type "application/json"

# Set access tier
az storage blob set-tier --container-name mycontainer --name myfile.txt --account-name mystorageaccount --tier Cool
```

### Copy Blobs
```bash
# Copy blob within same storage account
az storage blob copy start --source-container mycontainer --source-blob myfile.txt --destination-container backup --destination-blob myfile-backup.txt --account-name mystorageaccount

# Copy blob from URL
az storage blob copy start --source-uri "https://otherstorage.blob.core.windows.net/container/file.txt" --destination-container mycontainer --destination-blob copied-file.txt --account-name mystorageaccount

# Copy blob with SAS token
az storage blob copy start --source-uri "https://otherstorage.blob.core.windows.net/container/file.txt?sas-token" --destination-container mycontainer --destination-blob copied-file.txt --account-name mystorageaccount

# Check copy status
az storage blob show --container-name mycontainer --name copied-file.txt --account-name mystorageaccount --query "properties.copy"
```

### Delete Blobs
```bash
# Delete single blob
az storage blob delete --container-name mycontainer --name myfile.txt --account-name mystorageaccount

# Delete blob with snapshots
az storage blob delete --container-name mycontainer --name myfile.txt --account-name mystorageaccount --delete-snapshots include

# Delete blobs with pattern
az storage blob delete-batch --source mycontainer --pattern "temp/*" --account-name mystorageaccount

# Soft delete (if enabled)
az storage blob undelete --container-name mycontainer --name myfile.txt --account-name mystorageaccount
```

## Security and Access Management

### Shared Access Signatures (SAS)
```bash
# Generate account-level SAS token
az storage account generate-sas --account-name mystorageaccount --resource-types sco --services b --permissions rwdl --expiry 2024-12-31T23:59:00Z

# Generate container-level SAS token
az storage container generate-sas --name mycontainer --account-name mystorageaccount --permissions rwdl --expiry 2024-12-31T23:59:00Z

# Generate blob-level SAS token
az storage blob generate-sas --container-name mycontainer --name myfile.txt --account-name mystorageaccount --permissions rwd --expiry 2024-12-31T23:59:00Z

# Generate SAS with IP restrictions
az storage blob generate-sas --container-name mycontainer --name myfile.txt --account-name mystorageaccount --permissions r --expiry 2024-12-31T23:59:00Z --ip "192.168.1.0/24"
```

### Access Policies
```bash
# Create stored access policy
az storage container policy create --container-name mycontainer --name mypolicy --account-name mystorageaccount --permissions rwdl --expiry 2024-12-31T23:59:00Z

# List access policies
az storage container policy list --container-name mycontainer --account-name mystorageaccount

# Update access policy
az storage container policy update --container-name mycontainer --name mypolicy --account-name mystorageaccount --permissions r

# Delete access policy
az storage container policy delete --container-name mycontainer --name mypolicy --account-name mystorageaccount
```

## Monitoring and Logging

### Enable Logging
```bash
# Enable storage analytics logging
az storage logging update --account-name mystorageaccount --services b --log rwde --retention 7

# Show logging configuration
az storage logging show --account-name mystorageaccount --services b
```

### Metrics
```bash
# Enable storage analytics metrics
az storage metrics update --account-name mystorageaccount --services b --api true --hour true --minute false --retention 7

# Show metrics configuration
az storage metrics show --account-name mystorageaccount --services b
```

## Useful Query Examples

### Advanced Listing with Filters
```bash
# List large blobs (>1MB)
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[?properties.contentLength > \`1048576\`].{Name:name, Size:properties.contentLength}" --output table

# List blobs modified in last 7 days
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[?properties.lastModified >= '$(date -d '7 days ago' -u +%Y-%m-%dT%H:%M:%SZ)'].{Name:name, Modified:properties.lastModified}" --output table

# List blobs by access tier
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[?properties.accessTier == 'Hot'].name" --output tsv
```

### Batch Operations
```bash
# Set tier for multiple blobs
az storage blob list --container-name mycontainer --account-name mystorageaccount --query "[].name" --output tsv | \
while read blob; do
    az storage blob set-tier --container-name mycontainer --name "$blob" --account-name mystorageaccount --tier Cool
done

# Download all blobs with specific extension
az storage blob download-batch --source mycontainer --destination ./downloads --pattern "*.pdf" --account-name mystorageaccount
```

## Environment Variables

```bash
# Set default storage account (to avoid repeating --account-name)
export AZURE_STORAGE_ACCOUNT=mystorageaccount

# Set storage account key
export AZURE_STORAGE_KEY=your-storage-key

# Set connection string
export AZURE_STORAGE_CONNECTION_STRING="DefaultEndpointsProtocol=https;AccountName=..."

# Now you can run commands without specifying account info
az storage container list
az storage blob list --container-name mycontainer
```

## Common Command Patterns

### Using Connection String
```bash
# Most commands support --connection-string instead of --account-name
az storage blob upload --file ./myfile.txt --container-name mycontainer --name myfile.txt --connection-string "DefaultEndpointsProtocol=https;..."
```

### Using SAS Token
```bash
# Use SAS token for authentication
az storage blob upload --file ./myfile.txt --container-name mycontainer --name myfile.txt --account-name mystorageaccount --sas-token "?sv=2021..."
```

### Output Formats
```bash
# Table format (human readable)
az storage blob list --container-name mycontainer --account-name mystorageaccount --output table

# JSON format (default)
az storage blob list --container-name mycontainer --account-name mystorageaccount --output json

# TSV format (tab-separated, good for scripting)
az storage blob list --container-name mycontainer --account-name mystorageaccount --output tsv

# YAML format
az storage blob list --container-name mycontainer --account-name mystorageaccount --output yaml
```
## Step 1: Sign in to Azure Portal
1. Go to [portal.azure.com](https://portal.azure.com)
2. Sign in with your Azure account credentials
3. If you don't have an Azure account, you'll need to create one first

## Step 2: Create a Storage Account
1. In the Azure portal, click **"Create a resource"** (+ icon) in the top-left corner
2. Search for **"Storage account"** in the marketplace
3. Select **"Storage account"** from the results
4. Click **"Create"**

## Step 3: Configure Basic Settings
Fill out the **Basics** tab:

**Project Details:**
- **Subscription**: Select your Azure subscription
- **Resource Group**: Choose existing or create new (e.g., "my-storage-rg")

**Instance Details:**
- **Storage account name**: Enter a unique name (3-24 characters, lowercase letters and numbers only)
  - Example: `mystorageaccount2024`
- **Region**: Choose your preferred location (e.g., "East US")
- **Performance**: 
  - **Standard** (for general purpose, cost-effective)
  - **Premium** (for high-performance scenarios)
- **Redundancy**: Choose based on your needs:
  - **LRS** (Locally Redundant Storage) - cheapest
  - **GRS** (Geo-Redundant Storage) - recommended for production
  - **ZRS** (Zone-Redundant Storage)
  - **GZRS** (Geo-Zone-Redundant Storage)

## Step 4: Configure Advanced Settings (Optional)
In the **Advanced** tab, you can configure:
- **Security**: Enable secure transfer, blob public access
- **Data Lake Storage Gen2**: Enable if needed for big data analytics
- **Blob storage**: Configure access tier (Hot, Cool, or Archive)

For basic blob storage, default settings are usually fine.

## Step 5: Configure Networking (Optional)
- **Public endpoint**: Allow access from all networks (default)
- **Private endpoint**: For enhanced security (advanced)

## Step 6: Configure Data Protection (Optional)
- **Recovery**: Enable soft delete for blobs and containers
- **Tracking**: Enable versioning and change feed if needed

## Step 7: Configure Encryption (Optional)
- Default encryption settings are usually sufficient
- You can configure customer-managed keys if required

## Step 8: Add Tags (Optional)
- Add tags for organization and billing purposes
- Example: Environment=Dev, Project=WebApp

## Step 9: Review and Create
1. Click **"Review + create"**
2. Review all your settings
3. Click **"Create"**
4. Wait for deployment to complete (usually 1-2 minutes)

## Step 10: Create a Container (Blob Container)
After your storage account is created:

1. Go to your new storage account
2. In the left menu, under **Data storage**, click **"Containers"**
3. Click **"+ Container"** at the top
4. Configure container settings:
   - **Name**: Enter container name (lowercase, no spaces)
     - Example: `images`, `documents`, `uploads`
   - **Public access level**:
     - **Private** (no anonymous access) - recommended
     - **Blob** (anonymous read access for blobs only)
     - **Container** (anonymous read access for containers and blobs)
5. Click **"Create"**

## Step 11: Upload Your First Blob
1. Click on your newly created container
2. Click **"Upload"** button
3. Select files from your computer
4. Optionally configure:
   - **Blob type**: Block blob (default for most files)
   - **Block size**: Leave default
   - **Access tier**: Hot, Cool, or Archive
5. Click **"Upload"**

## Step 12: Get Connection Information
To use your blob storage programmatically:

1. Go to your storage account
2. In the left menu, click **"Access keys"**
3. Copy either:
   - **Connection string** (easiest for applications)
   - **Storage account name** and **Key** (for REST API)

## Step 13: Configure Access (Optional)
For programmatic access, you can also create:

**Shared Access Signature (SAS):**
1. Go to **"Shared access signature"** in the left menu
2. Configure permissions and expiration
3. Generate SAS token

**Access Policies:**
1. Go to your container
2. Click **"Access policy"**
3. Add stored access policies for better management

## Verification
You now have:
- ✅ Azure Storage Account
- ✅ Blob Container
- ✅ Access credentials
- ✅ First blob uploaded (optional)

Your blob storage is ready to use! You can now access it via:
- Azure portal (manual management)
- REST API (programmatic access)
- Azure Storage SDKs (various programming languages)
- Azure Storage Explorer (desktop application)

The URL format for your blobs will be:
```
https://{your-storage-account}.blob.core.windows.net/{container-name}/{blob-name}
```

Here's a detailed explanation of each Azure Blob Storage option and when to use them:

## Performance Tiers

### **Standard Performance**
- **What**: Uses traditional hard drives (HDD)
- **When to use**: 
  - General-purpose applications
  - File storage, backups, archives
  - Web applications with moderate traffic
  - Cost-sensitive projects
- **Why**: Much cheaper, sufficient for most use cases
- **IOPS**: Up to 20,000 IOPS per storage account

### **Premium Performance** 
- **What**: Uses solid-state drives (SSD)
- **When to use**:
  - High-performance applications
  - Real-time analytics
  - Gaming, video streaming
  - Low-latency requirements
  - High transaction volumes
- **Why**: Faster response times, higher throughput
- **IOPS**: Up to 100,000+ IOPS per storage account
- **Cost**: 3-4x more expensive than Standard

## Redundancy Options

### **LRS (Locally Redundant Storage)**
- **What**: 3 copies within a single data center
- **When to use**:
  - Development/testing environments
  - Non-critical data that can be recreated
  - Tight budget constraints
  - Data sovereignty requirements (stays in one location)
- **Why**: Cheapest option
- **Durability**: 99.999999999% (11 nines)
- **Risk**: Vulnerable to data center failures

### **ZRS (Zone-Redundant Storage)**
- **What**: 3 copies across 3 availability zones in same region
- **When to use**:
  - Production applications
  - Need high availability within a region
  - Regulatory requirements for zone redundancy
- **Why**: Protection against zone failures
- **Durability**: 99.9999999999% (12 nines)
- **Cost**: ~25% more than LRS

### **GRS (Geo-Redundant Storage)**
- **What**: 6 copies total - 3 in primary region + 3 in secondary region
- **When to use**:
  - Business-critical data
  - Disaster recovery requirements
  - Compliance mandates for geographic backup
  - Can't afford regional outages
- **Why**: Protection against regional disasters
- **Durability**: 99.99999999999999% (16 nines)
- **Cost**: ~2x more than LRS

### **GZRS (Geo-Zone-Redundant Storage)**
- **What**: Combines ZRS and GRS - zone redundancy in primary + geo-redundancy
- **When to use**:
  - Mission-critical applications
  - Maximum durability requirements
  - Enterprise applications with strict SLAs
- **Why**: Highest level of protection
- **Cost**: Most expensive option

## Access Tiers (Blob Level)

### **Hot Tier**
- **What**: Optimized for frequent access
- **When to use**:
  - Active websites, web apps
  - Files accessed daily/weekly
  - Recently uploaded content
  - Working datasets
- **Why**: Lowest access costs, higher storage costs
- **Cost**: Highest storage cost, lowest access cost
- **Use case**: User profile pictures, active documents

### **Cool Tier**
- **What**: Optimized for infrequent access (monthly)
- **When to use**:
  - Short-term backups
  - Disaster recovery data
  - Older content accessed occasionally
  - Data kept for 30+ days
- **Why**: Lower storage costs than Hot, higher access costs
- **Cost**: 50% cheaper storage than Hot
- **Minimum**: 30-day storage commitment

### **Archive Tier**
- **What**: Optimized for rarely accessed data
- **When to use**:
  - Long-term backups
  - Compliance/regulatory data
  - Historical data
  - Data kept for years
- **Why**: Cheapest storage, expensive to access
- **Cost**: 80% cheaper than Hot tier
- **Retrieval**: Takes hours to access (rehydration required)
- **Minimum**: 180-day storage commitment

## Container Public Access Levels

### **Private (No Anonymous Access)**
- **What**: Requires authentication for all access
- **When to use**:
  - Confidential business data
  - User personal files
  - Proprietary content
  - Default recommendation for security
- **Why**: Maximum security control
- **Access**: Only via storage keys, SAS tokens, or Azure AD

### **Blob (Anonymous Read Access for Blobs Only)**
- **What**: Individual blobs can be accessed publicly if URL is known
- **When to use**:
  - Public website assets (CSS, JS, images)
  - Content delivery networks (CDN)
  - Public downloads
- **Why**: Allows direct linking to files
- **Security**: Container contents not listable, but blobs accessible

### **Container (Anonymous Read Access for Container and Blobs)**
- **What**: Anyone can list and download all container contents
- **When to use**:
  - Fully public data repositories
  - Open-source file sharing
  - Public datasets
- **Why**: Maximum accessibility
- **Risk**: All files are publicly discoverable

## Blob Types

### **Block Blobs**
- **What**: Optimized for uploading large amounts of data efficiently
- **When to use**:
  - Text and binary files
  - Documents, images, videos
  - Application files
  - 99% of typical use cases
- **Why**: Most versatile and commonly used
- **Size**: Up to 190.7 TB per blob
- **Features**: Support for parallel uploads, versioning

### **Append Blobs**
- **What**: Optimized for append operations
- **When to use**:
  - Log files
  - Audit trails
  - Data streaming scenarios
  - Write-once, read-many scenarios
- **Why**: Efficient for continuously growing files
- **Size**: Up to 195 GB per blob
- **Limitation**: Can only append, not modify existing content

### **Page Blobs**
- **What**: Optimized for random read/write operations
- **When to use**:
  - Virtual machine hard disks (VHDs)
  - Database files
  - Applications requiring random access
- **Why**: Supports efficient random I/O operations
- **Size**: Up to 8 TB per blob
- **Use case**: Primarily for Azure VMs

## Security Options

### **Secure Transfer Required**
- **When to enable**: Always (recommended)
- **Why**: Forces HTTPS/encrypted connections
- **Use case**: Any production environment

### **Blob Public Access**
- **When to disable**: For sensitive data storage accounts
- **When to enable**: For content delivery/public assets
- **Why**: Prevents accidental public exposure

### **Minimum TLS Version**
- **Recommendation**: Use TLS 1.2 minimum
- **Why**: Older TLS versions have security vulnerabilities

## Decision Matrix Summary

| Use Case | Performance | Redundancy | Access Tier | Container Access |
|----------|-------------|------------|-------------|------------------|
| Web App Assets | Standard | LRS/ZRS | Hot | Blob |
| Business Documents | Standard | GRS | Cool | Private |
| Long-term Backups | Standard | GRS | Archive | Private |
| Gaming/Real-time | Premium | ZRS/GZRS | Hot | Private |
| Development/Testing | Standard | LRS | Hot | Private |
| Compliance Data | Standard | GZRS | Archive | Private |
| CDN Content | Standard | GRS | Hot | Blob |
| Log Files | Standard | ZRS | Cool | Private |

The key is matching your requirements for **cost**, **performance**, **durability**, and **security** with the appropriate tier combinations.
