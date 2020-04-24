# net5-samples

_This is an *early* preview of Xamarin in .NET 5 **not for production use**. Expect breaking changes as Xamarin is still in development for .NET 5._

First install the [latest master build of .NET 5][0].

Projects:

* HelloAndroid - a native Xamarin.Android application
* HelloiOS - a native Xamarin.iOS application
* HelloForms, HelloForms.iOS, HelloForms.Android - a cross-platform Xamarin.Forms application

### Android

Prerequisites:

* You will need the Android SDK installed as well as `Android SDK Platform 29`. Simplest way to get this is to install the current Xamarin workload and go to `Tools > Android > Android SDK Manager` from within Visual Studio.

For example, to build the Android project:

    dotnet publish HelloAndroid/HelloAndroid.csproj

You can also deploy an Android project to an attached emulator via:

    dotnet publish -t:Install HelloAndroid/HelloAndroid.csproj

To deploy to a device, you can either modify `$(RuntimeIdentifier)` in
the `.csproj` or run:

    dotnet publish -t:Install HelloAndroid/HelloAndroid.csproj -r android.21-arm64

To launch the app:

    dotnet build -t:StartAndroidActivity HelloAndroid/HelloAndroid.csproj

_This can also be done at the same time as `dotnet publish -t:Install -t:StartAndroidActivity`. We will eventually add a `Run` target for simplicity._

### iOS

Prerequisites:

* Xcode 11.4. Earlier versions won't work.

To build the iOS project:

    dotnet publish HelloiOS/HelloiOS.csproj

To launch the iOS project on a simulator:

    dotnet publish HelloiOS/HelloiOS.csproj -t:Run

[0]: https://github.com/dotnet/installer#installers-and-binaries

## Known Issues

Currently...

* There is not a way to do `dotnet publish` with multiple RIDs.
* There is not a way to setup a binding project, neither for Xamarin.Android nor Xamarin.iOS.
* `System.Console.WriteLine` does not work on Xamarin.Android. Use
  `Android.Util.Log.Debug` for now.
* Building for device doesn't work for iOS.
* Building for tvOS or watchOS does not work.

[nslog]: https://stackoverflow.com/questions/9204160/monotouch-nslog-and-testflightsdk

## Workarounds

These are notes for things we had to workaround for these samples to work.

### NuGet

Currently, NuGet is not able to restore existing Xamarin.Android/iOS
packages for a .NET 5 project. We used `$(AssetTargetFallback)`,
however, this option does not work in combination with transitive
dependencies. The `Xamarin.AndroidX.*` set of NuGet packages has a
complex dependency tree. We just listed every package manually for
now.

Additionally, we had some problems with the Xamarin.Forms NuGet
package listing the same assembly in both:

* `lib\netstandard2.0\Xamarin.Forms.Platform.dll`
* `lib\MonoAndroid10.0\Xamarin.Forms.Platform.dll`

For now we added an MSBuild target in `Directory.Build.targets` to
resolve this. We also had to manually reference
`Xamarin.Forms.Platform.Android.dll`.

### AndroidX MSBuild tasks

We need to port some MSBuild tasks to `netstandard2.0` such as:

https://github.com/xamarin/AndroidSupportComponents/blob/68d28bc676673ec45f7f5ea2462c10bed87e2a2a/source/buildtasks/support-vector-drawable/Support-Vector-Drawable-BuildTasks.csproj#L10

We set `$(VectorDrawableCheckBuildToolsVersionTaskBeforeTargets)` to
an empty string in `Directory.Build.targets` for now.

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
