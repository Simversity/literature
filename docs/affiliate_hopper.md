Router
======

Router is responsible for redirecting an Affiliate Link to the desired Marketing Landing Page.

**NOTE**
The under discussed flow should ONLY be executed when the Referer is a whitelisted subdomain of siminars.com which is CNAME'd to hasoffers.
Example: click.siminars.com

For all other visits, Router should redirect to the correspondong MLP without performing any routines.

&tldr;
------

    1. User clicks on HasOffer affiliate link click.siminars.com/xgh1234
    2. Browser is redirected to router at /router/xfghjde45?query=params
    3. System generates `user_ident`
    4. Update or Create an unfulfilled transaction.
    5. User is redirected to appropriate Marketing Landing Page.
    6. Anonymous user logs in.
    7. Acquire all transactions corresponding to user_ident
    8. User Completes Payment. For each paid cart_id issue completion on router.
    9. Router validates and relays a completion call on hasOffers.
    10. Mark all transactions {item_id, user_id} as fulfilled.

Backend
-------

Maintain a persistent database (preferably MongoDB or any other Disk-based DB)

Each Document in the collection looks like:

```
{
    id:  String,
    path:  String,
    item_id: String,
    ident: String,
    timestamp: Timestamp,
    is_fulfilled: Boolean,
    ho_transaction_id: String,
    query: Json
}
```
user_ident
----------

Since router is hosted on siminars.com/router it has access to the cookies.
Issue an API call to grab the user.

If the item_id corresponding to the current path has already been purchased by the user, redirect to the object itself. Skip the transit Marketing Landing Pages redirections.

If the user is anonymous, generate and set an anonymous_ident in the cookie.
anonymous_ident should be available for all anonymous siminars.com access via router, also it should only be generated if not present in cookie already.

Web & API Interface
-------------------

Until we start using the hasOffers API, Router will expose a web interface to allow generation of router links against the provided Marketing Landing Page URL.

**/list**

    * Method: GET
    * Output: List of all active Paths with corresponding MLP/Callback/FloorPrice

**/generate**

    * Method: POST
    * Input: item_id, MarketingLandingPage, CallbackURL, FloorPrice(optional)
    * Output: UniquePath (Without leading /) Ex. To be later used as http://siminars.com/router/WTY76R

**/delete**

    * Method: DELETE
    * Input: UniquePath (without the leading /)
    * Output: Delete the UniquePath from the Backend Database.

**/acquire**

    * METHOD: POST
    * Input: anonymous_ident, user_id
    * Update all transactions with {is_fulfilled:false, user_ident:anonymous_ident} to user_id.
    * Fetch user and all above transactions where item_id already purchased by user should be marked as fulfilled.

**/validate-item-id**

    * Method: POST
    * Input: item_id, user_id
    * Output: True/False If there is an unfulfilled transaction for {item_id, user_id}

**/fulfill**

    * Method: POST
    * Input: item_id, user_id
    * Mark all transactions with {item_id, is_fulfilled: false} as fulfilled.


**/complete**

    * Method: POST
    * Input: item_id, user_id, AmountPaid
    * Output: Fetch QueryParams relay complete call on hasOffers.

**/< UniquePath >**

    * Method: GET
    * Input: UniquePath (Without leading /)
    * Output: {"mlp": mlp_url, "item_id": item_id, "floor_price": floor_price}

PayOut Completion
-----------------

As early as a cart payment is completed. BackendServer (Most probably Party) should send raise /complete calls on the router.

Validations:

    * Amount > 0
    * Amount > FloorPrice
    * Amount > MinimumServiceFee

**Note**

If there are multiple unfulfilled items for {item_id, user_id} the recent most should be considered, since there could only be one payout corresponding to a single item purchase.

Refund Processing
-----------------

Add thomas on the refund alias. Such that everytime a user issues a refund He is aware of the payouts to be held back.

