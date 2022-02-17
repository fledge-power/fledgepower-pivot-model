# fledgepower-pivot-model
Pivot object data model specifications

## Protocol translation
### Pivot object definition
Before diving into the details of the specification, it is useful to illustrate some use cases. They should help demonstrate how the specification is expected to be used and to help understand the benefits of a pivot object.

The objective of a gateway is to allow communication between different systems by providing some translation mechanisms from one means of communication to another one.

All translation mechanisms introduce a limitation of functionality in the translated space compared to the original space. The major challenge is to provide some correspondences between the two systems, with a limited impact to the original scope of features.

### Use case 1: monitoring
Single Substation/RTU gateway

In this use case, monitoring data are acquired from a field device called RTU (Remote Terminal Unit) which acts as a IEC 104 server (slave) and are exposed to the central SCADA which acts as a ICCP TASE.2 client. Each incoming message from the RTU comes as an IEC 104 ASDU object. The south service plugin applies a first translation process which consists of (1) reading and checking the ASDU object using the IEC 104 data model and (2) mapping each data attribute to the corresponding pivot object data attribute. Once the message is converted to a pivot object, it can then be stored or consumed by the north or any other service. The north service plugin can then apply a second translation process which consists of (3) reading and checking the pivot object using the pivot data model and (4) mapping each data attribute to the corresponding TASE.2 data attribute. The resulting TASE.2 object can then be exposed to the TASE.2 client by the north service plugin.

Multiple Substation/RTU gateway

In this alternative scenario, the gateway is connected to multiple RTUs located in multiple substations. Each South plugin (IEC 104 client) is connected to an RTU (IEC 104 server) to collect data. The collected data flow then through the Storage and the North plugin (TASE.2) to expose data to the SCADA.

### Use case 2: monitoring and control
Single Scada/single protocol

In this use case data flows in two directions: monitoring and control.

Monitoring data are acquired from a field device called IED (Intelligent Electronic Device) which acts as a 61850 MMS server and are exposed to the central SCADA which acts as a OPC UA client. Each incoming message from the IED comes as a 61850 MMS object. The south service plugin applies a first translation process which consists of (1) reading and checking the 61850 MMS object using the 61850 MMS data model and (2) mapping each data attribute to the corresponding pivot object data attribute. Once the message is converted to a pivot object, it can then be stored or consumed by the north or any other service. The north service plugin can then apply a second translation process which consists of (3) reading and checking the pivot object using the pivot object data model and (4) mapping each data attribute to the corresponding OPC UA data attribute. The resulting OPC UA object can then be exposed to the OPC UA client by the north service plugin.

Control data are received from the control center SCADA which acts as a OPC UA client and are sent to the IED which acts as a 61850 server. Each incoming message from the control center SCADA comes as an OPC UA object. The north service plugin applies a first translation process which consists of (1) reading and checking the OPC UA object using the OPC UA data model and (2) mapping each data attribute to the corresponding pivot object data attribute. Once the message is converted to a pivot object, it can then be stored or consumed by the south or any other service. The south service plugin can then apply a second translation process which consists of (3) reading and checking the pivot object using the pivot object data model and (4) mapping each data attribute to the corresponding 61850 MMS data attribute. The resulting 61850 MMS object can then be exposed to the 61850 client by the south service plugin.

Multiple Scadas/multiple protocols

In this alternative scenario, the gateway is connected to multiple SCADAs using two different protocols. The North plugin (TASE.2) exposes data to the TASE.2 SCADA and the North plugin OPC UA exposes data to the OPC UA SCADA. Instead of having a one to one translation process (61850 to TASE.2, 61850 to OPC UA, TASE.2 to 61850, OPC UA to 61850), we use the pivot object to transmit data from 61850 to both TASE.2 and OPC UA. The translation process is done only once for each protocol.

## Benefits and challenges of a pivot object
### Benefits
The main benefit of the pivot object is it allows decoupling the protocols data model from each other. Each south or north plugin deals with the complexity and the specifics of a given protocol without interfering with the core of the gateway or with another south/north plugin. Adding new protocol plugins or maintaining existing ones is then made much easier. This contributes in maintaining the whole gateway system at a low level of complexity thus minimizing the costs of new developments or maintenance.

### Challenges
In order to reach the goal of simplicity we want to achieve, the main challenge is to be able to build a pivot object that is technically independent from the protocols. The data model should also be neutral regarding protocols data models. The protocol translation should not imply a loss or degradation of the source information, especially in the case where the input and output protocols are identical.
