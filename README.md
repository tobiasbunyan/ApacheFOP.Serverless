# ApacheFOP.Serverless
*ApacheFOP.Serverless* is a ready to use server-less implementation of Apache FOP via Azure Functions. This provides a micro-service for dynamically rendering PDF binary outputs from XSL-FO source using Apache FOP.

You should be able to pull this code down, open with IntelliJ (after installing the Azure Toolkit for IntelliJ), and deploy to your own Subscription/Resource group and be up and running in minutes.

If you like this project and/or use it the please give me a Star (c'mon it's free, and it'll make my day)! 

### [Buy me a Coffee ☕](https://www.buymeacoffee.com/cajuncoding)
*I'm happy to share with the community, but if you find this useful (e.g for professional use), and are so inclinded,
then I do love-me-some-coffee!*

<a href="https://www.buymeacoffee.com/cajuncoding" target="_blank">
<img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174">
</a> 

## Updates
Updated the project to v1.1 as it now incorporates the following new updates:
 - Upgraded the project to use Java v11 now as the latest long term supported (LTS) version for Azure Functions (aka Zulu Java v11)
   - _Previously was Java 8 (v1.8) (aka Zulu Java v8)._
 - Bumping the versions of all dependencies to the latest stable versions
 - Bumping the Apache FOP version to v2.6 (just released in Jan 2021)
 - Adding support for configuration Xml to fully configure ApacheFOP Factory by editing the `/src/main/resources/apache-fop-config.xml' as needed.
   - _The configuration will be bundled and deployed with the application._
 - Now includes an existing `apache-fop-config.xml` file which enables Font 'auto-detect' feature for much better Font support.
 - Removed dependency on `com.sun.deploy.net.HttpRequest` import as importing it no longer compiles on the latest versions of IntelliJ IDEA; little value was added by using only one constant that was needed: _ACCEPT_ENCODING_
 - All Heading and Content type constants are now self-contained so no additional dependencies are needed.
   - _This enabled removal of the dependency on com.sun.deploy.net.HttpRequest import as importing it no longer compiles on the latest versions of IntelliJ IDEA, and is a bad practice.  Little value was added by using only 1 constant was needed, ACCEPT_ENCODING_

 - Notable cleanup & optimization of the Pom.xml
 - Implemented a fix for a possilbe deployment risk when AppName and ResourceGroupName values are not unique with the azure-functions-maven-plugin
   - _As noted here: https://github.com/Azure/azure-functions-java-worker/issues/140_


## Technical Summary:
This project provides a REST API that recieves a POST body containing a well formed Xsl-FO Xml document ([like these Apache FOP samples](https://github.com/apache/xmlgraphics-fop/tree/trunk/fop/examples/fo/basic)). The service will respond with the rendered Pdf binary (file bytes).

If an error occurs -- likely due to incorrect Xsl-FO syntax or structure -- then an Http 500 Response will be returned with a JSON payload containing details about the error.  For issues with the Xsl-FO parsing/processing, ApacheFOP generally provides very helpful info. about the line, location, and markup that caused the error so that it can be resolved.

#### Azure Function API:
 - Path: **`/api/apache-fop/xslfo`**
 - Request Type: **POST**
 - Request Body: **Xsl-FO Content as valid Xml** to Render

#### Responses:
 - **Http 200-OK**: *Binary File paylaod containing the rendered Pdf File*
 - **Http 500-InternalServerError** -- *Json response payload with ApacheFOP processing error details.*


#### Postman Example:
<p align="center">
<img src="/postman-test-fonts-fo.png" style="width:auto;height:auto;max-width:1200px;">
</p>

## Project Overview:
Generating high quality printable PDF outputs from a highly flexible [pdf templating approach (separating content/data from presentation)](https://github.com/cajuncoding/PdfTemplating.XslFO) hasn't been easy in the world of .Net -- vs the world of Java where ApacheFOP has been around for a very long time.

For a more exhaustive dive into why PDF templating and markup based solutions are more powerful than report designer based solutions -- in today's modern web apps -- 
I ramble on about that over here in: 
 - [Part 1: Considerations for a robust PDF or Web reporting solution?](https://cajuncoding.com/2020-11-17-pdf-reports-part1-how-hard-can-it-be/)
 - [Part 2: Why markup based PDF Templating is the way to go...](https://cajuncoding.com/2020-11-18-pdf-reports-part2-ok-where-does-that-leave-us/)

Suffice it to say that markup based solutions have alot of value, and Xsl-FO is still one of the best ways to maintain strong software development practices by rendering PDF outputs (as a presentation output) from separated content/data + template. And Xsl-FO offers features that some approaches just can't do (looking at you *Crystal Reports*).

There has been a fully managed .Net C# port of [Apache FOP](https://xmlgraphics.apache.org/fop/) (FO.Net) based on a pre-v1.0 version (*is my guesstimate*); it's old & unsupported, but still fairly functional, and I've used it very successfully on several projects. But Apache FOP is now on [v2.6 as of Jan 2021!](https://xmlgraphics.apache.org/fop/2.6/changes_2.6.html) with annual/bi-annual support updates still being released.

So my goal has been, for a while, to take advantage of the many great innovations in the past several years to provide an interoperable integration between Java Apache FOP and .Net, without resorting to [something that makes my eyes cross (ugg).](http://codemesh.com/products/juggernet/).

Taking advantage of some awesome new innovations (in Azure) we can do this in a much cleaner way using:
 - **[C# .Net for Templating using Razor](https://github.com/cajuncoding/PdfTemplating.XslFO)** *(anything other than native Java will benefit from this)*
 - **Microservice** for integration architecture
 - **REST API** for interoperability
 - **Azure Function** for native Java Support
 - **[Apache FOP](https://xmlgraphics.apache.org/fop/)** for the free & robust XSL-FO processing implementation

So that's the goal of this project, to provide a ready-to-deploy microservice implementation of Apache FOP via Azure Functions. And enable others to pull the code down, deploy to their Azure subscription/resource group and be up and running in minutes -- especially those less experienced in Java.

I'm definitely not the only person to think of this, and definitely gleaned some insight from [this article written by Marc Mathijssen.](https://medium.com/@marcmathijssen/using-the-power-of-apache-fop-in-a-net-world-using-azure-functions-971669b888dc)...

But, I did find that article left alot of nebulous details unclear, and would expect that a Java novice may really struggle to get it up and running. Especially the dependency on the command line tutorial, and lack of guidance on Java IDE, or how to Debug/Test, etc.

And ultimately, it didn't provide any insight on how to configure Maven correctly *(I can hear some devs asking "what's Maven" right now)* or any code at all really. So, **Kudos** for the intro, but I hope this helps to bring it across the goal line!

## Getting Started:
Here's the high level steps to get started...

1. First start with this article to get your Java IDE *IntelliJ IDEA* setup with Azure Toolkit!
   - [https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-maven-intellij](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-maven-intellij)
   - It uses [IntelliJ IDEA](https://www.jetbrains.com/idea/); the best Java IDE available (*in my humble opinion*), especially for those new to Java :-)
   - It guides you on the use of **The Azure Toolkit** for IntelliJ IDEA which will make debugging & deploying a breeze...
2. This project has already been configured using an Azure Functions Maven archetype, and all necessary dependencies for ApacheFOP, Apache Commons libraries, etc. have already been configured correctly.
3. Once IntelliJ is up and running, you can pull down the Repo, and then just open the `apachefop-serverless-az-func` as the root project -- I just right click and select _**"Open Folder as IntelliJ Community Edition Project"**_.
4. It's usually a good idea to reload the Maven pom.xml -- which will refresh and ensure that all dependencies are ready to go.
5. [Run the function app locally and debug](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-maven-intellij#debug-the-function-app-locally) . . . click the good-ole Debug Icon in IntelliJ and fire up the micro-service locally.
   - Yes, this will fully support local execution, testing, and debugging!
6. Install Postman (or equivalent) and play with it...
7. Finally, [Deploy to Azure using the Azure Toolkit](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-maven-intellij#deploy-your-function-app-to-azure) whenever you're ready...

## Additional Features:

#### GZIP Compression:
In addition to rendering the PDF, this service already implements GZIP compression for returning the Binary PDF files. This can greatly improve performance and download times, especially for PDFs rendered without much imagery, as the text content bytes tends to be more compressible.

All you need to do is add the `Accept-Encoding` header with a value of `gzip` to the request:
```
Accept-Encoding=gzip
```
<p align="center">
    <img src="/postman-header-enable-gzip.png" style="width:auto;height:auto;max-width:800px;">
</p>

## Calling the Service from .Net (ApacheFOP.Serverless REST Client on Nuget):
Below is a snippet to make a simplified/straightforward call, however _ApacheFOP.Serverless_ has a several other advanced features including compression options and debugging outputs.  So to make things alot easier I've shared a very lightweight REST Client as a .Net Standard 2.0 library that can be used in any .Net project.  It provides options to easily use all of the advanced features and handles debugging details automatically.

To easily add support for _ApacheFOP.Serverless_ into your project just add the ready-to-go client libary **availalbe on Nuget here:** [**PdfTemplating.XslFO.Render.ApacheFOP.Serverless Client Library**](https://www.nuget.org/packages/PdfTemplating.XslFO.Render.ApacheFOP.Serverless/)

Also, I provide additional usage details in my article (mentioned below), but you can jump to the [_ApacheFOP.Serverless_ REST Client details here...](https://cajuncoding.com/2021-08-22-pdf-reporting-with-a-serverless-architecture/#ApacheFopServerlessClient)

### Simplified Snippet:
Because I talked about follow-through up above, I'd be amiss if I didn't provide a sample implementation of calling this code from .Net.

Assuming the use of the great *RESTSharp library* for REST api calls, and the Xsl-FO content is validated and parsed as an *XDocument* (Linq2Xml)... this sample should get you started on the .Net side as a client calleing the new PDF microservice.

*NOTE: Just use RESTSharp and avoid [incorrectly implementing HttpClient (hint, it should be a singleton)](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/)*

Snippet taken from the [implementation here](https://github.com/cajuncoding/PdfTemplating.XslFO/blob/feature/iniial_support_for_apache_fop_serverless_rendering/PdfTemplating.XslFO.Render.ApacheFOP.Serverless/XslFOPdfRenderService.cs), in my PdfTemplating project.

```csharp
using RestSharp;
using RestSharp.CustomExtensions;
using System;
using System.CustomExtensions;
using System.Threading.Tasks;
using System.Xml.Linq;

namespace PdfTemplating.XslFO.ApacheFOP.Serverless
{
    //READ from Configuration, or DI Constructor Injection
    private string apacheFOPServiceHost = "http://localhost:7071";
    private string apacheFOPServiceApi = "api/apache-fop/xslfo";

    public static class ApacheFOPServerless
    {
        public async Task<byte[]> RenderPdfBytesAsync(XDocument xslFODoc)
        {
            //Initialize the Xsl-FO microservice via configuration...
            var restClient = new RestClient(apacheFOPServiceHost);

            //Get the Raw Xml Source for our Xsl-FO to be tansformed into Pdf binary...
            var xslFoSource = xslFODoc.ToString();

            //Create the REST request for the Apache FOP micro-service...
            var restRequest = new RestRequest(apacheFOPServiceApi, Method.POST);
            restRequest.AddRawTextBody(xslFoSource, ContentType.Xml);

            //Execute the request to the service, validate, and retrieve the Raw Binary resposne...
            var restResponse = await restClient.ExecuteWithExceptionHandlingAsync(restRequest);

            var pdfBytes = restResponse.RawBytes;
            return pdfBytes;            
        }
    }
}
```

### .Net PdfTemplating (Full blown) Implementation:
A full blown demo implementation of templating + ApacheFOP.Serverless, as well as ready-to-go .Net Clients (via Nuget package) can be found in my [Pdf Templating project here](https://github.com/cajuncoding/PdfTemplating.XslFO).

It illustrates the use of both Xslt and/or Razor templates from ASP.Net MVC to render PDF Binary reports dynamically from queries to the [Open Movie Database API](http://www.omdbapi.com/). And it has now been enhanced to also illustrate the use of _ApacehFOP.Serverless_ microservice for rendering instead of the embedded legacy FO.Net implementation.

With the running application provided in the project above, the following page url's will render the dynamic Pdf using ApacheFOP.Serverless.
 
 *NOTE: You will need to have ApacheFOP.Serverless project running either locally or in your own instance of Azure :-) Just update the Web.config to point to your Host (Local or Azure).*

 - http://localhost:57122/movies/pdf/razor/apache-fop?title=Star%20Wars
 - http://localhost:57122/movies/pdf/xslt/apache-fop?title=Star%20Wars

<p align="center">
    <img src="/pdf-templating-apache-fop-serverless-chrome-test.png" style="width:auto;height:auto;max-width:1200px;">
</p>

## Additional Background:
For additional details check out my article [**_PDF Reporting with a Serverless Architecture_**](https://cajuncoding.com/2021-08-22-pdf-reporting-with-a-serverless-architecture/), where I provide a broader overview of the background, architecture, and additional details around the _ApacheFOP.Serverless_ project.

Now Geaux Code!
