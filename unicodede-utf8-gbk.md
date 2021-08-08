---
title: "Character set, Encoding, Locale, Font and More..."
date: 2018-01-28 16:39:00 +0800
tags: [Unicode, encoding, locale, font, Sublime-Text]
categories: [technique]
---

It is almost destined that at some point of our programming years, we will meet
the issue of character set, encoding and all the related stuff. There are tons
of articles which explains the meaning of `Unicode`,  `UTF-8`, `UTF-16` etc. I
am not going to repeat that here. In this post, I will write about what
confused me when I tried to understand all this stuff.

<!--more-->

# Character, character set, code point and encoding

We use characters every day. They are huge number of them, e.g., English
characters (`A`,  `B`, `C`), punctuations (`,`, `.`, `!`), Chinese characters
(`你`, `好`), Japanese characters (`の`, `た`).

In order to represent all these characters or part of them, we give each
character a unique number (you can think of this number as an ID). In other
words, when we refer to a number, the corresponding character will be uniquely
determined. We call this number [`code
point`](https://en.wikipedia.org/wiki/Code_point).

Simply said, a **_character set_** is table which list the correspondence
between code points and characters. One of the most simple character set is
[ASCII](https://en.wikipedia.org/wiki/ASCII), in which characters are
represented as code points in the range $[0, 127]$. For example, the code point
of letter `A` is 65 (`0x41` in hex format) and the code point of `!` is 33
(`0x21` in hex format). There are a lot of other character sets available,
e.g., Unicode, GB18030, GBK, etc.

Now, let's talk about encoding. Since we have character set, where each
character gets its own code point. But how to represent these code points so
that characters can be actually saved in the hard disk (encode issue), and how
to read from the hard disk and know which character we are reading (decode
issue)?  That is what encodings deal with. With proper encoding of character
set, we can save characters in disk and read characters from disk without any
error. Since encoding is just a way to represent code points in memory, it is
natural that various encodings exist for the same character set. Character set
and encoding are different concept. Do not confuse them. Take Unicode for an
example, Unicode is a character set, and several encodings such as UTF-8,
UTF-16 and UTF-32 designed for it exist.

It should be noted that for simple character set such as `ASCII` and `Latin 1`,
the code point of a character and its binary representation in memory are the
same. That is because all the code point can be expressed in just one byte.
That is why sometimes character set and encoding are use interchangeably. But
for Unicode, the code point of a character does not necessarily equal to its
binary representation due to encoding issues of several bytes.

## References

+ <https://www.w3.org/International/questions/qa-what-is-encoding>
+ <http://ergoemacs.org/emacs/unicode_basics.html>

# SBCS, DBCS and MBCS

SBCS denotes single-byte character set, where all characters are represented
using a single byte. Thus, the maximum characters which can be represented are
256. DBCS denotes double-byte character set, where all characters are
represented using two bytes. It is easy to see that both SBCS and DBCS can
represent only a limited number of characters. In contrast, a multi-byte
character set (MBCS) can represent a character using single byte, two bytes,
three bytes or even more bytes. This gives it much more flexibility to
represent more characters. Table below lists some representative character
sets,

| character set | representatives |
| ------------- | --------------- |
| SBCS          | ASCII, Latin 1  |
| DBCS          | GB2312          |
| MBCS          | Unicode         |

# Unicode

Unicode is a combined efforts from different organizations to try to
standardize the representation of all existing characters in the world. It is
the most comprehensive character set in the world, and it is updated regularly.
Each character in Unicode is represented using a code point. The Unicode
representation for code point starts with `U+`. For example, the code point for
my name is [`U+90DD`](http://unicode.scarfboy.com/?s=%E9%83%9D),
[`U+6770`](http://unicode.scarfboy.com/?s=%E6%9D%B0) and
[`U+4E1C`](http://unicode.scarfboy.com/?s=%E4%B8%9C). It is one of the most
widely used character set in today's world.

## References

+ [Joel Spolsky's Unicode article](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses).

# Relationship between Unicode and UTF-8, UTF-16, UTF-32 etc.

Unicode is just a comprehensive character set, which assign a code point to
each character. In contrast, UTF-8, UTF-16 and UTF-32 are encodings which
implement the Unicode. For example, UTF-8 decrees how the different characters
in Unicode character set are actually represented in memory. In UTF-8 encoding,
code point does not necessarily equal to the actual memory representation of a
character. Take the character `东` for an example, its code point is `U+4E1C`,
and the byte representation in memory is `E4B89C`.

## References

+ [Difference between Unicode and UTF-8](https://stackoverflow.com/q/643694/6064933).
+ <http://www.polylab.dk/utf8-vs-unicode.html>
+ [Difference between Unicode, UTF8, ANSI etc.](https://stackoverflow.com/q/700187/6064933)
+ [Difference between Unicode and ASCII](https://stackoverflow.com/q/19212306/6064933).

# GB2312, GBK, GB18030, Big5

Like Unicode, both the above names denote different character sets related to
Chinese (simplified or traditional).

GB2312 is an early character set for simplified Chinese, which contains 6763
Chinese characters (simplified) and 682 other signs. From
[WikiPedia](https://en.wikipedia.org/wiki/GB_2312),

>GB2312 includes 6,763 Chinese characters (on two levels: the first is arranged
by reading, the second by radical then number of strokes), along with symbols
and punctuation, Japanese kana, the Greek and Cyrillic alphabets, Zhuyin, and a
double-byte set of Pinyin letters with tone marks

Big5 is the character set for traditional Chinese characters used in Taiwan,
HongKong etc. It is developed by 5 biggest IT companies in Taiwan in the 1980s.

GBK is an extended character set of the early GB2312 (`k` denotes `Kuozhan` in
Chinese, which means `extend`). From
[WikipPedia](https://en.wikipedia.org/wiki/GBK_(character_encoding)),

> GBK not only extended the old standard GB2312 with Traditional Chinese
> characters, but also with Chinese characters that were simplified after the
> establishment of GB2312 in 1981. With the arrival of GBK, certain names with
> characters formerly unrepresentable, like the "róng"(镕) character in former
> Chinese Premier Zhu Rongji's name, are now representable.

[GB18030](https://en.wikipedia.org/wiki/GB_18030) is a further improvement over
GB2312 and GBK and is backward compatible with GB2312 and GBK. It encodes all
the code points in the Unicode 3.0 standard. So we can convert between Unicode
and GB18030.

## References

+ <https://www.pinyinjoe.com/pinyin/encoding.htm>

# Microsoft Windows Code page

In the old days before Unicode becomes popular. Microsoft use code pages to
deal with different characters used by different regions in the world. The name
_code page_ has somewhat combined meanings. Sometimes, it refers to character
sets, and other times, it means character encoding.

For computers sold in mainland China, the default code page for command line is
[`code page 936`](https://en.wikipedia.org/wiki/Code_page_936_(Microsoft_Windows)) (or
`cp936` in short form). In the Windows command line, you can check the current
code page by using `chcp` without any argument. If the error message from some
executable are in Unicode format and you are using `cp936`, [the error message
will be shown as gibberish](https://jdhao.github.io/2017/12/29/cmder-usage/).
The way to fix is to change your code page to UTF-8. The Windows code page for
UTF-8 is 65001. You can use `chcp 65001` or `chcp utf-8` to change your code
page to UTF-8.

## References

+ <https://msdn.microsoft.com/en-us/library/windows/desktop/dd317756>(v=vs.85).aspx
+ [Is codepage 65001 and UTF-8 the same](https://stackoverflow.com/q/1629437/6064933)?

# Character set and typeface (or font)

Now that we have character set and encoding. How to display these characters in
certain form (fontsize, bold or italic)? This is what the typeface or font (the
two terms are often used interchangeably) do:

> A font is a set of printable or displayable text characters in a specific
> style and size. The type design for a set of fonts is the typeface and
> variations of this design form the typeface family .

You may have already noticed that for a particular character, there are several
font which can display it. See the image below for the display of the character
`东` in different fonts.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-30/10105248.jpg"
         title="Different font, same character"
         style="float: middle;">

A font has many glyphs for different characters. But no font contains all the
characters in the Unicode standard. According to discussion
[here](https://stackoverflow.com/questions/34732718/why-isnt-there-a-font-that-contains-all-unicode-glyphs),
a maximum of 65536 glyphs are allowed for OpenType font. If you want to display
a Unicode character in a certain font, you have to make sure the font has the
glyph for that character. If the chosen font does not have a glyph for the
character, the character may be displayed as a square box. See reference below
on how to check if a character is included in a certain font.

## References

+ [How to know if a Unicode character is missing from a font](https://stackoverflow.com/q/43834362/6064933).

# Similar character may have different code point

The other day, I encounter a *strange* problem. Two characters looking similar
are actually different. The two characters are shown below (enlarged in order
to show their subtle difference):

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/1523291.jpg" width="400">
</p>

# Locale and difference between various UTF-8 locales

In Linux system, we often see the term `locale`. What does that mean? According
to [oracle documentation](https://docs.oracle.com/cd/E23824_01/html/E26033/glmbx.html):

>A locale consists of a number of categories for which country-dependent
formatting or other specifications exist. A program's locale defines its code
sets, date and time formatting conventions, monetary conventions, decimal
formatting conventions, and collation (sort) order.
>
>A locale name can be composed of a base language, country (territory) of use,
and codeset. For example, German language is de, an abbreviation for Deutsch,
while Swiss German is de_CH, CH being an abbreviation for Confederation
Helvetica. This convention allows for specific differences by country, such as
currency unit notation. In Oracle Solaris 11 the default locale codeset is
UTF-8, an ASCII compatible 8-bit encoding form of Unicode. The fully defined
locale name for Swiss German would thus be de_CH.UTF-8.
>
>More than one locale can be associated with a particular language, which
allows for regional differences. For example, an English-speaking user in the
United States can select the en_US.UTF-8 locale (English for the United
States), while an English-speaking user in Great Britain can select en_GB.UTF-8
(English for Great Britain).

Thus, different locales can share the same encoding `UTF-8`, which is why you
see locales such as `en_US.UTF-8` and `zh_CN.UTF-8`. In these locales, the
characters use the same encoding `UTF-8`. Their differences reside in
region-specific format, e.g., date format and monetary sign (`$` or `￥`).

## References

+ [Difference between zh_CN.UTF-8 and en_US.UTF-8](https://stackoverflow.com/q/21153485/6064933).
+ [Difference between UTF-8 and en_AU.UTF-8](https://stackoverflow.com/q/29751218/6064933).
+ [What should I set my locale to](https://unix.stackexchange.com/q/149111/221410).

# Search a Unicode character in Sublime Text using its code point

We usually search a character using the character itself. But certain
characters, such as the [zero-width space
character](https://en.wikipedia.org/wiki/Zero-width_space) are hard to type. In
that situation, it is easier to search such characters using their code points.
In Sublime Text, you can use `\x{CODE_POINT}` to search a character with code
point `CODE_POINT`. For example, for zero-width space, you can use `\x{200b}`
to search it.

# References

+ [Character sets, locales, fonts and code page relationship](https://stackoverflow.com/q/23979669/6064933).
