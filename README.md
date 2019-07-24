# Android Reader SDK
This document targets Reader SDK developers.

The **ReaderSDK** allow you to [read](#ReadAZave) content from an unzipped Zave on your local device.

If the Zave isn't embedded, the Zave can be [downloaded](#downloadAZave).


## This page covers:

*   [Reader SDK Usage](#Usage)
    * [Download a Zave](#downloadAZave)   
    * [Read a Zave](#ReadAZave) 	
*   [Work on reader SDK](#Setup)
*   [Repository Structure](#Structure)
*   [Changelog](#Changelog)

## <a name="Usage"></a> Use the Reader SDK

### Supported Android Versions

The library works with :
 - `minSdkVersion : 15` 
 - `targetSdkVersion : 28`

### Import the SDK

To use aqf-reader sdk, add these linez in your app's build.gradle.

```groovy
repositories {
    maven {url 'http://artifactory.raksdtd.com/artifactory/libs-release'}
    // or target 'libs-snapshot' for alpha and snapshot. 
}

dependencies {
...
    implementation 'com.aquafadas:aqf-reader:[version]'
...
}
```

To use `aqf-reader`, multidex and specific ndk must be enabled.

```groovy
android {
    defaultConfig {
    	...
        multiDexEnabled true
        ndk {
            abiFilters "armeabi-v7a", "x86"
        }
    }
}

dependencies {
    implementation 'com.android.support:multidex:1.0.3'
}
```

More info about abiFilters :
* <https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.NdkOptions.html>
* <https://developer.android.com/studio/build/configure-apk-splits#configure-abi-split>


### <a name="downloadAZave"></a> Download a Zave
#### Step 1 : Get an instance of ZaveDownloadManager

The class **`ZaveDownloadManager`** allows to download zave.
```kotlin
// Get an instance of ZaveDownloadManager.
ZaveDownloadManager.getInstance([Context])
```


#### Step 2 : Add a ZaveDownloadManagerListener
To handle download events (progress of the download, download complete, ...), add a `ZaveDownloadManagerListener` to the ZaveDownloadManager.

__Methods overridables in the listener :__

* `onFirstPartCompleted(zaveId: String, tags: HashMap<String, Any>, hasMultiplePart: Boolean) {}`
* `onProgressChanged(zaveId: String, tags: HashMap<String, Any>, globalProgress: Int, firstPartProgress: Int) {}`
* `onCanceled(zaveId: String, tags: HashMap<String, Any>) {}`
* `onCompleted(zaveId: String, tags: HashMap<String, Any>) {}`
* `onExceptionOccurred(zaveId: String, tags: HashMap<String, Any>, exception: Exception?) {}`

```kotlin
// Add a listener to the ZaveDownloadManager to handle download events.
zaveDownloader.addListener(object : ZaveDownloadManager.ZaveDownloadManagerListener {
            
            override fun onCompleted(zaveId: String, tags: HashMap<String, Any>) {
                super.onCompleted(zaveId, tags)
                // Do something like lanching the reader.
            }

            override fun onProgressChanged(zaveId: String, tags: HashMap<String, Any>, globalProgress: Int, firstPartProgress: Int) {
                super.onProgressChanged(zaveId, tags, globalProgress, firstPartProgress
                // do something like displaying download progress.
            }
        })
```

#### Step 3 : Create a download request

```kotlin
// Create a request to downloading the zave.
val downloadRequest = DownloadRequest.Builder(String zaveId, String url, ZaveDownloadManager.ZaveType zaveType).build()
```

#### Step 4 : Download the zave

```kotlin
// Launch the download with the request.
zaveDownloader.getZave(downloadRequest)
```

### <a name="ReadAZave"></a> Read a Zave
#### Launch Reader

The Zave to read need to be unzipped. To launch the reader, create a `ReaderIntent`with the builder.

```kotlin
// Create a Reader intent. It require the context and the directory path of the unzipped zave.
val intent = ReaderIntent.Builder(context, zavePath).build()
// launch the intent
context.startActivity(intent)
```
The ReaderIntent's builder allow to add extras to customize reader with glassPanBars, gui.xml, ... 

#### ZaveFile
ZaveFile class gives information about the Zave.

Example : 
```kotlin
// Get the path where the zave will be download. By default teh path is: "$externalCacheDir/$zaveId"
val zavePath = ZaveDownloadManager.getInstance(this).getFinalDestPath(zaveId)

// Folder where is the unzipped zave.
val zaveFile = ZaveFile(zavePath)

// check if the zave exist.
if (zaveFile.exists()) {
 // do something
}
```



## <a name="Setup"></a> Getting started to work on reader repository
### Getting started to work on the SDK

```bash
git clone ssh://git@gitpub.rakuten-it.com:7999/eco/ext-android-aquafadas-reader.git --recursive
cd ext-android-aquafadas-reader
```

### Version file
Versions are defined in the file : `dependenciesReader.gradle`.


## <a name="Structure"></a> Repository Structure

```bash
├── README.md                  # guide for developers
├── dependenciesReader.gradle  # This gradle file define an array, ext.readerDep... wich define repo version number and the libs used by the whole repo
├── reader-sample-app          # sample application that uses aqf-reader
├── aqf-reader                 # library used to launch and read the zave documents
├── config                     # submodule: fork of config rakuten plugin - We added scripts and versions in config-aquafadas and the submodule config-internal 
```

## Related Documents
* [Rakuten Developper](https://developers.rakuten.com/intra/)
* [Config repo](https://gitpub.rakuten-it.com/projects/ECO/repos/ext-android-aquafadas-config/browse)
* [Common repo with common-aframowork lib and commonaquafadas-utils](https://gitpub.rakuten-it.com/projects/ECO/repos/ext-android-aquafadas-common/browse)


## <a name="Changelog"></a> Change log

### 1.2.6 - 2019-07-23
- [BUGFIX] Show current page number in action bar when option is activated in Gui.xml

### 1.2.5 - 2019-07-19
- Update common framework for version 1.2.2

### 1.2.4 - 2019-07-15
- Update common framework for version 1.2.1

### 1.2.2 - 2019-07-11
- Revert last bugfix

### 1.2.1 - 2019-07-11
- [BUGFIX] Show current page number in action bar (subtitle)

### 1.2.0 - 2019-07-04
- [FEATURE] Update foxit lib to 64 bits
- Replace old way to read encrypted file (change old cpp source by java style)

### 1.1.1 - 2019-06-20
- [FEATURE] Keep audio in background handle audio focus from other media and page transition.

### 1.1.0 - 2019-05-21
- [BUGFIX] Display mode not applied.
- [FEATURE] Add boolean `reader_play_audio_in_background` to keep audio enrichment playing in background.

### 1.0.4 - 2019-04-03
- [BUGFIX] Analytics not sent for the first opened page.

### 1.0.3 - 2019-03-1
- [FEATURE] Add analytics in reflow
- [BUGFIX] Remove xxxhdpi drawable for ressource : afdpreader_ic_page_placeholder

### 1.0.2 - 2019-02-8
- [FEATURE] Add french translation : "REFLOW SUR CETTE PAGE ( %1$s )"
- [BUGFIX] Fix NPE when a zave PDF doesn't contain background image.

### 1.0.1 - 2019-01-24
- [BUGFIX] Move drawable folder from hdmi to default folder to fix "unfound ressources" in React and Cordova.

### 1.0.0 - 2019-01-16
- **FIRST RELEASE**

