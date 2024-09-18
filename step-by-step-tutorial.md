# Step by Step Tutorial

1. **Clone project repository**

```
git clone https://github.com/heroku-examples/lumina-solar-api
```

2. **Create Heroku app**

```
heroku create <username>-lumina-solar
```

3. **Provision Heroku Postgres Add-on**

```
heroku addons:create heroku-postgresql:mini
heroku config --shell > .env
```

4. **Import the database schema using by running**

```
npm run setup
```

5. **Seed the database using**

```
node data/seed.js
```

6. **Deploy**

```
git push heroku main
```

7. **Create External Credential**
   1. From Setup, in the Quick Find box, enter `Named Credentials`, and then select Named Credentials.
   2. Click External Credentials.
   3. To create a new external credential, click New. To edit an existing external credential, click its link in the list of external credentials and then click Edit.
   4. Complete the fields.
      1. Label: `LuminaAPI`
      2. Name: `LuminaAPI`
      3. Authentication Protocol: Select `No Authentication`
   5. Save the external credential.
8. **Create Principals for No Authentication Protocol**
   1. On the Named Credentials page, click External Credential.
   2. Select the `LuminaAPI` external credential that you created.
      1. Scroll to Principals.
      2. To create a principal for the external credential, click New or select Edit from the Actions menu of an existing principal. When editing an existing principal, not all the fields listed here are modifiable.
      3. Enter the information for the principal.
         1. Parameter Name: `LuminaAPIPrincipal`
         2. Sequence Number: `1`
         3. Identity Type: `Named Principal`
      4. Click Save to save the principal.
9. **Create a Permission Set to enable External Credential Principals and assign permission set to your user**
   1. From Setup, in the Quick Find box, enter `Permission Sets`, and then select Permission Sets.
   2. Click New.
   3. Enter your permission set information.
      1. Label: `Lumina Service`
      2. API Name: `LuminaService`
   4. Click Save.
   5. Click External Credential Principal Access in the Apps section.
   6. Click Edit.\
      The Edit page displays two columns: one for available external credential principals, and one for external credential principals that are currently enabled.\
      External credential principals take the form:\
      _external credential name - external credential principal parameter name_
   7. Select `LuminaAPI - LuminaAPIPrincipal` from the list of available principals. Click the Add arrow.
   8. Click Save.
   9. Click Manage Assignments and then Add Assignments.
   10. Select the checkbox next to your user.
   11. Click Assign.
   12. Click Done.
10. **Create Named Credential**
    1. From Setup, enter `Named Credentials` in the Quick Find box, then select Named Credentials.
    2. Click Named Credentials.
    3. To create a new named credential, click New . To edit an existing named credential, click its link in the list of named credentials and then click Edit.
    4. Complete the fields.
       1. Label: `LuminaAPI`
       2. Name: `LuminaAPI`
       3. URL: Web URL of Heroku app (Find “Web URL” via `heroku apps:info -a <username>-lumina-solar`)
       4. External Credential: `LuminaAPI` (created in “Create External Credential” step above)
    5. Save the Named Credential
11. **Register External Service**
    1. From Setup in Lightning Experience, enter `External Services` in the Quick Find box, and select External Services.
    2. Click New External Service.
    3. On the Select an API Source page, select whether you’re importing an API spec From API Specification.
    4. Under External Service Name: `LuminaService`.
    5. Under Service Schema, select Complete JSON. Provide a complete, valid OpenAPI 3.0, and JSON-compliant schema.
    6. Click under Named Credentials and select `LuminaAPI` named credential from the list.
    7. Copy your `LuminaAPI` app’s OpenAPI JSON specification.\
       On your Heroku deployed app, go to the SwaggerUI page shown via `/api-docs/static/index.html`.
       1. Click on the link below the header: `/api-docs/json`.
       2. Copy the OpenAPI specification JSON.
    8. In Schema, paste your JSON-formatted schema into the provided text field.
    9. Click Save & Next. Your uploaded schema is validated by External Services.
    10. Select the Select All to import all `LuminaAPI` operations into your external service registration.
    11. Click Next.
    12. Review External Service Actions to be imported.
    13. Click Finish.
12. **Create Apex Class**
    1. Review `LuminaService` External Service generated Apex classes.
    2. From Setup, in the Quick Find box, enter `Apex Classes`, and then select Apex Classes.
    3. Scroll down to Dynamic Apex Classes.
    4. Note Apex classes that represent `LuminaService` operations:
       1. `LuminaService`: invokes `LuminaAPI` operations.
       2. `LuminaService_error`: encapsulates API response errors.
       3. `LuminaService_getApiSummaryBySystemId_OUT_200`: encapsulates `/api/systems` API responses.
       4. `LuminaService_metric`: encapsulates `/api/metrics/{systemId}` API responses.
       5. `LuminaService_z0system`: encapsulates `/api/summary/{systemId}` API responses.
    5. Write an Apex class that invokes the `LuminaService` External Service.
       1. Click the Setup icon, and then select Developer Console.
       2. Click File | New | Apex Class.
       3. Enter a name for the apex class, `InvokeLuminaService`.
       4. In the class editor, enter this source:

```apex
public class InvokeLuminaService {

    // Invoke /api/systems
    public static void logAllSystems() {
        ExternalService.LuminaService luminaService = new ExternalService.LuminaService();
        
        // Invoke API
        ExternalService.LuminaService.getApiSystems_Response response = luminaService.getApiSystems();
        System.debug(JSON.serialize(response));
    }
    
    // Invoke /api/metrics/{systemId} w/ date
    public static void logMetrics() {
        ExternalService.LuminaService luminaService = new ExternalService.LuminaService();
        
        // Get a SystemId
        ExternalService.LuminaService.getApiSystems_Response getApiSystemsResponse = 
            luminaService.getApiSystems();
        String systemId = getApiSystemsResponse.Code200.get(0).id;
        
        // Assemble request
        ExternalService.LuminaService.getApiMetricsBySystemId_Request request = 
            new ExternalService.LuminaService.getApiMetricsBySystemId_Request();
        Date filterDate = Date.today();  // Date of when the data was seeding
        request.z0date = filterDate;
        request.systemId = systemId;
        
        // Invoke API
        ExternalService.LuminaService.getApiMetricsBySystemId_Response response = 
            luminaService.getApiMetricsBySystemId(request);
        System.debug(JSON.serialize(response));
    }
    
    // Invoke /api/summary/{systemId}
    public static void logSummary() {
        ExternalService.LuminaService luminaService = new ExternalService.LuminaService();
        
        // Get a SystemId
        ExternalService.LuminaService.getApiSystems_Response getApiSystemsResponse = luminaService.getApiSystems();
        String systemId = getApiSystemsResponse.Code200.get(0).id;
        
        // Assemble request
        ExternalService.LuminaService.getApiSummaryBySystemId_Request request = 
            new ExternalService.LuminaService.getApiSummaryBySystemId_Request();
        request.systemId = systemId;
        
        // Invoke API
        ExternalService.LuminaService.getApiSummaryBySystemId_Response response = 
            luminaService.getApiSummaryBySystemId(request);
        System.debug(JSON.serialize(response));
    }
}
```

13. **Invoke `InvokeLuminaService` Apex class**
    1. In Developer Console, click Debug | Open Execute Anonymous Window to open the Enter Apex Code window.
    2. Enter the code below to run in the Enter Apex Code window. To automatically open the resulting debug log when execution is complete, select Open Log.
    3. To execute all code in the window, click Execute or CTRL+E.
    4. After the code executes, the debug log will be listed on the Logs tab. Double-click the log to open it in the Log Inspector.
    5. Review `EXTERNAL_SERVICE_REQUEST` and `EXTERNAL_SERVICE_RESPONSE` log details.

```apex
InvokeLuminaService.logAllSystems();
InvokeLuminaService.logMetrics();
InvokeLuminaService.logSummary();
```

14. **Create Flow**
    1. From Setup in Lightning Experience, enter `Flows` in the Quick Find box, and select Flows.
    2. Click New Flow.
    3. Select Screen Flow to start a new flow from scratch.
    4. Add an Action element that invokes the LuminaService External Service.
       1. In-between Start and End, hover over circle and click plus.
       2. In the Add Element dialog box, and in select Action.
       3. In the New Action dialog box, select LuminaService.
       4. In the Action search box, select Get Api Systems.
       5. Enter the following field values:
          1. Label: `Get Systems`
          2. API Name: `Get_Systems`
       6. Click Done.
    5. Add an Screen element that has the user select a Lumina Solar System.
       1. In-between Get Systems Action and End, hover over circle and click plus.
       2. In the Add Element dialog box, and in select Screen.
       3. Under Screen Properties, enter the following field values:
          1. Label: `Select a system`
          2. API Name: `Select_a_system`
       4. Under Inputs, select and drag Picklist to the empty box under Select a Lumina Solar System.
       5. In the Picklist configuration, provide the following values:
          1. Label: `Systems`
          2. API Name: `Systems`
          3. Required: Check
    6. In the Configure Choices configuration, provide the following values:
       1. Let Users Select Multiple Options: No
       2. Choice: Select New Choice Resource
          1. Resource Type: `Collection Choice Set`
          2. API Name: `System_Select`
          3. Collection: `Outputs from Get_Systems >_ 200` (`{!Get_Systems.200}`)
          4. Under Configure Each Choice:
             1. Choice Label: `address`
             2. Data Type: `Text`
             3. Choice Value: `id`
          5. Click Done.
       3. Click Done.
    7. Click Save.
    8. Enter the following field values:
       1. Flow Label: `Select a Lumina Solar System`
       2. Flow API Name: `Select_a_Lumina_Solar_System`
    9. Click Run.
