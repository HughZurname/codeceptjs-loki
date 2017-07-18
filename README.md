# codeceptjs-loki
[Codecept](http://codecept.io/) Helper with in memory databse (provided by [loki.js](http://lokijs.org/)) for data driven testing and result capturing. An example scenario can be found [below](#example)

**Parameters**

-   `config` **Object** configuration can be overridded by values found in `codecept.json`

## addCollection

Will check for an existing collection of the same name and return that, if it already exists.

**Parameters**

-   `collection` **string** creates a collection with supplied name.

## findCollection

**Parameters**

-   `collection` **string** name of desired collection.

## removeCollection

Removes a matching collection.

**Parameters**

-   `collection` **string** finds a collection that matches the supplied name

## insert

Inserts records from the supplied data to a matching collection.

**Parameters**

-   `collection` **string** name of desired collection.
-   `data` **Array&lt;Object&gt;** takes an array of objects as records for the destination collection.

## clear

Clears data from a matching collection.

**Parameters**

-   `collection` **string** name of desired collection.

## find

Searches the Users colection for matching values.

**Parameters**

-   `collection` **string** name of desired collection.
-   `query` **Object** used in searching the destination collection for matching values.

**Examples**

```javascript
// Searches for a user with an email of "someone@email.com"
I.find("Users",{email: "someone@email.com"})
```

Returns **Array&lt;Object&gt;** Returns an array of objects that match.

## findOne

Effectively the same as `find()` but returns a single object rather than an array.

**Parameters**

-   `collection` **string** name of desired collection.
-   `query` **Object** used in searching the destination collection for matching values.

## findAndUpdate

Finds a list of values based on the supplied query and runs an update function on each result.

**Parameters**

-   `collection` **string** name of desired collection.
-   `query` **Object** used in searching the destination collection for matching values.
-   `updateFunction` **function** executes against each of the results in the result array.

**Examples**

```javascript
// Before {email: "someone@email.com", firstname: "Some", surname: "One", address1: "1 Some Place"}
I.findAndUpdate("users",{email: "someone@email.com"},(res)=>{res.number = "01234567890"})
// Find updated record
I.findOne("Users",{email: "someone@email.com"})
// After {email: "someone@email.com", firstname: "Some", surname: "One", address1: "1 Some Place", number:01234567890}
```

## findAndRemove

Like `findAndUpdate()` but finds a record in a collection and removes it.

**Parameters**

-   `collection` **string** name of desired collection.
-   `query` **Object** used in searching the destination collection for matching values.


## importData

Imports data from a directory into a collection. Uses file names to create a collection of the same name and imports the contents of the file as records to the destination.

**Parameters**

-   `dir` **string** takes a directory as a string.

## Example

```javascript
Feature("Order - User Details");

let user, claimId;

Scenario("Enter user Details", function* (I) {
    
    //Retrieving some user form data that was seeded before the test execution.
    //The arguments are the document store being queried and the query object itself.
    //The first matching value is selected, but this can just as easily be used to randomise the selected user.
    user = yield I.findOne("users", {email: "someguy@email.com"});

    //testId is generated elsewhere per run
    I.fillField("orderNumber", testId);
    I.dontSee("Document number is required");

    I.fillField("firstName", user.firstname);
    I.dontSee("First name is required");

    I.fillField("surname", user.surname);
    I.dontSee("Surname is required");

    I.fillField("addressLine1", user.address1);
    I.dontSee("Address1 is required");

    I.fillField("postCode", user.postcode);
    I.dontSee("Postcode is required");

    I.fillField("phoneNumber", user.number);
    I.dontSee("Telephone number is required");

    I.fillField("email", user.email);
    I.dontSee("Must be a valid email");
});

Scenario("Save & Continue",  function* (I) {
    I.click("Save & Continue");

    //Example custom helper function to grab the "order id" from the url.
    claimId = yield I.grabUrlPart(4);
});

AfterSuite((I) => {
    //Create a new document store for orders before inserting relevant data from the test above.
    //This becomes queryable in later scenarios/features, like users was above. Thus allowing validation against known values while still allowing the use dynamic data to drive testing.
    I.addCollection("orders");
    I.insert("orders", {
        orderId: claimId,
        orderNumber: testId,
        email: user.email,
        fullName: `${user.firstname} ${user.surname}`,
    });
});
```
