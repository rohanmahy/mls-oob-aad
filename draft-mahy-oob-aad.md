---
title: "MLS Extension for Out-Of-Band Additional Authenticated Data"
abbrev: "MLS Out-Of-Band AAD"
category: info

docname: draft-mahy-oob-aad-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup: WG Working Group
keyword:
 - MLS AAD
 - additional authenticated data
 - authenticated_data
 - sage_aad
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -
    fullname: Rohan Mahy
    email: rohan.ietf@gmail.com

normative:

informative:

...

--- abstract

This document specifies an explicit way to signal that the Additional Authenticated Data does include or should include data elements that are conveyed out-of-band (ex: are not in the MLS message).


--- middle

# Introduction

The Messaging Layer Security (MLS) protocol includes message types with Additional Authenticated Data.

{{?I-D.pham-mls-additional-wire-formats}} defined new MLS Wire Formats for a similar purpose, however the Wire Format should be orthogonal to out-of-band AAD functionality.

# Conventions and Definitions

{::boilerplate bcp14-tagged}




Out-Of-Band AAD and In-Band AAD.

# Mechanism


~~~ tls
struct {
    opaque group_id<V>;
    uint64 epoch;
    ContentType content_type;
    SafeAADItem oob_aad<V>;        /*  <== this is new     */
    opaque authenticated_data<V>;  /* usually just SafeAAD */
} PrivateContentAAD;

struct {
    ProtocolVersion version = mls10;
    WireFormat wire_format;
    FramedContent content;         /* can contain SafeAAD  */
    SafeAADItem oob_aad<V>;        /*  <== this is new     */
    select (FramedContentTBS.content.sender.sender_type) {
        case member:
        case new_member_commit:
            GroupContext context;
        case external:
        case new_member_proposal:
            struct{};
    };
} FramedContentTBS;
~~~



Consists of:

- a `oob-aad` extension type. This specification cannot be a "safe" extension according to {{!I-D.ietf-mls-extensions}} since it requires the MLS stack to modify its behavior outside of the concept of the specific components it also defines. The presence of the `oob-aad` extension type in the GroupContext means that all members of the group support the extensions and that if an out-of-band AAD component is signaled, the members of the room will include its actual value in Out-Of-Band AAD, without communicating it in the `authenticated_data`.
- an `default-oob-aad-component` component type. When present in the `app_data_dictionary` in the GroupContext
- an `as-needed-oob-aad-component` component type.
  - When present in the `app_data_dictionary` in the GroupContext it enumerates OOB AAD that can be signaled on a per-message basis.
  - When present as a `SafeAadItem` in the `aad_items` list in the `authenticated_content`, the out-of-band value of each signaled as-needed component is also included in the Out-Of-Band AAD.



# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
