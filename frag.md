# Fragment (frag) file format

The fragment file (.frag) is a tab-delimited text file consisting of a header section and a fragment record section. Header lines start with the `#` character, while fragment records do not. Each fragment record has 6 mandatory fields. The fragment file is similar to the BED format, but contains a defined header format and may contain additional fields.

## Fragment file header section

The specification of the fragment file header was heavily influenced by the [SAM format specification](https://samtools.github.io/hts-specs/SAMv1.pdf).

The fragment file header is an optional section. If present, each header line must start with a `#` character, and fragment records must not start with a `#` character. 

The following tables describe the header record types that may be used. Tags listed with `*` are required. New header tags can be freely added for further data fields.

