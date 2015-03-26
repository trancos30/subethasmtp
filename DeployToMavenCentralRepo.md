In the directory with the pom.xml file, run the following Maven command:
```
mvn clean source:jar javadoc:jar deploy
```

This command performs a clean build, creates a source jar, javadoc jar, and binary product jar, and then deploys them to the configured remote Maven repository.

The artifacts must be signed, and the configured maven-gpg-plugin will automatically do so before deploying.  It will prompt for your key passphrase before signing (which means the deployer must have GPG installed and key generated with passphrase).