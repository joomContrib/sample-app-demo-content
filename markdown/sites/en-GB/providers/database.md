# <span class="text-info" data-icon="&#xe02d;" aria-hidden="true"></span> Database <small>Service Provider</small> {.page-header .text-success}

%%%ArticleOptions%%%

<div id="requires" class="row">
 <div class="col-md-6 col-lg-3" markdown="1">
**Resources**

 - @add links
 </div>
 <div class="col-md-6 col-lg-3" markdown="1">
**Requires**

 - ["joomla/registry": "1.1.*@stable"] [j-registry]
   - Container `config` must be set.
 - ["joomla/database": "1.1.*@stable"] [j-database]
   - Container `logger` must be set.
      - Psr\Log\LoggerInterface 
 </div>
 <div class="col-md-6 col-lg-3" markdown="1">
**Require (Dev)**

 - none
 </div>
 <div class="col-md-6 col-lg-3" markdown="1">
**Suggests**

 - none
 </div>
</div>


## <span data-icon="&#xe08f;" aria-hidden="true"></span> Config Options {.page-header .text-muted}

**Following options are available. A "*" in Value means the value is required.**

  Option   |  Value   |  Description
 --------- | -------- | --------------
 driver    | string * | Database driver - See Joomla\Database for available drivers.
 host      | string * | Database host.
 database  | string * | Database name.
 prefix    | string * | A prefix for the database tables.
 user      | string * | Username for the database login.
 password  | string * | The password for the user.
 debug     | bool     | Optional debug setting default:`false`.

**The path `database.*` is required in the config file, see example below.**

```javascript
??? ,json config example,&#xe08f;,code
{
    "database":{
        "host": "localhost",
        "driver": "mysqli",
        "database": "db_name",
        "prefix": "pref_",
        "user": "username",
        "password": "123456789",
        "debug": false
    }
}
```


## <span data-icon="&#xe15b;" aria-hidden="true"></span> Usage {.page-header .text-success}

**Basic usage description for the database provider.**

```php
??? ,usage,&#xe15b;,success
// Create a new or get existing Container.
$container = new Container; //$this->getContainer();

// Register the service provider.
$container->registerServiceProvider(new DatabaseServiceProvider);

// Get a database connection from the container.
$this->getContainer()->get('db');
```


## <span data-icon="&#xe15e;" aria-hidden="true"></span> Application Example {.page-header .text-warning}

%%%CodeUsageWarning%%%

Example for your application initialise method, including:

 - Create Container
 - Create/Share Config and define debug
 - Registering the Logger
 - Registering a database connection
 - Set Logger and Container
 - Write a log message

To your already existing initialise() method, which you can get from
the [Monolog example] [site-monolog], you just need to add one line.
Insert it **after** you register the logger.

```php
??? ,add to initialise(),&#xe15e;,warning
$container->registerServiceProvider(new DatabaseServiceProvider);
```

**Your new initialise() method looks like this now.**

```php
??? ,Application initialise method,&#xe15e;,warning
protected function initialise()
{
    // Create the container.
    $container = new Container;

    // Get Config - Share Config.
    $config    = new Registry;
    $conf_file = JPATH_ETC .'/jconfig.json';
		
    if (!is_file($conf_file))
    {
        throw new \RuntimeException('Could not load application configuration file.', 500);
    }
		
    $config->loadFile($conf_file);
		
    $container->share('config', function (Container $c) use ($config) {
        return $config;
    }, true);
		
    // Set debug.
    // If true the logger overrides the trigger level for `default_level` to debug.
    define('JDEBUG', $config->get('system.debug', false));

    // Register the logger.
    $container->registerServiceProvider(new MonologServiceProvider);

	// Register a database connection.
	$container->registerServiceProvider(new DatabaseServiceProvider);

    // Set the logger to the application.
    $logger = $container->get('logger');
    $this->setLogger($logger);
		
    // Set the container to the application.
    $this->setContainer($container);

	// Log msg.
    $logger->debug('Application initialise complete.');
}
```


## <span data-icon="&#xe06f;" aria-hidden="true"></span> Comments {.page-header .text-primary}

%%%DisqusComments%%%



[j-database]: https://packagist.org/packages/joomla/database "Joomla Database Package"
[j-registry]: https://packagist.org/packages/joomla/registry "Joomla Registry Package"

[site-monolog]: http://jc.joomworker.com/site/providers/monolog "A Monolog Service Provider"

