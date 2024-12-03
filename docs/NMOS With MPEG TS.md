# AMWA BCP-006-04: NMOS Support for MPEG Transport Streams \[Work In Progress\]
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2024, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_
![NMOS logo](images/NMOS-logo.png)

[MPEG Transport Stream][MPEG-TS] is a standard digital container format specified in ISO/IEC 13818-1, widely used for transmission and storage of audio, video, and data.

SMPTE [ST 2022-2][ST-2022-2] defines the Unidirectional Transport of Constant Bit Rate MPEG-2 Transport Streams using RTP on IP Networks.

This BCP allows for the use of all transport types defined in the [Transports][Transports-Registry] registry that can carry media type `video/MP2T`.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary][NMOS-Glossary].

## MPEG TS IS-04 Sources, Flows

Nodes capable of transmitting MPEG TS streams **MUST** have Source, Flow, and Sender resources in the IS-04 Node API.

Nodes **MUST** support IS-04 v1.3 to implement all aspects of this specification.

Partial implementation can be achieved using IS-04 v1.2 and earlier.

### Sources

The Source resource **MUST** indicate `urn:x-nmos:format:mux` for the `format`.

Source resources can be associated with many Flows at the same time.

The Source is therefore unaffected by the use of MPEG TS or the encapsulated content.

### Flows
The Flow resource **MUST** indicate `video/MP2T` in the `media_type` attribute, and `urn:x-nmos:format:mux` for the `format`.

An example Flow resource is provided in the [Examples](../examples/).

## MPEG TS IS-04 Senders and Receivers
IS-04 Senders and Receivers **MUST** use transports defined in the [Transports][Transports-Registry] parameter registry.

### Senders
For Nodes implementing IS-04 v1.3 or higher, the following additional attributes defined in the [sender attributes register][Sender-Attributes] of the NMOS Parameter Registers are used for MPEG TS:

- [Bit Rate][Sender-Transport-Bit-Rate]
  This attribute **MUST** be present as defined in equipment where the bit rate is known.

The SDP file published at the `manifest_href` end-point **MUST** comply with the IS-05 usage guidelines for the specific transport and [RFC 4566][RFC-4566]

An example Sender resource is provided in the [Examples](../examples/).
## Receivers
Nodes capable of receiving MPEG TS streams **MUST** have a Receiver resource in the IS-04 Node API, which lists `video/MP2T` in the `media_types` array within the `caps` object.

If the Receiver has limitations on or preferences regarding the MPEG TS streams that it supports, the Receiver resource **MUST** indicate constraints in accordance with the [BCP 004-01][BCP-004-01] Receiver Capabilities specification.

The Receiver **SHOULD** express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams.

It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they **SHOULD** describe as many of its commonly used operating points as practical and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of parameters which the Receiver can support, using the parameter constraints defined in the [Capabilities register][Capabilities-Register] of the NMOS Parameter Registers.

The following parameter constraints **MAY** be used to express limitations on MPEG TS streams:
- [Transport Bit Rate][Cap-Transport-Bit-Rate]
    
An example Receiver resource is provided in the [Examples](../examples/).

## MPEG TS IS-05 Senders and Receivers

Connection Management follows IS-05 for the transport type.

Any SDP file published at the **/transportfile** end-point **MUST** comply with the IS-05 usage guidelines for the RTP transport and [RFC 4566][RFC-4566].

An example SDP file is provided in the [Examples](../examples/).

## Controllers

Controllers **MUST** use IS-04 to discover MPEG TS Senders and Receivers and IS-05 to manage connections between them.

Controllers **MUST** support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between MPEG TS Senders and Receivers.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[MPEG-TS]: https://www.iso.org/standard/69461.html "ISO/IEC 13818-1 Systems"
[VSF]: https://vsf.tv/ "Video Services Forum"
[RFC-2119]: https://datatracker.ietf.org/doc/html/rfc2119 "Key words for use in RFCs"
[ST-2022-2]: https://ieeexplore.ieee.org/document/7291602 "SMPTE ST 2022-2: Unidirectional Transport of Constant Bit Rate MPEG-2 Transport Streams on IP Networks"
[RFC-4855]: https://datatracker.ietf.org/doc/html/rfc4855 "Media Type Registration of RTP Payload Formats"
[NMOS-Glossary]: https://specs.amwa.tv/nmos/main/docs/Glossary.html "NMOS Glossary"
[Capabilities-Register]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/ "Capabilities Register"
[Sender-Attributes]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes "Sender Attributes Register"
[Sender-Transport-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate "Sender Transport Bit Rate"
[Cap-Transport-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate "Transport Bit Rate"
[Transports-Registry]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/transports "Transports"


