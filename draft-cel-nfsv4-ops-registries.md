---
title: "Registries of Network File System Version 4 Operations"
abbrev: "NFSv4 Operations Registries"
category: std

docname: draft-cel-nfsv4-ops-registries-latest
submissiontype: IETF
ipr: trust200902
updates: 8178
stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]
v: 3
area: "Web and Internet Transport"
workgroup: Network File System Version 4
keyword:
 - NFS
 - COMPOUND
 - IANA

venue:
  group: nfsv4
  type: Working Group
  mail: nfsv4@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/nfsv4/
  github: chucklever/i-d-ops-registries
  latest: https://chucklever.github.io/i-d-ops-registries/draft-cel-nfsv4-ops-registries.html

author:
 -
    fullname: Charles Lever
    role: editor
    country: United States of America
    email: cel-ietf@chucklever.net

normative:
  RFC7120:
  RFC7530:
  RFC7862:
  RFC7931:
  RFC8126:
  RFC8178:
  RFC8276:
  RFC8881:
  RFC9754:

informative:
  RFC4506:
  RFC7531:
  RFC5662:
  RFC7863:
  I-D.cel-nfsv4-fattr4-registry:

--- abstract

NFS version 4 operations and callback operations are identified by
numeric values that no IANA registry records, so protocol extensions
under development at the same time can assign the same value to
different operations.  This document requests an IANA registry of
NFSv4 COMPOUND operations and a registry of NFSv4 CB_COMPOUND
callback operations, populates them from published RFCs, and
requires future operations to obtain their values through them.  It
updates RFC 8178.


--- middle

# Introduction {#intro}

{{RFC7530}}, {{RFC8881}}, and {{RFC7862}} together specify three
minor versions of the Network File System (NFS) version 4 protocol.
Each minor version has exactly two RPC procedures: NULL and
COMPOUND.  A COMPOUND request carries a sequence of operations, and
every NFSv4 file access, locking, session, and pNFS action is
expressed as one of those operations.  The callback program that a
server uses to contact a client likewise has two procedures, CB_NULL
and CB_COMPOUND, and a CB_COMPOUND request carries a sequence of
callback operations.

The above specifications (and others) assign a numeric value to each
operation and to each callback operation.  The value is the
operation's constant in the nfs_opnum4 or nfs_cb_opnum4 XDR
enumeration and the discriminant of the union that carries the
operation's arguments and results within a COMPOUND or CB_COMPOUND.
It therefore identifies the operation on the wire.  Operations and
callback operations have separate value spaces: the value 3
identifies ACCESS in a COMPOUND and CB_GETATTR in a CB_COMPOUND.

{{RFC8178}} specifies a set of permitted mechanisms to extend the
NFSv4 protocol, but no registry records which operation values have
been assigned.  Assignment of new values is currently handled ad hoc
by the nfsv4 Working Group, and authors of concurrent extensions can
select the same value for different operations.  {{Section 8.6 of
RFC8276}}, for example, records that its values were checked by hand
against the specifications the authors were aware of.

It has been additionally observed that there is no single place that
maps each operation to the RFC (or RFCs) where that operation is
specified.  The Working Group assignment mechanism is not how
protocol-element-to-numeric-value mappings are commonly handled
elsewhere.

To address these concerns, this document requests the creation of
two new IANA registries, one for operations and one for callback
operations, so that value assignment is managed via a well-known and
authoritative mechanism.  The document provides an initial set of
entries for each registry based on values in published RFCs to
date.  Lastly, a new assignment mechanism is normatively specified
for operations and callback operations, updating {{RFC8178}}.

# Requirements Language

{::boilerplate bcp14-tagged}

# Allocation of Operation Values {#allocation}

## Background

Each NFSv4 operation is identified by a non-negative integer.  That
integer is the value of the operation's constant in the nfs_opnum4
enumeration (for example, OP_GETATTR has the value 9), and it
selects the arm of the nfs_argop4 and nfs_resop4 unions that carries
the operation's arguments and results.  Each callback operation is
likewise identified by a non-negative integer that is the value of
its constant in the nfs_cb_opnum4 enumeration (for example,
OP_CB_GETATTR has the value 3) and that selects the arm of the
nfs_cb_argop4 and nfs_cb_resop4 unions.  Unlike a fattr4 attribute
value, an operation value is not a bit position, so the set of
values is bounded only by the width of the enumeration.  In practice,
implementations index tables of operation handlers by operation
value, so sparse or very large values impose a cost on every
implementation.

{{Section 4.2 of RFC8178}} permits the addition of previously
unspecified operation codes, in either direction, as an XDR
extension, and {{Section 6 of RFC8178}} requires that such an
extension to an existing minor version be published as a Proposed
Standard.  {{RFC8178}} also forbids the deletion or reuse of an
operation code once assigned.  However, {{RFC8178}} does not say how
the numeric value for a new operation or callback operation is
chosen.  In practice, an author of an Internet-Draft
selects the next value after the highest one the author is aware of.
When two drafts are in progress concurrently, both may select the
same value, and the collision is discovered only when one of the
documents is revised or when implementations of the two features are
combined.

## The Registries {#mechanism}

This document creates the "NFSv4 Operations" registry and the
"NFSv4 Callback Operations" registry (see {{iana}}) as the single
authoritative sources of operation and callback operation value
assignments.  The rules below apply to both registries; where they
say "operation", read "operation or callback operation".  From the
publication of this document onward:

* As required by {{Section 6 of RFC8178}}, a new operation is
  specified in a document published as a Proposed Standard.  The
  registration policy for the registry is therefore Standards Action
  with Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}), which
  admits exactly that class of document and matches the policy of
  the other NFSv4 registries created by {{Section 22 of RFC8881}}.
  The role of the Designated Expert is described in {{expert}}.

* The document that specifies a new operation MUST include an IANA
  Considerations section requesting that the operation be added to
  the appropriate registry, and MUST obtain the operation's numeric
  value through that request.  The XDR constant for the operation is derived from
  the registered value; a document MUST NOT specify a constant that
  differs from the registered value.

* To eliminate collisions between concurrently developed extensions,
  values MAY be allocated before publication using the early
  allocation procedure of {{RFC7120}}.  A Working Group document that
  introduces a new operation SHOULD request early allocation once
  the Working Group has adopted it, and MUST NOT use a value that has
  not been either registered or early-allocated.

* Values are assigned sequentially, starting from the lowest
  unassigned value that is not reserved.  IANA MUST NOT assign a
  value that has been previously assigned, even if the corresponding
  operation has since been withdrawn from every minor version,
  because {{Section 4.2 of RFC8178}} prohibits reuse of an operation
  code.

* An operation is never removed from the registry.  A document that
  withdraws an operation updates the registry entry to record the
  withdrawal and the withdrawing document (see {{Section 6 of
  RFC8126}} for status terminology), but the value remains allocated.

* Each registry entry records the minor version in which the
  operation was introduced.  Per {{Section 8.2 of RFC8178}}, an
  operation introduced in one minor version is available in all
  later minor versions unless a later minor version explicitly
  removes support for it.

## Designated Expert Guidance {#expert}

The Designated Expert's review is confined to the registration
itself, not the merits of the operation, which are for the IETF
consensus process that produced the Proposed Standard.[^ednote]  The
guidance applies to both registries.  The expert confirms that:

* The request names the registry that matches the direction of the
  operation: the "NFSv4 Operations" registry for an operation sent
  in a COMPOUND, and the "NFSv4 Callback Operations" registry for
  one sent in a CB_COMPOUND.

* The requested value is the lowest unassigned value in that
  registry that is not reserved, or was previously early-allocated
  to the same document.

* The name is unique within the registry and follows the naming
  conventions in {{iana-registry}} or {{iana-cb-registry}}.

* The arguments and results of the operation are defined in XDR in
  the requesting document or in a document it normatively
  references.

* The Reference column identifies the section of the requesting
  document that specifies the operation, and any additions the
  requesting document makes to the Reference column of an existing
  entry satisfy the selection rule in {{iana-registry}}, which
  {{iana-cb-registry}} shares.

For a document that withdraws an existing operation, the expert
additionally confirms that:

* The document states which minor versions the change applies to.

* The requested Status value is consistent with that scope.  An
  operation withdrawn from every minor version in which it is
  available is marked "Deprecated" or "Obsolete", and the document
  is added to the Reference column.  An operation withdrawn from
  some minor versions but still valid in others keeps an empty
  Status, and only the Reference column changes.

* The document does not reuse the operation's value or renumber the
  operation, and does not request that the entry be removed.

[^ednote]: Editor's note: skeletal; to be expanded if the Working
    Group wants the expert to check more than registry hygiene.

## Updates to RFC 8178 {#updates-8178}

This document updates {{RFC8178}} as follows.

The first bullet of {{Section 4.2 of RFC8178}} describes the
addition of previously unspecified operation codes as an XDR
extension that extends the operation enumeration and the
corresponding request and response switches.  This document adds
the requirement that operation codes added to the nfs_opnum4
enumeration be allocated from the "NFSv4 Operations" registry, and
that operation codes added to the nfs_cb_opnum4 enumeration be
allocated from the "NFSv4 Callback Operations" registry, as
specified in {{mechanism}}, rather than being chosen by the author of
the extension.

{{Section 6 of RFC8178}} continues to govern the publication
requirements for an extension that introduces a new operation or
callback operation.  The registries do not relax those requirements;
they only record the outcome of the process.

Nothing in this document changes the rules in {{RFC8178}} for XDR
extensions other than operations and callback operations, nor the
rules for creating new minor versions.

# Security Considerations {#security}

This document creates two IANA registries and specifies how values
in those registries are allocated.  It introduces no new protocol behavior
and therefore no new security considerations beyond those of the
documents that define the individual operations.

# IANA Considerations {#iana}

## NFSv4 Parameters Registry Group {#iana-group}

IANA is requested to create, if it does not already exist, a
registry group titled "Network File System Version 4 (NFSv4)
Parameters".  {{I-D.cel-nfsv4-fattr4-registry}} requests the same
group.  This document places two registries in the group, described
in {{iana-registry}} and {{iana-cb-registry}}.  Future documents may
add registries for other
NFSv4 numeric protocol elements to this group.  This document does
not request that the existing NFSv4 registries created by
{{Section 22 of RFC8881}} be moved into the group.

## NFSv4 Operations Registry {#iana-registry}

IANA is requested to create a new registry titled "NFSv4 Operations"
within the group described in {{iana-group}}.

The registry has the following columns:

Value:
: The operation's numeric value, which is the value of its XDR
  constant in the nfs_opnum4 enumeration and the discriminant that
  selects the operation's arm of the nfs_argop4 and nfs_resop4
  unions.  Values are non-negative integers.

Name:
: The operation's name as used in the specifying document.  The name
  of the operation's XDR constant is derived from the Name and is
  not recorded separately.  The XDR constant, whose value is the
  operation's Value, is the Name prefixed with "OP_" (for example,
  OP_GETATTR for the operation named GETATTR).  A document that
  registers a new operation MUST follow this convention.  By
  convention, the XDR structures carrying the operation's arguments
  and results are the Name suffixed with "4args" and "4res" (for
  example, GETATTR4args and GETATTR4res); the registry does not
  record these names.

Minor Version:
: The NFSv4 minor version in which the operation was first
  specified.

Reference:
: The sections of the documents that normatively specify the
  operation, selected as follows.  For each minor version
  specification that defines the operation, the operation's
  definition section is cited.  No other section of a minor version
  specification is cited, except a section that withdraws the
  operation from that minor version.  A section of any other
  document is cited only if it formally updates the defining
  specification with respect to the operation, or contains a
  normative statement whose subject is the operation: its arguments,
  its results, the errors it returns, or whether a server implements
  it.  A statement whose subject is an attribute or other protocol
  element, and which mentions the operation only as the means by
  which that element is carried, does not qualify.

Status:
: Empty for an operation in current use in at least one minor
  version.  Otherwise one of "Deprecated", "Obsolete", or "Reserved"
  (see {{Section 6 of RFC8126}}).  "Deprecated" and "Obsolete" are
  set by a later document that changes the operation's status, and
  that document is added to the Reference column.  "Reserved" marks
  a value that is not available for assignment.

The registration policy for this registry is Standards Action with
Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}).  Guidance for
the Designated Expert is in {{expert}}.  Early allocation of values
is permitted per {{RFC7120}}.  Values are assigned sequentially from
the lowest unassigned value that is not reserved.  Assigned values
MUST NOT be reused, and entries MUST NOT be removed (see
{{mechanism}}).

## Initial Registry Contents {#iana-initial}

The initial contents of the registry are the operations specified in
{{RFC7530}}, {{RFC8881}}, {{RFC7862}}, and {{RFC8276}}.  For
operations introduced in NFSv4.0, both the NFSv4.0 and NFSv4.1
specifications are referenced, because each minor version
specification restates the operation.  Additional references were
selected by applying the rule in {{iana-registry}} to {{RFC7931}},
{{RFC7862}}, {{RFC8276}}, and {{RFC9754}}.

Five NFSv4.0 operations (OPEN_CONFIRM, RENEW, SETCLIENTID,
SETCLIENTID_CONFIRM, and RELEASE_LOCKOWNER) MUST NOT be implemented
in NFSv4.1 ({{Section 8.8 of RFC8881}}) or NFSv4.2 ({{Section 13 of
RFC7862}}).  Because these operations remain valid in NFSv4.0, their
Status column is empty and the withdrawing sections appear in the
Reference column, as described in {{expert}}.

Values 0, 1, and 2 are not assigned by any minor version
specification, which begins the nfs_opnum4 enumeration at 3.  This
document marks them Reserved so that they are never assigned.[^reserved]

[^reserved]: Editor's note: the base specifications give no reason
    for starting at 3.  If the Working Group would rather leave 0
    through 2 unassigned, the three Reserved entries can be dropped
    from both registries.

| Value | Name | Minor Version | Reference | Status |
|------:|------|---------------|-----------|--------|
| 0 | | | | Reserved |
| 1 | | | | Reserved |
| 2 | | | | Reserved |
| 3 | ACCESS | 4.0 | {{Section 16.1 of RFC7530}}, {{Section 18.1 of RFC8881}}, {{Section 8.5 of RFC8276}} | |
| 4 | CLOSE | 4.0 | {{Section 16.2 of RFC7530}}, {{Section 18.2 of RFC8881}} | |
| 5 | COMMIT | 4.0 | {{Section 16.3 of RFC7530}}, {{Section 18.3 of RFC8881}} | |
| 6 | CREATE | 4.0 | {{Section 16.4 of RFC7530}}, {{Section 18.4 of RFC8881}} | |
| 7 | DELEGPURGE | 4.0 | {{Section 16.5 of RFC7530}}, {{Section 18.5 of RFC8881}} | |
| 8 | DELEGRETURN | 4.0 | {{Section 16.6 of RFC7530}}, {{Section 18.6 of RFC8881}} | |
| 9 | GETATTR | 4.0 | {{Section 16.7 of RFC7530}}, {{Section 18.7 of RFC8881}} | |
| 10 | GETFH | 4.0 | {{Section 16.8 of RFC7530}}, {{Section 18.8 of RFC8881}} | |
| 11 | LINK | 4.0 | {{Section 16.9 of RFC7530}}, {{Section 18.9 of RFC8881}} | |
| 12 | LOCK | 4.0 | {{Section 16.10 of RFC7530}}, {{Section 18.10 of RFC8881}} | |
| 13 | LOCKT | 4.0 | {{Section 16.11 of RFC7530}}, {{Section 18.11 of RFC8881}} | |
| 14 | LOCKU | 4.0 | {{Section 16.12 of RFC7530}}, {{Section 18.12 of RFC8881}} | |
| 15 | LOOKUP | 4.0 | {{Section 16.13 of RFC7530}}, {{Section 18.13 of RFC8881}} | |
| 16 | LOOKUPP | 4.0 | {{Section 16.14 of RFC7530}}, {{Section 18.14 of RFC8881}} | |
| 17 | NVERIFY | 4.0 | {{Section 16.15 of RFC7530}}, {{Section 18.15 of RFC8881}} | |
| 18 | OPEN | 4.0 | {{Section 16.16 of RFC7530}}, {{Section 18.16 of RFC8881}}, {{Section 4 of RFC9754}} | |
| 19 | OPENATTR | 4.0 | {{Section 16.17 of RFC7530}}, {{Section 18.17 of RFC8881}} | |
| 20 | OPEN_CONFIRM | 4.0 | {{Section 16.18 of RFC7530}}, {{Section 8.8 of RFC8881}}, {{Section 13 of RFC7862}} | |
| 21 | OPEN_DOWNGRADE | 4.0 | {{Section 16.19 of RFC7530}}, {{Section 18.18 of RFC8881}} | |
| 22 | PUTFH | 4.0 | {{Section 16.20 of RFC7530}}, {{Section 18.19 of RFC8881}} | |
| 23 | PUTPUBFH | 4.0 | {{Section 16.21 of RFC7530}}, {{Section 18.20 of RFC8881}} | |
| 24 | PUTROOTFH | 4.0 | {{Section 16.22 of RFC7530}}, {{Section 18.21 of RFC8881}} | |
| 25 | READ | 4.0 | {{Section 16.23 of RFC7530}}, {{Section 18.22 of RFC8881}} | |
| 26 | READDIR | 4.0 | {{Section 16.24 of RFC7530}}, {{Section 18.23 of RFC8881}} | |
| 27 | READLINK | 4.0 | {{Section 16.25 of RFC7530}}, {{Section 18.24 of RFC8881}} | |
| 28 | REMOVE | 4.0 | {{Section 16.26 of RFC7530}}, {{Section 18.25 of RFC8881}} | |
| 29 | RENAME | 4.0 | {{Section 16.27 of RFC7530}}, {{Section 18.26 of RFC8881}} | |
| 30 | RENEW | 4.0 | {{Section 16.28 of RFC7530}}, {{Section 8.8 of RFC8881}}, {{Section 13 of RFC7862}} | |
| 31 | RESTOREFH | 4.0 | {{Section 16.29 of RFC7530}}, {{Section 18.27 of RFC8881}} | |
| 32 | SAVEFH | 4.0 | {{Section 16.30 of RFC7530}}, {{Section 18.28 of RFC8881}} | |
| 33 | SECINFO | 4.0 | {{Section 16.31 of RFC7530}}, {{Section 18.29 of RFC8881}} | |
| 34 | SETATTR | 4.0 | {{Section 16.32 of RFC7530}}, {{Section 18.30 of RFC8881}} | |
| 35 | SETCLIENTID | 4.0 | {{Section 16.33 of RFC7530}}, {{Section 8.4 of RFC7931}}, {{Section 8.8 of RFC8881}}, {{Section 13 of RFC7862}} | |
| 36 | SETCLIENTID_CONFIRM | 4.0 | {{Section 16.34 of RFC7530}}, {{Section 8.8 of RFC8881}}, {{Section 13 of RFC7862}} | |
| 37 | VERIFY | 4.0 | {{Section 16.35 of RFC7530}}, {{Section 18.31 of RFC8881}} | |
| 38 | WRITE | 4.0 | {{Section 16.36 of RFC7530}}, {{Section 18.32 of RFC8881}} | |
| 39 | RELEASE_LOCKOWNER | 4.0 | {{Section 16.37 of RFC7530}}, {{Section 8.8 of RFC8881}}, {{Section 13 of RFC7862}} | |
| 40 | BACKCHANNEL_CTL | 4.1 | {{Section 18.33 of RFC8881}} | |
| 41 | BIND_CONN_TO_SESSION | 4.1 | {{Section 18.34 of RFC8881}} | |
| 42 | EXCHANGE_ID | 4.1 | {{Section 18.35 of RFC8881}}, {{Section 14.1 of RFC7862}} | |
| 43 | CREATE_SESSION | 4.1 | {{Section 18.36 of RFC8881}} | |
| 44 | DESTROY_SESSION | 4.1 | {{Section 18.37 of RFC8881}} | |
| 45 | FREE_STATEID | 4.1 | {{Section 18.38 of RFC8881}} | |
| 46 | GET_DIR_DELEGATION | 4.1 | {{Section 18.39 of RFC8881}} | |
| 47 | GETDEVICEINFO | 4.1 | {{Section 18.40 of RFC8881}} | |
| 48 | GETDEVICELIST | 4.1 | {{Section 18.41 of RFC8881}}, {{Section 14.2 of RFC7862}} | |
| 49 | LAYOUTCOMMIT | 4.1 | {{Section 18.42 of RFC8881}} | |
| 50 | LAYOUTGET | 4.1 | {{Section 18.43 of RFC8881}} | |
| 51 | LAYOUTRETURN | 4.1 | {{Section 18.44 of RFC8881}} | |
| 52 | SECINFO_NO_NAME | 4.1 | {{Section 18.45 of RFC8881}} | |
| 53 | SEQUENCE | 4.1 | {{Section 18.46 of RFC8881}} | |
| 54 | SET_SSV | 4.1 | {{Section 18.47 of RFC8881}} | |
| 55 | TEST_STATEID | 4.1 | {{Section 18.48 of RFC8881}} | |
| 56 | WANT_DELEGATION | 4.1 | {{Section 18.49 of RFC8881}} | |
| 57 | DESTROY_CLIENTID | 4.1 | {{Section 18.50 of RFC8881}} | |
| 58 | RECLAIM_COMPLETE | 4.1 | {{Section 18.51 of RFC8881}} | |
| 59 | ALLOCATE | 4.2 | {{Section 15.1 of RFC7862}} | |
| 60 | COPY | 4.2 | {{Section 15.2 of RFC7862}} | |
| 61 | COPY_NOTIFY | 4.2 | {{Section 15.3 of RFC7862}} | |
| 62 | DEALLOCATE | 4.2 | {{Section 15.4 of RFC7862}} | |
| 63 | IO_ADVISE | 4.2 | {{Section 15.5 of RFC7862}} | |
| 64 | LAYOUTERROR | 4.2 | {{Section 15.6 of RFC7862}} | |
| 65 | LAYOUTSTATS | 4.2 | {{Section 15.7 of RFC7862}} | |
| 66 | OFFLOAD_CANCEL | 4.2 | {{Section 15.8 of RFC7862}} | |
| 67 | OFFLOAD_STATUS | 4.2 | {{Section 15.9 of RFC7862}} | |
| 68 | READ_PLUS | 4.2 | {{Section 15.10 of RFC7862}} | |
| 69 | SEEK | 4.2 | {{Section 15.11 of RFC7862}} | |
| 70 | WRITE_SAME | 4.2 | {{Section 15.12 of RFC7862}} | |
| 71 | CLONE | 4.2 | {{Section 15.13 of RFC7862}} | |
| 72 | GETXATTR | 4.2 | {{Section 8.4.1 of RFC8276}} | |
| 73 | SETXATTR | 4.2 | {{Section 8.4.2 of RFC8276}} | |
| 74 | LISTXATTRS | 4.2 | {{Section 8.4.3 of RFC8276}} | |
| 75 | REMOVEXATTR | 4.2 | {{Section 8.4.4 of RFC8276}} | |
| 10044 | ILLEGAL | 4.0 | {{Section 16.38 of RFC7530}}, {{Section 18.52 of RFC8881}} | |
{: #ops-initial title="Initial contents of the NFSv4 Operations registry"}

Values 76 through 10043, and 10045 and above, are unassigned.

## NFSv4 Callback Operations Registry {#iana-cb-registry}

IANA is requested to create a new registry titled "NFSv4 Callback
Operations" within the group described in {{iana-group}}.

The registry has the same columns as the registry described in
{{iana-registry}}, with these differences:

Value:
: The callback operation's numeric value, which is the value of its
  XDR constant in the nfs_cb_opnum4 enumeration and the discriminant
  that selects the operation's arm of the nfs_cb_argop4 and
  nfs_cb_resop4 unions.  Values are non-negative integers.

Name:
: The callback operation's name as used in the specifying document.
  Every name begins with "CB_".  The XDR constant, whose value is the
  operation's Value, is the Name prefixed with "OP_" (for example,
  OP_CB_GETATTR for the operation named CB_GETATTR).  A document
  that registers a new callback operation MUST follow these
  conventions.  By convention, the XDR structures carrying the
  operation's arguments and results are the Name suffixed with
  "4args" and "4res" (for example, CB_GETATTR4args and
  CB_GETATTR4res); the registry does not record these names.

The Minor Version, Reference, and Status columns are as described in
{{iana-registry}}, including the Reference selection rule.

The registration policy, early allocation, sequential assignment,
and prohibition on reuse and removal are the same as for the
registry described in {{iana-registry}}.

## Initial Callback Registry Contents {#iana-cb-initial}

The initial contents of the registry are the callback operations
specified in {{RFC7530}}, {{RFC8881}}, and {{RFC7862}}.  For callback
operations introduced in NFSv4.0, both the NFSv4.0 and NFSv4.1
specifications are referenced, because each minor version
specification restates the operation.  Applying the rule in
{{iana-registry}} to {{RFC7931}}, {{RFC8276}}, and {{RFC9754}} yields
no additional references.  No callback operation has been withdrawn
from any minor version, so the Status column is empty for every
entry except the three Reserved values, which are reserved for the
reason given in {{iana-initial}}.

| Value | Name | Minor Version | Reference | Status |
|------:|------|---------------|-----------|--------|
| 0 | | | | Reserved |
| 1 | | | | Reserved |
| 2 | | | | Reserved |
| 3 | CB_GETATTR | 4.0 | {{Section 18.1 of RFC7530}}, {{Section 20.1 of RFC8881}} | |
| 4 | CB_RECALL | 4.0 | {{Section 18.2 of RFC7530}}, {{Section 20.2 of RFC8881}} | |
| 5 | CB_LAYOUTRECALL | 4.1 | {{Section 20.3 of RFC8881}} | |
| 6 | CB_NOTIFY | 4.1 | {{Section 20.4 of RFC8881}} | |
| 7 | CB_PUSH_DELEG | 4.1 | {{Section 20.5 of RFC8881}} | |
| 8 | CB_RECALL_ANY | 4.1 | {{Section 20.6 of RFC8881}} | |
| 9 | CB_RECALLABLE_OBJ_AVAIL | 4.1 | {{Section 20.7 of RFC8881}} | |
| 10 | CB_RECALL_SLOT | 4.1 | {{Section 20.8 of RFC8881}} | |
| 11 | CB_SEQUENCE | 4.1 | {{Section 20.9 of RFC8881}} | |
| 12 | CB_WANTS_CANCELLED | 4.1 | {{Section 20.10 of RFC8881}} | |
| 13 | CB_NOTIFY_LOCK | 4.1 | {{Section 20.11 of RFC8881}} | |
| 14 | CB_NOTIFY_DEVICEID | 4.1 | {{Section 20.12 of RFC8881}} | |
| 15 | CB_OFFLOAD | 4.2 | {{Section 16.1 of RFC7862}} | |
| 10044 | CB_ILLEGAL | 4.0 | {{Section 18.3 of RFC7530}}, {{Section 20.13 of RFC8881}} | |
{: #cb-ops-initial title="Initial contents of the NFSv4 Callback Operations registry"}

Values 16 through 10043, and 10045 and above, are unassigned.


--- back

# Open Issues {#open-issues}

This section is to be removed before publishing as an RFC.

Each item below is tracked as an issue in this document's issue
tracker, where the detail and the discussion live.

There are currently no open issues.

# Acknowledgments
{:numbered="false"}

Thanks to Éric Vyncke for suggesting this approach.

The editor is grateful to
Bill Baker,
Greg Marsden,
and
Martin Thomson
for their input and support.

Special thanks to
Area Director
Gorry Fairhurst,
NFSv4 Working Group Chair
Brian Pawlowski,
and
NFSv4 Working Group Secretary
Thomas Haynes
for their guidance and oversight.
