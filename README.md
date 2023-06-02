# IKOSOFT-API

The official documentation of the IKOSOFT API.

## Documentation

The swagger page of the IKOSOFT API can be found here https://api.ikosoft.net

## Usage

### Authentication
IKOSOFT authenticates API requests using the apiKey. Based on their permissions the api keys can give you access to all API endpoints or just to a limited number of endpoints.
All requests need an apiKey in the HTTP header of the request, otherwise a 401 Unauthorized response will be returned  by the API.
In the case the apiKey does not have the permissions to access a certain endpoint a 403 Forbidden response will be returned.

### Header parameters
Most requests need 2 parameters to be passed in the HTTP Header. The parameters are the "apiKey" and the "instituteId".
The instituteId is the ID of the salon/institute that is assigned in the Merlin X2 software.

### Input and output parameters
All the input and output parameters can be found in the swagger documentation mentioned above.
Each method also specifies the possible HTTP status codes.
In case of a non-successful status code, aka something outside the 200-299 range, the IKOSOFT API will return the status code (400,401,403,403, 500 for example) and also the following json structure in the response.
```JSON response in case of error
{
	"ErrorCode":3,
	"ErrorDescription: "The account was disabled"
}
```
The error code and description should give more insights about why the request could not be fulfilled, not just a 400 Bad Request for example.

### Salon types
The IKOSOFT API works with Merlin X2 salons. The Merlin X2 software can run on cloud or on premises.
Cloud salons benefit from a number of features, like enhanced slots search, smart slots proposal, Google Maps booking integration and so on.

### Integrating with Merlin X2 via IKOSOFT API

#### Getting the salon settings and list of services
First step would be to get the salon information.
GET api/v1/administration/salon-info endpoint will return the information about the salon.
GET api/v1/administration/settings endpoint will return the online booking related settings of the salon. Things like the slots duration, number of days available for online booking, can the customer cancel appointments, notification to customer or to the salon settings and so on.
GET api/v1/administration/services will return the list of services, packages, families and employees
Besides this we will have the competences of the employees, and the service prices by employee.

#### Adding a service to the booking basket
Once we have the list of services we want to book we can select a skilled employee or leave the API choose one for us.
Using POST api/v1/booking/slots we query for available slots, sending our shopping basket to the API.
Once a slots are retrieved, the customer can choose the slot it wants, and make a PUT request to create the appointment.
PUT api/v1/booking/slots does this, creates the appointment and depending on settings, it can notify the customer/salon owner about this new booking.

#### Customer perspective
Customers can be created in a salon database. They can also update their profile.
The customer can login via our API, it can query for slots, book appointments and buy vouchers.
He can also view his next appointments by using GET api/v1/customer/appointmentsssss
Depending on salon settings he can also delete using DELETE api/customer/appointmentsssss





