# ZFDebug - a debug bar for Zend Framework
ZFDebug is a plugin for the Zend Framework for PHP5, providing useful debug information displayed in a small bar at the bottom of every page.

Time spent, memory usage and number of database queries are presented at a glance. Additionally, included files, a listing of available view variables and the complete SQL command of all queries are shown in separate panels:

The available plugins at this point are:

  * Cache: Information on Zend_Cache, APC and Zend OPcache (for PHP 5.5).
  * Database: Full listing of SQL queries from Zend_Db and the time for each.
  * Exception: Error handling of errors and exceptions.
  * File: Number and size of files included with complete list.
  * Html: Number of external stylesheets and javascripts. Link to validate with W3C.
for custom memory measurements.
  * Log: Timing information of current request, time spent in action controller and custom timers. Also average, min and max time for requests.
  * Variables: View variables, request info and contents of `$_COOKIE`, `$_POST` and `$_SESSION`

Installation & Usage
------------
To install, place the folder 'ZFDebug' in your library path, next to the Zend
folder. Then add the following method to your bootstrap class (in ZF1.8+):

In Bootstrap.php

    protected function _initZFDebug() {
        if (Zend_Registry::isRegistered('config') && $config = Zend_Registry::get('config')) {
            if ($config ['settings'] ['debug']['enabled'] == false):
                return true;
            else:
                $autoloader = Zend_Loader_Autoloader::getInstance();
                $autoloader->registerNamespace('ZFDebug');
            endif;
        }

        $options = array(
            'plugins' => array(
                'Variables',
                //'ZFDebug_Controller_Plugin_Debug_Plugin_Debug' => array('tab' => 'Debug', 'panel' => ''),
                'ZFDebug_Controller_Plugin_Debug_Plugin_Auth',
                'File' => array('basePath' => PATH_PROJECT),
                'Database',
                'Registry',
                'Memory',
                'Time',
                'Exception'
            ),
            'jquery_path' => '');
        // Setup the cache plugin
        if (Zend_Registry::isRegistered('cache')) {
            $cache = Zend_Registry::get('cache');
            $options ['plugins'] ['Cache'] ['backend'] = $cache->getBackend();
        }

        // Setup the databases
        $resource = $this->getPluginResource('multidb');
        if (Zend_Registry::isRegistered('config')) {
            $config = Zend_Registry::get('config');
            $databases = $config ['resources'] ['multidb'];
            foreach ($databases as $name => $adapter) {
                $db_adapter = $resource->getDb($name);
                $options ['plugins'] ['Database'] ['adapter'] [$name] = $db_adapter;
            }
        }

        // Init the ZF Debug Plugin
        $debug = new ZFDebug_Controller_Plugin_Debug($options);
        $this->bootstrap('frontController');
        $frontController = $this->getResource('frontController');
        $frontController->registerPlugin($debug);
    }

In .ini

[development : bootstrap]
	settings.debug.enabled = 1 ; enable debug bar


Using Composer
--------------
You may now install ZFDebug using the dependency management tool Composer.

To use ZFDebug with Composer, add the following to the require list in your
project's composer.json file:

	"require": {
	    "visualweber/zfdebug": "0.1"
	},

Run the install command to resolve and download the dependencies:

	php composer.phar install

Further documentation will follow as the github move progresses.
