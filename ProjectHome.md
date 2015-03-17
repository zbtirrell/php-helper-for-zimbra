Working with the Zimbra SOAP API can sometimes be complicated for simple business logic.  This API helps to simplify this process when developing with PHP.

The code is also available on Plymouth State's website, it is possible that the code provided there may be more current than what is here.  Plymouth State is running this in production with their Zimbra 5.x install.

[Plymouth State's PHP Zimbra API](http://www.plymouth.edu/webapp/code/zimbra.class.phps)

Brief and simple example:
```
<?php

require_once 'zimbra.class.php'; // include the library
$zimbra = new Zimbra('username','prod'); // instantiate with username and instance
$zimbra->connect(); // make the connection

$task_list = $zimbra->getTasks(); // execute some function, in this example get the tasks

// then do some other stuff...
?>
```


If you are looking to do single-sign on to Zimbra, it is relatively simple:
```
<?php

// $username must be somehow set to a known username
// one possible solution would be to use phpCAS to get it

/*
////////////////////////////////
// CAS Authentication Example
////////////////////////////////

// import phpCAS lib (http://esup-phpcas.sourceforge.net/)
include_once('cas/CAS.php');

// initialize phpCAS
phpCAS::client(CAS_VERSION_2_0,$cas_hostname,443,$cas_url_path);

// check CAS authentication
phpCAS::authenticateIfNeeded();

// at this step, the user has been authenticated by the CAS server
// and the user's login name can be read with phpCAS::getUser().
$username = phpCAS::getUser();
////////////////////////////////
*/

require 'zimbra.class.php';
$zimbra = new Zimbra($username);
$zimbra->SSO();

?>
```


This should be scrubbed out, but you need a function along these lines:

```
	/**
	 * Prettifies an XML string into a human-readable and indented work of art
	 * http://gdatatips.blogspot.com/2008/11/xml-php-pretty-printer.html
	 *
	 * @param $xml The XML as a string
	 * @param $html_output True if the output should be escaped (for use in HTML)
	 */
	public static function xmlpp($xml, $html_output=false)
	{
		$xml_obj = new SimpleXMLElement($xml);
		$level = 4;
		$indent = 0; // current indentation level
		$pretty = array();

		// get an array containing each XML element
		$xml = explode("\n", preg_replace('/>\s*</', ">\n<", $xml_obj->asXML()));

		// shift off opening XML tag if present
		if (count($xml) && preg_match('/^<\?\s*xml/', $xml[0])) {
			$pretty[] = array_shift($xml);
		}

		foreach ($xml as $el) {
			if (preg_match('/^<([\w])+[^>\/]*>$/U', $el)) {
				// opening tag, increase indent
				$pretty[] = str_repeat(' ', $indent) . $el;
				$indent += $level;
			} else {
				if (preg_match('/^<\/.+>$/', $el)) {            
					$indent -= $level;  // closing tag, decrease indent
				}
				if ($indent < 0) {
					$indent += $level;
				}
				$pretty[] = str_repeat(' ', $indent) . $el;
			}
		}   
		$xml = implode("\n", $pretty);   
		return ($html_output) ? '<pre style="border: 1px solid black; background-color: white; padding: 1ex; margin-bottom: 1em;">' . htmlentities($xml) . '</pre>' : $xml;
	}//end xmlpp
```

More examples are coming soon, but seriously, if you want examples let me know...  Without people asking, I'm not all that incentivized to write examples for something I know how to use.