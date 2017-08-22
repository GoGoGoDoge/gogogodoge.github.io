---
layout: post
title: "HEVC-http-live-streaming-prototype"
date: 2017-08-20
---

<!-- ## {{page.title}} ## -->

This blog mainly helps me refresh what I have done one year ago to implement the HEVC live streaming, as shown to all it is computation demanding. The idea is pretty straightforward. If one server is not enough to encode raw frames to catch up the playback speed at a frame at 24 fps and some folks may go 30 fps, we try manipulate all servers we own.

This blog will include:
* A review of basic knowledge
* The whole procedure

---
## A review of basic knowledge ##

What is a raw frame, a pure pixel is represented by red, green and blue. Say for example, a single pixel would at least requires 24 bits for it, assuming 255 level for each elemental color. This does not sound any intimidating but a frame if 1920 x 1080 (1080 progressive), this is like 5 MB for a raw frame. 1 hour 24 fps would cost 421 GB. Wow, best wish to my hard disk and whatever store it.

Okay, some genius use YUV to represent instead. This actually takes advantage of human perception. Human is more sensitive to brightness but less sensitive to chrominance. So we focus more on the brightness. In my project, actually, we have device to output raw frame to us. It is called Declink from [BlackMagic](https://www.blackmagicdesign.com/products/decklink). It use 10 bit YUV. This sounds better but far from enough to deliver.

Here comes to encoding codec. H.264 AVC. This allows the low bandwidth network to carry our amazing video. Most of streaming provider use this majority codec, yes including Youtube. The implementation is not simple. One of major advantages is from motion compensation which is compensated by different macro blocks from H.264 from 4x4 to 16x16 pixels. When we know we can refer to others to recompute the approximate pixels we do not require to store them and therefore compress!

For X.265 HEVC, it can search up to 64x64 and other optimization of course. Therefore, it normally achieves better quality (from human perception) and lower compressed rate. However, the price is that it requires more computation power.

## The whole procedure ##

HEVC is good but it is slow in encoding speed. For live streaming service, at least we live up to 24 fps to match the playback speed otherwise we lose users just kidding. If one server is not enough, I just sequencing (logically interconnected via switch) connect all servers. Imaging pouring water (raw data frame) into a sequence of glasses. When water leaks from a glass, it will be filled into another (server).

I actually go further to try 4K, 3840 x 2160. When it works but if it is action movie, it does not work quite well.

Is it feasible? Yes, for ethernet, most switch can provide 1000Mbps, for a raw frame (YUV) it is around 20Mbps for 1920 x 1080 (HD). Okay, at least network speed is not the bottleneck. We use TCP but I know this concept better after I joined CMU and learn some stuff related to socket. Since it is live broadcast application, we need some container to wrap Audio (AAC) and Video (HEVC) together. We choose HLS. Apple always dominates... Since it is live broadcast application, loss is also allowed. But the strategy of dropping is important, a series of frames must be dropped together, I frame, B frame and P frame. We lost I frame, drop all. Loss B frame, just drop it.

The programming consists of two part, deliver it or encode it. For each node, it finds the incoming frames are encoded already just pass it until sinker. Otherwise it encodes it. However, a monitor program keeps track with encoding frame rate, if it is lower than a threshold, it must deliver it. How about failover? I write another program running as service to monitor its previous nodes. This is called a notebook. Once its parent failed or lost connect, it checks the notebook which inherit from its parent, and checks until an available node.  
