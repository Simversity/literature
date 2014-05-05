##Introduction##

API is the primary interface to Siminars' Data.

It is mostly RESTful, barring a few old requests, and most resources can be accessed using GET, PUT, POST & DELETE requests.

## API Connection ##

Through out this documentation we will assume that you have an active connection to the API Server.
You can create a new connection using various types of HTTPClients exposed by simtools.uhttpclient.

For simplicity's sake we will use BlockingHTTPClient in this example.

```
from simtools.uhttpclient import BlockingHTTPClient

con = BlockingHTTPClient("/tmp/api.sock") #If listening on a Unix Domain Socket.
con = BlockingHTTPClient("127.0.0.1:{PORT}") #If listening on a TCP Port of your choice. Default is 8000.
```

## Content-Type ##
The API uses HTTP calls for all communication and only accepts Content-Type: application/javascript as a valid content-type.

## Apitoken ##
All API requests require a valid apitoken. In DEBUG mode you can use apitoken=2 to accesss all the data.


## Data Return Format ##
API's return is always a JSON dict of the format
```
{
	"code": <int>,
	"data": <dict>,
	"error": <HTTPErrorinstance>, //Optional
	"message": <string> //Optional
}
```

Code is the HTTP Status Code for the corresponding action.

In case of and Error data would always be None and message would contain the reason of the failure.
In case everything is ok and there is no Error, both error & message would be None.

### Status Codes ###

Status codes used with the corresponding Integer value.

- OK = PASS = 200
- CREATED = 201
- ACCEPTED = 202
- RESET = 205
- SEE_OTHER = 303
- LOGIN_REQUIRED = 401
- PAYMENT_REQUIRED = 402
- ACCESS_DENIED = 403
- NOT_FOUND = 404
- NOT_ALLOWED = 405
- CONFLICT = 409
- NULL_PARAM = 412
- CONDITION_FAIL = 412
- INVALID_PARAM = 412
- FAIL = 500
- INTERNAL_ERROR = 500

## URLS ##
You can generate a list of all the URLs exposed using the following call

```
out = con("/urls", None, data={"apitoken": 2}, method="GET")
print out["data"]

```

Output is:
```
{u'data': {
    u'urls': {
        u'agora_delete': u'/delete',
        u'agora_get': u'/get',
        u'all_bundles': u'/jack/bundles',
        u'asset': u'/asset/%(asset_id)s',
        u'associate': u'/connect/%(service)s',
        u'authtoken': u'/authtoken',
        u'bundle': u'/bundle/%(bundle_id)s',
        u'bundle_siminars': u'/bundle/%(bundle_id)s/bundle_siminars',
        u'bundle_users': u'/bundle/%(bundle_id)s/%(role)s',
        u'bundles': u'/bundles/%(plan_id)s',
        u'cart': u'/account/cart',
        u'cname': u'/custom/cname',
        u'coupon': u'/coupon',
        u'custom_url': u'/custom/urls/%(url_id)s',
        u'custom_urls': u'/custom/urls',
        u'feedback': u'/feedback',
        u'feedback_questions': u'/feedback/questions',
        u'feeds': u'/feed',
        u'invalidate': u'/invalidate',
        u'jaction': u'/%(handle)s/%(object_id)s',
        u'jontent': u'/jack/jontent/%(jontent_id)s',
        u'jontents': u'/jack/jontents',
        u'notify': u'/notify',
        u'one_time_token': u'/one-time-token',
        u'owner_assets': u'/owner/%(owner_id)s/assets',
        u'payment_accounts': u'/account/payment',
        u'payout_accounts': u'/account/payout',
        u'per_program_user': u'/user/%(user_id)s',
        u'plan': u'/plan/%(plan_id)s',
        u'plan_manager': u'/plan/manager',
        u'plan_templates': u'/plan/templates',
        u'pricing': u'/price',
        u'probable_siminars': u'/bundle/%(bundle_id)s/probable_siminars',
        u'program_user': u'/user',
        u'quizresult': u'/quizresult/%(object_id)s',
        u'relations': u'/relation/%(user_id)s',
        u'session': u'/session/%(session_id)s',
        u'session_users': u'/session/%(session_id)s/%(role)s',
        u'sessions': u'/series/%(bundle_id)s/sessions',
        u'siminar': u'/jack/siminar/%(siminar_id)s',
        u'siminar_users': u'/siminar/%(siminar_id)s/%(role)s',
        u'siminars': u'/jack/siminars',
        u'step': u'/jack/step/%(step_id)s',
        u'steps': u'/jack/siminar/%(siminar_id)s/steps',
        u'suggestions': u'/suggestions',
        u'testimonials': u'/testimonial/%(object_id)s',
        u'ticker': u'/ticker',
        u'transfer': u'/plan/transfer',
        u'urls': u'/urls',
        u'user_notifications': u'/account/notifications'
    }
},
u'message': u'',
u'status': 200}

```

### Making API Requests ###

The output is {key: value} pair of the format {"urlname": "url_format"}
If the contains the %(variable_name)s it means that it needs to be substituted with a value.
Example: 

To find data corresponding to a Siminar the corresponding pair is: u'siminar': u'/jack/siminar/%(siminar_id)s'
To %(siminar_id)s in the URL needs to be replaced with the actual siminar_id you want the data for.

Example:
```
out = con("/jack/siminar/134936656289522642318098/", None, data={"apitoken": 2}, method="GET")
import pprint; pprint.pprint(out)
```

Output is:
```
{'code': 200,
 'data': {u'data': {u'siminar': {
    u'CreatedBy': u'',
    u'CreatedOn': 0,
    u'ModifiedBy': u'',
    u'ModifiedOn': 0,
    u'SiminarId': u'',
    u'_id': u'134936656289522642318098',
    u'admin_id': u'133071947969939498163702',
    u'allow_unverified_comments': False,
    u'author': None,
    u'author_id': u'133071947969939498163702',
    u'banner_url': u'http://test.simversity.com.s3.amazonaws.com/photo/138143727483366379669305.png',
    u'blurb': u'',
    u'bundle_ids': [],
    u'deleted': False,
    u'description': u"<p>let's find out ...</p>",
    u'done': {},
    u'enforce_sequence': u'',
    u'featured_book': [u'136876039914810979336852'],
    u'free_steps': 3,
    u'has_completed': False,
    u'has_started': False,
    u'hidden_step_dict': {},
    u'instructors': [u'133071947969939498163702'],
    u'invisible_instructors': [u'132985648455586944663702'],
    u'is_closed': False,
    u'is_listed': True,
    u'is_paid': True,
    u'is_participation_moderated': False,
    u'is_pro': True,
    u'is_shared': True,
    u'is_verified': False,
    u'keep_access': u'',
    u'layout': u'two_column',
    u'list_price': 20,
    u'locked_to_context': u'',
    u'logo_url': u'http://static.siminars.com/thumbnail/mongodb-sq-png-200x200.png',
    u'marketing_facilitator': 0,
    u'marketing_featured_book': 0,
    u'oldest_step': 0,
    u'oldest_step_id': u'',
    u'plan_author_id': u'133071947969939498163702',
    u'plan_id': u'136798989944706737735425',
    u'promotional_video_asset_id': u'',
    u'purchase_price': 12,
    u'role': True,
    u'session_ids': [],
    u'show_outline': False,
    u'show_title_over_banner': False,
    u'siminar_ids': None,
    u'siminar_type': u'retail',
    u'status': 12,
    u'step_dict': {
        u'134936656326335937354962': {
            u'_id': u'134936656326335937354962',
            u'can_visit': True,
            u'curated_count': 0,
            u'description': u'Why did we chose Mongo over Other Databases ?',
            u'eta': 0,
            u'hidden': False,
            u'last_item': u'',
            u'sequence': 2,
            u'title': u'Choosing & Setting-Up the right Database',
            u'todo_count': 0,
            u'visited_on': False
        },
        u'134937296241062751654962': {
            u'_id': u'134937296241062751654962',
            u'can_visit': True,
            u'curated_count': 0,
            u'description': u'Components and Resources behind the Task Server, explained.',
            u'eta': 0,
            u'hidden': False,
            u'last_item': u'',
            u'sequence': 1,
            u'title': u'Introduction to the Task Server',
            u'todo_count': 0,
            u'visited_on': False
        },
        u'134943845527824879854962': {
            u'_id': u'134943845527824879854962',
            u'can_visit': True,
            u'curated_count': 0,
            u'description': u'Detailed Snippets of the Code.',
            u'eta': 0,
            u'hidden': False,
            u'last_item': u'',
            u'sequence': 3,
            u'title': u'Explaining the Codes',
            u'todo_count': 0,
            u'visited_on': False
        },
        u'134946526481315149154962': {
            u'_id': u'134946526481315149154962',
            u'can_visit': True,
            u'curated_count': 0,
            u'description': u'Proposed enhancements',
            u'eta': 0,
            u'hidden': False,
            u'last_item': u'',
            u'sequence': 5,
            u'title': u'ToDo',
            u'todo_count': 0,
            u'visited_on': False
        },
        u'134963289888335063154962': {
            u'_id': u'134963289888335063154962',
            u'can_visit': True,
            u'curated_count': 0,
            u'description': u'The system exposes TasksCleanup & RecurringTasks, both of which have been implemented using the base architecture.',
            u'eta': 0,
            u'hidden': False,
            u'last_item': u'',
            u'sequence': 4,
            u'title': u'Extras: Goodies implemented using the back stack.',
            u'todo_count': 0,
            u'visited_on': False
        }
    },
    u'steps': [
        u'134937296241062751654962',
        u'134936656326335937354962',
        u'134943845527824879854962',
        u'134963289888335063154962',
        u'134946526481315149154962'
    ],
    u'steps_num': 5,
    u'students': {},
    u'students_pending': {},
    u'style': u'step',
    u'subtitle': u'Concurrent Task Processor',
    u'testimonial': None,
    u'title': u'Build a High Capacity Task Processor with MongoDB',
    u'user_is_instructor': False,
    u'user_is_nobody': True,
    u'user_is_student': False,
    u'user_is_unapproved': False,
    u'user_needs_signup': True,
    u'visited_by': {}
    }},
    u'message': u'',
    u'status': 200
},
'error': False,
'message': None}

```
## Authentication ##

Most API requests would require a valid authtoken. A request is validated by supplying "authtoken" along with the other data in the submitted call.

Use cases:
 - Leave blank for anonymous view of a particular request.
 - Different authtokens can be used to have different views as per the user.

Examples:
 - An unlaunched Siminar can only be viewed by the Siminar Facilitator. If the right authtoken is not supplied it will raise 404.
 - A paid siminar will raise 405 if the authtoken supplied is of a non-member.
 - WHile fetching responses authtoken supplied will identify the user and show/hide private responses accordingly.

Supplying the right authtoken is very important and they must be handled with full attention and care.

### Using Username/Password ###

 - Urlname: authtoken
 - Method: POST
 - Arguments: email, password

Where:
email is the email address of the user (case insensitive)
password is a md5 of username and user's clear-text password.

The password encryption is important because our databases do not store or accept clear-text-passwords and we discourage the transfer of sensitive clear-text information over the wire.

Simtools exposes a method to generate the encrypted_password using email and clear-text password.

```python
from simtools.school import make_md5_password
encrypted_password = make_md5_password(email, password)
```

The actual algorithm is:

 - Convert Email to lower case
 - Convert to BaseString
 - Concatenate username and password
 - Generate a hash using md5 algorithm.
 - return hexdigest() of the hash output.

NOTE:

hexdigest returns a digest of the hash, where the output only contains hexadecimal digits. This may be used to exchange the value safely in email or other non-binary environments.


Example:

If the username is piyush@siminars.com and the password is Hello,
an authtoken can be generated using:

```python
from simtools.school import make_md5_password

email = "piyush@siminars.com"
password = "Hello"

encrypted_password = make_md5_password(email, password)

auth_data = {
    "apitoken": 2,
    "email": email,
    "password": encrypted_password
}

out = con("/authtoken", None, data=auth_data, method="POST")
print out["data"]

```

Output carries the authtoken and the user_id of the just authenticated user:
```
{
    u'status': 201,
    u'message': u'',
    u'data': {
        u'authtoken': u'139927583041014080855057',
        u'user_id': u'133550072236313429963702'
    }
}
```

However, if you provide an incorrect username and password. API would respond with an error that looks like:
```
{
    'message': u'Invalid Username or Password.',
    'code': 412,
    'data': None,
    'error': HTTPError(u'HTTP 412: Invalid Username or Password.')
}
```

### Using 3rd Party Services like Facebook/ Google/ LinkedIn ###

_NOTE: LinkedIn support shall be dropped soon_

(.... continued ...)

### LogOut ###
 - Urlname: authtoken
 - Method: DELETE
 - Arguments: authtoken

Do note that Authtokens are valid for 6 months until you logout using the same authoken.

Does not Exist [TODO]:

 - Invalidate ALL the authtokens of a user. You can only invalidate them one-by-one.
 - List all active authtokens of a user.

### Forgot Password/ Reset Email ###

API does not expose a way to forget password and rest the primary email. It can only be done using the web interface.