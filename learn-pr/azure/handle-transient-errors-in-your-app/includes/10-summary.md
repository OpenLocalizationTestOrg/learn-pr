In this module you have seen how to capture transient errors, and how your app can try to recover from them. You've implemented a retry policy, that is configurable and external to your app, allowing you to reuse the policy in multiple places, or other apps.

This module has focused on the code you can write to handle transient errors, using as many native libraries provided by the language as possible. There are a number of third-party open-source libraries you can use, if you require more comprehensive fault handling.

[!include[](../../../includes/azure-sandbox-cleanup.md)]

## Learn More

Resources:

- [Microsoft Best Practice](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)
- [Retry guidance for specific services](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) 
- [Azure Transient Fault Handling](https://docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)

Error Handling Libraries:

- [Polly .NET](http://www.thepollyproject.org/)
- [Polly.JS](https://www.npmjs.com/package/polly-js)
- [Resilience4j](https://github.com/resilience4j/resilience4j)
- [Transient Fault Handling Application Block](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

Logging Libraries:

- [log4J](https://logging.apache.org/log4j/2.x/)
- [log4net](https://logging.apache.org/log4net/)
- [Winston](https://github.com/winstonjs/winston)