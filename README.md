# Smart_Plugin

This abstract class is the core of [QuickStart](https://github.com/dougwollison/quickstart/). It enables any class that extends it to have it's methods auto-hooked to actions and filters within WordPress (or anything using the Plugin API).

## Usage

Below is an example of how you should structure your class.

    class My_Plugin extends Smart_Plugin {
        protected static $method_hooks = array(
            'my_method' => array( 'hook_name', 10 /* priority */, 2 /* accepted callback arguments */ ),
        );
        
        protected function _my_method( $hook_arg1, $hook_arg2, $custom_arg1, $custom_arg2 ) {
            // Do stuff...
        }
    }

Any methods should be defined with an underscore before the name (visibility isn't really relevant). The arguments list should be the arguments you expect to get from the hook first, followed by the custom ones you'll be passing when you set it up.

To setup your method you would do something like this.

    $plugin = new MyPlugin();
    $plugin->my_method( 'foo', 'bar' );

The method will be registered to the hook using the settings defined in the `$method_hooks` array (defaulting to `array('init', 10, 0)` if nothing is found for the method), and when the hook is fired, the method will run with both the hooks arguments and the custom ones passed during setup. The arguments list pass when it's called will be sliced to the expected length denoted by the accepted args value for the hook details.

You can also use it to save a callback but not attach it to a hook; it will return the callable array, allowing you to pass it as a callback with custom arguments you want used. To do this, you would use `setup_callback` (in either context; it'll overload to the proper context version), passing the method name, the arguments array, and the hook information. The hook information can be the name or `add_filter` args to attach the callback to the hook, or, if you just need a callback for a non-hook use, you can pass an integer denoting the number of arguments to expect when it's called (defaults to 0).

    $plugin->setup_callback( 'my_method', array( 'foo', 'bar' ), array( 'hook_name', 10, 2 ) );
    MyPlugin::setup_callback( 'my_method', array( 'foo', 'bar' ), array( 'hook_name', 10, 2 ) );
    
    $my_callback = $plugin->setup_callback( 'my_method', array( 'foo', 'bar' ), 2 );
    $my_callback = MyPlugin::setup_callback( 'my_method', array( 'foo', 'bar' ), 2 );

## Todo

- Possibly add tools for removing registered hooks without knowing the callback ID.
