# react-native
# react-native
这篇文章主要是记录在react-native开发过程中遇到的比较坑的问题，如果后来入坑的同学进来，有同样问题可以参考一下，开始可能比较乱，等入门之后重新整理一遍。
系统：MacOS 10.13.5
react: 16.4.0,
react-native: 0.55.4
1.开发环境搭建基本没遇到问题可以参考https://reactnative.cn/docs/0.51/getting-started.html#content

2.在集成源生iOS应用的时候就出问题了https://reactnative.cn/docs/0.51/integration-with-existing-apps.html#content
  首先，执行在执行pod install指令的时候出现以下错误：
  Analyzing dependencies
Fetching podspec for `DoubleConversion` from `../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec`
Fetching podspec for `Folly` from `../node_modules/react-native/third-party-podspecs/Folly.podspec`
Fetching podspec for `React` from `../node_modules/react-native`
Fetching podspec for `glog` from `../node_modules/react-native/third-party-podspecs/glog.podspec`
Fetching podspec for `yoga` from `../node_modules/react-native/ReactCommon/yoga`
[!] CocoaPods could not find compatible versions for pod "Folly":
  In Podfile:
    Folly (from `../node_modules/react-native/third-party-podspecs/Folly.podspec`)

Specs satisfying the `Folly (from `../node_modules/react-native/third-party-podspecs/Folly.podspec`)` dependency were found, but they required a higher minimum deployment target.

[!] Automatically assigning platform `ios` with version `7.0` on target `NumberTileGame` because no platform was specified. Please specify a platform for this target in your Podfile. See `https://guides.cocoapods.org/syntax/podfile.html#platform`.
该问题需要将Podfile文件的platform选项解除注释，改成iOS8以上版本。
  执行安装指令以后，仍会报错：
  Analyzing dependencies
Fetching podspec for `DoubleConversion` from `../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec`
Fetching podspec for `Folly` from `../node_modules/react-native/third-party-podspecs/Folly.podspec`
Fetching podspec for `React` from `../node_modules/react-native`
Fetching podspec for `glog` from `../node_modules/react-native/third-party-podspecs/glog.podspec`
Fetching podspec for `yoga` from `../node_modules/react-native/ReactCommon/yoga`
Downloading dependencies
Installing DoubleConversion (1.1.5)
Installing Folly (2016.09.26.00)
Installing React (0.55.4)
Installing boost-for-react-native (1.63.0)
Installing glog (0.3.4)
[!] /bin/bash -c 
set -e
#!/bin/bash
set -e

PLATFORM_NAME="${PLATFORM_NAME:-iphoneos}"
CURRENT_ARCH="${CURRENT_ARCH:-armv7}"

export CC="$(xcrun -find -sdk $PLATFORM_NAME cc) -arch $CURRENT_ARCH -isysroot $(xcrun -sdk $PLATFORM_NAME --show-sdk-path)"
export CXX="$CC"

# Remove automake symlink if it exists
if [ -h "test-driver" ]; then
    rm test-driver
fi

./configure --host arm-apple-darwin

# Fix build for tvOS
cat << EOF >> src/config.h

/* Add in so we have Apple Target Conditionals */
#ifdef __APPLE__
#include <TargetConditionals.h>
#include <Availability.h>
#endif

/* Special configuration for AppleTVOS */
#if TARGET_OS_TV
#undef HAVE_SYSCALL_H
#undef HAVE_SYS_SYSCALL_H
#undef OS_MACOSX
#endif

/* Special configuration for ucontext */
#undef HAVE_UCONTEXT_H
#undef PC_FROM_UCONTEXT
#if defined(__x86_64__)
#define PC_FROM_UCONTEXT uc_mcontext->__ss.__rip
#elif defined(__i386__)
#define PC_FROM_UCONTEXT uc_mcontext->__ss.__eip
#endif
EOF

checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for arm-apple-darwin-strip... no
checking for strip... strip
checking for a thread-safe mkdir -p... ./install-sh -c -d
checking for gawk... no
checking for mawk... no
checking for nawk... no
checking for awk... awk
checking whether make sets $(MAKE)... yes
checking whether make supports nested variables... yes
checking for arm-apple-darwin-gcc... /Library/Developer/CommandLineTools/usr/bin/cc -arch armv7 -isysroot 
checking whether the C compiler works... no
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: SDK "iphoneos" cannot be located
xcrun: error: unable to lookup item 'Path' in SDK 'iphoneos'
/Users/yourname/Library/Caches/CocoaPods/Pods/External/glog/f09d6cdb8398b4922e87d51f5245de7e-1de0b/missing: Unknown `--is-lightweight' option
Try `/Users/yourname/Library/Caches/CocoaPods/Pods/External/glog/f09d6cdb8398b4922e87d51f5245de7e-1de0b/missing --help' for more information
configure: WARNING: 'missing' script is too old or missing
configure: error: in `/Users/yourname/Library/Caches/CocoaPods/Pods/External/glog/f09d6cdb8398b4922e87d51f5245de7e-1de0b':
configure: error: C compiler cannot create executables
See `config.log' for more details
该问题主要是SDK默认路径错误所导致的，可以通过sudo xcode-select --switch  /Applications/Xcode.app/Contents/Developer/
指令指定正确路径
