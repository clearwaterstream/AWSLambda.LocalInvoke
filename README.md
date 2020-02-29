# aws-lambda-aspdotnetcore-appmesh

You have a fleet of serverless ASP.NET Core apps configured as AWS Lambda functions. While `AmazonLambdaClient.InvokeAsync()` found in the [AWS SDK](https://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/Lambda/MLambdaInvokeInvokeRequest.html) is a great way to achieve [inter-Lambda communication](https://docs.aws.amazon.com/lambda/latest/dg/lambda-invocation.html), the method does not work for invocation of Lambdas running on your local machine. This library aims to fill this functionality gap by marshalling your requests using IISExpress or IIS when your lambdas are running locally.

### Roadmap
* Validate the solution on OSX + Visual Studio for Mac
* Support for Lambdas deployed behind an ALB
* NET 3.1 support (dependant on AWS)

### Example

```csharp
Say you have an existing Invoke code such as this:

var invokeReq = new InvokeRequest();
invokeReq.FunctionName = "MyLambdaFunction";
invokeReq.InvocationType = InvocationType.RequestResponse;
// ... other params

var apiGatewayReq = new APIGatewayProxyRequest()
{
    HttpMethod = "GET",
    Path = "/Home/Index"
};

invokeReq.Payload = JsonConvert.SerializeObject(apiGatewayReq);

var lambdaClient = new AmazonLambdaClient(); // region, creds

var resp = await lambdaClient.InvokeAsync(invokeReq); // When running in AWS environment

// --- OR, WHEN DEBUGGING LOCALLY ---
// This will route the request to MyLambdaFunction running on your local machine

resp = await invokeReq.RouteAPIGatewayProxyRequestLocally();
```
## Getting Started

#### Catalog installation

Catalog keeps track of all the running ASP.NET Core Applications on your local machine that choose to register to recieve incoming `InvokeRequest` requests.

```
dotnet tool install -g AWSLambda.AspNetCoreAppMesh.Catalog
```

Run the catalog

```
lambda-mesh-cat --urls http://localhost:5050
```

The `--urls` param is optional. The tool will listen on port 5000 and 5001 by default.
