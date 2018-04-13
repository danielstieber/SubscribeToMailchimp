# SubscribeToMailchimp
Lightweight module for ProcessWire that let you subscribe a user to a mailchimp list.

## How To Install
1. Download the [zip file](https://github.com/danielstieber/SubscribeToMailchimp/archive/master.zip) at Github or clone directly the repo into your `site/modules`
2. If you dowloaded the `zip file`, extract it in your `sites/modules` directory.
3. You might have to change the folders name to 'SubscribeToMailchimp'.
4. Goto the modules admin page, click on refresh and install it.

## Setup
1. Log into your Mailchimp account and go to `Profile > Extras > API Keys`.
2. If you don't have an API Key, create a new one.
3. Copy your API Key and paste it in the module settings (`Processwire > Modules > Site > SubscribeToMailchimp`).
4. Back in Mailchimp, go to the list, where you want your new subscribers.
5. Go to `Settings > List name and defaults`. Copy the List ID an paste it in to the module settings.

![module settings](https://i.imgur.com/RcKqzEt.png)

## Usage
To use the module, you need to load it into you template:
```PHP
$mc = $modules->get("SubmitToMailchimp");
```
Now you can pass an email adress to the module and it will try to edit (if the user exists) or create a new subscriber in your list.
```PHP
$mc->submitForm('john.doe@example.com');
```
You can also pass a data array, to add additional info.
```PHP
$mc->submitForm('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe']);
```
You can even choose an alternative list, if you don't want this subscriber in your default list.
```PHP
$mc->submitForm('john.doe@example.com', ['FNAME' => 'John', 'LNAME' => 'Doe'], 'abcdef1356'); // Subscribe to List ID abcdef1356
```

## Important Notes
* Use a sever-sided Validation like [Valitron](https://github.com/vlucas/valitron) before submitting fields
* Make sure that you have set up your fields in your mailchimp list. You can do it at `Settings > List fields and *|MERGE|* tags`.  

## Example
Example usage after a form is submitted on your page:
```PHP
// ... validation of form data

$mc = $modules->get("SubmitToMailchimp");
$email = $input->post->email;
$subscriber = [
  'FNAME' => $input->post->firstname,
  'LNAME' => $input->post->lastname,
];
$mc->submitForm($email, $subscriber);

```

## Troubleshooting 
In case of trouble check your ProcessWire warning logs.

### I can't see the subscriber in the list
If you have enabled double opt-in (it is enabled by default) you will only see the 

### I get an error i my ProccessWire warning
* Check if you have the right List ID and API Key.
* Check if you pass fields, that exist in you list.
* Check if you pass a valid emaild adress.

Go to [Mailchimps Error Glossary](https://developer.mailchimp.com/documentation/mailchimp/guides/error-glossary/) for more Information
