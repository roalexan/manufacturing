# Upload sample data (optional)

DMM comes with sample data, but it is not pre-loaded. Below are the steps to load the sample data if you want it.

## Grant network access to batch storage account

Users need to have network access to the batch storage account in order to have access to it. This is where the sample data can be downloaded, unzipped, and then uploaded for batch ingestion. Below is an example of granting network access to the batch storage account for yourself.

### Azure portal

1. browse: [portal.azure.com](https://portal.azure.com/)
1. click: resource groups
1. search: DMM_RESOURCE_GROUP_NAME
1. click: storage account: sabatch*
1. click: Networking
1. check: Enabled from selected virtual networks and IP addresses
1. check: Add your client IP address
1. click: Save

# Download the sample data

You will need to download the sample data from the batch storage account and unzip it. Below is an example of how to do this.

## Azure portal

1. browse: [portal.azure.com](https://portal.azure.com/)
1. click: resource groups
1. search: DMM_RESOURCE_GROUP_NAME
1. click: storage account: sabatch*
1. click: container: sample
1. click: Download
1. unzip the file SampleData.zip locally

# Upload the sample data

You will need to upload the sample data to the batch storage account. Below is an example of how to do this.

## Azure portal

1. browse: [portal.azure.com](https://portal.azure.com/)
1. click: resource groups
1. search: DMM_RESOURCE_GROUP_NAME
1. click: storage account: sabatch*
1. click: container: requests
1. click: Upload
1. upload all the CSV files in the SampleData\CopilotData folder

> [!NOTE]
> It takes about 5 minutes after loading for the data to be ready for querying.