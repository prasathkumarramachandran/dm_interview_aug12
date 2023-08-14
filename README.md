## Context:
Scenario: Assume there is an ice factory. Ice cream is getting manufactured at a plant called ICF. 

The whole process of manufacturing ice cream is considered as 3 steps. 

Mixing - where the ingredients are consumed and mixed. 
Freezing - where the mixed cream is set to freezing below -5 degrees. 
Packing -  where the freezed ice cream is packed in boxes.

When an order is placed to produce 100 kgs of icecream, the order is executed at all these three resources 1. MIXING, 2. FREEZING, 3. PACKING

 

## Requirement:
### Existing objects
Plant, Resources (in this case plant is ICF and resources are MIXING, FREEZING, PACKING) belong to different Microservice
Order belongs to another microservice.

The company plans to collect temperature data at the resource FREEZING at a very high frequency. say 50 times in a second.
Develop code to collect data like this.

For some other customer, factory, there could be different key/value pairs. like Density, Pressure, Viscosity or any other parameter and a value to be collected against that.
Also note that there would be customers who want to collect >500 key/value per second (data collection).

### Validations: 

Ensure plant is valid, resource is valid, order is valid. (In real case, these would be external MS API calls, you can mock these in your local code. Mock in such a way, each of these calls takes around 200 ms.)
After you develop the code check in JMeter and document the performance and check if your code does solve the 500 requests/second requirement.

If so, good. If not, what would be your next steps? how do you improve the performance? Where and all you will check things to improve like DB design, code/logic, remote calls, threads, scaling your app - horizontal / vertical, etc.,

 

### Step 1:
API Input: Resource, Order, Key(TEMPERATURE), Value(say -1 degrees)
API : to get the above inputs, validate them, persist in DB.

 

### Step 2:
Assume customers say they want to add min / max values for the data parameter - say Freezing - Temperature Min is -15 degrees and max is -5 degrees
And while collecting data if the passed value for temperature is beyond the min / max value, we need yo raise an event - say via Kafka Producer (or any Message System)

 

Solution should support such a collection of data at huge volumes

Plant, Resource, Order -- make an external API call for validation.

### Resource JSON 

```json
{
  "resourceId": "MIXING",
  "resourceName": "Mixing Machine"
}
```

### Order JSON Object
```json
{
  "orderId": "ORDER123",
  "orderQuantity": 100,
  "productName": "Ice Cream"
}

```

### Temprature JSON Object
```json
{
  "resourceId": "FREEZING",
  "order": {
    "orderId": "ORDER123",
    "orderQuantity": 100,
    "productName": "Ice Cream"
  },
  "parameter": {
    "key": "TEMPERATURE",
    "value": -5,
    "minValue": -15,
    "maxValue": -5
  }
}
```
### Event JSON Object (Raised when Temperature Value Exceeds Limits):
```json
{
  "resourceId": "FREEZING",
  "order": {
    "orderId": "ORDER123",
    "orderQuantity": 100,
    "productName": "Ice Cream"
  },
  "parameter": {
    "key": "TEMPERATURE",
    "value": -20,
    "minValue": -15,
    "maxValue": -5
  },
  "event": {
    "eventType": "OUT_OF_RANGE",
    "description": "Temperature value is below minimum limit"
  }
}
```

## User Interface Requirement 

The ice cream manufacturing company now requires a user interface to interact with the data collection process and monitor the manufacturing steps in real-time. 
The user interface will be developed using React.js and will provide a dashboard for the plant manager and operators to visualize and manage the data collection and manufacturing process.

### Dashboard Overview: 
The dashboard will provide an overview of the ongoing manufacturing process, including the status of mixing, freezing, and packing for each order.

### Real-time Temperature Display: 
A real-time temperature display will be integrated into the dashboard to show the temperature data collected from the FREEZING resource. 
The temperature display should update at a high frequency to reflect the most recent temperature readings.

### Order Management: 
The user interface will allow the plant manager to view the orders placed, their quantities, and the current status of each order in terms of the manufacturing steps.

### Parameter Configuration: 
The interface will provide a configuration panel to set the minimum and maximum values for the temperature parameter for each resource (e.g., FREEZING). 
Any temperature readings that fall outside these limits will trigger an event, as described in the previous problem statement.

### Event Monitoring: 
The dashboard should display events that are raised when parameter values exceed the configured limits. These events should be clearly visible to the operators for immediate attention.

### Validation Feedback: 
The interface should provide feedback to the user regarding the validation status of the plant, resource, and order. If any of these entities fail validation, appropriate messages should be displayed.

### Integration with Backend: 
The React user interface will communicate with the backend microservices to retrieve manufacturing process data, order information, temperature readings, and configuration settings.
This communication will be carried out through RESTful API calls.

### Next Steps: 
If the performance of the user interface is not up to the required standard, consider optimizing the UI components, utilizing server-side rendering, implementing caching mechanisms. You may explore optimizing the backend API calls to reduce response times and ensure smooth communication between the UI and the backend services.
