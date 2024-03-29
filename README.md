# IKOSOFT-API

The official documentation of the IKOSOFT API.

## Documentation

The swagger page of the IKOSOFT API can be found here https://api.ikosoft.net

## Usage

### Authentication
IKOSOFT authenticates API requests using the apiKey. Based on their permissions the api keys can give you access to all API endpoints or just to a limited number of endpoints.<br>
All requests need an apiKey in the HTTP header of the request, otherwise a 401 Unauthorized response will be returned  by the API.<br>
In the case the apiKey does not have the permissions to access a certain endpoint a 403 Forbidden response will be returned.

### Header parameters
Most requests need 2 parameters to be passed in the HTTP Header.<br>
The parameters are the "apiKey" and the "instituteId".<br>
The instituteId is the ID of the salon/institute that is assigned in the Merlin X2 software.<br>

### Input and output parameters
All the input and output parameters can be found in the swagger documentation mentioned above.<br>
Each method also specifies the possible HTTP status codes.<br>
In case of a non-successful status code, aka something outside the 200-299 range, the IKOSOFT API will return the status code (400,401,403,403, 500 for example) and also the following json structure in the response.<br>
```JSON response in case of error
{
	"ErrorCode":3,
	"ErrorDescription: "The salon was not found"
}
```

The list of application error codes can be found below.
```
public enum ApplicationErrorCode
    {
        InvalidArguments = 1,
        InvalidLogin = 2,
        InstituteNotFound = 3,
        OnlineServicesNotEnabled = 4,
        InstituteDisabled = 5,
        InvalidAPIKey = 6,
        ExpiredAPIKey = 7,
        CustomerNotFound = 8,
        CustomerAlreadyExists = 9,
        InstituteOffline = 10,
        DeamonNotReachable = 11,
        OperationNotImplemented = 12,
        MultipleLocationsNotAllowed = 13,
        LocationNotFound = 14,

        UrlDomainAlreadyExists = 15,
        UrlDomainDoesNotExist = 16,
        ShortUrlAlreadyExists = 17,
        ShortUrlDoesNotExist = 18,
        UrlStatsDoesNotExist = 19,

        AuthorizationFailed = 20,

        AudienceNotFound = 21,

        BeautyCardNotFound = 22,

        LibraryNotFound = 100,

        GeneralError = 99,
    }
```
The error code and description should give more insights about why the request could not be fulfilled, not just a 400 Bad Request for example.

### Salon types
The IKOSOFT API works with Merlin X2 salons. The Merlin X2 software can run on cloud or on premises.
Cloud salons benefit from a number of features, like enhanced slots search, smart slots proposal, Google Maps booking integration and so on.

### Integrating with Merlin X2 via IKOSOFT API

#### Getting the salon settings and list of services
First step would be to get the salon information.<br>
**GET /api/v1/administration/salon-info** endpoint will return the information about the salon.<br>
**GET /api/v1/administration/settings** endpoint will return the online booking related settings of the salon. Things like the slots duration, number of days available for online booking, can the customer cancel appointments, notification to customer or to the salon settings and so on.<br>
**GET /api/v1/booking/services** will return the list of services, packages, families and employees
Besides this we will have the competences of the employees, and the service prices by employee.
The parameters to pass to this method are:
- customerId - the customerId of the logged in user; optional
- maxServicesInPackages - pass the value obtained from **GET** /api/v1/settings response; optional with a default value of 10.
- familyId - if we want to get only services from a certain family; leave null or empty and it will return all services and packages from all families
- startDate - the start date of the search; this will impact the response based on the availability of services-employees during that period. Usually you would pass today.
- endDate - the end data of the search; usually this will be startDate + the parameter *NbDaysForLimitingAppointments* from /settings response.
The response contains the list of employees, services, packages and service families.
Each service has a list of Prices; the prices list contains the price per employee of that service plus contains only the qualified employees for that service.
The same applies for Packages.

#### Customer
The IKOSOFT API allows you to create customers in the Merlin X2 system. You can also update the customer profile information, delete a customer or query for customers.<br>
Customer future appointments are also available and the possibility to cancel an appointment, if the salon settings/cancellation window allows it. <br>
**POST /api/v1/customer** will create a new customer; a successful response contains the new CustomerId; in the case the customer already exists, the CustomerExists will be true and customerId will be empty.<br>
**PUT /api/v1/customer** will modify customer information.<br>

**GET /api/v1/customer** will retrieve customer data given a customerId.<br>
**GET /api/v1/customers** will search customers by email and/or mobile phone.<br>
**GET /api/v1/customer/exists** will tell you if a customer with the given login already exists or not.v
**GET /api/v1/customers/search** will search customers by different properties, like first name, last name, city, etc.<br>
**POST /api/v1/booking/login** will login the customer<br>
**GET /api/v1/booking/customer-appointments** will retrieve the customer future appointments.<br>
**DELETE /api/v1/booking/customer-appointments** will delete a customer appointments using the appointmentId.<br>


#### Booking an appointment
In order to book an appointment we need to first:
- login a customer(get a customerId)
- select a list of services/packages
- optionally we can for each service select a desired employee
- get the time slots for our appointment
- book the appointment
<br>
Once a slots are retrieved, the customer can choose the slot it wants, and make a PUT request to create the appointment.<br>
The information that a salon is on cloud or on premises can be obtained from the IsCloudEnabled parameter in the GET /salon-info response.<br>
The parameters to pass to this method are:<br>

For on premises salons<br>
**POST /api/v1/booking/slots** we query for available slots, 1 day at a time(for on premises salons) or for the whole booking period(cloud salons), sending our shopping basket to the API.
- startDate - the startDate, usually today
- endDate - the end date<br>

For cloud salons<br>
**POST /api/v1/booking/slots/days** we get the slots for the whole period
The parameters to pass to this method are:
- startDate - the startDate, usually today
- endDate - startDate + the parameter *NbDaysForLimitingAppointments* from /settings response.

Parameters for both methods
- locationId - the parameter from /salon-info response
- customerId - optional for querying slots, mandatory for booking the appointment
- proposal Offset - the parameter MinutesBetweenAppointments from /settings response
- a list of services; 
    - serviceId - mandatory
    - employeeId - optional
    - packageId - optional, only if we book a package
<br>

**PUT api/v1/booking/slots** creates the appointment and depending on settings it can notify the customer/salon owner about this new booking. Notification is via email.
In case of success the API returns a 201 response containing the AppointmentId of the newly booked appointment.




