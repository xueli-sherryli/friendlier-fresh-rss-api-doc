## What is Google Reader

**Google Reader** (Google Reader) was once a web-based aggregator owned by Google Inc. that reads [Atom](https://zh.wikipedia.org/wiki/Atom_ (standard)) and [RSS](https://zh. wikipedia.org/wiki/RSS). The English version of Google Reader was released on 7 October 2005 via [Google Labs](https://zh.wikipedia.org/wiki/Google实验室), and on 17 September 2007 it became the official version. The Chinese version of Google Reader was released around 18 September 2007 or so.

On 13 March 2013 Google announced that as one of the second spring cleaning plans, Google Reader was terminated on 1 July 2013 due to the year-on-year decline in the number of users

## What is the FreshRSS / Google Reader API

FreshRSS is a self-hosted RSS aggregator. It is lightweight, easy to use, powerful and customisable.

The FreshRSS / Google Reader API provides the ability to interact with its backend, making it more flexible for developers!

!!! note
    Google has never publicly released its API documentation, the existing API documentation is based on the results of reverse engineering more than ten years ago.

Now, the Google Reader API with [Fresh RSS](https://freshrss.github.io/FreshRSS/en/developers/06_GoogleReader_API.html),[Tiny Tiny RSS](https:// github.com/eric-pierce/freshapi), [miniflux](https://miniflux.app/docs/google_reader.html) and other open-source RSS services compatible, giving more RSS readers/middleware greater interaction possibilities, the purpose of writing this document The purpose of this document is to facilitate developers to call (if someone is willing to develop an SDK based on this document that would be better!)

## What the FreshRSS / Google Reader API is not

FreshRSS / Google Reader API itself is not a specific provider of RSS feeds, i.e., it does not provide content distribution services, it is only used to facilitate access to various RSS backend APIs, and the specific access to refer to the implementation of the specific backend (some of the methods may not be supported by all the backend/plugin).

## Components

This document is divided into two parts

- Identity authentication: to obtain the SID/LSID/Auth, as well as for the operation of the T token
- API call: using the above authentication items, to operate the reader