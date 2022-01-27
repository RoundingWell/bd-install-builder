# BitDefender Install Builder

Purpose: This project builds a custom BitDefender Endpoint Security install package for use with FleetSmith (or other MDM solutions as well). The project downloads the latest installers for AMD and Intel Macs from GravityZone. Then, it generates a release formatted with `$(date +%Y-%M-%d-%H-%M-%S)` and uploads the built packages along with signature files via github actions. The build/release of process only occurs when there are new pushes to the main branch. Additionally, the build/release job can be triggered manually from the actions menu in the case that BD releases an updated installer file.

## Requirements

Since github actions automatically generates a github token that has enough permissions to draft a release, the only requirement is to set an environment secret of `BD_ID` which contains the short code denoting the org and deploy package-specific code within Gravityzone that determines the content of the install.xml file with the package download. You can find this by logging into your own BitDefender Gravityzone portal, going to `Network --> Packages`, checking the box next to the package you want to link to and then clicking on "Send download links" and looking at the macOS Downloader link. The link will look like `https://cloudgz.gravityzone.bitdefender.com/Packages/MAC/0/${BD_ID}/setup_downloader.dmg`. Copy the value where `${BD_ID}` is and store that as an environment secret. If you build a new package in Gravityzone, make sure to pick up the BD_ID of the new package and update the environment secret and re-deploy to get the package.

## Outputs

This process builds 4 artifacts. The 4 artifacts consist of 2 pairs of files:

* `rwell_bitdefender_arm.pkg` and `rwell_bitdefender_arm.sig`
* `rwell_bitdender_intel.pkg` and `rwell_bitdefender_intel.sig`

The `.sig` files contain the signature of the package upon creation. The signature is created with `shasum -a 256 /path/to/file` command.

## Structure

In order to supply a public link to FleetSmith, this repo has to remain public. Therefore, no secrets are stored in the repo and only the compiled installers are uploaded.

### bin/

The `bin/` directory contains scripts for generating the pkg files.

### scripts/

The `scripts/` directory is used as the scripts directory in the `pkgbuild` command. You can read more about pkg file scripts [here](https://www.unix.com/man-page/osx/1/pkgbuild/).

### .github/workflows/

This project uses [GitHub Actions](https://github.com/features/actions) as a CI/CD tool. You can define workflows using `.yml` files in the appropriate syntax to automate tasks.

## Additional Info

The following information can be used to set exclusions via MDM solutions to bypass the need to add security and privacy exclusions via System Preferences.

### System Extensions

**Team ID:** `GUNFMW623Y`
**Bundle ID's:**

* `com.bitdefender.FileProtect`
* `com.bitdefender.atc`
* `com.bitdefender.ExecAuth`
* `com.bitdefender.SelfProtect`
* `com.bitdefender.TMProtection`

### Kernel Extensions

**Team ID:** `GUNFMW623Y`
**Bundle ID's:**

* `com.bitdefender.FileProtect`
* `com.bitdefender.atc`
* `com.bitdefender.ExecAuth`
* `com.bitdefender.SelfProtect`
* `com.bitdefender.TMProtection`

### File Access

**BundleID:** `com.bitdefender.epsecurity.BDLDaemonApp`
**codeSigningRequirement:** `anchor apple generic and identifier "com.bitdefender.epsecurity.BDLDaemonApp" and (certificate leaf[field.1.2.840.113635.100.6.1.9] /* exists */ or certificate 1[field.1.2.840.113635.100.6.2.6] /* exists */ and certificate leaf[field.1.2.840.113635.100.6.1.13] /* exists */ and certificate leaf[subject.OU] = GUNFMW623Y)`
**SystemSettings:** `FilePolicy:AllFiles`
