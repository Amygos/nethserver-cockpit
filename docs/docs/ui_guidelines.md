# UI guidelines

**Index**

-   [UI design](#ui-design)
    -   [Left menu](#left-menu)
    -   [Applications](#applications)
    -   [Forms](#forms)
    -   [Notifications](#notifications)
-   [Accessibility](#accessibility)

## UI design

The whole web UI is designed following [PatternFly](http://www.patternfly.org/) patterns.
Please, read carefully PatternFly documentation before starting the design of a new web page.

The main goal is to have a consistent design and behavior across all modules.

All web page should guide the user to well-defined and specific task like:

-   bad task example: "The user must choose the correct account provider"
-   good task example: "The user must choose Active Directory provider if ACLs on shared folders are a functional requirements"

### Left menu

The left menu should be considered fixed: modules shouldn't add items inside the left menu.
When a module needs to add a new feature, the web page should be added inside exiting sections like "System".

### Applications

The Applications page is a container of each new module which implements new features, like mail server, nextcloud, etc.
Each new application web page should display:

-   the status of the application status on the top
-   a list of common actions just below the status
-   all configuration parameters (forms, table, etc.) in the center

On first run, the application **must** display a modal wizard if the module can't be configured with a reasonable defaults.

Also the web page should display suggestions to help the user to follow best practices.
For example, inside the "Certificate page", the interface should display a suggestion to enable Let's Encrypt if
a self-signed certificate is used as default.

Each application must be described by a [JSON manifest](./application_manifest) inspired to AppData freedesktop.org format.

### Forms

A configuration form should always display the minimum number of fields needed to configure the feature.
Extra parameters should be hidden inside an [Advanced](http://www.patternfly.org/pattern-library/forms-and-controls/expand-collapse-section/)
panel with reasonable defaults.

For example, when creating a user you need at least the user name, the full name and the password;
but you could hide extra details like street address or phone number.

Also it is a good practice to add a "Description" when creating records inside tables.

### Notifications

NethServer Cockpit provides multiple [toast notification](http://www.patternfly.org/pattern-library/communication/toast-notifications/).
Each notification can have one of these states:

-   **success**: everything is ok. Transient: it stays on the screen for 3 seconds.
-   **error**: something went wrong. Not transient: it stays on the screen until the user explicitly close it.
    May require an action link.

Finally there also is a **task** notification, it stays on the screen until the task
has been completed. This kind of notification can be created only by `signal-event`.

#### Example

```js
// success - hide notifications after 3 seconds
parent.ns.$children[0].notifications.success.show = true;
parent.ns.$children[0].notifications.success.message = "Your success message";
setTimeout(function() {
    parent.ns.$children[0].notifications.success.show = false;
}, 3000);

// error
parent.ns.$children[0].notifications.error.show = true;
parent.ns.$children[0].notifications.error.message = "Your error message";

// event
parent.ns.$children[0].notifications.event.show = true;
parent.ns.$children[0].notifications.event.name = "Your event name";
parent.ns.$children[0].notifications.event.message = "Your action-name";
parent.ns.$children[0].notifications.event.progress = 50;
/* */
```

### Call API

Using `Cockpit API` you can easily call NethServer API or your own module API in this way:

```js
// Definition
parent.ns.exec(
    ["<api-name>/<action>"], // action can be: read | validate | update
    null, // used for input in JSON format -> { "key": "value" }
    null, // used for strem output, for actions the print on STDOUT
    function(success) {
        success = JSON.parse(success);
        return success;
    },
    function(error) {
        return error;
    }
);


// Example
// define method
function validate(obj, callback) {
    parent.ns.exec(
        ["your-own-module/validate"],
        obj,
        null,
        function(success) {
            var success = JSON.parse(success);
            callback(success);
        },
        function(error, data) {
            var errorData = JSON.parse(data);
            callback(errorData);
        }
    );
}

// call it
var validateObj = {}
    validate(validateObj, function (result) {
        // check errors
        // if(result) { ... }

        // if no errors
        // update value
        // ...
    });
```

### Modals

Modal overlay should be provided any time the user needs to enter data on a form and press "Save" button to change system configuration.

See also [Modal Overlay](http://www.patternfly.org/pattern-library/forms-and-controls/modal-overlay/) for extra use cases.

## Accessibility

We need to evaluate how PatternFly copes with people suffering of low vision, who need to access most features
using the keyboard shortcuts.
