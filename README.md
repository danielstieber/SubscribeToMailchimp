# SubscribeToMailchimp
Lightweight module for ProcessWire that let you subscribe a user to a mailchimp list.

I have only tested it with PHP7.x and Processwire 3.x.

## The basic idea
```PHP
// Easily subscribe a user with SubscribeToMailchimp
$mc = $modules->get("SubscribeToMailchimp");
$mc->subscribe('john.doe@example.com');
```

## How To Install
1. Download the [zip file](https://github.com/danielstieber/SubscribeToMailchimp/archive/master.zip) at Github or clone directly the repo into your `site/modules`
2. If you downloaded the `zip file`, extract it in your `sites/modules` directory.
3. You might have to change the folders name to 'SubscribeToMailchimp'.
4. Goto the modules admin page, click on refresh and install it.

## Setup
1. Log into your Mailchimp account and go to `Profile > Extras > API Keys`.
2. If you don't have an API Key, create a new one.
3. Copy your API Key and paste it in the module settings (`Processwire > Modules > Site > SubscribeToMailchimp`).
4. Back in Mailchimp, go to the Audience, where you want your new subscribers.
5. Go to `Settings > audience name and defaults`. Copy the Audience ID and paste it into the module settings.
6. Test your settings with the provided checkbox.

![module settings](https://i.imgur.com/37rJeGK.png)

## Usage
```PHP
// load module into template
$mc = $modules->get("SubscribeToMailchimp");

 // subscribe / update a user in your default audience
$mc->subscribe('john.doe@example.com');

// add merge_fields to fill out user data, based on your audience MERGE_FIELD options
// You need to setup the fields at "Settings > List fields and *|MERGE|* tags" first!
$mc->subscribe('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe']);

// Subscribe a user to a custom audience (other than default)
$mc->subscribe('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe'], 'adcdef12345');

// Work with additional parameters (not merge_field values!)
$mc->subscribe('john.doe@example.com', NULL, NULL, [
	'language' => 'en', // find language audience here: https://kb.mailchimp.com/lists/manage-contacts/view-and-edit-contact-languages#Language-Codes
	'vip' => true, // boolean vip status
	'location' => [ // geo location based on lat/log coordinates 
		'latitude' => '48.8722344',
		'longitude', => '2.7736192'
	],
	'interests' => [ // subscribe user to interest categories / groups based on group id
		'32fec3561e' => true, 
		'63mel4395m' => false
	]
]);
```

Additional methods
```PHP
// Unsubscribe a user
$mc->unsubscribe('john.doe@example.com'); 

// Unsubscribe a user from a custom audience
$mc->unsubscribe('john.doe@example.com', 'abcdef1356');

// Permanently delete a user. Carefully, this step cannot be undone!
$mc->delete('john.doe@example.com');

// Get the subscription status of a user.
$mc->getStatus('john.doe@example.com');

// Static function to test your settings.
// If no audience id is provided, it will test your default audience and returns a formated html result
$mc->testSettings('audience id');

```

## Important Notes
* This module does not do any data validation. Use a sever-sided validation like [Valitron](https://github.com/vlucas/valitron)
* Make sure that you have set up your fields in your Mailchimp audience. You can do it at `Settings > Audience fields and *|MERGE|* tags` 

## Example
Example usage after a form is submitted on your page:
```PHP
// ... validation of form data

$mc = $modules->get("SubscribeToMailchimp");
$email = $input->post->email;
$subscriber = [
  'FNAME' => $input->post->firstname,
  'LNAME' => $input->post->lastname,
];
$mc->subscribe($email, $subscriber);

```

## Troubleshooting 
In case of trouble check your ProcessWire warning logs.

### FAQ
#### I can't see the subscriber in the audience
If you have enabled double opt-in (it is enabled by default) you will not see the subscriber, until he confirmed the subscription in the email sent by Mailchimp

#### I get an error in my ProccessWire warning logs
* Check if you have the right audience ID and API Key.
* Check if you pass fields, that exist in your audience.
* Check if you pass a valid email address.

Go to [Mailchimps Error Glossary](https://developer.mailchimp.com/documentation/mailchimp/guides/error-glossary/) for more Information

## Changelog
_Until now, you can update from every version without changing your code_
### 0.0.5 (Juli 11, 2019)
* [#8](https://github.com/danielstieber/SubscribeToMailchimp/issues/8): Fixed a bug, that prevents re-subscribing, if double-top in was disabled – thanks to [@jliebermann](https://github.com/jliebermann) for pointing this out
* [#7](https://github.com/danielstieber/SubscribeToMailchimp/issues/7): Removed the 'warning', that was logged appeared when trying to add a new subscriber – thanks to [@horst-n](https://github.com/horst-n) for mentioning
* Updated wording from 'list' to 'audience' ([Introducing Audience](https://mailchimp.com/resources/introducing-your-new-audience-dashboard/))

### 0.0.4 (May 27, 2018)
* [#6](https://github.com/danielstieber/SubscribeToMailchimp/pull/6): Fixed a bug, where you couldn't use the 'ADDRESS' merge field and removed more unused lines - Big thanks to [@ml-eds](https://github.com/ml-eds) for the quick fix!
* [#4](https://github.com/danielstieber/SubscribeToMailchimp/issues/4): Deleted unused line

### 0.0.3 (May 17, 2018)
* [#3](https://github.com/danielstieber/SubscribeToMailchimp/issues/3): Updated **subscribe** method. You can now add additional parameters like language or interests. Find more about this in the [Mailchimp API Documentation](http://developer.mailchimp.com/documentation/mailchimp/reference/lists/members/#create-post_lists_list_id_members)
* [#2](https://github.com/danielstieber/SubscribeToMailchimp/pull/2): Added **getStatus** method `$mc->getStatus($email, $list = "")`
* [#2](https://github.com/danielstieber/SubscribeToMailchimp/pull/2): Added ability to resubscribe a user, if he has unsubscribed. The user will have to confirm his subscription again via double-opt-in (if not disabled)
* [#1](https://github.com/danielstieber/SubscribeToMailchimp/pull/1): Added **testSetting** method and a beautiful way to test your settings right in the module settings pages - Big thanks to [@horst-n](https://github.com/horst-n)

* Centralized the validation warning via a constant
* Updated the **getApiBase** to be more flexible with different API calls

### 0.0.2 (April 24, 2018)
* Added 'Unsubscribe' method `$mc->unsubscribe($email, $list = "")`
* Added 'Delete' method `$mc->delete($email, $list = "")`
* Removed type declarations to be compatible with PHP 5.1* (thanks to wbmnfktr)
* Changed the way, the base url for the api gets called

\*I have only tested it with PHP 7.x so far, so use on owners risk :)

## Contribution
Any suggestions? Join the [Discussion](https://processwire.com/talk/topic/19023-subscribe-to-mailchimp/?tab=comments#comment-165449) in the ProcessWire Forums or contribute directly on Github.