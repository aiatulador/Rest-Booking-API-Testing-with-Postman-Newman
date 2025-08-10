
# 🧪 Rest Booking API Testing with Postman & Newman

This project demonstrate automated API testing using **Postman** and **Newman**, for the Restful Booker API. It includes test cases, environment configurations, and detailed reporting using Newman’s HTML reporter.

## 🚀 Features

- ✅ Tests for `GET`, `POST`, `PUT`, `DELETE` requests
- 📁 Organized Postman collection and environment
- 🔄 Pre-request scripts for dynamic data generation
- 🧾 Test scripts for assertions and validations
- 📊 HTML reports with enhanced visualization

## 📚 API Documentation

- 🔗 ![Restful Booker API](https://restful-booker.herokuapp.com)
- 📘 ![API Docs](https://restful-booker.herokuapp.com/apidoc/index.html)
- 📄 ![Postman Documentation](https://documenter.getpostman.com/view/39619510/2sB3BEmpmr)

## 🛠️ Technologies Used

- Postman
- ![Newman](https://www.npmjs.com/packagettps://www.npmjs.com/package/new# Node.js
- !Node Js](https://nodejs.org/)

### **Prerequisite:**
- Node Js
- Newman Html Report Library

### **Installation**

1. Postman: If you haven't already, [download and install Postman.](https://www.postman.com/downloads/)
2. Clone the repository:
 ```console 
  git clone https://github.com/aiatulador/Rest-Booking-API-Testing-with-Postman-Newman.git
```
3. Import the Postman collection:
    - Open Postman.
    - Click on the Import button.
    - Select the file from the repository.
4. Import the Postman environment:
    - In Postman, click on the gear icon in the top right corner.
    - Select **Import** and choose the file.
5. Newman and Report Installation Process:
    - Newman Install Command:
     ```console 
      npm install -g newman
    ```
    - Newman Html Report Install Command:
     ```console 
      npm install -g newman-reporter-htmlextra
    ```
### **Usage**
1. Select Environment:
    -   In Postman, select the appropriate environment (e.g., Development, Production) from the top-right dropdown.
3. Run Collection:
    -   Select the imported collection from the Collections sidebar.
    -   Click on the Runner button to open the collection runner.
    -   Select the desired environment.
    -   Click Start Test to run the collection.
8. View Results:
    -   Once the tests are complete, view the results in the Runner tab.
    -   Detailed test results can be viewed for each request.

## **Testing**

## Test Case Scenarios:

## _**1. Create New Booking**_

### Request URL: https://restful-booker.herokuapp.com/booking/
### Request Method: POST
### Pre-request Script:
```console 
    var firstname = pm.variables.replaceIn("{{$randomFirstName}}")
pm.environment.set("firstname", firstname)


var lastname = pm.variables.replaceIn("{{$randomLastName}}")
pm.environment.set("lastname", lastname)

var totalprice = pm.variables.replaceIn("{{$randomInt}}")
pm.environment.set("totalprice", totalprice)

var depositpaid = pm.variables.replaceIn("{{$randomBoolean}}")
pm.environment.set("depositpaid", depositpaid)


//Date

const moment = require('moment')
var checkin = moment().format('YYYY-MM-DD')
var checkout = moment().add(7, 'd').format('YYYY-MM-DD')

pm.environment.set("checkin", checkin)
pm.environment.set("checkout", checkout)


// Define the array of possible values
var additionalNeedsOptions = ["Breakfast", "Lunch", "Dinner"];

// Store the full array for validation in tests
pm.environment.set("additionalneeds", JSON.stringify(additionalNeedsOptions));

// Randomly select one option
var randomIndex = Math.floor(Math.random() * additionalNeedsOptions.length);
var selectedNeed = additionalNeedsOptions[randomIndex];

// Store the selected value to use in request body
pm.environment.set("selectedNeed", selectedNeed);

```
### post script

```console

var jsonData = pm.response.json()
pm.environment.set("post_bookingid",jsonData.bookingid)

```

  **Request Body:** 
 ```console 
{
	"firstname" : "{{firstname}}",
	"lastname" : "{{lastname}}",
	"totalprice" : {{totalprice}},
	"depositpaid" : {{depositpaid}},
	"bookingdates" : {
    	"checkin" : "{{checkin}}",
    	"checkout" : "{{checkout}}"
	},
	"additionalneeds" : "{{selectedNeed}}"
}

```
  **Response Body:**
 ```console 
{
    "bookingid": 1734,
    "booking": {
        "firstname": "Winifred",
        "lastname": "Hermiston",
        "totalprice": 920,
        "depositpaid": true,
        "bookingdates": {
            "checkin": "2025-08-10",
            "checkout": "2025-08-17"
        },
        "additionalneeds": "Dinner"
    }
}
```
 ## _**2. Get Booking Details By ID**_
### Request URL: https://restful-booker.herokuapp.com/booking/bookingid
### Request Method: GET
### Response Body:
 ```console 
{
    "firstname": "Winifred",
    "lastname": "Hermiston",
    "totalprice": 920,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2025-08-10",
        "checkout": "2025-08-17"
    },
    "additionalneeds": "Dinner"
}

```
### Test Case generation

```console
var statusCode = pm.response.code;
console.log(statusCode)

if (statusCode == 200){

var jsonData = pm.response.json();

// First Name
pm.test("First Name Validation", function () {
    pm.expect(jsonData.firstname).to.eql(pm.environment.get("firstname"));
});

// Last Name
pm.test("Last Name Validation", function () {
    pm.expect(jsonData.lastname).to.eql(pm.environment.get("lastname"));
});

// Total Price
pm.test("Total Price Validation", function () {
    pm.expect(jsonData.totalprice).to.eql(parseInt(pm.environment.get("totalprice")));
});

// Deposit Paid
pm.test("Deposit Paid Validation", function () {
    pm.expect(jsonData.depositpaid).to.eql(pm.environment.get("depositpaid") === "true");
});

// Check-in Date
pm.test("Check-in Date Validation", function () {
    pm.expect(jsonData.bookingdates.checkin).to.eql(pm.environment.get("checkin"));
});

// Check-out Date
pm.test("Check-out Date Validation", function () {
    pm.expect(jsonData.bookingdates.checkout).to.eql(pm.environment.get("checkout"));
});

// Additional Needs
pm.test("Additional Needs Validation", function () {
    pm.expect(jsonData.additionalneeds).to.eql(pm.environment.get("selectedNeed"));
});

}

else if(statusCode == 400) {

pm.test("Status Code 400, Not Found") 

}else{
    pm.test("Something went Wrong")
}

pm.test("Response status code is 200", function () {
    pm.expect(pm.response.code).to.equal(200);
});


pm.test("Response contains required fields", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData).to.have.all.keys('firstname', 'lastname', 'totalprice', 'depositpaid', 'bookingdates', 'additionalneeds');
});


pm.test("Total price is a non-negative integer", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    pm.expect(responseData.totalprice).to.be.a('number').and.to.be.at.least(0, "Total price should be a non-negative integer");
});


pm.test("Deposit paid should be a boolean value", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.have.property('depositpaid').that.is.a('boolean');
});


pm.test("Check-in and check-out dates are in a valid date format", function () {
    const responseData = pm.response.json();
    
    pm.expect(responseData).to.be.an('object');
    
    const checkinDate = responseData.bookingdates.checkin;
    const checkoutDate = responseData.bookingdates.checkout;
    
    const isValidDate = (date) => !isNaN(Date.parse(date));
    
    pm.expect(isValidDate(checkinDate)).to.be.true;
    pm.expect(isValidDate(checkoutDate)).to.be.true;
});

```

## _**3. Create A Token For Authentication.**_
### Request URL: https://restful-booker.herokuapp.com/auth
### Request Method: POST
### Pre-request Script: None
### Request Body:
 ```console 
{
	"username": "admin",
	"password": "password123"
}
```
  **Response Body:**
 ```console 
{
    "token": "06eb798bf6f2caa"
}
```

 ## _**4. Update the Booking Details**_
### Request URL: https://restful-booker.herokuapp.com/booking/bookingid
### Request Method: PUT
### Pre-request Script:
```console 
  var update_firstname = pm.variables.replaceIn("{{$randomFirstName}}")
pm.environment.set("firstname", update_firstname)


var update_lastname = pm.variables.replaceIn("{{$randomLastName}}")
pm.environment.set("lastname", update_lastname)

var update_totalprice = pm.variables.replaceIn("{{$randomInt}}")
pm.environment.set("totalprice", update_totalprice)

var update_depositpaid = pm.variables.replaceIn("{{$randomBoolean}}")
pm.environment.set("depositpaid", update_depositpaid)


//Date

const moment = require('moment')
var update_checkin = moment().format('YYYY-MM-DD')
var update_checkout = moment().add(7, 'd').format('YYYY-MM-DD')

pm.environment.set("checkin", update_checkin)
pm.environment.set("checkout", update_checkout)


// Define the array of possible values
var update_additionalNeedsOptions = ["Breakfast", "Lunch", "Dinner"];

// Store the full array for validation in tests
pm.environment.set("additionalneeds", JSON.stringify(update_additionalNeedsOptions));

// Randomly select one option
var randomIndex = Math.floor(Math.random() * update_additionalNeedsOptions.length);
var update_selectedNeed = update_additionalNeedsOptions[randomIndex];

// Store the selected value to use in request body
pm.environment.set("selectedNeed", update_selectedNeed);

```
  **Request Body:** 
 ```console 
  {
	  "firstname" : "{{firstName}}",
	  "lastname" : "{{lastName}}",
	  "totalprice" : {{totalPrice}},
	  "depositpaid" : {{depositPaid}},
	  "bookingdates" : {
    	  "checkin" : "{{checkin}}",
    	  "checkout" : "{{checkout}}"
	  },
	  "additionalneeds" : "{{additionalNeeds}}"
  }
```
  **Response Body:**
 ```console 
  {
      "bookingid": 4334,
      "booking": {
          "firstname": "Joelle",
          "lastname": "Krajcik",
          "totalprice": 266,
          "depositpaid": true,
          "bookingdates": {
              "checkin": "2024-03-15",
              "checkout": "2024-03-20"
          },
          "additionalneeds": "monitor"
      }
  }
```

 ## _**5. Delete Booking Record**_

### Request URL: https://restful-booker.herokuapp.com/booking/bookingid
### Request Method: DELETE
### Response Body: None 


## 🏃‍♂️ Run Command

### 🔹 Run Command for Console


#### Syntax: newman run <collection_file.json> -e <environment_file.json>
```console
newman run Rest-Booking-API.postman_collection.json -e Rest-Booking.postman_environment.json
```

### 🔹 Run Command for Report:

#### Syntax: newman run <collection_file.json> -e <environment_file.json> --iteration-count <number> --delay-request <milliseconds> -r htmlextra

```bash
newman run Rest-Booking-API.postman_collection.json -e Rest-Booking.postman_environment.json --iteration-count 20 --delay-request 1000 -r htmlextra
```


## 📊 Newman Report Summary

After running the collection with the `htmlextra` reporter, a detailed HTML report is generated showing:

- ✅ Test pass/fail status  
- 📤 Request/response details  
- 🧪 Assertions and logs  

📁 **Example Report Screenshot**:  
![Newman Summary Report](Reports/Screenshot%202025-08-10%20at%2011-08-28%20Newman%20Summary%20Report.png)
