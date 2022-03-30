# Fragment file format

**Version**: 0.1  
**Date last updated**: 2022-03-24

The fragment file (.frag) is a tab-delimited text file consisting of a header section and a fragment record section. The fragment format was originally introduced by 10x Genomics through their `cellranger-atac` software, and is commonly used in the storage and analysis of single-cell chromatin data. **Note that the file format described here differs from the format currently used by 10x Genomics**.

Header lines start with the `#` character, while fragment records do not. Each fragment record has 6 mandatory fields. The fragment file is similar to the BED format, but contains a defined header format and may contain additional fields.

## Fragment file header section

The specification of the fragment file header was heavily influenced by the [SAM format specification](https://samtools.github.io/hts-specs/SAMv1.pdf).

The fragment file header is an optional section. If present, each header line must start with a `#` character, and fragment records must not start with a `#` character. In the header, each line is TAB-delimited and, apart from `#CO` lines, each data field follows a format `TAG:VALUE` where `TAG` is a two-character string that defines the format and content of `VALUE`. Within each (non-`#CO`) header line, no field tag may appear more than once and the order in which the fields appear is not significant

The following tables describe the header record types that may be used. Tags listed with `*` are required. New header tags can be freely added for further data fields.

### HD tag

This describes file-level metadata. If present, there must be only one `#HD` line and it must be the first line of the file.

Attributes:

| Tag | Description |
| --- | ----------- |
| VN\* | File format version |
| SO | Sorting order of fragment entries. Valid values: `unknown` (default), `unsorted`, `coordinate`, `barcode` |

### SQ tag

This describes the reference sequence dictionary. There may be multiple `#SQ` tags.

Attributes:

| Tag | Description |
| --- | ----------- |
| SN\*  | Reference sequence name. All Reference sequence names must be distinct. The value in this field is used in the fragment records in the RNAME field |
| LN\*  | Reference sequence length |
| AN  | Alternative reference sequence names. A comma-separated list of alternative names that tools may use when referring to this reference sequence |
| AS  | Genome assembly identifier |
| DS  | Description |
| M5  | MD5 checksum of the sequence |
| SP  | Species |
| TP  | Molecule topology. Valid values: `linear`, `circular` |
| UR  | URI of the sequence. This value may start with one of the standard protocols, eg 'https' or 'ftp'. If it does not start with one of these protocols, it is assumed to be a local path |

### PG tag

This describes information about programs run on the fragment file. There may be multiple `#PG` tags.

Attributes:

| Tag | Description |
| --- | ----------- |
| ID\*  | Program record identifier. Each #PG line must have a unique ID |
| PN  | Program name |
| CL  | Command line |
| PP  | Previous `#PG-ID`. Must match another `#PG` header’s ID tag |
| DS  | Description |
| VN  | Program version |


### CO tag

One-line text comment. Multiple `#CO` lines are allowed.

## Fragment file records section

### Mandatory fields

| Column | Field name | Type | Description |
| ------ | ---------- | ---- | ----------- |
|   1    |   RNAME    | String | Reference sequence name |
|   2    |   START    | Int  | 0-based leftmost mapping position of the fragment. Must be a non-negative integer |
|   3    |   END      | Int  | 0-based rightmost mapping position of the fragment. Must be an integer greater than START, and less than the total number of bases in RNAME |
|   4    |   BAR      | String | Cell barcode identifier |
|   5    |   READS    | Int  | Total number of sequenced read pairs associated with this fragment |
|   6    |   STRAND   | String | Aligned strand. Allowed values: `-`, `+`, or `.` (unstranded) |

### Optional fields

An additional 7th column may be included containing optional space-delimited fields. All optional fields must follow the `TAG:TYPE:VALUE` format where `TAG` is a two-character string that matches `/[A-Za-z][A-Za-z0-9]/`, as for optional SAM tags. Within each fragment record entry, no `TAG` may appear more than once, and the order of `TAG`s is not significant. A `TAG` containing lowercase letters is reserved for end users.

Type key:

| Type | Description |
| ---- | ----------- |
|  A   | Printable character |
|  i   | Integer     |
|  f   | Float       |
|  Z   | String. Whitespace not allowed |
|  H   | Byte array in Hex format  |
|  B   | Integer or numeric array |

# Recommended practice for the fragment format

This section describes the recommended best practice for representing data in the fragment format.

1. The `#HD` header line should be present, with the `SO` tag specified  
2. The `#SQ` header lines should be present  
3. One or more `#PG` header lines should be present  

The file should be coordinate-sorted and block-gzipped to allow rapid position-based querying of the file, using an index generated with [tabix](http://www.htslib.org/doc/tabix.html).
