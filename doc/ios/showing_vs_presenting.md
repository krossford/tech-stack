# Presenting Versus Showing a View Controller

The UIViewController class offers two ways to display a view controller:

The showViewController:sender: and showDetailViewController:sender: methods offer the most adaptive and flexible way to display view controllers. These methods let the presenting view controller decide how best to handle the presentation. For example, a container view controller might incorporate the view controller as a child instead of presenting it modally. The default behavior presents the view controller modally.

The presentViewController:animated:completion: method **always displays the view controller modally**. The view controller that calls this method might not ultimately handle the presentation but the presentation is always modal. This method adapts the presentation style for horizontally compact environments.

The showViewController:sender: and showDetailViewController:sender: methods are the preferred way to initiate presentations. A view controller can call them without knowing anything about the rest of the view controller hierarchy or the current view controller’s position in that hierarchy. These methods also make it easier to reuse view controllers in different parts of your app without writing conditional code paths.