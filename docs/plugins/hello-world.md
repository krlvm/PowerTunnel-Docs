# Developing Hello World Plugin

In this guide you will learn how to develop a simple plugin that logs "Hello World" on bootstrap, and blocks requests to specific websites.  
The resulting project can be found [here](https://github.com/krlvm/PowerTunnel-Plugin-Template).

It assumes you are already familiar with Java.

## Preparing Main Class

First, create an arbitrary package and a class in it, for example, `com.example.plugin.MyPlugin`.

Every plugin should extend `PowerTunnelPlugin` class to get access to plugin lifecycle:

``` java
package com.example.plugin;

import io.github.krlvm.powertunnel.sdk.plugin.PowerTunnelPlugin;

public class MyPlugin extends PowerTunnelPlugin {
    ...
}
```

## Listening to server lifecycle

The key plugin method is `onProxyInitialization` - it is being called after injecting the last plugin and before starting the proxy server. During the initialization, you can modify proxy server configuration and bind your server lifecycle listeners - you can listen to server status change (starting, running, stopping, not running) and proxy server lifecycle events - control the traffic when it's going from client to proxy, from proxy to server, from server to proxy, and from proxy to client, and control DNS requests.

To bind a server lifecycle listener, use method `registerServerListener`, which accepts `ServerListener` instance as argument.

!!! tip
    It's more convenient to extend `ServerAdapter` than `ServerListener`

``` java
@Override
public void onProxyInitialization(ProxyServer proxy) {
    System.out.println("Hello World!");
    registerServerListener(new ServerAdapter() {
        @Override
        public void onProxyStatusChanged(ProxyStatus status) {
            System.out.println("status = " + status.name());
        }
    });
}
```

## Listening to proxy lifecycle

### Blocking requests

To listen to proxy lifecycle events, you need to register your listener in `onProxyInitialization` using method `registerProxyListener`, which accepts `ProxyListener` instance and (optionally) priority for the given listener instance.

!!! tip
    It's more convenient to extend `ProxyAdapter` than `ProxyListener`

In this article, we are developing a plugin that restricts access to some websites, the best time for filtering is the transfer of a packet from the client to the proxy - corresponding ProxyListener lifecycle callback is `onClientToProxyRequest`.

For example, we can restrict access to `google.com` using following code:

``` java
@Override
public void onProxyInitialization(ProxyServer proxy) {
    registerProxyListener(new ProxyAdapter() {
        @Override
        public void onClientToProxyRequest(ProxyRequest request) {
            if(request.getHost().endsWith("github.com")) {
                request.setBlocked(true);
            }
        }
    });
}
```
Calling method `setBlocked` is equivalent to calling method `setResponse` with body text "Access denied by proxy server" and status code 403. Request is considered blocked if response is not null, if response is null, the request goes further along the chain. You can check if a request is blocked by calling the `isBlocked` method.

!!! important
    It's highly recommended to check if the request is blocked before inspecting it, because it might be blocked by another plugin and there's no need to modify it as it will not reach its destination.

If you want to set custom response, use `setResponse` method:

``` java
@Override
public void onProxyInitialization(ProxyServer proxy) {
    registerProxyListener(new ProxyAdapter() {
        @Override
        public void onClientToProxyRequest(@NotNull ProxyRequest request) {
            if(request.isBlocked()) {
                // Request is already blocked by another plugin
                return;
            }
            if(request.getHost().endsWith("github.com")) {
                request.setResponse(proxy.getResponseBuilder("This website is blocked", 403)
                        .contentType("text/plain")
                        .build()
                );
            }
        }
    });
}
```

!!! faq
    **Why can't I see the blocking message?**  
    You may notice that website you blocked just doesn't load without showing any message.  
    This is because most likely (in 99% of cases) you are loading the site over [HTTPS](https://en.wikipedia.org/wiki/HTTPS), where S stands for Secure: you cannot just interfere with HTTPS traffic without a [MITM](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attack. Information on using MITM is found below.  
    If you're looking for plain HTTP website for testing, check [neverssl.com](http://neverssl.com/)

### Modifying responses

To modify the response, it's recommended to use `onProxyToClientResponse` lifecycle method.

Since most websites use HTTPS, you need to use a MITM attack to decrypt packets.  
You should also make sure that you're working with the full packet and not with a chunk.  
PowerTunnel provides a very simple way to do this.

!!! caution
    You will need to install a Root CA on every machine, the traffic that you are MITMing.  
    Some webservers validates the certificate, so they could break in unexpected ways.

``` java
@Override
public void onProxyInitialization(ProxyServer proxy) {
    proxy.setMITMEnabled(true);
    proxy.setFullResponse(true);
}
```

For example, you want to add "Hello World" to the beginning of the web page.  
You will need to check that you aren't working with a chunk.

!!! warning
    Make sure that MITM is enabling before modifying request to make sure you will not break encrypted HTTPS packet

``` java
@Override
public void onProxyToClientResponse(@NotNull ProxyResponse response) {
    if(!proxy.isMITMEnabled()) return;
    if(!response.isDataPacket()) return;
    response.setRaw("<b>Hello World!</b>" + response.raw());
}
```

## Creating manifest

To make your plugin work, you need to provide a manifest, as described in previous article.  
Assuming our main class is named `MyPlugin` and it's located in package `com.example.plugin`, we should create a `plugin.ini` in project resources with the following content:

``` yaml
id: my-plugin
version: 1.0
versionCode: 1
name: My Plugin
description: Hello World Plugin
author: Unknown Author
homepage: https://github.com/krlvm/PowerTunnel-Plugin-Template
mainClass: com.example.plugin.MyPlugin
targetSdkVersion: 99
```

## Testing plugin

Since Desktop and Android editions are sharing the same codebase, it's easier to test the plugin on Desktop version, while it is guaranteed that it will work in the same way on Android.

!!! note
    Android uses its own Java Virtual Machine — Android Runtime (ART), previously known as Dalvik.  
    To produce Android-compatible jarfile, the plugin code should be compiled to `.dex` file.  
    PowerTunnel uses [D8](https://developer.android.com/studio/command-line/d8) tool from Android SDK to produce Android-compatible jars, it will be available later.

To test plugin, you need to produce a `.jar` artifact and put the jarfile into `plugins` directory of PowerTunnel distribution directory. If the proxy server was running at the moment you pasted the file into plugins directory, you need to completely restart PowerTunnel.

## To be continued

The documentation is not yet complete, if you have any suggestions or questions, please open a new issue on GitHub.