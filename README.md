# <img align="left" src="osgiX.png"> OsgiX: Expose the entire JRE to OSGi<br>&nbsp;using extension bundles

If you've been working with OSGi, you've certainly seen this error before: `Unresolved requirement: Import-Package: sun.misc`

The cause of the problem is that OSGi doesn't supply the entire JRE to its bundles - it only provides the most standardized packages.  This means that non-standard packages like `sun.misc` and `sun.reflect` aren't available to your bundles, even though they are in common usage.

The easiest way to fix this is with an **extension bundle**.  This is a fragment bundle which contains nothing besides a manifest that looks like this:

```
Export-Package: sun.misc
Fragment-Host: system.bundle; extension:=framework
Bundle-SymbolicName: com.diffplug.osgi.extension.sun.misc
Bundle-ManifestVersion: 2
Manifest-Version: 1.0
```

This forces the OSGi system bundle to export the `sun.misc` package, thus fixing the `Unresolved requirement` errors.

## MavenCentral coordinates

This project provides extension bundles for the following packages:

* `sun.misc` ([MavenCentral](http://search.maven.org/#artifactdetails%7Ccom.diffplug.osgi%7Ccom.diffplug.osgi.extension.sun.misc%7C0.0.0%7Cjar))
* `sun.reflect` ([MavenCentral](http://search.maven.org/#artifactdetails%7Ccom.diffplug.osgi%7Ccom.diffplug.osgi.extension.sun.reflect%7C0.0.0%7Cjar))
* `com.sun.net.httpserver` ([MavenCentral](http://search.maven.org/#artifactdetails%7Ccom.diffplug.osgi%7Ccom.diffplug.osgi.extension.com.sun.net.httpserver%7C0.0.0%7Cjar))

They are available on MavenCentral at coordinates following this convention:

* `com.diffplug.osgi:com.diffplug.osgi.extension.<package>:0.0.0`

## Other packages

If you need an extension bundle for a different package, file an issue and we'd be happy to publish it for you.  Or you can make your own bundle using the following recipe:

* set the `PKG` variable in `gradle.properties`
* run `gradlew build`
* your extension bundle will be in `build/libs`

## Alternatives to extension bundles

For a more detailed description of what's going on, [the Spring blog has a detailed article](http://spring.io/blog/2009/01/19/exposing-the-boot-classpath-in-osgi/).  We're pretty convinced that the extension bundles provided above are the fastest and clearest ways to address this issue, but here's a summary of the alternatives.

Another way to accomplish this same effect is to set `org.osgi.framework.system.packages=sun.misc,sun.reflect`.  This will fix import errors around `sun.misc` and `sun.reflect`, but now you will have new errors about `javax.swing` and every other standard java package.  By setting this property, you are overriding all of the framework default packages, so you're likely to cause new problems.

If you happen to be using OSGi 4.2 or later, you can set `org.osgi.framework.system.packages.extra=sun.misc,sun.reflect`, and it will simply add those two packages to the system default packages.  This has the exact same effect as including the extension bundles for `sun.misc` and `sun.reflect`.

A slightly different choice is to set `org.osgi.framework.bootdelegation=sun.misc.*,sun.reflect.*`.  This will fix the errors, but will do so by automatically providing these packages to all bundles, whether they imported them or not.  It's a subtle difference, and it usually doesn't matter, but you can try it if you want.

## Acknowledgements

* Thanks to Spring for their [excellent blog post](http://spring.io/blog/2009/01/19/exposing-the-boot-classpath-in-osgi/) on this subject.
* Maintained by [DiffPlug](http://www.diffplug.com/).
