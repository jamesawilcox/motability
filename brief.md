# Technical Test #

## Background ##

The application process for a Motability vehicle progresses over a number of weeks, controlled through one of our main applications (called OLA).

### High Level Process ###

The journey starts with the customer visiting a dealership. The Dealer is acting on behalf of Motability, so the customers deal with Motability via that route. The dealer takes the details of the customer and enters them in OLA.

They select vehicle, details, adaptations, for example. The application is then saved when submitted. Eligibility checks are carried out during the submission process such as:

Awards check: to check the customer is receiving the relevant disability benefit

Driver check: the driver names against the DVLA to verify they're active etc

### High Level Process ###

The OLA application performs a number of these processes via API calls. For example, the initial application is created via a POST call to the endpoint

```
/api/v2/applications
````

with a JSON body like:

``` typescript
{
    dealerCode: 'string',
    customer: {
        firstName: 'string',
        lastName: 'string',
        title: 'string',
        dob: date,
        nino: 'string',
        residentialCareHome: boolean,
        twentyFourHourCare: boolean,
        mobilePhoneNumber: boolean,
        homePhoneNumber: 'string',
        email: 'string',
        addressLine1: 'string',
        addressLine2: 'string',
        addressLine3: 'string',
        addressLine4: 'string',
        postcode: 'string',
    },
}
```

A simplified flow would therefore be:

- Create the Initial Application
- Indicate the customer has accepted DWP consent
- Add drivers (up to 3)
- Add any vehicle adaptations
- Add the estimated delivery date
- Confirm customer communication preferences
- Submit the application

Once the application is submitted, we call a third party service provided by the DVLA to verify the driver(s) details and to check their eligibility. We then use a category code endorsement to set business rules. Examples of business rules we have are:

- DRIVER_WITH_AUTOMATIC_LICENCE_CANNOT_DRIVE_MANUAL_VEHICLE

The rule will fail if the vehicle has manual transmission and the driver has an automatic licence.

- AT_LEAST_ONE_DRIVER_WITH_FULL_LICENCE

The rule will pass if there is at least one driver with a full entitlement (i.e. not provisional), if not it will fail.

- DRIVER_IS_DISQUALIFIED

The rule will fail if the driver is disqualified at the moment.

- DAG_V2_DRIVER_LICENCE_CATEGORY_GHI_ENDORSEMENT

The rule will fail if the driver licence has 4 or more endorsements in the last 4 years from categories G, H, and I combined.

- DAG_V2_DRIVER_LICENCE_CATEGORY_G_ENDORSEMENT

The rule will fail if the driver has two or more endorsements in the last 4 years from category G and has a conviction date in the past 4 years.

- DAG_V2_DRIVER_LICENCE_CATEGORY_F_ENDORSEMENT

The rule will fail if the driver has any endorsements in the last 4 years from category F and has a conviction date in the past 4 years.

Typical peak traffic for these end eligibility end points is 12,000 calls per hour with up to 50 concurrent users.  There is an agreed service level API response time of less than a second.

## The Task ##

Create a short presentation of 10-15 minutes on the strategy for testing this flow, including any relevant non-functional testing, with emphasis on the eligibility checks.

The format you choose is up to you, there is no expectation to do this in code and more than one format can be used.
