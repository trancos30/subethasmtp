# Download #
Click on the "Downloads" tab and then the desired release.

# Maven Central Repository #
The SubEtha SMTP binary, source, and JavaDocs jars are published to the Maven Central Repository.  The Central repo is active by default in Maven, requiring no extra configuration to obtain.

To use the binary jar, add the dependency to the POM (adjust the version as necessary):
```
<dependency>
  <groupId>org.subethamail</groupId>
  <artifactId>subethasmtp</artifactId>
  <version>3.1.3</version>
</dependency>
```

Some IDEs/IDE plugins, such as m2eclipse, will automatically download the source and JavaDocs jars and configure the IDE to use them (very handy for debugging and mouse-pointer-hover pop-up display information).