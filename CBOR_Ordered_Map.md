# Ordered Maps for CBOR

This document specifies a tag for ordered maps in Concise Binary Object Representation (CBOR) [1].

    Tag: 272 (ordered map)
    Data item: array
    Semantics: Order-sensitive key-value mapping
    Reference: https://github.com/Sekenre/cbor-ordered-map-spec/blob/master/CBOR_Ordered_Map.md
    Contact: Kio Smallwood <it-flex@flexitricity.com>

## Introduction

In the Python programming language, there is a dictionary type (OrderedDict [3]) which allows efficient access to key-value
pairs in the order in which they are added. If these are encoded as major type 5 the order dependent information
is lost, especially when serializing to a canonical format. Therefore an order-preserving tag should be used.

## Semantics

Tag 272 can be applied to a CBOR array data item to indicate that it is an ordered map. Ordered Maps should be handled
similarly to CBOR maps: an ordered map that has duplicate keys may be well-formed, but it is not valid. Like
CBOR map keys, data items in an ordered map do not need to be of the same type.

The number of items in the CBOR array data must be even. A missing value should raise a decoder error since CBOR supports
both explicit null and undefined data items.

The advice from the CBOR specification on map keys [4] also applies to ordered map keys.

The encoding and decoding applications need to agree on what types of items are going to be used in ordered maps.

Duplicate keys are also prohibited by CBOR decoders that are using strict mode [2].

## Rationale

When encoding a Python OrderedDict type with CBOR it will usually be encoded in the same order as the dictionary was
constructed. Unfortunately the decoder has no way of knowing this and will decode a map as a standard dictionary
thus discarding any order information. Encoding as a standard map with Canonical encoding will also discard the original
order of keys.

## Canonical CBOR

Canonical encoding is handled in the same way as a CBOR array. Using this tag will prevent re-ordering the keys when
encoding canonically.

## Examples

Given the following CBOR map data item, in CBOR diagnostic notation:

    {"a": 1, "b": 2}

The equivalent value as an ordered map in CBOR diagnostic notation is

    272(["a", 1, "b", 2])

And its encoding is 0xd9011084616101616202:

    D9 0110     # tag(272)
      84       # array(4)
        61    # text(1)
           61 # "a"
        01    # unsigned(1)
        61    # text(1)
           62 # "b"
        02    # unsigned(2)

## References

[1] C. Bormann, and P. Hoffman. "Concise Binary Object Representation (CBOR)". RFC 7049, October 2013.

[2] https://tools.ietf.org/html/rfc7049#section-3.10

[3] https://docs.python.org/3/library/collections.html#collections.OrderedDict

[4] https://tools.ietf.org/html/rfc7049#section-3.7

## Authors

Kio Smallwood <kio.smallwood@flexitricity.com>
