
# SubscribeToMailchimp
Lightweight module for ProcessWire that let you subscribe a user to a mailchimp list.

I have only tested it with PHP7.x and Processwire 3.x.

## How To Install
1. Download the [zip file](https://github.com/danielstieber/SubscribeToMailchimp/archive/master.zip) at Github or clone directly the repo into your `site/modules`
2. If you downloaded the `zip file`, extract it in your `sites/modules` directory.
3. You might have to change the folders name to 'SubscribeToMailchimp'.
4. Goto the modules admin page, click on refresh and install it.

## Setup
1. Log into your Mailchimp account and go to `Profile > Extras > API Keys`.
2. If you don't have an API Key, create a new one.
3. Copy your API Key and paste it in the module settings (`Processwire > Modules > Site > SubscribeToMailchimp`).
4. Back in Mailchimp, go to the list, where you want your new subscribers.
5. Go to `Settings > List name and defaults`. Copy the List ID an paste it into the module settings.

![module settings](https://i.imgur.com/RcKqzEt.png)

## Usage
To use the module, you need to load it into your template:
```PHP
$mc = $modules->get("SubscribeToMailchimp");
```
Now you can pass an email address to the module and it will try to edit (if the user exists) or create a new subscriber in your list.
```PHP
$mc->subscribe('john.doe@example.com');
```
You can also pass a data array, to add additional info.
```PHP
$mc->subscribe('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe']);
```
You can even choose an alternative list, if you don't want this subscriber in your default list.
```PHP
$mc->subscribe('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe'], 'abcdef1356'); // Subscribe to list ID abcdef1356
```
If you want to unsubscribe a user from a list, you can use the unsubscribe method.
```PHP
$mc->unsubscribe('john.doe@example.com'); // Unsubscribe john.doe@example.com from the default list
$mc->unsubscribe('john.doe@example.com', 'abcdef1356'); // Unsubscribe john.doe@example.com from the list abcdef1356
```
If you want to permantly delete a user, you can call the delete method. Carefully, this step cannot be undone!
```PHP
$mc->delete('john.doe@example.com'); // Permanently deletes john.doe@example.com from the default list
$mc->delete('john.doe@example.com', 'abcdef1356'); // Permanently deletes john.doe@example.com from the list abcdef1356
```
If you just want to get a subscribers status, you can use the getStatus method. I do not suggest to implement this in your forms like 'this user is already subscribed' to protect the anonymity of your users. You can learn more about status labels [here](http://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/#check-subscription-status) 
```PHP
$mc->getStatus('john.doe@example.com'); // Returns the status of a user. 
```
## Important Notes
* This module does not do any data validation. Use a sever-sided validation like [Valitron](https://github.com/vlucas/valitron)
* Make sure that you have set up your fields in your Mailchimp list. You can do it at `Settings > List fields and *|MERGE|* tags` 

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
#### I can't see the subscriber in the list
If you have enabled double opt-in (it is enabled by default) you will not see the subscriber, until he confirmed the subscription in the email sent by Mailchimp

#### I get an error in my ProccessWire warning logs
* Check if you have the right List ID and API Key.
* Check if you pass fields, that exist in your list.
* Check if you pass a valid email address.

Go to [Mailchimps Error Glossary](https://developer.mailchimp.com/documentation/mailchimp/guides/error-glossary/) for more Information

## Changelog
### 0.0.3
_Note: You can update savely from 0.0.2 without any changes in your code_
* Added 'getStatus' method `$mc->getStatus($email, $list = "")`
* Added ability to resubscribe a user, if he has unsubscribed. The user will have to confirm his subscription again via double-opt-in (if not disabled)
#### Other
* Centralized the validation warning via a constant
### 0.0.2
_Note: You can update savely from 0.0.1 without any changes in your code_
#### New Features
* Added 'Unsubscribe' method `$mc->unsubscribe($email, $list = "")`
* Added 'Delete' method `$mc->delete($email, $list = "")`
#### Bug Fixes and compatibility changes
* Removed type declarations to be compatible with PHP 5.1* (thanks to wbmnfktr)
#### Other
* Changed the way, the base url for the api gets called

\*I have only tested it with PHP 7.x so far, so use on owners risk :)


## Contribution
Any suggestions? Join the [Discussion](https://processwire.com/talk/topic/19023-subscribe-to-mailchimp/?tab=comments#comment-165449) in the ProcessWire Forums or contribute directly on Github.