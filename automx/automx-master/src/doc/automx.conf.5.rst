=============
 automx.conf
=============

:Date: 02/08/2013
:Subtitle: automx configuration parameters
:Manual Section: 5
:Manual Group: automx
:Copyright: This document has been placed in the public domain.

Description
'''''''''''

The ``automx`` automx.conf configuration file specifies all parameters that control
the ``automx`` configuration system. Parameters not specified in automx.conf are
left at their default values.


Syntax
''''''

The general format of the automx.conf file is as follows:

- Each logical line has the form ``parameter = value``. Whitespace around the ``=`` is ignored, as is whitespace at the end of a logical line.
- Empty lines and whitespace-only lines are ignored, as are lines whose first non-whitespace character is a ``#``.
- When the same parameter is defined multiple times, only the last instance is remembered.
- Uppercase and lowercase matters. Use parameter names, macros and variables exactly as specified.

Structure
'''''''''

The configuration file is split into sections.

- A section begins with the section name surrounded by square brackets, e.g. ``[example.com]``.
- A section name identifies a domain or subdomain automx should respond with autoconfiguration instructions upon client request.
- A section defines services which will be sent as autoconfiguration instructions to a client.
- Section names ``automx``, ``DEFAULT`` and ``global`` are reserved - they have special meaning.

Services
''''''''

Each section may specify one more services that should be provided to the
client. A service must be defined in a section in order to be enabled. Options
specific to a service are given using a concatenation of a service name and the
parameter it should configure.

The following concatenation of service name smtp and service option _server
creates the smtp_server parameter::

	smtp_server = mail.example.com

Service names available in automx are shown in the following list. The service
options to create parameters are specified in the section called Parameters_:

imap
	This name specifies a service as defined in RFC 3501. The protocol to
	connect to this server is IMAP. Specifying this name is only applicable
	for account_type = email.

pop
	This name specifies a service as defined in RFC 1939. The protocol to
	connect to this server is POP3. Specifying this name is only applicable
	for account_type = email.

smtp
	This name specifies an SMTP service as defined in RFC 5321. The
	protocol to connect to this server is SMTP. Specifying this name is
	only applicable for account_type = email.

caldav
	This name specifies a CalDAV service as defined in RFC 4791. The
	protocol to connect to this server is HTTP. The server definition expects
	a URL like https://caldav.example.com/. Specifying this name is only
	applicable for account_type = email currently.
	This service only affects autodiscover view.

carddav
	This name specifies a CardDAV service as defined in RFC 6352. The
	protocol to connect to this server is HTTP. The server definition expects
	a URL like https://carddav.example.com/. Specifying this name is only
	applicable for account_type = email currently.
	This service only affects autodiscover view.

ox
	This name specifies an existing OX App Suite service. Some clients
	need to know where to access the OX App Suite HTTP API. The server
	definition expects a URL like https://ox.example.com/. Specifying
	this name is only applicable for account_type = email currently.
	This service only affects autodiscover view.


Parameters
''''''''''

autoconfig (no default)
	Specifies a path to a file that contains static autoconfiguration
	options following to the Mozilla schema.

		.. NOTE::

			This parameter is valid only if backend = file has been specified.

autodiscover (no default)
	Specifies a path to a file that contains static autoconfiguration
	options following to the Microsoft schema.

		.. NOTE::

			This parameter is valid only if backend = file has been specified.

account_name (no default, mandatory)
	Specifies a display name in MUA account listings.

account_name_short (no default, mandatory)
	Specifies a short display name in MUA account listings.

account_type (default: email, mandatory)
	Specifies the account type that should be configured:

	email
		Setting this option will create an email configuration.

		.. NOTE::

			The Microsoft schema specifies additional account_types. Currently automx only supports email.

action (default: settings, mandatory)
	Specifies whether the response to the client contains configuration
	settings or if it should visit a different server or use a different
	address.

		.. NOTE::

			This option applies to Microsoft schema only.

	settings
		The client should use the configuration settings sent in this
		response.

	backend (default: DEFAULT, mandatory)
		Specifies the backend method to lookup configuration data. The
		following options are available:

	file
		automx should use logic provided within this section to
                identify a different section which holds configuration
                settings::

			backend = file

        filter
                automx should use logic provided within this section to
                identify a different section which holds configuration
                settings::

			backend = filter

        global
                automx should use general settings defined in the global
                section::

			backend = global

        ldap
                automx should use a mixture of general and individual
                settings. General settings are set like static settings.
                Individual settings should be retrieved from an LDAP
                query::

			backend = ldap

        See also automx_ldap(5) for a list of LDAP related configuration options.

        sql
                automx should use a mixture of general and individual
                settings. General settings are set like static settings.
                Individual settings should be retrieved from an SQL query::

			backend = sql

        See also automx_sql(5) for a list of SQL related configuration options.

        static
                automx should use general settings provided within the
                current section::

                        backend = static

debug (default: no)
	Specifies if automx should note client request and server response to
	the (SSL) error log.

display_name (no default, optional)
	Specifies an ???optional display name that indicates the name of the
	sender (...) that could be displayed to the user of a mail application???
	(see: 3.4. Address Specification in RFC 5322). The client can decide to
	accept or change the name.

		.. NOTE::

			This option applies to Microsoft schema only.

domains (no default)
	Specifies a list of domains automx will output autoconfiguration
	information for.

	*
		Specify ``*`` to let automx reply for any domains listed in a
                section.

        domain, domain, ...
                Specify a comma separated list of domains automx should
                provide autoconfiguration for.

mobileconfig (no default)
	Specifies a path to a file that contains static mobileconfiguration
	options following to the Mozilla schema.

		.. NOTE::

			This parameter is valid only if backend = file has been specified.

provider (no default, mandatory)
	The FQDN domain name of the domain that provides the configuration
	service::

			provider = example.com

section_filter (default: domainpart, optional)
	Specifies a list of one or more filters whose result outputs a section
	name. The filters will be used in order specified. The first match ends
	execution of subsequent filters.

	These filters will be used instead of the hard coded, internal
	domainpart filter, which strictly uses the domainpart taken from the
	email address the client submitted in its configuration request::

		section_filters = server_1, server_2
		server_1 = /usr/sbin/postmap -q "%u" hash:/etc/postfix/virtual_alias_domains | \
			sed -e 's/^.*@\(\.*\)/\1/g' | grep internal.example.com
		server_2 = /usr/sbin/postmap -q "%u" hash:/etc/postfix/virtual_alias_domains | \
			sed -e 's/^.*@\(\.*\)/\1/g' | grep dmz.example.com

service (default: no)
	Specifies the service type that should be provided in the configuration
	response. By default all services are disabled. See the section called
	Services_ for a list of valid service names.

service_auth_identity (no default)
	Specifies the login name the client should use when it identifies the
	user in order to gain access to the service. See the section called
	`Macros and Variables`_ for available options.

service_auth (no default)
	Specifies the method the client should use when it identifies the user
	in order to gain access to the service. The following options are
	available:

		.. NOTE::

			Thunderbird 3.0 accepts only ``plain`` and ``secure``. It will ignore the whole XML file, if other values are given.

	plaintext
		The client should use the SASL mechanisms PLAIN or LOGIN
                to identify the user.

        encrypted
                The client should use the SASL mechanisms CRAM-MD5 or
                DIGEST-MD5 to identify the user.

        ntlm
                The client should use the SASL NTLM mechanism to identify
                the user.

        gssapi
                The client should use the SASL GSSAPI mechanism to
                identify the user.

        client-ip-address
                The client will not send identification data. Instead the
                server should recognize the user based on the clients IP
                address.

        tls-client-cert
                The client should send a TLS client certificate when the
                server requests one.

        smtp-after-pop
                The client should authenticate using POP first, and then
                start sending messages over SMTP later.

        none
                The client should not send any identification data.

service_port (no default)
	Specifies port number on which the service is offered. Typical,
	standardized port numbers are:

service_server (no default)
	Specifies the IP address or hostname on which the service is offered.

service_encryption (no default)
	Specifies whether the client should use a plaintext or an encrypted
	transport layer for client-server communication. The following options
	are available:

        auto
                The client should try to start with starttls, proceed with
                ssl and settle with none, if only that is available.

		.. NOTE::

			This feature is not available in clients following the Mozilla schema. For these clients automx will always output none as encryption level.

        none
                The client should use an unencrypted transport layer.

        ssl
                The client should use an SSL3 or TLS1 encrypted transport
                layer from the start.

		.. NOTE::

			This option is typical for smtps, pop3s and imaps services and usually requires a dedicated port on the server for SSL encryption only.

        starttls
                The client should begin communication on an unencrypted
                port and then upgrade the communication to TLS via the
                STARTTLS command.

                .. NOTE::

			This option is typical for smtp, pop3 and imap services.

smtp_author (default: %s)
	Specifies the envelope sender address used when the client sends a
	message. See the section called `Macros and Variables`_ for available
	options.

	.. NOTE::

		This parameter is experimental. The feature is available for
		Microsoft clients only. For a definition of ???author??? see also
		RFC 5598, Section 2.1 User Actors.

smtp_default (no default)
	Specifies if this service should be used globally for all outgoing
	messages from all accounts.

	.. NOTE::

		This feature is available to clients following the Mozilla schema only.

sign_mobileconfig (default: no)
        Specifies whether configuration files for iOS and MacOS should be sent
        signed or not. By default signing is disabled.

sign_cert (no default)
        Specifies the path to the cert used to sign configuration files for iOS
        and MacOS. The file must contain all certificates - certificate and all
        intermediate certificates concatenated.

sign_key (no default)
        Specifies the path to the key used to sign configuration files for iOS
        and MacOS.


Macros and Variables
''''''''''''''''''''

The following macros and variables can be used within automx to build service
configuration.

%%
	This is replaced by a literal ``%`` character.

%d
	When the input key is an address of the form localpart@domainpart, this
	macro will be replaced by the (RFC 2253) quoted domain part of the
	address.

%s
	When the input key is an address of the form localpart@domainpart, this
	macro will be replaced by this (RFC 2253) quoted mail address.

${varname}
	The value of ${varname}, retrieved from an LDAP or SQL query, will be
	used.

%u
	When the input key is an address of the form localpart@domainpart, this
	macro will be replaced by the (RFC 2253) quoted local part of the
	address.

Authors
'''''''

Christian Roessner <cr@sys4.de>
        Wrote the program.

Patrick Ben Koetter <p@sys4.de>
        Wrote the documentation.

See also
''''''''

`automx(8)`_, `automx.conf(5)`_, `automx_ldap(5)`_, `automx_script(5)`_, `automx_sql(5)`_, `automx-test(1)`_

.. _automx(8): automx.8.html
.. _automx.conf(5): automx.conf.5.html
.. _automx_ldap(5): automx_ldap.5.html
.. _automx_sql(5): automx_sql.5.html
.. _automx_script(5): automx_script.5.html
.. _automx-test(1): automx-test.1.html
