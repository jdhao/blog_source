---
title: "The YouTube Content ID system"
date: 2021-08-02T01:27:05+08:00
draft: false
tags: [YouTube, video, audio]
categories: [Note]
---

Google introduced the [content ID system](https://support.google.com/youtube/answer/2797370?hl=en) in 2007 to cope with the
unauthorized use of copyrighted music and video content. The content ID system
has successfully helped the copyright owners to find the pirated content and
paid billions of dollars to the copyright owners[^1].

<!--more-->

# How content ID works

The copyright owners can submit the audio and video content they want to
protect to YouTube. When a user uploads new content to YouTube, the new content
will be compared to the files in the database to check their similarity.

How do we compare the similarity between audio or video files? We need [audio](https://emysound.com/blog/open-source/2020/06/12/how-audio-fingerprinting-works.html) and [video](https://en.wikipedia.org/wiki/Digital_video_fingerprinting) fingerprinting technology. Generally speaking, we can extract fingerprints (i.e., features or vectors) from the audio and video files and store them in the database. When a new content arrives, we extract its fingerprints and compare it with the fingerprints in the database. Then we can calculate the similarity between the new content and the content in the database. If the similarity is above a threshold, we can assume that the uploaded content is a pirated content.

There is little resource online talking about which methods Google use for
fingerprinting audio and videos.

# Audio fingerprinting

The YouTube audio fingerprinting technology is quite advanced. As pointed out
by videos [here](https://www.youtube.com/watch?v=DRCPzRfej5c) and [here](https://www.youtube.com/watch?v=hIa3ZQ9bs0Y), it can spots audio piracy even if you
distort the music significantly (by adding noise or change speed etc.).

[This paper](https://ieeexplore.ieee.org/document/4217502) from Google research
shows a method for audio fingerprinting. It may be used by content ID. However,
this paper is quite old. YouTube may have improved its algorithms over the
years.

# Video fingerprinting

For video fingerprinting, we would guess that YouTube extract frames from
videos and use deep-neural nets to extract features from video frames. Those
features are the so-called fingerprints for the video. The similarity between
videos can be compared frame by frame and aggregated in some way, for example,
by calculating how many similar frames two videos share in common.

This above guess is partially verified by post [here](https://www.fastcompany.com/3062494/how-youtube-is-fixing-its-most-controversial-feature).
Apart from that, I can not seem to find resource on what methods YouTube
actually use for video fingerprinting.

# Counter-attack techs

Since fingerprinting uses machine learning methods for finding pirated content,
we can forge adversarial content to fool the content ID system: the pirated
content can be altered in a subtle way so that the system can not detect it is
a pirated content.

There are some research on this, for example, see [here](https://www.cs.umd.edu/~tomg/projects/copyrightattack/) and [here](https://arxiv.org/abs/1906.07153).

# References

+ https://www.blog.google/outreach-initiatives/public-policy/protecting-what-we-love-about-internet-our-efforts-stop-online-piracy/
+ https://www.streamingmedia.com/Articles/ReadArticle.aspx?ArticleID=78358
+ Youtube content identification technology?: https://stackoverflow.com/q/649116/6064933
+ Manipulating the YouTube Algorithm: https://www.youtube.com/watch?v=1PGm8LslEb4

[^1]: Source here: https://www.tubefilter.com/2018/11/07/youtube-payouts-content-id/
