Error Messages
===

When an invalid or undeliverable message has been sent to a broker, the broker
must respond with an error message.

The *in_reply_to* field of the error message contains the ID of the message
that prompted that error.

The *data* field of error messages is a string description.

An error message must have the *message_type* field equal to:

`http://puppetlabs.com/error_message`
