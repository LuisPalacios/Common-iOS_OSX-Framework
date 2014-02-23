Introduction
============

This document describes how to create an iOS and OSX framework that both share same source and header files. This is something I need to do from time to time, however I allways forget HowTo, so I've written this document and hope it's also helpful for the community.

The whoel iOS idea on how to create a Framework is coming from the excellent document done by jverkoey: <a href="https://github.com/jverkoey/iOS-Framework">How to create, develop, and distribute iOS Static Frameworks quickly and efficiently</a>.

<a rel="license" href="http://creativecommons.org/licenses/by/3.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by/3.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/3.0/">Creative Commons Attribution 3.0 Unported License</a>.
<br /a>
<br /a>

I'm using real code to create both Frameworks, indeed the code is from a project called **RNCryptor** <a href="https://github.com/RNCryptor/RNCryptor">CCCryptor (AES encryption) wrappers for iOS and Mac</a>, a library which fits very well in my objective, to create two frameworks (iOS & OSX) both using the same sources and headers.
<br />
<br />


Table of Contents 
===================

- [Common iOS and OSX Frameworks](#walkthrough)
  - [Overview](#overview)
  - [Common](#overview)
  - [iOS Framework](#iosframework)
      - [Create the static library](#staticlibrary)    
      - [Create the framework distribution target](#framework_distribution_target)  
      - [Create the Resources and Bundles](#resources)  
  - [OSX Framework](#osxframework)
	  - [Cocoa Framework](#cocoaframework)
  - [Using iOS/OSX Framworks in a project](#useinproject)
- [License](#license)


<a name="walkthrough" />
Common iOS and OSX Frameworks
========================================


As I mentioned I'm using the RNCryptor project as an example, however my Framework will be called "LPrncryptor" and the XCode project will be renamed to LPrncryptor_Framework-iOS.xcodeproj and LPrncryptor_Framework-OSX.xcodeproj.


<a name="overview" />
Overview
========

First I'm going to create three directories, one for the common code and the other two for iOS and OSX Xcode Projects.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-dirStructure.png)


<a name="common" />
Common code
===========
I grabbed a copy from the real project, **RNCryptor** <a href="https://github.com/RNCryptor/RNCryptor">CCCryptor (AES encryption) wrappers for iOS and Mac</a>, and then copy the main files into the Common directory:

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-CommonCopy.png)

Now that I have my common code lets create the iOS project and tweak it so it will create a .framework. 


<a name="iosframework" />
iOS Project
========================

First step is to create an XCode project for the iOS framework, which will have three targets: a static library, a bundle, and an aggregate.

The static library target will build the source into a static library (.a) and specify which headers will be "public", meaning they will be accessible from the .framework when we distribute it.

The bundle target will contain all of our resources and will be loadable from the framework.

The aggregate target will build the static library for i386/armv6/armv7/armv7s, generate the fat framework binary, and also build the bundle. You will run this target when you plan to distribute the .framework.


<a name="staticlibrary" />
iOS Framework: Static Library Target
----------------------------------------

### Step 1: Create a New "Cocoa Touch Static Library" Project

First step is to create a new project. Choose the same name that you want your Future framework to have. In my case the product name will be `LPrncryptor`, you'll see later that my framwork will also be named `LPrncryptor.framework`, so lets go ahead:

- Xcode->File New Project->iOS Framework & Library->Cocoa Touch Static Library
- Product Name: LPrncryptor
- Directory: LPrncryptor/Framework-iOS

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newproject.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newprojectdir.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newprojectname.png)

Final result: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_projectinit.png)



### Step 2: Rename Project adding "_Framework-iOS"

This step is very important for me, it's a personal preference. I like to rename the XCode project (but not its content) just to indicate this one is for iOS. I'll do the same later for the OSX one, so when I have both open pointing to the same sources I know where I am... 

In summary, I will end up having a Project called "LPrncryptor_Framework-iOS.xcodeproj" where all its contents will be called "LPrncryptor". So, within XCode, click on the project name and rename it: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject1.png)

***IMPORTANT: Don't rename the project content items,***

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject2.png)
 
With versions previous to XCode 5, after this you may need to change the manage scheme from here. 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject3.png)

I'm using XCode 5 and I've found it's not necessary.

### Step 3: Delete unnesary files and add Common code

From XCode you can now delete the LPrncryptor.m and LPrncryptor.h files and send them to the trash, you don't need them.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_deleteTwoFiles.png)

Next step, bind source and header files from the Common directory

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_bindcommon.png)

Final result: 
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_bindcommondone.png)


### Step 4: Create Primary Framework Header

Developers expect to be able to import your framework just by importing one file. In my example it will the be `<LPrncryptor/LPrncryptor.h>` header. I recommend that your project has such one header that imports all the public ones. Create such file under LPrncryptor group and select the same *Commoon directory where you copied all the common files*. File->new file->iOS->C and C++->Header file. I've named it LPrncryptor.h (and made sure I check the LPrncryptor target).

Within this header you are going to import **all of your framework's public headers**. I'm my example the are: RNCryptor.h, RNCryptorEngine.h, RNDecryptor.h, RNEncryptor.h, and not the file RNCryptor+Private.h which is private. So, the final result of my new file would be: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-CommonHeader.png)

Then in your projects you will only import this file

```
#import <Foundation/Foundation.h>
:
#import <LPrncryptor/LPrncryptor.h>
```

### Step 5: Make headers public

Once you've created your framework header file you need to make the Common headers "public". Public headers are headers that will be copied to the .framework and can be imported by those using your framework. These differs from "project" headers which will *not* be distributed with the framework. This distinction is what allows you to have a concept of public and private APIs.

**Xcode 5:**
Add Build Phases from the menu. Click on Editor > Add Build Setting -> Add Copy Headers. Note: If the menu options are grayed out, you'll need to click on the whitespace below the Build Phases to regain focus and retry.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_copyheaders1.png)

You should end up with something similar to this: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_copyheaders2.png)

### Ongoing Step: Adding New Sources to the Framework

Whenever you add new source to the framework you must decide whether to expose the .h publicly or
not. To modify a header's scope you will follow the same process as Step 5. By default a header's
scope will be "Project", meaning it will not be copied to the framework's public headers.


### Step 6: Update the Public Headers Location

By default the static library project will copy private and public headers to the same folder:
`/usr/local/include`. To avoid mistakenly copying private headers to our framework we want to ensure
that our public headers are copied to a separate directory, e.g. `$(PROJECT_NAME)Headers`. To change this setting,
select the project in the Project Navigator and then click the "Build Settings" tab. Search for "public
headers" and then set the "Public Headers Folder Path" to "$(PROJECT_NAME)Headers" for all configurations.
If you are working with multiple Frameworks make sure that this folder is unique.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_ProjectHeadersLocation.png)


### Step 7: Disable Code Stripping

We do not want to strip any code from the library; we leave this up to the application that is
linking to the framework. To disable code stripping we must modify the following configuration
settings:

    "Dead Code Stripping" => No (for all settings)
    "Strip Debug Symbols During Copy" => No (for all settings)
    "Strip Style" => Non-Global Symbols (for all settings)

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_nostrip.png)

### Step 8: Build for all architectures

It's important to know that you need to build for all architectures (i386 x86_64 armv7 armv7s arm64). In the "iOS Framework: Distribution Target->Step 3: Build the Other Platform" we create a script that takes care of it adding "ONLY_ACTIVE_ARCH=NO" to one of the commands. 

So, you don't have to, but here is the alternative, set "Build Active Architecture Only" to NO on both Debug and Release under Build Settings

    "Build Active Architecture Only" => No (for all settings)

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_allarchs.png)

In case you need to double check later if all architectures where built, you'll be able to use the following command

```
$ lipo -info LPrncryptor.framework/LPrncryptor
Architectures in the fat file: LPrncryptor.framework/LPrncryptor are: i386 x86_64 armv7 armv7s arm64
```

### Step 9: Prepare Framework skeleton

In order to use the static library as it were a framework we're going to generate the basic
skeleton of the framework in the static library target. To do this we'll include a simple post-build
script. Add a post-build script by selecting your project in the Project Navigator, selecting the target, and then the
"Build Phases" tab. 

**Xcode 4.X:** Click Add Build Phase > Add Run Script

**Xcode 5:** Select Editor menu > Add Build Phase > Add Run Script Build Phase

Paste the following script in the source portion of the run script build phase. You can rename the phase by clicking
the title of the phase (I've named it "LP_Prepare Framework (Run Script)"

#### Script that Prepares the framework

```bash
set -e

mkdir -p "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/A/Headers"

# Link the "Current" version to "A"
/bin/ln -sfh A "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/Current"
/bin/ln -sfh Versions/Current/Headers "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Headers"
/bin/ln -sfh "Versions/Current/${PRODUCT_NAME}" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/${PRODUCT_NAME}"

# The -a ensures that the headers maintain the source modification date so that we don't constantly
# cause propagating rebuilds of files that import these headers.
/bin/cp -a "${TARGET_BUILD_DIR}/${PUBLIC_HEADERS_FOLDER_PATH}/" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/A/Headers"

```

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_prepareframework.png)


This will generate the following folder structure:

```
-- Note: "->" denotes a symbolic link --

LPrncryptor.framework/
  Headers/ -> Versions/Current/Headers
  LPrncryptor -> Versions/Current/LPrncryptor
  Versions/
    A/
      Headers/
        LPrncryptor.h
        RNCryptor.h        RNCryptorEngine.h        RNDecryptor.h        RNEncryptor.h
    Current -> A
```

Try building your project now and look at the build products directory (usually
`~/Library/Developer/Xcode/DerivedData/<ProjectName>-<gibberish>/Build/Products/...`). You should
see a `libLPrncryptor.a` static library, a `Headers` folder, and a `LPrncryptor.framework` folder that contains the basic skeleton of your framework.



<a name="framework_distribution_target">
iOS Framework: Distribution Target
----------------------------------------

We must provide what is called a "fat binary" version of the static library that is comprised of all the possible platforms. These platforms include iphonesimulator (i386, x86_64) and iphoneos (armv6, armv7, and armv7s).

To generate this fat binary we're going to build the static library target for each platform.

### Step 1: Create an Aggregate Target

Click File > New Target > iOS > Other and create a new Aggregate target. Title it something like "Framework".

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate1.png)

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate2.png)

### Step 2: Add the Static Library as a Dependent Target

Add the static library target to the "Target Dependencies".

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate3.png)


### Step 3: Build the Other Platform

To build all platforms we're going to use a "Run Script" phase to execute some basic commands.
Add a new "Run Script" build phase to your aggregate target and paste the following code into it. You can rename the phase by clicking the title of the phase (I've named it "LP_Create Fat Framework (Run Script)", for example).

#### Script to create the Fat framework

```bash
set -e
set +u
# Avoid recursively calling this script.
if [[ $SF_MASTER_SCRIPT_RUNNING ]]
then
    exit 0
fi
set -u
export SF_MASTER_SCRIPT_RUNNING=1

SF_TARGET_NAME=${PROJECT_NAME}
SF_EXECUTABLE_PATH="lib${SF_TARGET_NAME}.a"
SF_WRAPPER_NAME="${SF_TARGET_NAME}.framework"

# The following conditionals come from
# https://github.com/kstenerud/iOS-Universal-Framework

if [[ "$SDK_NAME" =~ ([A-Za-z]+) ]]
then
    SF_SDK_PLATFORM=${BASH_REMATCH[1]}
else
    echo "Could not find platform name from SDK_NAME: $SDK_NAME"
    exit 1
fi

if [[ "$SDK_NAME" =~ ([0-9]+.*$) ]]
then
    SF_SDK_VERSION=${BASH_REMATCH[1]}
else
    echo "Could not find sdk version from SDK_NAME: $SDK_NAME"
    exit 1
fi

if [[ "$SF_SDK_PLATFORM" = "iphoneos" ]]
then
    SF_OTHER_PLATFORM=iphonesimulator
else
    SF_OTHER_PLATFORM=iphoneos
fi

if [[ "$BUILT_PRODUCTS_DIR" =~ (.*)$SF_SDK_PLATFORM$ ]]
then
    SF_OTHER_BUILT_PRODUCTS_DIR="${BASH_REMATCH[1]}${SF_OTHER_PLATFORM}"
else
    echo "Could not find platform name from build products directory: $BUILT_PRODUCTS_DIR"
    exit 1
fi

# Build the other platform.
# Without cocoapods
xcrun xcodebuild ONLY_ACTIVE_ARCH=NO -project "${PROJECT_FILE_PATH}" -target "${TARGET_NAME}" -configuration "${CONFIGURATION}" -sdk ${SF_OTHER_PLATFORM}${SF_SDK_VERSION} BUILD_DIR="${BUILD_DIR}" OBJROOT="${OBJROOT}" BUILD_ROOT="${BUILD_ROOT}" SYMROOT="${SYMROOT}" $ACTION

# Build the other platform.
# With cocoapods
# As documented here: https://github.com/jverkoey/iOS-Framework/issues/46
# when using cocoapods, change to this :
# xcrun xcodebuild ONLY_ACTIVE_ARCH=NO -workspace "${PROJECT_DIR}/${PROJECT_NAME}.xcworkspace" -scheme "${TARGET_NAME}" -configuration "${CONFIGURATION}" -sdk ${SF_OTHER_PLATFORM}${SF_SDK_VERSION} BUILD_DIR="${BUILD_DIR}" OBJROOT="${OBJROOT}" BUILD_ROOT="${BUILD_ROOT}" SYMROOT="${SYMROOT}" $ACTION



# Smash the two static libraries into one fat binary and store it in the .framework
xcrun lipo -create "${BUILT_PRODUCTS_DIR}/${SF_EXECUTABLE_PATH}" "${SF_OTHER_BUILT_PRODUCTS_DIR}/${SF_EXECUTABLE_PATH}" -output "${BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}"

# Copy the binary to the other architecture folder to have a complete framework in both.
cp -a "${BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}" "${SF_OTHER_BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}"

```

#### Important Note

The above script assumes that your library name matches your project name in the following line:

```bash
SF_TARGET_NAME=${PROJECT_NAME}
```

As this is not the case, (e.g. my xcode project is named LPrncryptor_Framework-iOS and the target name is LPrncryptor) then I have explicitly set the target name on that line. For example:

```bash
SF_TARGET_NAME="LPrncryptor"
```

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate4.png)


### Step 4: Build and Verify

You now have everything set up to build a distributable .framework to third-party developers. 


IMPORTNAT: **SELECT YOUR Aggregate target and build it**.

Notice this step, it's important to select the "Aggregate" and not the LPrncryptor target.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate5.png)

Once it's done, expand the Products folder in Xcode, right click the
static library and click "Show in Finder". If this doesn't open Finder to where the static library
exists then try opening
`~/Library/Developer/Xcode/DerivedData/<project name>/Build/Products/Debug-iphonesimulator/`.

Within this folder you will see your .framework folder.

You can now drag the .framework elsewhere, zip it up, upload it, and distribute it to your
third-party developers.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newTargetAggregate6.png)

Note: When you build the Aggregate you'll get the same LPrncryptor.framework either under iphoneos or iphonesimulator, the script is responsible of duplicating it to make sure you have the saome on both sides. You only need to copy one of them in order to use it in another project.



<a name="resources" />
iOS Framework: Resources and Bundles
----------------------------------------
I'm not using this section, so I'm just leaving the original from jverkoey: <a href="https://github.com/jverkoey/iOS-Framework">How to create, develop, and distribute iOS Static Frameworks quickly and efficiently</a>.

To distribute resources with a framework, we are going to provide the developer with a separate
.bundle that contains all of the strings and resources. This distribution method provides a number
of advantages over including the resources in the .framework itself.

- Encapsulation of resources. We can scope resource loading to our framework's bundle.
- Easy to add bundles to projects.
- The developer doesn't have to copy the entire .framework into their application.

The hard part about bundles is creating the target. Xcode's bundle target doesn't actually create a
loadable bundle object, so we have to do some post-build massaging of the bundle. It's important
that we create a bundle target because we need to create the bundle using the Copy Bundle Resources
phase that will correctly compile .xib files (a Copy Files phase does not accomplish this!).

### Step 1: Create the Bundle Target

In the framework project, create a new bundle target. Click on File > New > Target > OS X > Bundle. You will need to name the bundle something
different from your framework name or Xcode will not let you create the target. I've named the target SerenityResources. We will rename the output of the target to Serenity.bundle in a following
step.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/newbundletarget.png)

Ensure that the Framework setting is set to "Core Foundation".

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/newbundletarget2.png)

### Step 2: Clean up the Bundle Target Settings

By default the bundle will only show build settings for Mac OS X. It doesn't really matter what it
builds for because the bundle isn't actually going to have any code in it, but I prefer to have
things nice and consistent. Open the bundle target settings and delete the settings for
Architectures, Base SDK, and Build Active Architecture Only.

**Xcode 5:** Deleting a build setting will reset it to the Project's build setting. It should switch from OS X to iOS.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/bundlesettings.png)

This is also when you should change your bundle target's product name to the name of your framework
rather than the target name. Click on your project in the Project Navigator and then select
the bundle target. Click Build Settings, search for "Product Name", and then replace
the value of Product Name with the name of your framework (e.g. $(TARGET_NAME) replaced by Serenity)

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/serenityproductname.png)

### Step 3: Remove HIDPI Mac OS X Build Setting
We created a OS X Bundle and it includes and option to merge HIDPI (retina and non-retina) art assets into a .tiff file. You don't want this behavior and need to disable it, or you will be unable to load your image assets from the bundle.

In the Bundle target go to Build Settings and search for `COMBINE_HIDPI_IMAGES` and delete the user defined setting. When you build, verify that your @2x.png and .png images are all in the bundle.

![Delete the COMBINE_HIDPI_IMAGES setting](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/delete_combine_hidpi.png)

### Ongoing Step: Add Resources to the Bundle Target Copy Files Phase

Whenever you add new resources that you want to include with your framework you need to add it to
the bundle target that you created.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/newbundleresource.png)

### Step 4: Add the Bundle Target to your Aggregate Target

Whenever we build the framework for distribution we likely also want to build the bundle. Add the
bundle target to your aggregate target's dependencies.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/bundledependency.png)

### Step 5: Loading Bundle Resources

In order to load bundle resources, we must first ask the third-party developer to add the .bundle to
their application. To do so they will simply drag the .bundle that you distributed with the
.framework to their project and ensure that it is copied in the copy files phase of their app
target.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/addbundle.png)

To load resources from the bundle we will use the following code:

```obj-c
// Load the framework bundle.
+ (NSBundle *)frameworkBundle {
  static NSBundle* frameworkBundle = nil;
  static dispatch_once_t predicate;
  dispatch_once(&predicate, ^{
    NSString* mainBundlePath = [[NSBundle mainBundle] resourcePath];
    NSString* frameworkBundlePath = [mainBundlePath stringByAppendingPathComponent:@"Serenity.bundle"];
    frameworkBundle = [[NSBundle bundleWithPath:frameworkBundlePath] retain];
  });
  return frameworkBundle;
}

[UIImage imageWithContentsOfFile:[[[self class] frameworkBundle] pathForResource:@"image" ofType:@"png"]];
```

You can see an example of loading a resource from within the framework in the Widget object in the
included Serenity framework.

**Xcode 5:** Do not use the Asset Catalog for any resources within a bundle. On an iOS 7.0 only project, a bug causes the pathForResource method to return nil. 




<a name="osxframework" />
OSX Framework
=============

Now let's go and create the XCode project for the OSX framework, which will have one targets: a cocoa Framework


<a name="cocoaframework" />
OSX Cocoa Framework
-------------------

### Step 1: Create a New "Cocoa Framework" Project

Create a new project. Choose the same name `LPrncryptor`:

- Xcode->File New Project->OSX Framework & Library->Cocoa Framework
- Product Name: LPrncryptor
- Directory: LPrncryptor/Framework-OSX

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_newproject.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_newprojectdir.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_newprojectname.png)

### Step 2: Rename Project adding "_Framework-OSX"

This step is very important for me, it's a personal preference. I like to rename the XCode project (but not its content) just to indicate this one is for OSX and to differentiate from previous iOSX project. 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject1.png)

***IMPORTANT: Don't rename the project content items,***

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_renameProject2.png)
 
With versions previous to XCode 5, after this you may need to change the manage scheme. 

### Step 3: Delete unnesary files and add Common code

From XCode you can now delete the LPrncryptor.m and LPrncryptor.h files and send them to the trash, you don't need them.

Next step, bind source and header files from the Common directory

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_bindcommon.png)

Final result: 
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_bindcommondone.png)


### Step 4: Use the Primary Framework Header

You should have the main header file `LPrncryptor.h` that you created in the `iOS Step 4: Create Primary Framework Header` so you don't need to do anything else as it has been added in previous step 3 from the Common directory. Just remember in your OSX projects to import this file

```
#import <LPrncryptor/LPrncryptor.h>
```

### Step 5: Make headers public

Make the Common headers "public". Public headers are headers that will be copied to the .framework and can be imported by those using your framework. These differs from "project" headers which will *not* be distributed with the framework. This distinction is what allows you to have a concept of public and private APIs.

Go into the Target->Build Phases->Copy Headers and move the public ones from Project to Public, so you should have something like this: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_copyheaders.png)

### Ongoing Step: Adding New Sources to the Framework

Whenever you add new source to the framework you must decide whether to expose the .h publicly or
not. To modify a header's scope you will follow the same process as Step 5. By default a header's
scope will be "Project", meaning it will not be copied to the framework's public headers.


### Step 6: Change installation directory

In future projects where you are going to install this Framework I recomend to create a directory called "Frameworks"" in the root of your future project. Then it's necessary that you modify this Framework Build Setting `Dynamic Library Install Name`

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_installframework.png)


### Step 7: Build and Verify

You now have everything set up to build a distributable .framework to third-party developers. 

IMPORTNAT: **SELECT YOUR Framework and build it**.

Once it's done, expand the Products folder in Xcode, right click the
LPrncrypto.framework and click "Show in Finder". It should be in a place like the following:
`~/Library/Developer/Xcode/DerivedData/<project name>/Build/Products/Debug/`.

Within this folder you will see your .framework folder. You can now drag the .framework elsewhere, zip it up, upload it, and distribute it to your third-party developers.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-OSX_newTargetAggregate.png)



<a name="useinproject" />
Using Frameworks in a project
=====================================

Use the iOS Framework
----------------------

Adding LPrncryptor.framework to your iOS project.

- Open your project in Xcode.
- Drag the LPrncryptor.framework file into your project in Xcode.
- Make sure Copy items into destination group's folder is selected. Press the Finish button.

Ensure that you have any additional frameworks needed by your own one. For example, in the case of the LPrncryptor I have to add also the Security.framework


Use the OSX Framework
----------------------

Adding LPrncryptor.framework to your OSX project.

- Open your project in Xcode.
- Drag the LPrncryptor.framework file into your project in Xcode.
- Make sure Copy items into destination group's folder is selected. Press the Finish button.

Now you want to make sure that the framework will be copied into your app bundle.

- Select the root of your project in the Project Navigator. This will show the project's configuration. Select the Build Phases tab which will show a list of Build Phases.
- If you do not see a Copy Files build phase, go to the menu bar and select Editor > Add Build Phase > Add Copy Files Build Phase.
- In the new build phase just added, set the destination to Frameworks.
- Click the + button and select the Dropbox.framework you just added.

Ensure that you have any additional frameworks needed by your own one. For example, in the case of the LPrncryptor I have to add also the Security.framework


Using both in the same workspace
--------------------------------

Imaging you have a Workspace where you have two projects inside, one for iOS and second for OSX, and where you also share common code (valid for both). In such scenario you may need to also insert your Frameworks ()different version iOS & OSX) inside these workspace. 

Here is an example of how I'm doing so, I created a Workspace called `farAdmin` which has two projects inside, one for iOS and one for OSX. As you can see in the directory design and binding, I've different directories for the iOS and OSX Frameworks

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-projectUse1.png)

In the case of OSX sub-project don't forget the copy files phase


<a name="license" />
License
=======

Except as otherwise noted, the content of this page is licensed under the Creative Commons
Attribution 3.0 Unported License, and code samples are licensed under the Apache 2.0 License.

To view a copy of this license, visit http://creativecommons.org/licenses/by/3.0/ or send a letter
to Creative Commons, 444 Castro Street, Suite 900, Mountain View, California, 94041, USA.




