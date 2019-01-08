## Reproduce

*  git clone git@github.com:paulb777/ArchiveNamespaceCollision.git
*  cd ArchiveNamespaceCollision/
* open ./NewBuild-Extension-Subspec.xcworkspace/
* Build and run the app and extension schemes and notice they both run successfully
* Change to "Generic iOS Device" and attempt to archive
* See the following:
```
error: Multiple commands produce '/Users/paulbeusterien/Library/Developer/Xcode/DerivedData/NewBuild-Extension-Subspec-dxwqxtrfbutjvtcttgjreepbbokc/Build/Intermediates.noindex/ArchiveIntermediates/NewBuild-Extension-Subspec/IntermediateBuildFilesPath/UninstalledProducts/iphoneos/GoogleUtilities.framework':
1) Target 'GoogleUtilities-NSData+zlib' has create directory command with output '/Users/paulbeusterien/Library/Developer/Xcode/DerivedData/NewBuild-Extension-Subspec-dxwqxtrfbutjvtcttgjreepbbokc/Build/Intermediates.noindex/ArchiveIntermediates/NewBuild-Extension-Subspec/IntermediateBuildFilesPath/UninstalledProducts/iphoneos/GoogleUtilities.framework'
2) Target 'GoogleUtilities-Environment' has create directory command with output '/Users/paulbeusterien/Library/Developer/Xcode/DerivedData/NewBuild-Extension-Subspec-dxwqxtrfbutjvtcttgjreepbbokc/Build/Intermediates.noindex/ArchiveIntermediates/NewBuild-Extension-Subspec/IntermediateBuildFilesPath/UninstalledProducts/iphoneos/GoogleUtilities.framework'
```
* Note that the archive operation will succeed with the old build system.

## Analysis

There are two intermediate targets both named GoogleUtilities.framework. They are both include static libraries so
that they are not needed after the build. However, the new build system is that the archiver wants to put all
intermediate frameworks into the same directory instead of separate directories for each build target like the regular builder.

Note the new error would make more sense if the frameworks were dynamic since there would be an eventual collision in the app bundle's
Frameworks folder. However, even in the dynamic case, it would be nice if the error occurred for regular builds instead of only
at Archive time.

## Additional Background

* https://github.com/CocoaPods/CocoaPods/issues/8206