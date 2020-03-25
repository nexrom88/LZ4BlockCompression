# LZ4BlockCompression
LZ4 compression of streams by dividing data into blocks to be seekable. Let's say e.g. that you want to get 20 bytes by starting from decompressed-byte offset 300 within a LZ4 compressed file you normally have to decompress the whole file first.
This library divides the data while compressing into blocks. Each block gets an unique header which tells the decompressor "where we are" wihin uncompressed data.
