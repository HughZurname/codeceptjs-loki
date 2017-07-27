```javascript
  Feature("Order - User Details");
  let user, claimId;
  Scenario("User Details", function (I) {
      //Retrieving some user form data that was seeded before the test execution.
      //The arguments are the document store being queried and the query object itself.
      //The first matching value is selected, but this can just as easily be used to randomise the selected user.
      user = yield I.findOne("users", {
          email: "someguy@email.com"
      });
      //testId is generated elsewhere per run
      I.fillField("orderNumber", testId);
      I.dontSee("Order number is required");
      I.fillField("firstName", user.firstname);
      I.dontSee("First name is required");
      I.fillField("surname", user.surname);
      I.dontSee("Surname is required");
      I.fillField("address", user.address);
      I.dontSee("Address is required");
      I.fillField("email", user.email);
      I.dontSee("Must be a valid email");
      I.click("Save");
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
