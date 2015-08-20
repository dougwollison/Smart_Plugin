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

Any methods should be defined with an underscore before the name (visibility isn't really relevant), so that calling them by their intended name will overload to the save_callback utility. The arguments list should be the arguments you expect to get from the hook first, followed by the custom ones you'll be passing when you set it up.

### Registering Callbacks

To setup your method you would do something like this.

    $plugin = new MyPlugin();
    $plugin->my_method( 'foo', 'bar' );

The method will be registered to the hook using the settings defined in the `$method_hooks` array (defaulting to `array('init', 10, 0)` if nothing is found for the method), and when the hook is fired, the method will run with both the hooks arguments and the custom ones passed during setup. The arguments list passed when it's called will be sliced to the expected length denoted by the accepted args value for the hook details.

### Removing Callbacks
    
Should you wish to remove a callback that's been registerd to a hook, you can use the `remove_callback` method (also in either context), passing the name of the method in question and the necessary hook arguments. You can specify to remove the method's callback from a specfic hook and priority, from the specific hook at any priority, or entirely, based on the arguments passed. It will return the number of callbacks that were sucessfully removed.

    MyPlugin::remove_callback( 'my_method' ); // from the default hook
    
    $plugin->remove_callback( 'my_method', 'hook_name' ); // specific hook, any priority
    $plugin->remove_callback( 'my_method', 'hook_name', 10 ); // specific hook/priority
    $plugin->remove_callback( 'my_method', true ); // any hook/priority
    
### Independent Callbacks

You can also use it to save a callback but not attach it to a hook; it will return the callable array, allowing you to pass it as a callback with custom arguments you want used. To do this, you would use `setup_callback` (in either context; it'll overload to the proper context version), passing the method name, the arguments array, and the hook information. The hook information can be the name or `add_filter` args to attach the callback to the hook, or, if you just need a callback for a non-hook use, you can pass an integer denoting the number of arguments to expect when it's called (defaults to 0).

    $plugin->setup_callback( 'my_method', array( 'foo', 'bar' ), array( 'hook_name', 10, 2 ) );
    MyPlugin::setup_callback( 'my_method', array( 'foo', 'bar' ), array( 'hook_name', 10, 2 ) );
    
    $my_callback = $plugin->setup_callback( 'my_method', array( 'foo', 'bar' ), 2 );
    $my_callback = MyPlugin::setup_callback( 'my_method', array( 'foo', 'bar' ), 2 );
    
## Todo

- Would it be worth adding a callback retrieval tool for the purpose of examing/editing callbacks after they're registered?
