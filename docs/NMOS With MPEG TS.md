# AMWA BCP-006-0x: NMOS with MPEG TS \[Work In Progress\]
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2024, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_
![NMOS logo](images/NMOS-logo.png)

[MPEG Transport Stream][MPEG-TS] is a standard digital container format specified in ISO/IEC 13818-1, widely used for transmission and storage of audio, video, and data.

SMPTE [ST 2022-2][ST-2022-2] defines the Unidirectional Transport of Constant Bit Rate MPEG-2 Transport Streams on IP Networks.

The [Video Services Forum][VSF] developed Technical Recommendations [TR-01][TR-01] and [TR-07][TR-07], which cover the encapsulation of JPEG 2000 and JPEG XS video respectively within MPEG TS over IP networks, compliant with ST 2022-2.

AMWA IS-04 and IS-05 have support for RTP transport and can signal the media type `video/MP2T` as defined in [RFC 3555][RFC-3555].

This BCP allows for the use of all transport types defined by IS-05 that can carry media type `video/MP2T`.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary][NMOS-Glossary].

## MPEG TS IS-04 Sources, Flows, and Senders

Nodes capable of transmitting MPEG TS streams, including those carrying JPEG 2000 or JPEG XS content as per VSF TR-01 and TR-07, **MUST** have Source, Flow, and Sender resources in the IS-04 Node API.

Nodes **MUST** support IS-04 v1.3 to implement all aspects of this specification.

Partial implementation can be achieved using IS-04 v1.2 and earlier.

### Sources

The Source resource **MUST** indicate `urn:x-nmos:format:mux` for the `format`.

Source resources can be associated with many Flows at the same time.

The Source is therefore unaffected by the use of MPEG TS or the encapsulated content.

### Flows
The Flow resource **MUST** indicate `video/MP2T` in the `media_type` attribute, and `urn:x-nmos:format:mux` for the `format`.

For Nodes implementing IS-04 v1.3 or higher, the following additional attributes defined in the [Flow Attributes register][Flow-Attributes] of the NMOS Parameter Registers are used for MPEG TS:

- [Bit Rate][Flow-Bit-Rate]
  This attribute **MUST** be present in the flow resource definition.
  
An example Flow resource is provided in the [Examples](../examples/).

### Senders
For Nodes transmitting MPEG TS over RTP transport as defined by ST 2022-2, the Sender `transport` attribute **MUST** be `urn:x-nmos:transport:rtp` or one of its sub-classifications.

The SDP file published at the `manifest_href` end-point **MUST** comply with the IS-05 usage guidelines for the specific transport and [RFC 4566][RFC-4566]

An example Sender resource is provided in the [Examples](../examples/).

## MPEG TS IS-04 Receivers

Nodes capable of receiving MPEG TS streams, including those carrying JPEG 2000 or JPEG XS content as per VSF TR-01 and TR-07, **MUST** have a Receiver resource in the IS-04 Node API, which lists `video/MP2T` in the `media_types` array within the `caps` object.

If the Receiver supports RTP transport, it **MUST** have the `transport` attribute set to `urn:x-nmos:transport:rtp` or one of its sub-classifications..

If the Receiver has limitations on or preferences regarding the MPEG TS streams that it supports, the Receiver resource **MUST** indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.

The Receiver **SHOULD** express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams.

It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they **SHOULD** describe as many of its commonly used operating points as practical and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of parameters which the Receiver can support, using the parameter constraints defined in the [Capabilities register][Capabilities-Register] of the NMOS Parameter Registers.

The following parameter constraints can be used to express limitations on MPEG TS streams:
- [Transport Bit Rate][Cap-Bit-Rate]
    
An example Receiver resource is provided in the [Examples](../examples/).

## MPEG TS IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried over the RTP tranport type.

Any SDP file published at the **/transportfile** end-point **MUST** comply with the IS-05 usage guidelines for the RTP transport and [RFC 4566][RFC-4566].

An example SDP file is provided in the [Examples](../examples/).

## Controllers

Controllers **MUST** use IS-04 to discover MPEG TS Senders and Receivers and IS-05 to manage connections between them.

Controllers **MUST** support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between MPEG TS Senders and Receivers.

Controllers **MUST** be capable of handling RTP transports as per the NMOS specifications indicated in the Sender and Receiver resources.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[MPEG-TS]: https://www.iso.org/standard/69461.html "ISO/IEC 13818-1 Systems"
[TR-01]: https://vsf.tv/download/technical_recommendations/VSF_TR-01_2018-06-22.pdf "VSF TR-01 Transport of JPEG 2000 Broadcast Profile Video in MPEG-2 TS over IP"
[TR-07]: https://vsf.tv/download/technical_recommendations/VSF_TR-07_2020-04-01.pdf "VSF TR-07 Transport of JPEG XS Video in MPEG-2 TS over RTP"
[VSF]: https://vsf.tv/ "Video Services Forum"
[RFC-2119]: https://datatracker.ietf.org/doc/html/rfc2119 "Key words for use in RFCs"
[RFC-3550]: https://datatracker.ietf.org/doc/html/rfc3550 "RTP: A Transport Protocol for Real-Time Applications"
[RFC-3555]: https://datatracker.ietf.org/doc/html/rfc3555 "MIME Type Registration of RTP Payload Types"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[ST-2022-2]: https://ieeexplore.ieee.org/document/7291602 "SMPTE ST 2022-2: Unidirectional Transport of Constant Bit Rate MPEG-2 Transport Streams on IP Networks"
[RFC-4566]: https://datatracker.ietf.org/doc/html/rfc4566 "SDP: Session Description Protocol"
[NMOS-Glossary]: https://specs.amwa.tv/nmos/main/docs/Glossary.html "NMOS Glossary"
[Capabilities-Register]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/ "Capabilities Register"
[Flow-Attributes]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/ "Flow Attributes Register"
[Flow-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate "Flow Bit Rate"
[Flow-Mux-Video-Media-Type]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#mux_video_media_type "Mux Video Media Type"
[Cap-Mux-Video-Media-Type]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#mux_video_media_type "Mux Video Media Type"
[Cap-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate "Transport Bit Rate"
[Cap-Stream-ID]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-stream-id "Transport Stream ID"
[IANA-MIME-Types]: https://www.iana.org/assignments/media-types "IANA Media Types"
