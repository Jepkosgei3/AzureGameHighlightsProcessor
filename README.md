# Azure Game Highlight Processor: A Complete Guide to Fetching and Storing NCAA Highlights

**Overview**

This blog post walks you through the **Azure Game Highlight Processor**, a project designed to fetch NCAA basketball highlights from a RapidAPI sports API, store the metadata in Azure Blob Storage, and then download the associated video files into the same storage. By the end of this post, you'll have an understanding of how the pipeline works and how to set it up on your local machine or deploy it to the cloud.

---

### **Project Overview and Workflow**

The project processes basketball highlights in three main stages:

1. **Fetch Highlights**: The first part of the pipeline fetches NCAA basketball highlights using the **RapidAPI Sports API**.
2. **Store in Azure Blob Storage**: It stores the fetched highlights as a JSON file in **Azure Blob Storage**.
3. **Process and Store Videos**: Finally, it processes the highlights by extracting video URLs, downloading the videos, and uploading them back to Blob Storage.

The following scripts handle these stages:

- **`create_storage_account.py`**: Creates a new Azure storage account and updates the configuration with credentials.
- **`fetch.py`**: Fetches highlight data from the RapidAPI service and saves it to Blob Storage.
- **`process_one_video.py`**: Extracts video URLs from the JSON file, downloads the videos, and uploads them back to Blob Storage.
- **`run_all.py`**: Orchestrates the entire pipeline and handles retries between each stage.

---

### **Tech Stack**

- **Azure Blob Storage**: Stores both highlight metadata (in JSON format) and processed video files.
- **RapidAPI Sports API**: Fetches basketball highlight data including video URLs.
- **Python**: The programming language used for writing the scripts.
- **Docker**: Containerizes the application to allow for easy deployment across environments.
- **Azure SDKs**: Used to interact with Azure services like Blob Storage and resource management.
- **Azure CLI**: Used for provisioning and managing resources from the command line.

---

### **Project Structure**

Here's the structure of the project:

```
src/
├── Dockerfile
├── config.py
├── fetch.py
├── create_storage_account.py
├── process_one_video.py
├── requirements.txt
├── run_all.py
├── .env
├── .gitignore
```

- **`Dockerfile`**: Containerizes the Python environment for easy deployment and scalability.
- **`config.py`**: Contains configuration settings, including environment variables (e.g., API keys, Azure credentials).
- **`requirements.txt`**: Lists the Python dependencies required to run the scripts.
- **`run_all.py`**: Orchestrates the entire workflow by running the steps in sequence with retry logic.
- **`.env`**: Stores sensitive environment variables like API keys, Azure credentials, and other configuration values.

---

### **Setting Up the Project Locally**

#### **Step 1: Clone the Repository**

Start by cloning the repository to your local machine:

```bash
git clone https://github.com/Jepkosgei3/AzureGameHighlightsProcessor.git
cd AzureGameHighlightProcessor/src
```

#### **Step 2: Install Dependencies**

You'll need to set up a Python environment and install the required dependencies. If you don't have a virtual environment, create one and activate it:

**macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

**Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

Now, install the required packages:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

#### **Step 3: Configure `.env` File**

The `.env` file contains sensitive information like your API keys and Azure credentials. Open the `.env` file and update the following values:

1. **`RAPIDAPI_KEY`**: Your RapidAPI key (sign up at [RapidAPI](https://rapidapi.com) if you don't have one).
2. **`AZURE_SUBSCRIPTION_ID`**: Your Azure subscription ID.
3. **`AZURE_RESOURCE_GROUP`**: The Azure resource group where your storage account will be created.
4. **`AZURE_BLOB_CONTAINER_NAME`**: The name of the Azure Blob container where the data will be stored.

```bash
RAPIDAPI_KEY=your-rapidapi-key
AZURE_SUBSCRIPTION_ID=your-azure-subscription-id
AZURE_RESOURCE_GROUP=your-resource-group
AZURE_BLOB_CONTAINER_NAME=your-blob-container-name
```

Secure the `.env` file to protect sensitive data:

```bash
chmod 600 .env
```

#### **Step 4: Run the Pipeline**

Once your environment is set up and your `.env` file is configured, run the entire pipeline:

```bash
python run_all.py
```

This will trigger the following sequence of operations:

1. **Create Storage Account**: If the storage account doesn't exist, it will be created. This is done by `create_storage_account.py`.
2. **Fetch Highlights**: `fetch.py` fetches the basketball highlights from RapidAPI and stores the JSON file in Azure Blob Storage.
3. **Process Video**: `process_one_video.py` will download the video from the JSON data, process it, and upload it to Azure Blob Storage.

---

### **Deployment with Docker**

For deployment in any environment, you can easily containerize the project using Docker. The `Dockerfile` provides the necessary steps to build a Docker image for the project:

1. **Build the Docker Image**:

```bash
docker build -t azure-game-highlight-processor .
```

2. **Run the Docker Container**:

```bash
docker run -it --env-file .env azure-game-highlight-processor
```

This will run the entire pipeline inside the container.

---

### **Understanding the Pipeline**

Here’s how the components of the pipeline interact:

1. **`create_storage_account.py`**: 
   - This script ensures that the Azure storage account and container are created. It uses **Azure Resource Management API** to create the storage account dynamically if it doesn't exist.

2. **`fetch.py`**:
   - This script interacts with **RapidAPI's Sports API** to fetch NCAA basketball highlights. It then saves the JSON data containing highlight information (including video URLs) into **Azure Blob Storage**.

3. **`process_one_video.py`**:
   - This script reads the JSON file from Blob Storage, extracts the video URL, downloads the video, and uploads it back to Azure Blob Storage for storage and further processing.

4. **`run_all.py`**:
   - This orchestrator script runs the entire pipeline in sequence, ensuring each step is executed successfully. It includes retry logic to handle failures.

---

### **Using Azure Blob Storage**

The data pipeline leverages **Azure Blob Storage** for both the highlight JSON and video files. Blob Storage provides highly scalable, durable, and low-cost storage, making it an ideal choice for storing both structured (JSON) and unstructured (video) data.

**Key operations:**
- **Uploading JSON**: After fetching the highlight data, the `fetch.py` script uploads the JSON to Blob Storage using the `azure-storage-blob` SDK.
- **Downloading Videos**: The `process_one_video.py` script downloads videos from external URLs and stores them in the Blob container for easy access.

---

### **Future Enhancements**

1. **Scalability**: Currently, the project processes a single video. We can extend this by processing multiple videos at once and saving them in different folders within the Blob container.
2. **Cloud Deployment**: The pipeline can be deployed to Azure VM or Azure Functions to automate the process on a schedule.
3. **Security**: Replace the Storage Account key with **Managed Identity** for a more secure and scalable solution.
4. **Enhanced Video Processing**: Integrate **Azure Media Services** to enhance video quality before storing it in the Blob Storage.

---

Happy coding! Feel free to ask any questions in the comments below or check out the [GitHub repository](https://github.com/Jepkosgei3/AzureGameHighlightsProcessor.git) for more details.

