---
layout: page
title: "Authentication"
category: api
date: 2014-05-29 19:39:59
order: 4
---

#### Authentication

Most API requests would require a valid authtoken. A request is validated by supplying "authtoken" along with the other data in the submitted call.

Use cases:

 - Leave blank for anonymous view of a particular request.
 - Different authtokens can be used to have different views as per the user.

Examples:

 - An unlaunched Siminar can only be viewed by the Siminar Facilitator. If the right authtoken is not supplied it will raise 404.
 - A paid siminar will raise 405 if the authtoken supplied is of a non-member.
 - WHile fetching responses authtoken supplied will identify the user and show/hide private responses accordingly.

Supplying the right authtoken is very important and they must be handled with full attention and care.

#### Using Username/Password

 - Urlname: authtoken
 - Method: POST
 - Arguments: email, password

Where:

	email is the email address of the user (case insensitive)
	password is a md5 of username and user's clear-text password.

The password encryption is important because our databases do not store or accept clear-text-passwords and we discourage the transfer of sensitive clear-text information over the wire.

Simtools exposes a method to generate the encrypted_password using email and clear-text password.

	from simtools.school import make_md5_password
	encrypted_password = make_md5_password(email, password)

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

~~~ python
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
~~~

Output carries the authtoken and the user_id of the just authenticated user:

	{
	    u'status': 201,
	    u'message': u'',
	    u'data': {
	        u'authtoken': u'139927583041014080855057',
	        u'user_id': u'133550072236313429963702'
	    }
	}

However, if you provide an incorrect username and password. API would respond with an error that looks like:

	{
	    'message': u'Invalid Username or Password.',
	    'code': 412,
	    'data': None,
	    'error': HTTPError(u'HTTP 412: Invalid Username or Password.')
	}

#### Using 3rd Party Services like Facebook/ Google/ LinkedIn

_NOTE: LinkedIn support shall be dropped soon_

(.... continued ...)

#### LogOut

 - Urlname: authtoken
 - Method: DELETE
 - Arguments: authtoken

Do note that Authtokens are valid for 6 months until you logout using the same authoken.

Does not Exist [TODO]:

 - Invalidate ALL the authtokens of a user. You can only invalidate them one-by-one.
 - List all active authtokens of a user.

#### Forgot Password/ Reset Email

API does not expose a way to forget password and rest the primary email. It can only be done using the web interface.