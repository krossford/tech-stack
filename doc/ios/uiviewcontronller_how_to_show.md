# UIViewController

## show 和 present 之间的区别

1. Show - Pushes the destination view controller onto the navigation stack, moving the source view controller out of the way (destination slides overtop from right to left), providing a back button to navigate back to the source - on all devices.

Example: Navigating inboxes/folders in Mail.

2. Show Detail - Replaces the detail/secondary view controller when in a UISplitViewController with no ability to navigate back to the previous view controller.

Example: In Mail on iPad in landscape, tapping an email in the sidebar replaces the view controller on the right to show the new email.

3. Present Modally - Presents a view controller in various different ways as defined by the Presentation option, covering up the previous view controller - most commonly used to present a view controller that animates up from the bottom and covers the entire screen on iPhone, but on iPad it's common to present it in a centered box format overtop that darkens the underlying view controller.

Example: Tapping the + button in Calendar on iPhone.

4. Popover Presentation - When run on iPad, the destination appears in a small popover, and tapping anywhere outside of this popover will dismiss it. On iPhone, popovers are supported as well but by default if it performs a Popover Presentation segue, it will present the destination view controller modally over the full screen.

Example: Tapping the + button in Calendar on iPad (or iPhone, realizing it is converted to a full screen presentation as opposed to an actual popover).

5. Custom - You may implement your own custom segue and have complete control over its appearance and transition.