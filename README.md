## Description
This is a code reproduction repo for https://github.com/ionic-team/capacitor/issues/6182

## Problem
The main issue here is I can't set the following Response headers at the top level of `index.html` loaded by `@capacitor/ios` as `capacitor://localhost`:
```
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```
Since I can't set those headers, I can't use enable Cross-site isolation within Ionic application and I can't use `SharedArrayBuffer`.

More info:
- https://web.dev/cross-origin-isolation-guide/
- https://web.dev/why-coop-coep/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer

My understanding is `@capacitor/ios` is using a [UrlSchemeHandler](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration/2875766-seturlschemehandler) 
to load the local files as `capacitor://localhost` and it should be able to set the response headers in the following code:
- https://github.com/ionic-team/capacitor/blob/main/ios/Capacitor/Capacitor/WebViewAssetHandler.swift#L35
- https://github.com/ionic-team/capacitor/blob/main/ios/Capacitor/Capacitor/WebViewAssetHandler.swift#L68

### My tests results
Using the safari devtools I can see that none of the response headers are being set and other info like 200 status code are also missing.
<img width="1673" alt="Screen Shot 2022-12-20 at 11 40 52 pm" src="https://user-images.githubusercontent.com/64799803/208812695-663714e4-f3bb-434e-a2fc-0781337d890e.png">


## Reproduction
### Technical Details
```
npm --version output: 8.19.2
node --version output: v16.18.1
pod --version output (iOS issues only): 1.11.2
iOS version: 15.4
```

### Steps to reproduce
On terminal

1. `git clone https://github.com/marcelo-nagatomo/capacitor-issue-6182.git`
2. `cd capacitor-issue-6182`
3. `npm install`
4. `ionic capacitor sync ios`
5. `ionic capacitor open ios`


On XCode
1. Select an iOS simulator of your preference
2. Run the application
3. Once application is loaded, you can open safari DevTools to inspect the `MyApp` application
4. On the `Network` tab, inspect all loaded assets and check the missing response headers.

**Note**
> Tested on:
>
> iOS simulator: iPhone 12 Pro Max - iOS 15.4
> 
> Real device: iPhone 11 Pro Max - iOS 16

### Troubleshooting
Xcode might complain 'cause of different versions, so you can remove and re-add the ios project.
```
rm -Rf ios
ionic capacitor add ios
ionic capacitor open ios
```
