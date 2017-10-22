# Setup

## IOS
1. yarn add react-native-fbsdk
2. react-native link

### Configure Facebook App Settings
3. Select your Facebook App, Settings > Add Platform > IOS
4. Add the Bundle Identifier Xcode to Bundle ID
5. Enable Single Sign On

### Download Facebook SDK for iOS
6. Download Facebook SDK for iOS - name it **FacebookSDK** folder.
7. Open your Xcode, create a Frameworks group in your project
8. Drag **Bolts.framework, FBSDKCoreKit.framework, FBSDKLoginKit.framework, and FBSDKShareKit.framework** files into the Frameworks group
9. choose **Create groups** for any added folders and **deselect Copy items** if needed.
10. Go to Project > Build Settings > All > Framework Search Paths > Copy the path of your **FacebookSDK** folder and paste it there

### Configure info.plist folder

11. Add the following info to your info.plist inside the IOS folder > projectName (to make it easier, go to quick start and copy the prefilled information)

```
	<key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>fb{your-app-id}</string>
			</array>
		</dict>
	</array>
	<key>FacebookAppID</key>
	<string>{your-app-id}</string>
	<key>FacebookDisplayName</key>
	<string>{your-app-name}</string>
```

### Connect App Delegate
Add the following to your AppDelegate.m file. 

```
#import <FBSDKCoreKit/FBSDKCoreKit.h>

- (BOOL)application:(UIApplication *)application 
            openURL:(NSURL *)url 
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {

  BOOL handled = [[FBSDKApplicationDelegate sharedInstance] application:application
    openURL:url
    sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]
    annotation:options[UIApplicationOpenURLOptionsAnnotationKey]
  ];
  // Add any custom logic here.
  return handled;
}
```

12. Add Facebook Login as a product in your Facebook app dashboard