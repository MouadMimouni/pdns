Logging DNS messages with Protocol Buffers
==========================================
The PowerDNS Recursor has the ability to emit a stream of protocol buffers messages over TCP, containing information about queries, answers and policy decisions.

Messages contain the IP address of the client initiating the query, the one on which the message was received, whether it was received over UDP or TCP, a timestamp and the qname, qtype and qclass of the question.
In addition, messages related to responses contain the name, type, class and rdata of A, AAAA and CNAME records present in the response, as well as the response code.

Finally, if a RPZ or custom Lua policy has been applied, response messages also contain the applied policy name and some tags.
This is particularly useful to detect and act on infected hosts.

Configuring Protocol Buffer logs
--------------------------------
Protobuf export to a server is enabled using the ``protobufServer()`` directive:

.. function:: protobufServer(server [, options]))

  .. versionadded:: 4.2.0

  Send protocol buffer messages to a server for incoming queries and/or outgoing responses. The client address may be masked using :func:`setProtobufMasks`, for anonymization purposes.

  :param string server: The IP and port to connect to
  :param table options: A table with key: value pairs with options.

  Options:

  * ``timeout=2``: int - Time in seconds to wait when sending a message
  * ``maxQueuedEntries=100``: int - How many entries will be kept in memory if the server becomes unreachable
  * ``reconnectWaitTime=1``: int - How long to wait, in seconds, between two reconnection attempts
  * ``taggedOnly=false``: bool - Only entries with a policy or a policy tag set will be sent
  * ``asyncConnect``: bool - When set to false (default) the first connection to the server during startup will block up to ``timeout`` seconds, otherwise the connection is done in a separate thread, after the first message has been queued
  * ``logQueries=true``: bool - Whether to export queries
  * ``logResponses=true``: bool - Whether to export responses
  * ``exportTypes={'A', 'AAAA', 'CNAME'}``: list of strings - The list of record types found in the answer section to export. Only A, AAAA, CNAME, MX, NS, PTR, SPF, SRV and TXT are currently supported

.. function:: protobufServer(server [[[[[[[, timeout=2], maxQueuedEntries=100], reconnectWaitTime=1], maskV4=32], maskV6=128], asyncConnect=false], taggedOnly=false])

  .. deprecated:: 4.2.0

  :param string server: The IP and port to connect to
  :param int timeout: Time in seconds to wait when sending a message
  :param int maxQueuedEntries: How many entries will be kept in memory if the server becomes unreachable
  :param int reconnectWaitTime: How long to wait, in seconds, between two reconnection attempts
  :param int maskV4: network mask to apply to the client IPv4 addresses, for anonymization purposes. The default of 32 means no anonymization.
  :param int maskV6: Same as maskV4, but for IPv6. Defaults to 128.
  :param bool taggedOnly: Only entries with a policy or a policy tag set will be sent.
  :param bool asyncConnect: When set to false (default) the first connection to the server during startup will block up to ``timeout`` seconds, otherwise the connection is done in a separate thread, after the first message has been queued..

.. function:: setProtobufMasks(maskv4, maskV6)

  .. versionadded:: 4.2.0

  :param int maskV4: network mask to apply to the client IPv4 addresses, for anonymization purposes. The default of 32 means no anonymization.
  :param int maskV6: Same as maskV4, but for IPv6. Defaults to 128.

Logging outgoing queries and responses
--------------------------------------

While :func:`protobufServer` only exports the queries sent to the recursor from clients, with the corresponding responses, ``outgoingProtobufServer()`` can be used to export outgoing queries sent by the recursor to authoritative servers, along with the corresponding responses.

.. function:: outgoingProtobufServer(server [, options])

  .. versionadded:: 4.2.0

  Send protocol buffer messages to a server for outgoing queries and/or incoming responses.

  :param string server: The IP and port to connect to
  :param table options: A table with key: value pairs with options.

  Options:

  * ``timeout=2``: int - Time in seconds to wait when sending a message
  * ``maxQueuedEntries=100``: int - How many entries will be kept in memory if the server becomes unreachable
  * ``reconnectWaitTime=1``: int - How long to wait, in seconds, between two reconnection attempts
  * ``taggedOnly=false``: bool - Only entries with a policy or a policy tag set will be sent
  * ``asyncConnect``: bool - When set to false (default) the first connection to the server during startup will block up to ``timeout`` seconds, otherwise the connection is done in a separate thread, after the first message has been queued
  * ``logQueries=true``: bool - Whether to export queries
  * ``logResponses=true``: bool - Whether to export responses
  * ``exportTypes={'A', 'AAAA', 'CNAME'}``: list of strings - The list of record types found in the answer section to export. Only A, AAAA, CNAME, MX, NS, PTR, SPF, SRV and TXT are currently supported

.. function:: outgoingProtobufServer(server [[[[, timeout=2], maxQueuedEntries=100], reconnectWaitTime=1], asyncConnect=false])

  .. deprecated:: 4.2.0

  :param string server: The IP and port to connect to
  :param int timeout: Time in seconds to wait when sending a message
  :param int maxQueuedEntries: How many entries will be kept in memory if the server becomes unreachable
  :param int reconnectWaitTime: How long to wait, in seconds, between two reconnection attempts
  :param bool asyncConnect: When set to false (default) the first connection to the server during startup will block up to ``timeout`` seconds, otherwise the connection is done in a separate thread, after the first message has been queued..

Protobol Buffers Definition
---------------------------

The protocol buffers message types can be found in the `dnsmessage.proto <https://github.com/PowerDNS/pdns/blob/master/pdns/dnsmessage.proto>`_ file and is included here:

.. literalinclude:: ../../../dnsmessage.proto
