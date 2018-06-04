# RefApp

### Launch Time Analysis

---

## Target Launch Time

* Apple Recommended App Launch Time: 400ms. On iPad, its 500ms.
* Its same amount of time system takes to animate app launch.
* If your app takes close to or over 20 seconds, your app will be terminated by watchdog.

---

## Launch Types

* Warm Launch:
	* When your app and/or its data is cached.  Generally between quit and relaunch of your app. Tends to be faster than warm launch.
* Cold Launch:
	* When neither your app nor its data is cached. Generally after device reset or reboot. Tends to be slower launch.

---

## How to Measure Launch Time

---

* Pre-main: Time OS takes to launch app's process, resolve and load dependecies and rebase and bind dylibs and their symbols before calling main function
	* Use DYLD_PRINT_STATISTICS environment variable to inform dyld to print the pre-main statistics to console. 

---

* Post-main: Time applications takes since OS calls main function till application becomes active and usable by users:
	* Use CFAbsoluteTime method to get time when main is called and right before returning from application:didFinishLauchWithOptions: in AppDelegate
	* Developers need to have their own measurement because methods could be returned successfully and yet app is not usable for users 

---

## Test Devices

* iPhone 5s, iOS 10.3.3
* iPhone 5s, iOS 11.3.1

---

## Observations

---

* Pre-main: 6.4 seconds (100.0%)
	* dylib loading time: 5.9 seconds (92.3%)
	* rebase/binding time: 173.3 miliseconds (2.6%)
	* ObjC setup time: 154.66 miliseconds (2.4%)
	* initializer time: 165.75 miliseconds (2.5%)
	* slowest initializers: 
		* libSystem.B.dylib: 13.61 miliseconds (0.2%) 

---

* Post-main: 2.6034619808197 seconds
	* Time measured between `application:willFinishLauchWithOptions:` and `application:didBecomeActive:`
	* Actual time before app becomes usable could be higher 

---

## DYLD Analysis

* RefApp loads 502 dylibs, including OS internal and external dylibs |
* RefApp depends on 87 external dylibs |
* RefApp depends on 52 platform dylibs |

---

## Improvements

---

#### Major Improvement Points

* Use lesser dylibs 
  * Apple recommends max 6 dylibs per app
* Merge dylibs 
  * UAppFrameworks should embed the component framework
  * Use subspecs to contain smaller components within other components

---

* Use static libraries/frameworks instead of dynamic frameworks
  * CocoaPods 1.5.0 added support for that
  * Components might need to do some work to support static libs
* Smaller UserDefaults size
  * Do not store big objects in UserDefaults
  * SecureStorage uses user defaults and need to be changed

---


* Do less work on app launch
  * Lazy initialization of components
  * Do initialization asynchronously or on background queues

---

#### Minor Improvement Points

* Less number of classes and methods
* Use more Swift
* Swift structs over classes
* Mark classes as final if not required to be inherited

---

## Random Point

---

Too many duplicate reachability classes

* AFNetworkingReachability
* RESTClientReachability
* DIComm.Reachability
* EWSReachability
* RegistrationReachability
* SwiftReachability

---

## Notes

---

## Thanks