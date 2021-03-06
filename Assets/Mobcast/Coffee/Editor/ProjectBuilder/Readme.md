ProjectBuilder
===

## Overview

A tool for easy automating and customizing build process for Unity.

### Basic & Advanced Aplication Build Settings
![image](https://user-images.githubusercontent.com/12690315/31534409-c342b2a4-b031-11e7-8fea-75bb916ef944.png)

* Build target
    * Build artifact is generated in `<project_dir>/build` directory or file.
* Company name
* Product name
* Application identifier
    * (Android) PackageName
    * (iOS) BundleIdentifier
* Development build option(Development & Script debugging)
* Defined Symbols
* Enable/Disable scenes in build
* Exclude directory in build
* Application version
* Internal version
    * (Android) BundleVersionCode
    * (iOS) BuildNumber

### AssetBundle Build Settings
![image](https://user-images.githubusercontent.com/12690315/31534416-cc880864-b031-11e7-8575-1010062d385e.png)

* Compression options
* Build artifacts are generated in `<project_dir>/AssetBundles` directory
* Copy to StreamingAssets directory


### Android Settings
![image](https://user-images.githubusercontent.com/12690315/31534438-e913876a-b031-11e7-9317-204c13a960e8.png)

* Keystore infomation


### iOS Settings
![image](https://user-images.githubusercontent.com/12690315/31534427-d693dd38-b031-11e7-96ba-984722562aff.png)

* XCode modifier
    * Languages
    * Frameworks
    * Services
    * Entitlement file
* Signing & Provisioning profile
* Generate exportOptions.plist




## Requirement

* Unity5.3+ *(included Unity 2017.x)*
* No other SDK




## Usage

1. Download [ProjectBuilder.unitypackage](https://github.com/mob-sakai/ProjectBuilder/raw/master/ProjectBuilder.unitypackage) and install to your project.
1. From the menu, click `Coffee` > `Project Builder`
1. Input build configurations.
1. Click `Buid` button to build application.
1. Build artifact is generated in `<project_dir>/build` directory or file.




## Build on command line

* The ProjectBuilder is accessible from the command line.  
It is useful when using CI tools such as Jenkins.
* The following command option executes the build with the specified builder.  
`-batchmode -buildTarget <ios|android|webgl> -executeMethod Mobcast.Coffee.Build.ProjectBuilder.Build -builder <builder_name> [-devBuildNumber <number>] [-appendSymbols 'INCLUDE_SYMBOL;!EXCLUDE_SYMBOL;...'] [-override <builder_json>]`

* For example, The following command option executes the build for iOS with 'Develop_iOS' builder asset, with `DEBUG_MODE` symbol.  
`-batchmode -buildTarget ios -executeMethod Mobcast.Coffee.Build.ProjectBuilder.Build -builder 'Default iOS' -appendSymbols DEBUG_MODE`

Note: **DO NOT USE** `-quit` option.  
For other infomation, see this link : <https://docs.unity3d.com/Manual/CommandLineArguments.html>


#### Override builder with json

`-override <builder_json>` to override builder setting. For example as following.
* `-override "{ \"applicationIdentifier\":\"com.test.app\", \"version\":\"1.1.0\", \"defineSymbols\":\"!DEVELOPMENT_BUILD;FAKE_PURCHASE\" }"`
    * Override application identifier to "com.test.app"
    * Override version to '1.1.0'
    * Disable symbol 'DEVELOPMENT_BUILD' and enable symbol 'FAKE_PURCHASE'
* To override other parameters, convert the builder to json from `Convert to JSON (console log)` in project builder window.




## Build on Unity Cloud Build(UCB)

1. Type `Mobcast.Coffee.Build.ProjectBuilder.PreExport` at `Config > Advanced Settings > Pre-Export Method Name` on UCB.
1. Builder asset used for building will be selected automatically based on build setting label.  
For example, a build setting labeled 'Default iOS' on UCB, selects builder asset named 'Default iOS' in project.




## How to customize the builder for your project?

1. Click `Create Custom Project Builder Script`
1. Save script with dialog.
1. Implement the script.  
The serialized field is not only displayed in the inspector, it can also be used in PostProcessBuild as following.

![image](https://user-images.githubusercontent.com/12690315/28651867-64891a28-72bf-11e7-911a-f7f13a371def.png)

```cs
[SerializeField] string stringParameter;

[PostProcessBuild]
protected static void OnPostProcessBuild(BuildTarget target, string path)
{
    CustomProjectBuilder current = Util.currentBuilder as CustomProjectBuilder;
    Debug.Log(current.stringParameter);
    ...
}
```




## How to add a supported build target to build?

* Implement `IBuildTargetSettings` interface as following for build targets you need.
* Add `System.Serializable` attribute to the class to be serializable.

```cs
[System.Serializable]
public class BuildTargetSettings_WebGL : IBuildTargetSettings
{
	public BuildTarget buildTarget{get{ return BuildTarget.WebGL;}}
	public Texture icon{get{ return EditorGUIUtility.FindTexture("BuildSettings.WebGL.Small");}}
	public void Reset(){}
	public void ApplySettings(ProjectBuilder builder){}
	public void DrawSetting(SerializedObject serializedObject){}
}
```

* Add serialized field to `ProjectBuilder` or `Custom ProjectBuilder` as following.
```cs
public BuildTargetSettings_WebGL webGlSettings = new BuildTargetSettings_WebGL();
```




## Release Notes

### ver.1.0.0:

* Feature: Override builder with json on command line.
* Feature: Memorize last selected builder each BuildTarget.
* Fixed: `Enable/Disable scenes in build` is not reflected.

### ver.0.10.1:

* Fixed: When select file path not under project directory, value to be empty.

### ver.0.10.0:

* Feature: Exclude directory in build.
* Feature: AssetBundle build report(Add/Delete/Update).

### ver.0.9.3:

* Fixed: AssetBundle output path is incorrect.

### ver.0.9.2:

* Fixed: Builder icon is not found.

### ver.0.9.1:

* Fixed: Build target on edit multiple builder.
* Changed: Rename `IPlatformSettings` to `IBuildTargetSettings`.

### ver.0.9.0:

* Feature: New editor window instead of inspector window.
* Feature: Copy AssetBundles to StreamingAssets.

### ver.0.8.1:

* Fixed: Build button for AssetBundle is not displayed.

### ver.0.8.0:

* Feature: Build AssetBundle.
    * Supports options.
    * Build artifacts are generated in `<project_dir>/AssetBundles` directory.

### ver.0.7.0:

* Fix: 'Usage For CUI' was corrected.
* Fix: Util.projectDir has error after compiling.
* Fix: (iOS) When 'Automatically Sign' is enabled, ignore 'Provisioning Profile Id', etc...

### ver.0.6.0:

* Feature: Supports WebGL.
* Feature: Supports other platforms by implementing `IPlatformSettings` interface.


### ver.0.5.0:

* Feature: Supports Unity Cloud Build.
* Feature: Enable/Disable scenes in build.


### ver.0.4.0:

* Obsolete: Obsolete 'Custom build pipeline' to simplify.
* Obsolete: Obsolete 'Generate CUI command' and explain it in Readme instead.
* Obsolete: Several CUI command options.
* Obsolete: Several BuildOptions.
* Feature: (iOS) Supports language options for XCode.


### ver.0.3.0:

* Feature: (iOS) Supports XCode 8 & Automatically Sign.
* Feature: (iOS) Add framework options.
* Feature: (iOS) Add entitlement file.
* Feature: (iOS) Apple servises (iCloud, Push, GameCenter, etc...) can be enable.
* Fix: Inspector GUI.


### ver.0.2.0:

* Supports Unity5.5+.
* Improvement: Setting items are simplified. Several items have been deleted.
* Feature: Add button to 'Build & Run'.
* Feature: (iOS) Generate 'exportOptions.plist'.
* Fix: Generated CUI command is incorrect.


### ver.0.1.0:

* Feature: Manage build configuration with builder asset.
* Feature: (Android) Keystore information.
* Feature: Custom build pipeline.
* Feature: Export `BUILD_VERSION` for CI.




## See Also

* GitHub Page : https://github.com/mob-sakai/ProjectBuilder
* Issue tracker : https://github.com/mob-sakai/ProjectBuilder/issues