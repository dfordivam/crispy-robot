# Rewards Points Service

### I. Overview 

This projects implements a part of reward points service, wherein the users can earn rewards by making purchase at vendors, and can also make use of points in doing the purchases.

This makes use of the proposal-accept pattern where the draft is created by the vendor and it gets approved by the customer.

When approving; the customer can choose from three options
* use the maximum possible points available in their account at that time.
* Use a specified amount of points
* not to use any points (but still earn points as rewards)

This choice exercised by the customer is not recorded in the contract. As that would end up disclosing the customer's account balance to the vendor.

The customers are able to view all the authorized vendors, and the corresponding "points factor", ie the amount of points they will earn when doing purchase at that vendor.

For simplicity each reward point corresponds to an equivalent amount in the currency value.

### II. Workflow

1. The issuer opens a `CustomerAccount`, optionally adding some bonus points at the start.

2. The issuer opens a `VendorAccount`, and an `AuthorizedVendor` contract for this vendor.

   The latter is visible to all the customers of the service, as the customers would be given a `CanReadAs` rights as `issuer_service_customers` Party.

3. The vendor can create a `SalesRequestDraft`, proposing a sales contract containing the list of items and total amount.

4. The customer can approve or reject the `SalesRequestDraft`.

   If the customer approves, they have the choice of using points to do partial or full payment.
   After approval the `SalesRequestDraft` is consumed and `SalesRequest` is created.

5. The approved `SalesRequest` has to be settled by the issuer.

   This may be done automatically by the issuer service, whenever a new `SalesRequest` is created.
   
   In the current design, the points used are deducted from the `CustomerAccount` and added to the VendorAccount. And if applicable, the earned points are added to the `CustomerAccount`.
   
6. Once settled the `SalesRequest` will be consumed and a `SalesRequestSettled` will be created with the customer and vendor as observers.

### III. Challenge(s)

The need to disclose all the active vendors and the `pointsFactor` of each of them to all the customers of the rewards service, required the use of `AuthorizedVendor` contract.

This added a bit of complexity; and the solution adopted here is likely a non-typical use of the templates.

Perhaps this scenario could be handled via other design patterns.


### IV. Compiling & Testing

To compile and test, run the pre-written script in the `Tests.daml` under /daml OR run:
```
$ daml test
```

Use `daml start` to open the navigator.

This would run the `runHappyScenario1` to setup the accounts and run the workflow from start to end.

Note: The navigator ~~seems to have a bug~~ (its a known issue https://github.com/digital-asset/daml/issues/7648), as it does not show the `AuthorizedVendor` contracts to the customer users (`Alice_Customer` and `Bob_Customer`). (The same `setup` script used in the `daml test` works.)

Due to this issue it is not possible for the customers to approve the `SalesRequestDraft` via navigator.
