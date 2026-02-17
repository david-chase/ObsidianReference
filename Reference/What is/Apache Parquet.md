#databases #apache #project

https://parquet.apache.org/

Apache Parquet is an open source, column-oriented data storage format designed for efficient data representation and retrieval within the Hadoop ecosystem and modern cloud data lakes. Unlike traditional row-based formats like CSV or JSON, Parquet stores values of the same column contiguously on disk, allowing for highly efficient compression and encoding schemes tailored to specific data types. This architecture is optimized for read-heavy analytical workloads, as it enables query engines to skip irrelevant data and minimize I/O by reading only the columns required for a specific query.

## Key Features

- Columnar Storage: Data is organized by column rather than row, which significantly reduces the amount of data read from disk for analytical queries that only require a subset of columns.
- Efficient Compression: Since data in a single column is often similar, Parquet can achieve high compression ratios using codecs such as Snappy, Gzip, LZO, Brotli, and Zstandard.
- Advanced Encoding: Supports various encoding techniques including dictionary encoding, bit packing, run-length encoding (RLE), and delta encoding to further reduce storage footprint.
- Self-Describing Metadata: Every Parquet file contains its own schema and metadata in a footer, including per-column statistics like minimum and maximum values, which allows for data skipping during query execution.
- Nested Data Support: Implements the record shredding and assembly algorithm to handle complex, nested data structures efficiently without flattening them into simple tables.
- Language Agnostic: While originally part of the Hadoop ecosystem, it is supported by a wide range of languages and frameworks including Java, C++, Python, Go, Rust, and JavaScript.
- Parallel Processing: Files are partitioned into row groups, which allow different parts of a single file to be processed in parallel across multiple nodes in a distributed system.

## Technical Components

- Row Groups: Horizontal partitions of data that contain a chunk for every column in the dataset, serving as the unit of parallelization.
- Column Chunks: Chunks of data for a particular column within a row group, stored contiguously to improve read performance.
- Pages: The smallest unit of a Parquet file, where compression and encoding are actually applied; a column chunk is made up of one or more pages.
- Footer: The metadata section located at the end of the file that contains the file's schema, version information, and the starting locations of all column chunks.
- Schema Evolution: Allows for limited changes to the data structure over time, such as adding new columns, without requiring a complete rewrite of existing data.