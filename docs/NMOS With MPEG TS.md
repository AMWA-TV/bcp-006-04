# AMWA BCP-006-0x: NMOS With MPEG TS
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_
![NMOS logo](images/NMOS-logo.png)

[MPEG Transport Stream][MPEG-TS] is a standard digital container format specified in ISO/IEC 13818-1, widely used for transmission and storage of audio, video, and data.

SMPTE [ST 2022-2][ST-2022-2] defines the Unidirectional Transport of Constant Bit Rate MPEG-2 Transport Streams on IP Networks.

The [Video Services Forum][VSF] developed Technical Recommendations [TR-01][TR-01] and [TR-07][TR-07], which cover the encapsulation of JPEG 2000 and JPEG XS video respectively within MPEG TS over IP networks, compliant with ST 2022-2.

AMWA IS-04 and IS-05 have support for RTP transport and can signal the media type `video/MP2T` as defined in [RFC 3555][RFC-3555].

All IS-05 standards referenced in the RTP transport type are supported, including those defined in the following SMPTE standards [SMPTE 2022-5][ST-2022-5], [SMPTE 2022-6][ST-2022-6] and [SMPTE 2022-7][ST-2022-7].

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary][NMOS-Glossary].

## MPEG TS IS-04 Sources, Flows, and Senders

Nodes capable of transmitting MPEG TS streams, including those carrying JPEG 2000 or JPEG XS content as per VSF TR-01 and TR-07, **MUST** have Source, Flow, and Sender resources in the IS-04 Node API.

Nodes **MUST** support IS-04 v1.3.x to implement all aspects of this specification.

Partial implementation can be achieved using IS-04 v1.2 and earlier.

### Sources

The Source resource **MUST** indicate `urn:x-nmos:format:mux` for the `format`.

Source resources can be associated with many Flows at the same time.

The Source is therefore unaffected by the use of MPEG TS or the encapsulated content.

### Flows

The Flow resource **MUST** indicate `video/MP2T` in the `media_type` attribute, and `urn:x-nmos:format:mux` for the `format`.

This has been permitted since IS-04 v1.1.

For Nodes implementing IS-04 v1.3.x or higher, the following additional attributes defined in the [Flow Attributes register][Flow-Attributes] of the NMOS Parameter Registers are used for MPEG TS:

- [Bit Rate][Flow-Bit-Rate]
  The Flow resource **SHOULD** indicate the bit rate (in bits per second) of the MPEG TS stream.

  The `bit_rate` integer value is expressed in bits per second, rounding up.

- [TS Format][Flow-TS-Format]
  The Flow resource **MUST** indicate the `ts_format` attribute. For streams encapsulating JPEG 2000 or JPEG XS as per TR-01 and TR-07, the `ts_format` **MUST** be set to `spts`. It **SHOULD NOT** be set to `mpts` in these cases.

  The `ts_format` attribute takes the values `spts` or `mpts`.

- [Encapsulated Format][Flow-Encapsulated-Format]
  The Flow resource **MUST** indicate the format of the encapsulated content within the MPEG TS stream if it is JPEG 2000 or JPEG XS. It **MUST NOT** be included for other content.

  The `encapsulated_format` attribute takes values such as `video/jpeg2000` for JPEG 2000 (as per TR-01) or `video/jxsv` for JPEG XS (as per TR-07).

An example Flow resource is provided in the [Examples](../examples/).

### Senders

For Nodes transmitting MPEG TS over RTP transport as defined by ST 2022-2, the Sender `transport` attribute **MUST** be `urn:x-nmos:transport:rtp`.

Sender resources provide no indication of media type or format since this is described by the associated Flow resource.

The SDP file at the `manifest_href` **MUST** comply with the requirements of ST 2022-2 for RTP.

Additionally, the SDP file needs to convey, so far as the defined parameters allow, the same information about the stream as conveyed by the Source, Flow, and Sender attributes (or their defaults, when omitted) defined by this specification and IS-04.

Therefore:

- The correct `media_type` (`video/MP2T`) **MUST** be included in all cases.

- The `bit_rate` attribute **SHOULD** be included if possible.

- For streams encapsulating JPEG 2000 or JPEG XS, the `encapsulated_format` attribute **MUST** be included.

An example Sender resource is provided in the [Examples](../examples/).

## MPEG TS IS-04 Receivers

Nodes capable of receiving MPEG TS streams, including those carrying JPEG 2000 or JPEG XS content as per VSF TR-01 and TR-07, **MUST** have a Receiver resource in the IS-04 Node API, which lists `video/MP2T` in the `media_types` array within the `caps` object.

This has been permitted since IS-04 v1.1.

If the Receiver supports RTP transport, it **MUST** have the `transport` attribute set to `urn:x-nmos:transport:rtp`.

If the Receiver has limitations on or preferences regarding the MPEG TS streams or the encapsulated content that it supports, the Receiver resource **MUST** indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.

The Receiver **SHOULD** express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams.

It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they **SHOULD** describe as many of its commonly used operating points as practical and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of parameters which the Receiver can support, using the parameter constraints defined in the [Capabilities register][Capabilities-Register] of the NMOS Parameter Registers.

The following parameter constraints can be used to express limitations on supported MPEG TS streams:

- [Transport Bit Rate][Transport-Bit-Rate]

- [TS Format][Transport-TS-Format]

- [Encapsulated Format][Transport-Encapsulated-Format]

- [Transport Stream ID][Transport-Stream-ID]

An example Receiver resource is provided in the [Examples](../examples/).

## MPEG TS IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried over RTP.

The SDP file at the **/transportfile** endpoint on an IS-05 Sender **MUST** comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

A `PATCH` request on the **/staged** endpoint of an IS-05 Receiver can contain an SDP file in the `transport_file` attribute.

The SDP file for an MPEG TS stream is expected to comply with [RFC 4566][RFC-4566], ST 2022-2 for RTP transport.

For streams encapsulating JPEG 2000 or JPEG XS, the `encapsulated_format` attribute **MUST** be included.

If the Receiver is not capable of consuming the stream described by the SDP file, it **SHOULD** reject the request.

If it is unable to assess the stream compatibility, for example when the SDP file does not include some of the optional parameters, it **MAY** accept the request.

An example SDP file is provided in the [Examples](../examples/).

## Controllers

Controllers **MUST** use IS-04 to discover MPEG TS Senders and Receivers and IS-05 to manage connections between them.

Controllers **MUST** support IS-04 v1.3.x to implement all aspects of this specification.

Partial implementation can be achieved using IS-04 v1.2 and earlier.

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
[ST-2022-5]:  https://ieeexplore.ieee.org/document/7291908 "Forward Error Correction for High Bit Rate Media Transport over IP Networks"
[ST-2022-6]: https://ieeexplore.ieee.org/document/7289943 "Transport of High Bit Rate Media Signals over IP Networks (HBRMT)"
[ST-2022-7]:  https://ieeexplore.ieee.org/document/7291851 "Seamless Protection Switching of SMTPE ST 2022 IP Datagrams"
[RFC-4566]: https://datatracker.ietf.org/doc/html/rfc4566 "SDP: Session Description Protocol"
[NMOS-Glossary]: https://specs.amwa.tv/nmos/main/docs/Glossary.html "NMOS Glossary"
[Capabilities-Register]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/ "Capabilities Register"
[Flow-Attributes]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/ "Flow Attributes Register"
[Flow-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate "Flow Bit Rate"
[Flow-TS-Format]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#ts-format "Flow TS Format"
[Flow-Encapsulated-Format]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#encapsulated-format "Flow Encapsulated Format"
[Transport-Bit-Rate]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate "Transport Bit Rate"
[Transport-TS-Format]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#ts-format "TS Format"
[Transport-Encapsulated-Format]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#encapsulated-format "Encapsulated Format"
[Transport-Stream-ID]: https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-stream-id "Transport Stream ID"


# Notes for the AMWA BCP MPEG-TS in NMOS working group
Do we want to incorporate FEC support, or does this belong in a separate BCP -> [ST-2022-1]: https://ieeexplore.ieee.org/document/7291470 "SMPTE ST 2022-1: SMPTE Standard - Forward Error Correction for Real-Time Video/Audio Transport Over IP Networks"
