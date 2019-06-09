# Unicode standard. Unicode transformation formats.

<div style="text-align: right;">
<i>"...I​t does not make sense to have a string without knowing what encoding it uses.<br> 
You can no longer stick your head in the sand and pretend that “plain” text is ASCII."<br>
Joel Spolsky - developer, StackOverflow CEO.</i>
</div>

## Sections
- [Unicode standard. Unicode transformation formats.](#unicode-standard-unicode-transformation-formats)
  - [Sections](#sections)
  - [Introduction](#introduction)
  - [A brief history of Unicode](#a-brief-history-of-unicode)
  - [Unicode standard](#unicode-standard)
  - [Unicode code space](#unicode-code-space)
  - [Unicode transformation formats](#unicode-transformation-formats)
  - [Details for developers](#details-for-developers)
  - [UTF-8](#utf-8)
  - [UTF-16](#utf-16)
  - [Comparison of UTF-8 and UTF-16](#comparison-of-utf-8-and-utf-16)
  - [Conclusion](#conclusion)

## Introduction
<p>
    One cannot but agree with the quote of Joel Spolsky given in the epigraph. 
    In the context of software development, we cannot use the text (string) as a kind of abstraction. 
    If you have a string in memory, in a file or anywhere else, 
    then you must understand how this string is encoded and stored, 
    otherwise you will not be able to interpret and display it to some user correctly.
</p>
<p>
    The purpose of this article is to review the technical features of the Unicode 
    dard and the Unicode transforming formats. In addition, a comparison of 
    transforming formats will help decide on the use of these formats in software 
    development.
</p>
<p>
    To understand why the Unicode standard is needed, why we need to talk about it 
    and why it was created, it is necessary to dive in a short excursion into history.
</p>

## A brief history of Unicode
In the 60s of the 20th century, the American National Standards Institute (ANSI) 
developed a character encoding table, which was subsequently used in all operating 
systems. This table is called ASCII (American Standard Code for Information 
Interchange). Up until the end of the 1980s, ASCII was the main way to encode 
characters (though EBCDIC is worth mentioning, but this goes beyond a brief 
excursion). The ASCII code provided the ability to encode letters of the Latin 
alphabet (upper and lower case), punctuation, space, and various service 
characters (non-typed characters). But the fact is that in accordance with ASCII, 
7 bits are enough for encoding of all characters of the ASCII table.

![ASCII table](/static/images/ascii_table.png "ASCII table")

Moreover, most computers of that time used 8-bit memory registers. 
Thus, you could freely store one coded character and at the same time 1 bit was 
not used at all. It is absolutely clear that the free bit can be used for your 
own purposes and extend ASCII capabilities to 256 characters! For example, 
the extended version of ASCI in IBM-PC (called the OEM character set) contained 
some diacritic marks for European languages and a character set for pseudographics.

Programmers from all over the world began to think about using the extra bit as 
soon as PCs were sold outside the US. Many OEM character sets have been developed 
that allow you to represent characters of national alphabets, because the ASCII 
table is convenient only for those who speak English (uses Latin script).

All wide variety of encodings was reduced to the ANSI standard. 
The ANSI standard described which characters were located below the 128 
position - almost everything remained the same as in ASCII in this area. 
And the characters with 128 to 255 positions (which contain a national alphabets
characters) were enclosed in _code pages_.​ For example, code page CP1251 is for Cyrillic.


ANSI did not solve all the problems, of course. What to do if there are too many 
characters in the alphabet and they do not fit in the single-byte encoding 
reserved for this position (there are thousands of characters in Asian languages!)? 
What if you need to use multiple languages within the same text? All of this was 
complicated by the fact that the era of the Internet was approaching and any text 
movement between computers of different locations became commonplace. 
Fortunately the Unicode standard was created quite timely. It saved humanity 
from the chaos of encodings.

The meaning of three main principles was enclosed in the name of Unicode:

- **universal** (addressing the needs of world languages)
- **uniform** (fixed-width codes for efficient access, but this principle is partially departed)​
- **unique** (bit sequence has only one interpretation into character codes)
  
The Unicode project began in late 1987 after discussions between Apple and Xerox 
engineers: Joe Becker, Lee Collins and Mark Davis. Basic researches were carried 
out by the beginning of 1988. And then the Unicode architecture was developed 
based on these researches and the experience of these engineers in various coding 
methods. Collins began creating a database of Unicode characters in the fall 
of 1988. In the following years such companies as IBM, Sun Microsystems, 
Microsoft and others joined the already established non-profit organization 
"Unicode Consortium". Unicode developers had a truly ambitious goal. 
Unicode was supposed to become a standard that would include all types of 
writing on the planet including even "dead" languages, some fictional and 
artificially created.

Unicode 1.0 was introduced as early as 1991. It contained 7161 characters. 
Now the world has version 12.1 and this version already contain **137,994** 
characters. This is enough history. Let's understand what Unicode is!

## Unicode standard
Before the Unicode era each character directly corresponded to its bit 
representation in memory. For example, in ASCII the Latin capital "A" 
corresponds to byte 0x41:

![Latin capital "A" encoding in ASCII](/static/images/ascii_0x41.png "Latin capital "A" encoding in ASCII")

There is a different approach is used in Unicode. Here we introduce an additional
 concept - _code point_. It's like an intermediate layer. It has a format like 
 **U+042F** where "U+" means that it is Unicode and then comes a hexadecimal number 
 of the character. Each character is mapped to the corresponding Unicode code 
 point and already the code point has its own bit mapping in memory. 
 Thus, there is a kind of double encoding. The bit representation of the 
 code point depends on the chosen _Unicode transformation format (UTF)_. 
 For example, it can be UTF-8, UTF-16 or UTF-32.

![Code point](/static/images/code_point.png "Code point")

The code point resembles something like an interface, doesn't it?! 
Now it doesn’t matter how the symbol will be displayed on the monitor or 
stored in memory. We have the code position U+042F and we can say for 
sure that this is a Cyrillic capital "Я". And any person on Earth will 
be able to determine the same if he knows about Unicode and how to use it.

## Unicode code space

The Unicode code space comprises 1,112,064 code positions in the range U+0000 
to U+10FFFF. UTF-8 and UTF-32 allow encoding of up to 231 (2,147,483,648) 
code points, but it was decided to use only 1,112,064 for compatibility 
with UTF-16. More information about UTF encodings is in the next section. 
The code space is divided into 17 planes of 216 (65,536) characters. 
The zero plane is called the _**Basic Multilingual Plane (BMP)**_ and contains 
the characters of the most common languages (range U+0000...U+FFFF). 
Other planes are called _**supplementary**_ and contain historical writing systems 
(plane 1, range U+10000...U+1FFFF), rarely used hieroglyphs in Chinese writing 
(plane 2, range U+20000...U+2FFFF), etc. 

## Unicode transformation formats

The Unicode standard offers several formats for code points transformation 
(encoding). The most commonly used are **UTF-8**, **UTF-16** and **UTF-32**, 
so let's talk about them in more detail, but you should keep in mind 
that there are also UTF-7, UTF-EBCDIC, etc.

You might think that the number in the encoding name means the number of 
bits per character, but this is true only for UTF-32 where each character 
is always encoded with 32 bits. **UTF-32 is fixed-width encoding**. 
**UTF-8 and UTF-16 are variable-width encodings.** The number in the title 
indicates the width of the code unit in bits. That is, for UTF-8, 
the width of the code unit is 8 bits (1 byte), and for UTF-16 - 16 bits (2 bytes). 
In this case, the Unicode code point can be encoded with 1, 2, 3, or 4 bytes 
(code units) in UTF-8 and 2 or 4 bytes (1 or 2 code units) in UTF-16, 
depending on the range in which the code point falls. For example, 
only 1 byte is required to UTF-8 to encode any of the first 128 characters 
(U+0000...U+007F) of Unicode, while 2 or 4 bytes (the minimum size of a code unit) 
are required for UTF-16 and UTF-32, respectively. **The important point is that 
the first 128 Unicode characters one-to-one correspond to ASCII-code characters, 
which makes UTF-8 backward compatible with ASCII code and this is its undoubted 
advantage.**  Further, for encoding the following 1920 Unicode characters 
(U+0080...U+07FF), 2 bytes are required in UTF-8 and UTF-16, and 4 bytes are 
still in UTF-32. For the remaining characters of the base multilingual plane 
(U+0800...U+FFFF), which includes the characters of most of the "living" 
languages of the world, 3 bytes will be used in UTF-8, still 2 bytes in UTF-16, 
and 4 bytes in UTF-32, of course.

At this stage it is worth making a conclusion regarding UTF-32. 
UTF-32 uses memory space too inefficiently and this is the main reason 
why it is not used in most cases. After all, 2 bytes of UTF-16 are 
sufficient to display the characters of the BMP. The advantage of this 
encoding format is the ability to directly index the characters, 
but this is necessary for specific tasks. For example, for the internal 
presentation of texts in a plain text editor, the use of this encoding 
would be unjustified. In this regard, UTF-32 will not be considered in detail.

## Details for developers
In the context of developing software for simple text processing and storage, 
the most common UTF-8 and UTF-16 formats deserve attention.

## UTF-8
The range the code point falls and the octets number can be quickly 
determined from the very useful table below.

![UTF-8 table](/static/images/utf_8_octets_table.png "UTF-8 table")

Let's take a closer look at the UTF-8 character encoding mechanism:

1. Depending on the character number (the range it falls), 
   it’s determined how many octets (code units) will be used to encode a character.
2. If 1 octet is required, the most significant bit of the octet will be 0, 
   and the remaining 7 bits will contain the character code 
   (0xxxxxxx, where x's are information bits) 
3. If more than one octet is required for encoding, then the n high-order bits 
   will be 1 in the first octet, where n is the number of octets. 
   There will be a zero bit after them. In subsequent octets, 
   the high-order bits will be 10.
4. After determining the service bits, the information bits are encoded.
   Fill in the bits marked x from the bits of the character number, 
   expressed in binary. Start by putting the lowest-order bit of the character 
   number in the lowest-order position of the last octet of the sequence, 
   then put the next higher-order bit of the character number in the next 
   higher-order position of that octet, etc. When the x bits of the last 
   octet are filled in, move on to the next to last octet, then to the preceding 
   one, etc. until all x bits are filled in. Point 4 is clearly shown in the image below.

![UTF-8 encoding](/static/images/utf_8_encoding.png "UTF-8 encoding")

This procedure is algorithmically justified and optimized. 
The same is about UTF-16. In this connection, conversions between 
UTF-8 and UTF-16 (UTF-32) formats happen quickly and safely, 
without loss of information, which is very important when storing and 
processing information in various hard- or software environments and 
operating systems. Conversions from one format to another are a wide 
practice within complex software products. 

The UTF-8 encoding usage mark is a sequence of 0xEF 0xBB 0xBF bytes at 
the beginning of the text. UTF-8 is described in [RFC 3629](<https://tools.ietf.org/html/rfc3629>).

## UTF-16

Recall that UTF-16 uses 2 bytes or 4 bytes to represent Unicode code points. 
And 2 bytes will suffice for most characters. UTF-16 is described in [RFC 2781](<https://tools.ietf.org/html/rfc2781>).

In the diagram above, where the encoding of the Cyrillic capital letter “Я” 
is presented, you should notice that the numeric value in the code position 
coincides with its byte mapping (U+042F = 0x042F). This is true for all code 
points with values up to 0x10000 (character number), which require 2 bytes 
to encode, but excluding the range 0xD800...0xDFFF.

The range 0xD800...0xDFFF is excluded in order to create surrogate pairs for 
encoding characters that require more than 2 bytes. Characters in the range 
0x10000...0x10FFFF (more than 2 bytes) are encoded as follows:

1. Let _U_ be the character code. 0x10000 is subtracted from the _U_. 
   The result is the value of _U'_ < 0xFFFFF, which requires 20 bits to be represented.
2. Two 2-byte words _W1_ and _W2_ are initialized (code units). 
   The high-order bits of _W1_ are assigned the value 0xD800, 
   and the high-order bits of _W2_ are assigned as 0xDC00. 
   Thus, _W1_ = 110110xxxxxxxxxx, and _W2_ = 110111yyyyyyyyyyy in binary form, 
   where _x, y_ are not yet assigned bits. 
3. The high-order 10 bits of the _U'_ (a number in the range 0x0000...0x03FF) 
   are added to 0xD800, and the result goes to the _W1_ empty bits, which is in 
   the range 0xD800...0xDBFF. 
4. The lower-order 10 bits (also a number in the range 0x0000...0x03FF) 
   are added to 0xDC00, and the result goes to the W2 empty bits, 
   which is in the range 0xDC00...0xDFFF. 
   Schematically, it looks like this: 

![UTF-16 encoding](/static/images/utf_16_encoding.png "UTF-16 encoding")

UTF-16 has three sub-formats: BE, LE and unmarked. 
The BE form uses big-endian byte serialization (most significant byte first), 
the LE form uses little-endian byte serialization (least significant byte first) 
and the unmarked form uses big-endian byte serialization by default, 
but may include a byte order mark (BOM) at the beginning to indicate 
the actual byte serialization used. BOM can have values U+FEFF for BE or U+FFFE 
for LE.  For example, the byte representation of the U+0042 character in UTF-16BE 
encoding is 0x0042, and in UTF-16LE encoding - 0x4200.

The sequence 0xFF is not used in UTF-8 encoding, 
so this may be a clear indication of UTF-16 (or UTF-32) encoding.

## Comparison of UTF-8 and UTF-16

Let's try to summarize information 
about these encodings and identify their pros and cons.

The summarized basic properties of encodings are presented in the table:
![Basic properties table](/static/images/props_table.png "Basic properties table")

And the following table shows the positive and negative properties 
of these encodings:
![Pros and cons](/static/images/pros_cons.png "Pros and cons")

## Conclusion

UTF-8 is very good for text files and network protocols, 
because there is no byte order problem. UTF-8 takes precedence when 
ASCII characters represent most of the characters in the text, 
because UTF-8 encodes all the characters into 1 byte (like ASCII). 
This gives a gain in the amount of memory up to 2 times compared to the UTP-16. 
UTF-8 file that contains only ASCII characters has the same encoding 
as the ASCII file and it’s very useful.

In turn, UTF-16 has the advantage in terms of the amount of memory used, 
unless ASCII characters dominate the text. As mentioned earlier, 
in the U+0800...U+FFFF range of Unicode basic plane characters 
UTF-8 will consume 3 bytes, and UTF-16 is still 2 bytes.

Thus, both forms of encoding can be useful for in-memory processing, 
and the best choice often depends on the software platforms and libraries you use. 
Java, JavaScript, ICU, and most Windows APIs are based on UTF-16, 
while Unix systems generally prefer UTF-8. 
The final choice of encoding format depends on the specific implementation 
of the software product.
