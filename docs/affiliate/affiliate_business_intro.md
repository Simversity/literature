### WHAT AN AFFILIATE LINKS LOOK LIKE

Link Example 
LANDS ON: Default Offer URL
```
http://click.siminars.com/aff_c?offer_id=12&aff_id=1000
```
Link Example - Different Landing Page
LANDS ON: Landing Page "X" 
```
http://click.siminars.com/aff_c?offer_id=12&aff_id=1000&url_id=2
```
Link Example With Custom Affiliate Data In Link (where only PART of the fields used)
LANDS ON: Default Offer URL
```
http://click.siminars.com/aff_c?offer_id=12&aff_id=1000&aff_sub=CUSTOMID1&aff_sub2=CUSTOMID2&aff_sub4=CUSTOMID4
```
Link Example With Custom Affiliate Data In Link (where ALL of the fields are used)
LANDS ON: Default Offer URL
```
http://click.siminars.com/aff_c?offer_id=12&aff_id=1000&source=FACEBOOK&aff_sub=CUSTOMID1&aff_sub2=CUSTOMID2&aff_sub3=CUSTOMID3&aff_sub4=CUSTOMID4&aff_sub5=CUSTOMID5
```

### COMMISSIONING MODELS
We will use a COMBINATION of conversion tracking, depending on how an offer needs to be paid out: 

    •    The TRANSACTION ID model lets us give a one time commission on a purchase.
    •    The AFFILIATE ID model lets us give recurring commissions for a program. 

The above is configured at the OFFER level. 

We have two commission models for our test: 

MODEL #1: Ars Amorata - Fixed One Time Payment --> TRANSACTION ID
Zan pays a 50$ commission to affiliates that join Ars Amorata. In this case: 
1) User clicks an affiliate link
2) A transaction ID is created
3) Transaction ID is consumed @ purchase
4) Commission is credited to the affiliate + transaction ID is consumed and cannot be re-used.

MODEL #2: Ageless You - Payments On Any Purchase In Offer --> AFFILIATE ID
We pay out on any purchase made from within this offer. In this case: 
1) User clicks an affiliate link
2) Commission is credited to the affiliate for any purchase within this offer.

### MINIMUM URI PARAMETERS PASSED TO SIMINARS HOPPER

MODEL #1 - Ars Amorata
Let's assume we have the Ars Amorata offer at http://siminars.com/hop/12345/ 

The DEFAULT offer URL (this is where the affiliate link will RESOLVE TO to in Has Offers) needs to be configured like this AT A MINIMUM:
```
http://siminars.com/hop/12345/?offer_id={offer_id}&affiliate_id={affiliate_id}&transaction_id={transaction_id}&file_id={offer_file_id}&source={source}&aff_sub={aff_sub}&aff_sub2={aff_sub2}&aff_sub3={aff_sub3}&aff_sub4={aff_sub4}&aff_sub5={aff_sub5}
```

Here's a sample of what this looks like in it's final form: 

Internet user sees a link to Ars Amorata and clicks it:
```
http://click.siminars.com/aff_coffer_id=12&aff_id=1000&source=OPRAH&aff_sub=PIYUSH&aff_sub2=MONEET&aff_sub3=NEERAJ&aff_sub4=THOMAS&aff_sub5=BHARAT
```

Has offers will redirect this way to us: 
```
http://siminars.com/hop/12345/offer_id=12&affiliate_id=1000&transaction_id=1026fd3bd1b5f32bc6af6f0d319500&file_id=0&source=OPRAH&aff_sub=PIYUSH&aff_sub2=MONEET&aff_sub3=NEERAJ&aff_sub4=THOMAS&aff_sub5=BHARAT
```

We grab the above, store it + redirect the user to the landing page specified in our hopper. 

MODEL #2 - Ageless You
Let's assume we have the Ageless You offer at http://siminars.com/hop/ABCDE/ 

The DEFAULT offer URL (this is where the affiliate link will RESOLVE TO to in Has Offers) needs to be configured like this AT A MINIMUM:
```
http://siminars.com/hop/ABCDE/?offer_id={offer_id}&affiliate_id={affiliate_id}&file_id={offer_file_id}&source={source}&aff_sub={aff_sub}&aff_sub2={aff_sub2}&aff_sub3={aff_sub3}&aff_sub4={aff_sub4}&aff_sub5={aff_sub5}
```

Here's a sample of what this looks like in it's final form: 

Internet user sees a link to Ageless You and clicks it:
```
http://click.siminars.com/aff_c?offer_id=18&aff_id=1000&aff_sub=JORDAN&aff_sub2=TEEKAY&aff_sub3=NEENEE&aff_sub4=MONEEE&aff_sub5=PEEPEE&source=OPRAH
```

Has offers will redirect this way to us: 
```
http://siminars.com/hop/ABCDE/?offer_id=18&affiliate_id=1000&file_id=0&source=OPRAH&aff_sub=JORDAN&aff_sub2=TEEKAY&aff_sub3=NEENEE&aff_sub4=MONEEE&aff_sub5=PEEPEE
```

We grab the above, store it + redirect the user to the landing page specified in our hopper. 

### LIST OF ALL URI PARAMETERS THAT CAN BE PASSED TO SIMINARS ON A CLICK
Affiliate Sub: Add {aff_sub} in the Offer URL and it will be replaced by the value of Affiliate Sub passed in via an affiliate tracking link.

Affiliate Sub 2: Add {aff_sub2} in the Offer URL and it will be replaced by the value of Affiliate Sub passed in via an affiliate tracking link.

Affiliate Sub 3: Add {aff_sub3} in the Offer URL and it will be replaced by the value of Affiliate Sub passed in via an affiliate tracking link.

Affiliate Sub 4: Add {aff_sub4} in the Offer URL and it will be replaced by the value of Affiliate Sub passed in via an affiliate tracking link.

Affiliate Sub 5: Add {aff_sub5} in the Offer URL and it will be replaced by the value of Affiliate Sub passed in via an affiliate tracking link.

Offer ID: Add {offer_id} in the Offer URL and it will be replaced by the ID of the Offer.

Offer Name: Add {offer_name} in the Offer URL and it will be replaced by the name of the Offer.

Offer URL ID: Add {offer_url_id} in the Offer URL and it will be replaced by the ID of the Offer URL used.

File ID: Add {offer_file_id} in the Offer URL and it will be replaced by the ID of the Offer File used.

File Name: Add {file_name} in the Offer URL and it will be replaced by the name of the Creative File used.

Advertiser ID: Add {advertiser_id} in the Offer URL and it will be replaced by the ID of the Advertiser of the offer.

Country Code: Add {country_code} in the Offer URL and it will be replaced by the user's country code (ISO 2-letter code, i.e. US).

Region Code: Add {region_code} in the Offer URL and it will be replaced by the user's region code (ISO 2-letter code, i.e. NY).

Custom Variables: Add {params} in the Offer URL if the Custom Variables option above is enabled. {params} will be replaced by the extra variables passed by the Affiliate into the affiliate tracking link.

Transaction ID: Add {transaction_id} in the Offer URL to send a unique transaction number to the Advertiser. Use this when trying to verify individual leads, to enable cookieless client-based tracking, or when server response 
tracking is required.

Date: Add {date} in the Offer URL and it will be replaced by current date formatted as YYYY-MM-DD.

Time: Add {time} in the Offer URL and it will be replaced by current time formatted as HH:MM:SS.

Date Time: Add {datetime} in the Offer URL and it will be replaced by current date and time formatted as YYYY-MM-DD HH:MM:SS.

IP: Add {ip} in the Offer URL and it will be replaced by IP address of the user clicking on the tracking link.

Offer Reference ID: Add {offer_ref} in the Offer URL and it will be replaced by the Reference ID of the offer.

Affiliate Reference ID: Add {affiliate_ref} in the Offer URL and it will be replaced by the Reference ID of the Affiliate promoting the offer.

Advertiser Reference ID: Add {advertiser_ref} in the Offer URL and it will be replaced by the Reference ID of the Advertiser of the offer.

### CONVERSION URLS TO PING
When a sale is completed, we need to ping a URL to notify His Offers this even happened. 

NOTE: The "adv_sub" field below will be used as a "secret key" validating that the notification came from our server. For example, all transaction notifications originating from our server will be tagged with "PUG5RUL3" - this value will never be known to affiliates and ensures that a sale confirmation originated form us. In addition to the above, we'll only accept conversions from our server IP. 

TRANSACTION ID SALE CONVERSION  NOTIFICATION (where the ID is consumed ONCE, then discarded) - BASIC FORMAT
IMPORTANT: This post back is UNIQUE TO THE OFFER.  Need a way to configure the "SPe" part.
```
http://click.siminars.com/SPe?adv_sub=SUB_ID&amount=AMOUNT&transaction_id={transaction_id}
```

To the above, we need to add whatever URI parameters have been captured for that user (the affiliate source, affiliate sub IDs, etc…). The amount is in DOLLARS and CENTS (i.e.: 10.99). The amount is OPTIONAL if we have a FIXED COMMISSION, but MANDATORY if we have a percentage split. 

AFFILIATE ID SALE CONVERSION  NOTIFICATION (for initial + recurring commission down the line) - BASIC FORMAT
IMPORTANT: This post back is UNIQUE TO THE OFFER.  Need a way to configure the "SP18" part. 
```
http://click.siminars.com/SP18?aff_id={affiliate_id}&adv_sub=SUB_ID&amount=AMOUNT
```

To the above, we need to add whatever URI parameters have been captured for that user (the affiliate source, affiliate sub IDs, etc…). The amount is in DOLLARS and CENTS (i.e.: 10.99). The amount is OPTIONAL if we have a FIXED COMMISSION, but MANDATORY if we have a percentage split. 
