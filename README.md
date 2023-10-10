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
