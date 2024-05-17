# Connect to Azure Cosmos DB for NoSQL with the SDK

## Lab scenario

The Azure SDK for .NET is a suite of libraries that provides a consistent developer interface to interact with many Azure services. The Azure SDK for .NET is built to the .NET Standard 2.0 specification ensuring that it can be used in .NET Framework (4.6.1 or above), .NET Core (2.1 or above), and .NET (5 or above) applications. <br>
In this lab, you'll connect to an Azure Cosmos DB SQL API account using the Azure SDK for .NET.

## Lab objectives

In this lab, you will complete the following tasks:
- Task 1: Prepare your development environment.
- Task 2: Create an Azure Cosmos DB SQL API account.
- Task 3: View the Microsoft.Azure.Cosmos library on NuGet.
- Task 4: Import the Microsoft.Azure.Cosmos library into a .NET project.
- Task 5: Use the Microsoft.Azure.Cosmos library.
- Task 6: Test the script.

## Estimated Timing: 30 minutes

### Task 1: Prepare your development environment

1. Start Visual Studio Code from the desktop.

     ![Visual Studio Code Icon](./media/vscode1.jpg)

2. Select the **Extensions** blade from the left panel.

3. Search with **C#** and select **Install** to install the extension.

    ![](./media/Csharp.png)

3. Select the **file** option on the top left of the screen, from the pane options, select **Open Folder** and navigate to **C:\AllFiles/dp-420-cosmos-db-dev**.

4. Select the folder **04-sdk-connect** and click on **Select Folder**.

### Task 2: Create an Azure Cosmos DB SQL API account

Azure Cosmos DB is a cloud-based NoSQL database service that supports multiple APIs. When provisioning an Azure Cosmos DB account for the first time, you will select which of the APIs you want the account to support (for example, **Mongo API** or **NoSQL API**). Once the Azure Cosmos DB SQL API account is done provisioning, you can retrieve the endpoint and key and use them to connect to the Azure Cosmos DB NoSQL API account using the Azure SDK for .NET or any other SDK of your choice.

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
    | --- | --- |
    | **Subscription** | *Your existing Azure subscription* |
    | **Resource group** | *Cosmosdb-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Account Name** | *sql-<inject key="DeploymentID" enableCopy="false"/>* |
    | **Location** | *Choose any available region* |
    | **Capacity mode** | *Provisioned throughput* |
    | **Apply Free Tier Discount** | *Do Not Apply* |
    | **Limit the total amount of throughput that can be provisioned on this account** | *Unchecked* |
   
1. Click on **Review+ Create** and after validation click on **Create**.

1. Wait for the deployment task to complete before continuing with this task.

1. Select **Go to resources**. On the newly created **Azure Cosmos DB** account under **Settings** navigate to the **Keys** pane.

1. This pane contains the connection details and credentials necessary to connect to the account from the SDK. Specifically:

    1. Record the value of the **URI** field. You will use this **endpoint** value later in this exercise.

    1. Record the value of the **PRIMARY KEY** field. You will use this **key** value later in this exercise.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Click the Lab Validation tab located at the upper right corner of the lab guide section and navigate to the Lab Validation Page.
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="ade422fd-22ef-466a-80b1-bd33186d9b51" />
    
### Task 3: View the Microsoft.Azure.Cosmos library on NuGet

The NuGet website contains a searchable index of packages that are available to import into your .NET applications. To import prerelease packages such as **Microsoft.Azure.Cosmos**, you can use the NuGet website to get the appropriate versions and commands to import the package into your applications.

1. In a web browser, navigate to the NuGet website (``nuget.org``).

1. Review the description of NuGet, the package manager for .NET, and its capabilities.

1. Search for the **Microsoft.Azure.Cosmos** library on NuGet.org.

1. Click on the link **.Net Standard 2.0**.

1. Select the **.NET CLI** tab to observe the command required to import the latest version of this library into a .NET project.
   
     >**Note**: No need to record this command. You will use a specific version of the library later in this exercise.
     
1. Close your web browser window or tab.

### Task 4: Import the Microsoft.Azure.Cosmos library into a .NET project

 The .NET CLI includes an [add package][docs.microsoft.com/dotnet/core/tools/dotnet-add-package] command to import packages from a pre-configured package feed. A .NET installation uses NuGet as its default package feed.
     
1. Open the **Visual Studio Code**.

1. Open the context menu for the **04-sdk-connect** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

      >**Note**: This command will open the terminal with the starting directory already set to the **04-sdk-connect** folder.

1. Add the [Microsoft.Azure.Cosmos][nuget.org/packages/microsoft.azure.cosmos/3.22.1] package from NuGet using the following command:

    ```
    dotnet add package Microsoft.Azure.Cosmos --version 3.*
    ```     
1. Close the integrated terminal.

### Task 5: Use the Microsoft.Azure.Cosmos library

Once the Azure Cosmos DB library from the Azure SDK for .NET has been imported, you can immediately use its classes within the [Microsoft.Azure.Cosmos][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos] namespace to connect to an Azure Cosmos DB SQL API account. The [CosmosClient][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient] class is the core class that is used to make the initial connection to an Azure Cosmos DB SQL API account.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **04-sdk-connect** folder.

1. Open the empty **script.cs** code file.

1. Add using blocks for the built-in **System** and **System.Linq** namespaces:

    ```
    using System;
    using System.Linq;
    ```

1. Add a using block for the [Microsoft.Azure.Cosmos][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos] namespace:

    ```
    using Microsoft.Azure.Cosmos;
    ```

1. Add a **string** variable named **endpoint** with its value set to the **endpoint** of the Azure Cosmos DB account you created earlier.
  
    ```
    string endpoint = "<cosmos-endpoint>";
    ```

    >**Note**: For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/**, then the C# statement would be: **string endpoint = "https&shy;://dp420.documents.azure.com:443/";**.

1. Add a **string** variable named **key** with its value set to the **key** of the Azure Cosmos DB account you created earlier.

    ```
    string key = "<cosmos-key>";
    ```

    >**Note**: For example, if your key is: **fDR2ci9QgkdkvERTQ==**, then the C# statement would be: **string key = "fDR2ci9QgkdkvERTQ==";**.

1. Add a new variable named **client** of type [CosmosClient][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient] using the **endpoint** and **key** variables in the constructor:
  
    ```
    CosmosClient client = new (endpoint, key);
    ```

1. Add a new variable named **account** of type [AccountProperties][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.accountproperties] using the asynchronous result of invoking the [ReadAccountAsync][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient.readaccountasync] method of the **client** variable:

    ```
    AccountProperties account = await client.ReadAccountAsync();
    ```

1. Use the built-in **Console.WriteLine** static method to print the [Id][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.accountproperties.id] property of the AccountProperties class with a header titled **Account Name**:

    ```
    Console.WriteLine($"Account Name:\t{account.Id}");
    ```

1. Use the built-in **Console.WriteLine** static method to query the [WritableRegions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.accountproperties.writableregions] property of the AccountProperties class and then print the [Name][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.accountregion.name] property of the first result with a header titled **Primary Region**:

    ```
    Console.WriteLine($"Primary Region:\t{account.WritableRegions.FirstOrDefault()?.Name}");
    ```

1. Once you are done, your code file should now include:
  
    ```
    using System;
    using System.Linq;
    
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";
    string key = "<cosmos-key>";

    CosmosClient client = new (endpoint, key);

    AccountProperties account = await client.ReadAccountAsync();

    Console.WriteLine($"Account Name:\t{account.Id}");
    Console.WriteLine($"Primary Region:\t{account.WritableRegions.FirstOrDefault()?.Name}");
    ```

1. **Save** the **script.cs** code file.

### Task 6: Test the script

Now that the .NET code to connect to the Azure Cosmos DB SQL API account is complete, you can test the script. This script will print the name of the account, and the name of the first writable region. When you created the account, you specified a location and you should expect to see that same location value printed as the result of this script.

1. In **Visual Studio Code**, open the context menu for the **04-sdk-connect** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the [dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run] command:

    ```
    dotnet run
    ```

1. The script will now output the name of the account, and the first writable region. For example, if you named the account **sql-<inject key="DeploymentID" enableCopy="false"/>**, and the first writable region was **West US 3**.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

### Review

In this lab, you have completed:

- Prepared your development environment.
- Created an Azure Cosmos DB NoSQL API account.
- Viewed the Microsoft.Azure.Cosmos library on NuGet.
- Imported the Microsoft.Azure.Cosmos library into a .NET project.
- Used the Microsoft.Azure.Cosmos library.
- Tested the script.
