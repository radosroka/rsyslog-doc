`back to rsyslog module overview <rsyslog_conf_modules.html>`_

TCP Syslog Input Module
=======================

**Module Name:    imtcp**

**Author:**\ Rainer Gerhards <rgerhards@adiscon.com>

**Multi-Ruleset Support:**\ since 4.5.0 and 5.1.1

**Description**:

Provides the ability to receive syslog messages via TCP. Encryption is
natively provided by selecting the approprioate network stream driver
and can also be provided by using `stunnel <rsyslog_stunnel.html>`_ (an
alternative is the use the `imgssapi <imgssapi.html>`_ module).

Multiple receivers may be configured by specifying $InputTCPServerRun
multiple times. This is available since version 4.3.1, earlier versions
do NOT support it.

**Configuration Directives**:

-  **$InputTCPServerAddtlFrameDelimiter <Delimiter>**
    This directive permits to specify an additional frame delimiter for
   plain tcp syslog. The industry-standard specifies using the LF
   character as frame delimiter. Some vendors, notable Juniper in their
   NetScreen products, use an invalid frame delimiter, in Juniper's case
   the NUL character. This directive permits to specify the ASCII value
   of the delimiter in question. Please note that this does not
   guarantee that all wrong implementations can be cured with this
   directive. It is not even a sure fix with all versions of NetScreen,
   as I suggest the NUL character is the effect of a (common) coding
   error and thus will probably go away at some time in the future. But
   for the time being, the value 0 can probably be used to make rsyslog
   handle NetScreen's invalid syslog/tcp framing. For additional
   information, see this `forum
   thread <http://kb.monitorware.com/problem-with-netscreen-log-t1652.html>`_.
   **If this doesn't work for you, please do not blame the rsyslog team.
   Instead file a bug report with Juniper!**
   Note that a similar, but worse, issue exists with Cisco's IOS
   implementation. They do not use any framing at all. This is confirmed
   from Cisco's side, but there seems to be very limited interest in
   fixing this issue. This directive **can not** fix the Cisco bug. That
   would require much more code changes, which I was unable to do so
   far. Full details can be found at the `Cisco tcp syslog
   anomaly <http://www.rsyslog.com/Article321.phtml>`_ page.
-  **$InputTCPServerDisableLFDelimiter** <on/**off**> (available since
   5.5.3)
    Industry-strandard plain text tcp syslog uses the LF to delimit
   syslog frames. However, some users brought up the case that it may be
   useful to define a different delimiter and totally disable LF as a
   delimiter (the use case named were multi-line messages). This mode is
   non-standard and will probably come with a lot of problems. However,
   as there is need for it and it is relatively easy to support, we do
   so. Be sure to turn this setting to "on" only if you exactly know
   what you are doing. You may run into all sorts of troubles, so be
   prepared to wrangle with that!
-  **$InputTCPServerNotifyOnConnectionClose** [on/**off**] (available
   since 4.5.5)
    instructs imtcp to emit a message if the remote peer closes a
   connection.
    **Important:** This directive is global to all listeners and must be
   given right after loading imtcp, otherwise it may have no effect.
-  **$InputTCPServerKeepAlive** <on/**off**>
    enable of disable keep-alive packets at the tcp socket layer. The
   default is to disable them.
-  **$InputTCPServerRun** <port>
    Starts a TCP server on selected port
-  **$InputTCPFlowControl** <**on**/off>
    This setting specifies whether some message flow control shall be
   exercised on the related TCP input. If set to on, messages are
   handled as "light delayable", which means the sender is throttled a
   bit when the queue becomes near-full. This is done in order to
   preserve some queue space for inputs that can not throttle (like
   UDP), but it may have some undesired effect in some configurations.
   Still, we consider this as a useful setting and thus it is the
   default. To turn the handling off, simply configure that explicitely.
-  **$InputTCPMaxListeners** <number>
    Sets the maximum number of listeners (server ports) supported.
   Default is 20. This must be set before the first $InputTCPServerRun
   directive.
-  **$InputTCPMaxSessions** <number>
    Sets the maximum number of sessions supported. Default is 200. This
   must be set before the first $InputTCPServerRun directive
-  **$InputTCPServerStreamDriverMode** <number>
    Sets the driver mode for the currently selected `network stream
   driver <netstream.html>`_. <number> is driver specifc.
-  **$InputTCPServerInputName** <name>
    Sets a name for the inputname property. If no name is set "imtcp" is
   used by default. Setting a name is not strictly necessary, but can be
   useful to apply filtering based on which input the message was
   received from.
-  **$InputTCPServerStreamDriverAuthMode** <mode-string>
    Sets the authentication mode for the currently selected `network
   stream driver <netstream.html>`_. <mode-string> is driver specifc.
-  **$InputTCPServerStreamDriverPermittedPeer** <id-string>
    Sets permitted peer IDs. Only these peers are able to connect to the
   listener. <id-string> semantics depend on the currently selected
   AuthMode and  `network stream driver <netstream.html>`_.
   PermittedPeers may not be set in anonymous modes.
-  **$InputTCPServerBindRuleset** <ruleset>
    Binds the listener to a specific `ruleset <multi_ruleset.html>`_.
-  **$InputTCPSupportOctetCountedFraming** <**on**\ \|off>
    If set to "on", the legacy octed-counted framing (similar to RFC5425
   framing) is activated. This is the default and should be left
   unchanged until you know very well what you do. It may be useful to
   turn it off, if you know this framing is not used and some senders
   emit multi-line messages into the message stream.

**Caveats/Known Bugs:**

-  module always binds to all interfaces
-  can not be loaded together with `imgssapi <imgssapi.html>`_ (which
   includes the functionality of imtcp)

**Example:**

This sets up a TCP server on port 514 and permits it to accept up to 500
connections:

$ModLoad imtcp # needs to be done just once $InputTCPMaxSessions 500
$InputTCPServerRun 514

Note that the parameters (here: max sessions) need to be set **before**
the listener is activated. Otherwise, the parameters will not apply.

[`rsyslog.conf overview <rsyslog_conf.html>`_\ ] [`manual
index <manual.html>`_\ ] [`rsyslog site <http://www.rsyslog.com/>`_\ ]

This documentation is part of the `rsyslog <http://www.rsyslog.com/>`_
project.
 Copyright © 2008,2009 by `Rainer
Gerhards <http://www.gerhards.net/rainer>`_ and
`Adiscon <http://www.adiscon.com/>`_. Released under the GNU GPL version
3 or higher.