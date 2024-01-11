.. Images
.. |operation_1| image:: images/operation_1.jpg


Operation Notification
======================

The *fledge-notify-operation* notification delivery plugin is a mechanism by which a notification can be used to send a request to a south services to perform an operation.

Once you have created your notification rule and move on to the delivery mechanism

  - Select the operation plugin from the list of plugins

  - Click *Next*

    +--------------+
    | |operation_1||
    +--------------+

  - Configure the plugin

    - **Service**: The name of the south service you wish to control

    - **Trigger Value**: The operation payload to send to the south service when the rule triggers. This is the name of the operation to perform and a set of name, value pairs which are the optional parameters to pass that operations.

    - **Cleared Value**: The operation payload to send to the south service when the rule clears. This is the name of the operation to perform and a set of name, value pairs which are the optional parameters to pass that operations.

  - Enable the plugin and click *Next*

  - Complete your notification setup

