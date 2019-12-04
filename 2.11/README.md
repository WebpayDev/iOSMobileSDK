# idcheckKYC 2.11

:point_right: *Take a look [here](https://github.com/WebpayDev/iOSMobileSDK/blob/master/2.11/Liveness3D.md) if you'd like to use Liveness3D module only.*

### Installation

This framework ment to be installed via [cocoapods](https://cocoapods.org/).

* Add to your `Podfile` specs repositories: `WebpayDev/Specs`, and public one - `CocoaPods/Specs`
* Specify `use_frameworks!` option
* Add dependency `idcheckKYC` to your target

Resulting `Podfile` could look as follows:
```ruby
platform :ios, '9.3'

source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/WebpayDev/Specs.git'

use_frameworks!

target 'MyAwesomeApp' do

  pod 'idcheckKYC'

  # any other dependencies
end
```
* Run `pod install --repo-update`

### Usage 

To instantiate framework call 
```objc
SSEngine *engine = [SSFacade setupForApplicant:applicantID
                                     withToken:authToken
                                        locale:locale
                                  supportEmail:supportEmail
                                       baseUrl:baseUrl
                                   colorConfig:colorConfigOrNil
                                   imageConfig:imageConfigOrNil];
``` 
Where 
* `applicantID` - your applicant identifier
* `authToken` - your auth token
* `locale` - user locale (preferably `NSLocale.currentLocale.localeIdentifier`, but you can use any)
* `baseUrl` - `test-msdk.webpay.by` for test environment or `msdk.webpay.by` for production one
* `colorConfigOrNil` - nil or subclass of `KYCColorConfig` (for color pallet customization)
* `imageConfigOrNil` - nil or subclass of `KYCImageConfig` (for icons customization)

Then, you should:
* Connect to remote - `[engine connectWithExpirationHandler:verificationResultHandler:]`
* Create KYC UI - `[SSFacade getChatControllerWithAttributedTitle:titleOrNil]` 
* Refresh auth token (when needed) - `engine.refreshToken = newToken`
