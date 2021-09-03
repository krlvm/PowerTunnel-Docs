<div align="center" style="margin-bottom: 8px">
<img src="https://raw.githubusercontent.com/krlvm/PowerTunnel/master/.github/images/logo.png" height="164px" width="164px" />
<br>
<h1 style="margin: 12px 0 0 0">PowerTunnel</h1>
<div style="margin: 10px 0 4px 0">
<a href="https://github.com/krlvm/PowerTunnel/releases/latest"><img src="https://img.shields.io/github/downloads/krlvm/PowerTunnel/total?style=flat-square&label=desktop%20downloads" alt="Desktop Downloads"/></a>
<a href="https://github.com/krlvm/PowerTunnel-Android/releases/latest"><img src="https://img.shields.io/github/downloads/krlvm/PowerTunnel-Android/total?style=flat-square&label=android%20downloads" alt="Android Downloads"/></a>
</div>
Powerful and extensible cross-platform proxy server
</div>
<hr style="margin: 12px 0">

[PowerTunnel](https://github.com/krlvm/PowerTunnel) is an extensible proxy server with user interface and powerful API.

The server is built on top of [LittleProxy](https://github.com/adamfisk/LittleProxy) and its functionality can be extended with plugins developed using PowerTunnel SDK. PowerTunnel plugins are developed in Java, or any other JVM language such as Kotlin.

PowerTunnel SDK allows you to write plugins which are able to filter and modify HTTP(S) traffic and DNS requests.
Any plugin you write is supported by [Desktop](https://github.com/krlvm/PowerTunnel) and [Android](https://github.com/krlvm/PowerTunnel-Android) editions without writing any additional platform-dependent code.

The codebase for PowerTunnel client versions consists of two parts: the platform code and PowerTunnel Core, which manages the proxy server and implements the SDK.