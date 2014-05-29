---
layout: page
title: "URLs"
category: api
date: 2014-05-29 19:39:55
order: 2
---

You can generate a list of all the URLs exposed using the following call

	out = con("/urls", None, data={"apitoken": 2}, method="GET")
	print out["data"]


Output is:

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