---
title: Talking a little bit about HASH MD5 in TISS
date: 2014-09-22 15:05:43
tags: cryptography
---
I created this post with the intention of helping other developers who encountered the same difficulty that I encountered during the process of developing TISS modules and HASH MD5 validations.

<!--more-->


To introduce the text, let's go to definitions (well summarized):

## What is TISS?

_The TISS - Exchange of Information on Supplementary Health is a standard for recording and exchanging data between operators of private health care plans and medical-hospital service providers of the National Agency for Supplementary Health - ANS in Brazil.
_

## What is MD5 Hash?

MD5 (Message Digest algorithm 5) is a 128-bit unidirectional hashing algorithm developed by RSA Data Security, Inc., described in RFC 1321, and widely used by peer-to-peer (P2P) protocol software. -Peer), integrity checking of files and logins.

## What is the purpose of the MD5 Hash in TISS?

The exchange of communication between operators and providers in the Tiss webservices is carried out in well-structured and complex XML files. The MD5 Hash exists to guarantee the integrity of the file, since the contents of the XML elements are used to generate the Hash encryption. This guarantees that the file has not been changed manually, as a simple change of characters or whitespace, for example, would completely change the MD5 HASH of the file.

## How to calculate MD5 Hash for TISS?

Concatenate the contents of all elements into a single String.
Remove whitespace ONLY at the beginning and end of the file.
Special characters, accents and dashes must be maintained.
MD5 encoding must be generated in UTF-8 standard.

Example method to generate MD5 calculation for TISS in C# .NET

Below is a simple method that I use to generate the MD5 Hash.


## Method to generate HashMD5
```
public static string getMD5Hash(string input)
{
    System.Security.Cryptography.MD5 md5 = System.Security.Cryptography.MD5.Create();
    byte[] inputBytes = System.Text.Encoding.UTF8.GetBytes(input);
    byte[] hash = md5.ComputeHash(inputBytes);
    System.Text.StringBuilder sb = new System.Text.StringBuilder();
    for (int i = 0; i < hash.Length; i++)
    {
        sb.Append(hash[i].ToString("x2"));
    }
    return sb.ToString();
}
```