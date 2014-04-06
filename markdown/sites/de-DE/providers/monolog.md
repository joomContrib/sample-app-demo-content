# <span class="text-info" data-icon="&#xe103;" aria-hidden="true"></span> Monolog <small>Service Provider</small> {.page-header .text-success}

%%%ArticleOptions%%%

<div class="alert alert-info">
<p>This site requires translation.</p>
</div>

> Service Provider for log messages, configurable through the application config.

<div id="requires" class="row">
 <div class="col-md-4" markdown="1">
**Requires**

 - ["monolog/monolog": "1.8.*@stable"] [monolog]
 - ["joomla/registry": "1.1.*@stable"] [j-registry]
   - Container `config` must be set.
 - ["swiftmailer/swiftmailer": "5.1.*@stable"] [swift-mailer]
   - Only if `handler_mail = true`
 </div>
 <div class="col-md-4" markdown="1">
**Require (Dev)**

 - none
 </div>
 <div class="col-md-4" markdown="1">
**Suggests**

 - ["swiftmailer/swiftmailer": "5.1.*@stable"] [swift-mailer]
 </div>
</div>


## <span class="text-info" data-icon="&#xe08f;" aria-hidden="true"></span> Config Options {.page-header .text-success}

```ini
??? ,available options,&#xe08f;,code
; Logger options.
"on"               : true,        [required to activate log def:false]
"logger_name"      : "",          [optional Name for the logger def:SysLog]
"handler_default"  : true,        [optional Use default handler def:true]
"default_level"    : "warning",   [optional Log trigger level def:warning]
"default_storage"  : "file",      [optional (file, file_rotate) def:file]
"default_maxfiles" : 25,          [optional Max log files default_storage:file_rotate]
"default_path"     : "",          [optional Path for logs def:JPATH_ETC/logs]
"handler_mail"     : true,        [optional Use mail handler def:false]
"mail_usesystem"   : true,        [optional Use site.* mail settings def:true]
"mail_level"       : "error",     [optional Mail trigger level def:critical]
"mail_transport"   : "smtp",      [(smtp, sendmail, php) def:smtp]
"mail_sendmail"    : "",          [optional def:/usr/sbin/sendmail -bs]
"mail_from"        : "",          [required if mail_transport:smtp]
"mail_smtp_user"   : "",          [required if mail_transport:smtp]
"mail_smtp_pw"     : "",          [required if mail_transport:smtp]
"mail_smtp_host"   : "",          [required if mail_transport:smtp]
"mail_smtp_port"   : 587,         [required if mail_transport:smtp]
"mail_to"          : "me@you.com" [required if handler_mail:true]
"extra":
    "ip"           : false        [optional def:false]
    "ip_xfor"      : false        [optional def:false]
    "http_method"  : false        [optional def:false]
    "useragent"    : false        [optional def:false]
    "requesturl"   : true         [optional def:false]


; Json config example.
{
	"system":{
		"log":{
			"on": true,
				"extra":{
					"requesturl": true,
					...
				},
			"logger_name": "MyLogger",
			...
		}
	}
}
```


## <span class="text-info" data-icon="&#xe15b;" aria-hidden="true"></span> Usage {.page-header .text-success}

```php
??? ,usage,&#xe15b;,success
// Create a new or get existing Container.
$container = new Container; //$this->getContainer();

// Register the service provider.
$container->registerServiceProvider(new MonologServiceProvider);

// Is $this LoggerAware?
if ($this instanceof LoggerAwareInterface)
{
	$this->setLogger($container->get('logger'));
}

// Log a msg.
$this->getLogger()->log('info', 'This is a info msg!', array('detail' => $detail));
```


## <span class="text-info" data-icon="&#xe15e;" aria-hidden="true"></span> Application Example {.page-header .text-success}

```php
??? ,initialise(),&#xe15e;,warning
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

// Set the logger to the application.
$logger = $container->get('logger');
$this->setLogger($logger);
		
// Set the container to the application.
$this->setContainer($container);

$logger->debug('Application initialise complete.');
```



[monolog]: https://packagist.org/packages/monolog/monolog "Sends your logs to files, sockets, inboxes, databases and various web services"
[j-registry]: https://packagist.org/packages/joomla/registry "Joomla Registry Package"
[swift-mailer]: https://packagist.org/packages/swiftmailer/swiftmailer "Swiftmailer, free feature-rich PHP mailer"

