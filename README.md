string exePath = @"downloaded_file.exe";
string arguments = "/s"; // This argument is generally used for silent installations

ProcessStartInfo startInfo = new ProcessStartInfo();
startInfo.FileName = "cmd.exe";
startInfo.Arguments = $"/C \"{exePath}\" {arguments}";
startInfo.WindowStyle = ProcessWindowStyle.Hidden; // Hide the command prompt window
startInfo.Verb = "runas"; // Request admin privileges

using (Process process = new Process())
{
    process.StartInfo = startInfo;
    process.Start();
    process.WaitForExit(); // Wait for the installation process to finish

    // Check if the installation was successful
    if (process.ExitCode == 0)
    {
        // Check if the mail client is not running
        if (IsMailClientNotRunning())
        {
            // If the installation was successful and the mail client is not running, launch the mail client
            ProcessStartInfo mailStartInfo = new ProcessStartInfo();
            mailStartInfo.FileName = "path_to_mail_client_executable"; // Replace with the actual path to your mail client executable
            mailStartInfo.WindowStyle = ProcessWindowStyle.Normal; // Show the mail client window

            using (Process mailProcess = new Process())
            {
                mailProcess.StartInfo = mailStartInfo;
                mailProcess.Start();
            }
        }
    }
}

private bool IsMailClientNotRunning()
{
    string mailClientProcessName = "mail_client_process_name"; // Replace with the actual mail client process name

    // Check if any process with the specified name is running
    Process[] processes = Process.GetProcessesByName(mailClientProcessName);
    return processes.Length == 0;
}


# apicall
using System;
using Microsoft.Deployment.WindowsInstaller;

class Program
{
    static void Main(string[] args)
    {
        // Replace with your application name
        string applicationName = "YourAppName";

        try
        {
            // Use the WindowsInstaller API to find the product code for the application
            string productCode = GetProductCode(applicationName);

            if (!string.IsNullOrEmpty(productCode))
            {
                // Uninstall the application using the product code
                UninstallProduct(productCode);
                Console.WriteLine("Uninstallation successful.");
            }
            else
            {
                Console.WriteLine("Application not found or product code not available.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("An error occurred: " + ex.Message);
        }
    }

    static string GetProductCode(string applicationName)
    {
        string productCode = null;

        using (var installer = new WindowsInstaller.Installer())
        {
            foreach (string product in installer.Products)
            {
                string productName = installer.ProductInfo[product, "ProductName"];
                if (!string.IsNullOrEmpty(productName) && productName.Contains(applicationName))
                {
                    productCode = product;
                    break;
                }
            }
        }

        return productCode;
    }

    static void UninstallProduct(string productCode)
    {
        using (var installer = new WindowsInstaller.Installer())
        {
            installer.ConfigureProduct(productCode, 0, InstallState.Absent);
        }
    }
}
