
TibcoEMSMonitor
===============

## Introduction

An AppDynamics Machine Agent add-on to report metrics from a [Tibco EMS 
Server][] and its queues.

Tibco EMS is messaging middleware that provides persistent queues as well as a 
publish/subscribe mechanism. It can be used as a JMS provider, or it can be 
used directly via native APIs.

This extension requires the Java Machine Agent.


## Prerequisites

Before starting this monitor, make sure your EMS server is configured to report 
statistics. You can do this by editing the file `tibemsd.conf` in your 
`TIBCO_HOME`, or by using the `tibemsadmin` command line utility.


#### Configuring statistics by editing tibemsd.conf

1. Add the following line to `tibemsd.conf`:

        statistics = enabled

2. Restart Tibco EMS.


#### Configuring statistics using the command line

Use the `tibemsadmin` utility to change the server configuration.

        root# tibemsadmin -server localhost:7222

        TIBCO Enterprise Message Service Administration Tool.
        Copyright 2003-2013 by TIBCO Software Inc.
        All rights reserved.

        Version 8.0.0 V9 6/7/2013

        Login name (admin):
        Password:
        Connected to: tcp://localhost:7222
        Type 'help' for commands help, 'exit' to exit:
        tcp://localhost:7222> set server statistics=enabled
        Server parameters have been changed
        tcp://localhost:7222> quit
        bye


## Installation

1. Download TibcoEMSMonitor.zip from the Community.
2. Copy TibcoEMSMonitor.zip into the directory where you installed the machine 
   agent, under `$AGENT_HOME/monitors`.
3. Unzip the file. This will create a new directory called `TibcoEMSMonitor`.
4. In `$AGENT_HOME/monitors/TibcoEMSMonitor`, edit the file `monitor.xml` and 
   configure the extension for your Tibco EMS installation.
5. Restart the machine agent.


## Configuration

Configuration for this monitor is in the `monitor.xml` file in the monitor 
directory. All of the configurable options are in the `<task-arguments>` 
section.

 * _hostname_ - Name or IP address of the Tibco EMS server. Required.
 * _port_ - TCP port number where the Tibco server is listening. The default value is 7222. Required.
 * _protocol_ - Specify "tcp" to use standard TCP or "ssl" to use SSL. The default is "tcp".
 * _userid_ - Administrative user ID for the Tibco admin interface. The default value is "admin". Required.
 * _password_ - Password for the administrative user ID. The default value is an empty password. Required.
 * _tier_ - Name of the tier in AppDynamics for which the monitor should register its
   metrics. If not specified, the metrics will be registered on all tiers.
   Optional.
 * _emsServerName_ - An additional folder to create under the "Custom Metrics" folder. If you are
   using this extension to monitor several EMS servers, you can use this option
   to put each server into its own folder. Optional.
 * _showTempQueues_ - If set to true, the monitor will report metrics on temporary queues (defined
   as any queue whose name starts with `$TMP$.`). **NOTE:** Enabling this option
   can potentially cause the agent to overflow its metric limit.
 * _showSysQueues_ - If set to true, the monitor will report metrics on system queues (defined as
   any queue whose name starts with `$sys.`).
 * _queuesToExclude_ - A whitespace-separated list of regex patterns. Queues that match any of the 
   patterns will be excluded from the output. Optional.
 * _topicsToExclude_ - A whitespace-separated list of regex patterns. Topic names that match any of the 
   patterns will be excluded from the output. Optional.

## Metrics Provided

### Global Instance Metrics

| Metric Name               | Description                                                           |
| :------------------------ | :-------------------------------------------------------------------- |
| DiskReadRate              | Rate at which messages are being read from disk, in bytes per second  |
| DiskWriteRate             | Rate at which messages are being written to disk, in bytes per second |
| ConnectionCount           | Current number of connections                                         |
| MaxConnections            | Maximum number of connections allowed by the server                   |
| ProducerCount             | Total number of producers                                             |
| ConsumerCount             | Total number of consumers                                             |
| PendingMessageCount       | Total number of pending messages                                      |
| PendingMessageSize        | Total size of pending messages in bytes                               |
| InboundBytesRate          | Instantaneous inbound bytes per second                                |
| InboundMessageRate        | Instantaneous inbound messages per second                             |
| InboundMessageCount       | Total number of inbound messages received since EMS was restarted     |
| InboundMessagesPerMinute  | Messages received per minute                                          |
| OutboundBytesRate         | Instantaneous outbound bytes per second                               |
| OutboundMessageRate       | Instantaneous outbound messages per second                            |
| OutboundMessageCount      | Number of outbound messages sent since EMS was restarted              |
| OutboundMessagesPerMinute | Messages sent per minute                                              |


### Per-Queue Metrics

| Metric Name               | Description |
| :------------------------ | :---------- |
| ConsumerCount             | Number of consumers for this destination |
| ReceiverCount             | Number of active receivers on this queue |
| DeliveredMessageCount     | Total number of messages that have been delivered to consumer applications but have not yet been acknowledged |
| PendingMessageCount       | Total number of pending messages for this destination |
| InTransitCount            | Total number of messages that have been delivered to the queue owner but have not yet been acknowledged |
| FlowControlMaxBytes       | Volume of pending messages (in bytes) at which flow control is enabled for this destination |
| PendingMessageSize        | Total size for all pending messages for this destination |
| MaxMsgs                   | Maximum number of messages that the server will store for pending messages bound for this destination |
| MaxBytes                  | Maximum number of message bytes that the server will store for pending messages bound for this destination |
| InboundByteRate           | Instantaneous bytes received per second |
| InboundMessageRate        | Instantaneous messages received per second |
| InboundByteCount          | Total number of bytes received since EMS was restarted |
| InboundMessageCount       | Total number of messages received since EMS was restarted |
| InboundBytesPerMinute     | Bytes received per minute |
| InboundMessagesPerMinute  | Messages received per minute |
| OutboundByteRate          | Instantaneous bytes sent per second |
| OutboundMessageRate       | Instantaneous messages sent per second |
| OutboundByteCount         | Total number of bytes sent since EMS was restarted |
| OutboundMessageCount      | Total number of messages sent since EMS was restarted |
| OutboundBytesPerMinute    | Bytes sent per minute |
| OutboundMessagesPerMinute | Messages sent per minute |


## Caution

This monitor can potentially register hundred of new metrics, depending on how 
many queues are in EMS. By default, the Machine Agent will only report 200 
metrics to the controller, so you may need to increase that limit when 
installing this monitor. To increase the metric limit, you must add a parameter 
when starting the Machine Agent, like this:

    java -Dappdynamics.agent.maxMetrics=1000 -jar machineagent.jar


## Support

For any questions or feature requests, please contact the [AppDynamics Center 
of Excellence][].

**Version:** 2.3.2  
**Controller Compatibility:** 3.6 or later  
**Last Updated:** 20-Oct-2014  
**Author:** Todd Radel  

------------------------------------------------------------------------------

## Release Notes

### Version 2.3.6
  - Changed logging level in shouldMonitorDestination() from `INFO` to `DEBUG`.

### Version 2.3.5
  - Adds new SSL configuration properties: `sslIdentityFile`, `sslIdentityPassword`, `sslTrustedCerts`, 
    `sslIssuerCerts`, `sslDebug`, `sslVendor`, `sslVerifyHost`, `sslVerifyHostName`. 
    Please check the `monitor.xml` bundled with this release for details.

### Version 2.3.4
  - Adding support for SSL and topics.

### Version 2.3.2
  - Added new derived metrics: InboundMessagesPerMinute, OutboundMessagesPerMinute, 
    InboundBytesPerMinute, and OutboundBytesPerMinute.
  - General cleanup of code.

### Version 2.3.1
  - Added `queuesToExclude` option to configuration.

### Version 2.3
  - Added protocol (tcp/ssl) option to configuration.

### Version 2.2.1
  - Recompiled for target JDK 1.5.

### Version 2.2.0
  - Cleaned up directory structure.
  - Rebuilt Ant scripts.
  

[Tibco EMS Server]: http://www.tibco.com/products/automation/messaging/enterprise-messaging/enterprise-message-service/default.jsp
[AppDynamics Center of Excellence]: mailto:ace-request@appdynamics.com
[help@appdynamics.com]: mailto:help@appdynamics.com
