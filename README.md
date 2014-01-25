PFCloud-Cache
=============

iOS library for caching cloud function calls in Parse.

This category adds an additional **cachePolicy** parameter to Parse's existing asynchronous (background) cloud function call methods. It exactly replicates the [existing caching behavior] used with the **PFQuery** object. It creates a record in the cache for every unique combination of function name + parameters. For it to work, the contents of the **parameters** dictionary must conform to the **NSCoding** protocol. 

```
+ (void)callFunctionInBackground:(NSString*)function
                  withParameters:(NSDictionary*)parameters
                     cachePolicy:(PFCachePolicy)cachePolicy
                           block:(PFIdResultBlock)block;
```
```
+ (void)callFunctionInBackground:(NSString*)function
                  withParameters:(NSDictionary*)parameters
                     cachePolicy:(PFCachePolicy)cachePolicy
                          target:(id)target
                        selector:(SEL)selector;
```

##Sample Usage

```
#import "PFCloud+Cache.h"

[PFCloud callFunctionInBackground:function
                   withParameters:params
                      cachePolicy:kPFCachePolicyCacheThenNetwork
                            block:^(id object, NSError* error) {
		//Because we are using kPFCachePolicyCacheThenNetwork as our cache policy,
		//this block will be invoked twice (if a cached result exists). 
}];
```

##Cache Management

Cached objects are persisted to disk in the following folder on the user's device:

```
~/Library/Caches/com.tumblr.TMDiskCache/TMDiskCacheShared/
```

Cached objects persist between app restarts until they expire. By default they never expire. To impose a maximum cache age use the ```setMaxCacheAge:``` method. The cache can also be explicity cleared for a particular cloud function call using the ```clearCachedResult:``` method, or for all calls using the ```clearAllCachedResults``` method.

##How it Works

The trick is to create a unique key in the cache for every unique combination of function + parameters. This library concatenates the function name with a JSON-serialized string of the parameters dictionary. It then takes an MD5 hash of this string using the [RSCategories] library to create a unique key in the cache. Actual caching is performed using Tumblr's [TMCache] library. Thanks to those libraries for their great work!

##Installation

Easiest installation is using CocoaPods to resolve all dependencies:

```pod 'PFCloud+Cache', '~> 0.0.1'```

Otherwise you must manually copy the .h and .m files from this repo as well as from [RSCategories] and [TMCache]. Obviously you must also have the [Parse SDK] installed. Enjoy!

[existing caching behavior]:https://parse.com/docs/ios_guide#queries-caching/iOS
[RSCategories]:https://github.com/reejosamuel/RSCategories
[TMCache]:https://github.com/tumblr/TMCache
[Parse SDK]:https://parse.com/downloads/ios/parse-library/latest
