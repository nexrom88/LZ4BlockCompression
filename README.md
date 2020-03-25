# LZ4BlockCompression
LZ4 compression of streams by dividing data into blocks to be seekable. Let's say e.g. that you want to get 20 bytes by starting from decompressed-byte offset 300 within a LZ4 compressed file you normally have to decompress the whole file first.
This library divides the data while compressing into blocks. Each block gets an unique header which tells the decompressor "where we are" wihin uncompressed data.

The compressed file looks like this:

![alt text](https://nexrom.de/nxm/lz4BlockCompression.png)

- "File Header" consists of two uint64 values (decompressed file size & decompressed block size)
- "Block Header" is the beginning of each new block and also consist of two uint64 values (decompressed file offset & compressed block size)
- "LZ4 block data" contains compressed data. Its size is "compressed block size". It's uncompressed size is "decompressed block size"

# Compress data

LZ4BlockCompression library can be used as a regular stream to write the compressed file:

```c#
System.IO.FileStream target = new System.IO.FileStream("c:\\target\\comp.lz4", System.IO.FileMode.Create, System.IO.FileAccess.ReadWrite);
BlockCompression.LZ4BlockStream bs = new BlockCompression.LZ4BlockStream(target, BlockCompression.AccessMode.write);

System.IO.FileStream source = new System.IO.FileStream("C:\\Users\\admin\\Downloads\\test.bin", System.IO.FileMode.Open, System.IO.FileAccess.Read);

int readBytes = -1;
while (readBytes != 0)
{
    byte[] buffer = new byte[512];
    readBytes = source.Read(buffer, 0, buffer.Length);
    bs.Write(buffer, 0, readBytes);
}

source.Close();
bs.Close();
target.Close();
```

# Decompress data

Like compressing data you can also decompress data like using an usual stream:

```c#
System.IO.FileStream source = new System.IO.FileStream("c:\\target\\numbers.lz4", System.IO.FileMode.Open, System.IO.FileAccess.Read);
BlockCompression.LZ4BlockStream bs = new BlockCompression.LZ4BlockStream(source, BlockCompression.AccessMode.read);

System.IO.FileStream target = new System.IO.FileStream("c:\\target\\numbers_dec_part.bin", System.IO.FileMode.Create, System.IO.FileAccess.Write);

int readBytes = -1;
bs.Seek(4 * 10000, System.IO.SeekOrigin.Begin);
while (readBytes != 0)
{
    byte[] buffer = new byte[1000000];
    readBytes = bs.Read(buffer, 0, buffer.Length);
    target.Write(buffer, 0, readBytes);
}

bs.Close();
target.Close();
```
