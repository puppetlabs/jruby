## Puppetlabs Fork of JRuby

This branch contains some minor packaging changes that allow us to release jars
for `jruby-stdlib` and `jruby-core`, independently from the upstream JRuby
release cycle.  This was necessary in order to get access to some critical fixes
that have been merged upstream but haven't yet been released.

The file changes are limited to poms:

```
./pom.xml
./maven/pom.xml
./maven/jruby-stdlib/pom.xml
./core/pom.xml
./lib/pom.xml
./ext/pom.xml
./ext/readline/pom.xml
./ext/ripper.pom.xml
```

The changes to those poms are limited to:

* Changing the `groupId` to `puppetlabs.org.jruby` so that we can distinguish our artifacts.
* Commenting out most modules / profiles blocks, because we don't want the `mvn` tasks that we
  run to bring in any of the other components, because we don't use them in our
  builds.
* Adding a `distributionManagement` section to the parent pom, so that we can specify
  the maven artifact repository we'd like to deploy to.

Technically we only really need to publish the `jruby-core` and `jruby-stdlib`
jars, but because those have parent poms in the `org.jruby` namsepace (and because
those parent poms will normally be versioned as SNAPSHOTs, and thus not yet
available in public maven repositories), we need to release `puppetlabs` versions
of the parent poms as well.

We also need to build the lib/ext modules because they contain embedded jars
and gems that are necessary for stdlib.

At the time of this writing I haven't automated any of the release process; you'll
need to manually update the version numbers in the poms, set up your
`~/.m2/settings.xml` to provide credentials for the target repository server,
and then run `mvn install` or `mvn deploy` from the root directory.

It's probably wise to make sure that you are running a JDK7 version of Java
when you do this, just to be certain that you don't end up with any published
jars that only work on JDK8.
