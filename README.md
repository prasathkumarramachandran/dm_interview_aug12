## Context:
Scenario: Assume there is an ice factory. Ice cream is getting manufactured at a plant called ICF. The whole process of manufacturing ice cream is considered as 3 steps. Mixing - where the ingredients are consumed and mixed. Freezing where the mixed cream is set to freezing below -5 degrees. And then Packing where the freezed ice cream is packed in boxes.
When an order is placed to produce 100 kgs of icecream, the order is executed at all these three resources 1. MIXING, 2. FREEZING, 3. PACKING

 

## Requirement:
### Existing objects
Plant, Resources (in this case plant is ICF and resources are MIXING, FREEZING, PACKING) belong to different Microservice
Order belongs to another microservice.
The company plans to collect temperature data at the resource FREEZING at a very high frequency. say 50 times in a second.
Develop code to collect data like this.
For some other customer, factory, there could be different key/value pairs. like Density, Pressure, Viscosity or any other parameter and a value to be collected against that.
Also note that there would be customers who want to collect >500 key/value per second (data collection).
Validations: Ensure plant is valid, resource is valid, order is valid. (In real case, these would be external MS API calls, you can mock these in your local code. Mock in such a way, each of these calls takes around 200 ms.)
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
