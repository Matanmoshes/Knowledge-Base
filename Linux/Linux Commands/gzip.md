# `gzip` Command
[gzip]

`gzip` (GNU zip) is a file compression utility in Linux and other Unix-like operating systems. It's commonly used to reduce the size of files, making them easier to store or transmit. `gzip` typically operates on single files and appends a `.gz` extension to the compressed files. It uses the DEFLATE algorithm for compression.

### Basic Usage
Here's how you can use `gzip` in its most basic form:

```bash
gzip filename
```

This command will compress the file named `filename` and replace it with `filename.gz` in the same directory.

### Common Options
- `-d`: Decompress a file
- `-k`: Keep the input files (does not delete the original file after compression or decompression)
- `-l`: List information about a compressed file
- `-r`: Recursively compress files in a directory
- `-1` to `-9`: Set the compression level (`-1` for fastest compression and `-9` for best compression)

### Examples

**Compressing a file while keeping the original:**
```bash
gzip -k filename
```

**Decompressing a file:**
```bash
gzip -d filename.gz
```

**Compressing all files in a directory recursively:**
```bash
gzip -r directory_name
```

**Checking compressed file details:**
```bash
gzip -l filename.gz
```

**Return to normal file 
```bash
gunzip filename.gz
```

`gzip` is widely used in scripts and pipelines in Linux systems for handling logs, backups, and any other scenarios where saving space is beneficial. If you need to compress multiple files into a single archive, you might typically pair `gzip` with `tar`, as `gzip` itself does not handle multiple files.

![[Pasted image 20240417201937.png]]
