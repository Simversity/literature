---
layout: page
title: "Requests"
category: api
date: 2014-05-29 19:45:32
order: 3
---

#### Making API Requests

The output is {key: value} pair of the format {"urlname": "url_format"}
If the contains the %(variable_name)s it means that it needs to be substituted with a value.
Example: 

To find data corresponding to a Siminar the corresponding pair is: u'siminar': u'/jack/siminar/%(siminar_id)s'
To %(siminar_id)s in the URL needs to be replaced with the actual siminar_id you want the data for.

Example:

	out = con(
		"/jack/siminar/134936656289522642318098/",
		None,
		data={"apitoken": 2},
		method="GET"
	)

	import pprint; pprint.pprint(out)

Output is:

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