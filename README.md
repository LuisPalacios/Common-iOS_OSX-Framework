NOTE: THIS DOCUMENT IS STILL WORK IN PROGRESS ... 

Introduction
============

How to create two frameworks, one for iOS and one for OSX, both sharing the same source and header files.

Every time I want to create code that will be shared by a project for iOS and OSX then I don't remember the HowTo, so I've written this document just for that and also return something to the community. 

This work is based on the excellent one done by jverkoey: <a href="https://github.com/jverkoey/iOS-Framework">How to create, develop, and distribute iOS Static Frameworks quickly and efficiently</a>.

In order to document by example, I'm using a real project, **RNCryptor** <a href="https://github.com/RNCryptor/RNCryptor">CCCryptor (AES encryption) wrappers for iOS and Mac</a>, a library which fits very well to create two frameworks (iOS & OSX) both using the same sources and headers.
<br />
<br />

<a rel="license" href="http://creativecommons.org/licenses/by/3.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by/3.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/3.0/">Creative Commons Attribution 3.0 Unported License</a>.
<br /a>
<br /a>


Table of Contents 
===================

- [Common iOS and OSX Frameworks](#walkthrough)
  - [Overview](#overview)
  - [Common](#overview)
  - [iOS Framework](#iosframework)
  - [OSX Framework](#osxframework)
- [License](#license)


<a name="walkthrough" />
Common iOS and OSX Frameworks
========================================

Objective is to create two frameworks, one for iOS and a different one for OSX, however both will share same source and header files.

I'm using the RNCryptor project as an example, however I'll call my project with a different name in order to differentiate: "LPrncryptor".


<a name="overview" />
Overview
========

First I'm going to create three directories, one for the common code and the other two for iOS and OSX Xcode frameworks. 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-dirStructure.png)

I'll copy the common code into the Common directory and then create a iOS Framework (this one is the difficult part) and finally create an OSX Framework (easy). 

- LPrncryptor
	- Common
	- Framework-iOS
	- Framework-OSX 



<a name="common" />
Common code
===========
As I mentioned, I'm using the code from the real project, **RNCryptor** <a href="https://github.com/RNCryptor/RNCryptor">CCCryptor (AES encryption) wrappers for iOS and Mac</a>, a library which fits very well to create two frameworks (iOS & OSX) both using the same sources and headers.
<br />
First step is to copy the common code in a common directory, so in this case I copy just the source and header files:

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-CommonCopy
.png)

Next step is the creation of the Frameworks

<a name="iosframework" />

Create the iOS Project
========================

First step is to create an XCode project for the iOS framework, which will have three targets: a static library, a bundle, and an aggregate.

The static library target will build the source into a static library (.a) and specify which headers will be "public", meaning they will be accessible from the .framework when we distribute it.

The bundle target will contain all of our resources and will be loadable from the framework.

The aggregate target will build the static library for i386/armv6/armv7/armv7s, generate the fat framework binary, and also build the bundle. You will run this target when you plan to distribute the .framework.


<a name="static_library_target" />
iOS Framework: Create the Static Library Target
----------------------------------------

### Step 1: Create a New "Cocoa Touch Static Library" Project


The product name will be the name of your framework, in my case, `LPrncryptor` will generate
`LPrncryptor.framework` once we've set up the project.

- Xcode->File New Project->iOS Framework & Library->Cocoa Touch Static Library
- Product Name: LPrncryptor
- Directory: LPrncryptor/Framework-iOS

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newproject.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newprojectdir.png)
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_newprojectname.png)

So you end up with something similar to 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_projectinit.png)




### Step 2: Rename Project adding "_Framework-iOS"

I like to rename the XCode project (not its subdirctories nor the groups or files) to highlight it's for iOS. I like to have a name like LPrncryptor_Framework-iOS.xcodeproj for the Project file and a different name "LPrncryptor" for everything else. The reason is simple, I like to differentiate correctly from the OSX framework project that I'll create later. 

So, within XCode and the iOS project open, rename it:

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject1.png)

Don't rename the project content items, 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject2.png)
 
With versions previous to XCode 5, after this you may need to change the manage scheme from here. 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_renameProject3.png)

If using XCode 5 I've found it's not necessary.

### Step 3: Delete unnesary files and add Common code

From XCode you can now delete the LPrncryptor.m and LPrncryptor.h files and send them to the trash.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_deleteTwoFiles.png)

Then bind the common source and header files from the Common directory

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_bindcommon.png)

so you sould get something similar to the following: 
![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_bindcommondone.png)


### Step 3: Create the Primary Framework Header

Developers expect to be able to import your framework and do it just by importing one file. In my example it will the be `<LPrncryptor/LPrncryptor.h>` header. Ensure that your project has such a header. So, go and create such file under LPrncryptor group and *important: under the same Commoon directory where you copied all the common files*. File->new file->iOS->C and C++->Header file and name it LPrncryptor.h and check the LPrncryptor target.

Within this header you are going to import **all of the public headers for your framework**. I'm my example they will be: RNCryptor.h, RNCryptorEngine.h, RNDecryptor.h, RNEncryptor.h but not the file RNCryptor+Private.h which is private. So, the final aspect of my new file would be: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-CommonHeader.png)

Then in your projects you only import this file

```
#import <Foundation/Foundation.h>
:
#import <LPrncryptor/LPrncryptor.h>
```

Once you've created your framework header file and have the Common files, you just need to make it them "public" header. Public headers are headers that will be copied to the .framework and can be imported by those using your framework. This differs from "project" headers which will *not* be distributed with the framework. This distinction is what allows you to have a concept of public and private APIs.

**Xcode 5:**
Add Build Phases from the menu. Click on Editor > Add Build Setting -> Add Copy Headers. Note: If the menu options are grayed out, you'll need to click on the whitespace below the Build Phases to regain focus and retry.

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_copyheaders1.png)

You should end up with something similar to this: 

![image](https://raw.github.com/LuisPalacios/Common-iOS_OSX-Framework/master/images/lp-iOS_copyheaders2.png)



### Step 3: Update the Public Headers Location

By default the static library project will copy private and public headers to the same folder:
`/usr/local/include`. To avoid mistakenly copying private headers to our framework we want to ensure
that our public headers are copied to a separate directory, e.g. `$(PROJECT_NAME)Headers`. To change this setting,
select the project in the Project Navigator and then click the "Build Settings" tab. Search for "public
headers" and then set the "Public Headers Folder Path" to "$(PROJECT_NAME)Headers" for all configurations.
If you are working with multiple Frameworks make sure that this folder is unique.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/publicheadersconfig.png)

### Ongoing Step: Adding New Sources to the Framework

Whenever you add new source to the framework you must decide whether to expose the .h publicly or
not. To modify a header's scope you will follow the same process as Step 2. By default a header's
scope will be "Project", meaning it will not be copied to the framework's public headers.

### Step 4: Disable Code Stripping

We do not want to strip any code from the library; we leave this up to the application that is
linking to the framework. To disable code stripping we must modify the following configuration
settings:

    "Dead Code Stripping" => No (for all settings)
    "Strip Debug Symbols During Copy" => No (for all settings)
    "Strip Style" => Non-Global Symbols (for all settings)

### Step 5: Prepare the Framework for use as a Dependent Target

In order to use the static library as though it were a framework we're going to generate the basic
skeleton of the framework in the static library target. To do this we'll include a simple post-build
script. Add a post-build script by selecting your project in the Project Navigator, selecting the target, and then the
"Build Phases" tab. 

**Xcode 4.X:** Click Add Build Phase > Add Run Script

**Xcode 5:** Select Editor menu > Add Build Phase > Add Run Script Build Phase

Paste the following script in the source portion of the run script build phase. You can rename the phase by clicking
the title of the phase (I've named it "Prepare Framework", for example).

#### prepare_framework.sh

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

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/prepareframework.png)

This will generate the following folder structure:

```
-- Note: "->" denotes a symbolic link --

Serenity.framework/
  Headers/ -> Versions/Current/Headers
  Serenity -> Versions/Current/Serenity
  Versions/
    A/
      Headers/
        Serenity.h
        Widget.h
    Current -> A
```

Try building your project now and look at the build products directory (usually
`~/Library/Developer/Xcode/DerivedData/<ProjectName>-<gibberish>/Build/Products/...`). You should
see a `libSerenity.a` static library, a `Headers` folder, and a `Serenity.framework` folder that
contains the basic skeleton of your framework.

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/buildphase1.png)

<a name="framework_distribution_target">
Create the Framework Distribution Target
----------------------------------------

When actively developing the framework we only care to build the platform that we're testing on. For
example, if we're testing on the iPhone simulator then we only need to build the i386 platform.

This changes when we want to distribute the framework to third party developers. The third-party
developers don't have the option of rebuilding the framework for each platform, so we must provide
what is called a "fat binary" version of the static library that is comprised of the possible
platforms. These platforms include: i386, armv6, armv7, and armv7s.

To generate this fat binary we're going to build the static library target for each platform.

### Step 1: Create an Aggregate Target

Click File > New Target > iOS > Other and create a new Aggregate target. Title it something like "Framework".

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/aggregatetarget.png)

### Step 2: Add the Static Library as a Dependent Target

Add the static library target to the "Target Dependencies".

![](https://github.com/jverkoey/iOS-Framework/raw/master/gfx/targetdependencies.png)

### Step 3: Build the Other Platform

To build the other platform we're going to use a "Run Script" phase to execute some basic commands.
Add a new "Run Script" build phase to your aggregate target and paste the following code into it.

#### build_framework.sh

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
xcrun xcodebuild -project "${PROJECT_FILE_PATH}" -target "${TARGET_NAME}" -configuration "${CONFIGURATION}" -sdk ${SF_OTHER_PLATFORM}${SF_SDK_VERSION} BUILD_DIR="${BUILD_DIR}" OBJROOT="${OBJROOT}" BUILD_ROOT="${BUILD_ROOT}" SYMROOT="${SYMROOT}" $ACTION

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

If this is not the case (e.g. your xcode project is named SerenityFramework and the target name is
Serenity) then you need to explicitly set the target name on that line. For example:

```bash
SF_TARGET_NAME=Serenity
```

### Step 4: Build and Verify

You now have everything set up to build a distributable .framework to third-party developers. Try
building the aggregate target. Once it's done, expand the Products folder in Xcode, right click the
static library and click "Show in Finder". If this doesn't open Finder to where the static library
exists then try opening
`~/Library/Developer/Xcode/DerivedData/<project name>/Build/Products/Debug-iphonesimulator/`.

Within this folder you will see your .framework folder.

You can now drag the .framework elsewhere, zip it up, upload it, and distribute it to your
third-party developers.

<a name="resources" />
Resources and Bundles
=====================

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


crear tres directorios the project we are going to have three targets: a static library, a bundle, and an aggregate.


<a name="license" />
License
=======

Except as otherwise noted, the content of this page is licensed under the Creative Commons
Attribution 3.0 Unported License, and code samples are licensed under the Apache 2.0 License.

To view a copy of this license, visit http://creativecommons.org/licenses/by/3.0/ or send a letter
to Creative Commons, 444 Castro Street, Suite 900, Mountain View, California, 94041, USA.




