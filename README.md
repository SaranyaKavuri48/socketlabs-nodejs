[![SocketLabs](https://static.socketlabs.com/logos/logo-dark-326x64.png)](https://www.socketlabs.com/developers)
# [![Twitter Follow](https://img.shields.io/twitter/follow/socketlabs.svg?style=social&label=Follow)](https://twitter.com/socketlabs) [![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/socketlabs/socketlabs-nodejs/blob/master/CONTRIBUTING.md)

The SocketLabs Email Delivery Node.js library allows you to easily send email messages via the [SocketLabs Injection API](https://www.socketlabs.com/docs/inject/).  The library makes it easy to build and send any type of message supported by the API, from a simple message to a single recipient all the way to a complex bulk message sent to a group of recipients with unique merge data per recipient.

# Table of Contents
* [Prerequisites and Installation](#prerequisites-and-installation)
* [Getting Started](#getting-started)
* [Managing API Keys](#managing-api-keys)
* [Examples and Use Cases](#examples-and-use-cases)
* [License](#license)


<a name="prerequisites-and-installation"></a>
# Prerequisites and Installation
## Prerequisites
* A supported Node.js version (`6.0` or higher)
* A SocketLabs account. If you don't have one yet, you can [sign up for a free account](https://signup.socketlabs.com/step-1?plan=free) to get started.

## Installation
For most uses we recommend installing the SocketLabs Email Delivery package via [npm](https://npmjs.org). If you already have a supported version of Node.js,
then it should have included npm automatically. To install the package to your current project, simply type the following in your terminal or command prompt:

```
> npm install --save @socketlabs/email
```

Alternately, you can simply [clone this repository](https://github.com/socketlabs/socketlabs-nodejs.git) directly to include the source code in your project.

<a name="getting-started"></a>
# Getting Started
## Obtaining your API Key and SocketLabs ServerId number
In order to get started, you'll need to enable the Injection API feature in the [SocketLabs Control Panel](https://cp.socketlabs.com).
Once logged in, navigate to your SocketLabs server's dashboard (if you only have one server on your account you'll be taken here immediately after logging in).
Make note of your 4 or 5 digit ServerId number, as you'll need this along with
your API key in order to use the Injection API.

To enable the Injection API, click on the "For Developers" dropdown on the top-level navigation, then choose the "Configure HTTP Injection API" option.
Once here, you can enable the feature by choosing the "Enabled" option in the
dropdown. Enabling the feature will also generate your API key, which you'll
need (along with your ServerId) to start using the API. Be sure to click the
"Update" button to save your changes once you are finished.

## Basic Message
A basic message is an email message like you'd send from a personal email client such as Outlook.
A basic message can have many recipients, including multiple To addresses, CC addresses, and even BCC addresses.
You can also send a file attachment in a basic message.

```js
const {SocketLabsClient} = require('@socketlabs/email');

const client = new SocketLabsClient(parseInt(process.env.SOCKETLABS_SERVER_ID), process.env.SOCKETLABS_INJECTION_API_KEY);

const message = {
    to: "recipient@example.com",
    from: "sender@example.com",
    subject: "Hello from Node.js",
    textBody: "This message was sent using the SocketLabs Node.js library!",
    htmlBody: "<html>This message was sent using the SocketLabs Node.js library!</html>",
    messageType: 'basic'
}

client.send(message);
```

A basic message supports up to 50 recipients and supports several different ways to add recipients. We've also provided several helper classes
to help you construct your message more easily:

```js
const {SocketLabsClient, BasicMessage, EmailAddress} = require('@socketlabs/email');

const client = new SocketLabsClient(parseInt(process.env.SOCKETLABS_SERVER_ID), process.env.SOCKETLABS_INJECTION_API_KEY);

//Instantiate the BasicMessage class to use built-in properties and helper methods. No need to set the messageType when using
//the helper classes.
let basicMessage = new BasicMessage();

basicMessage.from = "sender@example.com";
basicMessage.subject = "Hello from Node.js";
basicMessage.textBody = "This message was sent using the SocketLabs Node.js library!";

//Add a recipient by pushing a string to the To array
basicMessage.to.push("recipient1@example.com");

//Add a recipient by pushing an object literal to the To array
basicMessage.to.push({emailAddress: "recipient2@example.com", friendlyName: "Recipent #2"});

//Add a recipient by pushing an EmailAddress object to the To array
basicMessage.to.push(new EmailAddress("recipient3@example.com", { friendlyName: "Recipient #3" }));

//Use the helper method on the instantiated BasicMessage
basicMessage.addToEmailAddress("recipient4@example.com", "Recipient #4");

//CC and BCC addresses have all of the same options
basicMessage.cc.push("cc@example.com");
basicMessage.addBccEmailAddress("bccRecipient@example.com");

client.send(message);
```

## Bulk Message
A bulk message usually contains a single recipient per message
and is generally used to send the same content to many recipients,
optionally customizing the message via the use of MergeData.
For more information about using Merge data, please see the [Injection API documentation](https://www.socketlabs.com/api-reference/injection-api/#merging).
```js
const {SocketLabsClient, BulkMessage, BulkRecipient} = require('@socketlabs/email');

const client = new SocketLabsClient(parseInt(process.env.SOCKETLABS_SERVER_ID), process.env.SOCKETLABS_INJECTION_API_KEY);

let bulkMessage = new BulkMessage();

bulkMessage.textBody = "Is your favorite color still %%FavoriteColor%%?";
bulkMessage.htmlBody = "<html>Is your favorite color still %%FavoriteColor%%?";
bulkMessage.subject = "Sending a Bulk Message With Merge Fields";
bulkMessage.setFrom("from@example.com");

//Use built-in classes and helper methods
let recipient1 = new BulkRecipient("recipient1@example.com");
recipient1.addMergeData("FavoriteColor", "Green");
bulkMessage.to.push(recipient1);

//Use object literals
const recipient2 = {
    emailAddress: "recipient2@example.com",
    friendlyName: "Recipient #2",
    mergeData: [
        { key: "FavoriteColor", value: "Orange" }
    ]
});

bulkMessage.to.push(recipient2);

client.send(bulkMessage);
```

<a name="managing-api-keys"></a>
## Managing API Keys
For ease of demonstration, some of our examples may include the ServerId and API
key directly in our code sample. Generally it is not considered a good practice
to store sensitive information like this directly in your code. In most cases
we recommend the use of [Environment Variables](https://medium.freecodecamp.org/heres-how-you-can-actually-use-node-environment-variables-8fdf98f53a0a).


<a name="examples-and-use-cases"></a>
# Examples and Use Cases
In order to demonstrate the many possible use cases for the Node.js library, we've provided
an assortment of code examples. These examples demonstrate many different
features available to the Injection API and Node.js library, including using templates
created in the [SocketLabs Email Designer](https://www.socketlabs.com/blog/introducing-new-email-designer/), custom email headers, sending
attachments, sending content that is stored in an HTML file, advanced bulk
merging, and even pulling recipients from a datasource.

### [Basic send from SocketLabs Template](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithApiTemplate.js)
This example demonstrates the sending of a piece of content that was created in the
SocketLabs Email Designer. This is also known as the [API Templates](https://www.socketlabs.com/blog/introducing-api-templates/) feature.

### [Basic send from HTML file](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendFromHtmlFile.js)
This example demonstrates how to read in your HTML content from an HTML file
rather than passing in a string directly.

### [Basic send with file attachment](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithAttachment.js)
This example demonstrates how to add a file attachment to your message.

### [Basic send with embedded image](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithEmbeddedImage.js)
This example demonstrates how to embed an image in your message.

### [Basic send with specified character set](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithASCIICharset.js)
This example demonstrates sending with a specific character set.

### [Basic send with custom email headers](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithCustomHeaders.js)
This example demonstrates how to add custom headers to your email message.

### [Basic send with a web proxy](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendWithProxy.js)
This example demonstrates how to use a proxy with your HTTP client.

### [Basic send complex example](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/basicSendComplexExample.js)
This example demonstrates many features of the Basic Send, including adding multiple recipients, adding message and mailing id's, and adding an embedded image.

### [Basic send with Amp ](https://github.com/socketlabs/socketlabs-nodejs/blob/main/examples/basic/basicSendWithAmpBodyExample.js)
This example demonstrates how to send a basic message with an AMP Html body.
For more information about AMP please see [AMP Project](https://amp.dev/documentation/)

### [Basic send with invalid file attachment](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/invalid/basicSendWithInvalidAttachment.js)
This example demonstrates the results of attempting to do a send with an invalid attachment.

### [Basic send with invalid from address](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/invalid/basicSendWithInvalidFrom.js)
This example demonstrates the results of attempting to do a send with an invalid from address.

### [Basic send with invalid recipients](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/basic/invalid/basicSendWithInvalidRecipients.js)
This example demonstrates the results of attempting to do a send with invalid recipients.

### [Bulk send with multiple recipients](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/bulk/bulkSend.js)
This example demonstrates how to send a bulk message to multiple recipients.

### [Bulk send with complex merge including attachments](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/bulk/bulkSendComplexExample.js)
This example demonstrates many features of the `BulkMessage()`, including
adding multiple recipients, merge data, and adding an attachment.

### [Bulk send with recipients pulled from a datasource](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/bulk/bulkSendFromDataSourceWithMerge.js)
This example uses a mock repository class to demonstrate how you would pull
your recipients from a database and create a bulk mailing with merge data.

### [Bulk send with Ascii charset and special characters](https://github.com/socketlabs/socketlabs-nodejs/blob/master/examples/bulk/bulkSendWithASCIICharsetMergeData.js)
This example demonstrates how to send a bulk message with a specified character
set and special characters.

### [Bulk send with Amp ](https://github.com/socketlabs/socketlabs-nodejs/blob/main/examples/bulk/bulkSendWithAmpBodyExample.js)
This example demonstrates how to send a bulk message with an AMP Html body.
For more information about AMP please see [AMP Project](https://amp.dev/documentation/)

<a name="version"></a>
# Version
* 1.1.0 - Adds Amp Html Support
* 1.0.0 - Initial Release

<a name="license"></a>
# License
The SocketLabs Node.js library and all associated code, including any code samples, are [MIT Licensed](https://github.com/socketlabs/socketlabs-nodejs/blob/master/LICENSE.MD).
