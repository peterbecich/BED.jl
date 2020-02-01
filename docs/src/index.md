# BED.jl

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
[![Latest Release](https://img.shields.io/github/release/BioJulia/BED.jl.svg)](https://github.com/BioJulia/BED.jl/releases/latest)
[![MIT license](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/BioJulia/BED.jl/blob/master/LICENSE)
[![Stable documentation](https://img.shields.io/badge/docs-stable-blue.svg)](https://biojulia.github.io/BED.jl/stable)
[![Latest documentation](https://img.shields.io/badge/docs-latest-blue.svg)](https://biojulia.github.io/BED.jl/dev/)
[![Join the chat at https://gitter.im/BioJulia/BED.jl](https://badges.gitter.im/BioJulia/BED.jl.svg)](https://gitter.im/BioJulia/BED.jl?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## Description
Data representation and IO tools for the BED file format.

BED is a text-based file format for representing genomic annotations like genes, transcripts, and so on.
A BED file has tab-delimited and variable-length fields; the first three fields denoting a genomic interval are mandatory.

This is an example of RNA transcripts:
```
chr9	68331023	68424451	NM_015110	0	+
chr9	68456943	68486659	NM_001206	0	-
```

I/O tools for BED are provided by the `BED` module, which exports following three types:
* Reader type: `BED.Reader`
* Writer type: `BED.Writer`
* Element type: `BED.Record`

## Installation
BED is made available to install through BioJulia's package registry.
By default, Julia's package manager only uses the "General" package registry.
Your Julia configuration needs to include the BioJulia registry to be able to install the latest version of BED.

To add the BioJulia registry from the [Julia REPL](https://docs.julialang.org/en/v1/manual/getting-started/), press `]` to enter [pkg mode](https://docs.julialang.org/en/v1/stdlib/Pkg/), then enter the following command:
```julia
registry add https://github.com/BioJulia/BioJuliaRegistry.git
```

After adding the registry to your configuration, you can install BED while in [pkg mode](https://docs.julialang.org/en/v1/stdlib/Pkg/) with the following:
```julia
add BED
```

If you are interested in the cutting edge of the development, please check out the [develop branch](https://github.com/BioJulia/BED.jl/tree/develop) to try new features before release.

## Examples
Here is a common workflow to iterate over all records in a BED file:
```julia
# Import the BED module.
using BED

# Open a BED file.
reader = open(BED.Reader, "data.bed")

# Iterate over records.
for record in reader
    # Do something on record (see Accessors section).
    chrom = BED.chrom(record)
    # ...
end

# Finally, close the reader.
close(reader)
```

If you repeatedly access records within specific ranges, it would be more efficient to construct an `IntervalCollection` object from a BED reader:
```julia
using BED
using GenomicFeatures

# Create an interval collection in memory.
icol = open(BED.Reader, "data.bed") do reader
    IntervalCollection(reader)
end

# Query overlapping records.
for interval in eachoverlap(icol, Interval("chrX", 40001, 51500))
    # A record is stored in the metadata field of an interval.
    record = metadata(interval)
    # ...
end
```
