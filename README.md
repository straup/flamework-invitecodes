flamework-invitecodes
--

These are drop-in PHP pages and libraries for adding invite code restrictions to
your Flamework application.

In keeping with the rest of Flamework the term "drop-in" is used loosely. It
really means copy and adjust to taste. It takes a bit longer to get set up but
it also means there is rarely any magic and you're forced to get at a least a
cursory understanding of how the pieces fit together.

If you're wondering how that means you deal with updated to
flamework-invitecodes it means that you deal with them by copying the newer
files in to your application as they are released. It's not ideal but it also
cuts down on the surprises.

database schemas
--

* schema/db_main.schema

files
--

To will need to copy these files in to your Flamework application:

* www/invite.php

* www/include/lib_invite_codes.php

* www/include/lib_rfc822.php

* www/template/page_invite.txt

* 

* www/javascript/jquery-min-1.6.2.js

config/app.php
--

You will need to add the following config flags to your config.php file:

	$GLOBALS['cfg']['crypto_invite_secret'] = '';

	$GLOBALS['cfg']['enable_feature_invite_codes'] = 1;
	$GLOBALS['cfg']['invite_codes_allow_signedin_users'] = 0;

www/.htaccess
--

You will need to add the following rewrite rules to your .htaccess file:

	RewriteRule ^invite/?$				invite.php?%{QUERY_STRING} [L]
	RewriteRule ^invite(?:/([^/]+))?/?$		invite.php?code=$1&%{QUERY_STRING} [L]

example code
--

	include("include/init.php");
	loadlib("invite_codes");

	if ($GLOBALS['cfg']['enable_feature_invite_codes']){

		if (! $GLOBALS['cfg']['crypto_invite_secret']){
			log_fatal("undefined secret for invite codes");
		}

		if (! invite_codes_get_by_cookie()){

			$cookie = login_get_cookie('invite');

			if (! $cookie){
				header("location: /invite/?redir=" . urlencode("/foo/bar/"));
				exit();
			}
		}
	}

	# the rest of your code including any checks to see whether
	# this is a logged in user; the invite code cookie is only
	# concerened with invite codes.
