# Migrate existing data using Azure Data Factory

## Lab scenario

In Azure Data Factory, Azure Cosmos DB is supported as a source of data ingest and as a target (sink) of data output.
In this lab, we will populate Azure Cosmos DB using a helpful command-line utility and then use Azure Data Factory to move a subset of data from one container to another.

## Lab objectives

In this lab, you will complete the following tasks:
- Task 1: Create and seed your Azure Cosmos DB SQL API account.
- Task 2: Create Azure Data Factory resource.

### Estimated Timing: 30 minutes

### Task 1: Create and seed your Azure Cosmos DB for NoSQL account

You will use a command-line utility that creates a **cosmicworks** database and a **products** container at **4,000** request units per second (RU/s). Once created, you will adjust the throughput down to 400 RU/s.

To accompany the products container, you will create a **flatproducts** container manually that will be the target of the ETL transformation and load operation at the end of this lab.

1. Inside the LabVM, double click on the **Azure Portal** shortcut.

    ![](media/azureportal.png)

1. On the **Sign-in into Microsoft Azure** tab you will see the login screen, in that enter the following email/username and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   
     ![04](media/04.png)
     
1. Now enter the following password and click on **Sign in**.
   * Password: <inject key="AzureAdUserPassword"></inject>
   
     ![05](media/05.png)
     
        >**Note:** If you see the **Help us protect your account** dialog box, then select the **Skip for now** option.

        ![06](media/06.png)
  
1. If you see the pop-up **Stay Signed in?**, click No

1. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.

1. If a **Welcome to Microsoft Azure** popup window appears, click **Maybe Later** to skip the tour.

1. Select **+ Create a resource**, search for *Cosmos DB*, select **Azure Cosmos DB**.

1. Select **create** under **Azure Cosmos DB for NoSQL**. Then create a new **Azure Cosmos DB for NoSQL** account resource with the following settings, leaving all remaining settings to their default values, and select **Review + create**:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Subscription** | *Your existing Azure subscription* |
    | **Resource group** | *Select an existing Cosmosdb-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Account Name** | *sql-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Location** | *Choose any available region* |
    | **Capacity mode** | *Provisioned throughput* |
    | **Apply Free Tier Discount** | *Do Not Apply* |
    | **Limit the total amount of throughput that can be provisioned on this account** | *Unchecked* |

1. Wait for the deployment task to complete before continuing with this task.

1. Select **Go to resources**. On the newly created **Azure Cosmos DB** account under **Settings** navigate to the **Keys** pane.

1. This pane contains the connection details and credentials necessary to connect to the account from the SDK. Specifically:

    1. Notice the **URI** field. You will use this **endpoint** value later in this exercise.

    1. Notice the **PRIMARY KEY** field. You will use this **key** value later in this exercise.

1. Keep the browser tab open, as we will return to it later.

1. On the LabVM select the **Visual Studio Code** shortcut.

    ![06](media/visualstudio.png)

1. In **Visual Studio Code**, open the **Terminal** menu by selecting **... (ellipses) (1)** > **Terminal (2)** > **New Terminal (3)** to open a new terminal with your existing instance.

    ![06](media/terminal.png)

1. Install the [cosmicworks][nuget.org/packages/cosmicworks] command-line tool for global use on your machine.

    ```
    dotnet tool install cosmicworks --global --version 1.*
    ```

    > **Note:** This command may take a couple of minutes to complete. This command will output the warning message (*Tool 'cosmicworks' is already installed') if you have already installed the latest version of this tool in the past.

1. Once the Installation is completed, make sure to close the **Visual Studio Code** and re-open to perform the below command.

1. Run cosmicworks to seed your Azure Cosmos DB account with the following command-line options:

    | **Option** | **Value** |
    | --- | --- |
    | **--endpoint** | *The endpoint value you copied earlier in this lab* |
    | **--key** | *The key value you coped earlier in this lab* |
    | **--datasets** | *product* |

    ```
    cosmicworks --endpoint <cosmos-endpoint> --key <cosmos-key> --datasets product
    ```

    > &#128221; For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/** and your key is: **fDR2ci9QgkdkvERTQ==**, then the command would be:
    > ``cosmicworks --endpoint https://dp420.documents.azure.com:443/ --key fDR2ci9QgkdkvERTQ== --datasets product``
    > **Note:** If you get an error while running the above command, close the visual studio code and re-open to run the above command.

1. Wait for the **cosmicworks** command to finish populating the account with a database, container, and items.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

1. Navigate to the **Azure portal**.

1. Select **Resource groups**, then select the resource group you created or viewed earlier in this lab, and then select the **Cosmosdb-<inject key="DeploymentID" enableCopy="false"/>** Azure Cosmos DB account resource you created in this lab.

1. Within the **Azure Cosmos DB** account resource, navigate to the **Data Explorer** pane.

1. In the **Data Explorer**, expand the **cosmicworks** database node, expand the **products** container node, and then select **Items**.

1. Observe and select the various JSON items in the **products** container. These are the items created by the command-line tool used in previous steps.

1. Select the **Scale & Settings** node. In the **Scale & Settings** tab, select **Manual**, update the **required throughput** setting from **4000 RU/s** to **400 RU/s** and then **Save** your changes**.

   ![06](media/scale1.png)

1. In the **Data Explorer** pane, select **New Container**.

   ![06](media/scale2.png)

1. In the **New Container** popup, enter the following values for each setting, and then select **OK**:

    | **Setting** | **Value** |
    | --- | --- |
    | **Database id** | *Use existing* &vert; *cosmicworks* |
    | **Container id** | *`flatproducts`* |
    | **Partition key** | *`/category`* |
    | **Container throughput (autoscale)** | *Manual* |
    | **RU/s** | *`400`* |

1. Back in the **Data Explorer** pane, expand the **cosmicworks** database node and then observe the **flatproducts** container node within the hierarchy.

1. Return to the **Home** of the Azure portal.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
    > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help.

    <validation step="4f0ebcc4-a71c-450a-b7e0-5099feed58d5" />

### Task 2: Create Azure Data Factory resource

Now that the Azure Cosmos DB for NoSQL resources are in place, you will create an Azure Data Factory resource and configure all of the necessary components and connections to perform a one-time data movement from one NoSQL API container to another to extract data, transform it, and load it to another NoSQL API container

1. Select **+ Create a resource**, search for *Data Factory*, and then create a new **Azure Data Factory** resource with the following settings, leaving all remaining settings to their default values:

    | **Setting** | **Value** |
    | --- | --- |
    | **Subscription** | *Your existing Azure subscription* |
    | **Resource group** | *Cosmosdb-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Name** | *datafactory-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Region** | *Choose any available region* |
    | **Version** | *V2* |
   
1. Click on **Next: Git configuration**. In **Git configuration** blade tick *Configure Git later*

1. Select **Review + Create** to navigate to the **Review + Create** tab, and then select **Create**.

1. Go to the newly created **Azure Data Factory** resource and select **Open Azure Data Factory Studio**.

   ![06](media/scale3.png)

   > &#128161; Alternatively, you can navigate to (``adf.azure.com/home``), select your newly created Data Factory resource, and then select the home icon.

1. From the **Home** screen. Select the **Ingest** option to begin the quick wizard to perform a one-time copy data at scale operation and move to the **Properties** step of the wizard.

1. Starting with the **Properties** step of the wizard, in the **Task type** section, select **Built-in copy task**.

1. In the **Task cadence or task schedule** section, select **Run once now** and then select **Next** to move to the **Source** step of the wizard.

1. In the **Source** step of the wizard, in the **Source type** list, select **Azure Cosmos DB NoSQL**.

1. In the **Connection** section, select **+ New connection**.

1. In the **New connection (Azure Cosmos DB for NoSQL)** popup, configure the new connection with the following values, and then select **Create**:

    | **Setting** | **Value** |
    | --- | --- |
    | **Name** | *`CosmosSqlConn`* |
    | **Connect via integration runtime** | *AutoResolveIntegrationRuntime* |
    | **Authentication method** | *Account key* &vert; *Connection string* |
    | **Account selection method** | *From Azure subscription* |
    | **Azure subscription** | *Your existing Azure subscription* |
    | **Azure Cosmos DB account name** | *Your existing Azure Cosmos DB account name you chose earlier in this lab* |
    | **Database name** | *cosmicworks* |

1. Back in the **Source data store** section, within the **Source tables** section, select **Query**.

1. In the **Table name** list, select **products**.

1. In the **Query** editor, delete the existing content and enter the following query:

    ```
    SELECT 
        p.name, 
        p.categoryName as category, 
        p.price 
    FROM 
        products p
    ```

1. Select **Preview data** to test the query's validity. Select **Next** to move to the **Destination** step of the wizard.

1. In the **Destination** step of the wizard, in the **Destination type** list, select **Azure Cosmos DB for NoSQL**.

1. In the **Connection** list, select **CosmosSqlConn**.

1. In the **Destination** list, select **flatproducts** and then select **Next** to move to the **Settings** step of the wizard.

1. In the **Settings** step of the wizard, in the **Task name** field, enter **`FlattenAndMoveData`**.

1. Leave all remaining fields to their default blank values and then select **Next** to move to the final step of the wizard.

1. Review the **Summary** of the steps you have selected in the wizard and then select **Next**.

1. Observe the various steps in the deployment. When the deployment has finished, select **Finish**.

1. Close your web browser window or tab.

1. Navigate to the **Azure portal**.

1. Select **Resource groups**, then select the resource group you created or viewed earlier in this lab, and then select the **Cosmosdb-<inject key="DeploymentID" enableCopy="false"/>** Azure Cosmos DB account resource you created in this lab.

1. Within the **Azure Cosmos DB** account resource, navigate to the **Data Explorer** pane.

1. In the **Data Explorer**, expand the **cosmicworks** database node, select the **flatproducts** container node, and then select **New SQL Query**.

1. Delete the contents of the editor area.

1. Create a new SQL query that will return all documents where the **name** is equivalent to **HL Headset**:

    ```
    SELECT 
        p.name, 
        p.category, 
        p.price 
    FROM
        products p
    WHERE
        p.name = 'HL Headset'
    ```

1. Select **Execute Query**.

1. Observe the results of the query.

1. Close your web browser window or tab.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
    > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help.

   <validation step="e513aa34-ca14-4de5-a2f3-139f051e5c35" />
   
### Review

In this lab, you have completed:

- Created and seeded your Azure Cosmos DB NoSQL API account.
- Created Azure Data Factory resource.

### You have successfully completed the lab.
