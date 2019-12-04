# idcheckKYC 2.11

## Liveness3D module

### Installation

This framework ment to be installed via [cocoapods](https://cocoapods.org/).

* Add to your `Podfile` specs repositories: `WebpayDev/Specs`, and public one - `CocoaPods/Specs`
* Specify `use_frameworks!` option
* Add dependency `idcheckKYC/Liveness3D` to your target

Resulting `Podfile` could look as follows:
```ruby
platform :ios, '9.3'

source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/WebpayDev/Specs.git'

use_frameworks!

target 'MyAwesomeApp' do

  pod 'idcheckKYC/Liveness3D'

  # any other dependencies
end
```
* Run `pod install --repo-update`

### Usage

Instantiate the module and pass handlers
```objc
SSLiveness3D *liveness3D =
[SSLiveness3D.alloc initWithBaseUrl:baseUrl
                        applicantId:applicantId
                              token:token
                             locale:locale
             tokenExpirationHandler:^(void (^ _Nonnull completionHandler)(NSString * _Nullable))
 {
     // get new token then call completionHandler
     getNewToken(^(NSString *token) {
        completionHandler(token);
     };
     
 } completionHandler:^(UIViewController * _Nonnull controller, SSLiveness3DStatus status, SSLiveness3DResult * _Nullable result) {
     
     // process status then update UI accordingly

     // possible statuses are:
     // SSLiveness3DStatus_Cancelled,
     // SSLiveness3DStatus_InitializationFailed,
     // SSLiveness3DStatus_CameraPermissionDenied,
     // SSLiveness3DStatus_TokenIsInvalid,
     // SSLiveness3DStatus_FaceMismatched,
     // SSLiveness3DStatus_VerificationPassedSuccessfully,

     [controller dismissViewControllerAnimated:YES completion:nil];
 }];
``` 
Where 
* `baseUrl` - `test-msdk.webpay.by` for test environment or `msdk.webpay.by` for production one
* `applicantId` - identifier of the applicant to check against
* `token` - your auth token
* `locale` - user locale (preferably `NSLocale.currentLocale.localeIdentifier`, but you can use any)

Upon success use `result.applicantActionId` as `#{actionId}` to check the results against the server with the GET `/resources/applicantActions/#{actionId}/one`.

Note please that being initialized once the overall process is looped until cancelled or completes successfully. So normally you never got `SSLiveness3DStatus_FaceMismatched` completion status. Use the following to change this behaviour.
```objc
liveness3D.shouldCompleteOnFaceMismatch = YES;
```

Also there is an optional way to get the server response right after it arrives from the server. That allows you to process the result asynchronously and possibly cancel the further processing.
```objc
// The block must call `completionHandler` provided with a boolean parameter. Passing `YES` stops the further processing.

liveness3D.resultHandler = ^(SSLiveness3DResult * _Nonnull result, void (^ _Nonnull completionHandler)(BOOL)) {
  
    NSLog(@"Coordinator: Liveness3D got result from server: %@", result);
    
    completionHandler(NO);
};

```

Then create and display UI
```objc
UIViewController *vc = [liveness3D getController];

vc.modalTransitionStyle = UIModalTransitionStyleCrossDissolve;

[self.navigationController presentViewController:vc animated:YES completion:nil];
```

### Customization

Texts within the Liveness3D module can be customized and/or localized with [Zoom.strings](https://github.com/WebpayDev/iOSMobileSDK/blob/master/2.11/Zoom.strings) file. Add it to your project, then modify/localize as required.

Colors could be adjusted by assigning `liveness3D.theme` an object that conforms to `SSLiveness3DThemeProtocol`
