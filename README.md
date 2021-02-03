# Trust self signed certificate in Ubuntu!

In this repository there are two different confirmed approaches that can provide a valid dev certificate for dotnet in ubuntu. The reason trusting a certificate in Linux is more elaborate is because in Linux there is no specific place where all the certificates are stored. The certificate location is changing between different Linux distributions.

## Manual Approach
In the manual approach there are two scripts
 - create-ca.sh
 - create-certificate.sh

Run first the create-ca.sh script to create a valid certificate authority and then run the create-certificate.sh in order to create a valid certificate using the authority created by the previous script. The newly created certificate authority needs to be imported as a trusted authority in the browser. In this approach in order dotnet application to use the certificate we need to explicitly set it in the program.cs file

```sh
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                    webBuilder.UseKestrel(opt => {
                        opt.ListenLocalhost(5000);
                        opt.ListenLocalhost(5001, listenOptions => {
                            listenOptions.UseHttps("localhost.pfx", "test");
                        });
                    });
                });
    }
```

The newly created localhost.pfx file needs to be imported into the solution and set to "copy always" to output directory.

## Credits for the scripts to [BenMorel]

   [BenMorel]: <https://github.com/BenMorel/dev-certificates>


# Automatic approach

In the automatic approach the script creates a self-signed certificate and then importes it and trusts it in several places in the system

- System certificates - to enable service-to-service communication
- User nssdb - to trust the certificate in supported application like Chromium or Microsoft Edge
- Snap Chromium nssdb - to trust the certificate in Chromium if installed via snap
- Snap Postman nssdb - to trust the certificate in Postman if installed via snap

This solution is working out of the box but it might be a bit more vunerable to future OS updates or between different distributions. Only prerequisites are:

- dotnet-sdk
- libnss3-tools


## Credits for the script to [BorisWilhelms]
   [BorisWilhelms]: <https://github.com/BorisWilhelms/create-dotnet-devcert>
