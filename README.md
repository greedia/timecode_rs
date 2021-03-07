# Timecode_rs

Timecode_rs is a tool that allows you to test and debug offsets. It can create a file that encodes offsets
within the data itself, which allows you to determine the offset of a piece of data with 11 bytes.

It is named after timecode vinyl records, which digitally encode offsets to map a position to an audio file.

The format of the file is pretty simple: a sequence of 32-bit integers in network byte order:

[00 00 00 00] [00 00 00 01] [00 00 00 02] etc.

In order to discover our offset, we need to read 11 bytes, which allows us to read 2 u32s with a 3-byte
sliding window.

```text
00 01 02 03 04 05 06 07 08 09 0A
[         ] [         ] on the first round
   [         ] [         ] on the second round
      [         ] [         ] on the third round
         [         ] [         ] on the fourth round
```

To discover the offset, we need to find the first whole u32. We make three attempts with the sliding window
to find 8 bytes that make up two u32s in a row, where second == first + 1.

Once we discover this first u32, we multiply it by 4 and add the sliding window (0-3), which gives us our offset.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall
be dual licensed as above, without any additional terms or conditions.
